---
title: Forum aux questions sur la sauvegarde de machines virtuelles Azure
description: 'Réponses aux questions courantes sur : le fonctionnement de la sauvegarde de machine virtuelle Azure, ses limitations et ce qu’il se passe lors d’un changement de stratégie'
services: backup
author: trinadhk
manager: shreeshd
keywords: sauvegarde de la machine virtuelle azure, restauration de la machine virtuelle azure, stratégie de sauvegarde
ms.service: backup
ms.topic: conceptual
ms.date: 8/16/2018
ms.author: trinadhk
ms.openlocfilehash: 910225e74fcd4655a9eff711d3ac1316d948c2b3
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2018
ms.locfileid: "48886192"
---
# <a name="questions-about-the-azure-vm-backup-service"></a>Questions sur le service de sauvegarde de machine virtuelle Azure
Cet article comporte les réponses aux questions fréquentes pour vous aider à comprendre rapidement les composants de la sauvegarde de machine virtuelle Azure. Certaines réponses comportent des liens vers les articles présentant des informations complètes. Vous pouvez également publier des questions sur le service Azure Backup dans le [forum de discussion](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup).

## <a name="configure-backup"></a>Configurer une sauvegarde
### <a name="do-recovery-services-vaults-support-classic-vms-or-resource-manager-based-vms-br"></a>Les coffres Recovery Services prennent-ils en charge les machines virtuelles Azure Classic ou Resource Manager ? <br/>
Les coffres Recovery Services prennent en charge les deux modèles.  Vous pouvez sauvegarder une machine virtuelle classique ou une machine virtuelle Resource Manager dans un coffre Recovery Services.

### <a name="what-configurations-are-not-supported-by-azure-vm-backup"></a>Quelles configurations ne sont pas prises en charge par la sauvegarde de machine virtuelle Azure ?
Consultez [Systèmes d’exploitation pris en charge](backup-azure-arm-vms-prepare.md#supported-operating-systems-for-backup) et [Limitations des sauvegardes de machine virtuelle](backup-azure-arm-vms-prepare.md#limitations-when-backing-up-and-restoring-a-vm)

### <a name="why-cant-i-see-my-vm-in-configure-backup-wizard"></a>Pourquoi je ne peux pas voir ma machine virtuelle dans l’assistant de configuration de la sauvegarde ?
Dans l’assistant de configuration de la sauvegarde, la sauvegarde Azure répertorie uniquement les machines virtuelles qui sont :
  * Actuellement non protégées : vous pouvez vérifier le statut de la sauvegarde d’une machine virtuelle en accédant au panneau de la machine virtuelle et en vérifiant le Statut de la sauvegarde à partir du Menu Paramètres. En savoir plus sur comment [Vérifier l’état de la sauvegarde de machine virtuelle](backup-azure-vms-first-look-arm.md#configure-the-backup-job-from-the-vm-operations-menu)
  * Appartient à la même région que la machine virtuelle

## <a name="backup"></a>Sauvegarde
### <a name="will-on-demand-backup-job-follow-same-retention-schedule-as-scheduled-backups"></a>Est-ce qu’un travail de sauvegarde à la demande suit le même planning de rétention que les autres sauvegardes planifiées ?
Non. Vous devez spécifier la plage de rétention pour un travail de sauvegarde à la demande. Par défaut, il est conservé pendant 30 jours s’il a été déclenché à partir du portail.

### <a name="i-recently-enabled-azure-disk-encryption-on-some-vms-will-my-backups-continue-to-work"></a>J’ai récemment activé Azure Disk Encryption sur certaines machines virtuelles. Est-ce que mes sauvegardes continueront de fonctionner ?
Vous devez accorder l’autorisation au service Azure Backup d’accéder au Key Vault. Vous pouvez fournir ces autorisations dans PowerShell à l’aide des étapes présentées dans la section *Sauvegarde des machines virtuelles Azure* de la documentation [PowerShell](backup-azure-vms-automation.md).

### <a name="i-migrated-disks-of-a-vm-to-managed-disks-will-my-backups-continue-to-work"></a>J’ai effectué une migration des disques d’une machine virtuelle sur des disques gérés. Est-ce que mes sauvegardes continueront de fonctionner ?
Oui, les sauvegardes fonctionnent de façon transparente et sans devoir de nouveau configurer la sauvegarde.

### <a name="my-vm-is-shut-down-will-an-on-demand-or-a-scheduled-backup-work"></a>Ma machine virtuelle est arrêtée. Les sauvegardes à la demande et planifiées fonctionneront-elles ?
Oui. Même lorsqu’une machine est éteinte, les sauvegardes fonctionnent et le point de récupération est marqué comme cohérent en cas d’incident. Pour plus d’informations, consultez la section de [cet article](backup-azure-vms-introduction.md#how-does-azure-back-up-virtual-machines) consacrée à la cohérence des données.

### <a name="can-i-cancel-an-in-progress-backup-job"></a>Puis-je annuler un travail de sauvegarde en cours ?
Oui. Vous pouvez annuler le travail de sauvegarde s’il est dans la phase « Capture instantanée en cours ». **Vous ne pouvez pas annuler un travail si un transfert de données à partir de la capture instantanée est en cours**.

### <a name="i-enabled-resource-group-lock-on-my-backed-up-managed-disk-vms-will-my-backups-continue-to-work"></a>J’ai activé le verrou de groupe de ressources sur mes machines virtuelles à disque géré sauvegardées. Est-ce que mes sauvegardes continueront de fonctionner ?
Si l’utilisateur verrouille le groupe de ressources, le service de sauvegarde ne peut pas supprimer les anciens points de restauration. Pour cette raison, les nouvelles sauvegardes échouent, car une limite maximale de 18 points de restauration est imposée par le back-end. Si vos sauvegardes échouent avec une erreur interne après le verrouillage du groupe de ressources, suivez ces [étapes pour supprimer la collection de points de restauration](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#backup-service-does-not-have-permission-to-delete-the-old-restore-points-due-to-resource-group-lock).

### <a name="does-backup-policy-take-daylight-saving-timedst-into-account"></a>La stratégie de sauvegarde prend-elle en compte l’heure d’été ?
Non. N’oubliez pas que la date et l’heure de votre ordinateur local s’affichent dans votre fuseau local avec le décalage actuel lié à l’heure d’été. La durée configurée pour les sauvegardes planifiées peut donc être différente de votre heure locale en raison de l’heure d’été.

### <a name="maximum-of-how-many-data-disks-can-i-attach-to-a-vm-to-be-backed-up-by-azure-backup"></a>Combien de disques de données au maximum puis-je attacher à une machine virtuelle qui doit être sauvegardée par la Sauvegarde Azure ?
Sauvegarde Azure prend désormais en charge la sauvegarde des machines virtuelles comportant jusqu’à 32 disques. Pour la prise en charge de 32 disques, [mettez à niveau vers la pile de sauvegarde de machine virtuelle Azure V2](backup-upgrade-to-vm-backup-stack-v2.md). Toutes les machines virtuelles qui activent la protection à partir du 24 septembre 2018 seront prises en charge.

### <a name="does-azure-backup-support-standard-ssd-managed-disk"></a>Le service Sauvegarde Azure prend-il en charge les disques managés SSD Standard ?
Le service Sauvegarde Azure prend en charge les [disques managés SSD Standard](https://azure.microsoft.com/blog/announcing-general-availability-of-standard-ssd-disks-for-azure-virtual-machine-workloads/), un nouveau type de stockage durable pour Machines virtuelles Microsoft Azure. Cette prise en charge fonctionne pour les [piles de sauvegarde de machine virtuelle Azure V2](backup-upgrade-to-vm-backup-stack-v2.md).

## <a name="restore"></a>Restore
### <a name="how-do-i-decide-between-restoring-disks-versus-full-vm-restore"></a>Comment choisir entre la restauration des disques et la restauration complète de la machine virtuelle ?
Vous devez considérer la restauration complète de la machine virtuelle Azure comme une option de création rapide. L’option de restauration de la machine virtuelle change les noms des disques, les conteneurs utilisés par les disques, les adresses IP publiques et les noms d’interface réseau. Ces modifications sont nécessaires pour préserver l’unicité des ressources générées lors de la création de la machine virtuelle. Cela n’ajoutera pas la machine virtuelle à un groupe à haute responsabilité.

Utilisez des disques de restauration pour :
  * Personnaliser la machine virtuelle créée à partir de la configuration dans le temps (modification de la taille, par exemple)
  * Ajouter des configurations qui ne sont pas présentes au moment de la sauvegarde
  * Contrôler la convention d’affectation de noms pour les ressources créées
  * Ajouter des machines virtuelles aux groupes à haute disponibilité
  * Pour n’importe quelle autre configuration pouvant être obtenue uniquement à l’aide de PowerShell/d’une définition de modèle déclaratif

### <a name="can-i-use-backups-of-unmanaged-disk-vm-to-restore-after-i-upgrade-my-disks-to-managed-disks"></a>Puis-je utiliser des sauvegardes de machine virtuelle avec disque non géré pour effectuer une restauration après la mise à niveau de mes disques vers des disques gérés ?
Oui, vous pouvez utiliser les sauvegardes effectuées avant la migration des disques non gérés vers des disques gérés. Par défaut, le travail de restauration de la machine virtuelle créera une machine virtuelle avec des disques non gérés. Vous pouvez utiliser la fonctionnalité restauration de disques pour restaurer des disques et les utiliser pour créer une machine virtuelle sur des disques gérés.

### <a name="what-is-the-procedure-to-restore-a-vm-to-a-restore-point-taken-before-the-conversion-from-unmanaged-to-managed-disks-was-done-for-a-vm"></a>Quelle est la procédure dédiée à la restauration d’une machine virtuelle à un point de restauration antérieur à la conversion de disques non gérés en disques gérés pour une machine virtuelle ?
Dans ce scénario, par défaut, la tâche de restauration de machine virtuelle crée une machine virtuelle avec des disques non gérés. Pour créer une machine virtuelle avec des disques gérés :
1. [Restaurer vers des disques non gérés](tutorial-restore-disk.md#restore-a-vm-disk)
2. [Convertir les disques restaurés en disques gérés](tutorial-restore-disk.md#convert-the-restored-disk-to-a-managed-disk)
3. [Créer une machine virtuelle avec des disques gérés](tutorial-restore-disk.md#create-a-vm-from-the-restored-disk) <br>
Pour les cmdlets PowerShell, consultez [cet article](backup-azure-vms-automation.md#restore-an-azure-vm).

### <a name="can-i-restore-the-vm-if-my-vm-is-deleted"></a>Puis-je restaurer une machine virtuelle après sa suppression ?
Oui. Le cycle de vie d’une machine virtuelle et celui de son élément de sauvegarde sont différents. Par conséquent, même si vous supprimez la machine virtuelle, vous pouvez accéder à son élément de sauvegarde dans le coffre Recovery Services, et déclencher une restauration à l’aide d’un des points de récupération.

## <a name="manage-vm-backups"></a>Gérer les sauvegardes de machine virtuelle
### <a name="what-happens-when-i-change-a-backup-policy-on-vms"></a>Que se passe-t-il lorsque je modifie une stratégie de sauvegarde sur des machines virtuelles ?
Lorsqu’une nouvelle stratégie est appliquée sur les machines virtuelles, le planning et la rétention de la nouvelle stratégie sont suivis. Si la rétention est étendue, les points de récupération existants sont marqués comme à conserver afin qu’ils soient conformes à la nouvelle stratégie. Si la rétention est réduite, ils sont marqués comme à nettoyer lors de la prochaine tâche de nettoyage et sont ensuite supprimés.

### <a name="how-can-i-move-a-vm-enrolled-in-azure-backup-between-resource-groups"></a>Comment puis-je déplacer une machine virtuelle inscrite dans la sauvegarde Azure entre les groupes de ressources ?
Suivez les étapes ci-dessous pour déplacer la machine virtuelle sauvegardée vers le groupe de ressources cible
1. Interrompre temporairement la sauvegarde et conserver les données de sauvegarde
2. Déplacer la machine virtuelle vers le groupe de ressources cible
3. Protéger de nouveau avec le même/un nouveau coffre

Les utilisateurs peuvent effectuer des restaurations à partir des points de restauration créés avant le déplacement.
