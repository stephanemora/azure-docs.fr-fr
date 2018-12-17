---
title: Modèle de données Log Analytics pour la sauvegarde Azure
description: Cet article présente des informations détaillées sur le modèle de données Log Analytics pour les données de sauvegarde Azure.
services: backup
author: adiganmsft
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 07/24/2017
ms.author: adigan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f4ecc87a0a7a0c74b02b72164fe129daa6530ea2
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2018
ms.locfileid: "52877579"
---
# <a name="log-analytics-data-model-for-azure-backup-data"></a>Modèle de données Log Analytics pour les données de sauvegarde Azure
Utilisez le modèle de données Log Analytics pour créer des rapports. Avec le modèle de données, vous pouvez créer des tableaux de bord et des requêtes personnalisés, ou personnaliser les données de sauvegarde Azure, comme vous le souhaitez.

## <a name="using-azure-backup-data-model"></a>Utilisation du modèle de données de sauvegarde Azure
Vous pouvez utiliser les champs suivants fournis dans le cadre du modèle de données pour créer des éléments visuels, des requêtes personnalisées et des tableaux de bord, en fonction de vos besoins.

### <a name="alert"></a>Alerte
Ce tableau fournit plus d’informations sur les champs liés aux alertes.

| Champ | Type de données | Description |
| --- | --- | --- |
| AlertUniqueId_s |Texte |Identificateur unique de l’alerte générée |
| AlertType_s |Texte |Type d’alerte, par exemple, Sauvegarde |
| AlertStatus_s |Texte |État de l’alerte, par exemple, Active |
| AlertOccurrenceDateTime_s |Date/Heure |Date et heure de la création de l’alerte |
| AlertSeverity_s |Texte |Gravité de l’alerte, par exemple, Critique |
| EventName_s |Texte |Nom de l’événement. Toujours AzureBackupCentralReport |
| BackupItemUniqueId_s |Texte |Identificateur unique de l’élément de sauvegarde associé à l’alerte |
| SchemaVersion_s |Texte |Version actuelle du schéma, par exemple **V1** |
| State_s |Texte |État actuel de l’objet alerte, par exemple, Actif, Supprimé |
| BackupManagementType_s |Texte |Type de fournisseur exécutant la sauvegarde (par exemple, Machine virtuelle IaaS, Dossier de fichiers) auquel appartient cette alerte |
| OperationName |Texte |Nom de l’opération actuelle, par exemple, Alerte |
| Catégorie |Texte |Catégorie de données de diagnostic transférées à Log Analytics. Toujours AzureBackupReport |
| Ressource |Texte |Ceci est la ressource pour laquelle les données sont collectées ; affiche le nom du coffre Recovery Services |
| ProtectedServerUniqueId_s |Texte |Identificateur unique du serveur protégé associé à l’alerte |
| VaultUniqueId_s |Texte |Identificateur unique du coffre protégé associé à l’alerte |
| SourceSystem |Texte |Système source des données actuelles - Azure |
| ResourceId |Texte |Identificateur unique de la ressource sur laquelle les données sont collectées. Par exemple, un id de ressource de coffre Recovery Services |
| SubscriptionId |Texte |Identificateur d’abonnement de la ressource (par ex. coffre Recovery Services) pour laquelle les données sont collectées |
| ResourceGroup |Texte |Groupe de ressources de la ressource (par ex. coffre Recovery Services) pour laquelle les données sont collectées |
| ResourceProvider |Texte |Fournisseur de ressources pour lequel les données sont collectées. Par exemple, Microsoft.RecoveryServices |
| ResourceType |Texte |Type de ressource pour lequel les données sont collectées. Par exemple, Coffres |

### <a name="backupitem"></a>BackupItem
Ce tableau fournit plus d’informations sur les champs liés aux éléments de sauvegarde.

| Champ | Type de données | Description |
| --- | --- | --- |
| EventName_s |Texte |Nom de l’événement. Toujours AzureBackupCentralReport |  
| BackupItemUniqueId_s |Texte |Identificateur unique de l’élément de sauvegarde |
| BackupItemId_s |Texte |Identificateur de l’élément de sauvegarde |
| BackupItemName_s |Texte |Nom de l’élément de sauvegarde |
| BackupItemFriendlyName_s |Texte |Nom convivial de l’élément de sauvegarde |
| BackupItemType_s |Texte |Type d’élément de sauvegarde, par exemple, Machine virtuelle, Dossier de fichiers |
| ProtectedServerName_s |Texte |Nom du serveur protégé auquel l’élément de sauvegarde appartient |
| ProtectionState_s |Texte |État de protection actuel de l’élément de sauvegarde par exemple, Protégé, Protection arrêtée |
| SchemaVersion_s |Texte |Version du schéma, par exemple **V1** |
| State_s |Texte |État de l’objet élément de sauvegarde, par exemple, Actif, Supprimé |
| BackupManagementType_s |Texte |Type de fournisseur exécutant la sauvegarde (par exemple, Machine virtuelle IaaS, Dossier de fichiers) auquel appartient cet élément de sauvegarde |
| OperationName |Texte |Nom de l’opération, par exemple, BackupItem |
| Catégorie |Texte |Catégorie de données de diagnostic transférées à Log Analytics. Toujours AzureBackupReport |
| Ressource |Texte |Ressource pour laquelle les données sont collectées, par exemple le nom du coffre Recovery Services |
| SourceSystem |Texte |Système source des données actuelles - Azure |
| ResourceId |Texte |Id de ressource des données collectées, par exemple id de ressource du coffre Recovery Services |
| SubscriptionId |Texte |Identificateur d’abonnement de la ressource (par ex. coffre Recovery Services) des données collectées |
| ResourceGroup |Texte |Groupe de ressources de la ressource (par ex. coffre Recovery Services) des données collectées |
| ResourceProvider |Texte |Fournisseur de ressources des données collectées, par exemple, Microsoft.RecoveryServices |
| ResourceType |Texte |Type de la ressource des données collectées, par exemple, Coffres |

### <a name="backupitemassociation"></a>BackupItemAssociation
Ce tableau fournit des détails sur les associations d’éléments de sauvegarde avec différentes entités.

| Champ | Type de données | Description |
| --- | --- | --- |
| EventName_s |Texte |Ce champ représente le nom de cet événement, qui est toujours AzureBackupCentralReport |  
| BackupItemUniqueId_s |Texte |ID unique de l’élément de sauvegarde |
| SchemaVersion_s |Texte |Ce champ indique la version actuelle du schéma, **V1** |
| State_s |Texte |État actuel de l’objet association d’éléments de sauvegarde, par exemple, Actif, Supprimé |
| BackupManagementType_s |Texte |Type de fournisseur pour le serveur exécutant la sauvegarde, par exemple, Machine virtuelle IaaS, Dossier de fichiers |
| OperationName |Texte |Ce champ représente le nom de l’opération en cours - Association d’éléments de sauvegarde |
| Catégorie |Texte |Ce champ représente la catégorie des données de diagnostic transférées à Log Analytics, c’est-à-dire AzureBackupReport |
| Ressource |Texte |Ceci est la ressource pour laquelle les données sont collectées ; affiche le nom du coffre Recovery Services |
| PolicyUniqueId_g |Texte |Identificateur unique de la stratégie associée à l’élément de sauvegarde |
| ProtectedServerUniqueId_s |Texte |Identificateur unique du serveur protégé associé à l’élément de sauvegarde |
| VaultUniqueId_s |Texte |Identificateur unique du coffre contenant l’élément de sauvegarde |
| SourceSystem |Texte |Système source des données actuelles - Azure |
| ResourceId |Texte |Identificateur de ressource des données collectées. Par exemple, id de ressource du coffre Recovery Services |
| SubscriptionId |Texte |Identificateur d’abonnement de la ressource (par ex. coffre Recovery Services) pour laquelle les données sont collectées |
| ResourceGroup |Texte |Groupe de ressources de la ressource (par ex. coffre Recovery Services) pour laquelle les données sont collectées |
| ResourceProvider |Texte |Fournisseur de ressources des données collectées, par exemple, Microsoft.RecoveryServices |
| ResourceType |Texte |Type de la ressource pour laquelle les données sont collectées, par exemple, Coffres |

### <a name="job"></a>Travail
Ce tableau fournit plus d’informations sur les champs liés aux travaux.

| Champ | Type de données | Description |
| --- | --- | --- |
| EventName_s |Texte |Nom de l’événement. Toujours AzureBackupCentralReport |
| BackupItemUniqueId_s |Texte |Identificateur unique de l’élément de sauvegarde |
| SchemaVersion_s |Texte |Version du schéma, par exemple **V1** |
| State_s |Texte |État actuel de l’objet travail, par exemple, Actif, Supprimé |
| BackupManagementType_s |Texte |Type de fournisseur pour le serveur exécutant la sauvegarde, par exemple, Machine virtuelle IaaS, Dossier de fichiers |
| OperationName |Texte |Ce champ représente le nom de l’opération en cours - Travail |
| Catégorie |Texte |Ce champ représente la catégorie des données de diagnostic transférées à Log Analytics, c’est-à-dire AzureBackupReport |
| Ressource |Texte |Ceci est la ressource pour laquelle les données sont collectées ; affiche le nom du coffre Recovery Services |
| ProtectedServerUniqueId_s |Texte |Identificateur unique du serveur protégé associé au travail |
| VaultUniqueId_s |Texte |Identificateur unique du coffre protégé |
| JobOperation_s |Texte |Opération pour laquelle le travail est exécuté, par exemple, Sauvegarde, Restauration, Configuration de sauvegarde |
| JobStatus_s |Texte |État du travail terminé par exemple, Terminé, Échec |
| JobFailureCode_s |Texte |Chaîne de Code d’échec en raison du type d’échec de travail survenu |
| JobStartDateTime_s |Date/Heure |Date et heure de démarrage de l’exécution du travail |
| BackupStorageDestination_s |Texte |Destination du stockage de sauvegarde, par exemple, Cloud, Disque  |
| JobDurationInSecs_s | Number |Durée totale de la tâche en secondes |
| DataTransferredInMB_s | Number |Données transférées en Mo pour ce travail|
| JobUniqueId_g |Texte |ID unique d’identification du travail |
| SourceSystem |Texte |Système source des données actuelles - Azure |
| ResourceId |Texte |Identificateur de ressource des données collectées. Par exemple, id de ressource du coffre Recovery Services|
| SubscriptionId |Texte |Identificateur d’abonnement de la ressource (par ex. coffre Recovery Services) pour laquelle les données sont collectées |
| ResourceGroup |Texte |Groupe de ressources de la ressource (par ex. coffre Recovery Services) pour laquelle les données sont collectées |
| ResourceProvider |Texte |Fournisseur de ressources pour lequel les données sont collectées. Par exemple, Microsoft.RecoveryServices |
| ResourceType |Texte |Type de ressource pour lequel les données sont collectées. Par exemple, Coffres |

### <a name="policy"></a>Stratégie
Ce tableau fournit plus d’informations sur les champs liés à la stratégie.

| Champ | Type de données | Description |
| --- | --- | --- |
| EventName_s |Texte |Ce champ représente le nom de cet événement, qui est toujours AzureBackupCentralReport |
| SchemaVersion_s |Texte |Ce champ indique la version actuelle du schéma, **V1** |
| State_s |Texte |État actuel de l’objet stratégie, par exemple, Actif, Supprimé |
| BackupManagementType_s |Texte |Type de fournisseur pour le serveur exécutant la sauvegarde, par exemple, Machine virtuelle IaaS, Dossier de fichiers |
| OperationName |Texte |Ce champ représente le nom de l’opération en cours - Stratégie |
| Catégorie |Texte |Ce champ représente la catégorie des données de diagnostic transférées à Log Analytics, c’est-à-dire AzureBackupReport |
| Ressource |Texte |Ceci est la ressource pour laquelle les données sont collectées ; affiche le nom du coffre Recovery Services |
| PolicyUniqueId_g |Texte |ID unique d’identification de la stratégie |
| PolicyName_s |Texte |Nom de la stratégie définie |
| BackupFrequency_s |Texte |Fréquence à laquelle les sauvegardes sont exécutées, par exemple, Quotidienne, Hebdomadaire |
| BackupTimes_s |Texte |Date et heure auxquelles les sauvegardes sont planifiées |
| BackupDaysOfTheWeek_s |Texte |Jours de la semaine auxquels les sauvegardes sont planifiées |
| RetentionDuration_s |Nombre entier |Durée de rétention des sauvegardes configurées |
| DailyRetentionDuration_s |Nombre entier |Durée totale de rétention en jours des sauvegardes configurées |
| DailyRetentionTimes_s |Texte |Date et heure auxquelles la rétention quotidienne est configurée |
| WeeklyRetentionDuration_s |Nombre décimal |Durée totale de rétention hebdomadaire en semaines des sauvegardes configurées |
| WeeklyRetentionTimes_s |Texte |Date et heure auxquelles la rétention hebdomadaire est configurée |
| WeeklyRetentionDaysOfTheWeek_s |Texte |Jours de la semaine sélectionnés pour la rétention hebdomadaire |
| MonthlyRetentionDuration_s |Nombre décimal |Durée totale de rétention en mois des sauvegardes configurées |
| MonthlyRetentionTimes_s |Texte |Date et heure auxquelles la rétention mensuelle est configurée |
| MonthlyRetentionFormat_s |Texte |Type de configuration de la rétention mensuelle, par exemple, quotidienne pour une configuration quotidienne, hebdomadaire pour une configuration hebdomadaire |
| MonthlyRetentionDaysOfTheWeek_s |Texte |Jours de la semaine sélectionnés pour la rétention mensuelle |
| MonthlyRetentionWeeksOfTheMonth_s |Texte |Semaines du mois au cours desquelles la rétention mensuelle est configurée, par exemple, Première, Dernière, etc. |
| YearlyRetentionDuration_s |Nombre décimal |Durée totale de rétention en années des sauvegardes configurées |
| YearlyRetentionTimes_s |Texte |Date et heure auxquelles la rétention annuelle est configurée |
| YearlyRetentionMonthsOfTheYear_s |Texte |Mois de l’année sélectionnés pour la rétention annuelle |
| YearlyRetentionFormat_s |Texte |Type de configuration de la rétention annuelle, par exemple, quotidienne pour une configuration quotidienne, hebdomadaire pour une configuration hebdomadaire |
| YearlyRetentionDaysOfTheMonth_s |Texte |Dates du mois sélectionnées pour la rétention annuelle |
| SourceSystem |Texte |Système source des données actuelles - Azure |
| ResourceId |Texte |Identificateur de ressource des données collectées. Par exemple, id de ressource du coffre Recovery Services |
| SubscriptionId |Texte |Identificateur d’abonnement de la ressource (par ex. coffre Recovery Services) pour laquelle les données sont collectées |
| ResourceGroup |Texte |Groupe de ressources de la ressource (par ex. coffre Recovery Services) pour laquelle les données sont collectées |
| ResourceProvider |Texte |Fournisseur de ressources pour lequel les données sont collectées. Par exemple, Microsoft.RecoveryServices |
| ResourceType |Texte |Type de ressource pour lequel les données sont collectées. Par exemple, Coffres |

### <a name="policyassociation"></a>PolicyAssociation
Ce tableau fournit des détails sur les associations de stratégies avec différentes entités.

| Champ | Type de données | Description |
| --- | --- | --- |
| EventName_s |Texte |Ce champ représente le nom de cet événement, qui est toujours AzureBackupCentralReport |
| SchemaVersion_s |Texte |Ce champ indique la version actuelle du schéma, **V1** |
| State_s |Texte |État actuel de l’objet stratégie, par exemple, Actif, Supprimé |
| BackupManagementType_s |Texte |Type de fournisseur pour le serveur exécutant la sauvegarde, par exemple, Machine virtuelle IaaS, Dossier de fichiers |
| OperationName |Texte |Ce champ représente le nom de l’opération en cours - Association de stratégies |
| Catégorie |Texte |Ce champ représente la catégorie des données de diagnostic transférées à Log Analytics, c’est-à-dire AzureBackupReport |
| Ressource |Texte |Ceci est la ressource pour laquelle les données sont collectées ; affiche le nom du coffre Recovery Services |
| PolicyUniqueId_g |Texte |ID unique d’identification de la stratégie |
| VaultUniqueId_s |Texte |ID unique du coffre auquel cette stratégie appartient |
| SourceSystem |Texte |Système source des données actuelles - Azure |
| ResourceId |Texte |Identificateur de ressource des données collectées. Par exemple, id de ressource du coffre Recovery Services |
| SubscriptionId |Texte |Identificateur d’abonnement de la ressource (par ex. coffre Recovery Services) pour laquelle les données sont collectées |
| ResourceGroup |Texte |Groupe de ressources de la ressource (par ex. coffre Recovery Services) pour laquelle les données sont collectées |
| ResourceProvider |Texte |Fournisseur de ressources pour lequel les données sont collectées. Par exemple, Microsoft.RecoveryServices |
| ResourceType |Texte |Type de ressource pour lequel les données sont collectées. Par exemple, Coffres |

### <a name="protectedserver"></a>ProtectedServer
Ce tableau fournit plus d’informations sur les champs liés au serveur protégé.

| Champ | Type de données | Description |
| --- | --- | --- |
| EventName_s |Texte |Ce champ représente le nom de cet événement, qui est toujours AzureBackupCentralReport |
| ProtectedServerName_s |Texte |Nom du serveur protégé |
| SchemaVersion_s |Texte |Ce champ indique la version actuelle du schéma, **V1** |
| State_s |Texte |État actuel de l’objet serveur protégé, par exemple, Actif, Supprimé |
| BackupManagementType_s |Texte |Type de fournisseur pour le serveur exécutant la sauvegarde, par exemple, Machine virtuelle IaaS, Dossier de fichiers |
| OperationName |Texte |Ce champ représente le nom de l’opération en cours - Serveur protégé |
| Catégorie |Texte |Ce champ représente la catégorie des données de diagnostic transférées à Log Analytics, c’est-à-dire AzureBackupReport |
| Ressource |Texte |Ceci est la ressource pour laquelle les données sont collectées ; affiche le nom du coffre Recovery Services |
| ProtectedServerUniqueId_s |Texte |ID unique du serveur protégé |
| RegisteredContainerId_s |Texte |ID du conteneur inscrit pour la sauvegarde |
| ProtectedServerType_s |Texte |Type de serveur protégé, par exemple, Windows |
| ProtectedServerFriendlyName_s |Texte |Nom convivial du serveur protégé |
| AzureBackupAgentVersion_s |Texte |Numéro de version de l’agent Backup |
| SourceSystem |Texte |Système source des données actuelles - Azure |
| ResourceId |Texte |Identificateur de ressource des données collectées. Par exemple, id de ressource du coffre Recovery Services |
| SubscriptionId |Texte |Identificateur d’abonnement de la ressource (par ex. coffre Recovery Services) pour laquelle les données sont collectées |
| ResourceGroup |Texte |Groupe de ressources de la ressource (par ex. coffre Recovery Services) pour laquelle les données sont collectées |
| ResourceProvider |Texte |Fournisseur de ressources pour lequel les données sont collectées. Par exemple, Microsoft.RecoveryServices |
| ResourceType |Texte |Type de ressource pour lequel les données sont collectées. Par exemple, Coffres |

### <a name="protectedserverassociation"></a>ProtectedServerAssociation
Ce tableau fournit des détails sur les associations de serveurs protégés avec d’autres entités.

| Champ | Type de données | Description |
| --- | --- | --- |
| EventName_s |Texte |Ce champ représente le nom de cet événement, qui est toujours AzureBackupCentralReport |
| SchemaVersion_s |Texte |Ce champ indique la version actuelle du schéma, **V1** |
| State_s |Texte |État actuel de l’objet association de serveurs protégés, par exemple, Actif, Supprimé |
| BackupManagementType_s |Texte |Type de fournisseur pour le serveur exécutant la sauvegarde, par exemple, Machine virtuelle IaaS, Dossier de fichiers |
| OperationName |Texte |Ce champ représente le nom de l’opération en cours - Association de serveurs protégés |
| Catégorie |Texte |Ce champ représente la catégorie des données de diagnostic transférées à Log Analytics, c’est-à-dire AzureBackupReport |
| Ressource |Texte |Ceci est la ressource pour laquelle les données sont collectées ; affiche le nom du coffre Recovery Services |
| ProtectedServerUniqueId_s |Texte |ID unique du serveur protégé |
| VaultUniqueId_s |Texte |ID unique du coffre auquel ce serveur protégé appartient |
| SourceSystem |Texte |Système source des données actuelles - Azure |
| ResourceId |Texte |Identificateur de ressource des données collectées. Par exemple, id de ressource du coffre Recovery Services |
| SubscriptionId |Texte |Identificateur d’abonnement de la ressource (par ex. coffre Recovery Services) pour laquelle les données sont collectées |
| ResourceGroup |Texte |Groupe de ressources de la ressource (par ex. coffre Recovery Services) pour laquelle les données sont collectées |
| ResourceProvider |Texte |Fournisseur de ressources pour lequel les données sont collectées. Par exemple, Microsoft.RecoveryServices |
| ResourceType |Texte |Type de ressource pour lequel les données sont collectées. Par exemple, Coffres |

### <a name="storage"></a>Stockage
Ce tableau fournit plus d’informations sur les champs liés au stockage.

| Champ | Type de données | Description |
| --- | --- | --- |
| CloudStorageInBytes_s |Nombre décimal |Stockage des sauvegardes dans le cloud utilisé par les sauvegardes, calculé en fonction de la dernière valeur |
| ProtectedInstances_s |Nombre décimal |Nombre d’instances protégées utilisées pour calculer le stockage frontal dans la facturation, calculé en fonction de la dernière valeur |
| EventName_s |Texte |Ce champ représente le nom de cet événement, qui est toujours AzureBackupCentralReport |
| SchemaVersion_s |Texte |Ce champ indique la version actuelle du schéma, **V1** |
| State_s |Texte |État actuel de l’objet stockage, par exemple, Actif, Supprimé |
| BackupManagementType_s |Texte |Type de fournisseur pour le serveur exécutant la sauvegarde, par exemple, Machine virtuelle IaaS, Dossier de fichiers |
| OperationName |Texte |Ce champ représente le nom de l’opération en cours - Stockage |
| Catégorie |Texte |Ce champ représente la catégorie des données de diagnostic transférées à Log Analytics, c’est-à-dire AzureBackupReport |
| Ressource |Texte |Ceci est la ressource pour laquelle les données sont collectées ; affiche le nom du coffre Recovery Services |
| ProtectedServerUniqueId_s |Texte |ID unique du serveur protégé pour lequel le stockage est calculé |
| VaultUniqueId_s |Texte |ID unique du coffre pour lequel le stockage est calculé |
| SourceSystem |Texte |Système source des données actuelles - Azure |
| ResourceId |Texte |Identificateur de ressource des données collectées. Par exemple, id de ressource du coffre Recovery Services |
| SubscriptionId |Texte |Identificateur d’abonnement de la ressource (par ex. coffre Recovery Services) pour laquelle les données sont collectées |
| ResourceGroup |Texte |Groupe de ressources de la ressource (par ex. coffre Recovery Services) pour laquelle les données sont collectées |
| ResourceProvider |Texte |Fournisseur de ressources pour lequel les données sont collectées. Par exemple, Microsoft.RecoveryServices |
| ResourceType |Texte |Type de ressource pour lequel les données sont collectées. Par exemple, Coffres |

### <a name="vault"></a>Coffre
Ce tableau fournit plus d’informations sur les champs liés aux coffres.

| Champ | Type de données | Description |
| --- | --- | --- |
| EventName_s |Texte |Ce champ représente le nom de cet événement, qui est toujours AzureBackupCentralReport |
| SchemaVersion_s |Texte |Ce champ indique la version actuelle du schéma, **V1** |
| State_s |Texte |État actuel de l’objet coffre, par exemple, Actif, Supprimé |
| OperationName |Texte |Ce champ représente le nom de l’opération en cours - Coffre |
| Catégorie |Texte |Ce champ représente la catégorie des données de diagnostic transférées à Log Analytics, c’est-à-dire AzureBackupReport |
| Ressource |Texte |Ceci est la ressource pour laquelle les données sont collectées ; affiche le nom du coffre Recovery Services |
| VaultUniqueId_s |Texte |ID unique du coffre |
| VaultName_s |Texte |Nom du coffre |
| AzureDataCenter_s |Texte |Centre de données dans lequel se trouve le coffre |
| StorageReplicationType_s |Texte |Type de réplication de stockage pour le coffre, par exemple, Géoredondant |
| SourceSystem |Texte |Système source des données actuelles - Azure |
| ResourceId |Texte |Identificateur de ressource des données collectées. Par exemple, id de ressource du coffre Recovery Services |
| SubscriptionId |Texte |Identificateur d’abonnement de la ressource (par ex. coffre Recovery Services) pour laquelle les données sont collectées |
| ResourceGroup |Texte |Groupe de ressources de la ressource (par ex. coffre Recovery Services) pour laquelle les données sont collectées |
| ResourceProvider |Texte |Fournisseur de ressources pour lequel les données sont collectées. Par exemple, Microsoft.RecoveryServices |
| ResourceType |Texte |Type de ressource pour lequel les données sont collectées. Par exemple, Coffres |

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous avez examiné le modèle de données de création de rapports de la Sauvegarde Azure, vous pouvez commencer à [créer des tableaux de bord](../azure-monitor/platform/dashboards.md) dans Log Analytics.
