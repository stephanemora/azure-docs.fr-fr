---
title: Créer un conteneur dans Azure Cosmos DB
description: En savoir plus sur la création d’un conteneur dans Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 11/06/2018
ms.author: mjbrown
ms.openlocfilehash: 8ce890500c31c1966254e5bca9d23c8fcdd7bb67
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2019
ms.locfileid: "58258290"
---
# <a name="create-an-azure-cosmos-container"></a>Créer un conteneur Azure Cosmos

Cet article explique les différentes façons de créer un conteneur (collection, table ou graphe). Vous utilisez le Portail Azure, Azure CLI ou les kits SDK pris en charge. Cet article montre comment créer un conteneur, spécifier la clé de partition et provisionner le débit.

## <a name="create-a-container-by-using-azure-portal"></a>Créer un conteneur à l’aide du Portail Azure

### <a id="portal-sql"></a>API SQL

1. Connectez-vous au [Portail Azure](https://portal.azure.com/).

1. [Créez un compte Azure Cosmos DB](create-sql-api-dotnet.md#create-account), ou sélectionnez un compte existant.

1. Ouvrez le volet **Explorateur de données**, puis sélectionnez **Nouvelle collection**. Fournissez ensuite les détails suivants :

   * Indiquez si vous créez une base de données ou si vous utilisez une base de données existante.
   * Entrez un ID de collection.
   * Entrez une clé de partition.
   * Entrez un débit, par exemple 1 000 RU.
   * Sélectionnez **OK**.

![Capture d’écran du volet Explorateur de données, avec l’option Nouvelle collection mise en surbrillance](./media/how-to-create-container/partitioned-collection-create-sql.png)

### <a id="portal-mongodb"></a>API Azure Cosmos DB pour MongoDB

1. Connectez-vous au [Portail Azure](https://portal.azure.com/).

1. [Créez un compte Azure Cosmos DB](create-mongodb-dotnet.md#create-a-database-account), ou sélectionnez un compte existant.

1. Ouvrez le volet **Explorateur de données**, puis sélectionnez **Nouvelle collection**. Fournissez ensuite les détails suivants :

   * Indiquez si vous créez une base de données ou si vous utilisez une base de données existante.
   * Entrez un ID de collection.
   * Sélectionnez la capacité de stockage **Illimitée**.
   * Entrez une clé de sharding.
   * Entrez un débit, par exemple 1 000 RU.
   * Sélectionnez **OK**.

![Capture d’écran de l’API Azure Cosmos DB pour MongoDB, boîte de dialogue Ajouter une collection](./media/how-to-create-container/partitioned-collection-create-mongodb.png)

### <a id="portal-cassandra"></a>API Cassandra

1. Connectez-vous au [Portail Azure](https://portal.azure.com/).

1. [Créez un compte Azure Cosmos DB](create-cassandra-dotnet.md#create-a-database-account), ou sélectionnez un compte existant.

1. Ouvrez le volet **Explorateur de données**, puis sélectionnez **Nouvelle table**. Fournissez ensuite les détails suivants :

   * Indiquez si vous créez un espace de clés ou si vous utilisez une base de données existante.
   * Saisissez le nom de la table.
   * Entrez les propriétés et spécifiez une clé primaire.
   * Entrez un débit, par exemple 1 000 RU.
   * Sélectionnez **OK**.

![Capture d’écran de l’API Cassandra, boîte de dialogue Ajouter une table](./media/how-to-create-container/partitioned-collection-create-cassandra.png)

> [!NOTE]
> Pour l’API Cassandra, la clé primaire est utilisée comme clé de partition.

### <a id="portal-gremlin"></a>API Gremlin

1. Connectez-vous au [Portail Azure](https://portal.azure.com/).

1. [Créez un compte Azure Cosmos DB](create-graph-dotnet.md#create-a-database-account), ou sélectionnez un compte existant.

1. Ouvrez le volet **Explorateur de données**, puis sélectionnez **Nouveau graphe**. Fournissez ensuite les détails suivants :

   * Indiquez si vous créez une base de données, ou si vous utilisez une base de données existante.
   * Entrez un ID de graphe.
   * Sélectionnez la capacité de stockage **Illimitée**.
   * Entrez une clé de partition pour les vertex.
   * Entrez un débit, par exemple 1 000 RU.
   * Sélectionnez **OK**.

![Capture d’écran de l’API Gremlin, boîte de dialogue Ajouter un graphe](./media/how-to-create-container/partitioned-collection-create-gremlin.png)

### <a id="portal-table"></a>API de table

1. Connectez-vous au [Portail Azure](https://portal.azure.com/).

1. [Créez un compte Azure Cosmos DB](create-table-dotnet.md#create-a-database-account), ou sélectionnez un compte existant.

1. Ouvrez le volet **Explorateur de données**, puis sélectionnez **Nouvelle table**. Fournissez ensuite les détails suivants :

   * Entrez un ID de table.
   * Sélectionnez la capacité de stockage **Illimitée**.
   * Entrez un débit, par exemple 1 000 RU.
   * Sélectionnez **OK**.

![Capture d’écran de l’API Table, boîte de dialogue Ajouter une table](./media/how-to-create-container/partitioned-collection-create-table.png)

> [!Note]
> Pour l’API Table, la clé de partition est spécifiée chaque fois que vous ajoutez une nouvelle ligne.

## <a name="create-a-container-by-using-azure-cli"></a>Créer un conteneur à l’aide d’Azure CLI

### <a id="cli-sql"></a>API SQL

```azurecli-interactive
# Create a container with a partition key and provision 1000 RU/s throughput.

az cosmosdb collection create \
    --resource-group $resourceGroupName \
    --collection-name $containerName \
    --name $accountName \
    --db-name $databaseName \
    --partition-key-path /myPartitionKey \
    --throughput 1000
```

### <a id="cli-mongodb"></a>API Azure Cosmos DB pour MongoDB

```azurecli-interactive
# Create a collection with a shard key and provision 1000 RU/s throughput.
az cosmosdb collection create \
    --resource-group $resourceGroupName \
    --collection-name $collectionName \
    --name $accountName \
    --db-name $databaseName \
    --partition-key-path /myShardKey \
    --throughput 1000
```

### <a id="cli-cassandra"></a>API Cassandra

```azurecli-interactive
# Create a table with a partition/primary key and provision 1000 RU/s throughput.
az cosmosdb collection create \
    --resource-group $resourceGroupName \
    --collection-name $tableName \
    --name $accountName \
    --db-name $keyspaceName \
    --partition-key-path /myPrimaryKey \
    --throughput 1000
```

### <a id="cli-gremlin"></a>API Gremlin

```azurecli-interactive
# Create a graph with a partition key and provision 1000 RU/s throughput.
az cosmosdb collection create \
    --resource-group $resourceGroupName \
    --collection-name $graphName \
    --name $accountName \
    --db-name $databaseName \
    --partition-key-path /myPartitionKey \
    --throughput 1000
```

### <a id="cli-table"></a>API de table

```azurecli-interactive
# Create a table with 1000 RU/s
# Note: you don't need to specify partition key in the following command because the partition key is set on each row.
az cosmosdb collection create \
    --resource-group $resourceGroupName \
    --collection-name $tableName \
    --name $accountName \
    --db-name $databaseName \
    --throughput 1000
```

## <a name="create-a-container-by-using-net-sdk"></a>Créer un conteneur à l’aide du kit SDK .NET

### <a id="dotnet-sql-graph"></a>API SQL et API Gremlin

```csharp
// Create a container with a partition key and provision 1000 RU/s throughput.
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "myContainerName";
myCollection.PartitionKey.Paths.Add("/myPartitionKey");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    myCollection,
    new RequestOptions { OfferThroughput = 1000 });
```

### <a id="dotnet-mongodb"></a>API Azure Cosmos DB pour MongoDB

```csharp
// Create a collection with a partition key by using Mongo Shell:
db.runCommand( { shardCollection: "myDatabase.myCollection", key: { myShardKey: "hashed" } } )
```

> [!Note]
> Le protocole de transmission MongoDB ne nécessite pas d’unités. Pour créer une collection avec un débit, utilisez le Portail Azure ou l’API SQL.

### <a id="dotnet-cassandra"></a>API Cassandra

```csharp
// Create a Cassandra table with a partition/primary key and provision 1000 RU/s throughput.
session.Execute(CREATE TABLE myKeySpace.myTable(
    user_id int PRIMARY KEY,
    firstName text,
    lastName text) WITH cosmosdb_provisioned_throughput=1000);
```

## <a name="next-steps"></a>Étapes suivantes

- [Partitioning in Azure Cosmos DB](partitioning-overview.md) (Partitionnement dans Azure Cosmos DB)
