---
title: Présentation du Stockage Table - Stockage d’objets dans Azure | Microsoft Docs
description: Stockez des données structurées dans le cloud à l’aide du stockage de tables Azure, un magasin de données NoSQL.
services: storage
ms.service: storage
author: tamram
ms.author: tamram
ms.devlang: dotnet
ms.topic: overview
ms.date: 05/27/2021
ms.subservice: tables
ms.openlocfilehash: 690afd54cef64f893bf5e68d537de4a47f1a93db
ms.sourcegitcommit: 1b698fb8ceb46e75c2ef9ef8fece697852c0356c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2021
ms.locfileid: "110654948"
---
# <a name="what-is-azure-table-storage-"></a>Qu’est-ce que le Stockage Table Azure ? 

[!INCLUDE [storage-table-cosmos-db-tip-include](../../../includes/storage-table-cosmos-db-tip-include.md)]

Stockage Table Azure est un service qui stocke dans le cloud des données structurées non relationnelles (également appelées données NoSQL structurées), fournissant un magasin de clés/attributs reposant sur une conception sans schéma. Comme le stockage de tables est sans schéma, il est aisé d’adapter vos données en fonction des besoins de votre application. L’accès aux données du Stockage Table est rapide et économique pour de nombreux types d’applications, et généralement moins coûteux que le SQL traditionnel pour des volumes de données similaires.

Vous pouvez utiliser le Stockage Table pour stocker des jeux de données flexibles, comme des données utilisateur pour des applications Web, des carnets d’adresses, des informations sur les périphériques ou d’autres types de métadonnées requis par votre service. Vous pouvez stocker un nombre quelconque d'entités dans une table, et un compte de stockage peut contenir un nombre quelconque de tables, jusqu'à la limite de capacité du compte de stockage.

[!INCLUDE [storage-table-concepts-include](../../../includes/storage-table-concepts-include.md)]

## <a name="next-steps"></a>Étapes suivantes

* [Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md) est une application autonome et gratuite de Microsoft qui vous permet d’exploiter visuellement les données de Stockage Azure sur Windows, macOS et Linux.

* [Prise en main de Stockage Table Azure dans .NET](../../cosmos-db/tutorial-develop-table-dotnet.md)

* Pour plus d'informations sur les API disponibles, consultez la documentation de référence du service de Table :

    * [Référence de la bibliothèque cliente de stockage pour .NET](/dotnet/api/overview/azure/storage)

    * [Référence d’API REST](/rest/api/storageservices/)