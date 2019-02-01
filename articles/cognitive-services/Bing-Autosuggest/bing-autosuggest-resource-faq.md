---
title: Forum Aux Questions (FAQ) – API Suggestion automatique Bing
titlesuffix: Azure Cognitive Services
description: Obtenez des réponses aux questions courantes sur l’API Suggestion automatique Bing.
services: cognitive-services
author: HeidiSteen
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-autosuggest
ms.topic: conceptual
ms.date: 07/26/2017
ms.author: heidist
ms.openlocfilehash: b0ec10bbf03e8a8d005eece4b6496b74b2943233
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55173256"
---
# <a name="frequently-asked-questions-faq-about-bing-autosuggest-api"></a>Forum Aux Questions (FAQ) sur l’API Suggestion automatique Bing
 
 Trouvez les réponses aux questions courantes sur les concepts, le code et les scénarios relatifs à l’API Suggestion automatique d’Azure Cognitive Services.

### <a name="how-do-i-get-the-optional-client-headers-when-calling-the-bing-autosuggest-api-from-javascript"></a>Comment récupérer les en-têtes clients facultatifs pendant l’appel de l’API Suggestion automatique Bing en JavaScript ?

Les en-têtes suivants sont facultatifs, mais nous vous recommandons de les traiter comme s’ils étaient obligatoires. Ils aident l’API Suggestion automatique Bing à retourner des résultats plus précis.

- X-Search-Location
- X-MSEdge-ClientID
- X-MSEdge-ClientIP

Toutefois, lorsque vous appelez l’API Suggestion automatique Bing en JavaScript, les fonctionnalités de sécurité intégrées de votre navigateur peuvent dans certains cas vous empêcher d’accéder aux valeurs de ces en-têtes.

Pour résoudre ce problème, vous pouvez envoyer la demande à l’API Suggestion automatique Bing par le biais d’un proxy CORS. La réponse émanant d’un proxy de ce type a un en-tête `Access-Control-Expose-Headers` qui met les en-têtes de réponse sur liste verte et les rend accessibles à du code JavaScript.

Il est facile d’installer un proxy CORS pour autoriser [l’application du tutoriel](tutorials/autosuggest.md) à accéder aux en-têtes clients facultatifs. Tout d’abord, [installez Node.js](https://nodejs.org/en/download/) si ce n’est pas déjà fait. Entrez alors la commande suivante dans l’invite de commandes.

    npm install -g cors-proxy-server

Ensuite, remplacez le point de terminaison de l’API Suggestion automatique Bing dans le fichier HTML par :

    http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/Suggestions

Enfin, lancez le proxy CORS avec la commande suivante :

    cors-proxy-server

Laissez la fenêtre de commande ouverte pendant que vous utilisez l’application du tutoriel ; si vous fermez la fenêtre, le proxy s’arrête. Dans la section des en-têtes HTTP (qui peut être développée) sous les résultats de la recherche, vous pouvez maintenant voir l’en-tête `X-MSEdge-ClientID` (entre autres) et vérifier qu’il est identique pour toutes les requêtes.

## <a name="next-steps"></a>Étapes suivantes

Votre question concerne-t-elle une fonctionnalité manquante ? Demandez-la ou votez pour elle sur notre [site web UserVoice](https://cognitive.uservoice.com/).

## <a name="see-also"></a>Voir aussi

- [Stack Overflow : Cognitive Services](http://stackoverflow.com/questions/tagged/microsoft-cognitive)
