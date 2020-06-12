---
title: Approvisionner le débit d’un conteneur dans Azure Cosmos DB
description: Apprenez à provisionner le débit au niveau du conteneur dans Azure Cosmos DB avec le portail Azure, l’interface CLI, PowerShell et différents kits SDK.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: mjbrown
ms.openlocfilehash: 6ade9baaf7dc125bac6738b44134ee496b8a2be4
ms.sourcegitcommit: 309cf6876d906425a0d6f72deceb9ecd231d387c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/01/2020
ms.locfileid: "84265928"
---
# <a name="provision-standard-manual-throughput-on-an-azure-cosmos-container"></a>Approvisionner le débit standard (manuel) sur un conteneur Azure Cosmos

Cet article explique comment approvisionner le débit standard (manuel) sur un conteneur (collection, graphique ou table) dans Azure Cosmos DB. Vous pouvez provisionner le débit sur un seul conteneur, ou [provisionner le débit sur une base de données](how-to-provision-database-throughput.md) et le partager entre les conteneurs de la base de données. Vous pouvez provisionner le débit sur un conteneur à l’aide du portail Azure, d’Azure CLI ou des SDK Azure Cosmos DB.

## <a name="azure-portal"></a>Portail Azure

1. Connectez-vous au [portail Azure](https://portal.azure.com/).

1. [Créez un compte Azure Cosmos](create-sql-api-dotnet.md#create-account) ou sélectionnez un compte Azure Cosmos existant.

1. Ouvrez le volet **Explorateur de données**, puis sélectionnez **Nouvelle collection**. Fournissez ensuite les détails suivants :

   * Indiquez si vous créez une base de données ou si vous utilisez une base de données existante.
   * Entrez un ID de conteneur (ou de table ou de graphe).
   * Entrez une valeur de clé de partition (par exemple `/userid`).
   * Entrez un débit que vous voulez provisionner (par exemple, 1 000 unités de requête).
   * Sélectionnez **OK**.

    ![Capture d’écran de l’Explorateur de données, avec l’option Nouvelle collection mise en surbrillance](./media/how-to-provision-container-throughput/provision-container-throughput-portal-all-api.png)

## <a name="azure-cli-or-powershell"></a>Azure CLI ou PowerShell

Pour créer un conteneur avec un débit dédié, voir

* [Créer un conteneur à l’aide d’Azure CLI](manage-with-cli.md#create-a-container)
* [Créer un conteneur à l’aide de PowerShell](manage-with-powershell.md#create-container)

> [!Note]
> Si vous provisionnez le débit sur un conteneur dans un compte Azure Cosmos configuré avec l’API Azure Cosmos DB pour MongoDB, utilisez `/myShardKey` pour le chemin de clé de partition. Si vous provisionnez le débit sur un conteneur dans un compte Azure Cosmos configuré avec l’API Cassandra, utilisez `/myPrimaryKey` pour le chemin de clé de partition.

## <a name="net-sdk"></a>Kit de développement logiciel (SDK) .NET

> [!Note]
> Utilisez les SDK Cosmos pour l’API SQL afin de provisionner le débit de toutes les API Cosmos DB, à l’exception des API Cassandra et MongoDB.

### <a name="sql-gremlin-and-table-apis"></a><a id="dotnet-most"></a>API SQL, Gremlin et Table

# <a name="net-sdk-v2"></a>[Kit de développement logiciel (SDK) .NET V2](#tab/dotnetv2)

```csharp
// Create a container with a partition key and provision throughput of 400 RU/s
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "myContainerName";
myCollection.PartitionKey.Paths.Add("/myPartitionKey");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    myCollection,
    new RequestOptions { OfferThroughput = 400 });
```

# <a name="net-sdk-v3"></a>[Kit de développement logiciel (SDK) .NET V3](#tab/dotnetv3)

[!code-csharp[](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos/tests/Microsoft.Azure.Cosmos.Tests/SampleCodeForDocs/ContainerDocsSampleCode.cs?name=ContainerCreateWithThroughput)]

---

## <a name="javascript-sdk"></a>Kit de développement logiciel (SDK) JavaScript

```javascript
// Create a new Client
const client = new CosmosClient({ endpoint, key });

// Create a database
const { database } = await client.databases.createIfNotExists({ id: "databaseId" });

// Create a container with the specified throughput
const { resource } = await database.containers.createIfNotExists({
id: "containerId",
throughput: 1000
});

// To update an existing container or databases throughput, you need to user the offers API
// Get all the offers
const { resources: offers } = await client.offers.readAll().fetchAll();

// Find the offer associated with your container or the database
const offer = offers.find((_offer) => _offer.offerResourceId === resource._rid);

// Change the throughput value
offer.content.offerThroughput = 2000;

// Replace the offer.
await client.offer(offer.id).replace(offer);
```

### <a name="mongodb-api"></a><a id="dotnet-cassandra"></a>API MongoDB

```csharp
// refer to MongoDB .NET Driver
// https://docs.mongodb.com/drivers/csharp

// Create a new Client
String mongoConnectionString = "mongodb://DBAccountName:Password@DBAccountName.documents.azure.com:10255/?ssl=true&replicaSet=globaldb";
mongoUrl = new MongoUrl(mongoConnectionString);
mongoClientSettings = MongoClientSettings.FromUrl(mongoUrl);
mongoClient = new MongoClient(mongoClientSettings);

// Change the database name
mongoDatabase = mongoClient.GetDatabase("testdb");

// Change the collection name, throughput value then update via MongoDB extension commands
// https://docs.microsoft.com/en-us/azure/cosmos-db/mongodb-custom-commands#update-collection

var result = mongoDatabase.RunCommand<BsonDocument>(@"{customAction: ""UpdateCollection"", collection: ""testcollection"", offerThroughput: 400}");
```

### <a name="cassandra-api"></a><a id="dotnet-cassandra"></a>API Cassandra

Des commandes similaires peuvent être émises avec n’importe quel pilote conforme à CQL.

```csharp
// Create a Cassandra table with a partition (primary) key and provision throughput of 400 RU/s
session.Execute("CREATE TABLE myKeySpace.myTable(
    user_id int PRIMARY KEY,
    firstName text,
    lastName text) WITH cosmosdb_provisioned_throughput=400");

```
### <a name="alter-or-change-throughput-for-cassandra-table"></a>Modifier le débit de la table Cassandra

```csharp
// Altering the throughput too can be done through code by issuing following command
session.Execute("ALTER TABLE myKeySpace.myTable WITH cosmosdb_provisioned_throughput=5000");
```


## <a name="next-steps"></a>Étapes suivantes

Consultez les articles suivants pour en savoir plus sur le provisionnement du débit dans Azure Cosmos DB :

* [Comment approvisionner le débit standard (manuel) sur une base de données](how-to-provision-database-throughput.md)
* [Comment approvisionner le débit avec mise à l’échelle automatique sur une base de données](how-to-provision-autoscale-throughput.md)
* [Unités de requête et débit dans Azure Cosmos DB](request-units.md)
