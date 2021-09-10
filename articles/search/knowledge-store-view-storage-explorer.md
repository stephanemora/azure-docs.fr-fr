---
title: Voir une base de connaissances avec l’Explorateur Stockage
titleSuffix: Azure Cognitive Search
description: Visualisez et analysez une base de connaissances Recherche cognitive Azure avec l’Explorateur Stockage du portail Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/10/2021
ms.openlocfilehash: ee6e17e6fe52125d75f2782b1fa77215045787a3
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122532233"
---
# <a name="view-a-knowledge-store-with-storage-explorer"></a>Voir une base de connaissances avec l’Explorateur Stockage

Une [base de connaissances](knowledge-store-concept-intro.md) est créée par un ensemble de compétences et enregistrée dans Stockage Azure. Cet article explique comment afficher le contenu d’une base de connaissances à l’aide de Explorateur Stockage dans le portail Azure.

## <a name="prerequisites"></a>Configuration requise

+ Créez une base de connaissances dans le [portail Azure](knowledge-store-create-portal.md) ou via [Postman et des API REST](knowledge-store-create-rest.md).

+ Vous aurez également besoin du nom du compte de stockage Azure contenant la base de connaissances, ainsi que de sa clé d’accès provenant du portail Azure.

## <a name="start-storage-explorer"></a>Démarrez Explorateur Stockage.

1. Dans le portail Azure, [ouvrez le compte de stockage](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/) que vous avez utilisé pour créer la base de connaissances.

1. Dans le volet de navigation de gauche du compte de stockage, cliquez sur **Explorateur Stockage**.

## <a name="view-edit-and-query-tables"></a>Afficher, modifier et interroger des tables

Les procédures pas à pas pour le portail et REST créent une base de connaissances dans Stockage Table.

1. Développez la liste **TABLES** pour afficher la liste des projections de tables Azure créées durant la création de la base de connaissances. Le contenu des tables doit avoir trait aux évaluations d’hôtels.

1. Sélectionnez une table pour voir les données enrichies, notamment les phrases clés et les scores de sentiment.

   ![Afficher des tables dans l’Explorateur Stockage](media/knowledge-store-view-storage-explorer/storage-explorer-tables.png "Afficher des tables dans l’Explorateur Stockage")

1. Pour changer le type de données d’une valeur de table ou pour changer des valeurs individuelles dans votre table, cliquez sur **Modifier**. Quand vous changez le type de données d’une colonne dans une ligne de table, il s’applique à toutes les lignes.

   ![Modifier une table dans l’Explorateur Stockage](media/knowledge-store-view-storage-explorer/storage-explorer-edit-table.png "Modifier une table dans l’Explorateur Stockage")

1. Pour exécuter des requêtes, cliquez sur **Requête** dans la barre de commandes, puis entrez vos conditions.  

   ![Interroger une table dans l’Explorateur Stockage](media/knowledge-store-view-storage-explorer/storage-explorer-query-table.png "Interroger une table dans l’Explorateur Stockage")

## <a name="next-steps"></a>Étapes suivantes

Connectez cette base de connaissances à Power BI pour effectuer une analyse plus approfondie, ou allez plus loin avec le code en utilisant l’API REST et Postman pour créer une autre base de connaissances.

> [!div class="nextstepaction"]
> [Connexion avec Power BI](knowledge-store-connect-power-bi.md)
