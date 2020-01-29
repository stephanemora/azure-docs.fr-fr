---
title: FAQ - Sauvegarder des machines virtuelles Azure
description: Cet article fournit des réponses à des questions courantes sur la sauvegarde des machines virtuelles Azure avec le service Sauvegarde Microsoft Azure.
ms.reviewer: sogup
ms.topic: conceptual
ms.date: 09/17/2019
ms.openlocfilehash: d70f4832daba59739d6798517902e921927194d6
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/21/2020
ms.locfileid: "76293978"
---
# <a name="frequently-asked-questions-back-up-azure-vms"></a>Forum aux questions - Sauvegarde de machines virtuelles Azure

Cet article fournit des réponses à des questions courantes sur la sauvegarde des machines virtuelles Azure avec le service [Sauvegarde Azure](backup-introduction-to-azure-backup.md).

## <a name="backup"></a>Backup

### <a name="which-vm-images-can-be-enabled-for-backup-when-i-create-them"></a>Quelles images de machine virtuelle peuvent être activées pour la sauvegarde lorsque je les crée ?

Lorsque vous créez une machine virtuelle, vous pouvez activer la sauvegarde pour les machines virtuelles exécutant des [systèmes d’exploitation pris en charge](backup-support-matrix-iaas.md#supported-backup-actions).

### <a name="is-the-backup-cost-included-in-the-vm-cost"></a>Le coût de la sauvegarde est-il inclus dans le coût de la machine virtuelle ?

Non. Les coûts de sauvegarde sont distincts des coûts d’une machine virtuelle. Apprenez-en davantage sur la [tarification de la Sauvegarde Azure](https://azure.microsoft.com/pricing/details/backup/).

### <a name="which-permissions-are-required-to-enable-backup-for-a-vm"></a>Quelles autorisations sont requises pour activer la sauvegarde sur une machine virtuelle ?

Si vous êtes un contributeur de machines virtuelles, vous pouvez activer la sauvegarde sur la machine virtuelle. Si vous utilisez un rôle personnalisé, vous devez disposer des autorisations suivantes pour activer la sauvegarde sur la machine virtuelle :

- Microsoft.RecoveryServices/Vaults/write
- Microsoft.RecoveryServices/Vaults/read
- Microsoft.RecoveryServices/locations/*
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/*/read
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write
- Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write
- Microsoft.RecoveryServices/Vaults/backupPolicies/read
- Microsoft.RecoveryServices/Vaults/backupPolicies/write

Si votre coffre Recovery Services et votre machine virtuelle ont des groupes de ressources différents, assurez-vous que vous disposez des autorisations d’écriture dans le groupe de ressources du coffre Recovery Services.  

### <a name="does-an-on-demand-backup-job-use-the-same-retention-schedule-as-scheduled-backups"></a>Est-ce qu’un travail de sauvegarde à la demande utilise le même planning de conservation que les autres sauvegardes planifiées ?

Non. Spécifiez la plage de rétention pour un travail de sauvegarde à la demande. Par défaut, il est conservé pendant 30 jours s’il a été déclenché à partir du portail.

### <a name="i-recently-enabled-azure-disk-encryption-on-some-vms-will-my-backups-continue-to-work"></a>J’ai récemment activé Azure Disk Encryption sur certaines machines virtuelles. Est-ce que mes sauvegardes continueront de fonctionner ?

Accordez au service Sauvegarde Azure l’autorisation d’accéder à Key Vault. Spécifiez les autorisations dans PowerShell, comme décrit dans la section **Activer la sauvegarde** de la documentation sur [PowerShell et Sauvegarde Azure](backup-azure-vms-automation.md).

### <a name="i-migrated-vm-disks-to-managed-disks-will-my-backups-continue-to-work"></a>J’ai migré des disques de machine virtuelle vers des disques managés. Est-ce que mes sauvegardes continueront de fonctionner ?

Oui, les sauvegardes fonctionnent de manière transparente. Vous n’avez rien à reconfigurer.

### <a name="why-cant-i-see-my-vm-in-the-configure-backup-wizard"></a>Pourquoi je ne vois pas ma machine virtuelle dans l’Assistant Configuration de la sauvegarde ?

L’Assistant liste uniquement les machines virtuelles situées dans la même région que le coffre et qui ne sont pas déjà en cours de sauvegarde.

### <a name="my-vm-is-shut-down-will-an-on-demand-or-a-scheduled-backup-work"></a>Ma machine virtuelle est arrêtée. Les sauvegardes à la demande et planifiées fonctionneront-elles ?

Oui. Les sauvegardes s’exécutent quand une machine est arrêtée. Le point de récupération est marqué comme étant cohérent en cas d’incident.

### <a name="can-i-cancel-an-in-progress-backup-job"></a>Puis-je annuler un travail de sauvegarde en cours ?

Oui. Vous pouvez annuler un travail de sauvegarde dont la **prise de l’instantané est en cours**. Vous ne pouvez pas annuler un travail si un transfert de données à partir de l’instantané est en cours.

### <a name="i-enabled-lock-on-resource-group-created-by-azure-backup-service-ie-azurebackuprg_geo_number-will-my-backups-continue-to-work"></a>J’ai verrouillé le groupe de ressources créé par le Service de sauvegarde Azure (ex. `AzureBackupRG_<geo>_<number>`). Est-ce que mes sauvegardes continueront de fonctionner ?

Si vous verrouillez le groupe de ressources créé par le Service de sauvegarde Azure, les sauvegardes échoueront, car il existe une limite maximale de 18 points de restauration.

L’utilisateur doit supprimer le verrou et effacer la collection des points de restauration à partir de ce groupe de ressources afin d’assurer la réussite des futures sauvegardes, [suivez ces étapes](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#clean-up-restore-point-collection-from-azure-portal) pour supprimer la collection des points de restauration.

### <a name="does-azure-backup-support-standard-ssd-managed-disks"></a>Le service Sauvegarde Microsoft Azure prend-il en charge les disques managés SSD standard ?

Oui, le service Sauvegarde Azure prend en charge les [disques managés SSD standard](https://azure.microsoft.com/blog/announcing-general-availability-of-standard-ssd-disks-for-azure-virtual-machine-workloads/).

### <a name="can-we-back-up-a-vm-with-a-write-accelerator-wa-enabled-disk"></a>Pouvons-nous sauvegarder une machine virtuelle dotée d’un disque avec Accélérateur d’écriture ?

Il n’est pas possible de prendre des instantanés sur un disque avec Accélérateur d’écriture. Toutefois, le service Sauvegarde Azure peut exclure un tel disque de la sauvegarde.

### <a name="i-have-a-vm-with-write-accelerator-wa-disks-and-sap-hana-installed-how-do-i-back-up"></a>J’ai une machine virtuelle dotée de disques avec Accélérateur d’écriture et sur laquelle SAP HANA est installé. Comment faire pour effectuer une sauvegarde ?

Sauvegarde Azure ne peut pas sauvegarder le disque avec Accélérateur d’écriture, mais peut l’exclure de la sauvegarde. Toutefois, la sauvegarde ne garantit pas la cohérence de la base de données car les informations situées sur le disque avec Accélérateur d’écriture ne sont pas sauvegardées. Vous pouvez sauvegarder des disques dans une telle configuration si vous souhaitez que la sauvegarde porte sur les disques du système d’exploitation et les disques sans Accélérateur d’écriture.

Nous exécutons une préversion privée pour une sauvegarde SAP HANA avec un RPO de 15 minutes. Cette version est générée de manière similaire à la sauvegarde de base de données SQL et utilise l’interface backInt pour des solutions tierces certifiées par SAP HANA. Si vous êtes intéressé, envoyez-nous un e-mail à l’adresse `AskAzureBackupTeam@microsoft.com` avec l’objet **Inscription à la préversion privée pour la sauvegarde de SAP HANA sur des machines virtuelles Azure**.

### <a name="what-is-the-maximum-delay-i-can-expect-in-backup-start-time-from-the-scheduled-backup-time-i-have-set-in-my-vm-backup-policy"></a>Quel est le délai maximal de lancement de la sauvegarde à partir de l’heure de sauvegarde planifiée définie dans ma stratégie de sauvegarde de machine virtuelle ?

La sauvegarde planifiée est déclenchée dans les 2 heures suivant l’heure de sauvegarde planifiée. Par exemple, si 100 machines virtuelles sont associées à une heure de début de sauvegarde planifiée sur 2 h 00, celle-ci débutera au maximum à 4 h 00. Si les sauvegardes planifiées ont été suspendues en raison d’une panne et ont repris, la sauvegarde peut démarrer en dehors de cette fenêtre de 2 heures planifiée.

### <a name="what-is-the-minimum-allowed-retention-range-for-daily-backup-point"></a>Quelle est la plage de rétention autorisée minimale pour le point de sauvegarde quotidien ?

La stratégie de sauvegarde de la machine virtuelle Azure prend en charge une plage de rétention minimale allant de 7 à 9 999 jours. Toute modification apportée à une stratégie de sauvegarde de machine virtuelle existante avec une plage de rétention inférieure à sept jours nécessitera une mise à jour pour répondre à la durée de rétention minimale de sept jours.

### <a name="can-i-backup-or-restore-selective-disks-attached-to-a-vm"></a>Puis-je sauvegarder ou restaurer des disques sélectifs attachés à une machine virtuelle ?

La sauvegarde Azure prend désormais en charge la sauvegarde et la restauration sélectives de disque à l’aide de la solution de sauvegarde de machine virtuelle Azure.

Aujourd’hui,la sauvegarde Azure prend en charge la sauvegarde de tous les disques (système d’exploitation et données) dans une machine virtuelle à l’aide de la solution de sauvegarde des machines virtuelles. Avec la fonctionnalité d’exclusion de disque, vous avez la possibilité de sauvegarder un seul ou plusieurs disques de données dans une machine virtuelle. Cette fonctionnalité offre une solution efficace et économique pour vos besoins en matière de sauvegarde et de restauration. Chaque point de récupération contient des données des disques inclus dans l’opération de sauvegarde, ce qui vous permet de disposer d’un sous-ensemble de disques restaurés à partir du point de récupération donné au cours de l’opération de restauration. Cela s’applique à la restauration de la capture instantanée et du coffre.

Cette solution se révèle particulièrement utile dans les scénarios suivants :
  
1. Vous avez des données critiques à sauvegarder sur un seul disque et vous ne voulez pas sauvegarder les autres disques attachés à une machine virtuelle. Cela réduit les coûts de stockage de sauvegarde.  
2. Vous disposez d’autres solutions de sauvegarde pour une partie des données de vos machines virtuelles. Par exemple, vous sauvegardez vos bases de données ou vos données avec une autre solution de sauvegarde de charge de travail, et vous souhaitez utiliser la sauvegarde au niveau de la machine virtuelle Azure pour le reste de vos disques et données afin de générer un système efficace et robuste en utilisant les meilleures fonctionnalités offertes.

Pour vous inscrire à la version préliminaire, écrivez-nous à l’adresse suivante : AskAzureBackupTeam@microsoft.com

## <a name="restore"></a>Restaurer

### <a name="how-do-i-decide-whether-to-restore-disks-only-or-a-full-vm"></a>Comment déterminer si je dois restaurer des disques uniquement ou une machine virtuelle complète ?

Considérez qu’une restauration de machine virtuelle est une option de création rapide d’une machine virtuelle Azure. Cette option modifie les noms des disques, les conteneurs utilisés par les disques, les adresses IP publiques et les noms d’interface réseau. Cette modification permet de conserver des ressources uniques lors de la création d’une machine virtuelle. La machine virtuelle n’est pas ajoutée à un groupe à haute disponibilité.

L’option de restauration de disque convient dans les cas suivants :

- Vous voulez personnaliser la machine virtuelle créée. Par exemple, vous voulez modifier sa taille.
- Vous voulez ajouter des paramètres de configuration qui n’existaient pas au moment de la sauvegarde.
- Vous voulez contrôler la convention d’affectation de noms pour les ressources créées.
- Vous voulez ajouter la machine virtuelle à un groupe à haute disponibilité.
- Vous voulez ajouter tout autre paramètre devant être configuré à l’aide de PowerShell ou d’un modèle.

### <a name="can-i-restore-backups-of-unmanaged-vm-disks-after-i-upgrade-to-managed-disks"></a>Puis-je restaurer des sauvegardes de disques de machine virtuelle non managés après avoir effectué une mise à niveau vers des disques managés ?

Oui, vous pouvez utiliser des sauvegardes effectuées avant la migration des disques non managés vers des disques managés.

### <a name="how-do-i-restore-a-vm-to-a-restore-point-before-the-vm-was-migrated-to-managed-disks"></a>Comment restaurer une machine virtuelle à un point de restauration antérieur à la migration de la machine virtuelle vers des disques managés ?

Le processus de restauration ne change pas. Si le point de récupération est un point dans le temps où la machine virtuelle avait des disques non managés, vous pouvez [restaurer les disques en tant que disques non managés](tutorial-restore-disk.md#unmanaged-disks-restore). Si la machine virtuelle avait des disques managés, vous pouvez [restaurer les disques en tant que disques managés](tutorial-restore-disk.md#managed-disk-restore). Vous pouvez ensuite [créer une machine virtuelle à partir de ces disques](tutorial-restore-disk.md#create-a-vm-from-the-restored-disk).

[Découvrez](backup-azure-vms-automation.md#restore-an-azure-vm) comment effectuer ces opérations dans PowerShell.

### <a name="can-i-restore-the-vm-thats-been-deleted"></a>Puis-je restaurer la machine virtuelle qui a été supprimée ?

Oui. Même si vous supprimez la machine virtuelle, vous pouvez accéder à l’élément de sauvegarde correspondant dans le coffre et effectuer une restauration à partir d’un point de récupération.

### <a name="how-to-restore-a-vm-to-the-same-availability-sets"></a>Comment restaurer une machine virtuelle dans les mêmes groupes à haute disponibilité ?

Pour une machine virtuelle Azure à disque managé, la restauration dans des groupes à haute disponibilité est activée en proposant une option dans un modèle lors de la restauration en tant que disque managé. Ce modèle dispose du paramètre d’entrée appelé **Groupes à haute disponibilité**.

### <a name="how-do-we-get-faster-restore-performances"></a>Comment accélérer les performances de restauration ?

La fonctionnalité [Restauration instantanée](backup-instant-restore-capability.md) permet d’accélérer les sauvegardes et les restaurations instantanées à partir des captures instantanées.

### <a name="what-happens-when-we-change-the-key-vault-settings-for-the-encrypted-vm"></a>Que se passe-t-il lorsque nous modifions les paramètres du coffre de clés pour la machine virtuelle chiffrée ?

Une fois que vous avez modifié les paramètres KeyVault pour la machine virtuelle chiffrée, les sauvegardes continuent de fonctionner avec le nouvel ensemble de détails. Toutefois, après la restauration à partir d’un point de récupération antérieur à la modification, vous devrez restaurer les secrets dans un KeyVault avant de pouvoir créer la machine virtuelle à partir de celui-ci. Pour plus d’informations, voir cet [article](https://docs.microsoft.com/azure/backup/backup-azure-restore-key-secret).

Des opérations telles que la restauration de secret/clé ne nécessitent pas cette étape et le même coffre de clés peut être utilisé après restauration.

### <a name="can-i-access-the-vm-once-restored-due-to-a-vm-having-broken-relationship-with-domain-controller"></a>Puis-je accéder à la machine virtuelle après restauration en raison d’une rupture de la relation entre la machine virtuelle et le contrôleur de domaine ?

Oui, vous accédez à la machine virtuelle après restauration en raison d’une rupture de la relation entre la machine virtuelle et le contrôleur de domaine. Pour plus d’informations, voir cet [article](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#post-restore-steps).

## <a name="manage-vm-backups"></a>Gérer les sauvegardes de machine virtuelle

### <a name="what-happens-if-i-modify-a-backup-policy"></a>Que se passe-t-il si je modifie une stratégie de sauvegarde ?

La machine virtuelle est sauvegardée selon la planification et les paramètres de conservation indiqués dans la stratégie nouvelle ou modifiée.

- Si la conservation est prolongée, les points de récupération existants sont marqués et conservés conformément à la nouvelle stratégie.
- Si la conservation est réduite, ils sont marqués de sorte à être nettoyés lors de la prochaine tâche de nettoyage, puis supprimés.

### <a name="how-do-i-move-a-vm-backed-up-by-azure-backup-to-a-different-resource-group"></a>Comment déplacer une machine virtuelle sauvegardée par Sauvegarde Azure dans un autre groupe de ressources ?

1. Interrompez temporairement la sauvegarde et conservez les données de sauvegarde.
2. Déplacez la machine virtuelle vers le groupe de ressources cible.
3. Réactivez la sauvegarde dans le nouveau ou même coffre.

Vous pouvez restaurer la machine virtuelle à partir des points de restauration disponibles créés avant l’opération de déplacement.

### <a name="is-there-a-limit-on-number-of-vms-that-can-beassociated-with-a-same-backup-policy"></a>Existe-t’il un nombre limite de machines virtuelles pouvant être associées à une même stratégie de sauvegarde ?

Oui, il existe une limite de 100 machines virtuelles pouvant être associées à la même stratégie de sauvegarde à partir du portail. Pour plus de 100 machines virtuelles, nous recommandons de créer plusieurs stratégies de sauvegarde avec la même planification ou une planification différente.
