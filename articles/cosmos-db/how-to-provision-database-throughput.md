---
title: Approvisionner le débit d’une base de données dans Azure Cosmos DB
description: Apprenez à approvisionner le débit au niveau de la base de données dans Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 11/06/2018
ms.author: mjbrown
ms.openlocfilehash: c648522e689c64de8e7e09b85ca3b6eb26b6945b
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/31/2019
ms.locfileid: "55477197"
---
# <a name="provision-throughput-on-an-azure-cosmos-database"></a>Provisionner du débit sur une base de données Azure Cosmos

Cet article explique comment approvisionner le débit d’une base de données dans Azure Cosmos DB. Vous pouvez approvisionner le débit d’un seul [conteneur](how-to-provision-container-throughput.md) ou d’une base de données et le partager entre les conteneurs au sein de la base de données. Vous pouvez approvisionner le débit au niveau d’une base de données avec le portail Azure ou les kits de développement logiciel (SDK) Cosmos DB.

## <a name="provision-throughput-using-azure-portal"></a>Approvisionner le débit à l’aide du portail Azure

### <a id="portal-sql"></a>API (Core) SQL

1. Connectez-vous au [portail Azure](https://portal.azure.com/).

1. [Créez un nouveau compte Cosmos DB](create-sql-api-dotnet.md#create-a-database-account) ou sélectionnez un compte existant.

1. Ouvrez le volet **Explorateur de données** et sélectionnez **Nouvelle base de données**. Ensuite, renseignez le formulaire avec les informations suivantes :

   * Entrez un ID de base de données. 
   * Sélectionnez Approvisionner le débit.
   * Entrez un débit, par exemple 1 000 unités de requête.
   * Sélectionnez **OK**.

![L’API SQL approvisionne le débit d’une base de données](./media/how-to-provision-database-throughput/provision-database-throughput-portal-all-api.png)

## <a name="provision-throughput-using-net-sdk"></a>Approvisionner le débit à l’aide du Kit de développement logiciel (SDK) .NET

> [!Note]
> Utilisez l’API SQL pour approvisionner le débit de toutes les API. Vous pouvez éventuellement utiliser l’exemple ci-dessous pour l’API Cassandra.

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

Consultez les articles suivants pour en savoir plus sur l’approvisionnement de débit dans Cosmos DB :

* [Approvisionner le débit d’un conteneur](how-to-provision-container-throughput.md)
* [Unités de requête et débit dans Azure Cosmos DB](request-units.md)
