---
title: Qu'est-ce que l'API Recherche d’images Bing ?
titleSuffix: Azure Cognitive Services
description: L’API Recherche d’images Bing vous permet d’utiliser les fonctionnalités Bing de recherche cognitive d’images dans votre application. En envoyant des requêtes de recherche utilisateur avec cette API, vous pouvez obtenir et afficher des images pertinentes et de haute qualité comparables aux Images Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.assetid: 1446AD8B-A685-4F5F-B4AA-74C8E9A40BE9
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: overview
ms.date: 12/18/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: eb657c16f6f3ff67f4379134f3aa478f10d8ef94
ms.sourcegitcommit: 32592ba24c93aa9249f9bd1193ff157235f66d7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/01/2020
ms.locfileid: "85603534"
---
# <a name="what-is-the-bing-image-search-api"></a>Qu'est-ce que l'API Recherche d’images Bing ?

L’API Recherche d’images Bing vous permet d’utiliser les fonctionnalités Bing de recherche d’images dans votre application. En envoyant des requêtes de recherche à l’API, vous pouvez obtenir des images de haute qualité comparables à celles de [bing.com/images](https://www.bing.com/images).

Alors que l’API Recherche d’images Bing fournit seulement des images dans les résultats de recherche, vous pouvez combiner ou utiliser les autres [API Recherche Bing](../bing-web-search/bing-api-comparison.md) disponibles pour rechercher de nombreux types de contenu sur le web.

## <a name="bing-image-search-features"></a>Fonctionnalités de recherche d’images Bing

| Fonctionnalité                                                                                                                                                                                 | Description                                                                                                                                                            |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Suggestion de termes de recherche en temps réel](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-sending-queries) | Améliorez votre expérience d’application en utilisant [l’API Suggestion automatique Bing](../bing-autosuggest/get-suggested-search-terms.md) pour afficher des suggestions de termes de recherche pendant la saisie de la requête. |
| [Filtrage et restriction des résultats d’image](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-get-images)                       | Filtrez les images renvoyées par Bing en modifiant les paramètres de requête.                                                                                                       |
| [Rognage, redimensionnement et affichage des miniatures](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/resize-and-crop-thumbnails)                                                | Modifiez et affichez les miniatures des images renvoyées par le service Recherche d’images Bing.                                                                                      |
| [Création d’un tableau croisé dynamique à partir des requêtes de recherche utilisateur et élargissement des requêtes](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-sending-queries)               | Élargissez vos capacités de recherche en incluant et en affichant les suggestions de termes de recherche Bing dans les requêtes.                                                                    |
| [Obtention des images tendance](trending-images.md)                                                                     | Personnalisez une recherche afin d’obtenir les images tendance du monde entier.                                                                                                          |

## <a name="workflow"></a>Workflow

L’API Recherche d’images Bing est un service web RESTful et peut donc être facilement appelé à partir de n’importe quel langage de programmation permettant d’exécuter des requêtes HTTP et d’analyser des réponses JSON. Vous pouvez utiliser ce service soit par le biais de [l’API REST](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/quickstarts/csharp?), soit à l’aide du [Kit de développement logiciel (SDK)](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/image-search-sdk-quickstart).

1. Créez un [compte d’API Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) disposant d’un accès aux API Recherche Bing. Si vous n’avez pas d’abonnement Azure, vous pouvez créer un [compte](https://azure.microsoft.com/free/cognitive-services/) gratuitement.
2. Envoyez une requête à l’API avec une [requête de recherche](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-sending-queries) valide.
3. Traitez la réponse de l’API en analysant le message JSON renvoyé.

## <a name="next-steps"></a>Étapes suivantes

Commencez par essayer la [démonstration interactive](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/) de l’API Recherche d’images Bing.
Cette démonstration vous explique comment personnaliser rapidement une requête de recherche et parcourir le web à la recherche d’images.

Pour exécuter rapidement votre première requête d’API, vous pouvez apprendre à effectuer les opérations suivantes :

* [envoyez des requêtes de recherche à Bing](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/quickstarts/csharp) à l’aide de l’API REST ou
* [demandez et filtrez](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/image-search-sdk-quickstart) les images renvoyées par Bing à l’aide du Kit de développement logiciel (SDK).

## <a name="see-also"></a>Voir aussi

* [Détail des prix](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/) des API Recherche Bing. 

* La section de référence de l’[API Recherche d’images Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference) contient des informations sur les points de terminaison, les en-têtes, les réponses et les paramètres de requête de l’API.

* L’article [Conditions d’utilisation et d’affichage de l’API Recherche Bing](./useanddisplayrequirements.md) spécifie les utilisations acceptables du contenu et des informations obtenus par le biais des API Recherche Bing.

* L’article [Obtention d’images à partir du web avec l’API Recherche d’images Bing](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-get-images) explique comment rechercher et obtenir des images à partir du web.

* L’article [Envoi et utilisation des requêtes de recherche](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-sending-queries) décrit comment créer et personnaliser des requêtes de recherche et comment leur ajouter un tableau croisé dynamique.

* Visitez la [page hub de l’API Recherche Bing](../bing-web-search/search-the-web.md) pour explorer les autres API disponibles.
