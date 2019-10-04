---
title: Prise en charge de Stockage Table Azure dans Azure Cosmos DB
description: Découvrez comment collaborent l’API Table Azure Cosmos DB et Stockage Table Azure.
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: overview
ms.date: 08/05/2019
author: wmengmsft
ms.author: wmeng
ms.reviewer: sngun
ms.openlocfilehash: 71bc5d73a7b5bc83dc1ac835c80ac1b14d5113e9
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/06/2019
ms.locfileid: "68814703"
---
# <a name="developing-with-azure-cosmos-db-table-api-and-azure-table-storage"></a>Développement avec l’API Table Azure Cosmos DB et Stockage Table Azure

L’API Table Azure Cosmos DB et Stockage Table Azure partagent le même modèle de données de table et exposent les mêmes opérations de création, suppression, mise à jour et interrogation par le biais de leurs SDK. 

[!INCLUDE [storage-table-cosmos-comparison](../../includes/storage-table-cosmos-comparison.md)]

## <a name="developing-with-the-azure-cosmos-db-table-api"></a>Développement avec l’API Table Azure Cosmos DB

À ce stade, [l’API Table de Azure Cosmos DB](table-introduction.md) a quatre kits disponibles pour le développement : 

* [Microsoft.Azure.Cosmos.Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) : SDK .NET. Cette bibliothèque cible .NET Standard et présente les mêmes classes et signatures de méthode que le [SDK Stockage Microsoft Azure](https://www.nuget.org/packages/WindowsAzure.Storage) public, mais elle peut également se connecter à des comptes Azure Cosmos DB à l’aide de l’API Table. Il est recommandé aux utilisateurs de la bibliothèque .NET Framework [Microsoft.Azure.CosmosDB.Table](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table/) de mettre à niveau vers [Microsoft.Azure.Cosmos.Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table), car elle est en mode maintenance et sera bientôt dépréciée.

* [SDK Python](table-sdk-python.md) : Le nouveau SDK Azure Cosmos DB Python est le seul SDK qui prend en charge le stockage Table Azure dans Python. Ce kit de développement logiciel se connecte à la fois avec l’API Table Azure Cosmos DB et le Stockage Table Azure.

* [SDK Java](table-sdk-java.md) : Ce kit de développement pour le stockage Azure a la possibilité de se connecter à des comptes Azure Cosmos DB à l’aide de l’API Table.

* [SDK Node.js](table-sdk-nodejs.md) : Ce kit de développement pour le stockage Azure a la possibilité de se connecter à des comptes Azure Cosmos DB à l’aide de l’API Table.


Des informations supplémentaires sur l’utilisation de l’API Table sont disponibles dans l’article [FAQ : Développer avec l’API Table](faq.md#table).

## <a name="developing-with-azure-table-storage"></a>Développement avec Stockage de tables Azure

Ces Kit SDK sont disponibles pour le développement avec le stockage Table Azure :

- [Kit SDK WindowsAzure.Storage .NET](https://www.nuget.org/packages/WindowsAzure.Storage/). Cette bibliothèque vous permet de travailler avec le service de Table de stockage.
- [Kit SDK Python](https://github.com/Azure/azure-cosmos-table-python). Le SDK Table Azure Cosmos DB pour Python prend en charge le service Stockage Table (parce que le service Stockage Table Azure et l’API Table de Cosmos DB partagent les mêmes caractéristiques et fonctionnalités, et dans le but de factoriser nos efforts de développement de SDK, nous vous recommandons d’utiliser ce SDK).
- [Kit SDK Stockage Azure pour Java](https://github.com/azure/azure-storage-java). Ce kit SDK du Stockage Azure fournit une bibliothèque cliente en Java pour consommer le stockage Table Azure.
- [Kit SDK Node.js](https://github.com/Azure/azure-storage-node). Ce kit SDK fournit un package Node.js et une bibliothèque de client compatible avec le navigateur JavaScript afin de consommer le service de Table de stockage.
- [Module PowerShell AzureRmStorageTable](https://www.powershellgallery.com/packages/AzureRmStorageTable). Ce module PowerShell comporte des cmdlet permettant de travailler avec des Tables de stockage.
- [Bibliothèque de client de stockage Azure pour C++](https://github.com/Azure/azure-storage-cpp/). Cette bibliothèque vous permet de créer des applications sur Stockage Azure.
- [Bibliothèque de client du service de Table de Stockage Azure pour Ruby](https://github.com/azure/azure-storage-ruby/tree/master/table). Ce projet fournit un package Ruby qui facilite l’accès aux services de Table de stockage Azure.
- [Bibliothèque de client PHP du service de Table de Stockage Azure pour Ruby](https://github.com/Azure/azure-storage-php/tree/master/azure-storage-table). Ce projet fournit une bibliothèque de client PHP qui facilite l’accès aux services de Table de stockage Azure.


   





