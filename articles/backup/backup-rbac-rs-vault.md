---
title: Gérer des sauvegardes avec le contrôle d’accès en fonction du rôle Azure
description: Le contrôle d’accès en fonction du rôle Azure permet de gérer l’accès aux opérations de gestion des sauvegardes dans le coffre Recovery Services.
ms.reviewer: utraghuv
ms.topic: conceptual
ms.date: 03/09/2021
ms.openlocfilehash: 0b321a5f33bd75ce8615d6d2a90442a83d9fff67
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102613440"
---
# <a name="use-azure-role-based-access-control-to-manage-azure-backup-recovery-points"></a>Utilisation du contrôle d’accès en fonction du rôle Azure pour gérer les points de récupération Sauvegarde Azure

Le contrôle d’accès en fonction du rôle Azure (Azure RBAC) permet une gestion des accès affinée pour Azure. Avec Azure RBAC, vous pouvez séparer les tâches au sein de votre équipe et accorder aux utilisateurs uniquement les accès nécessaires pour accomplir leur travail.

> [!IMPORTANT]
> Les rôles fournis par Azure Backup sont limités à des actions qui peuvent être effectuées dans le portail Azure ou via l’API REST, le coffre Recovery Services Powershell, ou les cmdlets d’interface de ligne de commande. Les actions effectuées dans l’IU cliente de l’agent Sauvegarde Azure, l’IU System Center Data Protection Manager ou l’IU de serveur de sauvegarde Azure ne sont pas contrôlées par ces rôles.

Azure Backup fournit 3 rôles intégrés pour contrôler les opérations de gestion des sauvegardes. Apprenez-en davantage sur les [rôles intégrés Azure](../role-based-access-control/built-in-roles.md)

* [Contributeur de sauvegarde](../role-based-access-control/built-in-roles.md#backup-contributor) : ce rôle dispose de toutes les autorisations pour créer et gérer des sauvegardes, à l’exception de la suppression du coffre Recovery Services et de l’octroi d’un accès à d’autres personnes. Imaginez ce rôle comme un administrateur de gestion des sauvegardes qui peut exécuter chaque opération de gestion des sauvegardes.
* [Opérateur de sauvegarde](../role-based-access-control/built-in-roles.md#backup-operator) : ce rôle dispose des autorisations généralement accordées à un contributeur à l’exception de la suppression de sauvegardes et de la gestion des stratégies de sauvegarde. Ce rôle est équivalent au contributeur, mais il ne peut pas effectuer d’opérations destructrices telles que l’arrêt de la sauvegarde avec suppression des données ou la suppression de l’enregistrement de ressources locales.
* [Lecteur de sauvegarde](../role-based-access-control/built-in-roles.md#backup-reader) : ce rôle dispose des autorisations pour afficher toutes les opérations de gestion des sauvegardes. Imaginez ce rôle comme un responsable de surveillance.

Si vous avez besoin de définir vos propres rôles pour un meilleur contrôle, découvrez comment créer [des rôles personnalisés dans RBAC Azure](../role-based-access-control/custom-roles.md).

## <a name="mapping-backup-built-in-roles-to-backup-management-actions"></a>Mappage des rôles de sauvegarde intégrés pour les actions de gestion des sauvegardes

### <a name="minimum-role-requirements-for-azure-vm-backup"></a>Exigences de rôle minimum pour la sauvegarde de machine virtuelle Azure

Le tableau suivant répertorie les actions de gestion des sauvegardes et le rôle Azure minimum nécessaire correspondant pour effectuer cette opération.

| Opération de gestion | Rôle Azure minimum nécessaire | Étendue requise | Alternative |
| --- | --- | --- | --- |
| Créer un coffre Recovery Services | Contributeur de sauvegarde | Groupe de ressources contenant le coffre |   |
| Activer la sauvegarde des machines virtuelles Azure | Opérateur de sauvegarde | Groupe de ressources contenant le coffre |   |
| | Contributeur de machine virtuelle | Ressource de machine virtuelle |  Plutôt qu’un rôle intégré, vous pouvez aussi utiliser un rôle personnalisé qui dispose des autorisations suivantes : Microsoft.Compute/virtualMachines/write |
| Sauvegarde de machine virtuelle à la demande | Opérateur de sauvegarde | Coffre Recovery Services |   |
| Restaurer une machine virtuelle | Opérateur de sauvegarde | Coffre Recovery Services |   |
| | Contributeur | Groupe de ressources dans lequel la machine virtuelle sera déployée |   Plutôt qu’un rôle intégré, vous pouvez aussi utiliser un rôle personnalisé qui dispose des autorisations suivantes : Microsoft.Resources/subscriptions/resourceGroups/write Microsoft.DomainRegistration/domains/write, Microsoft.Compute/virtualMachines/write  Microsoft.Network/virtualNetworks/read Microsoft.Network/virtualNetworks/subnets/join/action |
| | Contributeur de machine virtuelle | Machine virtuelle source qui a été sauvegardée |   Plutôt qu’un rôle intégré, vous pouvez aussi utiliser un rôle personnalisé qui dispose des autorisations suivantes : Microsoft.Compute/virtualMachines/write |
| Restaurer la sauvegarde de machine virtuelle de disques non managés | Opérateur de sauvegarde | Coffre Recovery Services |
| | Contributeur de machine virtuelle | Machine virtuelle source qui a été sauvegardée | Plutôt qu’un rôle intégré, vous pouvez aussi utiliser un rôle personnalisé qui dispose des autorisations suivantes : Microsoft.Compute/virtualMachines/write |
| | Contributeur de compte de stockage | Ressource de compte de stockage où les disques vont être restaurés |   Plutôt qu’un rôle intégré, vous pouvez aussi utiliser un rôle personnalisé qui dispose des autorisations suivantes : Microsoft.Storage/storageAccounts/write |
| Restaurer des disques managés de la sauvegarde de machine virtuelle | Opérateur de sauvegarde | Coffre Recovery Services |
| | Contributeur de machine virtuelle | Machine virtuelle source qui a été sauvegardée |    Plutôt qu’un rôle intégré, vous pouvez aussi utiliser un rôle personnalisé qui dispose des autorisations suivantes : Microsoft.Compute/virtualMachines/write |
| | Contributeur de compte de stockage | Compte de stockage temporaire sélectionné dans le cadre de la restauration pour conserver les données de l’espace de stockage avant de les convertir en disques managés. |   Plutôt qu’un rôle intégré, vous pouvez aussi utiliser un rôle personnalisé qui dispose des autorisations suivantes : Microsoft.Storage/storageAccounts/write |
| | Contributeur | Groupe de ressources vers lequel le(s) disque(s) managé(s) sera (seront) restauré(s) | Plutôt qu’un rôle intégré, vous pouvez aussi utiliser un rôle personnalisé qui dispose des autorisations suivantes : Microsoft.Resources/subscriptions/resourceGroups/write|
| Restaurer des fichiers individuels à partir d’une sauvegarde de machine virtuelle | Opérateur de sauvegarde | Coffre Recovery Services |
| | Contributeur de machine virtuelle | Machine virtuelle source qui a été sauvegardée | Plutôt qu’un rôle intégré, vous pouvez aussi utiliser un rôle personnalisé qui dispose des autorisations suivantes : Microsoft.Compute/virtualMachines/write |
| Restauration inter-région | Opérateur de sauvegarde | Abonnement au coffre Recovery Services | Cela s’ajoute aux autorisations de restauration mentionnées ci-dessus. Spécifiquement pour CRR, au lieu d’un rôle intégré, vous pouvez envisager un rôle personnalisé qui dispose des autorisations suivantes :   "Microsoft.RecoveryServices/locations/backupAadProperties/read" "Microsoft.RecoveryServices/locations/backupCrrJobs/action"         "Microsoft.RecoveryServices/locations/backupCrrJob/action" "Microsoft.RecoveryServices/locations/backupCrossRegionRestore/action"          "Microsoft.RecoveryServices/locations/backupCrrOperationResults/read" "Microsoft.RecoveryServices/locations/backupCrrOperationsStatus/read" |
| Créer une stratégie de sauvegarde pour la sauvegarde de machine virtuelle Azure | Contributeur de sauvegarde | Coffre Recovery Services |
| Modifier une stratégie de sauvegarde pour la sauvegarde de machine virtuelle Azure | Contributeur de sauvegarde | Coffre Recovery Services |
| Supprimer une stratégie de sauvegarde pour la sauvegarde de machine virtuelle Azure | Contributeur de sauvegarde | Coffre Recovery Services |
| Arrêt de la sauvegarde (avec conservation ou suppression des données) lors la sauvegarde d’une machine virtuelle | Contributeur de sauvegarde | Coffre Recovery Services |
| Inscrire un Windows Server/client/SCDPM ou un serveur de sauvegarde Azure | Opérateur de sauvegarde | Coffre Recovery Services |
| Supprimer un Windows Server/client/SCDPM inscrit ou un serveur de sauvegarde Azure | Contributeur de sauvegarde | Coffre Recovery Services |

> [!IMPORTANT]
> Si vous spécifiez Contributeur de machine virtuelle au niveau de l’étendue d’une ressource de machine virtuelle et sélectionnez **Sauvegarde** dans les paramètres de la machine virtuelle, l’écran **Activer la sauvegarde** s’ouvre même si la machine virtuelle est déjà sauvegardée. Cela est dû au fait que l’appel permettant de vérifier l’état de la sauvegarde fonctionne uniquement au niveau de l’abonnement. Pour éviter cela, accédez au coffre et ouvrez l’affichage des éléments de sauvegarde de la machine virtuelle ou spécifiez le rôle Contributeur de machine virtuelle au niveau de l’abonnement.

### <a name="minimum-role-requirements-for-azure-workload-backups-sql-and-hana-db-backups"></a>Exigences de rôle minimum pour les sauvegardes de charge de travail Azure (sauvegardes SQL et HANA DB)

Le tableau suivant répertorie les actions de gestion des sauvegardes et le rôle Azure minimum nécessaire correspondant pour effectuer cette opération.

| Opération de gestion | Rôle Azure minimum nécessaire | Étendue requise | Alternative |
| --- | --- | --- | --- |
| Créer un coffre Recovery Services | Contributeur de sauvegarde | Groupe de ressources contenant le coffre |   |
| Activer la sauvegarde des bases de données SQL et/ou HANA | Opérateur de sauvegarde | Groupe de ressources contenant le coffre |   |
| | Contributeur de machine virtuelle | Ressource de machine virtuelle où la base de source est installée |  Plutôt qu’un rôle intégré, vous pouvez aussi utiliser un rôle personnalisé qui dispose des autorisations suivantes : Microsoft.Compute/virtualMachines/write |
| Sauvegarde de base de données à la demande | Opérateur de sauvegarde | Coffre Recovery Services |   |
| Restaurer la base de données ou restaurer sous forme de fichiers | Opérateur de sauvegarde | Coffre Recovery Services |   |
| | Contributeur de machine virtuelle | Machine virtuelle source qui a été sauvegardée |   Plutôt qu’un rôle intégré, vous pouvez aussi utiliser un rôle personnalisé qui dispose des autorisations suivantes : Microsoft.Compute/virtualMachines/write |
| | Contributeur de machine virtuelle | Machine virtuelle cible dans laquelle la base de fichiers sera restaurée ou des fichiers seront créés |   Plutôt qu’un rôle intégré, vous pouvez aussi utiliser un rôle personnalisé qui dispose des autorisations suivantes : Microsoft.Compute/virtualMachines/write |
| Créer une stratégie de sauvegarde pour la sauvegarde de machine virtuelle Azure | Contributeur de sauvegarde | Coffre Recovery Services |
| Modifier une stratégie de sauvegarde pour la sauvegarde de machine virtuelle Azure | Contributeur de sauvegarde | Coffre Recovery Services |
| Supprimer une stratégie de sauvegarde pour la sauvegarde de machine virtuelle Azure | Contributeur de sauvegarde | Coffre Recovery Services |
| Arrêt de la sauvegarde (avec conservation ou suppression des données) lors la sauvegarde d’une machine virtuelle | Contributeur de sauvegarde | Coffre Recovery Services |

### <a name="minimum-role-requirements-for-the-azure-file-share-backup"></a>Exigences de rôle minimum pour la sauvegarde de partage de fichiers Azure

Le tableau suivant répertorie les actions de gestion des sauvegardes et le rôle nécessaire correspondant pour effectuer cette opération de partage Azure File.

| Opération de gestion | Rôle requis | Ressources |
| --- | --- | --- |
| Activer la sauvegarde d’un partage de fichiers Azure | Contributeur de sauvegarde |Coffre Recovery Services |
| |Compte de stockage | Ressource de compte de stockage de contributeur |
| Sauvegarde de machine virtuelle à la demande | Opérateur de sauvegarde | Coffre Recovery Services |
| Restaurer le partage de fichiers | Opérateur de sauvegarde | Coffre Recovery Services |
| | Contributeur de compte de stockage | Ressources du compte de stockage où les partages de fichiers source et cible de restauration sont présents |
| Restaurer des fichiers individuels | Opérateur de sauvegarde | Coffre Recovery Services |
| |Contributeur de compte de stockage|Ressources du compte de stockage où les partages de fichiers source et cible de restauration sont présents |
| Arrêter la protection |Contributeur de sauvegarde | Coffre Recovery Services |
| Annuler l’inscription d’un compte de stockage au coffre |Contributeur de sauvegarde | Coffre Recovery Services |
| |Contributeur de compte de stockage | Ressource du compte de stockage|

## <a name="next-steps"></a>Étapes suivantes

* [Contrôle d’accès en fonction du rôle Azure (Azure RBAC)](../role-based-access-control/role-assignments-portal.md) : Découvrez comment bien démarrer avec Azure RBAC dans le portail Azure.
* Découvrez comment gérer l’accès avec :
  * [PowerShell](../role-based-access-control/role-assignments-powershell.md)
  * [Azure CLI](../role-based-access-control/role-assignments-cli.md)
  * [REST API](../role-based-access-control/role-assignments-rest.md)
* [Résolution des problèmes de contrôle d’accès en fonction du rôle Azure](../role-based-access-control/troubleshooting.md) : obtenez des suggestions pour résoudre les problèmes courants.
