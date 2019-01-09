---
title: Qu’est-ce que l’API Recherche visuelle Bing ?
titleSuffix: Azure Cognitive Services
description: La Recherche visuelle Bing fournit des détails ou des informations sur une image, comme des images similaires ou des sources d’achat.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-visual-search
ms.topic: overview
ms.date: 04/10/2018
ms.author: scottwhi
ms.openlocfilehash: aab17f8a0213c213920ebdfc2b2fe31248657a91
ms.sourcegitcommit: 21466e845ceab74aff3ebfd541e020e0313e43d9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/21/2018
ms.locfileid: "53742845"
---
# <a name="what-is-the-bing-visual-search-api"></a>Qu’est-ce que l’API Recherche visuelle Bing ?

L’API Recherche visuelle Bing fournit des détails d’image similaires à ceux illustrés sur Bing.com/images. En téléchargeant une image ou grâce à une URL permettant d’accéder à une image, cette API peut identifier de nombreux détails, notamment des images similaires, des sources d’achat, des pages web présentant cette image, etc. Si vous utilisez [l’API Recherche d’images Bing](../bing-image-search/overview.md), vous pouvez utiliser des jetons joints aux résultats de recherche de l’API pour éviter d’avoir à charger une image.

## <a name="insights"></a>Insights

Voici les informations que la recherche visuelle vous permet de découvrir :

| Insight                              | Description |
|--------------------------------------|-------------|
| Images visuellement similaires              | Liste d’images qui sont visuellement semblables à l’image d’entrée. |
| Produits visuellement similaires            | Produits visuellement similaires au produit présenté.            |
| Sources d’achat                     | Lieux où vous pouvez acheter l’article affiché dans l’image d’entrée.            |
| Recherches associées                     | Recherches associées effectuées par d’autres personnes ou basées sur le contenu de l’image.            |
| Pages Web où l’image est intégrée     | Pages Web contenant l’image d’entrée.            |
| Recettes                              | Pages web qui comportent des recettes pour préparer le plat illustré dans l’image d’entrée.            |

En plus de ces informations, la recherche visuelle renvoie également un ensemble varié de termes (balises) dérivés de l’image d’entrée. Ces balises permettent aux utilisateurs d’explorer les concepts de l’image. Par exemple, si l’image d’entrée représente un athlète célèbre, une des balises peut être le nom du sportif et une autre balise peut être Sport. Ou, si l’image d’entrée représente une tarte aux pommes, les balises peuvent être Tarte aux pommes, Tarte ou Desserts, afin que les utilisateurs puissent explorer les concepts connexes.

Les résultats de la recherche visuelle incluent également des rectangles englobants pour les zones d’intérêt de l’image. Par exemple, si l’image représente plusieurs célébrités, les résultats peuvent inclure des rectangles englobants pour chacune des célébrités reconnues dans l’image. Ou, si Bing reconnaît un produit ou un vêtement dans l’image, le résultat peut inclure un rectangle englobant pour le produit ou le vêtement reconnu.

> [!IMPORTANT]
> Si vous obtenez des informations sur les images à l’aide de l’API Recherche d’images Bing, songez à passer à l’API Recherche visuelle Bing pour accéder à des informations plus complètes.

## <a name="workflow"></a>Workflow

L’API Recherche visuelle Bing est un service web RESTful et peut donc être facilement appelé à partir de n’importe quel langage de programmation permettant d’exécuter des requêtes HTTP et d’analyser des réponses JSON. Vous pouvez utiliser ce service soit par le biais de l’API REST, soit à l’aide du Kit de développement logiciel (SDK).

1. Créez un compte d’API Cognitive Services disposant d’un accès aux API Recherche Bing. Si vous n’avez pas d’abonnement Azure, vous pouvez créer un compte gratuitement.
2. Envoyez une requête à l’API avec une requête de recherche valide.
3. Traitez la réponse de l’API en analysant le message JSON renvoyé.


## <a name="next-steps"></a>Étapes suivantes

Commencez par essayer la [démonstration interactive](https://azure.microsoft.com/services/cognitive-services/bing-visual-search/) de l’API Recherche d’images Bing.
Cette démonstration vous explique comment personnaliser rapidement une requête de recherche et parcourir le web à la recherche d’images.

Lorsque vous êtes prêt à appeler l’API, créez un [compte d’API Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account). Si vous n’avez pas d’abonnement Azure, vous pouvez créer un [compte](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) gratuitement.

Pour configurer rapidement votre première demande, consultez les guides de démarrage rapide suivants : [C#](quickstarts/csharp.md) | [Java](quickstarts/java.md) | [node.js](quickstarts/nodejs.md) | [Python](quickstarts/python.md).


## <a name="see-also"></a>Voir aussi

* La documentation de référence concernant la [Recherche visuelle Bing](https://docs.microsoft.com/rest/api/cognitiveservices/bingvisualsearch/images/visualsearch) contient des définitions et des informations sur les points de terminaison, en-têtes, réponses d’API et paramètres de requête que vous pouvez utiliser pour demander des résultats de recherche sous forme d’images.

* L’article [Conditions d’utilisation et d’affichage de l’API Recherche Bing](./use-and-display-requirements.md) spécifie les utilisations acceptables du contenu et des informations obtenus par le biais des API Recherche Bing.
