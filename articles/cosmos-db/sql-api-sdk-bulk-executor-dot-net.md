---
title: 'Azure Cosmos DB : SDK, ressources et API .NET de l’exécuteur en bloc | Microsoft Docs'
description: Tout savoir sur l’API .NET et le SDK de l’exécuteur en bloc, notamment les dates de publication, les dates de déclassement et les modifications apportées entre chaque version du SDK .NET de l’exécuteur en bloc Azure Cosmos DB.
services: cosmos-db
documentationcenter: .net
author: tknandu
manager: kfile
editor: cgronlun
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 05/07/2018
ms.author: ramkris
ms.openlocfilehash: 52dcd8497a6dad2885a27197f873de4603378501
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2018
---
# <a name="net-bulkexecutor-library-download-information"></a>Bibliothèque BulkExecutor .NET : informations sur le téléchargement 

> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [Flux de modification .NET](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.JS](sql-api-sdk-node.md)
> * [Java asynchrone](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [API REST Resource Provider](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)
> * [BulkExecutor - .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [BulkExecutor - Java](sql-api-sdk-bulk-executor-java.md)

<table>

<tr><td>**Description**</td><td>La bibliothèque BulkExecutor permet aux applications clientes d’effectuer des opérations en bloc dans des comptes Azure Cosmos DB. La bibliothèque BulkExecutor fournit les espaces de noms BulkImport et BulkUpdate. Le module BulkImport peut ingérer en bloc des documents de manière optimisée afin que le débit provisionné pour une collection soit consommé dans ses limites maximales. Le module BulkUpdate peut mettre à jour en bloc des données existantes dans des conteneurs Azure Cosmos DB sous forme de correctifs.</td></tr>

<tr><td>**Téléchargement du Kit de développement logiciel (SDK)**</td><td>[NuGet](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.BulkExecutor/)</td></tr>

<tr><td>**Bibliothèque BulkExecutor dans GitHub**</td><td>[GitHub](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started)</td></tr>

<tr><td>**Documentation de l’API**</td><td>[Documentation de référence sur l’API .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor?view=azure-dotnet)</td></tr>

<tr><td>**Bien démarrer**</td><td>[Bien démarrer avec le kit SDK .NET de la bibliothèque BulkExecutor](bulk-executor-dot-net.md)</td></tr>

<tr><td>**Infrastructure actuellement prise en charge**</td><td><ul><li>[Microsoft.Azure.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/)(version >= 1.21.1)</li><li>
[Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/)(version >= 10.0.2)
</li></ul></td></tr>
</table></br>

