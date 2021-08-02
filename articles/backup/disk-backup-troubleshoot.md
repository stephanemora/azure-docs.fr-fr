---
title: Résolution des échecs de Sauvegarde sur disque Azure
description: Découvrez comment résoudre des échecs de Sauvegarde sur disque Azure
ms.topic: conceptual
ms.date: 06/08/2021
ms.openlocfilehash: a749c87949d53781dc810148d01cc5d179d70f77
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111754112"
---
# <a name="troubleshooting-backup-failures-in-azure-disk-backup"></a>Résolution des échecs de Sauvegarde sur disque Azure

Cet article fournit des informations sur la résolution des problèmes de sauvegarde et de restauration rencontrés avec des disques Azure. Pour plus d’informations sur la disponibilité régionale, les scénarios pris en charge et les limitations de la [Sauvegarde sur disque Azure](disk-backup-overview.md), consultez la [matrice de prise en charge](disk-backup-support-matrix.md).

## <a name="common-issues-faced-with-azure-disk-backup"></a>Problèmes courants liés à la Sauvegarde sur disque Azure

### <a name="error-code-usererrorsnapshotrgsubscriptionmismatch"></a>Code d’erreur : UserErrorSnapshotRGSubscriptionMismatch

Message d’erreur : Abonnement non valide sélectionné pour le magasin de données d’instantanés

Action recommandée : Les disques et captures instantanées de disque sont stockés dans le même abonnement. Vous pouvez choisir n’importe quel groupe de ressources pour stocker les instantanés de disque au sein de l’abonnement. Sélectionnez le même abonnement que celui du disque source. Pour plus d’informations, consultez la [matrice de prise en charge](disk-backup-support-matrix.md).

### <a name="error-code-usererrorsnapshotrgnotfound"></a>Code d’erreur : UserErrorSnapshotRGNotFound

Message d’erreur : Impossible d’effectuer l’opération parce que le groupe de ressources du magasin de données d’instantanés n’existe pas.

Action recommandée : Créez le groupe de ressources et fournissez les autorisations nécessaires sur celui-ci. Pour plus d’informations, consultez [Configurer la sauvegarde](backup-managed-disks.md#configure-backup).

### <a name="error-code-usererrormanageddisknotfound"></a>Code d’erreur : UserErrorManagedDiskNotFound

Message d’erreur : Impossible d’effectuer l’opération, car le disque managé n’existe plus.

Action recommandée : Les sauvegardes continuent d’échouer, car le disque source peut être supprimé ou déplacé vers un autre emplacement. Utilisez le point de restauration existant pour restaurer le disque s’il est supprimé par erreur. Si le disque est déplacé vers un autre emplacement, configurez la sauvegarde du disque.

### <a name="error-code-usererrornotenoughpermissionondisk"></a>Code d’erreur : UserErrorNotEnoughPermissionOnDisk

Message d’erreur : Le service Sauvegarde Azure nécessite des autorisations supplémentaires sur le disque pour effectuer cette opération.

Action recommandée : Accordez à l’identité managée du coffre Sauvegarde les autorisations appropriées sur le disque. Consultez [la documentation](backup-managed-disks.md) pour comprendre les autorisations que requiert l’identité managée du coffre Sauvegarde et la manière de les fournir.

### <a name="error-code-usererrornotenoughpermissiononsnapshotrg"></a>Code d’erreur : UserErrorNotEnoughPermissionOnSnapshotRG

Message d’erreur : Le service Sauvegarde Azure nécessite des autorisations supplémentaires sur le groupe de ressources du magasin de données d’instantanés pour effectuer cette opération.

Action recommandée : Accordez à l’identité managée du coffre Sauvegarde les autorisations appropriées sur le groupe de ressources du magasin de données d’instantanés. Le groupe de ressources du magasin de données d’instantanés est l’emplacement de stockage des captures instantanées de disque. Consultez [la documentation](backup-managed-disks.md) pour savoir quel est le groupe de ressources, quelles autorisations requiert l’identité managée du coffre Sauvegarde et comment la fournir.

### <a name="error-code-usererrordiskbackupdiskormsipermissionsnotpresent"></a>Code d’erreur : UserErrorDiskBackupDiskOrMSIPermissionsNotPresent

Message d’erreur : Un disque non valide ou le service Sauvegarde Azure nécessitent des autorisations supplémentaires sur le disque pour effectuer cette opération.

Action recommandée : Les sauvegardes continuent d’échouer, car le disque source peut être supprimé ou déplacé vers un autre emplacement. Utilisez le point de restauration existant pour restaurer le disque s’il est supprimé par erreur. Si le disque est déplacé vers un autre emplacement, configurez la sauvegarde du disque. Si le disque n’est pas supprimé ou déplacé, accordez à l’identité managée du coffre Sauvegarde les autorisations appropriées sur le disque. Consultez [la documentation](backup-managed-disks.md) pour comprendre les autorisations que requiert l’identité managée du coffre Sauvegarde et la manière de les fournir.

### <a name="error-code-usererrordiskbackupsnapshotrgormsipermissionsnotpresent"></a>Code d’erreur : UserErrorDiskBackupSnapshotRGOrMSIPermissionsNotPresent

Message d’erreur : Impossible d’effectuer l’opération parce que le groupe de ressources du magasin de données d’instantanés n’existe plus. Ou le service Sauvegarde Azure nécessite des autorisations supplémentaires sur le groupe de ressources du magasin de données d’instantanés pour effectuer cette opération.

Action recommandée : Créez un groupe de ressources et accordez à l’identité managée du coffre Sauvegarde les autorisations appropriées sur le groupe de ressources du magasin de données d’instantanés. Le groupe de ressources du magasin de données d’instantanés est l’emplacement de stockage des captures instantanées de disque. Consultez [la documentation](backup-managed-disks.md) pour savoir quel est le groupe de ressources, quelles autorisations requiert l’identité managée du coffre Sauvegarde et comment la fournir.

### <a name="error-code-usererrordiskbackupauthorizationfailed"></a>Code d’erreur : UserErrorDiskBackupAuthorizationFailed

Message d’erreur : L’identité managée du coffre Sauvegarde ne dispose pas des autorisations nécessaires pour effectuer cette opération.

Action recommandée : Accordez à l’identité managée du coffre Sauvegarde les autorisations appropriées sur le disque à sauvegarder et sur le groupe de ressources du magasin de données d’instantanés dans lequel les captures instantanées sont stockées. Consultez [la documentation](backup-managed-disks.md) pour comprendre les autorisations que requiert l’identité managée du coffre Sauvegarde et la manière de les fournir.

### <a name="error-code-usererrorsnapshotrgormsipermissionsnotpresent"></a>Code d’erreur : UserErrorSnapshotRGOrMSIPermissionsNotPresent

Message d’erreur : Impossible d’effectuer l’opération parce que le groupe de ressources du magasin de données d’instantanés n’existe plus. Ou le service Sauvegarde Azure nécessite des autorisations supplémentaires sur le groupe de ressources du magasin de données d’instantanés pour effectuer cette opération.

Action recommandée : Créez le groupe de ressources et accordez à l’identité managée du coffre Sauvegarde les autorisations appropriées sur le groupe de ressources du magasin de données d’instantanés. Le groupe de ressources du magasin de données d’instantanés est l’emplacement de stockage des captures instantanées. Consultez [la documentation](backup-managed-disks.md) pour savoir quel est le groupe de ressources, quelles autorisations requiert l’identité managée du coffre Sauvegarde et comment la fournir.

### <a name="error-code-usererroroperationalstoreparametersnotprovided"></a>Code d’erreur : UserErrorOperationalStoreParametersNotProvided

Message d’erreur : Impossible d’effectuer l’opération parce que le paramètre du groupe de ressources du magasin de données d’instantanés n’est pas fourni.

Action recommandée : Fournissez le paramètre du groupe de ressources du magasin de données d’instantanés. Le groupe de ressources du magasin de données d’instantanés est l’emplacement de stockage des captures instantanées de disque. Pour plus d’informations, consultez [la documentation](backup-managed-disks.md).

### <a name="error-code-usererrorinvalidoperationalstoreresourcegroup"></a>Code d’erreur : UserErrorInvalidOperationalStoreResourceGroup

Message d’erreur : Le groupe de ressources du magasin de données d’instantanés fourni n’est pas valide.

Action recommandée : Fournissez un groupe de ressources valide pour le magasin de données d’instantanés. Le groupe de ressources du magasin de données d’instantanés est l’emplacement de stockage des captures instantanées de disque. Pour plus d’informations, consultez [la documentation](backup-managed-disks.md).

### <a name="error-code-usererrordiskbackupdisktypenotsupported"></a>Code d’erreur : UserErrorDiskBackupDiskTypeNotSupported

Message d’erreur : Type de disque non pris en charge.

Action recommandée : Consultez [la matrice de prise en charge](disk-backup-support-matrix.md) sur les limitations et les scénarios non pris en charge.

### <a name="error-code-usererrorsamenamediskalreadyexists"></a>Code d’erreur : UserErrorSameNameDiskAlreadyExists

Message d’erreur : Restauration impossible parce qu’un disque portant le même nom existe déjà dans le groupe de ressources cible sélectionné.

Action recommandée : Fournissez un autre nom de disque pour la restauration. Pour plus d’informations, consultez [Restaurer des disques managés Azure](restore-managed-disks.md).

### <a name="error-code-usererrorrestoretargetrgnotfound"></a>Code d’erreur : UserErrorRestoreTargetRGNotFound

Message d’erreur : L’opération a échoué parce que le groupe de ressources cible n’existe pas.

Action recommandée : Fournissez un groupe de ressources valide à restaurer. Pour plus d’informations, consultez [Restaurer des disques managés Azure](restore-managed-disks.md).

### <a name="error-code-usererrornotenoughpermissionontargetrg"></a>Code d’erreur : UserErrorNotEnoughPermissionOnTargetRG

Message d’erreur : Le service Sauvegarde Azure requiert des autorisations supplémentaires sur le groupe de ressources cible pour effectuer cette opération.

Action recommandée : Accordez à l’identité managée du coffre Sauvegarde les autorisations appropriées sur le groupe de ressources cible. Le groupe de ressources cible est l’emplacement sélectionné où le disque doit être restauré. Consultez [la documentation relative à la restauration](restore-managed-disks.md) pour comprendre les autorisations que requiert l’identité managée du coffre Sauvegarde et la manière de les fournir.

### <a name="error-code-usererrorsubscriptiondiskquotalimitreached"></a>Code d’erreur : UserErrorSubscriptionDiskQuotaLimitReached

Message d’erreur : L’opération a échoué parce que la limite maximale du quota de disque a été atteinte sur l’abonnement.

Action recommandée : Consultez la [documentation sur les limites et le quota du service et de l’abonnement Azure](../azure-resource-manager/management/azure-subscription-service-limits.md), ou contactez le Support Microsoft.

### <a name="error-code-usererrordiskbackuprestorergormsipermissionsnotpresent"></a>Code d’erreur : UserErrorDiskBackupRestoreRGOrMSIPermissionsNotPresent

Message d’erreur : L’opération a échoué parce que le groupe de ressources cible n’existe pas. Ou le service Sauvegarde Azure requiert des autorisations supplémentaires sur le groupe de ressources cible pour effectuer cette opération.

Action recommandée : Fournissez un groupe de ressources valide à restaurer, et accordez à l’identité managée du coffre Sauvegarde les autorisations appropriées sur le groupe de ressources cible. Le groupe de ressources cible est l’emplacement sélectionné où le disque doit être restauré. Consultez [la documentation relative à la restauration](restore-managed-disks.md) pour comprendre les autorisations que requiert l’identité managée du coffre Sauvegarde et la manière de les fournir.

### <a name="error-code-usererrordeskeyvaultkeydisabled"></a>Code d’erreur : UserErrorDESKeyVaultKeyDisabled

Message d’erreur : La clé de coffre de clés utilisée pour le jeu de chiffrement de disque n’est pas dans un état activé.

Action recommandée : Activez la clé de coffre de clés utilisée pour le jeu de chiffrement de disque. Consultez les limitations dans la [matrice de prise en charge](disk-backup-support-matrix.md).

### <a name="error-code-usererrormsipermissionsnotpresentondes"></a>Code d’erreur : UserErrorMSIPermissionsNotPresentOnDES

Message d’erreur : Le service Sauvegarde Azure a besoin d’une autorisation pour accéder au jeu de chiffrement de disque utilisé avec le disque.

Action recommandée : Fournissez un accès lecteur à l’identité managée du coffre Sauvegarde au jeu de chiffrement de disque (DES).

### <a name="error-code-usererrordeskeyvaultkeynotavailable"></a>Code d’erreur : UserErrorDESKeyVaultKeyNotAvailable

Message d’erreur : La clé de coffre de clés utilisée pour le jeu de chiffrement de disque n’est pas disponible.

Action recommandée : Assurez-vous que la clé de coffre de clés utilisée pour le jeu de chiffrement de disque n’est pas désactivée ou supprimée.

### <a name="error-code-usererrordisksnapshotnotfound"></a>Code d’erreur : UserErrorDiskSnapshotNotFound

Message d’erreur : La capture instantanée de disque pour ce point de restauration a été supprimée.

Action recommandée : Les instantanés sont stockés dans le groupe de ressources du magasin de données d’instantanés au sein de votre abonnement. Il est possible que la capture instantanée liée au point de restauration sélectionné ait été supprimée ou déplacée de ce groupe de ressources. Songez à utiliser un autre point de récupération à restaurer. Suivez également les instructions recommandées pour choisir le groupe de ressources d’instantanés mentionné dans la [documentation relative à la restauration](restore-managed-disks.md).

### <a name="error-code-usererrorsnapshotmetadatanotfound"></a>Code d’erreur : UserErrorSnapshotMetadataNotFound

Message d’erreur : Les métadonnées de capture instantanée de disque pour ce point de restauration ont été supprimées

Action recommandée : Songez à utiliser un autre point de restauration à restaurer. Pour plus d’informations, consultez la [documentation relative à la restauration](restore-managed-disks.md).

### <a name="error-code-backupagentpluginhostvalidateprotectionerror"></a>Code d’erreur : BackupAgentPluginHostValidateProtectionError

Message d’erreur : La sauvegarde sur disque n’est pas encore disponible dans la région du coffre de sauvegarde sous lequel la configuration de la protection est essayée.

Action recommandée : Le coffre de sauvegarde doit être dans une région prise en charge. Pour connaître la disponibilité par région, consultez la [matrice de prise en charge](disk-backup-support-matrix.md).

### <a name="error-code-usererrordppdatasourcealreadyhasbackupinstance"></a>Code d’erreur : UserErrorDppDatasourceAlreadyHasBackupInstance

Message d’erreur : Le disque pour lequel vous essayez de configurer la sauvegarde est déjà protégé. Le disque est déjà associé à une instance de sauvegarde dans un coffre de sauvegarde.

Action recommandée : Ce disque est déjà associé à une instance de sauvegarde dans un coffre de sauvegarde. Si vous souhaitez reprotéger ce disque, supprimez l’instance de sauvegarde du coffre de sauvegarde où le disque est actuellement protégé et reprotégez-le dans un autre coffre.

### <a name="error-code-usererrordppdatasourcealreadyprotected"></a>Code d’erreur : UserErrorDppDatasourceAlreadyProtected

Message d’erreur : Le disque pour lequel vous essayez de configurer la sauvegarde est déjà protégé. Le disque est déjà associé à une instance de sauvegarde dans un coffre de sauvegarde.

Action recommandée : Ce disque est déjà associé à une instance de sauvegarde dans un coffre de sauvegarde. Si vous souhaitez reprotéger ce disque, supprimez l’instance de sauvegarde du coffre de sauvegarde où le disque est actuellement protégé et reprotégez-le dans un autre coffre.

### <a name="error-code-usererrormaxconcurrentoperationlimitreached"></a>Code d’erreur : UserErrorMaxConcurrentOperationLimitReached

Message d’erreur : Impossible de démarrer l’opération, car le nombre maximal de sauvegardes simultanées autorisées a été atteint.

Action recommandée : Attendez la fin de l’exécution de la sauvegarde précédente.

### <a name="error-code-usererrormissingsubscriptionregistration"></a>Code d’erreur : UserErrorMissingSubscriptionRegistration

Message d’erreur : L’abonnement n’est pas inscrit pour utiliser l’espace de noms « Microsoft.Compute ».

Action recommandée : Le fournisseur de ressources requis n’a pas été inscrit pour votre abonnement. Inscrivez l’espace de noms des fournisseurs de ressources (_Microsoft.Compute_ et _Microsoft.Storage_) en suivant les étapes de la [Solution 3](../azure-resource-manager/templates/error-register-resource-provider.md#solution-3---azure-portal).

## <a name="next-steps"></a>Étapes suivantes

[Matrice de prise en charge de sauvegarde de disques Azure](disk-backup-support-matrix.md)