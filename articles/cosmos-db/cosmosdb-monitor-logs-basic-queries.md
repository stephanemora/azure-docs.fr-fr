---
title: " Résoudre les problèmes liés aux requêtes de diagnostic"
titleSuffix: Azure Cosmos DB
description: Découvrez la procédure d’interrogation des journaux de diagnostic pour résoudre les problèmes liés aux données stockées dans Azure Cosmos DB
author: StefArroyo
services: cosmos-db
ms.service: cosmos-db
ms.topic: how-to
ms.date: 05/12/2021
ms.author: esarroyo
ms.openlocfilehash: 51ab68d77e6d5f7e69701b7bc36eaf58f51bf48d
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111966362"
---
# <a name="troubleshoot-issues-with-diagnostics-queries"></a> Résoudre les problèmes liés aux requêtes de diagnostic

Dans cet article, nous allons aborder l’écriture de requêtes simples pour vous aider à résoudre les problèmes liés à votre compte Azure Cosmos DB à l’aide des journaux de diagnostic envoyés à **AzureDiagnostics (hérité)** et aux tables **spécifiques aux ressources (préversion)** .

Pour les tables Diagnostics Azure, toutes les données sont écrites dans une seule table et les utilisateurs doivent spécifier la catégorie à interroger.

Pour les tables spécifiques aux ressources (actuellement en préversion pour l’API SQL), les données sont écrites dans des tables individuelles pour chaque catégorie de la ressource. Nous recommandons ce mode, car il simplifie considérablement l’utilisation des données, fournit une meilleure détectabilité des schémas et améliore les performances de la latence et des délais d’interrogation de l’ingestion.

## <a name="azurediagnostics-queries"></a><a id="azure-diagnostics-queries"></a> Requêtes AzureDiagnostics

- Comment rechercher les opérations dont l’exécution prend plus de 3 millisecondes :

   ```Kusto
   AzureDiagnostics 
   | where toint(duration_s) > 3 and ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
   | summarize count() by clientIpAddress_s, TimeGenerated
   ```

- Comment rechercher l’agent utilisateur qui exécute les opérations :

   ```Kusto
   AzureDiagnostics 
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
   | summarize count() by OperationName, userAgent_s
   ```

- Comment rechercher les opérations durables :

   ```Kusto
   AzureDiagnostics 
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
   | project TimeGenerated , duration_s 
   | summarize count() by bin(TimeGenerated, 5s)
   | render timechart
   ```
    
- Comment obtenir des statistiques de clé de partition afin d’évaluer le décalage entre les 3 premières partitions pour un compte de base de données :

   ```Kusto
   AzureDiagnostics 
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="PartitionKeyStatistics" 
   | project SubscriptionId, regionName_s, databaseName_s, collectionName_s, partitionKey_s, sizeKb_d, ResourceId 
   ```

- Comment obtenir les frais de requête pour les requêtes coûteuses ?

   ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" and todouble(requestCharge_s) > 10.0
   | project activityId_g, requestCharge_s
   | join kind= inner (
   AzureDiagnostics
   | where ResourceProvider =="MICROSOFT.DOCUMENTDB" and Category == "QueryRuntimeStatistics"
   | project activityId_g, querytext_s
   ) on $left.activityId_g == $right.activityId_g
   | order by requestCharge_s desc
   | limit 100
   ```

- Comment trouver les opérations qui prennent en charge la plupart des RU/s ?

    ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
   | where TimeGenerated >= ago(2h) 
   | summarize max(responseLength_s), max(requestLength_s), max(requestCharge_s), count = count() by OperationName, requestResourceType_s, userAgent_s, collectionRid_s, bin(TimeGenerated, 1h)
   ```

- Comment obtenir toutes les requêtes qui consomment plus de 100 RU/s jointes à des données de **DataPlaneRequests** et **QueryRunTimeStatistics**.

   ```kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" and todouble(requestCharge_s) > 100.0
   | project activityId_g, requestCharge_s
   | join kind= inner (
           AzureDiagnostics
           | where ResourceProvider =="MICROSOFT.DOCUMENTDB" and Category == "QueryRuntimeStatistics"
           | project activityId_g, querytext_s
   ) on $left.activityId_g == $right.activityId_g
   | order by requestCharge_s desc
   | limit 100
   ```

- Comment obtenir les frais de requête et la durée d’exécution d’une requête ?

   ```kusto
   AzureDiagnostics
   | where TimeGenerated >= ago(24hr)
   | where Category == "QueryRuntimeStatistics"
   | join (
   AzureDiagnostics
   | where TimeGenerated >= ago(24hr)
   | where Category == "DataPlaneRequests"
   ) on $left.activityId_g == $right.activityId_g
   | project databasename_s, collectionname_s, OperationName1 , querytext_s,requestCharge_s1, duration_s1, bin(TimeGenerated, 1min)
   ```

- Comment obtenir la distribution pour différentes opérations ?

   ```kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
   | where TimeGenerated >= ago(2h) 
   | summarize count = count()  by OperationName, requestResourceType_s, bin(TimeGenerated, 1h) 
   ```

- Quel est le débit maximal qui a été consommé par une partition ?
   
   ```kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
   | where TimeGenerated >= ago(2h) 
   | summarize max(requestCharge_s) by bin(TimeGenerated, 1h), partitionId_g
   ```

- Comment obtenir les informations sur les clés de partition consommables par seconde ?

   ```kusto
   AzureDiagnostics 
   | where ResourceProvider == "MICROSOFT.DOCUMENTDB" and Category == "PartitionKeyRUConsumption" 
   | summarize total = sum(todouble(requestCharge_s)) by databaseName_s, collectionName_s, partitionKey_s, TimeGenerated 
   | order by TimeGenerated asc 
   ```

- Comment obtenir les frais de requête pour une clé de partition spécifique

   ```kusto
   AzureDiagnostics 
   | where ResourceProvider == "MICROSOFT.DOCUMENTDB" and Category == "PartitionKeyRUConsumption" 
   | where parse_json(partitionKey_s)[0] == "2" 
   ```

- Comment obtenir les principales clés de partition avec la plupart des RU/s consommées au cours d’une période donnée ?

   ```kusto
   AzureDiagnostics 
   | where ResourceProvider == "MICROSOFT.DOCUMENTDB" and Category == "PartitionKeyRUConsumption" 
   | where TimeGenerated >= datetime("11/26/2019, 11:20:00.000 PM") and TimeGenerated <= datetime("11/26/2019, 11:30:00.000 PM") 
   | summarize total = sum(todouble(requestCharge_s)) by databaseName_s, collectionName_s, partitionKey_s 
   | order by total desc
   ```

- Comment obtenir les journaux des clés de partition dont la taille de stockage est supérieure à 8 Go ?

   ```kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="PartitionKeyStatistics"
   | where todouble(sizeKb_d) > 800000
   ```

- Comment obtenir des latences de réplication P99 ou P50 pour les opérations, la charge de requête ou la durée de la réponse ?

   ```kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
   | where TimeGenerated >= ago(2d)
   | summarize percentile(todouble(responseLength_s), 50), percentile(todouble(responseLength_s), 99), max(responseLength_s), percentile(todouble(requestCharge_s), 50), percentile(todouble(requestCharge_s), 99), max(requestCharge_s), percentile(todouble(duration_s), 50), percentile(todouble(duration_s), 99), max(duration_s), count() by OperationName, requestResourceType_s, userAgent_s, collectionRid_s, bin(TimeGenerated, 1h)
   ```
 
- Comment se procurer les journaux ControlPlane ?
 
   N’oubliez pas d’activer l’indicateur comme décrit dans l’article [Désactiver l’accès en écriture aux métadonnées basé sur les clés](audit-control-plane-logs.md#disable-key-based-metadata-write-access) et exécutez les opérations en utilisant Azure PowerShell, l’interface CLI Azure ou Azure Resource Manager.
 
   ```kusto  
   AzureDiagnostics 
   | where Category =="ControlPlaneRequests"
   | summarize by OperationName 
   ```


## <a name="resource-specific-queries"></a><a id="resource-specific-queries"></a> Requêtes spécifiques aux ressources

- Comment rechercher les opérations dont l’exécution prend plus de 3 millisecondes :

    ```kusto
    CDBDataPlaneRequests 
    | where toint(DurationMs) > 3
    | summarize count() by ClientIpAddress, TimeGenerated
    ```

- Comment rechercher l’agent utilisateur qui exécute les opérations :

   ```kusto
    CDBDataPlaneRequests
    | summarize count() by OperationName, UserAgent
   ```

- Comment rechercher les opérations durables :

   ```kusto
    CDBDataPlaneRequests
    | project TimeGenerated , DurationMs 
    | summarize count() by bin(TimeGenerated, 5s)
    | render timechart
   ```
    
- Comment obtenir des statistiques de clé de partition afin d’évaluer le décalage entre les 3 premières partitions pour un compte de base de données :

   ```kusto
    CDBPartitionKeyStatistics
    | project RegionName, DatabaseName, CollectionName, PartitionKey, SizeKb
   ```

- Comment obtenir les frais de requête pour les requêtes coûteuses ?

   ```kusto
    CDBDataPlaneRequests
    | where todouble(RequestCharge) > 10.0
    | project ActivityId, RequestCharge
    | join kind= inner (
    CDBQueryRuntimeStatistics
    | project ActivityId, QueryText
    ) on $left.ActivityId == $right.ActivityId
    | order by RequestCharge desc
    | limit 100
   ```

- Comment trouver les opérations qui prennent en charge la plupart des RU/s ?

    ```kusto
    CDBDataPlaneRequests
    | where TimeGenerated >= ago(2h) 
    | summarize max(ResponseLength), max(RequestLength), max(RequestCharge), count = count() by OperationName, RequestResourceType, UserAgent, CollectionName, bin(TimeGenerated, 1h)
   ```

- Comment obtenir toutes les requêtes qui consomment plus de 100 RU/s jointes à des données de **DataPlaneRequests** et **QueryRunTimeStatistics**.

   ```kusto
    CDBDataPlaneRequests
    | where todouble(RequestCharge) > 100.0
    | project ActivityId, RequestCharge
    | join kind= inner (
    CDBQueryRuntimeStatistics
    | project ActivityId, QueryText
    ) on $left.ActivityId == $right.ActivityId
    | order by RequestCharge desc
    | limit 100
   ```

- Comment obtenir les frais de requête et la durée d’exécution d’une requête ?

   ```kusto
    CDBQueryRuntimeStatistics
    | join kind= inner (
    CDBDataPlaneRequests
    ) on $left.ActivityId == $right.ActivityId
    | project DatabaseName, CollectionName, OperationName , QueryText, RequestCharge, DurationMs, bin(TimeGenerated, 1min)
   ```

- Comment obtenir la distribution pour différentes opérations ?

   ```kusto
    CDBDataPlaneRequests
    | where TimeGenerated >= ago(2h) 
    | summarize count = count()  by OperationName, RequestResourceType, bin(TimeGenerated, 1h)
   ```

- Quel est le débit maximal qui a été consommé par une partition ?

   ```kusto
   CDBDataPlaneRequests
   | where TimeGenerated >= ago(2h) 
   | summarize max(RequestCharge) by bin(TimeGenerated, 1h), PartitionId
   ```

- Comment obtenir les informations sur les clés de partition consommables par seconde ?

   ```kusto
   CDBPartitionKeyRUConsumption 
   | summarize total = sum(todouble(RequestCharge)) by DatabaseName, CollectionName, PartitionKey, TimeGenerated 
   | order by TimeGenerated asc 
   ```

- Comment obtenir les frais de requête pour une clé de partition spécifique ?

   ```kusto
   CDBPartitionKeyRUConsumption  
   | where parse_json(PartitionKey)[0] == "2" 
   ```

- Comment obtenir les principales clés de partition avec la plupart des RU/s consommées au cours d’une période donnée ? 

   ```kusto
   CDBPartitionKeyRUConsumption
   | where TimeGenerated >= datetime("02/12/2021, 11:20:00.000 PM") and TimeGenerated <= datetime("05/12/2021, 11:30:00.000 PM") 
   | summarize total = sum(todouble(RequestCharge)) by DatabaseName, CollectionName, PartitionKey 
   | order by total desc
   ```

- Comment obtenir les journaux des clés de partition dont la taille de stockage est supérieure à 8 Go ?

   ```kusto
   CDBPartitionKeyStatistics
   | where todouble(SizeKb) > 800000
   ``` 

- Comment obtenir des latences de réplication P99 ou P50 pour les opérations, la charge de requête ou la durée de la réponse ?

   ```kusto
   CDBDataPlaneRequests
   | where TimeGenerated >= ago(2d)
   | summarize percentile(todouble(ResponseLength), 50), percentile(todouble(ResponseLength), 99), max(ResponseLength), percentile(todouble(RequestCharge), 50), percentile(todouble(RequestCharge), 99), max(RequestCharge), percentile(todouble(DurationMs), 50), percentile(todouble(DurationMs), 99), max(DurationMs),count() by OperationName, RequestResourceType, UserAgent, CollectionName, bin(TimeGenerated, 1h)
   ```
 
- Comment se procurer les journaux ControlPlane ?
 
   N’oubliez pas d’activer l’indicateur comme décrit dans l’article [Désactiver l’accès en écriture aux métadonnées basé sur les clés](audit-control-plane-logs.md#disable-key-based-metadata-write-access) et exécutez les opérations en utilisant Azure PowerShell, l’interface CLI Azure ou Azure Resource Manager.
 
   ```kusto  
   CDBControlPlaneRequests
   | summarize by OperationName 
   ```

## <a name="next-steps"></a>Étapes suivantes
* Pour plus d’informations sur la procédure de création de paramètres de diagnostic pour Cosmos DB, consultez l’article [Création de paramètres de diagnostic](cosmosdb-monitor-resource-logs.md).

* Pour plus d’informations sur la procédure de création d’un paramètre de diagnostic à l’aide du Portail Azure, de l’interface CLI ou de PowerShell, consultez l’article [Créer un paramètre de diagnostic pour collecter les journaux et les indicateurs de performance de la plateforme dans Azure](../azure-monitor/essentials/diagnostic-settings.md).