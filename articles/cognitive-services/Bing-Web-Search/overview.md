---
title: Qu’est-ce que l’API Recherche Web Bing ?
titleSuffix: Azure Cognitive Services
description: L’API Recherche Web Bing est un service RESTful qui fournit des réponses instantanées aux requêtes de recherche web. Vous pouvez configurez les résultats de façon à inclure pages web, images, vidéos, actualités et plus encore. Les résultats sont fournis au format JSON et basés sur la pertinence de la recherche et sur vos abonnements API Recherche Web Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: overview
ms.date: 03/31/2020
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 5ea98a4ef759937c5962dc86f0384051b8fa0bb3
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80477469"
---
# <a name="what-is-the-bing-web-search-api"></a>Qu’est-ce que l’API Recherche Web Bing ?

L’API Recherche Web Bing est un service RESTful fournissant des réponses instantanées aux requêtes utilisateur. Les résultats de la recherche sont facilement configurés pour inclure des pages web, des images, des vidéos, des actualités, des traductions et bien davantage. Recherche Web Bing fournit les résultats au format JSON basés sur la pertinence de la recherche et sur vos abonnements API Recherche Web Bing.

Cette API répond de manière optimale aux besoins des applications qui doivent accéder à l’ensemble du contenu pertinent pour une requête de recherche utilisateur. Si vous créez une application ne nécessitant qu’un type de résultat spécifique, envisagez d’utiliser [l’API Recherche d’images Bing](../Bing-Image-Search/overview.md), [l’API Recherche de vidéos Bing](../Bing-Video-Search/search-the-web.md) ou [l’API Recherche d’actualités Bing](../Bing-News-Search/search-the-web.md). Pour obtenir la liste complète des API Recherche Bing, Consultez la section [API Cognitive Services](https://docs.microsoft.com/azure/cognitive-services).

Vous souhaitez découvrir le fonctionnement de l’API ? Essayez notre [démonstration de l’API Recherche Web Bing](https://azure.microsoft.com/services/cognitive-services/bing-web-search-api/).

## <a name="features"></a>Fonctionnalités  

Recherche Web Bing ne vous donne pas seulement accès à des réponses instantanées. Il offre en outre des fonctionnalités et capacités supplémentaires vous permettant de personnaliser les résultats de la recherche pour vos utilisateurs.

| Fonctionnalité | Description |
|---------|-------------|
| [Suggestion de termes de recherche en temps réel](../bing-autosuggest/get-suggested-search-terms.md) | Améliorez votre expérience d’application en utilisant l’API Suggestion automatique Bing pour afficher des suggestions de termes de recherche pendant la saisie de la requête. |
| [Filtrage et restriction des résultats par type de contenu](filter-answers.md) | Personnalisez et affinez les résultats de la recherche à l’aide de filtres et de paramètres de requête pour les pages web, images, vidéos, recherches sécurisées et bien davantage. |
| [Mise en surbrillance des correspondances pour les caractères Unicode](hit-highlighting.md) | Identifiez et supprimez les caractères Unicode indésirables des résultats de la recherche avant de les présenter aux utilisateurs grâce à la mise en surbrillance des correspondances. |
| [Localisation des résultats de la recherche par pays, région et/ou marché](supported-countries-markets.md) | Le service Recherche Web Bing prend en charge plus de trois douzaines de pays ou régions. Utilisez cette fonctionnalité pour affiner les résultats de la recherche pour un pays, une région ou un marché spécifiques. |
| [Analyse des mesures de recherche avec Statistiques Bing](bing-web-stats.md) | Statistiques Bing est un abonnement payant fournissant l’analyse du volume d’appels, des principales chaînes de requête, de la répartition géographique, et bien davantage. |

## <a name="workflow"></a>Workflow

L’API Recherche Web Bing peut être facilement appelée à partir de n’importe quel langage de programmation permettant d’exécuter des requêtes HTTP et d’analyser des réponses JSON. Ce service est accessible à l’aide de l’[API REST](quickstarts/python.md) ou des [bibliothèques de client Recherche Web Bing](./quickstarts/client-libraries.md).

1. [Créez une ressource Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) pour l’API Recherche Bing. Si vous n’avez pas d’abonnement Azure, vous pouvez [créer un compte gratuit](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api).  
2. Envoyez une [requête à l’API Recherche Web Bing](quickstarts/python.md).
3. Analysez la réponse JSON.

## <a name="next-steps"></a>Étapes suivantes

* Utilisez notre [guide de démarrage rapide Python](./quickstarts/client-libraries.md?pivots=programming-language-python) pour effectuer votre premier appel de l’API Recherche Web Bing.  
* [Créez une application web à page unique](tutorial-bing-web-search-single-page-app.md).
* Consultez la documentation de [référence sur l’API Recherche Web v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference).  
* Apprenez-en davantage sur les [conditions d’utilisation et d’affichage](UseAndDisplayRequirements.md) de l’API Recherche Web Bing.  
