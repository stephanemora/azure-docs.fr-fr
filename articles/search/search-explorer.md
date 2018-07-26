---
title: Explorateur de recherche pour l’interrogation des index dans Recherche Azure | Microsoft Docs
description: Découvrez comment utiliser l’Explorateur de recherche pour interroger des index dans Recherche Azure.
manager: cgronlun
author: HeidiSteen
services: search
ms.service: search
ms.topic: conceptual
ms.date: 07/10/2018
ms.author: heidist
ms.openlocfilehash: 520d9e7b1899c54d922ff6fb77e0901f9609b029
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/13/2018
ms.locfileid: "39004131"
---
# <a name="how-to-use-search-explorer-to-query-indexes-in-azure-search"></a>Comment utiliser l’Explorateur de recherche pour interroger des index dans Recherche Azure 

Cet article vous explique comment interroger un index de Recherche Azure à l’aide de l’**Explorateur de recherche** dans le Portail Azure. Vous pouvez utiliser l’Explorateur de recherche pour envoyer des chaînes de requête Lucene simples ou complètes à un index existant dans votre service.

## <a name="open-the-service-dashboard"></a>Ouverture du tableau de bord des services
1. Cliquez sur **Toutes les ressources** dans la barre de raccourcis de gauche du [portail Azure](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices).
2. Sélectionnez votre service Recherche Azure.

## <a name="select-an-index"></a>Sélection d’un index

Sélectionnez l’index dans lequel vous souhaitez effectuer la recherche à partir de la mosaïque **Index**.

   ![](./media/search-explorer/pick-index.png)

## <a name="open-search-explorer"></a>Ouvrir l’Explorateur de recherche

Cliquez sur la vignette de l’Explorateur de recherche pour ouvrir par glissement la barre de recherche et le volet des résultats.

   ![](./media/search-explorer/search-explorer-tile.png)

## <a name="start-searching"></a>Commencez la recherche

Lorsque vous utilisez l’Explorateur de recherche, vous pouvez spécifier des [paramètres de requête](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) pour formuler la requête.

1. Saisissez une requête dans **Chaîne de requête** puis appuyez sur **Rechercher**. 

   La chaîne de requête est automatiquement analysée dans l’URL de requête appropriée pour soumettre une requête HTTP à l’API REST de Recherche Azure.   
   
   Vous pouvez utiliser n’importe quelle syntaxe de requête Lucene simple ou complète valide pour créer la requête. Le caractère `*` équivaut à une recherche vide ou non spécifiée qui retourne tous les documents dans aucun ordre particulier.

2. Dans **Résultats**, les résultats de la requête sont présentés au format JSON brut, identiques à la charge utile retournée dans un corps de réponse HTTP lors de l’émission de requêtes par programmation.

   ![](./media/search-explorer/search-bar.png)

## <a name="next-steps"></a>Étapes suivantes

Les ressources suivantes fournissent des exemples et des informations supplémentaires concernant la syntaxe de requête.

 + [Syntaxe de requête simple](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) 
 + [Syntaxe de requête Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) 
 + [Exemples de syntaxe de requête Lucene](https://docs.microsoft.com/azure/search/search-query-lucene-examples) 
 + [Syntaxe d’expression de filtre OData](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) 