---
title: Créer un conteneur dans Azure Cosmos DB
description: Découvrez comment créer un conteneur dans Azure Cosmos DB avec le portail Azure, le SDK .Net, le SDK Java, le SDK Python, le SDK Node.js et autres kits SDK.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mjbrown
ms.openlocfilehash: 4eaa2974817bfcd8bef83e5139d75a2d4c2ec107
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873707"
---
# <a name="create-an-azure-cosmos-container"></a>Créer un conteneur Azure Cosmos

Cet article explique les différentes façons de créer un conteneur Azure Cosmos (collection, table ou graphe). À cette fin, vous pouvez utiliser le portail Azure, Azure CLI ou les SDK pris en charge. Cet article montre comment créer un conteneur, spécifier la clé de partition et provisionner le débit.

## <a name="create-a-container-using-azure-portal"></a>Créer un conteneur à l’aide du portail Azure

### <a id="portal-sql"></a>API SQL

1. Connectez-vous au [Portail Azure](https://portal.azure.com/).

1. [Créez un compte Azure Cosmos](create-sql-api-dotnet.md#create-account) ou sélectionnez un compte existant.

1. Ouvrez le volet **Explorateur de données**, puis sélectionnez **Nouveau conteneur**. Fournissez ensuite les détails suivants :

   * Indiquez si vous créez une base de données ou si vous utilisez une base de données existante.
   * Entrez un ID de conteneur.
   * Entrez une clé de partition.
   * Entrez un débit à provisionner (par exemple, 1 000 unités de requête).
   * Sélectionnez **OK**.

    ![Capture d'écran du volet Explorateur de données, avec l'option Nouveau conteneur mise en surbrillance](./media/how-to-create-container/partitioned-collection-create-sql.png)

### <a id="portal-mongodb"></a>API Azure Cosmos DB pour MongoDB

1. Connectez-vous au [Portail Azure](https://portal.azure.com/).

1. [Créez un compte Azure Cosmos](create-mongodb-dotnet.md#create-a-database-account) ou sélectionnez un compte existant.

1. Ouvrez le volet **Explorateur de données**, puis sélectionnez **Nouveau conteneur**. Fournissez ensuite les détails suivants :

   * Indiquez si vous créez une base de données ou si vous utilisez une base de données existante.
   * Entrez un ID de conteneur.
   * Entrez une clé de sharding.
   * Entrez un débit à provisionner (par exemple, 1 000 unités de requête).
   * Sélectionnez **OK**.

    ![Capture d'écran de l'API Azure Cosmos DB pour MongoDB, boîte de dialogue Ajouter un conteneur](./media/how-to-create-container/partitioned-collection-create-mongodb.png)

### <a id="portal-cassandra"></a>API Cassandra

1. Connectez-vous au [Portail Azure](https://portal.azure.com/).

1. [Créez un compte Azure Cosmos](create-cassandra-dotnet.md#create-a-database-account) ou sélectionnez un compte existant.

1. Ouvrez le volet **Explorateur de données**, puis sélectionnez **Nouvelle table**. Fournissez ensuite les détails suivants :

   * Indiquez si vous créez un espace de clés ou si vous utilisez une base de données existante.
   * Saisissez le nom de la table.
   * Entrez les propriétés et spécifiez une clé primaire.
   * Entrez un débit à provisionner (par exemple, 1 000 unités de requête).
   * Sélectionnez **OK**.

    ![Capture d’écran de l’API Cassandra, boîte de dialogue Ajouter une table](./media/how-to-create-container/partitioned-collection-create-cassandra.png)

> [!NOTE]
> Pour l’API Cassandra, la clé primaire est utilisée comme clé de partition.

### <a id="portal-gremlin"></a>API Gremlin

1. Connectez-vous au [Portail Azure](https://portal.azure.com/).

1. [Créez un compte Azure Cosmos](create-graph-dotnet.md#create-a-database-account) ou sélectionnez un compte existant.

1. Ouvrez le volet **Explorateur de données**, puis sélectionnez **Nouveau graphe**. Fournissez ensuite les détails suivants :

   * Indiquez si vous créez une base de données, ou si vous utilisez une base de données existante.
   * Entrez un ID de graphe.
   * Sélectionnez la capacité de stockage **Illimitée**.
   * Entrez une clé de partition pour les vertex.
   * Entrez un débit à provisionner (par exemple, 1 000 unités de requête).
   * Sélectionnez **OK**.

    ![Capture d’écran de l’API Gremlin, boîte de dialogue Ajouter un graphe](./media/how-to-create-container/partitioned-collection-create-gremlin.png)

### <a id="portal-table"></a>API de table

1. Connectez-vous au [Portail Azure](https://portal.azure.com/).

1. [Créez un compte Azure Cosmos](create-table-dotnet.md#create-a-database-account) ou sélectionnez un compte existant.

1. Ouvrez le volet **Explorateur de données**, puis sélectionnez **Nouvelle table**. Fournissez ensuite les détails suivants :

   * Entrez un ID de table.
   * Entrez un débit à provisionner (par exemple, 1 000 unités de requête).
   * Sélectionnez **OK**.

    ![Capture d’écran de l’API Table, boîte de dialogue Ajouter une table](./media/how-to-create-container/partitioned-collection-create-table.png)

> [!Note]
> Pour l’API Table, la clé de partition est spécifiée chaque fois que vous ajoutez une nouvelle ligne.

## Créer un conteneur à l'aide d'Azure CLI<a id="cli-sql"></a><a id="cli-mongodb"></a><a id="cli-cassandra"></a><a id="cli-gremlin"></a><a id="cli-table"></a>

Les liens ci-dessous expliquent comment créer des conteneurs pour Azure Cosmos DB à l'aide d'Azure CLI.

Pour obtenir la liste de tous les exemples Azure CLI de l'ensemble des API Azure Cosmos DB, consultez [API SQL](cli-samples.md), [API Cassandra](cli-samples-cassandra.md), [API MongoDB](cli-samples-mongodb.md), [API Gremlin](cli-samples-gremlin.md) et [API Table](cli-samples-table.md).

* [Créer un conteneur à l'aide d'Azure CLI](manage-with-cli.md#create-a-container)
* [Créer une collection pour l'API Azure Cosmos DB pour MongoDB à l'aide d'Azure CLI](./scripts/cli/mongodb/create.md)
* [Créer une table Cassandra à l'aide d'Azure CLI](./scripts/cli/cassandra/create.md)
* [Créer un graphique Gremlin à l'aide d'Azure CLI](./scripts/cli/gremlin/create.md)
* [Créer une table API Table à l'aide d'Azure CLI](./scripts/cli/table/create.md)

## Créer un conteneur à l'aide de PowerShell<a id="ps-sql"></a><a id="ps-mongodb"><a id="ps-cassandra"></a><a id="ps-gremlin"><a id="ps-table"></a>

Les liens ci-dessous expliquent comment créer des conteneurs pour Azure Cosmos DB à l'aide de PowerShell.

Pour obtenir la liste de tous les exemples Azure CLI de l'ensemble des API Azure Cosmos DB, consultez [API SQL](powershell-samples-sql.md), [API Cassandra](powershell-samples-cassandra.md), [API MongoDB](powershell-samples-mongodb.md), [API Gremlin](powershell-samples-gremlin.md) et [API Table](powershell-samples-table.md).

* [Créer un conteneur à l'aide de PowerShell](manage-with-powershell.md#create-container)
* [Créer une collection pour l'API Azure Cosmos DB pour MongoDB à l'aide de PowerShell](./scripts/powershell/mongodb/ps-mongodb-create.md)
* [Créer une table Cassandra à l'aide de PowerShell](./scripts/powershell/cassandra/ps-cassandra-create.md)
* [Créer un graphique Gremlin à l'aide de PowerShell](./scripts/powershell/gremlin/ps-gremlin-create.md)
* [Créer une table API Table à l'aide de PowerShell](./scripts/powershell/table/ps-table-create.md)

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

### <a id="dotnet-mongodb"></a>API Azure Cosmos DB pour MongoDB

```csharp
// Create a collection with a partition key by using Mongo Shell:
db.runCommand( { shardCollection: "myDatabase.myCollection", key: { myShardKey: "hashed" } } )
```

> [!Note]
> Le protocole filaire MongoDB ne comprend pas le concept d’[unités de requête](request-units.md). Pour créer une collection avec un débit provisionné dessus, utilisez le portail Azure ou les SDK Cosmos DB pour l’API SQL.

### <a id="dotnet-cassandra"></a>API Cassandra

```csharp
// Create a Cassandra table with a partition/primary key and provision 1000 RU/s throughput.
session.Execute(CREATE TABLE myKeySpace.myTable(
    user_id int PRIMARY KEY,
    firstName text,
    lastName text) WITH cosmosdb_provisioned_throughput=1000);
```

## <a name="next-steps"></a>Étapes suivantes

* [Partitioning in Azure Cosmos DB](partitioning-overview.md) (Partitionnement dans Azure Cosmos DB)
* [Unités de requête dans Azure Cosmos DB](request-units.md)
* [Provisionner le débit sur les conteneurs et les bases de données](set-throughput.md)
* [Utiliser un compte Azure Cosmos](account-overview.md)
