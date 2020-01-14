---
title: Qu’est-ce que la Suggestion automatique Bing ?
titleSuffix: Azure Cognitive Services
description: L’API Suggestion automatique Bing retourne une liste de requêtes suggérées en fonction d’une chaîne de requête partielle dans la zone de recherche.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-autosuggest
ms.topic: overview
ms.date: 12/18/2019
ms.author: scottwhi
ms.openlocfilehash: a90fa0a66fb32b2a885599f09458964188353880
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75448829"
---
# <a name="what-is-bing-autosuggest"></a>Qu’est-ce que la Suggestion automatique Bing ?

Si votre application envoie des requêtes à l’une des API Recherche Bing, vous pouvez utiliser l’API Suggestion automatique Bing pour améliorer l’expérience de recherche de vos utilisateurs. L’API Suggestion automatique Bing retourne une liste de requêtes suggérées en fonction d’une chaîne de requête partielle dans la zone de recherche. À mesure que des caractères sont entrés dans la zone de recherche, vous pouvez afficher des suggestions dans une liste déroulante.

## <a name="bing-autosuggest-api-features"></a>Fonctionnalités de l’API Suggestion automatique Bing

| Fonctionnalité                                                                                                                                                                                 | Description                                                                                                                                                            |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Suggestion de termes de recherche en temps réel](concepts/get-suggestions.md) | Améliorez l’expérience dans votre application en utilisant l’API Suggestion automatique Bing pour afficher des suggestions de termes de recherche pendant la saisie d’une requête. |

## <a name="workflow"></a>Workflow

L’API Suggestion automatique Bing est un service web RESTful que vous pouvez facilement appeler à partir de n’importe quel langage de programmation capable d’exécuter des requêtes HTTP et d’analyser des réponses JSON. 

1. Créez un [compte d’API Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) disposant d’un accès aux API Recherche Bing. Si vous n’avez pas d’abonnement Azure, vous pouvez créer un [compte](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) gratuitement.
2. Envoyez une requête à cette API chaque fois qu’un utilisateur tape un nouveau caractère dans la zone de recherche de votre application.
3. Traitez la réponse de l’API en analysant le message JSON renvoyé.

En général, vous appelez cette API chaque fois que l’utilisateur tape un nouveau caractère dans la zone de recherche de votre application. À mesure que l’utilisateur tape d’autres caractères, l’API retourne des suggestions de requête de recherche plus pertinentes. Par exemple, les suggestions retournées par l’API pour le caractère `s` sont probablement moins pertinentes que celles retournées pour `sail` (dériveurs).

L’exemple suivant montre une zone déroulante de recherche avec les termes de requête suggérés provenant de l’API Suggestion automatique Bing.

![Liste de zone de recherche déroulante Suggestion automatique](./media/cognitive-services-bing-autosuggest-api/bing-autosuggest-drop-down-list.PNG)

Quand un utilisateur sélectionne une suggestion dans la liste déroulante, vous pouvez l’utiliser pour lancer la recherche avec l’une des API Recherche Bing ou accéder directement à la page des résultats de la recherche Bing.

## <a name="next-steps"></a>Étapes suivantes

Pour configurer rapidement votre première requête, consultez [Création de votre première requête](quickstarts/csharp.md).

Familiarisez-vous avec les informations de référence sur [l’API Suggestion automatique Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference). Vous y trouverez la liste des points de terminaison, en-têtes et paramètres de requête à utiliser pour demander des suggestions de termes de requête, ainsi que les définitions des objets de réponse.

Visitez la [page hub de l’API Recherche Bing](../bing-web-search/search-the-web.md) pour explorer les autres API disponibles.


Découvrez comment effectuer des recherches sur le web à l’aide de l’[API Recherche Web Bing](../bing-web-search/search-the-web.md), et explorez les autres [API de recherche Bing](../bing-web-search/index.yml).

Prenez soin de lire les [exigences relatives à l’affichage et à l’utilisation de Bing](./useanddisplayrequirements.md) pour respecter les règles d’utilisation des résultats de la recherche.
