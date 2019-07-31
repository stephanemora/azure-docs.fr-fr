---
title: 'Azure Cosmos DB : Ressources, kit SDK et API .NET Standard SQL'
description: Tout savoir sur l’API SQL et le Kit de développement logiciel (SDK) .NET, y compris les dates de sortie, les dates de déclassement et les modifications effectuées entre chaque version du Kit de développement logiciel (SDK) .NET Azure Cosmos DB.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 07/12/2019
ms.author: dech
ms.openlocfilehash: 01645e66907e01b2e8fcb771976b50e5bbc8abf4
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/15/2019
ms.locfileid: "68228952"
---
# <a name="azure-cosmos-db-net-standard-sdk-for-sql-api-download-and-release-notes"></a>Kit de développement logiciel .NET Standard Azure Cosmos DB pour l’API SQL : Téléchargement et notes de publication
> [!div class="op_single_selector"]
> * [.NET Standard](sql-api-sdk-dotnet-standard.md)
> * [.NET](sql-api-sdk-dotnet.md)
> * [Flux de modification .NET](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.JS](sql-api-sdk-node.md)
> * [Java asynchrone](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [API REST Resource Provider](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [BulkExecutor - .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [BulkExecutor - Java](sql-api-sdk-bulk-executor-java.md)

| |  |
|---|---|
|**Téléchargement du Kit de développement logiciel (SDK)**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/)|
|**Documentation de l’API**|[Documentation de référence sur l’API .NET](/dotnet/api/overview/azure/cosmosdb?view=azure-dotnet)|
|**Exemples**|[Exemples de code .NET](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/CodeSamples)|
|**Prise en main**|[Prise en main du Kit de développement logiciel (SDK) .NET Azure Cosmos DB](sql-api-get-started.md)|
|**Didacticiel d’application web**|[Développement d’applications web avec Azure Cosmos DB](sql-api-dotnet-application.md)|
|**Infrastructure actuellement prise en charge**|[Microsoft .NET Standard 2.0](/dotnet/standard/net-standard)|

## <a name="release-notes"></a>Notes de publication

### <a name="a-name300300"></a><a name="3.0.0"/>3.0.0 
* Disponibilité générale de la [version 3.0.0](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/) du kit de développement logiciel (SDK) .NET
* Cible .NET Standard 2.0, prenant en charge .NET framework 2.0+ et .NET Core 4.6.1+
* Nouveau modèle d’objet, avec CosmosClient de niveau supérieur et les méthodes réparties entre les classes Database et Container appropriées
* Nouvelles API de flux hautement performantes
* Prise en charge intégrée des API du processeur de flux de modification
* API du générateur Fluent pour CosmosClient, Container et le processeur de flux de modification
* API de gestion du débit idiomatique
* RequestOptions et ResponseTypes détaillées pour les requêtes de base de données, de conteneur, d’élément, de requête et de débit
* Possibilité de mettre à l’échelle des conteneurs non partitionnés 
* Sérialiseur extensible et personnalisable
* Pipeline de requêtes extensible avec prise en charge des gestionnaires personnalisés


## <a name="release--retirement-dates"></a>Dates de lancement et de suppression
Microsoft envoie une notification au moins **12 mois** avant le retrait d’un Kit de développement logiciel (SDK) pour faciliter la transition vers une version plus récente/prise en charge.

Les nouvelles fonctionnalités et fonctions, et les optimisations sont uniquement ajoutées au Kit SDK actuel. Par conséquent, il est recommandé de toujours passer à la dernière version du SDK dès que possible. 

Toute requête envoyée à Azure Cosmos DB à l’aide d’un Kit de développement logiciel (SDK) supprimé est rejetée par le service.

<br/>

| Version | Date de lancement | Date de suppression |
| --- | --- | --- |
| [3.0.0](#3.0.0) |15 juillet 2019 |--- |

## <a name="faq"></a>Forum Aux Questions
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Voir aussi
Pour en savoir plus sur Cosmos DB, consultez la page du service [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/). 

