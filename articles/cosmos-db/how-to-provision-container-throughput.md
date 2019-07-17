---
title: Approvisionner le débit d’un conteneur dans Azure Cosmos DB
description: Découvrez comment provisionner le débit au niveau du conteneur dans Azure Cosmos DB
author: rimman
ms.service: cosmos-db
ms.topic: sample
ms.date: 07/03/2019
ms.author: rimman
ms.openlocfilehash: 945bff075828bdbddd2a31642b35a5c592216b93
ms.sourcegitcommit: d2785f020e134c3680ca1c8500aa2c0211aa1e24
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/04/2019
ms.locfileid: "67565886"
---
# <a name="provision-throughput-on-an-azure-cosmos-container"></a>Provisionner du débit sur un conteneur Azure Cosmos

Cet article explique comment provisionner le débit sur un conteneur (collection, graphe ou table) dans Azure Cosmos DB. Vous pouvez provisionner le débit sur un seul conteneur, ou [provisionner le débit sur une base de données](how-to-provision-database-throughput.md) et le partager entre les conteneurs de la base de données. Vous pouvez provisionner le débit sur un conteneur à l’aide du portail Azure, d’Azure CLI ou des SDK Azure Cosmos DB.

## <a name="provision-throughput-using-azure-portal"></a>Approvisionner le débit à l’aide du portail Azure

1. Connectez-vous au [Portail Azure](https://portal.azure.com/).

1. [Créez un compte Azure Cosmos](create-sql-api-dotnet.md#create-account) ou sélectionnez un compte Azure Cosmos existant.

1. Ouvrez le volet **Explorateur de données**, puis sélectionnez **Nouvelle collection**. Fournissez ensuite les détails suivants :

   * Indiquez si vous créez une base de données ou si vous utilisez une base de données existante.
   * Entrez un ID de conteneur (ou de table ou de graphe).
   * Entrez une valeur de clé de partition (par exemple `/userid`).
   * Entrez un débit que vous voulez provisionner (par exemple, 1 000 unités de requête).
   * Sélectionnez **OK**.

![Capture d’écran de l’Explorateur de données, avec l’option Nouvelle collection mise en surbrillance](./media/how-to-provision-container-throughput/provision-container-throughput-portal-all-api.png)

## <a name="provision-throughput-using-azure-cli"></a>Approvisionner le débit à l’aide d’Azure CLI

```azurecli-interactive
# Create a container with a partition key and provision throughput of 400 RU/s
az cosmosdb collection create \
    --resource-group $resourceGroupName \
    --collection-name $containerName \
    --name $accountName \
    --db-name $databaseName \
    --partition-key-path /myPartitionKey \
    --throughput 400
```

## <a name="provision-throughput-using-powershell"></a>Provisionner le débit à l’aide de PowerShell

```azurepowershell-interactive
# Create a container with a partition key and provision throughput of 400 RU/s
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container1"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

$ContainerProperties = @{
    "resource"=@{
        "id"=$containerName;
        "partitionKey"=@{
            "paths"=@("/myPartitionKey");
            "kind"="Hash"
        }
    };
    "options"=@{ "Throughput"= 400 }
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties
```

Si vous provisionnez le débit sur un conteneur dans un compte Azure Cosmos configuré avec l’API Azure Cosmos DB pour MongoDB, utilisez `/myShardKey` pour le chemin de clé de partition. Si vous provisionnez le débit sur un conteneur dans un compte Azure Cosmos configuré avec l’API Cassandra, utilisez `/myPrimaryKey` pour le chemin de clé de partition.

## <a name="provision-throughput-by-using-net-sdk"></a>Provisionner le débit à l’aide du kit SDK .NET

> [!Note]
> Utilisez les SDK Cosmos pour l’API SQL afin de provisionner le débit de toutes les API Cosmos DB, à l’exception de l’API Cassandra.

### <a id="dotnet-most"></a>API SQL, MongoDB, Gremlin et Table

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

### <a id="dotnet-cassandra"></a>API Cassandra

```csharp
// Create a Cassandra table with a partition (primary) key and provision throughput of 400 RU/s
session.Execute(CREATE TABLE myKeySpace.myTable(
    user_id int PRIMARY KEY,
    firstName text,
    lastName text) WITH cosmosdb_provisioned_throughput=400);
```

## <a name="next-steps"></a>Étapes suivantes

Consultez les articles suivants pour en savoir plus sur le provisionnement du débit dans Azure Cosmos DB :

* [Guide pratique pour provisionner le débit sur une base de données](how-to-provision-database-throughput.md)
* [Unités de requête et débit dans Azure Cosmos DB](request-units.md)
