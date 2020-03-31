---
title: Sauvegarder et restaurer des machines virtuelles Azure chiffrées
description: Décrit comment sauvegarder et restaurer des machines virtuelles Azure chiffrées avec le service Sauvegarde Azure.
ms.topic: conceptual
ms.date: 04/03/2019
ms.openlocfilehash: a3976cc83f749b1abe00cef3f5bf867ffbc30ab6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78206688"
---
# <a name="back-up-and-restore-encrypted-azure-vm"></a>Sauvegarder et restaurer une machine virtuelle Azure chiffrée

Cet article explique comment sauvegarder et restaurer des machines virtuelles Azure Windows ou Linux avec des disques chiffrés à l’aide du service [Sauvegarde Azure](backup-overview.md).

Avant de commencer, si vous souhaitez en savoir plus sur la façon dont Sauvegarde Azure interagit avec les machines virtuelles Azure, consultez ces ressources :

- [Passez en revue](backup-architecture.md#architecture-built-in-azure-vm-backup) l’architecture de sauvegarde de machine virtuelle Azure.
- [Découvrez](backup-azure-vms-introduction.md) la sauvegarde des machines virtuelles Azure et l’extension Sauvegarde Azure.

## <a name="encryption-support"></a>Prise en charge du chiffrement

Sauvegarde Azure prend en charge la sauvegarde des machines virtuelles Azure dont le disque de système d’exploitation/données est chiffré avec Azure Disk Encryption (ADE). ADE utilise BitLocker pour le chiffrement des machines virtuelles Windows et la fonctionnalité dm-crypt pour les machines virtuelles Linux. ADE s’intègre à Azure Key Vault pour gérer les secrets et clés de chiffrement de disque. Les clés de chiffrement de clé Azure Key Vault (KEK) permettent d’ajouter une couche de sécurité, en chiffrant les secrets de chiffrement avant de les écrire dans le coffre de clés.

Sauvegarde Azure peut sauvegarder et restaurer des machines virtuelles Azure à l’aide d’ADE avec et sans l’application Azure AD, comme le récapitule le tableau suivant.

**Type de disque de machine virtuelle** | **ADE (BEK/dm-crypt)** | **ADE et clé KEK**
--- | --- | ---
**Non managé** | Oui | Oui
**Managé**  | Oui | Oui

- Découvrez-en plus sur [ADE](../security/azure-security-disk-encryption-overview.md), [Key Vault](../key-vault/key-vault-overview.md) et les clés [KEK](https://docs.microsoft.com/azure/virtual-machine-scale-sets/disk-encryption-key-vault#set-up-a-key-encryption-key-kek).
- Consultez les [Questions fréquentes (FAQ)](../security/azure-security-disk-encryption-faq.md) sur le chiffrement des disques de machines virtuelles Azure.

### <a name="limitations"></a>Limites

- Vous pouvez sauvegarder et restaurer des machines virtuelles chiffrées dans les mêmes abonnement et région.
- Sauvegarde Azure prend en charge les machines virtuelles chiffrées à l’aide de clés autonomes. Aucune clé appartenant à un certificat utilisé pour chiffrer une machine virtuelle n’est prise en charge.
- Vous pouvez sauvegarder et restaurer des machines virtuelles chiffrées dans les mêmes abonnement et région que le coffre de sauvegarde Recovery Services.
- Les machines virtuelles chiffrées ne peuvent pas être récupérées au niveau fichier/dossier. Vous devez récupérer la machine virtuelle entière pour restaurer des fichiers et des dossiers.
- Quand vous restaurez une machine virtuelle, vous ne pouvez pas utiliser l’option de [remplacement de la machine virtuelle existante](backup-azure-arm-restore-vms.md#restore-options) pour les machines virtuelles chiffrées. Cette option est uniquement prise en charge pour les disques managés non chiffrés.

## <a name="before-you-start"></a>Avant de commencer

Avant de commencer, procédez comme suit :

1. Veillez à disposer d’une ou plusieurs machines virtuelles [Windows](../security/azure-security-disk-encryption-windows.md) ou [Linux](../virtual-machines/linux/disk-encryption-overview.md) sur lesquelles ADE est activé.
2. [Passez en revue la matrice de prise en charge](backup-support-matrix-iaas.md) de la sauvegarde des machines virtuelles Azure.
3. [Créez](backup-azure-arm-vms-prepare.md#create-a-vault) un coffre de sauvegarde Recovery Services si vous n’en avez pas.
4. Si vous activez le chiffrement pour des machines virtuelles qui sont déjà activées pour la sauvegarde, vous devez simplement fournir à Sauvegarde des autorisations pour accéder au coffre de clés afin que les sauvegardes puissent se poursuivre sans interruption. [Découvrez-en plus](#provide-permissions) sur l’affectation de ces autorisations.

Par ailleurs, vous risquez de devoir faire deux choses dans certaines circonstances :

- **Installer l’agent de machine virtuelle sur la machine virtuelle** : Sauvegarde Azure sauvegarde les machines virtuelles Azure en installant une extension à l’agent de machine virtuelle Azure en cours d’exécution sur l’ordinateur. Si votre machine virtuelle a été créée à partir d’une image de la Place de marché Azure, l’agent est installé et en cours d’exécution. Si vous créez une machine virtuelle personnalisée ou que vous migrez une machine locale, vous devrez peut-être [installer l’agent manuellement](backup-azure-arm-vms-prepare.md#install-the-vm-agent).

## <a name="configure-a-backup-policy"></a>Configurer une stratégie de sauvegarde

1. Si vous n’avez pas encore créé de coffre de sauvegarde Recovery Services, suivez [ces instructions](backup-azure-arm-vms-prepare.md#create-a-vault)
2. Ouvrez le coffre dans le portail, puis sélectionnez **Sauvegarde** dans la section **Démarrage**.

    ![Panneau Sauvegarde](./media/backup-azure-vms-encryption/select-backup.png)

3. Dans **Objectif de sauvegarde** > **Où s’exécute votre charge de travail ?** , sélectionnez **Azure**.
4. Dans **Que voulez-vous sauvegarder ?** , sélectionnez **Machine virtuelle** > **OK**.

      ![Panneau Scénario](./media/backup-azure-vms-encryption/select-backup-goal-one.png)

5. Dans **Stratégie de sauvegarde** > **Choisir une stratégie de sauvegarde**, sélectionnez la stratégie à associer au coffre. Cliquez ensuite sur **OK**.
    - Une stratégie de sauvegarde spécifie le moment auquel les sauvegardes sont effectuées ainsi que leur durée de stockage.
    - Les détails de la stratégie par défaut sont répertoriés dans le menu déroulant à l’écran.

    ![Ouvrir le panneau Scénario](./media/backup-azure-vms-encryption/select-backup-goal-two.png)

6. Si vous ne souhaitez pas utiliser la stratégie par défaut, sélectionnez **Créer** et [Créer une stratégie personnalisée](backup-azure-arm-vms-prepare.md#create-a-custom-policy).

7. Cliquez sur les machines virtuelles chiffrées que vous souhaitez sauvegarder à l’aide de la stratégie sélectionnée, puis sélectionnez **OK**.

      ![Sélectionner des machines virtuelles chiffrées](./media/backup-azure-vms-encryption/selected-encrypted-vms.png)

8. Si vous utilisez Azure Key Vault, dans la page du coffre, vous voyez un message indiquant que Sauvegarde Azure a besoin d’un accès en lecture seule aux clés et secrets dans le coffre de clés.

    - Si vous recevez le message ci-après, aucune action n’est requise.

        ![Accès OK](./media/backup-azure-vms-encryption/access-ok.png)

    - Si vous recevez le message ci-après, vous devez définir des autorisations comme décrit dans la [procédure ci-dessous](#provide-permissions).

        ![Avertissement concernant l’accès](./media/backup-azure-vms-encryption/access-warning.png)

9. Cliquez sur **Activer la sauvegarde** pour déployer la stratégie de sauvegarde dans le coffre et activer la sauvegarde pour les machines virtuelles sélectionnées.

## <a name="trigger-a-backup-job"></a>Déclencher une tâche de sauvegarde

La sauvegarde initiale s’exécutera conformément à la planification, mais vous pouvez l’exécuter immédiatement comme suit :

1. Dans le menu du coffre, cliquez sur **Éléments de sauvegarde**.
2. Sur **Éléments de sauvegarde**, cliquez sur **Machine virtuelle Azure**.
3. Dans la liste **Éléments de sauvegarde**, cliquez sur le bouton de sélection (...).
4. Cliquez sur **Sauvegarder maintenant**.
5. Dans **Sauvegarder maintenant**, utilisez le contrôle de calendrier pour sélectionner le dernier jour de rétention du point de récupération. Cliquez ensuite sur **OK**.
6. Surveiller les notifications du portail. Vous pouvez surveiller la progression du travail dans le tableau de bord du coffre > **Travaux de sauvegarde** > **En cours d’exécution**. Selon la taille de votre machine virtuelle, la création de la sauvegarde initiale peut prendre un certain temps.

## <a name="provide-permissions"></a>Fournir des autorisations

L’agent de machine virtuelle Azure a besoin d’un accès en lecture seule aux clés et secrets ainsi qu’aux machines virtuelles associées.

- Votre coffre de clés est associé au locataire Azure AD de l’abonnement Azure. Si vous êtes **utilisateur membre**, Sauvegarde Azure acquiert l’accès au coffre de clés sans qu’aucune autre action soit nécessaire.
- Si vous êtes **utilisateur invité**, vous devez accorder des autorisations afin que Sauvegarde Azure puisse accéder au coffre de clés.

Pour définir des autorisations :

1. Dans le portail Azure, sélectionnez **Tous les services**, puis recherchez **Coffres de clés**.
2. Sélectionnez le coffre de clés associé à la machine virtuelle chiffrée en cours de sauvegarde.
3. Sélectionnez **Stratégies d’accès** > **Ajouter**.
4. Sélectionnez **Sélectionner le principal**, puis tapez **Gestion des sauvegardes**.
5. Sélectionnez **Service de gestion des sauvegardes** > **Sélectionner**.

    ![Sélection du service de sauvegarde](./media/backup-azure-vms-encryption/select-backup-service.png)

6. Dans **Ajouter une stratégie d’accès** > **Configurer à partir du modèle (facultatif)** , sélectionnez **Sauvegarde Azure**.
    - Les autorisations requises sont préremplies dans les listes déroulantes **Autorisations de clé** et **Autorisations du secret**.
    - Si votre machine virtuelle est chiffrée à l’aide de **BEK uniquement**, supprimez la sélection pour **Autorisations de clé** dans la mesure où vous avez uniquement besoin d’autorisations pour les secrets.

    ![Sélection de Sauvegarde Azure](./media/backup-azure-vms-encryption/select-backup-template.png)

7. Cliquez sur **OK**. **Service de gestion des sauvegardes** est ajouté à **Stratégies d’accès**.

    ![Stratégies d’accès](./media/backup-azure-vms-encryption/backup-service-access-policy.png)

8. Cliquez sur **Enregistrer** pour fournir les autorisations à Sauvegarde Azure.

## <a name="restore-an-encrypted-vm"></a>Restaurer une machine virtuelle chiffrée

Vous restaurez des machines virtuelles chiffrées comme suit :

1. [Restaurez le disque de la machine virtuelle](backup-azure-arm-restore-vms.md#restore-disks).
2. Recréez l’instance de machine virtuelle en procédant de l’une des manières suivantes :
    1. Utilisez le modèle généré durant l’opération de restauration pour personnaliser les paramètres de la machine virtuelle et déclencher le déploiement de celle-ci. [Plus d’informations](backup-azure-arm-restore-vms.md#use-templates-to-customize-a-restored-vm)
    2. Créez une machine virtuelle à partir des disques restaurés à l’aide de PowerShell. [Plus d’informations](backup-azure-vms-automation.md#create-a-vm-from-restored-disks)
3. Pour les machines virtuelles Linux, réinstallez l’extension ADE pour que les disques de données soient ouverts et montés.

## <a name="next-steps"></a>Étapes suivantes

Si vous rencontrez des problèmes, consultez les articles suivants :

- [Erreurs courantes](backup-azure-vms-troubleshoot.md) liées à la sauvegarde et à la restauration de machines virtuelles Azure chiffrées.
- Problèmes liés à l’[extension de sauvegarde ou à l’agent de machine virtuelle Azure](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md).
