---
title: Créer un conteneur dans Azure Cosmos DB
description: En savoir plus sur la création d’un conteneur dans Azure Cosmos DB
services: cosmos-db
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 11/06/2018
ms.author: mjbrown
ms.openlocfilehash: 5558409c3a3b0aef3757ebb73b2046a7018e4150
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2018
ms.locfileid: "53088185"
---
# <a name="create-a-container-in-azure-cosmos-db"></a>Créer un conteneur dans Azure Cosmos DB

Cet article explique les différentes façons de créer un conteneur (collection, table, graphique). Un conteneur peut être créé à l’aide du portail Azure, d’Azure CLI, ou des kits de développement logiciel (SDK) pris en charge. Cet article montre comment créer un conteneur, spécifier la clé de partition et approvisionner le débit.

## <a name="create-a-container-using-azure-portal"></a>Créer un conteneur à l’aide du portail Azure

### <a id="portal-sql"></a>API (Core) SQL

1. Connectez-vous au [portail Azure](https://portal.azure.com/).

1. [Créez un nouveau compte Cosmos DB](create-sql-api-dotnet.md#create-a-database-account) ou sélectionnez un compte existant.

1. Ouvrez le volet **Explorateur de données** et sélectionnez **Nouvelle collection**. Ensuite, renseignez le formulaire avec les informations suivantes :

   * Créez une nouvelle base de données ou utilisez-en une existante.
   * Saisissez l’ID de la collection.
   * Entrez une clé de partition.
   * Entrez un débit, par exemple 1 000 unités de requête.
   * Sélectionnez **OK**.

![L’API SQL crée une collection](./media/how-to-create-container/partitioned-collection-create-sql.png)

### <a id="portal-mongodb"></a>API MongoDB

1. Connectez-vous au [portail Azure](https://portal.azure.com/).

1. [Créez un nouveau compte Cosmos DB](create-mongodb-dotnet.md#create-a-database-account) ou sélectionnez un compte existant.

1. Ouvrez le volet **Explorateur de données** et sélectionnez **Nouvelle collection**. Ensuite, renseignez le formulaire avec les informations suivantes :

   * Créez une nouvelle base de données ou utilisez-en une existante.
   * Saisissez l’ID de la collection.
   * Sélectionnez la capacité de stockage **Illimitée**.
   * Entrez une clé de partition.
   * Entrez un débit, par exemple 1 000 unités de requête.
   * Sélectionnez **OK**.

![L’API MongoDB crée une collection](./media/how-to-create-container/partitioned-collection-create-mongodb.png)

### <a id="portal-cassandra"></a>API Cassandra

1. Connectez-vous au [portail Azure](https://portal.azure.com/).

1. [Créez un nouveau compte Cosmos DB](create-cassandra-dotnet.md#create-a-database-account) ou sélectionnez un compte existant.

1. Ouvrez le volet **Explorateur de données** et sélectionnez **Nouvelle table**. Ensuite, renseignez le formulaire avec les informations suivantes :

   * Créez un nouvel espace de clés ou utilisez-en un existant.
   * Saisissez le nom de la table.
   * Entrez les propriétés et spécifiez une CLÉ PRIMAIRE.
   * Entrez un débit, par exemple 1 000 unités de requête.
   * Sélectionnez **OK**.

![L’API Cassandra crée une collection](./media/how-to-create-container/partitioned-collection-create-cassandra.png)

> [!NOTE]
> Pour l’API Cassandra, la clé primaire est utilisée comme clé de partition.

### <a id="portal-gremlin"></a>API Gremlin

1. Connectez-vous au [portail Azure](https://portal.azure.com/).

1. [Créez un nouveau compte Cosmos DB](create-graph-dotnet.md#create-a-database-account) ou sélectionnez un compte existant.

1. Ouvrez le volet **Explorateur de données** et sélectionnez **Nouveau graphique**. Ensuite, renseignez le formulaire avec les informations suivantes :

   * Créez une nouvelle base de données ou utilisez-en une existante.
   * Saisissez l’ID du graphique.
   * Sélectionnez la capacité de stockage **Illimitée**.
   * Entrez une clé de partition pour les vertex.
   * Entrez un débit, par exemple 1 000 unités de requête.
   * Sélectionnez **OK**.

![L’API Gremlin crée une collection](./media/how-to-create-container/partitioned-collection-create-gremlin.png)

### <a id="portal-table"></a>API de table

1. Connectez-vous au [portail Azure](https://portal.azure.com/).

1. [Créez un nouveau compte Cosmos DB](create-table-dotnet.md#create-a-database-account) ou sélectionnez un compte existant.

1. Ouvrez le volet **Explorateur de données** et sélectionnez **Nouvelle table**. Ensuite, renseignez le formulaire avec les informations suivantes :

   * Saisissez l’ID de la table.
   * Sélectionnez la capacité de stockage **Illimitée**.
   * Entrez un débit, par exemple 1 000 unités de requête.
   * Sélectionnez **OK**.

![L’API Table crée une collection](./media/how-to-create-container/partitioned-collection-create-table.png)

> [!Note]
> Pour l’API Table, la clé de partition est spécifiée chaque fois que vous ajoutez une nouvelle ligne.

## <a name="create-a-container-using-azure-cli"></a>Créer un conteneur à l’aide d’Azure CLI

### <a id="cli-sql"></a>API (Core) SQL

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

### <a id="cli-mongodb"></a>API MongoDB

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

### <a id="cli-cassandra"></a> API Cassandra

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

## <a name="create-a-container-using-net-sdk"></a>Créer un conteneur à l’aide du kit de développement logiciel (SDK) .NET

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

### <a id="dotnet-mongodb"></a>API MongoDB

```csharp
// Create a collection with a partition key by using Mongo Shell:
db.runCommand( { shardCollection: "myDatabase.myCollection", key: { myShardKey: "hashed" } } )
```

> [!Note]
> MongoDB ne comporte de concept d’unités de requête. Pour créer une nouvelle collection avec un débit, utilisez le portail Azure ou l’API SQL, comme indiqué dans les exemples précédents.

### <a id="dotnet-cassandra"></a>API Cassandra

```csharp
// Create a Cassandra table with a partition/primary key and provision 1000 RU/s throughput.
session.Execute(CREATE TABLE myKeySpace.myTable(
    user_id int PRIMARY KEY,
    firstName text,
    lastName text) WITH cosmosdb_provisioned_throughput=1000);
```

## <a name="next-steps"></a>Étapes suivantes

Consultez les articles suivants pour en savoir plus sur le partitionnement dans Cosmos DB :

- [Partitionnement dans Azure Cosmos DB](partitioning-overview.md)
