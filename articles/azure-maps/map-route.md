---
title: Afficher les itinéraires sur une carte | Microsoft Azure Maps
description: Dans cet article, vous apprendrez à afficher des directions entre deux emplacements sur une carte à l’aide du Kit de développement logiciel (SDK) web Microsoft Azure Maps.
author: jingjing-z
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: codepen
ms.openlocfilehash: 359f2b42ca6f56087be53a5aeb328fe43a478d63
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/04/2020
ms.locfileid: "76988278"
---
# <a name="show-directions-from-a-to-b"></a>Afficher des directions de A à B

Cet article vous explique comment exécuter une requête d’itinéraire et afficher l’itinéraire sur la carte.

Il existe deux manières de procéder. La première consiste à interroger l’[API Azure Maps Route](https://docs.microsoft.com/rest/api/maps/route/getroutedirections) via un module de service. La seconde consiste à utiliser l' [API de récupération](https://fetch.spec.whatwg.org/) pour effectuer une requête de recherche pour l'[API de routage Azure Maps](https://docs.microsoft.com/rest/api/maps/route/getroutedirections). Les deux méthodes sont décrites ci-dessous.

## <a name="query-the-route-via-service-module"></a>Demander l’itinéraire via le module de service

<iframe height='500' scrolling='no' title='Montrer un itinéraire de A à B sur une carte (module Service)' src='//codepen.io/azuremaps/embed/RBZbep/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consultez la section <a href='https://codepen.io/azuremaps/pen/RBZbep/'>Montrer un itinéraire de A à B sur une carte</a> par Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

Dans le code ci-dessus, le premier bloc construit un objet de carte et définit le mécanisme d’authentification pour utiliser le jeton d’accès. Vous pouvez consulter la section [Créer une carte](./map-create.md) pour obtenir des instructions.

Le deuxième bloc de code crée un `TokenCredential` pour authentifier les requêtes HTTP auprès d’Azure Maps avec le jeton d’accès. Il transfère ensuite le `TokenCredential` à `atlas.service.MapsURL.newPipeline()` et crée une instance [Pipeline](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-maps-typescript-latest). `routeURL` représente une URL des opérations sur l’[itinéraire](https://docs.microsoft.com/rest/api/maps/route) d’Azure Maps.

Le troisième bloc de code crée et ajoute un objet [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) à la carte.

Le quatrième bloc de code crée des objets [point](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) de début et de fin et les ajoute à l’objet dataSource.

Une ligne est une [fonctionnalité](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) pour LineString. Un élément [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) affiche les objets de ligne encapsulés dans [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) sous forme de lignes sur la carte. Le quatrième bloc de code crée une couche de lignes et l’ajoute à la carte. Consultez les propriétés d’une couche de lignes dans [LinestringLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest).

Une [couche de symboles](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) utilise des textes ou des icônes pour restituer des données basées sur des points qui sont enveloppées dans [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest). Les textes ou les icônes sont rendus sous forme de symboles sur la carte. Le cinquième bloc de code crée une couche de symboles et l’ajoute à la carte.

Le sixième bloc de code interroge le service de routage Azure Maps, qui fait partie du [module de service](how-to-use-services-module.md). La méthode [calculateRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.routeurl?view=azure-iot-typescript-latest#methods) de RouteURL est utilisée pour obtenir une route entre les points de début et de fin. Une collection de fonctionnalités GeoJSON de la réponse est alors extraite à l’aide de la méthode `geojson.getFeatures()` et ajoutée à la source de données. Elle affiche ensuite la réponse sous forme de route sur la carte. Pour plus d’informations sur l’ajout d’une ligne à la carte, consultez [Ajouter une ligne sur la carte](map-add-line-layer.md).

Le dernier bloc de code définit les limites de la carte à l’aide de la propriété [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) de la carte.

La requête d’itinéraire, la source de données, le symbole, les couches de lignes et les limites de la caméra sont créés et définis à l’intérieur du [détecteur d’événements](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) de la carte. Cette structure de code garantit que les résultats s’affichent uniquement après le chargement complet de la carte.

## <a name="query-the-route-via-fetch-api"></a>Demander l'itinéraire via l'API de récupération

<iframe height='500' scrolling='no' title='Afficher des directions de A à B sur une carte' src='//codepen.io/azuremaps/embed/zRyNmP/?height=469&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consultez la page <a href='https://codepen.io/azuremaps/pen/zRyNmP/'>Show directions from A to B on a map</a> (Afficher des directions de A à B sur une carte) d’Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

Dans le code ci-dessus, le premier bloc de code construit un objet de carte et définit le mécanisme d’authentification pour utiliser le jeton d’accès. Vous pouvez consulter la section [Créer une carte](./map-create.md) pour obtenir des instructions.

Le deuxième bloc de code crée et ajoute un objet [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) à la carte.

Le troisième bloc de code crée les points de début et de destination pour la route et les ajoute à la source de données. Consultez la section relative à [l’ajout d’un repère sur la carte](map-add-pin.md) pour en savoir plus sur l’utilisation de [addPins](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest).

Un élément [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) affiche les objets de ligne encapsulés dans [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) sous forme de lignes sur la carte. Le quatrième bloc de code crée une couche de lignes et l’ajoute à la carte. Consultez les propriétés d’une couche de lignes dans [LineLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest).

Une [couche de symboles](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) utilise du texte ou des icônes pour afficher les données basées sur le point, qui sont wrappées dans la [source de données](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) en tant que symboles sur la carte. Le cinquième bloc de code crée une couche de symboles et l’ajoute à la carte. Consultez les propriétés d’une couche de symboles à la page [SymbolLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions?view=azure-iot-typescript-latest).

Le bloc de code suivant crée les points `SouthWest` et `NorthEast` à partir des points de début et de destination, et définit les limites de la carte à l’aide de la propriété [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) de la carte.

Le dernier bloc de code utilise l'[API de récupération](https://fetch.spec.whatwg.org/) pour effectuer une requête de recherche pour l'[API de routage Azure Maps](https://docs.microsoft.com/rest/api/maps/route/getroutedirections). La réponse est ensuite analysée. Si la réponse est correcte, les informations de latitude et longitude sont utilisées pour créer une ligne en reliant ces points. Les données de la ligne sont ensuite ajoutées à la source de données pour afficher l’itinéraire sur la carte. Pour plus d’instructions, consultez la section relative à [l’ajout d’une ligne sur la carte](map-add-line-layer.md).

La requête d’itinéraire, la source de données, le symbole, les couches de lignes et les limites de la caméra sont créés et définis à l’intérieur du [détecteur d’événements](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) de la carte. Là encore, nous voulons nous assurer que les résultats s’affichent après le chargement complet de la carte.

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les classes et les méthodes utilisées dans cet article :

> [!div class="nextstepaction"]
> [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

Pour voir des exemples de codes complets, consultez les articles suivants :

> [!div class="nextstepaction"]
> [Afficher le trafic sur la carte](./map-show-traffic.md)

> [!div class="nextstepaction"]
> [Interaction avec la carte - Événements de souris](./map-events.md)
