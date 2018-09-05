---
title: Afficher les résultats de recherche avec Azure Maps | Microsoft Docs
description: Guide d’exécution d’une requête de recherche avec Azure Maps et d’affichage des résultats sur une carte JavaScript
author: jingjing-z
ms.author: jinzh
ms.date: 08/26/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 540faa04573f7fa66b574f6743d42adfe855c571
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/27/2018
ms.locfileid: "43095253"
---
# <a name="show-search-results-on-the-map"></a>Afficher les résultats de recherche sur la carte

Cet article explique comment rechercher des lieux d’intérêt et afficher les résultats de la recherche sur la carte. 

## <a name="understand-the-code"></a>Comprendre le code

<iframe height='305' scrolling='no' title='Afficher les résultats de la recherche sur une carte (module de service)' src='//codepen.io/azuremaps/embed/zLdYEB/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consultez <a href='https://codepen.io/azuremaps/pen/zLdYEB/'>Show search results on a map (Service Module)</a> d’Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

Dans le code ci-dessus, le premier bloc de code crée un objet carte et instancie un service client. Vous pouvez consulter la section [Créer une carte](./map-create.md) pour obtenir des instructions.

Le deuxième bloc de code utilise [l’API de recherche approximative Azure Maps](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) pour rechercher des lieux d’intérêt. L’API de recherche approximative peut gérer n’importe quelle combinaison d’entrées approximatives. La réponse du service de recherche approximative est ensuite analysée au format GeoJSON, et des épingles sont ajoutées à la carte pour indiquer les lieux d’intérêt. 

Le dernier bloc de code ajoute des limites d’appareil photo pour la carte à l’aide de la propriété [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-control/models.cameraboundsoptions?view=azure-iot-typescript-latest) de Maps.

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les classes et les méthodes utilisées dans cet article : 

* [API de recherche approximative Azure Maps](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)
* [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)
    * [addPins](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addpins)
    
Pour consulter plus d’exemples de code à ajouter à vos cartes, consultez les articles suivants : 
* [Obtenir des informations à partir d’une coordonnée](./map-get-information-from-coordinate.md)
* [Afficher des directions de A à B](./map-route.md)
