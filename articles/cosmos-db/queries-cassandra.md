---
title: Résoudre les problèmes liés aux requêtes de diagnostic avancées pour l’API Cassandra
titleSuffix: Azure Cosmos DB
description: Découvrez comment interroger les journaux de diagnostic pour résoudre les problèmes de données stockées dans Azure Cosmos DB pour l’API Cassandra
author: StefArroyo
services: cosmos-db
ms.service: cosmos-db
ms.topic: how-to
ms.date: 06/12/2021
ms.author: esarroyo
ms.openlocfilehash: a9bd63d48e4cfd5c197e050b071d2a9dea927ebf
ms.sourcegitcommit: f0168d80eb396ce27032aa02fe9da5a0c10b5af3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2021
ms.locfileid: "112560926"
---
# <a name="troubleshoot-issues-with-advanced-diagnostics-queries-for-cassandra-api"></a>Résoudre les problèmes liés aux requêtes de diagnostic avancées pour l’API Cassandra

[!INCLUDE[appliesto-all-apis-except-table](includes/appliesto-all-apis-except-table.md)]

> [!div class="op_single_selector"]
> * [API SQL (Core)](cosmos-db-advanced-queries.md)
> * [API MongoDB](queries-mongo.md)
> * [API Cassandra](queries-cassandra.md)
> * [API Gremlin](queries-gremlin.md)


Dans cet article, nous allons voir comment écrire des requêtes plus avancées pour vous aider à résoudre les problèmes de votre compte Azure Cosmos DB en utilisant les journaux de diagnostic envoyés aux tables **Diagnostics Azure (hérité)** et **spécifiques aux ressources**.

Pour les tables Diagnostics Azure, toutes les données sont écrites dans une seule table et les utilisateurs doivent spécifier la catégorie à interroger. Si vous souhaitez afficher la requête de texte intégral de votre demande, [suivez cet article](cosmosdb-monitor-resource-logs.md#full-text-query) pour savoir comment activer cette fonctionnalité.

Pour les [tables spécifiques aux ressources](cosmosdb-monitor-resource-logs.md#create-setting-portal), les données sont écrites dans des tables individuelles pour chaque catégorie de la ressource. Nous recommandons ce mode, car il simplifie considérablement l’utilisation des données, fournit une meilleure détectabilité des schémas et améliore les performances de la latence et des délais d’interrogation de l’ingestion.

## <a name="common-queries"></a>Requêtes courantes

- Top N(10) des requêtes consommatrices de RU dans une fenêtre de temps donné

# <a name="resource-specific"></a>[Spécifique à la ressource](#tab/resource-specific)

   ```Kusto
   let topRequestsByRUcharge = CDBDataPlaneRequests 
   | where TimeGenerated > ago(24h)
   | project  RequestCharge , TimeGenerated, ActivityId;
   CDBCassandraRequests
   | project PIICommandText, ActivityId, DatabaseName , CollectionName
   | join kind=inner topRequestsByRUcharge on ActivityId
   | project DatabaseName , CollectionName , PIICommandText , RequestCharge, TimeGenerated
   | order by RequestCharge desc
   | take 10
   ```

# <a name="azure-diagnostics"></a>[Diagnostics Azure](#tab/azure-diagnostics)

   ```Kusto
   let topRequestsByRUcharge = AzureDiagnostics
   | where Category == "DataPlaneRequests" and TimeGenerated > ago(1h)
   | project  requestCharge_s , TimeGenerated, activityId_g;
   AzureDiagnostics
   | where Category == "CassandraRequests"
   | project piiCommandText_s, activityId_g, databasename_s , collectionname_s
   | join kind=inner topRequestsByRUcharge on activityId_g
   | project databasename_s , collectionname_s , piiCommandText_s , requestCharge_s, TimeGenerated
   | order by requestCharge_s desc
   | take 10
   ```    
---

- Requêtes limitées (statusCode = 429) dans une fenêtre de temps donnée 

# <a name="resource-specific"></a>[Spécifique à la ressource](#tab/resource-specific)

   ```Kusto
   let throttledRequests = CDBDataPlaneRequests
   | where StatusCode == "429"
   | project  OperationName , TimeGenerated, ActivityId;
   CDBCassandraRequests
   | project PIICommandText, ActivityId, DatabaseName , CollectionName
   | join kind=inner throttledRequests on ActivityId
   | project DatabaseName , CollectionName , PIICommandText , OperationName, TimeGenerated
   ```

# <a name="azure-diagnostics"></a>[Diagnostics Azure](#tab/azure-diagnostics)

   ```Kusto
   let throttledRequests = AzureDiagnostics
   | where Category == "DataPlaneRequests"
   | where statusCode_s == "429"
   | project  OperationName , TimeGenerated, activityId_g;
   AzureDiagnostics
   | where Category == "CassandraRequests"
   | project piiCommandText_s, activityId_g, databasename_s , collectionname_s
   | join kind=inner throttledRequests on activityId_g
   | project databasename_s , collectionname_s , piiCommandText_s , OperationName, TimeGenerated
   ```    
---

- Requêtes avec réponse longue (taille du contenu de la réponse du serveur)

# <a name="resource-specific"></a>[Spécifique à la ressource](#tab/resource-specific)

   ```Kusto
   let operationsbyUserAgent = CDBDataPlaneRequests
   | project OperationName, DurationMs, RequestCharge, ResponseLength, ActivityId;
   CDBCassandraRequests
   //specify collection and database
   //| where DatabaseName == "DBNAME" and CollectionName == "COLLECTIONNAME"
   | join kind=inner operationsbyUserAgent on ActivityId
   | summarize max(ResponseLength) by PIICommandText
   | order by max_ResponseLength desc
   ```

# <a name="azure-diagnostics"></a>[Diagnostics Azure](#tab/azure-diagnostics)

   ```Kusto
   let operationsbyUserAgent = AzureDiagnostics
   | where Category=="DataPlaneRequests"
   | project OperationName, duration_s, requestCharge_s, responseLength_s, activityId_g;
   AzureDiagnostics
   | where Category == "CassandraRequests"
   //specify collection and database
   //| where databasename_s == "DBNAME" and collectioname_s == "COLLECTIONNAME"
   | join kind=inner operationsbyUserAgent on activityId_g
   | summarize max(responseLength_s1) by piiCommandText_s
   | order by max_responseLength_s1 desc
   ```    
---

- Consommation de RU par partition physique (sur tous les réplicas du jeu de réplicas)

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

- Consommation de RU par partition logique (sur tous les réplicas du jeu de réplicas)

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
* Pour plus d’informations sur la procédure de création de paramètres de diagnostic pour Cosmos DB, consultez l’article [Création de paramètres de diagnostic](cosmosdb-monitor-resource-logs.md).

* Pour plus d’informations sur la procédure de création d’un paramètre de diagnostic à l’aide du Portail Azure, de l’interface CLI ou de PowerShell, consultez l’article [Créer un paramètre de diagnostic pour collecter les journaux et les indicateurs de performance de la plateforme dans Azure](../azure-monitor/essentials/diagnostic-settings.md).