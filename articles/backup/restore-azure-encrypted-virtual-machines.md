---
title: Restaurer des machines virtuelles Azure chiffrées
description: Décrit comment restaurer des machines virtuelles Azure chiffrées avec le service Sauvegarde Azure.
ms.topic: conceptual
ms.date: 08/20/2021
ms.openlocfilehash: 95d665fb2ae2e1dc0427090842cddbecfb3c3706
ms.sourcegitcommit: ef448159e4a9a95231b75a8203ca6734746cd861
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/30/2021
ms.locfileid: "123187863"
---
# <a name="restore-encrypted-azure-virtual-machines"></a>Restaurer des machines virtuelles Azure chiffrées

Cet article explique comment restaurer des machines virtuelles Azure Windows ou Linux avec des disques chiffrés à l’aide du service [Sauvegarde Azure](backup-overview.md). Pour plus d'informations, consultez [Chiffrement des sauvegardes de machines virtuelles Azure](backup-azure-vms-introduction.md#encryption-of-azure-vm-backups).


## <a name="before-you-start"></a>Avant de commencer

Passer en revue les limitations connues avant de commencer la restauration d’une machine virtuelle chiffrée

- Vous pouvez sauvegarder et restaurer des machines virtuelles chiffrées par ADE dans le même abonnement.
- Sauvegarde Azure prend en charge les machines virtuelles chiffrées à l’aide de clés autonomes. Aucune clé appartenant à un certificat utilisé pour chiffrer une machine virtuelle n’est prise en charge.
- Les machines virtuelles chiffrées par ADE ne peuvent pas être récupérées au niveau du fichier/dossier. Vous devez récupérer la machine virtuelle entière pour restaurer des fichiers et des dossiers.
- Quand vous restaurez une machine virtuelle, vous ne pouvez pas utiliser l’option de [remplacement de la machine virtuelle existante](backup-azure-arm-restore-vms.md#restore-options) pour les machines virtuelles chiffrées par ADE. Cette option est uniquement prise en charge pour les disques managés non chiffrés.


## <a name="restore-an-encrypted-vm"></a>Restaurer une machine virtuelle chiffrée

Vous ne pouvez restaurer les machines virtuelles chiffrées qu’en restaurant le disque des machines virtuelles et en créant une instance de machine virtuelle, comme expliqué ci-dessous. Le **remplacement d’un disque existant sur la machine virtuelle existante**, la **création d’une machine virtuelle à partir de points de restauration** et la **restauration au niveau des fichiers ou du dossier** ne sont actuellement pas pris en charge.
 
Suivez les étapes ci-dessous pour restaurer des machines virtuelles chiffrées :

### <a name="step-1-restore-the-vm-disk"></a>**Étape 1** : restaurer le disque de la machine virtuelle

1. Dans **Configuration de la restauration** > **Créer** > **Type de restauration**, sélectionnez **Restaurer des disques**.
1. Dans **Groupe de ressources**, sélectionnez un groupe de ressources existant pour les disques restaurés ou créez-en un avec un nom global unique.
1. Dans **Emplacement intermédiaire**, spécifiez le compte de stockage dans lequel les disques durs virtuels doivent être copiés. [Plus d’informations](backup-azure-arm-restore-vms.md#storage-accounts)

    ![Sélectionner un groupe de ressources et un emplacement intermédiaire](./media/backup-azure-arm-restore-vms/trigger-restore-operation1.png)

1. Sélectionnez **Restaurer** pour démarrer l’opération de restauration.

Quand votre machine virtuelle utilise des disques managés et que vous sélectionnez l’option **Créer une machine virtuelle**, Sauvegarde Azure n’utilise pas le compte de stockage spécifié. Dans le cas d’une **restauration de disques** et d’une **restauration instantanée**, le compte de stockage est utilisé uniquement pour stocker le modèle. Les disques managés sont créés dans le groupe de ressources spécifié.
Lorsque votre machine virtuelle utilise des disques non gérés, ceux-ci sont restaurés en tant qu’objets blob dans le compte de stockage.

   > [!NOTE]
   > Après avoir restauré le disque de la machine virtuelle, vous pouvez échanger manuellement le disque du système d’exploitation de la machine virtuelle d’origine avec le disque de machine virtuelle restauré sans le recréer. [Plus d’informations](https://azure.microsoft.com/blog/os-disk-swap-managed-disks/)

### <a name="step-2-recreate-the-virtual-machine-instance"></a>**Étape 2** : recréer l’instance de machine virtuelle 

Effectuez l’une des actions suivantes :

- Utilisez le modèle généré pendant l’opération de restauration pour personnaliser les paramètres de la machine virtuelle et déclencher le déploiement de celle-ci. [Plus d’informations](backup-azure-arm-restore-vms.md#use-templates-to-customize-a-restored-vm)
  >[!NOTE]
   >Lors du déploiement du modèle, vérifiez les conteneurs du compte de stockage et les paramètres public/privé.
- Créez une machine virtuelle à partir des disques restaurés à l’aide de PowerShell. [Plus d’informations](backup-azure-vms-automation.md#create-a-vm-from-restored-disks)

### <a name="step-3-restore-an-encrypted-linux-vm"></a>**Étape 3** : restaurer une machine virtuelle Linux chiffrée

Réinstallez l’extension ADE pour que les disques de données soient ouverts et montés.

## <a name="cross-region-restore-for-an-encrypted-azure-vm"></a>Restauration inter-régions pour une machine virtuelle Azure chiffrée

Sauvegarde Azure prend en charge la restauration inter-régions de machines virtuelles Azure chiffrées vers les [régions jumelées Azure](../best-practices-availability-paired-regions.md). Découvrez comment [activer la restauration inter-régions](backup-create-rs-vault.md#configure-cross-region-restore) pour une machine virtuelle chiffrée.

## <a name="move-an-encrypted-azure-vm"></a>Déplacer une machine virtuelle Azure chiffrée

Le déplacement d’une machine virtuelle chiffrée dans un coffre ou un groupe de ressources est identique au déplacement d’une machine virtuelle Azure sauvegardée. Consultez .

- [Étapes de déplacement d’une machine virtuelle Azure vers un autre coffre Recovery Services](backup-azure-move-recovery-services-vault.md#move-an-azure-virtual-machine-to-a-different-recovery-service-vault)
- [Étapes de déplacement d’une machine virtuelle Azure vers un autre groupe de ressources ou un autre abonnement](../azure-resource-manager/management/move-resource-group-and-subscription.md)


## <a name="next-steps"></a>Étapes suivantes

Si vous rencontrez des problèmes, consultez les articles suivants :

- [Erreurs courantes](backup-azure-vms-troubleshoot.md) liées à la sauvegarde et à la restauration de machines virtuelles Azure chiffrées.
- Problèmes liés à l’[extension de sauvegarde ou à l’agent de machine virtuelle Azure](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md).



