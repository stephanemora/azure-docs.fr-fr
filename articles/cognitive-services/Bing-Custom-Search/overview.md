---
title: Qu’est-ce que l’API Recherche personnalisée Bing ?
titlesuffix: Azure Cognitive Services
description: L’API Recherche personnalisée Bing vous permet de créer des expériences de recherche adaptées aux sujets qui vous intéressent.
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: overview
ms.date: 09/29/2017
ms.author: aahi
ms.openlocfilehash: 8dae58b1ae58a634ca6a566424a7c2d3fcf6add2
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/18/2018
ms.locfileid: "53554603"
---
# <a name="what-is-the-bing-custom-search-api"></a>Qu’est-ce que l’API Recherche personnalisée Bing ?

L’API Recherche personnalisée Bing vous permet de créer des expériences de recherche exemptes de publicité, adaptées aux sujets qui vous intéressent. Vous pouvez spécifier les domaines et pages web dans lesquels Bing doit effectuer ses recherches, également épingler, élever ou rétrograder un contenu précis pour créer une vue personnalisée du web qui permet à vos utilisateurs d’obtenir rapidement des résultats pertinents par rapport à leurs recherches. 

## <a name="features"></a>Caractéristiques

|Fonctionnalité  |Description  |
|---------|---------|
|[Suggestions personnalisées de recherche en temps réel](define-custom-suggestions.md)     | Fournir des suggestions de recherche qui peuvent être affichées sous forme de liste déroulante, au fur et à mesure que l’utilisateur tape un texte au clavier.       | 
|[Expériences personnalisées de recherche d’image](get-images-from-instance.md)     | Autoriser les utilisateurs à rechercher des images dans les domaines et sites web spécifiés dans votre instance de recherche personnalisée.        |        
|[Expériences personnalisées de recherche de vidéo](get-videos-from-instance.md)     | Autoriser les utilisateurs à rechercher des vidéos dans les domaines et sites web spécifiés dans votre instance de recherche personnalisée.        |    
|[Partager votre instance de recherche personnalisée](share-your-custom-search.md)     | Modifier et tester en collaboration votre instance de recherche en la partageant avec des membres de votre équipe.        | 
|[Configurer une interface utilisateur pour vos applications et sites web](hosted-ui.md)     | Modifier et tester en collaboration votre instance de recherche en la partageant avec des membres de votre équipe.        | 
## <a name="workflow"></a>Workflow

Vous pouvez créer une instance de recherche personnalisée par l’intermédiaire du [portail Recherche personnalisée Bing](https://customsearch.ai). Ce portail vous permet de créer une instance de recherche personnalisée qui spécifie les domaines, sites web et pages web que Bing doit utiliser pour ses recherches, et ceux qu’il ne doit pas utiliser. Vous pouvez également utiliser le portail pour afficher un aperçu de l’expérience de recherche, ajuster les classements de recherche que fournit l’API et, au besoin, configurer une interface utilisateur de recherche affichable dans vos applications et vos sites web.

Après avoir créé votre instance de recherche, vous pouvez l’intégrer (avec une interface utilisateur si nécessaire) à votre application ou votre site web en appelant l’API Recherche personnalisée Bing :

![Image illustrant la possibilité de se connecter à la Recherche personnalisée Bing via l’API](media/BCS-Overview.png "Fonctionnement de la Recherche personnalisée Bing.")


## <a name="next-steps"></a>Étapes suivantes

Pour bien démarrer rapidement, consultez [Créer votre première instance Recherche personnalisée Bing](quick-start.md).

Pour plus d’informations sur la personnalisation de votre instance de recherche, consultez [Définir une instance de recherche personnalisée](define-your-custom-view.md).

Prenez soin de lire les [Conditions d’utilisation et d’affichage Bing](./use-and-display-requirements.md) pour utiliser des résultats de recherche dans vos services et applications.

Familiarisez-vous avec le contenu de référence pour chacun des points de terminaison de la recherche personnalisée. Vous y trouverez les points de terminaison, les en-têtes et les paramètres de requête à utiliser pour demander les résultats de la recherche. Vous y trouverez également les définitions des objets de réponse.

- [API Recherche personnalisée](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference)
- [API Image personnalisée](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-images-api-v7-reference)
- [API Vidéo personnalisée](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-videos-api-v7-reference)
- [API Suggestion automatique personnalisée](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-autosuggest-api-v7-reference)

