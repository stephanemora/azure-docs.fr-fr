---
title: 'Azure Cosmos DB : API .NET, Kit de développement logiciel (SDK) et ressources de l’exécuteur en bloc | Microsoft Docs'
description: Tout savoir sur l’API .NET et le Kit de développement logiciel (SDK) de l’exécuteur en bloc, notamment les dates de publication, les dates de déclassement et les modifications apportées entre chaque version du Kit de développement logiciel (SDK) .NET de l’exécuteur en bloc Azure Cosmos DB.
services: cosmos-db
author: tknandu
manager: kfile
editor: cgronlun
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 09/14/2018
ms.author: ramkris
ms.openlocfilehash: ffd8f438429cd8769ac0dbff7f489327166e0000
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/19/2018
ms.locfileid: "46294456"
---
# <a name="net-bulk-executor-library-download-information"></a>Bibliothèque de l’exécuteur en bloc .NET : informations sur le téléchargement 

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
> * [Exécuteur en bloc - .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [Exécuteur en bloc - Java](sql-api-sdk-bulk-executor-java.md)

<table>

<tr><td>**Description**</td><td>La bibliothèque de l’exécuteur en bloc permet aux applications clientes d’effectuer des opérations en bloc dans des comptes Azure Cosmos DB. La bibliothèque de l’exécuteur en bloc fournit les espaces de noms BulkImport, BulkUpdate et BulkDelete. Le module BulkImport peut ingérer en bloc des documents de manière optimisée afin que le débit provisionné pour une collection soit consommé dans ses limites maximales. Le module BulkUpdate peut mettre à jour en bloc des données existantes dans des conteneurs Azure Cosmos DB sous forme de correctifs. Le module BulkDelete peut supprimer en bloc des documents de manière optimisée afin que le débit provisionné pour une collection soit consommé dans ses limites maximales.</td></tr>

<tr><td>**Téléchargement du Kit de développement logiciel (SDK)**</td><td>[NuGet](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.BulkExecutor/)</td></tr>

<tr><td>**Bibliothèque BulkExecutor dans GitHub**</td><td>[GitHub](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started)</td></tr>

<tr><td>**Documentation de l’API**</td><td>[Documentation de référence sur l’API .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor?view=azure-dotnet)</td></tr>

<tr><td>**Démarrer**</td><td>[Bien démarrer avec le kit SDK .NET de la bibliothèque de l’exécuteur en bloc](bulk-executor-dot-net.md)</td></tr>

<tr><td>**Infrastructure actuellement prise en charge**</td><td><ul><li>[Microsoft.Azure.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/)(version >= 2.0.0)</li><li>
[Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/)(version >= 9.0.1)
</li></ul></td></tr>
</table></br>

## <a name="release-notes"></a>Notes de publication

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0

* Ajout de la prise en charge de l’opération BulkDelete pour les comptes d’API SQL Azure Cosmos DB.
* Ajout de la prise en charge de l’opération BulkImport pour les comptes d’API MongoDB Azure Cosmos DB.
* Amélioration de la dépendance du Kit de développement logiciel (SDK) .NET DocumentDB à la version 2.0.0. 

### <a name="a-name102102"></a><a name="1.0.2"/>1.0.2

* Ajout de la prise en charge de l’opération BulkImport pour les comptes d’API Gremlin Azure Cosmos DB.

### <a name="a-name101101"></a><a name="1.0.1"/>1.0.1

* Correction d’un bogue mineur dans l’opération BulkImport pour les comptes d’API SQL Azure Cosmos DB.

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0

* Ajout de la prise en charge des opérations BulkImport et BulkUpdate pour les comptes d’API SQL Azure Cosmos DB.
