---
title: Prise en charge de Stockage Table Azure dans Azure Cosmos DB
description: Découvrez comment l’API Table Azure Cosmos DB et les tables de stockage Azure fonctionnent ensemble en partageant le même modèle de données de table et les mêmes opérations.
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: how-to
ms.date: 08/25/2021
author: sakash279
ms.author: akshanka
ms.reviewer: sngun
ms.openlocfilehash: f2d337f7154e2ae9412930aab9720a47bde4768e
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124762034"
---
# <a name="developing-with-azure-cosmos-db-table-api-and-azure-table-storage"></a>Développement avec l’API Table Azure Cosmos DB et Stockage Table Azure
[!INCLUDE[appliesto-table-api](../includes/appliesto-table-api.md)]

L’API Table Azure Cosmos DB et Stockage Table Azure partagent le même modèle de données de table et exposent les mêmes opérations de création, suppression, mise à jour et interrogation par le biais de leurs SDK.

> [!NOTE]
> Le [mode de capacité serverless](../serverless.md) est maintenant disponible sur l’API Table d’Azure Cosmos DB.

[!INCLUDE [storage-table-cosmos-comparison](../../../includes/storage-table-cosmos-comparison.md)]

## <a name="azure-sdks"></a>SDK Azure

### <a name="current-release"></a>Version actuelle

Les packages du kit de développement logiciel (SDK) suivants fonctionnent avec l’API Table Azure Cosmos et le Stockage Table Azure.

* **.NET** : use les [Azure.Data.Tables](https://www.nuget.org/packages/Azure.Data.Tables/) disponibles sur NuGet.

* **Python** : utilisez les [azure-data-tables](https://pypi.org/project/azure-data-tables/) disponibles auprès de PyPi.

* **JavaScript/TypeScript** : utilisez le package [@azure/data-tables](https://www.npmjs.com/package/@azure/data-tables) disponible sur npm.js.  

* **Java** : utilisez le package [azure-data-tables](https://mvnrepository.com/artifact/com.azure/azure-data-tables/12.0.0) disponible sur Maven.

### <a name="prior-releases"></a>Versions antérieures

Les packages de kit de développement logiciel (SDK) suivants fonctionnent uniquement avec l’API Table Azure Cosmos DB.

* **.NET** - [Microsoft.Azure.Cosmos.Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) disponible sur NuGet.  Cette bibliothèque fonctionne uniquement avec l’API Table Cosmos DB.

* **Python** - [azure-cosmosdb-table](https://pypi.org/project/azure-cosmosdb-table/) disponible auprès de PyPi. Ce kit de développement logiciel se connecte à la fois avec l’API Table Azure Cosmos DB et le Stockage Table Azure.

* **JavaScript/TypeScript** - Package [azure-storage](https://www.npmjs.com/package/azure-storage) disponible sur npm.js. Ce kit de développement pour le stockage Azure a la possibilité de se connecter à des comptes Azure Cosmos DB à l’aide de l’API Table.

* **Java** - [Kit de développement logiciel (SDK) client Stockage Microsoft Azure pour Java](https://mvnrepository.com/artifact/com.microsoft.azure/azure-storage) sur Maven. Ce kit de développement pour le stockage Azure a la possibilité de se connecter à des comptes Azure Cosmos DB à l’aide de l’API Table.

* **C++**   - [Bibliothèque cliente Stockage Azure pour C++](https://github.com/Azure/azure-storage-cpp/). Cette bibliothèque vous permet de créer des applications sur Stockage Azure.

* **Ruby** - [Bibliothèque de client du service Table de Stockage Azure pour Ruby](https://github.com/azure/azure-storage-ruby/tree/master/table). Ce projet fournit un package Ruby qui facilite l’accès aux services de Table de stockage Azure.

* **PHP** - [Bibliothèque de client PHP du service Table de Stockage Azure](https://github.com/Azure/azure-storage-php/tree/master/azure-storage-table). Ce projet fournit une bibliothèque de client PHP qui facilite l’accès aux services de Table de stockage Azure.

* **PowerShell** - [Module PowerShell AzureRmStorageTable](https://www.powershellgallery.com/packages/AzureRmStorageTable). Ce module PowerShell comporte des cmdlet permettant de travailler avec des Tables de stockage.
