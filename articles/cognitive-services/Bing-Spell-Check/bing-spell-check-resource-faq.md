---
title: Forum aux Questions concernant l’API Vérification orthographique Bing - Services cognitifs Azure | Microsoft Docs
description: Obtenez des réponses aux questions courantes sur l’API Vérification orthographique Bing sur Azure.
services: cognitive-services
author: HeidiSteen
manager: jhubbard
ms.service: cognitive-services
ms.component: bing-spell-check
ms.topic: conceptual
ms.date: 07/26/2017
ms.author: heidist
ms.openlocfilehash: 87b1f3ed3e0aaa9f3c3c804dc9eac3ee60b4a565
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35370220"
---
# <a name="frequently-asked-questions-about-the-bing-spell-check-api"></a>Forum aux Questions sur l’API Vérification orthographique Bing

 Trouvez les réponses aux questions fréquemment posées sur les concepts, codes et scénarios relatifs à l’API Vérification orthographique Bing pour Microsoft Cognitive Services sur Azure.

## <a name="how-do-i-get-the-optional-client-headers-when-calling-the-bing-spell-check-api-from-javascript"></a>Comment récupérer les en-têtes clients facultatifs pendant l’appel de l’API Vérification orthographique Bing en JavaScript ?

Les en-têtes suivants sont facultatifs, mais nous vous recommandons de les traiter comme s’ils étaient obligatoires. Ils aident l’API Vérification orthographique Bing à renvoyer des résultats plus précis.

- X-Search-Location
- X-MSEdge-ClientID
- X-MSEdge-ClientIP

Toutefois, lorsque vous appelez l’API Vérification orthographique Bing en JavaScript, les fonctionnalités de sécurité intégrées de votre navigateur peuvent dans certains cas vous empêcher d’accéder aux valeurs de ces en-têtes.

Pour résoudre ce problème, vous pouvez envoyer la requête à l’API Vérification orthographique Bing par le biais d’un proxy CORS. La réponse émanant d’un proxy de ce type a un en-tête `Access-Control-Expose-Headers` qui met les en-têtes de réponse sur liste verte et les rend accessibles à du code JavaScript.

Il est facile d’installer un proxy CORS pour autoriser [l’application du tutoriel](tutorials/spellcheck.md) à accéder aux en-têtes clients facultatifs. Tout d’abord, si ce n’est déjà fait, [installez Node.js](https://nodejs.org/en/download/). Entrez alors la commande suivante dans l’invite de commandes.

    npm install -g cors-proxy-server

Ensuite, remplacez le point de terminaison de l’API Vérification orthographique Bing dans le fichier HTML par :

    http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/spellcheck/

Enfin, démarrez le proxy CORS avec la commande suivante :

    cors-proxy-server

Laissez la fenêtre de commande ouverte pendant que vous utilisez l’application du didacticiel ; la fermeture de la fenêtre entraîne l’arrêt du proxy. Dans la section des en-têtes HTTP (qui peut être développée) sous les résultats de la recherche, vous pouvez maintenant voir l’en-tête `X-MSEdge-ClientID` (entre autres) et vérifier qu’il est identique pour toutes les requêtes.

## <a name="next-steps"></a>Étapes suivantes

Votre question concerne-t-elle une fonctionnalité manquante ? Demandez-la ou votez pour elle sur le [site web UserVoice](https://cognitive.uservoice.com/).

## <a name="see-also"></a>Voir aussi

 [StackOverflow : Cognitive Services](http://stackoverflow.com/questions/tagged/microsoft-cognitive)
