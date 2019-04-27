---
title: Exemples d’insights Bing - Recherche visuelle Bing
titleSuffix: Azure Cognitive Services
description: Présente des exemples d’insights affichés sur Bing.com.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: scottwhi
ms.openlocfilehash: 94f8d4ea98f42ad919597ef53dc63281825f0e6b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60610345"
---
# <a name="examples-of-bing-insights-usage"></a>Exemples d’utilisation d’insights de Bing

Cet article contient des exemples d’utilisation et d’affichage d’insights relatifs aux images sur Bing.com.

## <a name="pagesincluding-insight-example"></a>Exemple d’insight PagesIncluding

Ce qui suit affiche un lien vers la première page Web et permet à l’utilisateur Développer / réduire la liste des autres pages Web qui incluent l’image :

![Pages développés incluant](./media/pages-including.PNG)

## <a name="shoppingsources-insight-example"></a>Exemple d’insight ShoppingSources

L’exemple suivant montre comment Bing peut afficher des sources d’achat de produits illustrés dans l’image :

![Sources d’achat](./media/shopping-sources.PNG)

## <a name="visualsearch-insight-example"></a>Exemple d’insight VisualSearch

L’exemple suivant montre comment Bing peut afficher des images similaires (consultez **images associées** dans l’exemple) :

![Images visuellement similaires](./media/similar-images.PNG)

## <a name="recipes-insight-example"></a>Exemple d’insight Recipes

L’exemple suivant montre comment Bing peut afficher des recettes pour les produits alimentaires illustrés dans l’image. L’exemple informe l’utilisateur recettes sont disponibles :

![Recettes et pages incluant](./media/recipes-pages-including.PNG)

 Et fournit le lien vers les recettes lorsque l’utilisateur développe la liste :

![Pages de recettes développées incluant](./media/expanded-recipes-pages-including.PNG)

## <a name="relatedsearches-insight-example"></a>Exemple d’insight RelatedSearches

L’exemple suivant montre comment Bing peut afficher les recherches connexes d’images effectuées par d’autres utilisateurs. Si l’utilisateur clique sur l’image, il est dirigé vers la page des résultats de la recherche Bing.com/images pour cette requête liée.

![Recherches connexes d’images](./media/bordered-related-searches.PNG)

## <a name="entity-insight-example"></a>Exemple d’insight Entity

L’exemple suivant montre comment Bing peut afficher des informations sur l’entité (personne, emplacement ou chose) illustrée dans l’image. Si l’utilisateur clique sur le lien de l’entité, l’utilisateur est dirigé vers la page de résultats de recherche de Bing.com pour l’entité :

![Entité illustrée dans l’image](./media/entity.PNG)

## <a name="displaying-other-insights-that-the-user-might-explore"></a>Afficher d’autres insights que l’utilisateur peut explorer

L’exemple suivant montre comment Bing peut afficher d’autres informations sur l’image que l’utilisateur peut explorer.

![Explorer d’autres insights sur l’image](./media/apple-pie-more-tags.PNG)

## <a name="bounding-boxes-and-hot-spots"></a>Rectangles englobants et zones réactives

Les balises autres que celles par défaut incluent le rectangle englobant qui identifie la zone d’intérêt dans l’image à laquelle la balise s’applique. Si le rectangle englobant n’identifie pas l’image entière, utilisez le rectangle englobant pour créer une zone réactive sur l’image. L’utilisateur peut cliquer sur la zone réactive pour obtenir des informations relatives au contenu figurant sous la zone réactive (ou rectangle). Par exemple, si l’image est une image haute Round-Robin, les résultats peuvent contenir les balises (et zones englobantes) pour les accessoires illustré dans l’image, comme un sac, bijoux, scarfs et ainsi de suite. L’exemple suivant montre un rectangle réactive pour l’ensoleillement est maximal indiqué dans l’image :

![Rectangle englobant et zone réactive](./media/click-to-search.PNG)

## <a name="next-steps"></a>Étapes suivantes

Pour commencer avec votre première requête, consultez les Démarrages rapides : [C#](quickstarts/csharp.md) | [Java](quickstarts/java.md) | [node.js](quickstarts/nodejs.md) | [Python](quickstarts/python.md)





