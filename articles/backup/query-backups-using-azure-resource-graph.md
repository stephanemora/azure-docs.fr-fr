---
title: Interroger vos sauvegardes à l’aide d’Azure Resource Graph (ARG)
description: En savoir plus sur l’interrogation des informations sur la sauvegarde de vos ressources Azure à l’aide du Groupe de ressources Azure (ARG).
ms.topic: conceptual
ms.date: 05/21/2021
ms.openlocfilehash: c464e95b9b6b45a49655b8f5f4659a262f9097ee
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/26/2021
ms.locfileid: "110482731"
---
# <a name="query-your-backups-using-azure-resource-graph-arg"></a>Interroger vos sauvegardes à l’aide d’Azure Resource Graph (ARG)

Vous pouvez demander des informations sur la sauvegarde de vos ressources Azure sans coût supplémentaire à l’aide [d’Azure Resource Graph (ARG)](../governance/resource-graph/overview.md). ARG est un service Azure conçu pour étendre la gestion des ressources Azure. Il vise à fournir une exploration efficace des ressources avec la possibilité d’interroger à grande échelle sur un ensemble d’abonnements donné. Voici les principaux avantages de l’utilisation du service ARG pour interroger vos métadonnées de sauvegarde :

- Possibilité d’interroger les ressources à grande échelle avec des propriétés de filtrage, regroupement et tri complexes par ressource.
- Possibilité d’obtenir des informations en temps réel sur vos sauvegardes, notamment sur les travaux de sauvegarde en cours.
- Possibilité de joindre des données liées à la sauvegarde contenant des informations utiles aux ressources Azure associées, comme les machines virtuelles Azure et les comptes de stockage.

## <a name="getting-started"></a>Prise en main

Pour commencer à interroger vos sauvegardes à l’aide du service ARG, procédez comme suit :

1. Recherchez _l’Explorateur Resource Graph_ dans le portail Azure. Sélectionnez-le pour être redirigé vers l’éditeur de requête ARG.
    
    ![Rechercher dans le groupe de ressources Azure](./media/query-backups-using-azure-resource-graph/search-resource-graph-explorer.png)

    Le volet gauche affiche toutes les tables (et leurs schémas associés) pouvant être interrogés.
    
    - La table **RecoveryServicesResources** contient la plupart des enregistrements liés à la sauvegarde, comme les détails de la tâche et de l’instance de sauvegarde.  Et ainsi de suite.
    - La table **Ressources** contient des informations sur toutes les ressources Azure de niveau supérieur, comme les coffres Recovery Services, les machines virtuelles Azure, les comptes de stockage, etc.

    ![Tables et schémas associés pouvant être interrogés](./media/query-backups-using-azure-resource-graph/tables-and-associated-schemas.png)

1. Pour explorer les données de l’une de ces tables, écrivez des requêtes **Kusto** dans l’éditeur de requête et cliquez sur **Exécuter la requête**.

    Vous pouvez télécharger la sortie de ces requêtes au format CSV à partir de **l’Explorateur Resource Graph**. Vous pouvez également utiliser ces requêtes dans le service personnalisé Automation à l’aide de n’importe quel client Automation pris en charge par ARG, comme [PowerShell](../governance/resource-graph/first-query-powershell.md), l’interface [CLI](../governance/resource-graph/first-query-azurecli.md) ou le kit [SDK](../governance/resource-graph/first-query-python.md). Vous pouvez également créer des [classeurs personnalisés](../azure-monitor/visualize/workbooks-overview.md) dans le portail Azure à l’aide d’ARG comme source de données.

>[!NOTE] 
>- Les travaux de sauvegarde/restauration des 14 derniers jours sont disponibles et peuvent être interrogés dans ARG. Si vous souhaitez interroger des enregistrements historiques, nous vous recommandons d’utiliser les **Journaux Azure Monitor**.
>- ARG vous permet d’interroger les ressources pour lesquelles vous disposez des droits RBAC appropriés.

## <a name="sample-queries"></a>Exemples de requêtes

Voici quelques exemples de requêtes ARG sur vos données de sauvegarde que vous pouvez utiliser dans les automatisations et tableaux de bord personnalisés.

### <a name="list-all-azure-vms-that-have-been-configured-for-backup"></a>Répertorier toutes les machines virtuelles Azure configurées pour la sauvegarde

```dotnetcli
RecoveryServicesResources 
| where type in~ ('Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems')
| extend vaultName = case(type =~ 'microsoft.dataprotection/backupVaults/backupInstances',split(split(id, '/Microsoft.DataProtection/backupVaults/')[1],'/')[0],type =~ 'Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems',split(split(id, '/Microsoft.RecoveryServices/vaults/')[1],'/')[0],'--')
| extend dataSourceType = case(type=~'Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems',properties.backupManagementType,type =~ 'microsoft.dataprotection/backupVaults/backupInstances',properties.dataSourceSetInfo.datasourceType,'--')
| extend friendlyName = properties.friendlyName
| extend dsResourceGroup = split(split(properties.dataSourceInfo.resourceID, '/resourceGroups/')[1],'/')[0]
| extend dsSubscription = split(split(properties.dataSourceInfo.resourceID, '/subscriptions/')[1],'/')[0]
| extend lastRestorePoint = properties.lastRecoveryPoint
| extend primaryLocation = properties.dataSourceInfo.resourceLocation
| extend policyName = case(type =~ 'Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems',properties.policyName, type =~ 'microsoft.dataprotection/backupVaults/backupInstances', properties.policyInfo.name, '--')
| extend protectionState = properties.currentProtectionState
| where protectionState in~ ('ConfiguringProtection','ProtectionConfigured','ConfiguringProtectionFailed','ProtectionStopped','SoftDeleted','ProtectionError')
| where (dsSubscription in~ ('00000000-0000-0000-0000-000000000000')) and (dataSourceType in~ ('AzureIaasVM')) //add the relevant subscription ids you wish to query to this line

```

### <a name="list-all-backup-jobs-on-azure-databases-for-postgresql-servers-in-the-last-one-week"></a>Répertorier toutes les tâches de sauvegarde sur les serveurs Azure Database pour PostgreSQL au cours de la dernière semaine

```dotnetcli
RecoveryServicesResources 
| where type in~ ('Microsoft.DataProtection/backupVaults/backupJobs')
| extend vaultName = case(type =~ 'microsoft.dataprotection/backupVaults/backupJobs',properties.vaultName,type =~ 'Microsoft.RecoveryServices/vaults/backupJobs',split(split(id, '/Microsoft.RecoveryServices/vaults/')[1],'/')[0],'--')
| extend friendlyName = case(type =~ 'microsoft.dataprotection/backupVaults/backupJobs',strcat(properties.dataSourceSetName , '/', properties.dataSourceName),type =~ 'Microsoft.RecoveryServices/vaults/backupJobs', properties.entityFriendlyName, '--')
| extend dataSourceType = case(type =~ 'Microsoft.RecoveryServices/vaults/backupJobs',properties.backupManagementType,type =~ 'microsoft.dataprotection/backupVaults/backupJobs',properties.dataSourceType,'--')
| extend backupInstanceName = properties.backupInstanceId
| extend dsResourceGroup = split(split(properties.dataSourceId, '/resourceGroups/')[1],'/')[0]| extend dsSubscription = split(split(properties.dataSourceId, '/subscriptions/')[1],'/')[0]
| extend status = properties.status
| extend dataSourceId = properties.dataSourceId
| extend primaryLocation = properties.dataSourceLocation
| extend jobStatus = case (properties.status == 'Completed' or properties.status == 'CompletedWithWarnings','Succeeded',properties.status == 'Failed','Failed',properties.status == 'InProgress', 'Started', properties.status), operation = case(type =~ 'microsoft.dataprotection/backupVaults/backupJobs' and tolower(properties.operationCategory) =~ 'backup' and properties.isUserTriggered == 'true',strcat('adhoc',properties.operationCategory),type =~ 'microsoft.dataprotection/backupVaults/backupJobs', tolower(properties.operationCategory), type =~ 'Microsoft.RecoveryServices/vaults/backupJobs' and tolower(properties.operation) =~ 'backup' and properties.isUserTriggered == 'true',strcat('adhoc',properties.operation),type =~ 'Microsoft.RecoveryServices/vaults/backupJobs',tolower(properties.operation), '--'),startTime = todatetime(properties.startTime),endTime = properties.endTime, duration = properties.duration
| project id, name, friendlyName, resourceGroup, vaultName, dataSourceType, operation, jobStatus, startTime, duration, backupInstanceName, dsResourceGroup, dsSubscription, status, primaryLocation, dataSourceId
| where  (dsSubscription in~ ('00000000-0000-0000-0000-000000000000')) and (dataSourceType in~ ('Microsoft.DBforPostgreSQL/servers/databases')) and (status in~ ('Started','InProgress','Succeeded','Completed','Failed','CompletedWithWarnings')) and (operation in~ ('adhocBackup','backup')) //add the relevant subscription ids you wish to query to this line
| where (startTime >= ago(7d))

```

### <a name="list-all-azure-vms-that-have-not-been-configured-for-backup"></a>Répertorier toutes les machines virtuelles Azure qui ne sont pas configurées pour la sauvegarde

```dotnetcli
Resources
| where type in~ ('microsoft.compute/virtualmachines','microsoft.classiccompute/virtualmachines') 
| extend resourceId=tolower(id) 
| join kind = leftouter ( RecoveryServicesResources
| where type == "microsoft.recoveryservices/vaults/backupfabrics/protectioncontainers/protecteditems"
| where properties.backupManagementType == "AzureIaasVM"
| project resourceId = tolower(tostring(properties.sourceResourceId)), backupItemid = id, isBackedUp = isnotempty(id) ) on resourceId 
| extend isProtected = isnotempty(backupItemid)
| where (isProtected == (0))
| project id,name,resourceGroup,location,tags

```

### <a name="list-all-backup-policies-used-for-azure-databases-for-postgresql-servers"></a>Répertorier toutes les stratégies de sauvegarde utilisées pour les serveurs Azure Database pour PostgreSQL

```dotnetcli
RecoveryServicesResources 
| where type in~ ('Microsoft.DataProtection/BackupVaults/backupPolicies')
| extend vaultName = case(type =~ 'microsoft.dataprotection/backupVaults/backupPolicies', split(split(id, '/Microsoft.DataProtection/backupVaults/')[1],'/')[0],type =~ 'microsoft.recoveryservices/vaults/backupPolicies', split(split(id, '/Microsoft.RecoveryServices/vaults/')[1],'/')[0],'--')
| extend datasourceType = case(type =~ 'Microsoft.RecoveryServices/vaults/backupPolicies', properties.backupManagementType,type =~ 'microsoft.dataprotection/backupVaults/backupPolicies',properties.datasourceTypes[0],'--')
| project id,name,vaultName,resourceGroup,properties,datasourceType
| where (datasourceType in~ ('Microsoft.DBforPostgreSQL/servers/databases'))

```

## <a name="next-steps"></a>Étapes suivantes

[En savoir plus sur Azure Resource Graph](../governance/resource-graph/overview.md)
