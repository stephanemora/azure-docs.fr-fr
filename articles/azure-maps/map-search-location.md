---
title: Afficher les résultats de recherche avec Azure Maps | Microsoft Docs
description: Comment effectuer une demande de recherche avec Azure Maps, puis afficher les résultats sur une carte JavaScript
author: jingjing-z
ms.author: jinzh
ms.date: 08/31/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 7d4eb5f9be4a6bcefe4b544d3f97a9b9391c0d81
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2018
ms.locfileid: "43665775"
---
# <a name="show-search-results-on-the-map"></a>Afficher les résultats de recherche sur la carte

Cet article explique comment rechercher des lieux d’intérêt et afficher les résultats de la recherche sur la carte. 

## <a name="understand-the-code"></a>Comprendre le code

<iframe height='500' scrolling='no' title='Afficher les résultats de la recherche sur une carte (module de service)' src='//codepen.io/azuremaps/embed/zLdYEB/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consultez <a href='https://codepen.io/azuremaps/pen/zLdYEB/'>Show search results on a map (Service Module)</a> d’Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

Dans le code ci-dessus, le premier bloc de code crée un objet carte et instancie un service client. Vous pouvez consulter la section [Créer une carte](./map-create.md) pour obtenir des instructions.

Le deuxième bloc de code utilise [l’API de recherche approximative Azure Maps](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) pour rechercher des lieux d’intérêt. L’API de recherche approximative peut gérer n’importe quelle combinaison d’entrées approximatives. La réponse du service de recherche partielle est ensuite analysée au format GeoJSON à l’aide de la méthode [getGeoJsonSearchResponse](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.geojson.geojsonsearchresponse?view=azure-iot-typescript-latest#geojsonsearchresponse). Ensuite, les repères sont ajoutés à la carte pour indiquer les points d’intérêt.

Le dernier bloc de code ajoute des limites d’appareil photo pour la carte à l’aide de la propriété [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-control/models.cameraboundsoptions?view=azure-iot-typescript-latest) de Maps.

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les classes et les méthodes utilisées dans cet article : 

* [API de recherche approximative Azure Maps](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)
* [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)
    * [addPins](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addpins)
    
Pour consulter plus d’exemples de code à ajouter à vos cartes, consultez les articles suivants : 
* [Obtenir des informations à partir d’une coordonnée](./map-get-information-from-coordinate.md)
* [Afficher des directions de A à B](./map-route.md)
