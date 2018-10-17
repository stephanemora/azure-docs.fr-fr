---
title: Rechercher un itinéraire avec Azure Maps | Microsoft Docs
description: Établir un itinéraire vers un point d’intérêt avec Azure Maps
author: dsk-2015
ms.author: dkshir
ms.date: 10/02/2018
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 3bf1aa6d1b9bd65c28ef99ddbac71fb75daf99e7
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2018
ms.locfileid: "48816716"
---
# <a name="route-to-a-point-of-interest-using-azure-maps"></a>Établir un itinéraire vers un point d’intérêt avec Azure Maps

Ce didacticiel montre comment utiliser votre compte Azure Maps et le SDK Route Service pour rechercher l’itinéraire vers votre point d’intérêt. Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer une page web à l’aide de l’API Map Control
> * Définir les coordonnées d’une adresse
> * Interroger Route Service afin d’obtenir des indications pour rejoindre un point d’intérêt

## <a name="prerequisites"></a>Prérequis

Avant de continuer, exécutez les étapes du didacticiel précédent afin de [créer votre compte Azure Maps](./tutorial-search-location.md#createaccount) et d’[obtenir la clé d’abonnement pour votre compte](./tutorial-search-location.md#getkey).

<a id="getcoordinates"></a>

## <a name="create-a-new-map"></a>Créer une carte

Les étapes suivantes vous indiquent comment créer une page HTML statique intégrée avec l’API Map Control.

1. Sur votre ordinateur local, créez un fichier et nommez-le **MapRoute.html**.
2. Ajoutez les composants HTML suivants au fichier :

    ```HTML
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="utf-8" />
        <meta name="viewport" content="width=device-width, user-scalable=no" />
        <title>Map Route</title>
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/css/atlas.min.css?api-version=1" type="text/css"/>
        <script src="https://atlas.microsoft.com/sdk/js/atlas.min.js?api-version=1"></script>
        <script src="https://atlas.microsoft.com/sdk/js/atlas-service.min.js?api-version=1"></script>
        <style>
            html,
            body {
                width: 100%;
                height: 100%;
                padding: 0;
                margin: 0;
            }

            #map {
                width: 100%;
                height: 100%;
            }
        </style>
    </head>

    <body>
        <div id="map"></div>
        <script>
            // Embed Map Control JavaScript code here
        </script>
    </body>

    </html>
    ```
    L’en-tête HTML intègre les emplacements des ressources des fichiers CSS et JavaScript pour la bibliothèque Azure Maps. Le segment *script* dans le corps du fichier HTML contient le code JavaScript inline permettant d’accéder aux API Azure Maps.

3. Ajoutez le code JavaScript suivant au bloc *script* du fichier HTML. Remplacez la chaîne **\<your account key\>** par la clé primaire copiée de votre compte Maps.

    ```JavaScript
    // Instantiate map to the div with id "map"
    var MapsAccountKey = "<your account key>";
    var map = new atlas.Map("map", {
        "subscription-key": MapsAccountKey
    });
    ```
    **atlas.Map** fournit le contrôle d’une carte web visuelle et interactive et est un composant de l’API Azure Map Control.

4. Enregistrez le fichier et ouvrez-le dans votre navigateur. À ce stade, vous disposez d’une carte de base que vous pouvez développer.

   ![Afficher la carte de base](./media/tutorial-route-location/basic-map.png)

## <a name="set-start-and-end-points"></a>Définir les points de départ et d’arrivée

Pour ce didacticiel, définissez Microsoft comme point de départ, et une station-service de Seattle comme point de destination.

1. Dans le même bloc *script* du fichier **MapRoute.html**, ajoutez le code JavaScript suivant afin de créer les points de départ et d’arrivée de l’itinéraire :

    ```JavaScript
    // Create the GeoJSON objects which represent the start and end point of the route
    var startPoint = new atlas.data.Point([-122.130137, 47.644702]);
    var startPin = new atlas.data.Feature(startPoint, {
        title: "Microsoft",
        icon: "pin-round-blue"
    });

    var destinationPoint = new atlas.data.Point([-122.3352, 47.61397]);
    var destinationPin = new atlas.data.Feature(destinationPoint, {
        title: "Contoso Oil & Gas",
        icon: "pin-blue"
    });
    ```
    Ce code crée deux [objets GeoJSON](https://en.wikipedia.org/wiki/GeoJSON) pour représenter les points de départ et d’arrivée de l’itinéraire.

2. Ajoutez le code JavaScript suivant pour ajouter à la carte les marqueurs des points de départ et d’arrivée :

    ```JavaScript
    // Fit the map window to the bounding box defined by the start and destination points
    var swLon = Math.min(startPoint.coordinates[0], destinationPoint.coordinates[0]);
    var swLat = Math.min(startPoint.coordinates[1], destinationPoint.coordinates[1]);
    var neLon = Math.max(startPoint.coordinates[0], destinationPoint.coordinates[0]);
    var neLat = Math.max(startPoint.coordinates[1], destinationPoint.coordinates[1]);
    map.setCameraBounds({
        bounds: [swLon, swLat, neLon, neLat],
        padding: 50
    });

    map.addEventListener("load", function () { 
        // Add pins to the map for the start and end point of the route
        map.addPins([startPin, destinationPin], {
            name: "route-pins",
            textFont: "SegoeUi-Regular",
            textOffset: [0, -20]
        });
    });
    ```
    L’instance **map.setCameraBounds** ajuste la fenêtre de la carte selon les coordonnées des points de départ et d’arrivée. L’instance **map.addEventListener** s’assure que toutes les fonctions de correspondance ajoutées à la carte sont chargées une fois le chargement terminé. L’API **map.addPins** dans l’écouteur d’événement ajoute les points au contrôle de carte sous la forme de composants visuels.

3. Enregistrez le fichier **MapRoute.html** et actualisez votre navigateur. Désormais, la carte est centrée sur Seattle, et vous pouvez observer le repère rond bleu marquant le point de départ et l’autre repère bleu marquant le point d’arrivée.

   ![Afficher la carte avec les points de départ et d’arrivée marqués](./media/tutorial-route-location/map-pins.png)

<a id="getroute"></a>

## <a name="get-directions"></a>Obtenir les directions

Cette section montre comment utiliser les API Route Service d’Azure Maps pour rechercher l’itinéraire entre un point de départ donné et une destination. Route Service fournit les API dédiées à la planification des itinéraires les plus *rapides*, *courts*, *économiques*, ou *intéressants* entre deux emplacements. Grâce à la base de données de trafic historique complète d’Azure, il permet également aux utilisateurs de planifier des itinéraires, durées comprises, pour n’importe quels jour et heure. Pour plus d’informations, voir [Get route directions](https://docs.microsoft.com/rest/api/maps/route/getroutedirections) (Obtenir les itinéraires). Toutes les fonctionnalités suivantes doivent être ajoutées **dans l’eventListener du chargement de la carte** pour s’assurer de leur chargement complet une fois la carte chargée.

1. Tout d’abord, ajoutez une nouvelle couche sur la carte pour afficher l’itinéraire, ou *linestring*. Ajoutez le code JavaScript suivant au bloc *script*.

    ```JavaScript
    // Initialize the linestring layer for routes on the map
    var routeLinesLayerName = "routes";
    map.addLinestrings([], {
        name: routeLinesLayerName,
        color: "#2272B9",
        width: 5,
        cap: "round",
        join: "round",
        before: "labels"
    });
    ```

2. Instanciez le service client en ajoutant le code Javascript suivant au bloc de script.
    ```JavaScript
    var client = new atlas.service.Client(MapsAccountKey);
    ```

3. Ajoutez le bloc de code suivant pour construire une chaîne de requête d’itinéraire.
    ```JavaScript
    // Construct the route query string
    var routeQuery = startPoint.coordinates[1] +
        "," +
        startPoint.coordinates[0] +
        ":" +
        destinationPoint.coordinates[1] +
        "," +
        destinationPoint.coordinates[0];
    ```

4. Pour obtenir l’itinéraire, ajoutez le bloc de code suivant au script. Il interroge le service de routage Azure Maps via la méthode [getRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/services.route?view=azure-iot-typescript-latest#getroutedirections), puis analyse la réponse au format GeoJSON à l’aide de [getGeoJsonRoutes](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.geojson.geojsonroutedirectionsresponse?view=azure-iot-typescript-latest#getgeojsonroutes). Elle ajoute ensuite toutes les lignes de réponse sur la carte pour restituer l’itinéraire. Pour plus d’informations, consultez la section relative à [l’ajout d’une ligne sur la carte](./map-add-shape.md#addALine).

    ```JavaScript
    // Execute the query then add the route to the map once a response is received  
    client.route.getRouteDirections(routeQuery).then(response => {
         // Parse the response into GeoJSON
         var geoJsonResponse = new atlas.service.geojson.GeoJsonRouteDirectionsResponse(response);

         // Get the first in the array of routes and add it to the map
         map.addLinestrings([geoJsonResponse.getGeoJsonRoutes().features[0]], {
             name: routeLinesLayerName
         });
    });
    ```

5. Enregistrez le fichier **MapRoute.html** et actualisez votre navigateur web. Dans le cadre d’une connexion efficace avec les API Maps, vous devriez observer une carte similaire au contenu suivant.

    ![Azure Map Control et Route Service](./media/tutorial-route-location/map-route.png)

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à :

> [!div class="checklist"]
> * Créer une page web à l’aide de l’API Map Control
> * Définir les coordonnées d’une adresse
> * Interroger Route Service afin d’obtenir des indications pour rejoindre un point d’intérêt

Vous trouverez ici un exemple de code pour ce didacticiel :

> [Rechercher un itinéraire avec Azure Maps](https://github.com/Azure-Samples/azure-maps-samples/blob/master/src/route.html)

Le didacticiel suivant vous explique comment créer une requête d’itinéraire avec des restrictions comme le mode de déplacement ou le type de chargement, puis affiche les différents itinéraires sur la même carte.

> [!div class="nextstepaction"]
> [Rechercher des itinéraires pour différents modes de déplacements](./tutorial-prioritized-routes.md)
