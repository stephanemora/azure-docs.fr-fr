---
title: Approvisionner le débit d’une base de données dans Azure Cosmos DB
description: Apprenez à approvisionner le débit au niveau de la base de données dans Azure Cosmos DB à l’aide du Portail Azure, de l’interface CLI, de PowerShell et des différents Kits de développement logiciel (SDK).
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.date: 09/28/2019
ms.author: mjbrown
ms.custom: devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 668aa51bdb57dc4bcde0e3a95c481bb60e3d8ed3
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88997368"
---
# <a name="provision-standard-manual-throughput-on-a-database-in-azure-cosmos-db"></a>Approvisionner le débit standard (manuel) sur une base de données Azure Cosmos DB

Cet article explique comment approvisionner le débit standard (manuel) sur une base de données dans Azure Cosmos DB. Vous pouvez approvisionner le débit d’un seul [conteneur](how-to-provision-container-throughput.md) ou d’une base de données et le partager entre les conteneurs au sein de la base de données. Pour savoir quand utiliser le débit au niveau du conteneur et au niveau de la base de données, voir [Cas d’usage de l’approvisionnement du débit des conteneurs et des bases de données](set-throughput.md). Vous pouvez provisionner le débit au niveau d’une base de données via le portail Azure ou à l’aide des kits SDK Azure Cosmos DB.

## <a name="provision-throughput-using-azure-portal"></a>Approvisionner le débit à l’aide du portail Azure

### <a name="sql-core-api"></a><a id="portal-sql"></a>API (Core) SQL

1. Connectez-vous au [portail Azure](https://portal.azure.com/).

1. [Créez un compte Azure Cosmos](create-sql-api-dotnet.md#create-account) ou sélectionnez un compte Azure Cosmos existant.

1. Ouvrez le volet **Explorateur de données**, puis sélectionnez **Nouvelle base de données**. Fournissez les détails suivants :

   * Entrez un ID de base de données.
   * Sélectionnez **Provisionner le débit**.
   * Entrez un débit, par exemple 1 000 RU.
   * Sélectionnez **OK**.

    :::image type="content" source="./media/how-to-provision-database-throughput/provision-database-throughput-portal-all-api.png" alt-text="Capture d’écran de la boîte de dialogue Nouvelle base de données":::

## <a name="provision-throughput-using-azure-cli-or-powershell"></a>Configurer le débit à l’aide d’Azure CLI ou de PowerShell

Pour créer une base de données avec débit partagé, voir

* [Créer une base de données à l’aide d’Azure CLI](manage-with-cli.md#create-a-database-with-shared-throughput)
* [Créer une base de données à l’aide de PowerShell](manage-with-powershell.md#create-db-ru)

## <a name="provision-throughput-using-net-sdk"></a>Approvisionner le débit à l’aide du Kit de développement logiciel (SDK) .NET

> [!Note]
> Vous pouvez utiliser les SDK Cosmos pour l’API SQL afin de provisionner le débit de toutes les API. Vous pouvez éventuellement utiliser l’exemple suivant pour l’API Cassandra.

### <a name="all-apis"></a><a id="dotnet-all"></a>Toutes les API

# <a name="net-sdk-v2"></a>[Kit de développement logiciel (SDK) .NET V2](#tab/dotnetv2)

```csharp
//set the throughput for the database
RequestOptions options = new RequestOptions
{
    OfferThroughput = 500
};

//create the database
await client.CreateDatabaseIfNotExistsAsync(
    new Database {Id = databaseName},  
    options);
```

# <a name="net-sdk-v3"></a>[Kit de développement logiciel (SDK) .NET V3](#tab/dotnetv3)

[!code-csharp[](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos/tests/Microsoft.Azure.Cosmos.Tests/SampleCodeForDocs/DatabaseDocsSampleCode.cs?name=DatabaseCreateWithThroughput)]

---

### <a name="cassandra-api"></a><a id="dotnet-cassandra"></a>API Cassandra

Une commande similaire peut être exécutée avec n’importe quel pilote conforme à CQL.

```csharp
// Create a Cassandra keyspace and provision throughput of 400 RU/s
session.Execute("CREATE KEYSPACE IF NOT EXISTS myKeySpace WITH cosmosdb_provisioned_throughput=400");
```
 
## <a name="next-steps"></a>Étapes suivantes

Consultez les articles suivants pour en savoir plus sur le débit provisionné dans Azure Cosmos DB :

* [Mettre à l’échelle le débit provisionné au niveau global](scaling-throughput.md)
* [Provisionner le débit sur les conteneurs et les bases de données](set-throughput.md)
* [Comment approvisionner le débit standard (manuel) sur un conteneur](how-to-provision-container-throughput.md)
* [Comment approvisionner le débit de mise à l’échelle automatique pour un conteneur](how-to-provision-autoscale-throughput.md)
* [Unités de requête et débit dans Azure Cosmos DB](request-units.md)
