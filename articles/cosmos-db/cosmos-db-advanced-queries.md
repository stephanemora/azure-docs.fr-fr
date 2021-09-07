---
title: Résoudre les problèmes liés aux requêtes de diagnostic avancées pour (API SQL)
titleSuffix: Azure Cosmos DB
description: Découvrez comment interroger les journaux de diagnostic pour résoudre les problèmes de données stockées dans Azure Cosmos DB - API SQL.
author: StefArroyo
services: cosmos-db
ms.service: cosmos-db
ms.topic: how-to
ms.date: 06/12/2021
ms.author: esarroyo
ms.openlocfilehash: 16c4fe809fc411a9d6eec89ed7ceeb04dce317d2
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123097771"
---
# <a name="troubleshoot-issues-with-advanced-diagnostics-queries-for-the-sql-core-api"></a>Résoudre les problèmes liés aux requêtes de diagnostic avancées pour l’API SQL (Core)

[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [API SQL (Core)](cosmos-db-advanced-queries.md)
> * [API MongoDB](mongodb/diagnostic-queries-mongodb.md)
> * [API Cassandra](cassandra/diagnostic-queries-cassandra.md)
> * [API Gremlin](graph/diagnostic-queries-gremlin.md)
>

Dans cet article, nous allons voir comment écrire des requêtes plus avancées pour vous aider à résoudre les problèmes de votre compte Azure Cosmos DB en utilisant les journaux de diagnostic envoyés aux tables **Diagnostics Azure (hérité)** et **spécifiques aux ressources**.

Pour les tables Diagnostics Azure, toutes les données sont écrites dans une seule table. Les utilisateurs spécifient la catégorie qu’ils veulent interroger. Si vous souhaitez afficher la requête de texte intégral de votre requête, consultez [Superviser les données Azure Cosmos DB en utilisant des paramètres de diagnostic dans Azure](cosmosdb-monitor-resource-logs.md#full-text-query) pour savoir comment activer cette fonctionnalité.

Pour les [tables spécifiques aux ressources](cosmosdb-monitor-resource-logs.md#create-setting-portal), les données sont écrites dans des tables individuelles pour chaque catégorie de la ressource. Nous recommandons ce mode parce qu’il :

- Facilite beaucoup l’utilisation des données. 
- Offre une meilleure découvrabilité des schémas.
- Améliore les performances au niveau de la latence d’ingestion et des délais de requêtes.

## <a name="common-queries"></a>Requêtes courantes
Les requêtes courantes sont affichées dans les tables spécifiques aux ressources et Diagnostics Azure.

### <a name="top-n10-queries-ordered-by-request-unit-ru-consumption-in-a-specific-time-frame"></a>Top N(10) des requêtes classées par consommation d’unités de requête (RU) dans une fenêtre de temps spécifique

# <a name="resource-specific"></a>[Spécifique à la ressource](#tab/resource-specific)

   ```Kusto
   let topRequestsByRUcharge = CDBDataPlaneRequests 
   | where TimeGenerated > ago(24h)
   | project  RequestCharge , TimeGenerated, ActivityId;
   CDBQueryRuntimeStatistics
   | project QueryText, ActivityId, DatabaseName , CollectionName
   | join kind=inner topRequestsByRUcharge on ActivityId
   | project DatabaseName , CollectionName , QueryText , RequestCharge, TimeGenerated
   | order by RequestCharge desc
   | take 10
   ```
# <a name="azure-diagnostics"></a>[Diagnostics Azure](#tab/azure-diagnostics)

   ```Kusto
   let topRequestsByRUcharge = AzureDiagnostics
   | where Category == "DataPlaneRequests" and TimeGenerated > ago(24h)
   | project  requestCharge_s , TimeGenerated, activityId_g;
   AzureDiagnostics
   | where Category == "QueryRuntimeStatistics"
   | project querytext_s, activityId_g, databasename_s , collectionname_s
   | join kind=inner topRequestsByRUcharge on activityId_g
   | project databasename_s , collectionname_s , querytext_s , requestCharge_s, TimeGenerated
   | order by requestCharge_s desc
   | take 10
   ```    
---

### <a name="requests-throttled-statuscode--429-in-a-specific-time-window"></a>Requêtes limitées (statusCode = 429) dans une fenêtre de temps spécifique 

# <a name="resource-specific"></a>[Spécifique à la ressource](#tab/resource-specific)

   ```Kusto
   let throttledRequests = CDBDataPlaneRequests
   | where StatusCode == "429"
   | project  OperationName , TimeGenerated, ActivityId;
   CDBQueryRuntimeStatistics
   | project QueryText, ActivityId, DatabaseName , CollectionName
   | join kind=inner throttledRequests on ActivityId
   | project DatabaseName , CollectionName , QueryText , OperationName, TimeGenerated
   ```
# <a name="azure-diagnostics"></a>[Diagnostics Azure](#tab/azure-diagnostics)

   ```Kusto
   let throttledRequests = AzureDiagnostics
   | where Category == "DataPlaneRequests" and statusCode_s == "429"
   | project  OperationName , TimeGenerated, activityId_g;
   AzureDiagnostics
   | where Category == "QueryRuntimeStatistics"
   | project querytext_s, activityId_g, databasename_s , collectionname_s
   | join kind=inner throttledRequests on activityId_g
   | project databasename_s , collectionname_s , querytext_s , OperationName, TimeGenerated
   ```    
---

### <a name="queries-with-the-largest-response-lengths-payload-size-of-the-server-response"></a>Requêtes avec les réponses les plus longues (taille du contenu de la réponse du serveur)

# <a name="resource-specific"></a>[Spécifique à la ressource](#tab/resource-specific)

   ```Kusto
   let operationsbyUserAgent = CDBDataPlaneRequests
   | project OperationName, DurationMs, RequestCharge, ResponseLength, ActivityId;
   CDBQueryRuntimeStatistics
   //specify collection and database
   //| where DatabaseName == "DBNAME" and CollectionName == "COLLECTIONNAME"
   | join kind=inner operationsbyUserAgent on ActivityId
   | summarize max(ResponseLength) by QueryText
   | order by max_ResponseLength desc
   ```
# <a name="azure-diagnostics"></a>[Diagnostics Azure](#tab/azure-diagnostics)

   ```Kusto
   let operationsbyUserAgent = AzureDiagnostics
   | where Category=="DataPlaneRequests"
   | project OperationName, duration_s, requestCharge_s, responseLength_s, activityId_g;
   AzureDiagnostics
   | where Category == "QueryRuntimeStatistics"
   //specify collection and database
   //| where databasename_s == "DBNAME" and collectioname_s == "COLLECTIONNAME"
   | join kind=inner operationsbyUserAgent on activityId_g
   | summarize max(responseLength_s1) by querytext_s
   | order by max_responseLength_s1 desc
   ```    
---

### <a name="ru-consumption-by-physical-partition-across-all-replicas-in-the-replica-set"></a>Consommation de RU par partition physique (sur tous les réplicas du jeu de réplicas)

# <a name="resource-specific"></a>[Spécifique à la ressource](#tab/resource-specific)

   ```Kusto
   CDBPartitionKeyRUConsumption
   | where TimeGenerated >= now(-1d)
   //specify collection and database
   //| where DatabaseName == "DBNAME" and CollectionName == "COLLECTIONNAME"
   // filter by operation type
   //| where operationType_s == 'Create'
   | summarize sum(todouble(RequestCharge)) by toint(PartitionKeyRangeId)
   | render columnchart
   ```
# <a name="azure-diagnostics"></a>[Diagnostics Azure](#tab/azure-diagnostics)

   ```Kusto
   AzureDiagnostics
   | where TimeGenerated >= now(-1d)
   | where Category == 'PartitionKeyRUConsumption'
   //specify collection and database
   //| where databasename_s == "DBNAME" and collectioname_s == "COLLECTIONNAME"
   // filter by operation type
   //| where operationType_s == 'Create'
   | summarize sum(todouble(requestCharge_s)) by toint(partitionKeyRangeId_s)
   | render columnchart  
   ```    
---

### <a name="ru-consumption-by-logical-partition-across-all-replicas-in-the-replica-set"></a>Consommation de RU par partition logique (sur tous les réplicas du jeu de réplicas)

# <a name="resource-specific"></a>[Spécifique à la ressource](#tab/resource-specific)

   ```Kusto
   CDBPartitionKeyRUConsumption
   | where TimeGenerated >= now(-1d)
   //specify collection and database
   //| where DatabaseName == "DBNAME" and CollectionName == "COLLECTIONNAME"
   // filter by operation type
   //| where operationType_s == 'Create'
   | summarize sum(todouble(RequestCharge)) by PartitionKey, PartitionKeyRangeId
   | render columnchart  
   ```
# <a name="azure-diagnostics"></a>[Diagnostics Azure](#tab/azure-diagnostics)

   ```Kusto
   AzureDiagnostics
   | where TimeGenerated >= now(-1d)
   | where Category == 'PartitionKeyRUConsumption'
   //specify collection and database
   //| where databasename_s == "DBNAME" and collectioname_s == "COLLECTIONNAME"
   // filter by operation type
   //| where operationType_s == 'Create'
   | summarize sum(todouble(requestCharge_s)) by partitionKey_s, partitionKeyRangeId_s
   | render columnchart  
   ```
---

## <a name="next-steps"></a>Étapes suivantes
* Pour plus d’informations sur la façon de créer des paramètres de diagnostic pour Cosmos DB, consultez [Créer des paramètres de diagnostic](cosmosdb-monitor-resource-logs.md).
* Pour plus d’informations sur la création d’un paramètre de diagnostic à l’aide du portail Azure, de l’interface Azure CLI ou de PowerShell, consultez [Créer des paramètres de diagnostic pour collecter des journaux et métriques de plateforme dans Azure](../azure-monitor/essentials/diagnostic-settings.md).
