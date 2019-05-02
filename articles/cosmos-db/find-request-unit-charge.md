---
title: Rechercher des frais d’unités de requête (RU) dans Azure Cosmos DB
description: Découvrir comment trouver les frais d’unités de requête pour toute opération exécutée sur un conteneur Azure Cosmos
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: sample
ms.date: 04/15/2019
ms.author: thweiss
ms.openlocfilehash: 7afa815f81e2a61db8ac83623baafb97cb986b2c
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64925348"
---
# <a name="find-the-request-unit-ru-charge-in-azure-cosmos-db"></a>Rechercher des frais d’unités de requête (RU) dans Azure Cosmos DB

Cet article présente les différentes façons de rechercher la consommation d’[unités de requête](request-units.md) pour toute opération exécutée sur un conteneur Azure Cosmos. Il est actuellement possible de mesurer cette consommation à l’aide du portail Azure ou en examinant la réponse envoyée à partir d’Azure Cosmos DB par l’intermédiaire de l’un des kits SDK.

## <a name="sql-core-api"></a>API SQL (Core)

### <a name="use-the-azure-portal"></a>Utilisation du portail Azure

Le portail Azure vous permet actuellement de trouver les frais de demande pour une requête SQL uniquement.

1. Connectez-vous au [Portail Azure](https://portal.azure.com/).

1. [Créez un compte Azure Cosmos](create-sql-api-dotnet.md#create-account) et remplissez-le avec des données, ou sélectionnez un compte Azure Cosmos existant qui contient déjà des données.

1. Ouvrez le volet **Explorateur de données**, puis sélectionnez le conteneur avec lequel vous voulez travailler.

1. Cliquez sur **Nouvelle requête SQL**.

1. Entrez une requête valide, puis cliquez sur **Exécuter la requête**.

1. Cliquez sur **Statistiques des requêtes** pour afficher les frais réels de la demande que vous venez d’exécuter.

![Capture d’écran des frais de demande de requête SQL sur le portail Azure](./media/find-request-unit-charge/portal-sql-query.png)

### <a name="use-the-net-sdk-v2"></a>Utiliser le SDK .NET V2

Les objets retournés par le [SDK .NET v2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/) (voir [ce Guide de démarrage rapide](create-sql-api-dotnet.md) concernant son utilisation) exposent une propriété `RequestCharge`.

```csharp
ResourceResponse<Document> fetchDocumentResponse = await client.ReadDocumentAsync(
    UriFactory.CreateDocumentUri("database", "container", "itemId"),
    new RequestOptions
    {
        PartitionKey = new PartitionKey("partitionKey")
    });
var requestCharge = fetchDocumentResponse.RequestCharge;

StoredProcedureResponse<string> storedProcedureCallResponse = await client.ExecuteStoredProcedureAsync<string>(
    UriFactory.CreateStoredProcedureUri("database", "container", "storedProcedureId"),
    new RequestOptions
    {
        PartitionKey = new PartitionKey("partitionKey")
    });
requestCharge = storedProcedureCallResponse.RequestCharge;

IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
    UriFactory.CreateDocumentCollectionUri("database", "container"),
    "SELECT * FROM c",
    new FeedOptions
    {
        PartitionKey = new PartitionKey("partitionKey")
    }).AsDocumentQuery();
while (query.HasMoreResults)
{
    FeedResponse<dynamic> queryResponse = await query.ExecuteNextAsync<dynamic>();
    requestCharge = queryResponse.RequestCharge;
}
```

### <a name="use-the-java-sdk"></a>Utiliser le SDK Java

Les objets retournés par le [SDK Java](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) (voir [ce Guide de démarrage rapide](create-sql-api-java.md) concernant son utilisation) exposent une méthode `getRequestCharge()`.

```java
RequestOptions requestOptions = new RequestOptions();
requestOptions.setPartitionKey(new PartitionKey("partitionKey"));

Observable<ResourceResponse<Document>> readDocumentResponse = client.readDocument(String.format("/dbs/%s/colls/%s/docs/%s", "database", "container", "itemId"), requestOptions);
readDocumentResponse.subscribe(result -> {
    double requestCharge = result.getRequestCharge();
});

Observable<StoredProcedureResponse> storedProcedureResponse = client.executeStoredProcedure(String.format("/dbs/%s/colls/%s/sprocs/%s", "database", "container", "storedProcedureId"), requestOptions, null);
storedProcedureResponse.subscribe(result -> {
    double requestCharge = result.getRequestCharge();
});

FeedOptions feedOptions = new FeedOptions();
feedOptions.setPartitionKey(new PartitionKey("partitionKey"));

Observable<FeedResponse<Document>> feedResponse = client
    .queryDocuments(String.format("/dbs/%s/colls/%s", "database", "container"), "SELECT * FROM c", feedOptions);
feedResponse.forEach(result -> {
    double requestCharge = result.getRequestCharge();
});
```

### <a name="use-the-nodejs-sdk"></a>Utiliser le SDK Node.js

Les objets retournés par le [SDK Node.js](https://www.npmjs.com/package/@azure/cosmos) (voir [ce Guide de démarrage rapide](create-sql-api-nodejs.md) concernant son utilisation) exposent un sous-objet `headers` qui mappe tous les en-têtes retournés par l’API HTTP sous-jacente. Les frais de demande sont disponibles sous la clé `x-ms-request-charge`.

```javascript
const item = await client
    .database('database')
    .container('container')
    .item('itemId', 'partitionKey')
    .read();
var requestCharge = item.headers['x-ms-request-charge'];

const storedProcedureResult = await client
    .database('database')
    .container('container')
    .storedProcedure('storedProcedureId')
    .execute({
        partitionKey: 'partitionKey'
    });
requestCharge = storedProcedureResult.headers['x-ms-request-charge'];

const query = client.database('database')
    .container('container')
    .items
    .query('SELECT * FROM c', {
        partitionKey: 'partitionKey'
    });
while (query.hasMoreResults()) {
    var result = await query.executeNext();
    requestCharge = result.headers['x-ms-request-charge'];
}
```

### <a name="use-the-python-sdk"></a>Utiliser le SDK Python

L’objet `CosmosClient` du [SDK Python](https://pypi.org/project/azure-cosmos/) (voir [ce Guide de démarrage rapide](create-sql-api-python.md) concernant son utilisation) expose un dictionnaire `last_response_headers` qui mappe tous les en-têtes retournés par l’API HTTP sous-jacente pour la dernière opération exécutée. Les frais de demande sont disponibles sous la clé `x-ms-request-charge`.

```python
response = client.ReadItem('dbs/database/colls/container/docs/itemId', { 'partitionKey': 'partitionKey' })
request_charge = client.last_response_headers['x-ms-request-charge']

response = client.ExecuteStoredProcedure('dbs/database/colls/container/sprocs/storedProcedureId', None, { 'partitionKey': 'partitionKey' })
request_charge = client.last_response_headers['x-ms-request-charge']
```

## <a name="azure-cosmos-dbs-api-for-mongodb"></a>API d’Azure Cosmos DB pour MongoDB

Les frais d’unités de requête sont exposés par une [commande de base de données](https://docs.mongodb.com/manual/reference/command/) personnalisée nommée `getLastRequestStatistics.` Cette commande retourne un document contenant le nom de la dernière opération exécutée, ses frais de demande et sa durée.

### <a name="use-the-azure-portal"></a>Utilisation du portail Azure

Le portail Azure vous permet actuellement de rechercher les frais de demande pour une requête uniquement.

1. Connectez-vous au [Portail Azure](https://portal.azure.com/).

1. [Créez un compte Azure Cosmos](create-mongodb-dotnet.md#create-a-database-account) et remplissez-le avec des données, ou sélectionnez un compte existant qui contient déjà des données.

1. Ouvrez le volet **Explorateur de données**, puis sélectionnez la collection avec laquelle vous voulez travailler.

1. Cliquez sur **Nouvelle requête**.

1. Entrez une requête valide, puis cliquez sur **Exécuter la requête**.

1. Cliquez sur **Statistiques des requêtes** pour afficher les frais réels de la demande que vous venez d’exécuter.

![Capture d’écran des frais de demande de requête MongoDB sur le portail Azure](./media/find-request-unit-charge/portal-mongodb-query.png)

### <a name="use-the-mongodb-net-driver"></a>Utiliser le pilote .NET MongoDB

Quand vous utilisez le [pilote .NET MongoDB officiel](https://docs.mongodb.com/ecosystem/drivers/csharp/) (voir [ce Guide de démarrage rapide](create-mongodb-dotnet.md) concernant son utilisation), les commandes peuvent être exécutées en appelant la méthode `RunCommand` sur un objet `IMongoDatabase`. Cette méthode nécessite une implémentation de la classe abstraite `Command<>`.

```csharp
class GetLastRequestStatisticsCommand : Command<Dictionary<string, object>>
{
    public override RenderedCommand<Dictionary<string, object>> Render(IBsonSerializerRegistry serializerRegistry)
    {
        return new RenderedCommand<Dictionary<string, object>>(new BsonDocument("getLastRequestStatistics", 1), serializerRegistry.GetSerializer<Dictionary<string, object>>());
    }
}

Dictionary<string, object> stats = database.RunCommand(new GetLastRequestStatisticsCommand());
double requestCharge = (double)stats["RequestCharge"];
```

### <a name="use-the-mongodb-java-driver"></a>Utiliser le pilote Java MongoDB

Quand vous utilisez le [pilote Java MongoDB officiel](https://mongodb.github.io/mongo-java-driver/) (voir [ce Guide de démarrage rapide](create-mongodb-java.md) concernant son utilisation), les commandes peuvent être exécutées en appelant la méthode `runCommand` sur un objet `MongoDatabase`.

```java
Document stats = database.runCommand(new Document("getLastRequestStatistics", 1));
Double requestCharge = stats.getDouble("RequestCharge");
```

### <a name="use-the-mongodb-nodejs-driver"></a>Utiliser le pilote Node.js MongoDB

Quand vous utilisez le [pilote Node.js MongoDB officiel](https://mongodb.github.io/node-mongodb-native/) (voir [ce Guide de démarrage rapide](create-mongodb-nodejs.md) concernant son utilisation), les commandes peuvent être exécutées en appelant la méthode `command` sur un objet `db`.

```javascript
db.command({ getLastRequestStatistics: 1 }, function(err, result) {
    assert.equal(err, null);
    const requestCharge = result['RequestCharge'];
});
```

## <a name="cassandra-api"></a>API Cassandra

Quand vous effectuez des opérations sur l’API Cassandra d’Azure Cosmos DB, les frais d’unités de requête sont retournés dans la charge utile entrante sous la forme d’un champ nommé `RequestCharge`.

### <a name="use-the-net-sdk"></a>Utiliser le kit de développement logiciel (SDK) .NET

Quand vous utilisez le [SDK .NET](https://www.nuget.org/packages/CassandraCSharpDriver/) (voir [ce Guide de démarrage rapide](create-cassandra-dotnet.md) concernant son utilisation), la charge utile entrante peut être récupérée sous la propriété `Info` d’un objet `RowSet`.

```csharp
RowSet rowSet = session.Execute("SELECT table_name FROM system_schema.tables;");
double requestCharge = BitConverter.ToDouble(rowSet.Info.IncomingPayload["RequestCharge"], 0);
```

### <a name="use-the-java-sdk"></a>Utiliser le SDK Java

Quand vous utilisez le [SDK Java](https://mvnrepository.com/artifact/com.datastax.cassandra/cassandra-driver-core) (voir [ce guide de démarrage rapide](create-cassandra-java.md) concernant son utilisation), la charge utile entrante peut être récupérée en appelant la méthode `getExecutionInfo()` sur un objet `ResultSet`.

```java
ResultSet resultSet = session.execute("SELECT table_name FROM system_schema.tables;");
Double requestCharge = resultSet.getExecutionInfo().getIncomingPayload().get("RequestCharge").getDouble();
```

## <a name="gremlin-api"></a>API Gremlin

### <a name="use-drivers-and-sdk"></a>Utiliser les pilotes et le SDK

Les en-têtes retournés par l’API Gremlin sont mappés aux attributs d’état personnalisés, qui sont actuellement exposés par le SDK Java et .NET Gremlin. Les frais de demande sont disponibles sous la clé `x-ms-request-charge`.

### <a name="use-the-net-sdk"></a>Utiliser le kit de développement logiciel (SDK) .NET

Quand vous utilisez le [SDK Gremlin.NET](https://www.nuget.org/packages/Gremlin.Net/) (voir [ce Guide de démarrage rapide](create-graph-dotnet.md) concernant son utilisation), les attributs d’état sont disponibles sous la propriété `StatusAttributes` de l’objet `ResultSet<>`.

```csharp
ResultSet<dynamic> results = client.SubmitAsync<dynamic>("g.V().count()").Result;
double requestCharge = (double)results.StatusAttributes["x-ms-request-charge"];
```

### <a name="use-the-java-sdk"></a>Utiliser le SDK Java

Quand vous utilisez le [SDK Java Gremlin](https://mvnrepository.com/artifact/org.apache.tinkerpop/gremlin-driver) (voir [ce Guide de démarrage rapide](create-graph-java.md) concernant son utilisation), les attributs d’état peuvent être récupérés en appelant la méthode `statusAttributes()` sur l’objet `ResultSet`.

```java
ResultSet results = client.submit("g.V().count()");
Double requestCharge = (Double)results.statusAttributes().get().get("x-ms-request-charge");
```

## <a name="table-api"></a>API de table

Le seul SDK retournant actuellement les frais d’unités de requête pour les opérations de table est le [SDK .NET standard](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) (voir [ce Guide de démarrage rapide](create-table-dotnet.md) concernant son utilisation). L’objet `TableResult` expose une propriété `RequestCharge` qui est remplie par le SDK quand il est utilisé sur l’API Table d’Azure Cosmos DB.

```csharp
CloudTable tableReference = client.GetTableReference("table");
TableResult tableResult = tableReference.Execute(TableOperation.Insert(new DynamicTableEntity("partitionKey", "rowKey")));
if (tableResult.RequestCharge.HasValue) // would be false when using Azure Storage Tables
{
    double requestCharge = tableResult.RequestCharge.Value;
}
```

## <a name="next-steps"></a>Étapes suivantes

Consultez les articles suivants pour en savoir plus sur l’optimisation de votre consommation d’unités de requête :

* [Unités de requête et débit dans Azure Cosmos DB](request-units.md)
* [Optimiser le coût du débit approvisionné dans Azure Cosmos DB](optimize-cost-throughput.md)
* [Optimiser le coût de requête dans Azure Cosmos DB](optimize-cost-queries.md)
* [Mettre à l’échelle le débit provisionné au niveau global](scaling-throughput.md)
* [Provisionner le débit sur les conteneurs et les bases de données](set-throughput.md)
* [Approvisionner le débit d’un conteneur](how-to-provision-container-throughput.md)
