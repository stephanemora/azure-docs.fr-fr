---
title: Affichage de directions avec Azure Maps | Microsoft Docs
description: Comment afficher des directions entre deux emplacements sur une carte Javascript
author: jingjing-z
ms.author: jinzh
ms.date: 3/7/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: codepen
ms.openlocfilehash: b8205383c25ba04212126e0e6ca1bd44e4efad1a
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59264520"
---
# <a name="show-directions-from-a-to-b"></a>Afficher des directions de A à B

Cet article vous explique comment exécuter une requête d’itinéraire et afficher l’itinéraire sur la carte.

Il existe deux manières de procéder. La première consiste à interroger l’[API Azure Maps Route](https://docs.microsoft.com/rest/api/maps/route/getroutedirections) via un module de service. La seconde consiste à utiliser le [API Fetch](https://fetch.spec.whatwg.org/) pour effectuer une requête de recherche pour le [API de Route Azure Maps](https://docs.microsoft.com/rest/api/maps/route/getroutedirections). Les deux méthodes sont décrites ci-dessous.

## <a name="query-the-route-via-service-module"></a>Demander l’itinéraire via le module de service

<iframe height='500' scrolling='no' title='Montrer un itinéraire de A à B sur une carte (module Service)' src='//codepen.io/azuremaps/embed/RBZbep/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consultez la section <a href='https://codepen.io/azuremaps/pen/RBZbep/'>Montrer un itinéraire de A à B sur une carte</a> par Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

Dans le code ci-dessus, le premier bloc de code construit un objet de mappage et définit le mécanisme d’authentification à utiliser la clé d’abonnement. Vous pouvez consulter la section [Créer une carte](./map-create.md) pour obtenir des instructions.

Le deuxième bloc de code crée un `SubscriptionKeyCredentialPolicy` pour authentifier les demandes HTTP vers Azure Maps avec la clé d’abonnement. `atlas.service.MapsURL.newPipeline()` utilise la stratégie `SubscriptionKeyCredential` et crée une instance de [pipeline](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-iot-typescript-latest). `routeURL` représente une URL des opérations sur l’[itinéraire](https://docs.microsoft.com/rest/api/maps/route) d’Azure Maps.

Le troisième bloc de code crée et ajoute un [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) objet à la carte.

Le quatrième bloc de code crée le début et fin [points](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) objets et les ajoute à l’objet de source de données.

Une ligne est une [fonctionnalité](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) de type LineString. Un élément [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) affiche les objets de ligne encapsulés dans [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) sous forme de lignes sur la carte. Le quatrième bloc de code crée une couche de lignes et l’ajoute à la carte. Consultez les propriétés d’une couche de lignes dans [LinestringLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest).

Une [couche de symboles](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) utilise du texte ou des icônes pour afficher les données basées sur le point, qui sont wrappées dans la [source de données](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) en tant que symboles sur la carte. Le cinquième bloc de code crée et ajoute une couche de symbole à la carte.

Le sixième bloc de code interroge le service de routage Azure Maps, qui fait partie de la [module service](https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas-service.min.js). Le [calculateRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.routeurl?view=azure-iot-typescript-latest#methods) méthode de la RouteURL est utilisée pour obtenir un itinéraire entre le début et les points de terminaison. Une collection de fonctionnalité GeoJSON à partir de la réponse est ensuite extraites à l’aide de la `geojson.getFeatures()` (méthode) et est ajouté à la source de données. Elle affiche ensuite la réponse sous forme de route sur la carte. Pour plus d’informations sur l’ajout d’une ligne à la carte, consultez [Ajouter une ligne sur la carte](./map-add-shape.md#addALine).

Le dernier bloc de code définit les limites de la carte à l’aide de la carte [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) propriété.

La requête de route, la source de données, les couches de lignes et de symboles, et les limites de l’appareil photo sont créés et définis au sein du [détecteur d’événements](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) de la carte pour garantir que les résultats sont affichés après le chargement complet de la carte.

## <a name="query-the-route-via-fetch-api"></a>L’itinéraire via extraire l’API de requête

<iframe height='500' scrolling='no' title='Afficher des directions de A à B sur une carte' src='//codepen.io/azuremaps/embed/zRyNmP/?height=469&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consultez la page <a href='https://codepen.io/azuremaps/pen/zRyNmP/'>Show directions from A to B on a map</a> (Afficher des directions de A à B sur une carte) d’Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

Dans le code ci-dessus, le premier bloc de code construit un objet de mappage et définit le mécanisme d’authentification à utiliser la clé d’abonnement. Vous pouvez consulter la section [Créer une carte](./map-create.md) pour obtenir des instructions.

Le deuxième bloc de code crée et ajoute un objet [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) à la carte.

Le troisième bloc de code crée les points de début et de destination pour la route et les ajoute à la source de données. Consultez la section relative à [l’ajout d’un repère sur la carte](map-add-pin.md) pour en savoir plus sur l’utilisation de [addPins](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest).

Un élément [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) affiche les objets de ligne encapsulés dans [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) sous forme de lignes sur la carte. Le quatrième bloc de code crée une couche de lignes et l’ajoute à la carte. Consultez les propriétés d’une couche de lignes dans [LineLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest).

Une [couche de symboles](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) utilise du texte ou des icônes pour afficher les données basées sur le point, qui sont wrappées dans la [source de données](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) en tant que symboles sur la carte. Le cinquième bloc de code crée et ajoute une couche de symbole à la carte. Consultez les propriétés d’une couche de symboles à la page [SymbolLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions?view=azure-iot-typescript-latest).

Le bloc de code suivant crée les points `SouthWest` et `NorthEast` à partir des points de début et de destination, et définit les limites de la carte à l’aide de la propriété [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) de la carte.

Le dernier bloc de code utilise le [API Fetch](https://fetch.spec.whatwg.org/) pour effectuer une requête de recherche pour le [API de Route Azure Maps](https://docs.microsoft.com/rest/api/maps/route/getroutedirections). La réponse est ensuite analysée. Si la réponse a réussi, les informations de latitude et longitude sont utilisées pour créer un tableau d’une ligne en connectant ces points. Les données de ligne sont ensuite ajoutées à la source de données pour restituer l’itinéraire sur la carte. Pour plus d’instructions, consultez la section relative à [l’ajout d’une ligne sur la carte](./map-add-shape.md#addALine).

La requête de route, la source de données, les couches de lignes et de symboles, et les limites de l’appareil photo sont créés et définis au sein du [détecteur d’événements](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) de la carte pour garantir que les résultats sont affichés après le chargement complet de la carte.

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les classes et les méthodes utilisées dans cet article :

> [!div class="nextstepaction"]
> [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

Pour voir des exemples de codes complets, consultez les articles suivants :

> [!div class="nextstepaction"]
> [Afficher le trafic sur la carte](./map-show-traffic.md)

> [!div class="nextstepaction"]
> [Interaction avec la carte - Événements de souris](./map-events.md)
