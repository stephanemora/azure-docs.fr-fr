---
title: Azure Monitor enregistre le modèle de données pour la sauvegarde Azure
description: Cet article traite de Azure Monitor journalise les détails de modèle de données pour les données de sauvegarde Azure.
services: backup
author: adigan
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 02/26/2019
ms.author: adigan
ms.openlocfilehash: dd4dad2cc3e541d3b6866c02341161dc1d9e1e6c
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/18/2019
ms.locfileid: "58075268"
---
# <a name="log-analytics-data-model-for-azure-backup-data"></a>Modèle de données Log Analytics pour les données de sauvegarde Azure

Utilisez le modèle de données Analytique de journal pour créer des alertes personnalisées à partir de l’Analytique de journal.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

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
|AlertTimeToResolveInMinutes_s    | Number        |Temps nécessaire pour résoudre une alerte. Vide pour les alertes actives.         |
|AlertConsolidationStatus_s   |Texte         |Déterminer si l’alerte est une alerte consolidée ou non         |
|CountOfAlertsConsolidated_s     |Number         |Nombre d’alertes consolidées dans le cas d’une alerte consolidée          |
|AlertRaisedOn_s     |Texte         |Type d’entité de sur que l’alerte est déclenchée         |
|AlertCode_s     |Texte         |Code pour identifier un type d’alerte         |
|RecommendedAction_s   |Texte         |Action recommandée pour résoudre l’alerte         |
| EventName_s |Texte |Nom de l’événement. Toujours AzureBackupCentralReport |
| BackupItemUniqueId_s |Texte |Identificateur unique de l’élément de sauvegarde associé à l’alerte |
| SchemaVersion_s |Texte |Version actuelle du schéma, par exemple **V2** |
| State_s |Texte |État actuel de l’objet alerte, par exemple, Actif, Supprimé |
| BackupManagementType_s |Texte |Type de fournisseur exécutant la sauvegarde (par exemple, Machine virtuelle IaaS, Dossier de fichiers) auquel appartient cette alerte |
| OperationName |Texte |Nom de l’opération actuelle, par exemple, Alerte |
| Catégorie |Texte |Catégorie de données de diagnostic vers les journaux Azure Monitor. Toujours AzureBackupReport |
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
| ProtectionState_s |Texte |État de protection actuel de l’élément de sauvegarde par exemple, Protégé, Protection arrêtée |
| ProtectionGroupName_s |Texte | Nom du groupe de Protection l’élément de sauvegarde est protégé, SC DPM et MABS, le cas échéant|
| SecondaryBackupProtectionState_s |Texte |Si la protection secondaire est activée pour l’élément de sauvegarde|
| SchemaVersion_s |Texte |Version du schéma, par exemple, **V2** |
| State_s |Texte |État de l’objet élément de sauvegarde, par exemple, Actif, Supprimé |
| BackupManagementType_s |Texte |Type de fournisseur exécutant la sauvegarde (par exemple, Machine virtuelle IaaS, Dossier de fichiers) auquel appartient cet élément de sauvegarde |
| OperationName |Texte |Nom de l’opération, par exemple, BackupItem |
| Catégorie |Texte |Catégorie de données de diagnostic vers les journaux Azure Monitor. Toujours AzureBackupReport |
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
| SchemaVersion_s |Texte |Ce champ indique la version actuelle du schéma, il est **V2** |
| State_s |Texte |État actuel de l’objet association d’éléments de sauvegarde, par exemple, Actif, Supprimé |
| BackupManagementType_s |Texte |Type de fournisseur pour le serveur exécutant la sauvegarde, par exemple, Machine virtuelle IaaS, Dossier de fichiers |
| BackupItemSourceSize_s |Texte | Taille frontale de l’élément de sauvegarde |
| BackupManagementServerUniqueId_s |Texte | Champ pour identifier de manière unique le serveur d’administration de sauvegarde de l’élément de sauvegarde est protégé par le biais, le cas échéant |
| Catégorie |Texte |Ce champ représente la catégorie des données de diagnostic transférées à Log Analytics, c’est-à-dire AzureBackupReport |
| OperationName |Texte |Ce champ représente le nom de l’opération en cours - Association d’éléments de sauvegarde |
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

### <a name="backupmanagementserver"></a>BackupManagementServer

Ce tableau fournit des détails sur les associations d’éléments de sauvegarde avec différentes entités.

| Champ | Type de données | Description |
| --- | --- | --- |
|BackupManagementServerName_s     |Texte         |Nom de la sauvegarde du serveur d’administration        |
|AzureBackupAgentVersion_s     |Texte         |Version de l’Agent de sauvegarde Azure sur le serveur de gestion des sauvegardes          |
|BackupManagementServerVersion_s     |Texte         |Version du serveur d’administration de sauvegarde|
|BackupManagementServerOSVersion_s    |Texte            |Version du système d’exploitation du serveur d’administration de sauvegarde|
|BackupManagementServerType_s     |Texte         |Type de la sauvegarde du serveur d’administration, en tant que serveur de sauvegarde AZURE, SC DPM|
|BackupManagementServerUniqueId_s     |Texte         |Champ pour identifier de façon unique le serveur de gestion de sauvegarde       |
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
| SchemaVersion_s |Texte |Version du schéma, par exemple, **V2** |
| State_s |Texte |État actuel de l’objet travail, par exemple, Actif, Supprimé |
| BackupManagementType_s |Texte |Type de fournisseur pour le serveur exécutant la sauvegarde, par exemple, Machine virtuelle IaaS, Dossier de fichiers |
| OperationName |Texte |Ce champ représente le nom de l’opération en cours - Travail |
| Catégorie |Texte |Ce champ représente la catégorie de données de diagnostic vers les journaux Azure Monitor, c'est-à-dire AzureBackupReport |
| Ressource |Texte |Ceci est la ressource pour laquelle les données sont collectées ; affiche le nom du coffre Recovery Services |
| ProtectedServerUniqueId_s |Texte |Identificateur unique du serveur protégé associé au travail |
| ProtectedContainerUniqueId_s |Texte | Id unique pour identifier le conteneur protégé, sur que le travail est exécuté |
| VaultUniqueId_s |Texte |Identificateur unique du coffre protégé |
| JobOperation_s |Texte |Opération pour laquelle le travail est exécuté, par exemple, Sauvegarde, Restauration, Configuration de sauvegarde |
| JobStatus_s |Texte |État du travail terminé par exemple, Terminé, Échec |
| JobFailureCode_s |Texte |Chaîne de Code d’échec en raison du type d’échec de travail survenu |
| JobStartDateTime_s |Date/Heure |Date et heure de démarrage de l’exécution du travail |
| BackupStorageDestination_s |Texte |Destination du stockage de sauvegarde, par exemple, Cloud, Disque  |
| AdHocOrScheduledJob_s |Texte | Champ pour spécifier si le travail est Ad Hoc ou planifiée |
| JobDurationInSecs_s | Number |Durée totale de la tâche en secondes |
| DataTransferredInMB_s | Number |Données transférées en Mo pour ce travail|
| JobUniqueId_g |Texte |ID unique d’identification du travail |
| RecoveryJobDestination_s |Texte | Destination d’une tâche de récupération, où les données sont récupérées. |
| RecoveryJobRPDateTime_s |DateTime | La date, heure de création de point de récupération en cours de récupération |
| RecoveryJobRPLocation_s |Texte | L’emplacement où le point de récupération est en cours de récupération a été stocké|
| SourceSystem |Texte |Système source des données actuelles - Azure |
| ResourceId |Texte |Identificateur de ressource des données collectées. Par exemple, id de ressource du coffre Recovery Services|
| SubscriptionId |Texte |Identificateur d’abonnement de la ressource (par ex. coffre Recovery Services) pour laquelle les données sont collectées |
| ResourceGroup |Texte |Groupe de ressources de la ressource (par ex. coffre Recovery Services) pour laquelle les données sont collectées |
| ResourceProvider |Texte |Fournisseur de ressources pour lequel les données sont collectées. Par exemple, Microsoft.RecoveryServices |
| ResourceType |Texte |Type de ressource pour lequel les données sont collectées. Par exemple, Coffres |

### <a name="policy"></a>Stratégie

Ce tableau fournit plus d’informations sur les champs liés à la stratégie.

| Champ | Type de données | Versions applicables | Description |
| --- | --- | --- | --- |
| EventName_s |Texte ||Ce champ représente le nom de cet événement, qui est toujours AzureBackupCentralReport |
| SchemaVersion_s |Texte ||Ce champ indique la version actuelle du schéma, il est **V2** |
| State_s |Texte ||État actuel de l’objet stratégie, par exemple, Actif, Supprimé |
| BackupManagementType_s |Texte ||Type de fournisseur pour le serveur exécutant la sauvegarde, par exemple, Machine virtuelle IaaS, Dossier de fichiers |
| OperationName |Texte ||Ce champ représente le nom de l’opération en cours - Stratégie |
| Catégorie |Texte ||Ce champ représente la catégorie de données de diagnostic vers les journaux Azure Monitor, c'est-à-dire AzureBackupReport |
| Ressource |Texte ||Ceci est la ressource pour laquelle les données sont collectées ; affiche le nom du coffre Recovery Services |
| PolicyUniqueId_g |Texte ||ID unique d’identification de la stratégie |
| PolicyName_s |Texte ||Nom de la stratégie définie |
| BackupFrequency_s |Texte ||Fréquence à laquelle les sauvegardes sont exécutées, par exemple, Quotidienne, Hebdomadaire |
| BackupTimes_s |Texte ||Date et heure auxquelles les sauvegardes sont planifiées |
| BackupDaysOfTheWeek_s |Texte ||Jours de la semaine auxquels les sauvegardes sont planifiées |
| RetentionDuration_s |Nombre entier ||Durée de rétention des sauvegardes configurées |
| DailyRetentionDuration_s |Nombre entier ||Durée totale de rétention en jours des sauvegardes configurées |
| DailyRetentionTimes_s |Texte ||Date et heure auxquelles la rétention quotidienne est configurée |
| WeeklyRetentionDuration_s |Nombre décimal ||Durée totale de rétention hebdomadaire en semaines des sauvegardes configurées |
| WeeklyRetentionTimes_s |Texte ||Date et heure auxquelles la rétention hebdomadaire est configurée |
| WeeklyRetentionDaysOfTheWeek_s |Texte ||Jours de la semaine sélectionnés pour la rétention hebdomadaire |
| MonthlyRetentionDuration_s |Nombre décimal ||Durée totale de rétention en mois des sauvegardes configurées |
| MonthlyRetentionTimes_s |Texte ||Date et heure auxquelles la rétention mensuelle est configurée |
| MonthlyRetentionFormat_s |Texte ||Type de configuration de la rétention mensuelle, par exemple, quotidienne pour une configuration quotidienne, hebdomadaire pour une configuration hebdomadaire |
| MonthlyRetentionDaysOfTheWeek_s |Texte ||Jours de la semaine sélectionnés pour la rétention mensuelle |
| MonthlyRetentionWeeksOfTheMonth_s |Texte ||Semaines du mois au cours desquelles la rétention mensuelle est configurée, par exemple, Première, Dernière, etc. |
| YearlyRetentionDuration_s |Nombre décimal ||Durée totale de rétention en années des sauvegardes configurées |
| YearlyRetentionTimes_s |Texte ||Date et heure auxquelles la rétention annuelle est configurée |
| YearlyRetentionMonthsOfTheYear_s |Texte ||Mois de l’année sélectionnés pour la rétention annuelle |
| YearlyRetentionFormat_s |Texte ||Type de configuration de la rétention annuelle, par exemple, quotidienne pour une configuration quotidienne, hebdomadaire pour une configuration hebdomadaire | |
| YearlyRetentionDaysOfTheMonth_s |Texte ||Dates du mois sélectionnées pour la rétention annuelle |
| SynchronisationFrequencyPerDay_s |Nombre entier |v2|Nombre de fois en une journée, qu'une sauvegarde de fichiers est synchronisée pour SC DPM et serveur de sauvegarde AZURE |
| DiffBackupFormat_s |Texte |v2|Mettre en forme pour les sauvegardes différentielles à SQL de sauvegarde de machine virtuelle Azure |
| DiffBackupTime_s |Temps |v2|Temps pour les sauvegardes différentielles à SQL dans la sauvegarde de machine virtuelle Azure|
| DiffBackupRetentionDuration_s |Nombre décimal |v2|Durée de rétention des sauvegardes différentielles à SQL dans la sauvegarde de machine virtuelle Azure|
| LogBackupFrequency_s |Nombre décimal |v2|Fréquence des sauvegardes du journal pour SQL|
| LogBackupRetentionDuration_s |Nombre décimal |v2|Durée de rétention des sauvegardes de journal pour SQL dans la sauvegarde de machine virtuelle Azure|
| DiffBackupDaysofTheWeek_s |Texte |v2|Jours de la semaine pour les sauvegardes différentielles à SQL dans la sauvegarde de machine virtuelle Azure|
| SourceSystem |Texte ||Système source des données actuelles - Azure |
| ResourceId |Texte ||Identificateur de ressource des données collectées. Par exemple, id de ressource du coffre Recovery Services |
| SubscriptionId |Texte ||Identificateur d’abonnement de la ressource (par ex. coffre Recovery Services) pour laquelle les données sont collectées |
| ResourceGroup |Texte ||Groupe de ressources de la ressource (par ex. coffre Recovery Services) pour laquelle les données sont collectées |
| ResourceProvider |Texte ||Fournisseur de ressources pour lequel les données sont collectées. Par exemple, Microsoft.RecoveryServices |
| ResourceType |Texte ||Type de ressource pour lequel les données sont collectées. Par exemple, Coffres |

### <a name="policyassociation"></a>PolicyAssociation

Ce tableau fournit des détails sur les associations de stratégies avec différentes entités.

| Champ | Type de données | Versions applicables | Description |
| --- | --- | --- | --- |
| EventName_s |Texte ||Ce champ représente le nom de cet événement, qui est toujours AzureBackupCentralReport |
| SchemaVersion_s |Texte ||Ce champ indique la version actuelle du schéma, il est **V2** |
| State_s |Texte ||État actuel de l’objet stratégie, par exemple, Actif, Supprimé |
| BackupManagementType_s |Texte ||Type de fournisseur pour le serveur exécutant la sauvegarde, par exemple, Machine virtuelle IaaS, Dossier de fichiers |
| OperationName |Texte ||Ce champ représente le nom de l’opération en cours - Association de stratégies |
| Catégorie |Texte ||Ce champ représente la catégorie de données de diagnostic vers les journaux Azure Monitor, c'est-à-dire AzureBackupReport |
| Ressource |Texte ||Ceci est la ressource pour laquelle les données sont collectées ; affiche le nom du coffre Recovery Services |
| PolicyUniqueId_g |Texte ||ID unique d’identification de la stratégie |
| VaultUniqueId_s |Texte ||ID unique du coffre auquel cette stratégie appartient |
| BackupManagementServerUniqueId_s |Texte |v2 |Champ pour identifier de manière unique le serveur d’administration de sauvegarde de l’élément de sauvegarde est protégé par le biais, le cas échéant        |
| SourceSystem |Texte ||Système source des données actuelles - Azure |
| ResourceId |Texte ||Identificateur de ressource des données collectées. Par exemple, id de ressource du coffre Recovery Services |
| SubscriptionId |Texte ||Identificateur d’abonnement de la ressource (par ex. coffre Recovery Services) pour laquelle les données sont collectées |
| ResourceGroup |Texte ||Groupe de ressources de la ressource (par ex. coffre Recovery Services) pour laquelle les données sont collectées |
| ResourceProvider |Texte ||Fournisseur de ressources pour lequel les données sont collectées. Par exemple, Microsoft.RecoveryServices |
| ResourceType |Texte ||Type de ressource pour lequel les données sont collectées. Par exemple, Coffres |

### <a name="protected-container"></a>Conteneur protégé

Ce tableau fournit les champs de base sur les conteneurs protégés. (Serveur protégé se trouvait dans v1)

| Champ | Type de données | Description |
| --- | --- | --- |
| ProtectedContainerUniqueId_s |Texte | Champ pour identifier de façon unique un conteneur protégé |
| ProtectedContainerOSType_s |Texte |Type de système d’exploitation du conteneur protégé |
| ProtectedContainerOSVersion_s |Texte |Version du système d’exploitation du conteneur protégé |
| AgentVersion_s |Texte |Numéro de version de l’Agent de sauvegarde ou de l’Agent de Protection (en cas de SC DPM et MABS) |
| BackupManagementType_s |Texte |Type de fournisseur exécutant la sauvegarde, par exemple, Machine virtuelle IaaS, Dossier de fichiers |
| EntityState_s |Texte |État actuel de l’objet serveur protégé, par exemple, Actif, Supprimé |
| ProtectedContainerFriendlyName_s |Texte |Nom convivial du serveur protégé |
| ProtectedContainerName_s |Texte |Nom du conteneur protégé |
| ProtectedContainerWorkloadType_s |Texte |Type du conteneur protégé sauvegardé, par exemple, IaaSVMContainer |
| ProtectedContainerLocation_s |Texte |Si le conteneur protégé est située en local ou dans Azure |
| ProtectedContainerType_s |Texte |Si le conteneur protégé est un serveur ou un conteneur |

### <a name="storage"></a>Stockage

Ce tableau fournit plus d’informations sur les champs liés au stockage.

| Champ | Type de données | Description |
| --- | --- | --- |
| CloudStorageInBytes_s |Nombre décimal |Stockage des sauvegardes dans le cloud utilisé par les sauvegardes, calculé en fonction de la dernière valeur |
| ProtectedInstances_s |Nombre décimal |Nombre d’instances protégées utilisées pour calculer le stockage frontal dans la facturation, calculé en fonction de la dernière valeur |
| EventName_s |Texte |Ce champ représente le nom de cet événement, qui est toujours AzureBackupCentralReport |
| SchemaVersion_s |Texte |Ce champ indique la version actuelle du schéma, il est **V2** |
| State_s |Texte |État actuel de l’objet stockage, par exemple, Actif, Supprimé |
| BackupManagementType_s |Texte |Type de fournisseur pour le serveur exécutant la sauvegarde, par exemple, Machine virtuelle IaaS, Dossier de fichiers |
| OperationName |Texte |Ce champ représente le nom de l’opération en cours - Stockage |
| Catégorie |Texte |Ce champ représente la catégorie de données de diagnostic vers les journaux Azure Monitor, c'est-à-dire AzureBackupReport |
| Ressource |Texte |Ceci est la ressource pour laquelle les données sont collectées ; affiche le nom du coffre Recovery Services |
| ProtectedServerUniqueId_s |Texte |ID unique du serveur protégé pour lequel le stockage est calculé |
| VaultUniqueId_s |Texte |ID unique du coffre pour lequel le stockage est calculé |
| SourceSystem |Texte |Système source des données actuelles - Azure |
| ResourceId |Texte |Identificateur de ressource des données collectées. Par exemple, id de ressource du coffre Recovery Services |
| SubscriptionId |Texte |Identificateur d’abonnement de la ressource (par ex. coffre Recovery Services) pour laquelle les données sont collectées |
| ResourceGroup |Texte |Groupe de ressources de la ressource (par ex. coffre Recovery Services) pour laquelle les données sont collectées |
| ResourceProvider |Texte |Fournisseur de ressources pour lequel les données sont collectées. Par exemple, Microsoft.RecoveryServices |
| ResourceType |Texte |Type de ressource pour lequel les données sont collectées. Par exemple, Coffres |

### <a name="storageassociation"></a>StorageAssociation

Ce tableau fournit les champs liés au stockage simples connexion de stockage à d’autres entités.

| Champ | Type de données | Description |
| --- | --- |  --- |
| StorageUniqueId_s |Texte |Id unique utilisé pour identifier l’entité de stockage |
| SchemaVersion_s |Texte |Ce champ indique la version actuelle du schéma, il est **V2** |
| BackupItemUniqueId_s |Texte |Id unique utilisé pour identifier l’élément de sauvegarde associée à l’entité de stockage |
| BackupManagementServerUniqueId_s |Texte |Id unique utilisé pour identifier le serveur de gestion des sauvegardes associée à l’entité de stockage|
| VaultUniqueId_s |Texte |Id unique utilisé pour identifier le coffre associée à l’entité de stockage|
| StorageConsumedInMBs_s |Number|Taille de stockage utilisé par l’élément de sauvegarde correspondant dans le stockage correspondant |
| StorageAllocatedInMBs_s |Number |Taille du stockage alloué par l’élément de sauvegarde correspondant dans le stockage correspondant de type disque|

### <a name="vault"></a>Coffre

Ce tableau fournit plus d’informations sur les champs liés aux coffres.

| Champ | Type de données | Description |
| --- | --- | --- |
| EventName_s |Texte |Ce champ représente le nom de cet événement, qui est toujours AzureBackupCentralReport |
| SchemaVersion_s |Texte |Ce champ indique la version actuelle du schéma, il est **V2** |
| State_s |Texte |État actuel de l’objet coffre, par exemple, Actif, Supprimé |
| OperationName |Texte |Ce champ représente le nom de l’opération en cours - Coffre |
| Catégorie |Texte |Ce champ représente la catégorie de données de diagnostic vers les journaux Azure Monitor, c'est-à-dire AzureBackupReport |
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

### <a name="backup-management-server"></a>Serveur de gestion des sauvegardes

Ce tableau fournit les champs de base sur les serveurs de gestion de sauvegarde.

|Champ  |Type de données  | Description  |
|---------|---------|----------|
|BackupManagmentServerName_s     |Texte         |Nom de la sauvegarde du serveur d’administration        |
|AzureBackupAgentVersion_s     |Texte         |Version de l’Agent de sauvegarde Azure sur le serveur de gestion des sauvegardes          |
|BackupManagmentServerVersion_s     |Texte         |Version du serveur d’administration de sauvegarde|
|BackupManagmentServerOSVersion_s     |Texte            |Version du système d’exploitation du serveur d’administration de sauvegarde|
|BackupManagementServerType_s     |Texte         |Type de la sauvegarde du serveur d’administration, en tant que serveur de sauvegarde AZURE, SC DPM|
|BackupManagmentServerUniqueId_s     |Texte         |Champ pour identifier de façon unique le serveur de gestion de sauvegarde       |

### <a name="preferredworkloadonvolume"></a>PreferredWorkloadOnVolume

Cette table spécifie un Volume est associé avec les charges de travail.

| Champ | Type de données | Description |
| --- | --- | --- |
| StorageUniqueId_s |Texte |Id unique utilisé pour identifier l’entité de stockage |
| BackupItemType_s |Texte |Les charges de travail pour lequel ce volume est le stockage préféré|

### <a name="protectedinstance"></a>ProtectedInstance

Ce tableau fournit les champs associés base instances protégées.

| Champ | Type de données |Versions applicables | Description |
| --- | --- | --- | --- |
| BackupItemUniqueId_s |Texte |v2|Identificateur unique utilisé pour identifier l’élément de sauvegarde pour les machines virtuelles sauvegardées à l’aide de DPM, le serveur de sauvegarde AZURE|
| ProtectedContainerUniqueId_s |Texte |v2|Id unique utilisé pour identifier le conteneur protégé pour tout sauf les machines virtuelles sauvegardées à l’aide de DPM, le serveur de sauvegarde AZURE|
| ProtectedInstanceCount_s |Texte |v2|Nombre d’Instances protégées pour l’élément de sauvegarde associé ou le conteneur protégé sur cette date-heure|

### <a name="recoverypoint"></a>RecoveryPoint

Ce tableau présente la récupération de base pointez les champs correspondants.

| Champ | Type de données | Description |
| --- | --- | --- |
| BackupItemUniqueId_s |Texte |Identificateur unique utilisé pour identifier l’élément de sauvegarde pour les machines virtuelles sauvegardées à l’aide de DPM, le serveur de sauvegarde AZURE|
| OldestRecoveryPointTime_s |Texte |Date de l’heure la plus ancienne du point de récupération pour l’élément de sauvegarde|
| OldestRecoveryPointLocation_s |Texte |Emplacement du point de récupération plus ancien pour l’élément de sauvegarde|
| LatestRecoveryPointTime_s |Texte |Date de l’heure du dernier point de récupération pour l’élément de sauvegarde|
| LatestRecoveryPointLocation_s |Texte |Emplacement du dernier point de récupération pour l’élément de sauvegarde|

## <a name="next-steps"></a>Étapes suivantes

Une fois que vous passez en revue le modèle de données, vous pouvez démarrer [création de requêtes personnalisées](../azure-monitor/learn/tutorial-logs-dashboards.md) dans les journaux d’Azure Monitor pour créer votre propre tableau de bord.
