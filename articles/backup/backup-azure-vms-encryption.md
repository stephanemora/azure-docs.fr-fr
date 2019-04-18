---
title: Sauvegarder et restaurer des machines virtuelles Azure chiffrées avec sauvegarde Azure
description: Décrit comment sauvegarder et restaurer des machines virtuelles Azure chiffrées avec le service de sauvegarde Azure.
services: backup
author: geetha
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 4/3/2019
ms.author: geetha
ms.openlocfilehash: 893a22fb9f325625707869c8f6571d572b8f6b33
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59358236"
---
# <a name="back-up-and-restore-encrypted-azure-vm"></a>Sauvegarder et restaurer la machine virtuelle Azure chiffrée

Cet article explique comment sauvegarder et restaurer Windows ou Linux Azure virtual machines virtuelles avec disques chiffrés à l’aide de la [sauvegarde Azure](backup-overview.md) service.

Si vous souhaitez en savoir plus sur la façon dont sauvegarde Azure interagit avec les machines virtuelles Azure avant de commencer, passez en revue ces ressources :

- [Révision](backup-architecture.md#architecture-direct-backup-of-azure-vms) l’architecture de sauvegarde de machine virtuelle Azure.
- [En savoir plus sur](backup-azure-vms-introduction.md) sauvegarde de machine virtuelle Azure et l’extension de sauvegarde Azure.

## <a name="encryption-support"></a>Prise en charge du chiffrement

Sauvegarde Azure prend en charge la sauvegarde des machines virtuelles Azure qui ont leurs disques de système d’exploitation/données chiffrées avec Azure Disk Encryption (ADE). ADE utilise BitLocker pour le chiffrement des machines virtuelles Windows et la fonctionnalité dm-crypt pour les machines virtuelles Linux. ADE s’intègre avec Azure Key Vault pour gérer les secrets et clés de chiffrement de disque. Clé de coffre de clés de chiffrement (clés) peut être utilisés pour ajouter une couche supplémentaire de sécurité, le cryptage des secrets de chiffrement avant leur écriture dans le coffre de clés.

Sauvegarde Azure peut sauvegarder et restaurer des machines virtuelles Azure à l’aide d’ADE avec et sans l’application Azure AD, comme décrit dans le tableau suivant.

**Type de disque de machine virtuelle** | **ADE (BEK/dm-crypt)** | **ADE et KEK**
--- | --- | ---
**non managé** | Oui | Oui
**Gérés**  | Oui | Oui

- En savoir plus sur [ADE](../security/azure-security-disk-encryption-overview.md), [Key Vault](../key-vault/key-vault-overview.md), et [Kek](https://blogs.msdn.microsoft.com/cclayton/2017/01/03/creating-a-key-encrypting-key-kek/).
- Lire le [FAQ](../security/azure-security-disk-encryption-faq.md) pour le chiffrement de disque de machine virtuelle Azure.



### <a name="limitations"></a>Limites

- Vous pouvez sauvegarder et restaurer des machines virtuelles chiffrées dans le même abonnement et région.
- Sauvegarde Azure prend en charge les machines virtuelles chiffrées à l’aide de clés d’autonome. N’importe quelle touche qui fait partie d’un certificat utilisé pour chiffrer une machine virtuelle n’est pas actuellement pris en charge.
- Vous pouvez sauvegarder et restaurer des machines virtuelles chiffrées dans le même abonnement et la même région que le coffre de sauvegarde Recovery Services.
- Les machines virtuelles chiffrées ne peuvent pas être récupérées au niveau fichier/dossier. Vous devez récupérer la machine virtuelle entière pour restaurer des fichiers et dossiers.
- Lorsque vous restaurez une machine virtuelle, vous ne pouvez pas utiliser le [remplacer la machine virtuelle existante](backup-azure-arm-restore-vms.md#restore-options) option pour les machines virtuelles chiffrées. Cette option est uniquement pris en charge pour les disques gérés non chiffrés.




## <a name="before-you-start"></a>Avant de commencer

Avant de commencer, procédez comme suit :

1. Vérifiez que vous disposez d’un ou plusieurs [Windows](../security/azure-security-disk-encryption-windows.md) ou [Linux](../security/azure-security-disk-encryption-linux.md) machines virtuelles avec ADE activées.
2. [Passez en revue la matrice de prise en charge](backup-support-matrix-iaas.md) pour la sauvegarde de machine virtuelle Azure
3. [Créer](backup-azure-arm-vms-prepare.md#create-a-vault) un coffre de sauvegarde des Services de récupération si vous n’en avez pas.
4. Si vous activez le chiffrement pour les machines virtuelles qui sont déjà activées pour la sauvegarde, vous devez simplement fournir une sauvegarde avec des autorisations pour accéder au Key Vault afin que les sauvegardes peuvent continuer sans interruption de service. [En savoir plus](#provide-permissions) sur l’affectation de ces autorisations.

En outre, il existe plusieurs choses que vous devrez peut-être faire dans certaines circonstances :

- **Installez l’agent de machine virtuelle sur la machine virtuelle**: Sauvegarde Azure sauvegarde les machines virtuelles Azure en installant une extension à l’agent de machine virtuelle Azure en cours d’exécution sur l’ordinateur. Si votre machine virtuelle a été créé à partir d’une image de place de marché Azure, l’agent est installé et en cours d’exécution. Si vous créez une machine virtuelle personnalisée ou si vous migrez un ordinateur local, vous devrez peut-être [installer manuellement l’agent](backup-azure-arm-vms-prepare.md#install-the-vm-agent).
- **Autoriser explicitement l’accès sortant**: En règle générale, vous n’avez pas besoin d’autoriser explicitement l’accès réseau sortant pour une machine virtuelle Azure afin qu’il communique avec la sauvegarde Azure. Toutefois, certaines machines virtuelles peuvent rencontrer des problèmes de connexion, à montrant le **ExtensionSnapshotFailedNoNetwork** erreur lorsque vous tentez de vous connecter. Si cela se produit, vous devez [autoriser explicitement l’accès sortant](backup-azure-arm-vms-prepare.md#explicitly-allow-outbound-access), de sorte que l’extension de sauvegarde Azure peut communiquer avec des adresses IP publiques Azure pour le trafic de sauvegarde.



## <a name="configure-a-backup-policy"></a>Configurer une stratégie de sauvegarde

1. Si vous n’avez pas encore créé un coffre de sauvegarde Recovery Services, suivez [ces instructions](backup-azure-arm-vms-prepare.md#create-a-vault)
2. Ouvrez le coffre dans le portail, puis sélectionnez **sauvegarde** dans le **mise en route** section.

    ![Panneau Sauvegarde](./media/backup-azure-vms-encryption/select-backup.png)

3. Dans **objectif de sauvegarde** > **où s’exécute votre charge de travail ?** sélectionnez **Azure**.
4. Dans **que voulez-vous sauvegarder ?** sélectionnez **machine virtuelle** > **OK**.

      ![Panneau Scénario](./media/backup-azure-vms-encryption/select-backup-goal-one.png)

5. Dans **stratégie de sauvegarde** > **choisir une stratégie de sauvegarde**, sélectionnez la stratégie que vous souhaitez associer à l’archivage. Cliquez ensuite sur **OK**.
    - Une stratégie de sauvegarde spécifie quand les sauvegardes sont effectuées, et la durée pendant laquelle ils sont stockés.
    - Les détails de la stratégie par défaut sont répertoriés dans le menu déroulant à l’écran.

    ![Ouvrir le panneau Scénario](./media/backup-azure-vms-encryption/select-backup-goal-two.png)

6. Si vous ne souhaitez pas utiliser la stratégie par défaut, sélectionnez **créer un nouveau**, et [créer une stratégie personnalisée](backup-azure-arm-vms-prepare.md#create-a-custom-policy).


7. Cliquez sur les machines virtuelles chiffrées que vous souhaitez sauvegarder à l’aide de la stratégie de sélection et sélectionnez **OK**.

      ![Sélectionner des machines virtuelles chiffrées](./media/backup-azure-vms-encryption/selected-encrypted-vms.png)

8. Si vous utilisez Azure Key Vault, sur la page du coffre, vous consultez un message que sauvegarde Azure a besoin d’accéder en lecture seule aux clés et secrets dans Key Vault.

    - Si vous recevez ce message, aucune action n’est requise.

        ![Accès OK](./media/backup-azure-vms-encryption/access-ok.png)

    - Si vous recevez ce message, vous devez définir les autorisations comme décrit dans la [procédure ci-dessous](#provide-permissions).

        ![Avertissement de l’accès](./media/backup-azure-vms-encryption/access-warning.png)

9. Cliquez sur **activer la sauvegarde** pour déployer la stratégie de sauvegarde dans le coffre et activer la sauvegarde pour les machines virtuelles sélectionnées.


## <a name="trigger-a-backup-job"></a>Déclencher une tâche de sauvegarde

La sauvegarde initiale s’exécute conformément au planning, mais vous pouvez l’exécuter immédiatement en procédant comme suit :

1. Dans le menu du coffre, cliquez sur **Éléments de sauvegarde**.
2. Sur **Éléments de sauvegarde**, cliquez sur **Machine virtuelle Azure**.
3. Dans le **éléments de sauvegarde** , cliquez sur les points de suspension (...).
4. Cliquez sur **Sauvegarder maintenant**.
5. Dans **sauvegarder maintenant**, utilisez le contrôle calendrier pour sélectionner le dernier jour où le point de récupération doit être conservé. Cliquez ensuite sur **OK**.
6. Surveiller les notifications du portail. Vous pouvez surveiller la progression du travail dans le tableau de bord du coffre > **Travaux de sauvegarde** > **En cours d’exécution**. Selon la taille de votre machine virtuelle, la création de la sauvegarde initiale peut prendre un certain temps.


## <a name="provide-permissions"></a>Fournir des autorisations

Machine virtuelle Azure a besoin d’un accès en lecture seule pour sauvegarder les clés et secrets, ainsi que les machines virtuelles associées.

- Votre coffre de clés est associé au locataire Azure AD de l’abonnement Azure. Si vous êtes un **utilisateur membre**, sauvegarde Azure acquiert l’accès à Key Vault sans aucune autre action.
- Si vous êtes un **utilisateur invité**, vous devez accorder des autorisations pour la sauvegarde Azure à accéder au coffre de clés.

Pour définir les autorisations :

1. Dans le portail Azure, sélectionnez **tous les services**, puis recherchez **coffres de clés**.
2. Sélectionnez le coffre de clés associé à la machine virtuelle chiffrée, vous effectuez une sauvegarde.
3. Sélectionnez **stratégies d’accès** > **Ajouter nouveau**.
4. Sélectionnez **sélectionner le principal**, puis tapez **gestion de sauvegarde**.
5. Sélectionnez **Service de gestion de sauvegarde** > **sélectionnez**.

    ![Sélection du service de sauvegarde](./media/backup-azure-vms-encryption/select-backup-service.png)

6. Dans **ajouter une stratégie d’accès** > **configurer à partir du modèle (facultatif)**, sélectionnez **sauvegarde Azure**.
    - Les autorisations requises sont préremplies dans les listes déroulantes **Autorisations de clé** et **Autorisations du secret**.
    - Si votre machine virtuelle est chiffrée à l’aide de **BEK uniquement**, supprimer la sélection pour **autorisations de clé** dans la mesure où vous devez uniquement des autorisations pour les clés secrètes.

    ![Sélection de Sauvegarde Azure](./media/backup-azure-vms-encryption/select-backup-template.png)

6. Cliquez sur **OK**. **Service de gestion de sauvegarde** est ajouté à **stratégies d’accès**.

    ![Stratégies d’accès](./media/backup-azure-vms-encryption/backup-service-access-policy.png)

7. Cliquez sur **enregistrer** pour fournir une sauvegarde Azure avec les autorisations.

## <a name="restore-an-encrypted-vm"></a>Restaurer une machine virtuelle chiffrée

Vous restaurez des machines virtuelles chiffrées comme suit :

1. [Restaurer le disque de machine virtuelle](backup-azure-arm-restore-vms.md#restore-disks).
2. Puis effectuez l’une des opérations suivantes :
    - Utilisez le modèle qui est généré au cours de l’opération de restauration pour personnaliser les paramètres de la machine virtuelle et déclencher le déploiement de machines virtuelles. [Plus d’informations](backup-azure-arm-restore-vms.md#use-templates-to-customize-a-restored-vm)
    - Créer une machine virtuelle à partir de disques restaurés à l’aide de Powershell. [Plus d’informations](backup-azure-vms-automation.md#create-a-vm-from-restored-disks)

## <a name="next-steps"></a>Étapes suivantes

Si vous rencontrez des problèmes, passez en revue

- [Les erreurs courantes](backup-azure-vms-troubleshoot.md) quand sauvegarder et restaurer les machines virtuelles Azure chiffrées.
- [Extension de l’agent/de sauvegarde de machine virtuelle Azure](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md) problèmes.
