---
title: FAQ sur la sauvegarde des fichiers Azure
description: Cet article fournit des réponses à des questions courantes sur la protection de vos partages de fichiers Azure avec le service Sauvegarde Microsoft Azure.
ms.date: 07/29/2019
ms.topic: conceptual
ms.openlocfilehash: 053fe38b07fff5d289c72024ef185fbf5d647f70
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/21/2020
ms.locfileid: "76293995"
---
# <a name="questions-about-backing-up-azure-files"></a>Questions sur la sauvegarde des fichiers Azure

Cet article répond aux questions courantes sur la sauvegarde des fichiers Azure. Certaines réponses comportent des liens vers les articles présentant des informations complètes. Vous pouvez également publier des questions sur le service Azure Backup dans le [forum de discussion](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup).

Pour analyser rapidement les sections de cet article, utilisez les liens sur la droite, en dessous de **Dans cet article**.

## <a name="configuring-the-backup-job-for-azure-files"></a>Configuration de la tâche de sauvegarde pour les fichiers Azure

### <a name="why-cant-i-see-some-of-my-storage-accounts-i-want-to-protect-that-contain-valid-azure-file-shares"></a>Pourquoi ne puis-je pas voir certains des comptes de stockage que je souhaite protéger et contenant des partages de fichiers Azure valides ?

Dans la préversion, la sauvegarde pour les partages de fichiers Azure ne prend pas en charge tous les types de comptes de stockage. Reportez-vous à la liste [ici](troubleshoot-azure-files.md#limitations-for-azure-file-share-backup-during-preview) pour afficher la liste des comptes de stockage pris en charge. Il est également possible que le compte de stockage que vous recherchez soit déjà protégé ou inscrit auprès d’un autre coffre. [Annuler l’inscription](troubleshoot-azure-files.md#configuring-backup) auprès du coffre pour découvrir le compte de stockage dans d’autres coffres pour la protection.

### <a name="why-cant-i-see-some-of-my-azure-file-shares-in-the-storage-account-when-im-trying-to-configure-backup"></a>Pourquoi ne puis-je pas voir certains de mes partages de fichiers Azure dans le compte de stockage lorsque j’essaie de configurer la sauvegarde ?

Vérifiez si le partage de fichiers Azure est déjà protégé dans le même coffre Recovery Services ou s’il a été supprimé récemment.

### <a name="can-i-protect-file-shares-connected-to-a-sync-group-in-azure-files-sync"></a>Puis-je protéger les partages de fichiers connectés à un groupe de synchronisation dans Azure Files Sync ?

Oui. La protection des partages de fichiers Azure connectés à des groupes de synchronisation est activée et disponible en préversion publique.

### <a name="when-trying-to-back-up-file-shares-i-clicked-on-a-storage-account-for-discovering-the-file-shares-in-it-however-i-did-not-protect-them-how-do-i-protect-these-file-shares-with-any-other-vault"></a>Quand j’ai essayé de sauvegarder des partages de fichiers, j’ai cliqué sur un compte de stockage pour détecter les partages de fichiers qu’il contient. Toutefois, je ne les ai pas protégés. Comment protéger ces partages de fichiers avec un autre coffre ?

Lorsque vous essayez de sauvegarder, le fait de sélectionner un compte de stockage pour détecter les partages de fichiers dans celui-ci permet d’enregistrer le compte de stockage avec le coffre à partir duquel cette action est effectuée. Si vous choisissez de protéger les partages de fichiers avec un autre coffre, [annulez l’enregistrement](troubleshoot-azure-files.md#configuring-backup) du compte de stockage sélectionné à partir de ce coffre.

### <a name="can-i-change-the-vault-to-which-i-back-up-my-file-shares"></a>Puis-je modifier le coffre dans lequel je sauvegarde mes partages de fichiers ?

Oui. Toutefois, vous devrez [arrêter la protection sur un partage de fichiers](manage-afs-backup.md#stop-protection-on-a-file-share) à partir du coffre connecté, [désinscrire](troubleshoot-azure-files.md#configuring-backup) ce compte de stockage, puis le protéger à partir d’un autre coffre.

### <a name="in-which-geos-can-i-back-up-azure-file-shares"></a>Dans quelles zones géographiques puis-je sauvegarder des partages de fichiers Azure ?

La sauvegarde pour les partages de fichiers Azure est actuellement en préversion et disponible uniquement dans les zones géographiques suivantes :

- Australie Est (AE)
- Sud-Est de l’Australie (ASE)
- Brésil Sud (BRS)
- Centre du Canada (CNC)
- Est du Canada (CE)
- USA Centre (CUS)
- Asie Est (EA)
- USA Est (EUS)
- USA Est 2 (EUS2)
- Japon Est (JPE)
- Japon Ouest (JPW)
- Inde Centre (INC)
- Sud de l’Inde (INS)
- Corée Centre (KRC)
- Corée du Sud (KRS)
- USA Centre Nord (NCUS)
- Europe Nord (NE)
- USA Centre Sud (SCUS)
- Asie Sud-Est (SEA)
- Sud du Royaume-Uni (UKS)
- Ouest du Royaume-Uni (UKW)
- Europe Ouest (WE)
- USA Ouest (WUS)
- USA Centre-Ouest (WCUS)
- USA Ouest 2 (WUS 2)
- US Gov Arizona (UGA)
- US Gov Texas (UGT)
- US Gov Virginie (UGV)
- Australie Centre (ACL)
- Inde Ouest (INW)
- Afrique du Sud Nord (SAN)
- Émirats arabes unis Nord (UAN)
- France Centre (FRC)
- Allemagne Nord (GN)                       
- Allemagne Centre-Ouest (GWC)
- Afrique du Sud Ouest (SAW)
- Émirats arabes unis Centre (UAC)
- NWE (Norvège Est)     
- NWW (Norvège Ouest)
- SZN (Suisse Nord)

Écrivez à [AskAzureBackupTeam@microsoft.com](email:askazurebackupteam@microsoft.com) si vous souhaitez l’utiliser dans une région qui n’est pas spécifiée ci-dessus.

### <a name="how-many-azure-file-shares-can-i-protect-in-a-vault"></a>Combien de partages de fichiers Azure puis-je protéger dans un coffre ?

Dans la préversion, vous pouvez protéger les partages de fichiers Azure d’un maximum de 50 comptes de stockage par coffre. Vous pouvez également protéger jusqu’à 200 partages de fichiers Azure dans un seul coffre.

### <a name="can-i-protect-two-different-file-shares-from-the-same-storage-account-to-different-vaults"></a>Puis-je protéger deux partages de fichiers différents du même compte de stockage dans différents coffres ?

Non. Tous les partages de fichiers dans un compte de stockage peuvent uniquement être protégés par le même coffre.

## <a name="backup"></a>Backup

### <a name="how-many-scheduled-backups-can-i-configure-per-file-share"></a>Combien de sauvegardes planifiées puis-je configurer pour chaque partage de fichiers ?

La sauvegarde Azure prend actuellement en charge la configuration de sauvegardes planifiées une fois par jour des partages de fichiers Azure.

### <a name="how-many-on-demand-backups-can-i-take-per-file-share"></a>Combien de sauvegardes à la demande puis-je prendre pour chaque partage de fichiers ?

À tout moment, vous pouvez disposer de 200 instantanés pour un partage de fichiers. La limite inclut des instantanés pris par Azure Backup, comme défini dans votre stratégie. Si vos sauvegardes échouent une fois la limite atteinte, supprimez des points de restauration à la demande pour réussir les prochaines sauvegardes.

## <a name="restore"></a>Restaurer

### <a name="can-i-recover-from-a-deleted-azure-file-share"></a>Puis-je effectuer une restauration d’un partage de fichiers Azure supprimé ?

Lorsqu’un partage de fichiers Azure est supprimé, la liste des sauvegardes qui seront supprimées est affichée et une confirmation est demandée. Un partage de fichiers Azure supprimé ne peut pas être restauré.

### <a name="can-i-restore-from-backups-if-i-stopped-protection-on-an-azure-file-share"></a>Puis-je effectuer une restauration à partir de sauvegardes après avoir arrêté la protection sur un partage de fichiers Azure ?

Oui. Si vous avez choisi l’option **Conserver les données de sauvegarde** lors de l’arrêt de la protection, vous pouvez restaurer à partir de tous les points de restauration existants.

### <a name="what-happens-if-i-cancel-an-ongoing-restore-job"></a>Que se passe-t-il si j’annule un travail de restauration en cours ?

Si un travail de restauration en cours est annulé, le processus de restauration s’arrête et tous les fichiers restaurés avant l’annulation restent dans la destination configurée (emplacement d’origine ou autre) sans aucun restauration.

## <a name="manage-backup"></a>Gérer la sauvegarde

### <a name="can-i-use-powershell-to-configuremanagerestore-backups-of-azure-file-shares"></a>Puis-je utiliser PowerShell pour configurer/gérer/restaurer des sauvegardes de partages de fichiers Azure ?

Oui. Reportez-vous à la documentation détaillée [ici](backup-azure-afs-automation.md)

### <a name="can-i-access-the-snapshots-taken-by-azure-backups-and-mount-it"></a>Puis-je accéder aux instantanés effectués par les sauvegardes Azure et les monter ?

Tous les instantanés pris par Azure Backup sont accessibles par l’option Voir les instantanés dans le portail, PowerShell ou CLI. Pour en savoir plus sur les instantanés de partage de fichiers Azure, consultez [Vue d’ensemble des instantanés de partage pour Azure Files (préversion)](../storage/files/storage-snapshots-files.md).

### <a name="what-is-the-maximum-retention-i-can-configure-for-backups"></a>Quelle est la durée de conservation maximale configurable pour les sauvegardes ?

La sauvegarde de partages de fichiers Azure offre la possibilité de configurer des stratégies de rétention allant jusqu’à 180 jours. Toutefois, à l’aide de la [option « sauvegarde à la demande » dans PowerShell](backup-azure-afs-automation.md#trigger-an-on-demand-backup), vous pouvez même conserver un point de récupération pendant 10 ans.

### <a name="what-happens-when-i-change-the-backup-policy-for-an-azure-file-share"></a>Que se passe-t-il lorsque je modifie la stratégie de sauvegarde pour un partage de fichiers Azure ?

Lorsqu’une nouvelle stratégie est appliquée sur les partages de fichiers, le planning et la rétention de la nouvelle stratégie sont suivis. Si la rétention est étendue, les points de récupération existants sont marqués comme à conserver afin qu’ils soient conformes à la nouvelle stratégie. Si la rétention est réduite, ils sont marqués comme à nettoyer lors de la prochaine tâche de nettoyage et supprimés.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les autres zones d’Azure Backup, consultez certaines de ces autres FAQ relatives à la sauvegarde :

- [FAQ du coffre Recovery Services](backup-azure-backup-faq.md)
- [FAQ sur la sauvegarde de la machine virtuelle Azure](backup-azure-vm-backup-faq.md)
- [FAQ sur l’Agent Azure Backup](backup-azure-file-folder-backup-faq.md)
