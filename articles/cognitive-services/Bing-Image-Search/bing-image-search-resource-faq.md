---
title: 'Forum Aux Questions (FAQ) : API Recherche d’images Bing'
titleSuffix: Azure Cognitive Services
description: Trouvez les réponses aux questions les plus fréquemment posées sur les concepts, le code et les scénarios liés à l’API Recherche d’images Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: 4e62aa434efade2cb1fa3d82265eb44d523dd869
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108773356"
---
# <a name="frequently-asked-questions-faq-about-the-bing-image-search-api"></a>Forum Aux Questions (FAQ) portant sur l’API Recherche d’images Bing

> [!WARNING]
> Les API Recherche Bing passent de Cognitive Services aux services de recherche Bing. À compter du **30 octobre 2020**, toutes les nouvelles instances de Recherche Bing doivent être provisionnées en suivant le processus documenté [ici](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> Les API Recherche Bing provisionnées à l’aide de Cognitive Services seront prises en charge les trois prochaines années ou jusqu’à la fin de votre Contrat Entreprise, selon la première éventualité.
> Pour obtenir des instructions de migration, consultez [Services de recherche Bing](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Trouvez les réponses aux questions fréquemment posées sur les concepts, codes et scénarios relatifs à l’API Recherche d’images Bing pour Azure Cognitive Services sur Azure.

## <a name="response-headers-in-javascript"></a>En-têtes de réponse dans JavaScript

Les en-têtes suivants peuvent se produire dans les réponses de l’API Recherche d’images Bing.

| Attribut           | Description   |
| ------------------- | ------------- |
| `X-MSEdge-ClientID` |L’ID unique que Bing a affecté à l’utilisateur |
| `BingAPIs-Market`   |Le marché qui a été utilisé pour répondre à la requête |
| `BingAPIs-TraceId`  |L’entrée du journal sur le serveur de l’API Bing pour cette requête (pour la prise en charge) |

Il est particulièrement important de conserver l’identifiant du client et de le renvoyer avec les requêtes suivantes. Lorsque vous effectuez cette opération, la recherche utilise un contexte passé pour classer les résultats de la recherche et propose également une expérience utilisateur cohérente.

Toutefois, lorsque vous appelez l’API Recherche d’images Bing depuis JavaScript, les fonctionnalités de sécurité intégrées de votre navigateur (CORS) peuvent vous empêcher d’accéder aux valeurs de ces en-têtes.

Pour accéder aux en-têtes, vous pouvez effectuer la requête d’API Recherche d’images Bing via un proxy CORS. La réponse provenant d’un proxy de ce type a un en-tête `Access-Control-Expose-Headers` qui filtre les en-têtes de réponse et les rend accessibles à JavaScript.

Il est facile d’installer un proxy CORS pour autoriser [l’application du didacticiel](tutorial-bing-image-search-single-page-app.md) à accéder aux en-têtes clients facultatifs. Tout d’abord, [installez Node.js](https://nodejs.org/en/download/) si ce n’est pas déjà fait. Entrez alors la commande suivante dans l’invite de commandes.

```console
npm install -g cors-proxy-server
```

Ensuite, remplacez le point de terminaison de l’API Recherche d’images Bing dans le fichier HTML par :\
`http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/search`

Enfin, lancez le proxy CORS avec la commande suivante :

```console
cors-proxy-server
```

Laissez la fenêtre de commande ouverte pendant que vous utilisez l’application du tutoriel ; si vous fermez la fenêtre, le proxy s’arrête. Dans la section des en-têtes HTTP (qui peut être développée) sous les résultats de la recherche, vous pouvez maintenant voir l’en-tête `X-MSEdge-ClientID` (entre autres) et vérifier qu’il est identique pour toutes les requêtes.

## <a name="response-headers-in-production"></a>En-têtes de réponse en production

L’approche du proxy CORS décrite dans la réponse précédente est appropriée pour le développement, le test et l’apprentissage.

Toutefois, dans un environnement de production, vous devez héberger un script côté serveur dans le même domaine que la page web qui utilise l’API Recherche Web Bing. Ce script doit effectuer les appels d’API à la demande à partir de la page web JavaScript et transmettre tous les résultats, y compris les en-têtes, au client. Étant donné que les deux ressources (la page et le script) partagent une origine, le CORS n’intervient pas et les en-têtes spéciaux sont accessibles pour JavaScript dans la page web.

Cette approche protège également votre clé API de l’exposition au public, étant donné que seul le script côté serveur en a besoin. Le script peut utiliser une autre méthode (comme le référent HTTP) pour s’assurer que la requête est autorisée.

## <a name="next-steps"></a>Étapes suivantes

Votre question concerne-t-elle une fonctionnalité manquante ? Envisagez de la demander ou de la voter à l’aide de l’[outil de commentaires](https://feedback.azure.com/forums/932041-azure-cognitive-services?category_id=395749).

## <a name="see-also"></a>Voir aussi

 [Stack Overflow : Cognitive Services](https://stackoverflow.com/questions/tagged/bing-api)
