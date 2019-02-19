---
title: Approvisionner le débit d’un conteneur dans Azure Cosmos DB
description: Découvrez comment provisionner le débit au niveau du conteneur dans Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 11/06/2018
ms.author: mjbrown
ms.openlocfilehash: 4df8a12581b5d71a76964ca1e3d40c6c53185f67
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55860318"
---
# <a name="provision-throughput-on-an-azure-cosmos-container"></a>Provisionner du débit sur un conteneur Azure Cosmos

Cet article explique comment provisionner le débit d’un conteneur (collection, graphe ou table) dans Azure Cosmos DB. Vous pouvez provisionner le débit d’un seul conteneur ou d’une [base de données](how-to-provision-database-throughput.md), et le partager entre les conteneurs de la base de données. Vous pouvez provisionner le débit d’un conteneur à l’aide du Portail Azure, d’Azure CLI ou des kits SDK Azure Cosmos DB.

## <a name="provision-throughput-by-using-azure-portal"></a>Approvisionner le débit à l’aide du portail Azure

1. Connectez-vous au [Portail Azure](https://portal.azure.com/).

1. [Créez un compte Azure Cosmos DB](create-sql-api-dotnet.md#create-a-database-account), ou sélectionnez un compte existant.

1. Ouvrez le volet **Explorateur de données**, puis sélectionnez **Nouvelle collection**. Fournissez ensuite les détails suivants :

   * Indiquez si vous créez une base de données ou si vous utilisez une base de données existante.
   * Entrez un ID de collection (ou de table ou de graphe).
   * Entrez une valeur de clé de partition (par exemple `/userid`).
   * Entrez un débit, par exemple 1 000 RU.
   * Sélectionnez **OK**.

![Capture d’écran de l’Explorateur de données, avec l’option Nouvelle collection mise en surbrillance](./media/how-to-provision-container-throughput/provision-container-throughput-portal-all-api.png)

## <a name="provision-throughput-by-using-azure-cli"></a>Provisionner le débit à l’aide d’Azure CLI

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

Si vous provisionnez le débit d’un compte Azure Cosmos DB configuré avec l’API Azure Cosmos DB pour MongoDB, utilisez `/myShardKey` pour le chemin de clé de partition. Si vous provisionnez le débit d’un compte Azure Cosmos DB configuré pour l’API Cassandra, utilisez `/myPrimaryKey` pour le chemin de clé de partition.

## <a name="provision-throughput-by-using-net-sdk"></a>Provisionner le débit à l’aide du kit SDK .NET

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

Consultez les articles suivants pour en savoir plus sur le provisionnement du débit dans Azure Cosmos DB :

* [Comment approvisionner le débit d’une base de données](how-to-provision-database-throughput.md)
* [Unités de requête et débit dans Azure Cosmos DB](request-units.md)
