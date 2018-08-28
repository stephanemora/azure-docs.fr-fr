---
title: Prise en charge de Stockage Table Azure dans Azure Cosmos DB | Microsoft Docs
description: Découvrez comment collaborent l’API Table Azure Cosmos DB et Stockage Table Azure.
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-table
ms.devlang: na
ms.topic: overview
ms.date: 11/15/2017
ms.author: sngun
ms.openlocfilehash: a6ac954caa8341d6d949811f0bb9d7e68c0b5aac
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/20/2018
ms.locfileid: "42022335"
---
# <a name="developing-with-azure-cosmos-db-table-api-and-azure-table-storage"></a>Développement avec l’API Table Azure Cosmos DB et Stockage Table Azure

L’API Table Azure Cosmos DB et Stockage Table Azure partagent le même modèle de données de table et exposent les mêmes opérations de création, suppression, mise à jour et interrogation par le biais de leurs SDK. 

[!INCLUDE [storage-table-cosmos-comparison](../../includes/storage-table-cosmos-comparison.md)]

## <a name="developing-with-the-azure-cosmos-db-table-api"></a>Développement avec l’API Table Azure Cosmos DB

À ce stade, [l’API Table de Azure Cosmos DB](table-introduction.md) a quatre kits disponibles pour le développement : 
- Kit de développement logiciel (SDK) .NET [Microsoft.Azure.CosmosDB.Table](https://aka.ms/tableapinuget). Cette bibliothèque présente les mêmes classes et signatures de méthode que le [SDK Stockage Microsoft Azure](https://www.nuget.org/packages/WindowsAzure.Storage) public, mais elle peut également se connecter à des comptes Azure Cosmos DB à l’aide de l’API Table. Notez que la bibliothèque `Microsoft.Azure.CosmosDB.Table` est actuellement disponible uniquement pour .NET Standard, elle n’est pas encore disponible pour .NET Core.
- [Kit de développement logiciel (SDK) Python](table-sdk-python.md). Le nouveau SDK Azure Cosmos DB Python est le seul SDK qui prend en charge le stockage Table Azure dans Python. Ce kit de développement logiciel se connecte à la fois avec l’API Table Azure Cosmos DB et le Stockage Table Azure.
- [Kit SDK Java](table-sdk-java.md). Ce kit de développement pour le stockage Azure a la possibilité de se connecter à des comptes Azure Cosmos DB à l’aide de l’API Table.
- [Kit de développement logiciel (SDK) Node.js](table-sdk-nodejs.md). Ce kit de développement pour le stockage Azure a la possibilité de se connecter à des comptes Azure Cosmos DB à l’aide de l’API Table.

Des informations supplémentaires sur l’utilisation de l’API Table sont disponibles dans l’article [FAQ : Développer avec l’API Table](faq.md#develop-with-the-table-api).

## <a name="developing-with-azure-table-storage"></a>Développement avec Stockage de tables Azure

Ces Kit de développement logiciel (SDK) sont disponibles pour le développement avec le stockage de tables Azure :

- [Kit de développement logiciel (SDK) WindowsAzure.Storage .NET](https://www.nuget.org/packages/WindowsAzure.Storage/). Cette bibliothèque vous permet de travailler avec le service de Table de stockage.
- [Kit de développement logiciel (SDK) Python](table-sdk-python.md). Le Kit de développement logiciel (SDK) Table d’Azure Cosmos DB pour Python prend également en charge le service de Table de stockage.
- [Kit de développement logiciel (SDK) Stockage Azure pour Java](https://github.com/azure/azure-storage-java). Ce Kit de développement logiciel (SDK) du Stockage Azure fournit une bibliothèque cliente en Java pour consommer le stockage de tables Azure.
- [Kit de développement logiciel (SDK) Node.js](table-sdk-nodejs.md). Ce Kit de développement logiciel (SDK) fournit un package Node.js et une bibliothèque de client compatible avec le navigateur JavaScript afin de consommer le service de Table de stockage.
- [Module PowerShell AzureRmStorageTable](https://www.powershellgallery.com/packages/AzureRmStorageTable/1.0.0.7). Ce module PowerShell comporte des cmdlet permettant de travailler avec des Tables de stockage.
- [Bibliothèque de client de stockage Azure pour C++](https://github.com/Azure/azure-storage-cpp/). Cette bibliothèque vous permet de créer des applications sur Stockage Azure.
- [Bibliothèque de client du service de Table de Stockage Azure pour Ruby](https://github.com/azure/azure-storage-ruby/tree/master/table). Ce projet fournit un package Ruby qui facilite l’accès aux services de Table de stockage Azure.
- [Bibliothèque de client PHP du service de Table de Stockage Azure pour Ruby](https://github.com/Azure/azure-storage-php/tree/master/azure-storage-table). Ce projet fournit une bibliothèque de client PHP qui facilite l’accès aux services de Table de stockage Azure.


   





