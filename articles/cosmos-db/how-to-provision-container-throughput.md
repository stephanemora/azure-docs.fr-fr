---
title: Approvisionner le débit d’un conteneur dans Azure Cosmos DB
description: Apprenez à provisionner le débit au niveau du conteneur dans Azure Cosmos DB avec le portail Azure, l’interface CLI, PowerShell et différents kits SDK.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/12/2019
ms.author: mjbrown
ms.openlocfilehash: ed6a55c8b6049f16e96a4a95ecf3ef125db908d7
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74872041"
---
# <a name="provision-throughput-on-an-azure-cosmos-container"></a>Provisionner du débit sur un conteneur Azure Cosmos

Cet article explique comment provisionner le débit sur un conteneur (collection, graphe ou table) dans Azure Cosmos DB. Vous pouvez provisionner le débit sur un seul conteneur, ou [provisionner le débit sur une base de données](how-to-provision-database-throughput.md) et le partager entre les conteneurs de la base de données. Vous pouvez provisionner le débit sur un conteneur à l’aide du portail Azure, d’Azure CLI ou des SDK Azure Cosmos DB.

## <a name="azure-portal"></a>Portail Azure

1. Connectez-vous au [Portail Azure](https://portal.azure.com/).

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
> Utilisez les SDK Cosmos pour l’API SQL afin de provisionner le débit de toutes les API Cosmos DB, à l’exception de l’API Cassandra.

### <a id="dotnet-most"></a>API SQL, MongoDB, Gremlin et Table
### <a name="net-v2-sdk"></a>SDK .Net v2

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

### <a name="net-v3-sdk"></a>SDK .Net v3
[!code-csharp[](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos/tests/Microsoft.Azure.Cosmos.Tests/SampleCodeForDocs/ContainerDocsSampleCode.cs?name=ContainerCreateWithThroughput)]

## <a name="javascript-sdk"></a>Kit de développement logiciel (SDK) JavaScript

```javascript
// Create a new Client
const client = new CosmosClient({ endpoint, key });

// Create a database
const { database } = await client.databases.createIfNotExists({ id: "databaseId" });

// Create a container with the specified throughput
const { resource } = await database.containers.createIfNotExists({
id: "contaierId ",
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

### <a id="dotnet-cassandra"></a>API Cassandra

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

* [Guide pratique pour provisionner le débit sur une base de données](how-to-provision-database-throughput.md)
* [Unités de requête et débit dans Azure Cosmos DB](request-units.md)
