---
title: Qu’est-ce que l’API Recherche de vidéos Bing ?
titleSuffix: Azure Cognitive Services
description: Découvrez comment rechercher des vidéos sur le web à l’aide de l’API Recherche de vidéos Bing.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: overview
ms.date: 12/18/2019
ms.author: scottwhi
ms.openlocfilehash: 6f5da1d17722f89bee88a4b69177583e095eeac3
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93094283"
---
# <a name="what-is-the-bing-video-search-api"></a>Qu’est-ce que l’API Recherche de vidéos Bing ?

> [!WARNING]
> Les API Recherche Bing passent de Cognitive Services aux services de recherche Bing. À compter du **30 octobre 2020** , toutes les nouvelles instances de Recherche Bing doivent être provisionnées en suivant le processus documenté [ici](https://aka.ms/cogsvcs/bingmove).
> Les API Recherche Bing provisionnées à l’aide de Cognitive Services seront prises en charge les trois prochaines années ou jusqu’à la fin de votre Contrat Entreprise, selon la première éventualité.
> Pour obtenir des instructions de migration, consultez [Services de recherche Bing](https://aka.ms/cogsvcs/bingmigration).

L’API Recherche de vidéos Bing facilite l’ajout de fonctionnalités de recherche de vidéos dans vos services et applications. En envoyant des requêtes de recherche utilisateur avec cette API, vous pouvez obtenir et afficher des vidéos pertinentes et de haute qualité comparables aux [vidéos Bing](https://www.bing.com/video). Utilisez cette API pour des résultats de recherche qui contiennent uniquement des vidéos. L’[API Recherche Web Bing](../bing-web-search/search-the-web.md) peut retourner d’autres types de contenu web, notamment des pages web, des vidéos, des actualités et des images.

## <a name="bing-video-search-api-features"></a>Fonctionnalités de l’API Recherche de vidéos Bing

| Fonctionnalité                                                                                                                                                                                 | Description                                                                                                                                                            |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Suggestion de termes de recherche en temps réel](concepts/sending-requests.md#suggest-search-terms-with-the-bing-autosuggest-api) | Améliorez votre expérience d’application en utilisant [l’API Suggestion automatique Bing](../bing-autosuggest/get-suggested-search-terms.md) pour afficher des suggestions de termes de recherche pendant la saisie de la requête. |
| [Filtrage et restriction des résultats de vidéos](concepts/get-videos.md#filtering-videos)                      | Filtrez les vidéos retournées par la modification des paramètres de requête.                                                                                                       |
| [Rognage, redimensionnement et affichage des miniatures](../bing-web-search/resize-and-crop-thumbnails.md)                                                | Modifiez et affichez les prévisualisations miniatures des vidéos renvoyées par l’API Recherche de vidéos Bing.                                                                                      |
| [Obtenir des vidéos populaires](trending-videos.md) | Recherchez des vidéos populaires du monde entier.                                                                                                          |
| [Obtenir des insights sur les vidéos](video-insights.md) | Personnalisez une recherche pour obtenir des vidéos tendance du monde entier.                                                                                                          |

## <a name="workflow"></a>Workflow

L’API Recherche de vidéos Bing est un service web RESTful, qui peut donc être facilement appelé à partir de n’importe quel langage de programmation capable d’exécuter des requêtes HTTP et d’analyser des réponses JSON. Vous pouvez utiliser ce service soit par le biais de [l’API REST](csharp.md), soit à l’aide du [Kit de développement logiciel (SDK)](video-search-sdk-quickstart.md).

1. Créez un [compte d’API Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) disposant d’un accès aux API Recherche Bing. Si vous n’avez pas d’abonnement Azure, vous pouvez créer un [compte](https://azure.microsoft.com/free/cognitive-services/) gratuitement.
2. Envoyez une requête à l’API avec une requête de recherche valide.
3. Traitez la réponse de l’API en analysant le message JSON renvoyé.


## <a name="next-steps"></a>Étapes suivantes

La [démonstration interactive](https://azure.microsoft.com/services/cognitive-services/bing-video-search-api/) de l’API Recherche de vidéos Bing montre comment vous pouvez personnaliser une requête de recherche et rechercher des vidéos sur le web.

Utilisez le [guide de démarrage rapide](csharp.md) pour exécuter rapidement votre première demande d’API.

## <a name="see-also"></a>Voir aussi

* La page de référence de l’[API Recherche de vidéos Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference) contient la liste des points de terminaison, des en-têtes et des paramètres de requête à utiliser pour demander les résultats de la recherche.

* L’article [Conditions d’utilisation et d’affichage de l’API Recherche Bing](./useanddisplayrequirements.md) spécifie les utilisations acceptables du contenu et des informations obtenus par le biais des API Recherche Bing.

* Visitez la [page hub de l’API Recherche Bing](../bing-web-search/search-the-web.md) pour explorer les autres API disponibles.