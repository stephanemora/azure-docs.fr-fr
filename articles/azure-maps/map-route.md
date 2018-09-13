---
title: Affichage de directions avec Azure Maps | Microsoft Docs
description: Comment afficher des directions entre deux emplacements sur une carte Javascript
author: jingjing-z
ms.author: jinzh
ms.date: 08/31/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: codepen
ms.openlocfilehash: 80abd6db9888524aa6a66d9861d8dc2d05188e19
ms.sourcegitcommit: e2348a7a40dc352677ae0d7e4096540b47704374
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/05/2018
ms.locfileid: "43781494"
---
# <a name="show-directions-from-a-to-b"></a>Afficher des directions de A à B 

Cet article vous explique comment exécuter une requête d’itinéraire et afficher l’itinéraire sur la carte. 

## <a name="understand-the-code"></a>Comprendre le code

<iframe height='500' scrolling='no' title='Montrer un itinéraire de A à B sur une carte (module Service)' src='//codepen.io/azuremaps/embed/RBZbep/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consultez la section <a href='https://codepen.io/azuremaps/pen/RBZbep/'>Montrer un itinéraire de A à B sur une carte</a> par Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

Dans le code ci-dessus, le premier bloc de code construit un objet de carte. Vous pouvez consulter la section [Créer une carte](./map-create.md) pour obtenir des instructions.

La ligne du deuxième bloc de code instancie un client de service.

Le troisième bloc de code initialise la [couche linestring](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addlinestrings) sur la carte.

Le quatrième bloc de code crée et ajoute des repères sur la carte pour représenter le point de départ et le point d’arrivée de l’itinéraire. Pour plus d’instructions, consultez la section relative à [l’ajout d’un repère sur la carte](map-add-pin.md).

Le bloc de code suivant utilise la fonction [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamerabounds) de la classe map pour définir le rectangle englobant de la carte en fonction des points de départ et d’arrivée de l’itinéraire.

Le sixième bloc de code construit une requête d’itinéraire.

Le dernier bloc de code interroge le service de routage Azure Maps avec la méthode [getRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/services.route?view=azure-iot-typescript-latest#getroutedirections) pour obtenir un itinéraire entre le point de départ et le point de destination. La réponse est ensuite analysée au format GeoJSON à l’aide de la méthode [getGeoJsonRoutes](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.geojson.geojsonroutedirectionsresponse?view=azure-iot-typescript-latest#getgeojsonroutes). Il ajoute toutes ces lignes sur la carte pour restituer l’itinéraire. Pour plus d’informations, consultez la section relative à [l’ajout d’une ligne sur la carte](./map-add-shape.md#addALine).

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les classes et les méthodes utilisées dans cet article : 

* [getRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/services.route?view=azure-iot-typescript-latest#getroutedirections)
* [getGeoJsonRoutes](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.geojson.geojsonroutedirectionsresponse?view=azure-iot-typescript-latest#getgeojsonroutes)
* [Couche linestring](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addlinestrings)
* [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)
    * [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamerabounds)
    * [addLinestrings](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addlinestrings)
    * [addPins](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addpins)

Pour consulter plus d’exemples de code à ajouter à vos cartes, consultez les articles suivants : 
* [Afficher le trafic sur la carte](./map-show-traffic.md)
* [Interaction avec la carte - Événements de souris](./map-events.md)
