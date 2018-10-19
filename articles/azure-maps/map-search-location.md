---
title: Afficher les résultats de recherche avec Azure Maps | Microsoft Docs
description: Comment effectuer une demande de recherche avec Azure Maps, puis afficher les résultats sur une carte JavaScript
author: jingjing-z
ms.author: jinzh
ms.date: 09/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 78ffa42bcf57b7163afc13b2550abdbae240ef00
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/17/2018
ms.locfileid: "45729228"
---
# <a name="show-search-results-on-the-map"></a>Afficher les résultats de recherche sur la carte

Cet article explique comment rechercher des lieux d’intérêt et afficher les résultats de la recherche sur la carte.

Vous pouvez rechercher un lieu d’intérêt de deux façons. La première consiste à utiliser un module de service pour effectuer une demande de recherche. La seconde consiste à effectuer une demande de recherche en envoyant un élément [XMLHttpRequest](https://xhr.spec.whatwg.org/) à [l’API de recherche approximative Azure Maps](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy). Les deux méthodes sont décrites ci-dessous.

## <a name="make-a-search-request-via-service-module"></a>Demande de recherche via un module de service

<iframe height='500' scrolling='no' title='Afficher les résultats de la recherche sur une carte (module de service)' src='//codepen.io/azuremaps/embed/zLdYEB/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consultez <a href='https://codepen.io/azuremaps/pen/zLdYEB/'>Show search results on a map (Service Module)</a> d’Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

Le premier bloc de code crée un objet carte et initialise le service client. Vous pouvez consulter la section [Créer une carte](./map-create.md) pour obtenir des instructions.

Le deuxième bloc de code utilise [l’API de recherche approximative Azure Maps](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) pour rechercher des lieux d’intérêt. L’API de recherche approximative peut gérer n’importe quelle combinaison d’entrées approximatives. La réponse du service de recherche partielle est ensuite analysée au format GeoJSON à l’aide de la méthode [getGeoJsonSearchResponse](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.geojson.geojsonsearchresponse?view=azure-iot-typescript-latest#geojsonsearchresponse). Ensuite, les repères sont ajoutés à la carte pour indiquer les points d’intérêt.

Le dernier bloc de code ajoute des limites d’appareil photo pour la carte à l’aide de la propriété [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-control/models.cameraboundsoptions?view=azure-iot-typescript-latest) de Maps.

## <a name="make-a-search-request-via-xmlhttprequest"></a>Demande de recherche via XMLHttpRequest

<iframe height='500' scrolling='no' title='Afficher les résultats de la recherche sur une carte' src='//codepen.io/azuremaps/embed/KQbaeM/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consultez la page <a href='https://codepen.io/azuremaps/pen/KQbaeM/'>Show search results on a map </a> (Afficher les résultats de recherche sur une carte) d’Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

Le premier bloc de code construit un objet de carte. Vous pouvez consulter la section [Créer une carte](./map-create.md) pour obtenir des instructions.

Le deuxième bloc de code ajoute une couche de résultats de la recherche à la carte. La couche de résultats de la recherche affiche les résultats de la recherche en plaçant des repères sur la carte. Des repères sont ajoutés à l’aide de [addPins](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addpins).

Le troisième bloc de code envoie un élément [XMLHttpRequest](https://xhr.spec.whatwg.org/) à [l’API de recherche approximative Azure Maps](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) pour rechercher les lieux d’intérêt. L’API de recherche approximative peut gérer n’importe quelle combinaison d’entrées approximatives.

Le dernier bloc de code analyse la réponse et ajuste les limites de l’appareil photo pour la carte à l’aide du paramètre [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-control/models.cameraboundsoptions?view=azure-iot-typescript-latest) pour afficher les repères correspondant aux résultats.

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur **Recherche approximative** :

> [!div class="nextstepaction"]
> [API de recherche approximative Azure Maps](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)

En savoir plus sur les classes et les méthodes utilisées dans cet article :

> [!div class="nextstepaction"]
> [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

Pour voir des exemples de codes complets, consultez les articles suivants :

> [!div class="nextstepaction"]
> [Obtenir des informations à partir d’une coordonnée](./map-get-information-from-coordinate.md)

> [!div class="nextstepaction"]
> [Afficher des directions de A à B](./map-route.md)
