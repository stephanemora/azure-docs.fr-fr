---
title: Affichage de directions avec Azure Maps | Microsoft Docs
description: Comment afficher des directions entre deux emplacements sur une carte Javascript
author: jingjing-z
ms.author: jinzh
ms.date: 09/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: codepen
ms.openlocfilehash: ed522779f5a86e38ee12a246cea9ac85d0379f9e
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/17/2018
ms.locfileid: "45729037"
---
# <a name="show-directions-from-a-to-b"></a>Afficher des directions de A à B

Cet article vous explique comment exécuter une requête d’itinéraire et afficher l’itinéraire sur la carte.

Il existe deux manières de procéder. La première consiste à interroger l’[API Azure Maps Route](https://docs.microsoft.com/rest/api/maps/route/getroutedirections) via un module de service. La deuxième consiste à faire une requête [XMLHttpRequest](https://xhr.spec.whatwg.org/) à l’API. Les deux méthodes sont décrites ci-dessous.

## <a name="query-the-route-via-service-module"></a>Demander l’itinéraire via le module de service

<iframe height='500' scrolling='no' title='Montrer un itinéraire de A à B sur une carte (module Service)' src='//codepen.io/azuremaps/embed/RBZbep/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consultez la section <a href='https://codepen.io/azuremaps/pen/RBZbep/'>Montrer un itinéraire de A à B sur une carte</a> par Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

Le premier bloc de code construit un objet de carte. Vous pouvez consulter la section [Créer une carte](./map-create.md) pour obtenir des instructions.

La ligne dans le deuxième bloc de code instancie un client de service.

Le troisième bloc de code initialise la [couche linestring](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addlinestrings) sur la carte.

Le quatrième bloc de code crée et ajoute des repères sur la carte pour représenter le point de départ et le point d’arrivée de l’itinéraire. Consultez la section relative à [l’ajout d’un repère sur la carte](map-add-pin.md) pour en savoir plus sur l’utilisation de [addPins](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addpins).

Le bloc de code suivant utilise la fonction [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamerabounds) de la classe map pour définir le rectangle englobant de la carte en fonction des points de départ et d’arrivée de l’itinéraire.

Le sixième bloc de code construit une requête d’itinéraire.

Le dernier bloc de code interroge le service de routage Azure Maps avec la méthode [getRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/services.route?view=azure-iot-typescript-latest#getroutedirections) pour obtenir un itinéraire entre le point de départ et le point de destination. La réponse est ensuite analysée au format GeoJSON à l’aide de la méthode [getGeoJsonRoutes](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.geojson.geojsonroutedirectionsresponse?view=azure-iot-typescript-latest#getgeojsonroutes). Il ajoute toutes ces lignes sur la carte pour restituer l’itinéraire. Pour plus d’informations, consultez la section relative à [l’ajout d’une ligne sur la carte](./map-add-shape.md#addALine).

## <a name="query-the-route-via-xmlhttprequest"></a>Demander l’itinéraire via XMLHttpRequest

<iframe height='500' scrolling='no' title='Afficher des directions de A à B sur une carte' src='//codepen.io/azuremaps/embed/zRyNmP/?height=469&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consultez la page <a href='https://codepen.io/azuremaps/pen/zRyNmP/'>Show directions from A to B on a map</a> (Afficher des directions de A à B sur une carte) d’Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

Le premier bloc de code construit un objet de carte. Vous pouvez consulter la section [Créer une carte](./map-create.md) pour obtenir des instructions.

Le deuxième bloc de code crée et ajoute des repères sur la carte pour représenter le point de départ et le point d’arrivée de l’itinéraire. Consultez la section relative à [l’ajout d’un repère sur la carte](map-add-pin.md) pour en savoir plus sur l’utilisation de [addPins](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addpins).

Le troisième bloc de code utilise la fonction [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamerabounds) de la classe map pour définir le rectangle englobant de la carte en fonction des points de départ et d’arrivée de l’itinéraire.

Le quatrième bloc de code envoie un élément [XMLHttpRequest](https://xhr.spec.whatwg.org/) à [l’API d’itinéraire Azure Maps](https://docs.microsoft.com/rest/api/maps/route/getroutedirections).

Le dernier bloc de code analyse la réponse entrante. Si la réponse est correcte, il collecte les données de latitude et de longitude pour chaque étape. Il crée un ensemble de lignes en reliant chaque étape à l’étape suivante. Il ajoute toutes ces lignes sur la carte pour restituer l’itinéraire. Pour plus d’instructions, consultez la section relative à [l’ajout d’une ligne sur la carte](./map-add-shape.md#addALine).

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les classes et les méthodes utilisées dans cet article :

> [!div class="nextstepaction"]
> [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

Pour voir des exemples de codes complets, consultez les articles suivants :

> [!div class="nextstepaction"]
> [Afficher le trafic sur la carte](./map-show-traffic.md)

> [!div class="nextstepaction"]
> [Interaction avec la carte - Événements de souris](./map-events.md)
