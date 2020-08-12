---
title: Créer un conteneur dans Azure Cosmos DB
description: Découvrez comment créer un conteneur dans Azure Cosmos DB avec le portail Azure et les kits SDK .Net, Java, Python, Node.js ou autres.
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.date: 07/29/2020
ms.author: mjbrown
ms.custom: devx-track-azurecli
ms.openlocfilehash: 6e62e307b6c5f5de939505eae8850f0117782a69
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/31/2020
ms.locfileid: "87498359"
---
# <a name="create-an-azure-cosmos-container"></a>Créer un conteneur Azure Cosmos

Cet article explique les différentes façons de créer un conteneur Azure Cosmos (collection, table ou graphe) à l’aide du portail Azure, de l’interface Azure CLI, de PowerShell et des kits SDK pris en charge. Cet article montre comment créer un conteneur, spécifier la clé de partition et provisionner le débit.

> [!NOTE]
> Lorsque vous créez des conteneurs, veillez à ne pas créer deux conteneurs portant le même nom et dont la casse est la seule différence. C’est parce que certaines parties de la plateforme Azure ne respectent pas la casse, ce qui peut entraîner une confusion ou collision de la télémétrie et des actions sur les conteneurs portant ces noms.

## <a name="create-a-container-using-azure-portal"></a>Créer un conteneur à l’aide du portail Azure

### <a name="sql-api"></a><a id="portal-sql"></a>API SQL

1. Connectez-vous au [portail Azure](https://portal.azure.com/).

1. [Créez un compte Azure Cosmos](create-sql-api-dotnet.md#create-account) ou sélectionnez un compte existant.

1. Ouvrez le volet **Explorateur de données**, puis sélectionnez **Nouveau conteneur**. Fournissez ensuite les détails suivants :

   * Indiquez si vous créez une base de données ou si vous utilisez une base de données existante.
   * Entrez un ID de conteneur.
   * Entrez une clé de partition.
   * Entrez un débit à provisionner (par exemple, 1 000 unités de requête).
   * Sélectionnez **OK**.

    :::image type="content" source="./media/how-to-create-container/partitioned-collection-create-sql.png" alt-text="Capture d’écran du volet Explorateur de données, avec l’option Nouveau conteneur mise en surbrillance":::

### <a name="azure-cosmos-db-api-for-mongodb"></a><a id="portal-mongodb"></a>API Azure Cosmos DB pour MongoDB

1. Connectez-vous au [portail Azure](https://portal.azure.com/).

1. [Créez un compte Azure Cosmos](create-mongodb-dotnet.md#create-a-database-account) ou sélectionnez un compte existant.

1. Ouvrez le volet **Explorateur de données**, puis sélectionnez **Nouveau conteneur**. Fournissez ensuite les détails suivants :

   * Indiquez si vous créez une base de données ou si vous utilisez une base de données existante.
   * Entrez un ID de conteneur.
   * Entrez une clé de sharding.
   * Entrez un débit à provisionner (par exemple, 1 000 unités de requête).
   * Sélectionnez **OK**.

    :::image type="content" source="./media/how-to-create-container/partitioned-collection-create-mongodb.png" alt-text="Capture d'écran de l'API Azure Cosmos DB pour MongoDB, boîte de dialogue Ajouter un conteneur":::

### <a name="cassandra-api"></a><a id="portal-cassandra"></a>API Cassandra

1. Connectez-vous au [portail Azure](https://portal.azure.com/).

1. [Créez un compte Azure Cosmos](create-cassandra-dotnet.md#create-a-database-account) ou sélectionnez un compte existant.

1. Ouvrez le volet **Explorateur de données**, puis sélectionnez **Nouvelle table**. Fournissez ensuite les détails suivants :

   * Indiquez si vous créez un espace de clés ou si vous utilisez une base de données existante.
   * Saisissez le nom de la table.
   * Entrez les propriétés et spécifiez une clé primaire.
   * Entrez un débit à provisionner (par exemple, 1 000 unités de requête).
   * Sélectionnez **OK**.

    :::image type="content" source="./media/how-to-create-container/partitioned-collection-create-cassandra.png" alt-text="Capture d’écran de l’API Cassandra, boîte de dialogue Ajouter une table":::

> [!NOTE]
> Pour l’API Cassandra, la clé primaire est utilisée comme clé de partition.

### <a name="gremlin-api"></a><a id="portal-gremlin"></a>API Gremlin

1. Connectez-vous au [portail Azure](https://portal.azure.com/).

1. [Créez un compte Azure Cosmos](create-graph-dotnet.md#create-a-database-account) ou sélectionnez un compte existant.

1. Ouvrez le volet **Explorateur de données**, puis sélectionnez **Nouveau graphe**. Fournissez ensuite les détails suivants :

   * Indiquez si vous créez une base de données, ou si vous utilisez une base de données existante.
   * Entrez un ID de graphe.
   * Sélectionnez la capacité de stockage **Illimitée**.
   * Entrez une clé de partition pour les vertex.
   * Entrez un débit à provisionner (par exemple, 1 000 unités de requête).
   * Sélectionnez **OK**.

    :::image type="content" source="./media/how-to-create-container/partitioned-collection-create-gremlin.png" alt-text="Capture d’écran de l’API Gremlin, boîte de dialogue Ajouter un graphique":::

### <a name="table-api"></a><a id="portal-table"></a>API de table

1. Connectez-vous au [portail Azure](https://portal.azure.com/).

1. [Créez un compte Azure Cosmos](create-table-dotnet.md#create-a-database-account) ou sélectionnez un compte existant.

1. Ouvrez le volet **Explorateur de données**, puis sélectionnez **Nouvelle table**. Fournissez ensuite les détails suivants :

   * Entrez un ID de table.
   * Entrez un débit à provisionner (par exemple, 1 000 unités de requête).
   * Sélectionnez **OK**.

    :::image type="content" source="./media/how-to-create-container/partitioned-collection-create-table.png" alt-text="Capture d’écran de l’API Table, boîte de dialogue Ajouter une table":::

> [!Note]
> Pour l’API Table, la clé de partition est spécifiée chaque fois que vous ajoutez une nouvelle ligne.

## <a name="create-a-container-using-azure-cli"></a>Créer un conteneur à l'aide d'Azure CLI<a id="cli-sql"></a><a id="cli-mongodb"></a><a id="cli-cassandra"></a><a id="cli-gremlin"></a><a id="cli-table"></a>

Les liens ci-dessous expliquent comment créer des conteneurs pour Azure Cosmos DB à l'aide d'Azure CLI.

Pour obtenir la liste de tous les exemples propres à l’interface Azure CLI dans toutes les API Azure Cosmos DB, consultez [Exemples Azure CLI pour Azure Cosmos DB](cli-samples.md).

* [Créer un conteneur à l'aide d'Azure CLI](manage-with-cli.md#create-a-container)
* [Créer une collection pour l'API Azure Cosmos DB pour MongoDB à l'aide d'Azure CLI](./scripts/cli/mongodb/create.md)
* [Créer une table Cassandra à l'aide d'Azure CLI](./scripts/cli/cassandra/create.md)
* [Créer un graphique Gremlin à l'aide d'Azure CLI](./scripts/cli/gremlin/create.md)
* [Créer une table API Table à l'aide d'Azure CLI](./scripts/cli/table/create.md)

## <a name="create-a-container-using-powershell"></a>Créer un conteneur à l’aide de PowerShell

Les liens ci-dessous expliquent comment créer des conteneurs pour Azure Cosmos DB à l'aide de PowerShell.

Pour obtenir la liste de tous les exemples PowerShell pour toutes les API Azure Cosmos DB, consultez [Exemples PowerShell](powershell-samples.md)

* [Créer un conteneur à l’aide de PowerShell](manage-with-powershell.md#create-container)
* [Créer une collection pour l’API Azure Cosmos DB pour MongoDB à l’aide de PowerShell](./scripts/powershell/mongodb/create.md)
* [Créer une table Cassandra à l’aide de PowerShell](./scripts/powershell/cassandra/create.md)
* [Créer un graphe Gremlin à l’aide de PowerShell](./scripts/powershell/gremlin/create.md)
* [Créer une table API Table à l’aide de PowerShell](./scripts/powershell/table/create.md)

## <a name="create-a-container-using-net-sdk"></a>Créer un conteneur à l’aide du kit de développement logiciel (SDK) .NET

Si vous rencontrez une exception de délai d’expiration lors de la création d’une collection, effectuez une opération de lecture pour valider si la collection a été créée avec succès. L’opération de lecture lève une exception jusqu’à ce que l’opération de création de la collection aboutisse. Pour obtenir la liste des codes d’état pris en charge par l’opération de création, consultez l’article [Codes d’état HTTP pour Azure Cosmos DB](/rest/api/cosmos-db/http-status-codes-for-cosmosdb).

### <a name="sql-api-and-gremlin-api"></a><a id="dotnet-sql-graph"></a>API SQL et API Gremlin

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

### <a name="azure-cosmos-db-api-for-mongodb"></a><a id="dotnet-mongodb"></a>API Azure Cosmos DB pour MongoDB

```csharp
// Create a collection with a partition key by using Mongo Shell:
db.runCommand( { shardCollection: "myDatabase.myCollection", key: { myShardKey: "hashed" } } )
```

> [!Note]
> Le protocole filaire MongoDB ne comprend pas le concept d’[unités de requête](request-units.md). Pour créer une collection avec un débit provisionné dessus, utilisez le portail Azure ou les SDK Cosmos DB pour l’API SQL.

### <a name="cassandra-api"></a><a id="dotnet-cassandra"></a>API Cassandra

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
