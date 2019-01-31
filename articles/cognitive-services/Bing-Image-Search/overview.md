---
title: Qu'est-ce que l'API Recherche d’images Bing ?
titleSuffix: Azure Cognitive Services
description: L’API Recherche d’images Bing vous permet d’utiliser les fonctionnalités Bing de recherche cognitive d’images dans votre application. En envoyant des requêtes de recherche utilisateur avec cette API, vous pouvez obtenir et afficher des images pertinentes et de haute qualité comparables aux Images Bing.
services: cognitive-services
author: aahill
manager: cgronlun
ms.assetid: 1446AD8B-A685-4F5F-B4AA-74C8E9A40BE9
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: overview
ms.date: 10/11/2017
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 8115ffd943750d88a1aa9f94ba53ff972a636373
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55157310"
---
# <a name="what-is-the-bing-image-search-api"></a>Qu'est-ce que l'API Recherche d’images Bing ?

L’API Recherche d’images Bing vous permet d’utiliser les fonctionnalités Bing de recherche cognitive d’images dans votre application. En envoyant des requêtes de recherche utilisateur avec cette API, vous pouvez obtenir et afficher des images pertinentes et de haute qualité comparables aux [Images Bing](https://www.bing.com/images).

N’oubliez pas que l’API Recherche d’images Bing renvoie uniquement des images dans les résultats de la recherche. Pour rechercher d’autres types de contenus web, utilisez [l’API Recherche Web Bing](../bing-web-search/search-the-web.md), [l’API Recherche de vidéos Bing](https://docs.microsoft.com/azure/cognitive-services/Bing-Video-Search) et [l’API Recherche d’actualités Bing](https://review.docs.microsoft.com/azure/cognitive-services/bing-news-search).

## <a name="bing-image-search-features"></a>Fonctionnalités de recherche d’images Bing

Bien que le service Recherche d’images Bing recherche et renvoie principalement des images pertinentes pour une requête de recherche, il offre également plusieurs fonctionnalités supplémentaires en matière de récupération d’images sur le web intelligente et ciblée.


| Fonctionnalité                                                                                                                                                                                 | Description                                                                                                                                                            |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Suggestion de termes de recherche en temps réel](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-sending-queries) | Améliorez votre expérience d’application en utilisant [l’API Suggestion automatique Bing](../bing-autosuggest/get-suggested-search-terms.md) pour afficher des suggestions de termes de recherche pendant la saisie de la requête. |
| [Filtrage et restriction des résultats d’image](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-get-images#filtering-images)                       | Filtrez les images renvoyées par Bing en modifiant les paramètres de requête.                                                                                                       |
| [Rognage, redimensionnement et affichage des miniatures](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/resize-and-crop-thumbnails)                                                | Modifiez et affichez les miniatures des images renvoyées par le service Recherche d’images Bing.                                                                                      |
| [Création d’un tableau croisé dynamique à partir des requêtes de recherche utilisateur et élargissement des requêtes](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-sending-queries#pivoting-the-query)               | Élargissez vos capacités de recherche en incluant et en affichant les suggestions de termes de recherche Bing dans les requêtes.                                                                    |
| [Obtention des images tendance](https://review.docs.microsoft.com/azure/cognitive-services/bing-image-search/trending-images)                                                                     | Personnalisez une recherche afin d’obtenir les images tendance du monde entier.                                                                                                          |

## <a name="workflow"></a>Workflow

L’API Recherche d’images Bing est un service web RESTful et peut donc être facilement appelé à partir de n’importe quel langage de programmation permettant d’exécuter des requêtes HTTP et d’analyser des réponses JSON. Vous pouvez utiliser ce service soit par le biais de [l’API REST](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/quickstarts/csharp?), soit à l’aide du [Kit de développement logiciel (SDK)](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/image-search-sdk-quickstart).

1. Créez un [compte d’API Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) disposant d’un accès aux API Recherche Bing. Si vous n’avez pas d’abonnement Azure, vous pouvez créer un [compte](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) gratuitement.
2. Envoyez une requête à l’API avec une [requête de recherche](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-sending-queries) valide.
3. Traitez la réponse de l’API en analysant le message JSON renvoyé.

## <a name="next-steps"></a>Étapes suivantes

Commencez par essayer la [démonstration interactive](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/) de l’API Recherche d’images Bing.
Cette démonstration vous explique comment personnaliser rapidement une requête de recherche et parcourir le web à la recherche d’images.

Lorsque vous êtes prêt à appeler l’API, créez un [compte d’API Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account). Si vous n’avez pas d’abonnement Azure, vous pouvez créer un [compte](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) gratuitement.

Pour exécuter rapidement votre première requête d’API, vous pouvez apprendre à effectuer les opérations suivantes :

* [envoyez des requêtes de recherche à Bing](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/quickstarts/csharp) à l’aide de l’API REST ou
* [demandez et filtrez](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/image-search-sdk-quickstart) les images renvoyées par Bing à l’aide du Kit de développement logiciel (SDK).

## <a name="see-also"></a>Voir aussi

* La section de référence concernant [l’API Recherche d’images Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference) contient des définitions et des informations sur les points de terminaison, en-têtes, réponses d’API et paramètres de requête que vous pouvez utiliser pour demander des résultats de recherche sous forme d’images.

* L’article [Conditions d’utilisation et d’affichage de l’API Recherche Bing](./useanddisplayrequirements.md) spécifie les utilisations acceptables du contenu et des informations obtenus par le biais des API Recherche Bing.

* L’article [Obtention d’images à partir du web avec l’API Recherche d’images Bing](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-get-images) explique comment rechercher et obtenir des images à partir du web.

* L’article [Envoi et utilisation des requêtes de recherche](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-sending-queries) décrit comment créer et personnaliser des requêtes de recherche et comment leur ajouter un tableau croisé dynamique.
