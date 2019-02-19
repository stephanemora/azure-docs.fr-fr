---
title: Approvisionner le débit d’une base de données dans Azure Cosmos DB
description: Apprenez à approvisionner le débit au niveau de la base de données dans Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 11/06/2018
ms.author: mjbrown
ms.openlocfilehash: 759adf95604e66209cf3ec5083246d16e952114a
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55884186"
---
# <a name="provision-throughput-for-a-database-in-azure-cosmos-db"></a>Approvisionner le débit d’une base de données dans Azure Cosmos DB

Cet article explique comment approvisionner le débit d’une base de données dans Azure Cosmos DB. Vous pouvez approvisionner le débit d’un seul [conteneur](how-to-provision-container-throughput.md) ou d’une base de données et le partager entre les conteneurs au sein de la base de données. Vous pouvez provisionner le débit au niveau d’une base de données via le portail Azure ou à l’aide des kits SDK Azure Cosmos DB.

## <a name="provision-throughput-by-using-azure-portal"></a>Approvisionner le débit à l’aide du portail Azure

### <a id="portal-sql"></a>API (Core) SQL

1. Connectez-vous au [Portail Azure](https://portal.azure.com/).

1. [Créez un compte Azure Cosmos DB](create-sql-api-dotnet.md#create-a-database-account), ou sélectionnez un compte existant.

1. Ouvrez le volet **Explorateur de données**, puis sélectionnez **Nouvelle base de données**. Fournissez les détails suivants :

   * Entrez un ID de base de données. 
   * Sélectionnez **Provisionner le débit**.
   * Entrez un débit, par exemple 1 000 RU.
   * Sélectionnez **OK**.

![Capture d’écran de la boîte de dialogue Nouvelle base de données](./media/how-to-provision-database-throughput/provision-database-throughput-portal-all-api.png)

## <a name="provision-throughput-by-using-net-sdk"></a>Provisionner le débit à l’aide du kit SDK .NET

> [!Note]
> Utilisez l’API SQL pour approvisionner le débit de toutes les API. Vous pouvez éventuellement utiliser l’exemple suivant pour l’API Cassandra.

### <a id="dotnet-all"></a>Toutes les API

```csharp
//set the throughput for the database
RequestOptions options = new RequestOptions
{
    OfferThroughput = 10000
};

//create the database
await client.CreateDatabaseIfNotExistsAsync(
    new Database {Id = databaseName},  
    options);
```

### <a id="dotnet-cassandra"></a>API Cassandra

```csharp
// Create a Cassandra keyspace and provision throughput of 10000 RU/s
session.Execute(CREATE KEYSPACE IF NOT EXISTS myKeySpace WITH cosmosdb_provisioned_throughput=10000);
```

## <a name="next-steps"></a>Étapes suivantes

Consultez les articles suivants pour en savoir plus sur le provisionnement du débit dans Azure Cosmos DB :

* [Approvisionner le débit d’un conteneur](how-to-provision-container-throughput.md)
* [Unités de requête et débit dans Azure Cosmos DB](request-units.md)
