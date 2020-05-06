---
title: FAQ sur la sauvegarde des fichiers Azure
description: Cet article fournit des réponses à des questions courantes sur la protection de vos partages de fichiers Azure avec le service Sauvegarde Microsoft Azure.
ms.date: 04/22/2020
ms.topic: conceptual
ms.openlocfilehash: d7b19fd11e6784a188a18f6a613eef5ff4f77764
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82105639"
---
# <a name="questions-about-backing-up-azure-files"></a>Questions sur la sauvegarde des fichiers Azure

Cet article répond aux questions courantes sur la sauvegarde des fichiers Azure. Certaines réponses comportent des liens vers les articles présentant des informations complètes. Vous pouvez également publier des questions sur le service Azure Backup dans le [forum de discussion](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup).

Pour analyser rapidement les sections de cet article, utilisez les liens sur la droite, en dessous de **Dans cet article**.

## <a name="configuring-the-backup-job-for-azure-files"></a>Configuration de la tâche de sauvegarde pour les fichiers Azure

### <a name="why-cant-i-see-some-of-my-storage-accounts-that-i-want-to-protect-which-contain-valid-azure-file-shares"></a>Pourquoi ne puis-je pas voir certains des comptes de stockage que je souhaite protéger et contenant des partages de fichiers Azure valides ?

Consultez la [matrice de prise en charge de la sauvegarde des partages de fichiers Azure](azure-file-share-support-matrix.md) pour vérifier que le compte de stockage appartient à l'un des types de compte de stockage pris en charge. Il est également possible que le compte de stockage que vous recherchez soit déjà protégé ou inscrit auprès d’un autre coffre. [Annulez l’inscription du compte de stockage](manage-afs-backup.md#unregister-a-storage-account) auprès du coffre pour découvrir le compte de stockage dans d’autres coffres pour la protection.

### <a name="why-cant-i-see-some-of-my-azure-file-shares-in-the-storage-account-when-im-trying-to-configure-backup"></a>Pourquoi ne puis-je pas voir certains de mes partages de fichiers Azure dans le compte de stockage lorsque j’essaie de configurer la sauvegarde ?

Vérifiez si le partage de fichiers Azure est déjà protégé dans le même coffre Recovery Services ou s’il a été supprimé récemment.

### <a name="can-i-protect-file-shares-connected-to-a-sync-group-in-azure-files-sync"></a>Puis-je protéger les partages de fichiers connectés à un groupe de synchronisation dans Azure Files Sync ?

Oui. La protection des partages de fichiers Azure connectés à des groupes de synchronisation est activée.

### <a name="when-trying-to-back-up-file-shares-i-clicked-on-a-storage-account-for-discovering-the-file-shares-in-it-however-i-didnt-protect-them-how-do-i-protect-these-file-shares-with-any-other-vault"></a>Quand j’ai essayé de sauvegarder des partages de fichiers, j’ai cliqué sur un compte de stockage pour détecter les partages de fichiers qu’il contient. Toutefois, je ne les ai pas protégés. Comment protéger ces partages de fichiers avec un autre coffre ?

Lorsque vous essayez de sauvegarder, le fait de sélectionner un compte de stockage pour détecter les partages de fichiers dans celui-ci permet d’enregistrer le compte de stockage avec le coffre à partir duquel cette action est effectuée. Si vous choisissez de protéger les partages de fichiers avec un autre coffre, [annulez l’enregistrement](manage-afs-backup.md#unregister-a-storage-account) du compte de stockage sélectionné à partir de ce coffre.

### <a name="can-i-change-the-vault-to-which-i-back-up-my-file-shares"></a>Puis-je modifier le coffre dans lequel je sauvegarde mes partages de fichiers ?

Oui. Toutefois, vous devrez [arrêter la protection sur un partage de fichiers](manage-afs-backup.md#stop-protection-on-a-file-share) à partir du coffre connecté, [désinscrire](manage-afs-backup.md#unregister-a-storage-account) ce compte de stockage, puis le protéger à partir d’un autre coffre.

### <a name="how-many-azure-file-shares-can-i-protect-in-a-vault"></a>Combien de partages de fichiers Azure puis-je protéger dans un coffre ?

Vous pouvez protéger les partages de fichiers Azure d’un maximum de 50 comptes de stockage par coffre. Vous pouvez également protéger jusqu’à 200 partages de fichiers Azure dans un seul coffre.

### <a name="can-i-protect-two-different-file-shares-from-the-same-storage-account-to-different-vaults"></a>Puis-je protéger deux partages de fichiers différents du même compte de stockage dans différents coffres ?

Non. Tous les partages de fichiers dans un compte de stockage peuvent uniquement être protégés par le même coffre.

## <a name="backup"></a>Backup

### <a name="what-should-i-do-if-my-backups-start-failing-due-to-the-maximum-limit-reached-error"></a>Que dois-je faire si mes sauvegardes échouent en raison d’une erreur de limite maximale atteinte ?

À tout moment, vous pouvez disposer de 200 instantanés pour un partage de fichiers. La limite inclut des instantanés pris par Azure Backup, comme défini dans votre stratégie. Si vos sauvegardes échouent une fois la limite atteinte, supprimez des instantanés à la demande pour réussir les prochaines sauvegardes.

## <a name="restore"></a>Restaurer

### <a name="can-i-recover-from-a-deleted-azure-file-share"></a>Puis-je effectuer une restauration d’un partage de fichiers Azure supprimé ?

Lorsqu’un partage de fichiers Azure est supprimé, la liste des sauvegardes qui seront supprimées est affichée et une confirmation est demandée. Actuellement, un partage de fichiers Azure supprimé ne peut pas être restauré.

### <a name="can-i-restore-from-backups-if-i-stopped-protection-on-an-azure-file-share"></a>Puis-je effectuer une restauration à partir de sauvegardes après avoir arrêté la protection sur un partage de fichiers Azure ?

Oui. Si vous avez choisi l’option **Conserver les données de sauvegarde** lors de l’arrêt de la protection, vous pouvez restaurer à partir de tous les points de restauration existants.

### <a name="what-happens-if-i-cancel-an-ongoing-restore-job"></a>Que se passe-t-il si j’annule un travail de restauration en cours ?

Si un travail de restauration en cours est annulé, le processus de restauration s’arrête et tous les fichiers restaurés avant l’annulation restent dans la destination configurée (emplacement d’origine ou autre) sans aucun restauration.

## <a name="manage-backup"></a>Gérer la sauvegarde

### <a name="can-i-use-powershell-to-configuremanagerestore-backups-of-azure-file-shares"></a>Puis-je utiliser PowerShell pour configurer/gérer/restaurer des sauvegardes de partages de fichiers Azure ?

Oui. Reportez-vous à la documentation détaillée [ici](backup-azure-afs-automation.md).

### <a name="can-i-access-the-snapshots-taken-by-azure-backups-and-mount-them"></a>Puis-je accéder aux instantanés effectués par les sauvegardes Azure et les monter ?

Tous les instantanés pris par Azure Backup sont accessibles par l’option Voir les instantanés dans le portail, PowerShell ou CLI. Pour en savoir plus sur les instantanés de partage de fichiers Azure, consultez [Vue d’ensemble des instantanés de partage pour Azure Files](../storage/files/storage-snapshots-files.md).

### <a name="what-is-the-maximum-retention-i-can-configure-for-backups"></a>Quelle est la durée de conservation maximale configurable pour les sauvegardes ?

Consultez la [matrice de prise en charge](azure-file-share-support-matrix.md) pour plus d’informations sur la rétention maximale. Sauvegarde Azure calcule en temps réel le nombre d’instantanée quand vous entrez les valeurs de rétention lors de la configuration de la stratégie de sauvegarde. Dès que le nombre d’instantanés correspondant à vos valeurs de rétention définies dépasse 200, le portail affiche un avertissement vous demandant d’ajuster vos valeurs de rétention. Cela vous évite de dépasser la limite du nombre maximal d’instantanés pris en charge à tout moment par Azure Files pour un partage de fichiers.

### <a name="what-happens-when-i-change-the-backup-policy-for-an-azure-file-share"></a>Que se passe-t-il lorsque je modifie la stratégie de sauvegarde pour un partage de fichiers Azure ?

Lorsqu’une nouvelle stratégie est appliquée sur les partages de fichiers, le planning et la rétention de la nouvelle stratégie sont suivis. Si la rétention est étendue, les points de récupération existants sont marqués comme à conserver afin qu’ils soient conformes à la nouvelle stratégie. Si la rétention est réduite, ils sont marqués comme à nettoyer lors de la prochaine tâche de nettoyage et supprimés.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les autres zones d’Azure Backup, consultez certaines de ces autres FAQ relatives à la sauvegarde :

- [FAQ du coffre Recovery Services](backup-azure-backup-faq.md)
- [FAQ sur la sauvegarde de la machine virtuelle Azure](backup-azure-vm-backup-faq.md)
- [FAQ sur l’Agent Azure Backup](backup-azure-file-folder-backup-faq.md)
