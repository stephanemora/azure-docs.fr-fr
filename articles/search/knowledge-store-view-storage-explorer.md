---
title: Voir une base de connaissances avec l’Explorateur Stockage
titleSuffix: Azure Cognitive Search
description: Visualisez et analysez une base de connaissances Recherche cognitive Azure avec l’Explorateur Stockage du portail Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/15/2021
ms.openlocfilehash: 8be37bfc85b56af67784fb1abb457da95b9565fc
ms.sourcegitcommit: 4abfec23f50a164ab4dd9db446eb778b61e22578
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130067038"
---
# <a name="view-a-knowledge-store-with-storage-explorer"></a>Voir une base de connaissances avec l’Explorateur Stockage

Un [magasin de connaissances](knowledge-store-concept-intro.md) est un contenu créé par des compétences Recherche cognitive Azure et enregistré dans Stockage Azure. Cet article explique comment afficher le contenu d’une base de connaissances à l’aide d’Explorateur Stockage dans le Portail Azure.

Commencez avec un magasin de connaissances existant créé dans le [Portail Azure](knowledge-store-create-portal.md) ou à l’aide des [API REST](knowledge-store-create-rest.md). Les procédures pas à pas pour le portail et REST créent une base de connaissances dans Stockage Table Azure.

## <a name="start-storage-explorer"></a>Démarrez Explorateur Stockage.

1. Dans le portail Azure, [ouvrez le compte de stockage](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/) que vous avez utilisé pour créer la base de connaissances.

1. Dans le volet de navigation de gauche du compte de stockage, sélectionnez **Explorateur Stockage**.

## <a name="view-edit-and-query-tables"></a>Afficher, modifier et interroger des tables

1. Développez la liste **TABLES** pour afficher la liste des projections de tables Azure créées durant la création de la base de connaissances. Si vous avez utilisé l’article de démarrage rapide ou REST pour créer le magasin de connaissances, les tables contiennent du contenu relatif aux révisions client d’un hôtel européen.

1. Sélectionnez une table dans la liste.

   ![Afficher des tables dans l’Explorateur Stockage](media/knowledge-store-view-storage-explorer/storage-explorer-tables.png "Afficher des tables dans l’Explorateur Stockage")

1. Pour modifier le type de données, le nom de la propriété ou les valeurs de données individuelles de votre table, cliquez sur **Modifier**.

   ![Modifier une table dans l’Explorateur Stockage](media/knowledge-store-view-storage-explorer/storage-explorer-edit-table.png "Modifier une table dans l’Explorateur Stockage")

1. Pour exécuter des requêtes, sélectionnez **Requête** dans la barre de commandes, puis entrez vos conditions.

   ![Interroger une table dans l’Explorateur Stockage](media/knowledge-store-view-storage-explorer/storage-explorer-query-table.png "Interroger une table dans l’Explorateur Stockage")

Dans Explorateur Stockage, vous ne pouvez interroger qu’une seule table à la fois à l’aide de la [syntaxe de requête prise en charge](/rest/api/storageservices/Querying-Tables-and-Entities). Pour interroger plusieurs tables, envisagez d’utiliser Power BI à la place.

## <a name="next-steps"></a>Étapes suivantes

Connectez ce magasin de connaissances pour Power BI afin de créer des visualisations qui incluent plusieurs tables.

> [!div class="nextstepaction"]
> [Connexion avec Power BI](knowledge-store-connect-power-bi.md)
