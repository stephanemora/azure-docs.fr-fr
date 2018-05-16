---
title: Afficher les résultats de recherche avec Azure Maps | Microsoft Docs
description: Guide d’exécution d’une requête de recherche avec Azure Maps et d’affichage des résultats sur une carte JavaScript
services: azure-maps
keywords: ''
author: jinzh-azureiot
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: article
ms.service: azure-maps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: codepen
ms.openlocfilehash: f66b1f93d7bc4c2e7c511c10d7091760e8f6d023
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2018
---
# <a name="show-search-results-on-the-map"></a>Afficher les résultats de recherche sur la carte

Cet article vous explique comment exécuter une requête de recherche et afficher les résultats de la recherche sur la carte. 

## <a name="understand-the-code"></a>Comprendre le code

<iframe height='500' scrolling='no' title='Afficher les résultats de la recherche sur une carte' src='//codepen.io/azuremaps/embed/KQbaeM/?height=519&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consultez la page <a href='https://codepen.io/azuremaps/pen/KQbaeM/'>Show search results on a map </a> (Afficher les résultats de recherche sur une carte) d’Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

Dans le code ci-dessus, le premier bloc de code construit un objet de carte. Vous pouvez consulter la section [Créer une carte](./map-create.md) pour obtenir des instructions.

Le deuxième bloc de code génère et ajoute une couche de repères de recherche sur la carte. Pour plus d’instructions, consultez la section relative à l’[ajout d’un repère sur la carte](./map-add-pin.md).

Le troisième bloc de code envoie un élément [XMLHttpRequest](https://xhr.spec.whatwg.org/) à l’[API de recherche approximative Azure Maps](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy).

Le dernier bloc de code analyse la réponse entrante. Si la réponse est correcte, il collecte les données de latitude et de longitude pour chaque emplacement renvoyé. Il ajoute l’ensemble des points d’emplacement sur la carte en tant que repères et ajuste le cadre de la carte afin d’afficher le rendu de l’ensemble des repères.


## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les classes et les méthodes utilisées dans cet article : 

* [API de recherche approximative Azure Maps](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)
* [Map](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest)
    * [addPins](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addpins)
