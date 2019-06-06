---
title: Obtenir les métriques de performances et de l’exécution de la requête SQL
description: Découvrez comment récupérer les métriques de l’exécution des requêtes SQL et de profil de performances des requêtes SQL des demandes Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 05/17/2019
ms.author: girobins
ms.openlocfilehash: b4017666956d0e01ea19781fb4f1ce2dde15fff5
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66481563"
---
# <a name="get-sql-query-execution-metrics-and-analyze-query-performance-using-net-sdk"></a>Obtenir les métriques de l’exécution des requêtes SQL et d’analyser les performances des requêtes à l’aide du Kit de développement logiciel .NET

Cet article présente comment profiler les performances des requêtes SQL sur Azure Cosmos DB. Ce profil peut être effectuée à l’aide `QueryMetrics` récupéré à partir du SDK .NET et est décrite ici. [QueryMetrics](https://msdn.microsoft.com/library/microsoft.azure.documents.querymetrics.aspx) est un objet fortement typé avec des informations sur l’exécution de requête du serveur principal. Ces mesures sont décrites plus en détail dans le [régler les performances de requête](https://docs.microsoft.com/azure/cosmos-db/documentdb-sql-query-metrics) article.

## <a name="set-the-feedoptions-parameter"></a>Définissez le paramètre FeedOptions

Toutes les surcharges pour [DocumentClient.CreateDocumentQuery](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentquery.aspx) prendre dans facultative [FeedOptions](https://msdn.microsoft.com/library/microsoft.azure.documents.client.feedoptions.aspx) paramètre. Cette option est ce qui permet à l’exécution des requêtes à être réglé et paramétrés. 

Pour collecter les mesures de l’exécution de requêtes Sql, vous devez définir le paramètre [PopulateQueryMetrics](https://msdn.microsoft.com/library/microsoft.azure.documents.client.feedoptions.populatequerymetrics.aspx#P:Microsoft.Azure.Documents.Client.FeedOptions.PopulateQueryMetrics) dans le [FeedOptions](https://msdn.microsoft.com/library/microsoft.azure.documents.client.feedoptions.aspx) à `true`. Paramètre `PopulateQueryMetrics` à true rend afin que le `FeedResponse` contiendra les informations pertinentes `QueryMetrics`. 

## <a name="get-query-metrics-with-asdocumentquery"></a>Obtenir les métriques de requête avec AsDocumentQuery()
L’exemple de code suivant montre comment de récupérer les métriques lors de l’utilisation [AsDocumentQuery()](https://msdn.microsoft.com/library/microsoft.azure.documents.linq.documentqueryable.asdocumentquery.aspx) méthode :

```csharp
// Initialize this DocumentClient and Collection
DocumentClient documentClient = null;
DocumentCollection collection = null;

// Setting PopulateQueryMetrics to true in the FeedOptions
FeedOptions feedOptions = new FeedOptions
{
    PopulateQueryMetrics = true
};

string query = "SELECT TOP 5 * FROM c";
IDocumentQuery<dynamic> documentQuery = documentClient.CreateDocumentQuery(Collection.SelfLink, query, feedOptions).AsDocumentQuery();

while (documentQuery.HasMoreResults)
{
    // Execute one continuation of the query
    FeedResponse<dynamic> feedResponse = await documentQuery.ExecuteNextAsync();
    
    // This dictionary maps the partitionId to the QueryMetrics of that query
    IReadOnlyDictionary<string, QueryMetrics> partitionIdToQueryMetrics = feedResponse.QueryMetrics;
    
    // At this point you have QueryMetrics which you can serialize using .ToString()
    foreach (KeyValuePair<string, QueryMetrics> kvp in partitionIdToQueryMetrics)
    {
        string partitionId = kvp.Key;
        QueryMetrics queryMetrics = kvp.Value;
        
        // Do whatever logging you need
        DoSomeLoggingOfQueryMetrics(query, partitionId, queryMetrics);
    }
}
```
## <a name="aggregating-querymetrics"></a>Agrégation QueryMetrics

Dans la section précédente, notez qu’il y avait plusieurs appels à [ExecuteNextAsync](https://msdn.microsoft.com/library/azure/dn850294.aspx) (méthode). Chaque appel a retourné un `FeedResponse` objet qui a un dictionnaire de `QueryMetrics`; un pour chaque continuation de la requête. L’exemple suivant montre comment agréger ces `QueryMetrics` à l’aide de LINQ :

```csharp
List<QueryMetrics> queryMetricsList = new List<QueryMetrics>();

while (documentQuery.HasMoreResults)
{
    // Execute one continuation of the query
    FeedResponse<dynamic> feedResponse = await documentQuery.ExecuteNextAsync();
    
    // This dictionary maps the partitionId to the QueryMetrics of that query
    IReadOnlyDictionary<string, QueryMetrics> partitionIdToQueryMetrics = feedResponse.QueryMetrics;
    queryMetricsList.AddRange(partitionIdToQueryMetrics.Values);
}

// Aggregate the QueryMetrics using the + operator overload of the QueryMetrics class.
QueryMetrics aggregatedQueryMetrics = queryMetricsList.Aggregate((curr, acc) => curr + acc);
Console.WriteLine(aggregatedQueryMetrics);
```

## <a name="grouping-query-metrics-by-partition-id"></a>Métriques de requête de regroupement par ID de Partition

Vous pouvez regrouper les `QueryMetrics` par l’ID de Partition. Le regroupement par ID de Partition vous permet de voir si une Partition spécifique est à l’origine des problèmes de performances par rapport à d’autres personnes. L’exemple suivant montre comment regrouper `QueryMetrics` avec LINQ :

```csharp
List<KeyValuePair<string, QueryMetrics>> partitionedQueryMetrics = new List<KeyValuePair<string, QueryMetrics>>();
while (documentQuery.HasMoreResults)
{
    // Execute one continuation of the query
    FeedResponse<dynamic> feedResponse = await documentQuery.ExecuteNextAsync();
    
    // This dictionary is maps the partitionId to the QueryMetrics of that query
    IReadOnlyDictionary<string, QueryMetrics> partitionIdToQueryMetrics = feedResponse.QueryMetrics;
    partitionedQueryMetrics.AddRange(partitionIdToQueryMetrics.ToList());
}

// Now we are able to group the query metrics by partitionId
IEnumerable<IGrouping<string, KeyValuePair<string, QueryMetrics>>> groupedByQueryMetrics = partitionedQueryMetrics
    .GroupBy(kvp => kvp.Key);

// If we wanted to we could even aggregate the groupedby QueryMetrics
foreach(IGrouping<string, KeyValuePair<string, QueryMetrics>> grouping in groupedByQueryMetrics)
{
    string partitionId = grouping.Key;
    QueryMetrics aggregatedQueryMetricsForPartition = grouping
        .Select(kvp => kvp.Value)
        .Aggregate((curr, acc) => curr + acc);
    DoSomeLoggingOfQueryMetrics(query, partitionId, aggregatedQueryMetricsForPartition);
}
```

## <a name="linq-on-documentquery"></a>LINQ sur DocumentQuery

Vous pouvez également obtenir le `FeedResponse` à partir d’une requête LINQ à l’aide de la `AsDocumentQuery()` méthode :

```csharp
IDocumentQuery<Document> linqQuery = client.CreateDocumentQuery(collection.SelfLink, feedOptions)
    .Take(1)
    .Where(document => document.Id == "42")
    .OrderBy(document => document.Timestamp)
    .AsDocumentQuery();
FeedResponse<Document> feedResponse = await linqQuery.ExecuteNextAsync<Document>();
IReadOnlyDictionary<string, QueryMetrics> queryMetrics = feedResponse.QueryMetrics;
```

## <a name="expensive-queries"></a>Requêtes coûteuses

Vous pouvez capturer les unités de requête consommées par chaque requête pour examiner les requêtes coûteuses ou des requêtes qui consomment un débit élevé. Vous pouvez obtenir les frais de requête à l’aide de la [RequestCharge](https://msdn.microsoft.com/library/azure/dn948712.aspx) propriété dans `FeedResponse`. Pour en savoir plus sur la façon d’obtenir les frais de requête à l’aide du portail Azure et les différents kits SDK, consultez [trouver les frais d’unités de demande](find-request-unit-charge.md) article.

```csharp
string query = "SELECT * FROM c";
IDocumentQuery<dynamic> documentQuery = documentClient.CreateDocumentQuery(Collection.SelfLink, query, feedOptions).AsDocumentQuery();

while (documentQuery.HasMoreResults)
{
    // Execute one continuation of the query
    FeedResponse<dynamic> feedResponse = await documentQuery.ExecuteNextAsync();
    double requestCharge = feedResponse.RequestCharge
    
    // Log the RequestCharge how ever you want.
    DoSomeLogging(requestCharge);
}
```

## <a name="get-the-query-execution-time"></a>Obtenir la durée d’exécution de requête

Lorsque vous calculez le temps nécessaire pour exécuter une requête côté client, assurez-vous que vous incluez uniquement le temps d’appeler le `ExecuteNextAsync` (méthode) et pas d’autres parties de votre base de code. Uniquement ces appels vous aident à dans le calcul de l’exécution des requêtes durée comme indiqué dans l’exemple suivant :

```csharp
string query = "SELECT * FROM c";
IDocumentQuery<dynamic> documentQuery = documentClient.CreateDocumentQuery(Collection.SelfLink, query, feedOptions).AsDocumentQuery();
Stopwatch queryExecutionTimeEndToEndTotal = new Stopwatch();
while (documentQuery.HasMoreResults)
{
    // Execute one continuation of the query
    queryExecutionTimeEndToEndTotal.Start();
    FeedResponse<dynamic> feedResponse = await documentQuery.ExecuteNextAsync();
    queryExecutionTimeEndToEndTotal.Stop();
}

// Log the elapsed time
DoSomeLogging(queryExecutionTimeEndToEndTotal.Elapsed);
```

## <a name="scan-queries-commonly-slow-and-expensive"></a>Analyse des requêtes (couramment lentes et coûteuses)

Une requête d’analyse fait référence à une requête qui n’a pas été pris en charge par l’index, ce qui, de nombreux documents sont chargés avant de retourner le jeu de résultats.

Voici un exemple d’une requête d’analyse :

```sql
SELECT VALUE c.description 
FROM   c 
WHERE UPPER(c.description) = "BABYFOOD, DESSERT, FRUIT DESSERT, WITHOUT ASCORBIC ACID, JUNIOR"
```

Filtre de cette requête utilise la fonction système supérieur, ce qui n’est pas pris en charge à partir de l’index. L’exécution de cette requête sur une grande collection de produits les métriques de requête suivantes pour la première continuation :

```
QueryMetrics

Retrieved Document Count                 :          60,951
Retrieved Document Size                  :     399,998,938 bytes
Output Document Count                    :               7
Output Document Size                     :             510 bytes
Index Utilization                        :            0.00 %
Total Query Execution Time               :        4,500.34 milliseconds
  Query Preparation Times
    Query Compilation Time               :            0.09 milliseconds
    Logical Plan Build Time              :            0.05 milliseconds
    Physical Plan Build Time             :            0.04 milliseconds
    Query Optimization Time              :            0.01 milliseconds
  Index Lookup Time                      :            0.01 milliseconds
  Document Load Time                     :        4,177.66 milliseconds
  Runtime Execution Times
    Query Engine Times                   :          322.16 milliseconds
    System Function Execution Time       :           85.74 milliseconds
    User-defined Function Execution Time :            0.00 milliseconds
  Document Write Time                    :            0.01 milliseconds
Client Side Metrics
  Retry Count                            :               0
  Request Charge                         :        4,059.95 RUs
```

Notez les valeurs suivantes à partir de la sortie de métriques de requête :

```
Retrieved Document Count                 :          60,951
Retrieved Document Size                  :     399,998,938 bytes
```

Cette requête de charger les 60,951 documents, totalisées 399,998,938 octets. Le chargement de ce nombre d’octets entraîne des frais d’unités de coût ou demande élevée. Il prend également beaucoup de temps pour exécuter la requête, est claire avec la propriété de temps total passé :

```
Total Query Execution Time               :        4,500.34 milliseconds
```

Ce qui signifie que la requête a nécessité 4.5 secondes à s’exécuter (et ce ne était qu’une seule continuation).

Pour optimiser cet exemple de requête, évitez d’utiliser supérieure dans le filtre. Au lieu de cela, lorsque des documents sont créés ou mis à jour, le `c.description` valeurs doivent être insérés dans tous les caractères majuscules. La requête devient alors : 

```sql
SELECT VALUE c.description 
FROM   c 
WHERE c.description = "BABYFOOD, DESSERT, FRUIT DESSERT, WITHOUT ASCORBIC ACID, JUNIOR"
```

Cette requête peut désormais être traités à partir de l’index.

Pour en savoir plus sur le réglage des performances des requêtes, consultez la [régler les performances de requête](https://docs.microsoft.com/azure/cosmos-db/documentdb-sql-query-metrics) article.

## <a id="References"></a>Références

- [Spécification SQL Azure Cosmos DB](https://go.microsoft.com/fwlink/p/?LinkID=510612)
- [ANSI SQL 2011](https://www.iso.org/iso/iso_catalogue/catalogue_tc/catalogue_detail.htm?csnumber=53681)
- [JSON](https://json.org/)
- [LINQ](/previous-versions/dotnet/articles/bb308959(v=msdn.10)) 

## <a name="next-steps"></a>Étapes suivantes

- [Régler les performances de requête](sql-api-query-metrics.md)
- [Vue d’ensemble de l’indexation](index-overview.md)
- [Exemples .NET Azure Cosmos DB](https://github.com/Azure/azure-cosmosdb-dotnet)
