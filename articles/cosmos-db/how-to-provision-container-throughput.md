---
title: Approvisionner le débit d’un conteneur dans Azure Cosmos DB
description: Découvrez comment provisionner le débit au niveau du conteneur dans Azure Cosmos DB
services: cosmos-db
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 11/06/2018
ms.author: mjbrown
ms.openlocfilehash: 4c17c972a39cd336ab33f6be8bd3db5b8f0f2c92
ms.sourcegitcommit: 9f87a992c77bf8e3927486f8d7d1ca46aa13e849
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/28/2018
ms.locfileid: "53808415"
---
# <a name="provision-throughput-for-an-azure-cosmos-db-container"></a>Approvisionner le débit d’un conteneur Azure Cosmos DB

Cet article explique comment approvisionner le débit d’un conteneur (collection, graphique, table) dans Azure Cosmos DB. Vous pouvez approvisionner le débit d’un seul conteneur ou [d’une base de données](how-to-provision-database-throughput.md) et le partager entre les conteneurs au sein de la base de données. Vous pouvez approvisionner le débit d’un conteneur à l’aide du portail Azure, d’Azure CLI ou des kits de développement logiciel (SDK) CosmosDB.

## <a name="provision-throughput-using-azure-portal"></a>Approvisionner le débit à l’aide du portail Azure

1. Connectez-vous au [portail Azure](https://portal.azure.com/).

1. [Créez un nouveau compte Cosmos DB](create-sql-api-dotnet.md#create-a-database-account) ou sélectionnez un compte existant.

1. Ouvrez le volet **Explorateur de données** et sélectionnez **Nouvelle collection**. Ensuite, renseignez le formulaire avec les informations suivantes :

   * Créez une nouvelle base de données ou utilisez-en une existante.
   * Saisissez un ID de collection (ou de table ou de graphique).
   * Entrez une valeur de clé de partition, par exemple `/userid`.
   * Entrez un débit, par exemple 1 000 unités de requête.
   * Sélectionnez **OK**.

![Approvisionner le débit d’un conteneur d’API SQL](./media/how-to-provision-container-throughput/provision-container-throughput-portal-all-api.png)

## <a name="provision-throughput-using-azure-cli"></a>Approvisionner le débit à l’aide d’Azure CLI

```azurecli-interactive
# Create a container with a partition key and provision throughput of 1000 RU/s
az cosmosdb collection create \
    --resource-group $resourceGroupName \
    --collection-name $containerName \
    --name $accountName \
    --db-name $databaseName \
    --partition-key-path /myPartitionKey \
    --throughput 1000
```

Si vous approvisionnez le débit d’un compte Cosmos configuré avec l’API Azure Cosmos DB pour MongoDB, utilisez '/myShardKey' comme chemin d’accès à la clé de partition. Si vous approvisionnez le débit d’un compte Cosmos configuré pour l’API Cassandra, utilisez '/myPrimaryKey' comme chemin d’accès à la clé de partition.

## <a name="provision-throughput-using-net-sdk"></a>Approvisionner le débit à l’aide du Kit de développement logiciel (SDK) .NET

> [!Note]
> Utilisez l’API SQL pour approvisionner le débit de toutes les API, à l’exception de l’API Cassandra.

### <a id="dotnet-most"></a>API SQL, MongoDB, Gremlin et Table

```csharp
// Create a container with a partition key and provision throughput of 1000 RU/s
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "myContainerName";
myCollection.PartitionKey.Paths.Add("/myPartitionKey");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    myCollection,
    new RequestOptions { OfferThroughput = 1000 });
```

### <a id="dotnet-cassandra"></a>API Cassandra

```csharp
// Create a Cassandra table with a partition (primary) key and provision throughput of 1000 RU/s
session.Execute(CREATE TABLE myKeySpace.myTable(
    user_id int PRIMARY KEY,
    firstName text,
    lastName text) WITH cosmosdb_provisioned_throughput=1000);
```

## <a name="next-steps"></a>Étapes suivantes

Consultez les articles suivants pour en savoir plus sur l’approvisionnement de débit dans Cosmos DB :

* [Comment approvisionner le débit d’une base de données](how-to-provision-database-throughput.md)
* [Unités de requête et débit dans Azure Cosmos DB](request-units.md)
