---
title: Présentation des API Recherche Bing
titleSuffix: Azure Cognitive Services
description: Cet article présente les API Recherche Bing et explique comment activer les recherches Internet cognitives dans vos applications et vos services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: article
ms.date: 03/12/2019
ms.author: aahi
ms.openlocfilehash: 5a883fcb3533374afbbf946281b6a4a1e9a2912e
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/18/2019
ms.locfileid: "57903111"
---
# <a name="what-are-the-bing-search-apis"></a>Présentation des API Recherche Bing

Les API recherche Bing vous permettent de créer des applications web connectées et les services qui recherche les pages Web, images, actualités, emplacements, etc. sans publications. En envoyant des requêtes de recherche à l’aide de l’API REST Recherche Bing ou des SDK, vous pouvez obtenir des informations et du contenu pertinents. Utilisez cet article pour en savoir plus sur la recherche Bing différentes API et comment vous pouvez intégrer des recherches cognitives dans vos applications et services. Les limites concernant les tarifs et les taux d’utilisation peuvent varier selon les API.

## <a name="the-bing-web-search-api"></a>L’API Recherche Web Bing

L’[API Recherche Web Bing](../Bing-Web-Search/index.yml) retourne des pages web, des images, des vidéos, des actualités, et bien plus encore. Vous pouvez filtrer les requêtes de recherche envoyés à cette API à inclure ou exclure certains types de contenu.

Utilisez l’API Recherche Web Bing dans les applications qui devront rechercher tous les types de contenu web pertinents. Si votre application recherche un type spécifique de contenu en ligne, vous pouvez utiliser l’une des API de recherche suivantes :

## <a name="content-specific-bing-search-apis"></a>API Bing de recherche de contenu spécifique

L’API de recherche Bing suivants retournent un contenu spécifique à partir du web tels que des images, des actualités, des entreprises locales et des vidéos.

| API Bing | Description |
| -- | -- |
| [Recherche d’entités](../Bing-Entities-Search/index.yml) | L’API Recherche d’entités Bing retourne les résultats de recherche contenant des entités (personnes, lieux ou objets). En fonction de la requête, l’API retourne une ou plusieurs entités qui satisfont la requête de recherche. La requête de recherche peut inclure des personnes dignes d’intérêt, les entreprises locales, points de repère, destinations et bien plus encore. |
| [Recherche d’images](../Bing-Image-Search/index.yml) | L’API de recherche d’images Bing vous permet de rechercher et rechercher statiques et animés images de haute qualité similaire à [Bing.com/images](https://www.Bing.com/images). Vous pouvez affiner la recherche de manière à inclure ou exclure des images en fonction d’un de leurs attributs (taille, couleur, licence ou actualité). Vous pouvez également rechercher des images populaires, charger des images pour obtenir des insights à leur sujet ou afficher des miniatures d’aperçu. |
| [Recherche d’actualités](../Bing-News-Search/index.yml) | L’API recherche d’actualités Bing vous permet de rechercher des articles de presse similaire à [Bing.com/news](https://www.Bing.com/news). L’API retourne des articles issus de plusieurs sources ou appartenant à des domaines spécifiques. Vous pouvez rechercher des articles populaires ou des grands titres appartenant à plusieurs catégories. |
| [Recherche de vidéos](../Bing-Video-Search/index.yml) | L’API de recherche de vidéos Bing vous permet de rechercher des vidéos sur le web. Trouvez des vidéos populaires, du contenu connexe et des miniatures d’aperçu. |
| [Recherche visuelle](../Bing-visual-search/index.yml) | Chargez une image ou utilisez une URL pour obtenir des informations pertinentes à son sujet, telles que des produits visuellement similaires, des images et des recherches associées. |
 [Recherche d’entreprise locale](../bing-local-business-search/index.yml) | L’API Bing Search entreprise Local permet à vos applications de trouver des informations de contact et l’emplacement sur les entreprises locales basées sur des requêtes de recherche. |

## <a name="the-bing-custom-search-api"></a>L’API Recherche personnalisée Bing

Création d’une instance de recherche personnalisée avec le [recherche personnalisée Bing](../Bing-Custom-Search/index.yml) API vous permet de créer une expérience de recherche axée uniquement sur le contenu et les rubriques qui vous intéressent. Par exemple, une fois que vous spécifiez les domaines, sites Web et des pages Web spécifiques qui effectue une recherche Bing, recherche personnalisée Bing adapter les résultats à ce contenu spécifique. Vous pouvez incorporer les API Suggestion automatique personnalisée, Image et Recherche de vidéos de Bing pour personnaliser encore davantage votre recherche.

## <a name="additional-bing-search-apis"></a>Autres API de recherche Bing

L’API recherche Bing suivantes vous permettent d’améliorer votre expérience de recherche en les combinant avec d’autres API de recherche Bing.

| API | Description |
| -- | -- |
| [Suggestion automatique Bing](../Bing-Autosuggest/index.yml) | Améliorer l’expérience de recherche de votre application avec l’API suggestion automatique Bing en retournant des recherches suggérées en temps réel.  |
| [Statistiques Bing](bing-web-stats.md) | L’API Statistiques Bing fournit une analytique aux API de recherche Bing qu’utilise votre application. Dans cette analytique, figurent le volume d’appels, les principales chaînes de requête et la répartition géographique. |

## <a name="next-steps"></a>Étapes suivantes

* [Détail des tarifs](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/) de l’API Recherche Bing
* L’article [Conditions d’utilisation et d’affichage de l’API Recherche Bing](./use-display-requirements.md) spécifie les utilisations acceptables du contenu et des informations obtenus par le biais des API Recherche Bing.
