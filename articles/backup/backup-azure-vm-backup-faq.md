---
title: Forum aux questions sur la sauvegarde des machines virtuelles Azure avec sauvegarde Azure
description: Réponses aux questions courantes sur la sauvegarde des machines virtuelles Azure avec sauvegarde Azure.
services: backup
author: sogup
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 03/22/2019
ms.author: sogup
ms.openlocfilehash: 9f233af316bd6022b93a7208bf3fae37e913e6af
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2019
ms.locfileid: "58885262"
---
# <a name="frequently-asked-questions-back-up-azure-vms"></a>Forum aux questions : sauvegarder des machines virtuelles Azure

Cet article répond aux questions courantes sur la sauvegarde des machines virtuelles Azure avec le [sauvegarde Azure](backup-introduction-to-azure-backup.md) service.


## <a name="backup"></a>Sauvegarde

### <a name="which-vm-images-can-be-enabled-for-backup-when-i-create-them"></a>Les images de machine virtuelle peuvent être activées pour la sauvegarde lorsque je crée les ?
Lorsque vous créez une machine virtuelle, vous pouvez activer la sauvegarde pour les machines virtuelles en cours d’exécution [les systèmes d’exploitation pris en charge](backup-support-matrix-iaas.md#supported-backup-actions)
 
### <a name="is-the-backup-cost-included-in-the-vm-cost"></a>Est le coût de sauvegarde inclus dans le coût de la machine virtuelle ? 

Non. Les coûts de sauvegarde sont distincts des coûts d’une machine virtuelle. En savoir plus sur [tarification d’Azure Backup](https://azure.microsoft.com/pricing/details/backup/).
 
### <a name="which-permissions-are-required-to-enable-backup-for-a-vm"></a>Les autorisations sont nécessaires pour activer la sauvegarde d’une machine virtuelle ? 

Si vous êtes un contributeur de machine virtuelle, vous pouvez activer la sauvegarde sur la machine virtuelle. Si vous utilisez un rôle personnalisé, vous devez disposer des autorisations suivantes pour activer la sauvegarde sur la machine virtuelle : 

- Microsoft.RecoveryServices/Vaults/write 
- Microsoft.RecoveryServices/Vaults/read 
- Microsoft.RecoveryServices/locations/* 
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/*/read 
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read 
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write 
- Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write 
- Microsoft.RecoveryServices/Vaults/backupPolicies/read 
- Microsoft.RecoveryServices/Vaults/backupPolicies/write 
 
Si votre coffre Recovery Services et la machine virtuelle ont différents groupes de ressources, assurez-vous que vous disposez des autorisations d’écriture dans le groupe de ressources pour le coffre Recovery Services.  


### <a name="what-azure-vms-can-you-back-up-using-azure-backup"></a>Quelles machines virtuelles Azure pouvez-vous sauvegarder à l’aide de Sauvegarde Azure ?

Examinez le [matrice de prise en charge](backup-support-matrix-iaas.md) pour les détails de la prise en charge et les limitations.

### <a name="does-an-on-demand-backup-job-use-the-same-retention-schedule-as-scheduled-backups"></a>Est-ce qu’un travail de sauvegarde à la demande utilise le même planning de conservation que les autres sauvegardes planifiées ?
Non. Spécifiez la durée de rétention pour un travail de sauvegarde à la demande. Par défaut, il est conservé pendant 30 jours s’il a été déclenché à partir du portail.

### <a name="i-recently-enabled-azure-disk-encryption-on-some-vms-will-my-backups-continue-to-work"></a>J’ai récemment activé Azure Disk Encryption sur certaines machines virtuelles. Est-ce que mes sauvegardes continueront de fonctionner ?
Accordez des autorisations pour la sauvegarde Azure à accéder à Key Vault. Spécifiez les autorisations dans PowerShell, comme décrit dans la section **Activer la sauvegarde** de la documentation sur [PowerShell et Sauvegarde Azure](backup-azure-vms-automation.md).

### <a name="i-migrated-vm-disks-to-managed-disks-will-my-backups-continue-to-work"></a>J’ai migré des disques de machine virtuelle vers des disques managés. Est-ce que mes sauvegardes continueront de fonctionner ?
Oui, les sauvegardes fonctionnent de manière transparente. Vous n’avez rien à reconfigurer.

### <a name="why-cant-i-see-my-vm-in-the-configure-backup-wizard"></a>Pourquoi je ne vois pas ma machine virtuelle dans l’Assistant Configuration de la sauvegarde ?
L’Assistant liste uniquement les machines virtuelles situées dans la même région que le coffre et qui ne sont pas déjà en cours de sauvegarde.

### <a name="my-vm-is-shut-down-will-an-on-demand-or-a-scheduled-backup-work"></a>Ma machine virtuelle est arrêtée. Les sauvegardes à la demande et planifiées fonctionneront-elles ?
Oui. Les sauvegardes s’exécutent quand une machine est arrêtée. Le point de récupération est marqué comme étant cohérent en cas d’incident.

### <a name="can-i-cancel-an-in-progress-backup-job"></a>Puis-je annuler un travail de sauvegarde en cours ?
Oui. Vous pouvez annuler un travail de sauvegarde dont la **prise de l’instantané est en cours**. Vous ne pouvez pas annuler un travail si un transfert de données à partir de l’instantané est en cours.

### <a name="i-enabled-lock-on-resource-group-created-by-azure-backup-service-ie-azurebackuprggeonumber-will-my-backups-continue-to-work"></a>J’ai activé le verrou sur le groupe de ressources créé par le Service de sauvegarde Azure (ex.) `AzureBackupRG_<geo>_<number>`), mes sauvegardes continueront de fonctionner ?
Si vous verrouillez le groupe de ressources créé par le Service de sauvegarde Azure, les sauvegardes commenceront à échouer car il existe une limite maximale de 18 points de restauration.

L’utilisateur doit supprimer le verrou et effacer la collection de points de restauration à partir de ce groupe de ressources afin de rendre les futures sauvegardes réussie, [suivez ces étapes](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#clean-up-restore-point-collection-from-azure-portal) à supprimer de la collection de points de restauration.

### <a name="does-the-backup-policy-consider-daylight-saving-time-dst"></a>La stratégie de sauvegarde tient-elle compte de l’heure d’été ?
Non. La date et l’heure indiquées sur votre ordinateur local correspond à la date et l’heure locales avec l’heure d’été appliquée. L’heure définie pour les sauvegardes planifiées risque donc de différer de l’heure locale en période d’heure d’été.

### <a name="how-many-data-disks-can-i-attach-to-a-vm-backed-up-by-azure-backup"></a>Combien de disques de données je peux attacher à une machine virtuelle sauvegardée par Sauvegarde Azure ?
Le service Sauvegarde Azure peut sauvegarder des machines virtuelles dotées de 16 disques au maximum. Une prise en charge de 16 disques est fournie dans la [restauration instantanée](backup-instant-restore-capability.md).

### <a name="does-azure-backup-support-standard-ssd-managed-disk"></a>Le service Sauvegarde Azure prend-il en charge les disques managés SSD standard ?
Le service Sauvegarde Azure prend en charge les [disques managés SSD standard](https://azure.microsoft.com/blog/announcing-general-availability-of-standard-ssd-disks-for-azure-virtual-machine-workloads/). Disques SSD gérés fournissent un nouveau type de stockage durable pour les machines virtuelles Azure. Une prise en charge disques managés SSD est fournie dans la [restauration instantanée](backup-instant-restore-capability.md).

### <a name="can-we-back-up-a-vm-with-a-write-accelerator-wa-enabled-disk"></a>Pouvons-nous sauvegarder une machine virtuelle dotée d’un disque avec Accélérateur d’écriture ?
Il n’est pas possible de prendre des instantanés sur un disque avec Accélérateur d’écriture. Toutefois, le service Sauvegarde Azure peut exclure un tel disque de la sauvegarde. L’exclusion de disques sur les machines virtuelles dotées de disques avec Accélérateur d’écriture est prise en charge uniquement pour les abonnements mis à niveau vers la restauration instantanée.

### <a name="i-have-a-vm-with-write-accelerator-wa-disks-and-sap-hana-installed-how-do-i-back-up"></a>J’ai une machine virtuelle dotée de disques avec Accélérateur d’écriture et sur laquelle SAP HANA est installé. Comment faire pour effectuer une sauvegarde ?
Sauvegarde Azure ne peut pas sauvegarder le disque avec Accélérateur d’écriture, mais peut l’exclure de la sauvegarde. Toutefois, la sauvegarde ne garantit pas la cohérence de la base de données car les informations situées sur le disque avec Accélérateur d’écriture ne sont pas sauvegardées. Vous pouvez sauvegarder des disques dans une telle configuration si vous souhaitez que la sauvegarde porte sur les disques du système d’exploitation et les disques sans Accélérateur d’écriture.

Nous exécutons une préversion privée pour une sauvegarde SAP HANA avec un RPO de 15 minutes. Cette version est générée de manière similaire à la sauvegarde de base de données SQL et utilise l’interface backInt pour des solutions tierces certifiées par SAP HANA. Si vous êtes intéressé, envoyez-nous un e-mail à `AskAzureBackupTeam@microsoft.com` avec l’objet **s’inscrire à la préversion privée pour la sauvegarde de SAP HANA sur des machines virtuelles Azure**.


## <a name="restore"></a>Restore

### <a name="how-do-i-decide-whether-to-restore-disks-only-or-a-full-vm"></a>Comment déterminer si je dois restaurer des disques uniquement ou une machine virtuelle complète ?
Considérez qu’une restauration de machine virtuelle est une option de création rapide d’une machine virtuelle Azure. Cette option modifie les noms de disques, les conteneurs utilisés par les disques, les adresses IP publiques et les noms d’interface réseau. Cette modification permet de conserver des ressources uniques lors de la création d’une machine virtuelle. La machine virtuelle n’est pas ajoutée à un groupe à haute disponibilité.

L’option de restauration de disque convient dans les cas suivants :
  * Vous voulez personnaliser la machine virtuelle créée. Par exemple, modifier la taille.
  * Ajouter des paramètres de configuration qui n’ont pas été il au moment de la sauvegarde.
  * Vous voulez contrôler la convention d’affectation de noms pour les ressources créées.
  * Vous voulez ajouter la machine virtuelle à un groupe à haute disponibilité.
  * Vous voulez ajouter tout autre paramètre devant être configuré à l’aide de PowerShell ou d’un modèle.

### <a name="can-i-restore-backups-of-unmanaged-vm-disks-after-i-upgrade-to-managed-disks"></a>Puis-je restaurer des sauvegardes de disques de machine virtuelle non managés après avoir effectué une mise à niveau vers des disques managés ?
Oui, vous pouvez utiliser des sauvegardes effectuées avant la migration des disques non managés vers des disques managés.
- Par défaut, un travail de restauration de machine virtuelle crée une machine virtuelle non gérée.
- Toutefois, vous pouvez restaurer des disques et les utiliser pour créer une machine virtuelle gérée.

### <a name="how-do-i-restore-a-vm-to-a-restore-point-before-the-vm-was-migrated-to-managed-disks"></a>Comment restaurer une machine virtuelle à un point de restauration antérieur à la migration de la machine virtuelle vers des disques managés ?
Par défaut, un travail de restauration de machine virtuelle crée une machine virtuelle avec des disques non managés. Pour créer une machine virtuelle avec des disques gérés :
1. [Effectuez une restauration vers des disques non managés](tutorial-restore-disk.md#restore-a-vm-disk).
2. [Convertissez les disques restaurés en disques managés](tutorial-restore-disk.md#convert-the-restored-disk-to-a-managed-disk).
3. [Créez une machine virtuelle avec des disques managés](tutorial-restore-disk.md#create-a-vm-from-the-restored-disk).

[Découvrez](backup-azure-vms-automation.md#restore-an-azure-vm) comment effectuer ces opérations dans PowerShell.

### <a name="can-i-restore-the-vm-thats-been-deleted"></a>Puis-je restaurer la machine virtuelle qui a été supprimée ?
Oui. Même si vous supprimez la machine virtuelle, vous pouvez accéder à l’élément de sauvegarde correspondant dans le coffre et effectuer une restauration à partir d’un point de récupération.

### <a name="how-to-restore-a-vm-to-the-same-availability-sets"></a>Comment restaurer une machine virtuelle dans les mêmes groupes à haute disponibilité ?
Pour une machine virtuelle Azure à disque managé, la restauration dans des groupes à haute disponibilité est activée en proposant une option dans un modèle lors de la restauration en tant que disque managé. Ce modèle dispose du paramètre d’entrée appelé **Groupes à haute disponibilité**.

### <a name="how-do-we-get-faster-restore-performances"></a>Comment accélérer les performances de restauration ?
Pour des performances de restauration plus rapides, nous utilisons la fonctionnalité de [restauration instantanée](backup-instant-restore-capability.md).

## <a name="manage-vm-backups"></a>Gérer les sauvegardes de machine virtuelle

### <a name="what-happens-if-i-modify-a-backup-policy"></a>Que se passe-t-il si je modifie une stratégie de sauvegarde ?
La machine virtuelle est sauvegardée selon la planification et les paramètres de conservation indiqués dans la stratégie nouvelle ou modifiée.

- Si la conservation est prolongée, les points de récupération existants sont marqués et conservés conformément à la nouvelle stratégie.
- Si la conservation est réduite, ils sont marqués de sorte à être nettoyés lors de la prochaine tâche de nettoyage, puis supprimés.

### <a name="how-do-i-move-a-vm-backed-up-by-azure-backup-to-a-different-resource-group"></a>Comment déplacer une machine virtuelle sauvegardée par Sauvegarde Azure dans un autre groupe de ressources ?

1. Interrompez temporairement la sauvegarde et conservez les données de sauvegarde.
2. Déplacez la machine virtuelle vers le groupe de ressources cible.
3. Sauvegarde réactivée dans le coffre même ou nouveau.

Vous pouvez restaurer la machine virtuelle à partir des points de restauration disponibles créés avant l’opération de déplacement.
