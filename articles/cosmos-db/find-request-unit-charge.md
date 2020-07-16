---
title: Rechercher des frais d’unités de requête (RU) dans Azure Cosmos DB
description: Découvrez comment trouver les frais d’unités de requête pour toute opération exécutée sur un conteneur Azure Cosmos.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: how-to
ms.date: 09/01/2019
ms.author: thweiss
ms.openlocfilehash: bf109d3f15c9865a8e9ad1d27a1e8d320d172761
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85261832"
---
# <a name="find-the-request-unit-charge-in-azure-cosmos-db"></a>Rechercher les frais d’unités de requête dans Azure Cosmos DB

Cet article présente les différentes façons de rechercher la consommation d’[unités de requête](request-units.md) pour toute opération exécutée sur un conteneur dans Azure Cosmos DB. Il n’est actuellement possible de mesurer cette consommation que sur le portail Azure ou en examinant la réponse renvoyée par Azure Cosmos DB via l’un des kits de développement logiciel (SDK).

## <a name="sql-core-api"></a>API SQL (Core)

Si vous utilisez l’API SQL, vous disposez de plusieurs options pour trouver la consommation d’unités de requête pour une opération sur un conteneur Azure Cosmos.

### <a name="use-the-azure-portal"></a>Utilisation du portail Azure

Actuellement, vous pouvez trouver les frais de requête sur le portail Azure uniquement pour une requête SQL.

1. Connectez-vous au [portail Azure](https://portal.azure.com/).

1. [Créez un compte Azure Cosmos](create-sql-api-dotnet.md#create-account) et remplissez-le avec des données, ou sélectionnez un compte Azure Cosmos existant qui contient déjà des données.

1. Accédez au volet **Explorateur de données**, puis sélectionnez le conteneur sur lequel vous voulez travailler.

1. Sélectionnez **Nouvelle requête SQL**.

1. Entrez une requête valide, puis sélectionnez **Exécuter la requête**.

1. Sélectionnez **Statistiques des requêtes** pour afficher les frais réels de la demande que vous avez exécutée.

:::image type="content" source="./media/find-request-unit-charge/portal-sql-query.png" alt-text="Capture d’écran des frais de demande de requête SQL sur le portail Azure":::

### <a name="use-the-net-sdk"></a>Utiliser le kit de développement logiciel (SDK) .NET

# <a name="net-sdk-v2"></a>[Kit de développement logiciel (SDK) .NET V2](#tab/dotnetv2)

Les objets retournés à partir du [Kit de développement logiciel (SDK) .NET v2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/) exposent une propriété `RequestCharge` :

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

# <a name="net-sdk-v3"></a>[Kit de développement logiciel (SDK) .NET V3](#tab/dotnetv3)

Les objets retournés à partir du [SDK .NET v3](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/) exposent une propriété `RequestCharge` :

[!code-csharp[](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos/tests/Microsoft.Azure.Cosmos.Tests/SampleCodeForDocs/CustomDocsSampleCode.cs?name=GetRequestCharge)]

Pour plus d’informations, consultez [Démarrage rapide : Générer une application web .NET à l’aide d’un compte d’API SQL dans Azure Cosmos DB](create-sql-api-dotnet.md).

---

### <a name="use-the-java-sdk"></a>Utiliser le SDK Java

Les objets retournés à partir du [Kit de développement logiciel Java](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) exposent une méthode `getRequestCharge()` :

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

Pour plus d’informations, consultez [Démarrage rapide : Générer une application Java à l’aide d’un compte d’API SQL Azure Cosmos DB](create-sql-api-java.md).

### <a name="use-the-nodejs-sdk"></a>Utiliser le SDK Node.js

Les objets retournés à partir du [Kit de développement logiciel (SDK) Node.js](https://www.npmjs.com/package/@azure/cosmos) exposent un sous-objet `headers` qui mappe tous les en-têtes retournés par l’API HTTP sous-jacente. Les frais de demande sont disponibles sous la clé `x-ms-request-charge` :

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

Pour plus d’informations, consultez [Démarrage rapide : Générer une application Node.js à l’aide d’un compte d’API SQL Azure Cosmos DB](create-sql-api-nodejs.md). 

### <a name="use-the-python-sdk"></a>Utiliser le SDK Python

L’objet `CosmosClient` retourné à partir du [Kit de développement logiciel (SDK) Python](https://pypi.org/project/azure-cosmos/) expose un dictionnaire `last_response_headers` qui mappe tous les en-têtes retournés par l’API HTTP sous-jacente pour la dernière opération exécutée. Les frais de demande sont disponibles sous la clé `x-ms-request-charge` :

```python
response = client.ReadItem(
    'dbs/database/colls/container/docs/itemId', {'partitionKey': 'partitionKey'})
request_charge = client.last_response_headers['x-ms-request-charge']

response = client.ExecuteStoredProcedure(
    'dbs/database/colls/container/sprocs/storedProcedureId', None, {'partitionKey': 'partitionKey'})
request_charge = client.last_response_headers['x-ms-request-charge']
```

Pour plus d’informations, consultez [Démarrage rapide : Générer une application Python à l’aide d’un compte d’API SQL Azure Cosmos DB](create-sql-api-python.md). 

## <a name="azure-cosmos-db-api-for-mongodb"></a>API Azure Cosmos DB pour MongoDB

Les frais d’unités de requête sont exposés par une [commande de base de données](https://docs.mongodb.com/manual/reference/command/) personnalisée nommée `getLastRequestStatistics`. Cette commande retourne un document contenant le nom de la dernière opération exécutée, ses frais de demande et sa durée. Si vous utilisez l’API Azure Cosmos DB pour MongoDB, vous disposez de plusieurs options pour récupérer les frais d’unités de requête.

### <a name="use-the-azure-portal"></a>Utilisation du portail Azure

Actuellement, vous pouvez trouver les frais de requête sur le portail Azure uniquement pour une requête.

1. Connectez-vous au [portail Azure](https://portal.azure.com/).

1. [Créez un compte Azure Cosmos](create-mongodb-dotnet.md#create-a-database-account) et remplissez-le avec des données, ou sélectionnez un compte existant qui contient déjà des données.

1. Accédez au volet **Explorateur de données**, puis sélectionnez le conteneur sur lequel vous voulez travailler.

1. Sélectionnez **Nouvelle requête**.

1. Entrez une requête valide, puis sélectionnez **Exécuter la requête**.

1. Sélectionnez **Statistiques des requêtes** pour afficher les frais réels de la demande que vous avez exécutée.

:::image type="content" source="./media/find-request-unit-charge/portal-mongodb-query.png" alt-text="Capture d’écran des frais de demande de requête MongoDB sur le portail Azure":::

### <a name="use-the-mongodb-net-driver"></a>Utiliser le pilote .NET MongoDB

Lorsque vous utilisez le [pilote .NET MongoDB officiel](https://docs.mongodb.com/ecosystem/drivers/csharp/), vous pouvez exécuter des commandes en appelant la méthode `RunCommand` sur un objet `IMongoDatabase`. Cette méthode nécessite une implémentation de la classe abstraite `Command<>` :

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

Pour plus d’informations, consultez [Démarrage rapide : Générer une application web .NET à l’aide d’une API Azure Cosmos DB pour MongoDB](create-mongodb-dotnet.md).

### <a name="use-the-mongodb-java-driver"></a>Utiliser le pilote Java MongoDB


Lorsque vous utilisez le [pilote Java MongoDB officiel](https://mongodb.github.io/mongo-java-driver/), vous pouvez exécuter des commandes en appelant la méthode `runCommand` sur un objet `MongoDatabase` :

```java
Document stats = database.runCommand(new Document("getLastRequestStatistics", 1));
Double requestCharge = stats.getDouble("RequestCharge");
```

Pour plus d’informations, consultez [Démarrage rapide : Générer une application web à l’aide d’une API Azure Cosmos DB pour MongoDB et le Kit de développement logiciel (SDK) Java](create-mongodb-java.md).

### <a name="use-the-mongodb-nodejs-driver"></a>Utiliser le pilote Node.js MongoDB

Lorsque vous utilisez le [pilote Node.js MongoDB officiel](https://mongodb.github.io/node-mongodb-native/), vous pouvez exécuter des commandes en appelant la méthode `command` sur un objet `db` :

```javascript
db.command({ getLastRequestStatistics: 1 }, function(err, result) {
    assert.equal(err, null);
    const requestCharge = result['RequestCharge'];
});
```

Pour plus d’informations, consultez [Démarrage rapide : Migrer une application web Node.js MongoDB existante vers Azure Cosmos DB](create-mongodb-nodejs.md).

## <a name="cassandra-api"></a>API Cassandra

Quand vous effectuez des opérations sur l’API Cassandra Azure Cosmos DB, les frais d’unités de requête sont retournés dans la charge utile entrante sous la forme d’un champ nommé `RequestCharge`. Vous disposez de plusieurs options pour récupérer les frais d’unités de requête.

### <a name="use-the-net-sdk"></a>Utiliser le kit de développement logiciel (SDK) .NET

Lorsque vous utilisez le [Kit de développement logiciel (SDK) .NET](https://www.nuget.org/packages/CassandraCSharpDriver/), vous pouvez récupérer la charge utile entrante sous la propriété `Info` d’un objet `RowSet` :

```csharp
RowSet rowSet = session.Execute("SELECT table_name FROM system_schema.tables;");
double requestCharge = BitConverter.ToDouble(rowSet.Info.IncomingPayload["RequestCharge"].Reverse().ToArray(), 0);
```

Pour plus d’informations, consultez [Démarrage rapide : Générer une application Cassandra à l’aide du Kit de développement logiciel (SDK) .NET et d’Azure Cosmos DB](create-cassandra-dotnet.md).

### <a name="use-the-java-sdk"></a>Utiliser le SDK Java

Lorsque vous utilisez le [Kit de développement logiciel (SDK) Java](https://mvnrepository.com/artifact/com.datastax.cassandra/cassandra-driver-core), vous pouvez récupérer la charge utile entrante en appelant la méthode `getExecutionInfo()` sur un objet `ResultSet` :

```java
ResultSet resultSet = session.execute("SELECT table_name FROM system_schema.tables;");
Double requestCharge = resultSet.getExecutionInfo().getIncomingPayload().get("RequestCharge").getDouble();
```

Pour plus d’informations, consultez [Démarrage rapide : Générer une application Cassandra à l’aide du Kit de développement logiciel (SDK) Java et d’Azure Cosmos DB](create-cassandra-java.md).

## <a name="gremlin-api"></a>API Gremlin

Lorsque vous utilisez l’API Gremlin, vous disposez de plusieurs options pour rechercher la consommation d’unités de requête pour une opération sur un conteneur Azure Cosmos. 

### <a name="use-drivers-and-sdk"></a>Utiliser les pilotes et le SDK

Les en-têtes retournés par l’API Gremlin sont mappés aux attributs d’état personnalisés qui sont actuellement exposés par les Kits de développement logiciel (SDK) Java et .NET Gremlin. Les frais de demande sont disponibles sous la clé `x-ms-request-charge`.

### <a name="use-the-net-sdk"></a>Utiliser le kit de développement logiciel (SDK) .NET

Lorsque vous utilisez le [Kit de développement logiciel (SDK) .NET Gremlin](https://www.nuget.org/packages/Gremlin.Net/), les attributs d’état sont disponibles sous la propriété `StatusAttributes` de l’objet `ResultSet<>` :

```csharp
ResultSet<dynamic> results = client.SubmitAsync<dynamic>("g.V().count()").Result;
double requestCharge = (double)results.StatusAttributes["x-ms-request-charge"];
```

Pour plus d’informations, consultez [Démarrage rapide : Générer une application .NET Framework ou Core à l’aide d’un compte d’API Gremlin Azure Cosmos DB](create-graph-dotnet.md).

### <a name="use-the-java-sdk"></a>Utiliser le SDK Java

Lorsque vous utilisez le [Kit de développement logiciel (SDK) Java Gremlin ](https://mvnrepository.com/artifact/org.apache.tinkerpop/gremlin-driver), vous pouvez récupérer les attributs d’état en appelant la méthode `statusAttributes()` sur l’objet `ResultSet` :

```java
ResultSet results = client.submit("g.V().count()");
Double requestCharge = (Double)results.statusAttributes().get().get("x-ms-request-charge");
```

Pour plus d’informations, consultez [Démarrage rapide : Générer une base de données de graphe dans Azure Cosmos DB à l’aide du Kit de développement logiciel (SDK) Java](create-graph-java.md).

## <a name="table-api"></a>API de table

Actuellement, le seul Kit de développement logiciel (SDK) qui retourne les frais d’unités de requête pour les opérations de table est le [Kit de développement .NET Standard](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table). L’objet `TableResult` expose une propriété `RequestCharge` qui est renseignée par le SDK quand vous l’utilisez sur l’API Table Azure Cosmos DB :

```csharp
CloudTable tableReference = client.GetTableReference("table");
TableResult tableResult = tableReference.Execute(TableOperation.Insert(new DynamicTableEntity("partitionKey", "rowKey")));
if (tableResult.RequestCharge.HasValue) // would be false when using Azure Storage Tables
{
    double requestCharge = tableResult.RequestCharge.Value;
}
```

Pour plus d’informations, consultez [Démarrage rapide : Générer une application d’API Table avec le Kit de développement logiciel (SDK) .NET et Azure Cosmos DB](create-table-dotnet.md).

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur l’optimisation de votre consommation d’unités de requête, consultez les articles suivants :

* [Unités de requête et débit dans Azure Cosmos DB](request-units.md)
* [Optimiser le coût du débit approvisionné dans Azure Cosmos DB](optimize-cost-throughput.md)
* [Optimiser le coût de requête dans Azure Cosmos DB](optimize-cost-queries.md)
* [Mettre à l’échelle le débit provisionné au niveau global](scaling-throughput.md)
* [Provisionner le débit sur les conteneurs et les bases de données](set-throughput.md)
* [Approvisionner le débit pour un conteneur](how-to-provision-container-throughput.md)
* [Superviser et déboguer à l’aide de métriques dans Azure Cosmos DB](use-metrics.md)
