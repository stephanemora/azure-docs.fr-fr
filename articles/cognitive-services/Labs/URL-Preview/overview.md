---
title: Qu’est-ce que l’aperçu d’URL de projet ?
titlesuffix: Azure Cognitive Services
description: Présentation de l’aperçu d’URL de projet.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: url-preview
ms.topic: overview
ms.date: 03/16/2018
ms.author: rosh
ms.openlocfilehash: 7022c3b2d2f3618d55b0a70d2690abf1497ec6a6
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55865333"
---
# <a name="what-is-project-url-preview"></a>Qu’est-ce que l’aperçu d’URL de projet ?
Le point de terminaison d’aperçu d’URL prend un paramètre de requête URL et retourne une réponse JSON avec le nom de la ressource cible, une brève description et un lien vers une image à afficher dans un aperçu. La réponse inclut également l’indicateur [isFamilyFriendly](url-preview-reference.md#query-parameters) qui précise si l’URL contient des données adulte, piratées ou tout autre contenu illégal. 

Pour obtenir des résultats d’aperçu d’URL, soumettez une requête GET et incluez l’en-tête *Ocp-Apim-Subscription-Key* avec un jeton valide :  
```
https://api.labs.cognitive.microsoft.com/urlpreview/v7.0/search?q=https://swiftkey.com

```
La réponse : 
```
HTTP Headers:
BingAPIs-TraceId: 3CC74C94769440C0851D9DF0869FCE7F
BingAPIs-SessionId: 52219085A6364692958C9C83983A0DBA
X-MSEdge-ClientID: 13D44DC2DE946B4B0F25460CDF036AD6
BingAPIs-Market: en-US
X-MSEdge-Ref: Ref A: 3CC74C94769440C0851D9DF0869FCE7F Ref B: CO1EDGE0315 Ref C: 2018-04-11T18:47:40Z
{
  "_type": "WebPage",
  "name": "SwiftKey - Smart prediction technology for easier mobile typing",
  "url": "https:\/\/swiftkey.com\/en",
  "description": "Discover the best Android and iPhone and iPad apps for faster, easier typing with emoji, colorful themes and more - download SwiftKey Keyboard free today.",
  "isFamilyFriendly": true,
  "primaryImageOfPage": {
    "contentUrl": "https:\/\/swiftkey.com\/images\/og\/default.jpg"
  }
}

```
## <a name="scenarios"></a>Scénarios 

L’API d’aperçu d’URL prend en charge de brèves descriptions de ressources web. Les développeurs l’utilisent pour créer des expériences d’aperçu riches.  Les utilisateurs peuvent partager ou marquer d’un signet des pages web, des actualités, des blogs, des forums, etc. Cette API peut également être utilisée pour la modération de contenu.    

Les applications utilisent l’aperçu d’URL pour envoyer des requêtes web au point de terminaison avec une requête affectée à l’URL à prévisualiser.  La réponse JSON contient les informations d’aperçu : nom, description de la ressource, indicateur *familyFriendly* et liens donnant accès à une image représentative et à la ressource en ligne complète. 

## <a name="terms-of-use"></a>Conditions d’utilisation
Utilisez uniquement les données de l’aperçu d’URL du projet pour afficher les extraits de code de l’aperçu et les vignettes sous forme de liens hypertexte vers les sites source dans une URL créée par l’utilisateur final et partagée sur les réseaux sociaux, un chatbot ou toute offre similaire. Vous ne devez pas copier, stocker ou mettre en cache les données que vous recevez d’un aperçu d’URL de projet. Répondez à toutes les requêtes visant à désactiver les aperçus éventuellement reçus des propriétaires de site web ou de contenu.

Vous, ou un tiers agissant en votre nom, ne pouvez pas utiliser, conserver, stocker, mettre en cache, partager ou distribuer les données issues de l’API d’aperçu d’URL à des fins de test, de développement, de formation, de distribution, ni rendre disponible un service ou une fonctionnalité non Microsoft. 

## <a name="throttling-requests"></a>Demandes de limitation

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>Étapes suivantes
- [Démarrage rapide C#](csharp.md)
- [Démarrage rapide Java](java-quickstart.md)
- [Démarrage rapide JavaScript](javascript.md)
- [Démarrage rapide Node](node-quickstart.md)
- [Démarrage rapide Python](python-quickstart.md)
