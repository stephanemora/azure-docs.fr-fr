---
title: Itinéraires multiples avec Azure Maps | Microsoft Docs
description: Rechercher des itinéraires pour différents modes de déplacement avec Azure Maps
author: dsk-2015
ms.author: dkshir
ms.date: 10/02/2018
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 340bf83f07b9e730cc43baccc60a39f5ba1f9942
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2018
ms.locfileid: "48815305"
---
# <a name="find-routes-for-different-modes-of-travel-using-azure-maps"></a>Rechercher des itinéraires pour différents modes de déplacement avec Azure Maps

Ce didacticiel montre comment utiliser votre compte Azure Maps et Route Service pour trouver l’itinéraire vers le point d’intérêt de votre choix, en fonction de votre mode de déplacement. Vous affichez deux itinéraires différents sur votre carte, un dédié aux voitures et l’autre aux camions, qui peuvent présenter des restrictions relatives à la hauteur, au poids ou aux chargements dangereux. Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer une page web à l’aide de l’API Map Control
> * Visualiser le flux du trafic sur votre carte
> * Créer des requêtes d’itinéraire déclarant le mode de déplacement
> * Afficher plusieurs itinéraires sur votre carte

## <a name="prerequisites"></a>Prérequis

Avant de continuer, exécutez les étapes du premier didacticiel afin de [créer votre compte Azure Maps](./tutorial-search-location.md#createaccount) et d’[obtenir la clé d’abonnement pour votre compte](./tutorial-search-location.md#getkey).

## <a name="create-a-new-map"></a>Créer une carte

Les étapes suivantes vous indiquent comment créer une page HTML statique intégrée avec l’API Map Control.

1. Sur votre ordinateur local, créez un fichier et nommez-le **MapTruckRoute.html**.
2. Ajoutez les composants HTML suivants au fichier :

    ```HTML
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="utf-8" />
        <meta name="viewport" content="width=device-width, user-scalable=no" />
        <title>Map Truck Route</title>
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/css/atlas.min.css?api-version=1" type="text/css" />
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
    L’en-tête HTML intègre les emplacements des ressources des fichiers CSS et JavaScript pour la bibliothèque Azure Maps. Le segment *script* dans le corps du fichier HTML contient le code JavaScript inline associé à la carte.
3. Ajoutez le code JavaScript suivant au bloc *script* du fichier HTML. Remplacez la chaîne **\<your account key\>** par la clé primaire copiée de votre compte Maps. Si vous n’indiquez pas à la carte l’emplacement sur lequel se concentrer, la carte du monde s’affiche. Ce code définit le point central sur la carte, et déclare un niveau de zoom qui vous permet de vous concentrer sur une zone spécifique par défaut.

    ```JavaScript
    // Instantiate map to the div with id "map"
    var MapsAccountKey = "<your account key>";
    var map = new atlas.Map("map", {
        "subscription-key": MapsAccountKey
         center: [-118.2437, 34.0522],
         zoom: 12
    });
    ```
    **atlas.Map** fournit le contrôle d’une carte web visuelle et interactive et est un composant de l’API Azure Map Control.

4. Enregistrez le fichier et ouvrez-le dans votre navigateur. À ce stade, vous disposez d’une carte de base que vous pouvez développer. 

   ![Afficher la carte de base](./media/tutorial-prioritized-routes/basic-map.png)

## <a name="visualize-traffic-flow"></a>Visualiser le flux du trafic

1. Ajoutez l’affichage du trafic à la carte.  L’instance **map.addEventListener** assure que toutes les fonctions de correspondance ajoutées à la carte sont chargées une fois le chargement de la carte terminé.

    ```JavaScript
    map.addEventListener("load", function() {
        // Add Traffic Flow to the Map
        map.setTraffic({
            flow: "relative"
        });
    });
    ```
    Ce code définit le flux du trafic sur `relative`, qui correspond à la vitesse de la route correspondant à un trafic fluide. Vous pouvez également le définir sur la vitesse `absolute` de la route, ou sur `relative-delay` qui affiche la vitesse relative lorsqu’elle diffère du trafic fluide.

2. Enregistrez le fichier **MapTruckRoute.html** et actualisez la page dans votre navigateur. Vous devriez observer les rues de Los Angeles avec leurs données de trafic actuelles.

   ![Afficher les données de trafic](./media/tutorial-prioritized-routes/traffic-map.png)

<a id="queryroutes"></a>

## <a name="set-start-and-end-points"></a>Définir les points de départ et d’arrivée

Pour ce didacticiel, définissez le point de départ sur une entreprise fictive de Seattle appelée Fabrikam et le point d’arrivée sur un siège Microsoft.

1. Ajoutez le code JavaScript suivant pour créer les épingles pour les points de départ et d’arrivée de l’itinéraire :

    ```JavaScript
    // Create the GeoJSON objects which represent the start and end point of the route
    var startPoint = new atlas.data.Point([-122.356099, 47.580045]);
    var startPin = new atlas.data.Feature(startPoint, {
        title: "Fabrikam, Inc.",
        icon: "pin-round-blue"
    });

    var destinationPoint = new atlas.data.Point([-122.130137, 47.644702]);
    var destinationPin = new atlas.data.Feature(destinationPoint, {
        title: "Microsoft",
        icon: "pin-blue"
    });
    ```
    Ce code crée deux [objets GeoJSON](https://en.wikipedia.org/wiki/GeoJSON) pour représenter les points de départ et d’arrivée de l’itinéraire.

2. Ajoutez le code JavaScript suivant pour ajouter à la carte les points de départ et d’arrivée :

    ```JavaScript
    // Fit the map window to the bounding box defined by the start and destination points
    var swLon = Math.min(startPoint.coordinates[0], destinationPoint.coordinates[0]);
    var swLat = Math.min(startPoint.coordinates[1], destinationPoint.coordinates[1]);
    var neLon = Math.max(startPoint.coordinates[0], destinationPoint.coordinates[0]);
    var neLat = Math.max(startPoint.coordinates[1], destinationPoint.coordinates[1]);
    map.setCameraBounds({
        bounds: [swLon, swLat, neLon, neLat],
        padding: 100
    });
    
    map.addEventListener("load", function() { 
        // Add pins to the map for the start and end point of the route
        map.addPins([startPin, destinationPin], {
            name: "route-pins",
            textFont: "SegoeUi-Regular",
            textOffset: [0, -20]
        });
    });
    ```
    L’appel **map.setCameraBounds** ajuste la fenêtre de la carte en fonction des coordonnées des points de départ et d’arrivée. L’instance **map.addEventListener** assure que toutes les fonctions de correspondance ajoutées à la carte sont chargées une fois le chargement de la carte terminé. L’API **map.addPins** ajoute les points au contrôle de carte sous la forme de composants visuels.

3. Enregistrez le fichier et actualisez votre navigateur afin d’afficher les repères sur votre carte. Même si vous avez défini votre carte avec un point central sur Los Angeles, l’instance **map.setCameraBounds** a déplacé la vue pour afficher les points de départ et d’arrivée.

   ![Afficher la carte avec les points de départ et d’arrivée](./media/tutorial-prioritized-routes/pins-map.png)

<a id="multipleroutes"></a>

## <a name="render-routes-prioritized-by-mode-of-travel"></a>Afficher les itinéraires priorisés par mode de déplacement

Cette section montre comment utiliser l’API Route Service d’Azure Maps pour rechercher plusieurs itinéraires entre un point de départ donné et une destination, en fonction de votre mode de transport. Route Service fournit des API pour planifier les itinéraires les plus *rapides*, *courts*, *économiques* ou *intéressants* entre deux emplacements, en fonction des conditions de circulation actuelles. Grâce à la base de données de trafic historique complète d’Azure, il permet également aux utilisateurs de planifier des itinéraires, durées comprises, pour n’importe quels jour et heure. Pour plus d’informations, voir [Get route directions](https://docs.microsoft.com/rest/api/maps/route/getroutedirections) (Obtenir les itinéraires).  Tous les blocs de code suivants doivent être ajoutés **dans l’eventListener du chargement de la carte** pour assurer leur chargement une fois la carte complètement chargée.

1. Tout d’abord, ajoutez une nouvelle couche sur la carte pour afficher l’itinéraire, ou *linestring*. Ce didacticiel comporte deux itinéraires, **car-route** et **truck-route**, chacun affichant un style propre. Ajoutez le code JavaScript suivant au bloc *script* :

    ```JavaScript
    // Place route layers on the map
    var carRouteLayerName = "car-route";
    map.addLinestrings([], {
        name: carRouteLayerName,
        color: "#B76DAB",
        width: 5,
        cap: "round",
        join: "round",
        before: "labels"
    });

    var truckRouteLayerName = "truck-route";
    map.addLinestrings([], {
        name: truckRouteLayerName,
        color: "#2272B9",
        width: 9,
        cap: "round",
        join: "round",
        before: carRouteLayerName
    });
    ```

2. Ajoutez le code JavaScript suivant au bloc *script*, afin de solliciter l’itinéraire pour un camion et afficher les résultats sur la carte :

    ```JavaScript
    // Instantiate the service client  
    var client = new atlas.service.Client(MapsAccountKey);

    // Construct the route query string
    var routeQuery = startPoint.coordinates[1] +
        "," +
        startPoint.coordinates[0] +
        ":" +
        destinationPoint.coordinates[1] +
        "," +
        destinationPoint.coordinates[0];

    // Execute the truck route query then add the route to the map once a response is received  
    client.route.getRouteDirections(routeQuery, {
        travelMode: "truck",
        vehicleWidth: 2,
        vehicleHeight: 2,
        vehicleLength: 5,
        vehicleLoadType: "USHazmatClass2"
    }).then(response => {
        // Parse the response into GeoJSON
        var geoJsonResponse = new atlas.service.geojson
            .GeoJsonRouteDirectionsResponse(response);

        // Get the first in the array of routes and add it to the map
        map.addLinestrings([geoJsonResponse.getGeoJsonRoutes().features[0]], {
            name: truckRouteLayerName
        });
    });
    ```
    L’extrait de code ci-dessus instancie un client de service et construit une chaîne de requête d’itinéraire. Il interroge le service de routage d’Azure Maps via la méthode [getRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/services.route?view=azure-iot-typescript-latest#getroutedirections), puis analyse la réponse au format GeoJSON à l’aide de [getGeoJsonRouteDirectionsResponse](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.geojson.geojsonroutedirectionsresponse?view=azure-iot-typescript-latest). Il crée ensuite un tableau de coordonnées pour l’itinéraire retourné et l’ajoute à la couche `truckRouteLayerName` de la carte.

3. Ajoutez le code JavaScript suivant afin de solliciter l’itinéraire pour une voiture et afficher les résultats :

    ```JavaScript
    // Execute the car route query then add the route to the map once a response is received  
    client.route.getRouteDirections(routeQuery).then(response => {
        // Parse the response into GeoJSON
        var geoJsonResponse = new tlas.service.geojson
            .GeoJsonRouteDiraectionsResponse(response);

        // Get the first in the array of routes and add it to the map 
        map.addLinestrings([geoJsonResponse.getGeoJsonRoutes().features[0]], {
            name: carRouteLayerName
        });
    });
    ```
    Cet extrait de code utilise la requête d’itinéraire de camion, mais pour une voiture. Il interroge le service de routage d’Azure Maps via la méthode [getRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/services.route?view=azure-iot-typescript-latest#getroutedirections), puis analyse la réponse au format GeoJSON à l’aide de [getGeoJsonRouteDirectionsResponse](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.geojson.geojsonroutedirectionsresponse?view=azure-iot-typescript-latest). Il crée ensuite un tableau de coordonnées pour l’itinéraire retourné et l’ajoute à la couche `carRouteLayerName` de la carte.

4. Enregistrez le fichier **MapTruckRoute.html** et actualisez votre navigateur afin d’afficher les résultats. Dans le cadre d’une connexion efficace avec les API Maps, vous devriez observer une carte similaire au contenu suivant.

    ![Itinéraires priorisés avec Azure Route Service](./media/tutorial-prioritized-routes/prioritized-routes.png)

    L’itinéraire réservé aux camions est bleu et plus épais, tandis que celui réservé aux voitures est mauve et plus fin. L’itinéraire réservé aux voitures passe au-dessus du Lac Washington via l’I-90, qui traverse des tunnels installés sous des zones résidentielles, ce qui interdit tout transport de déchets dangereux. L’itinéraire des camions, pour lequel est défini le type de chargement USHazmatClass2, utilise à raison une voie de circulation différente.

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à :

> [!div class="checklist"]
> * Créer une page web à l’aide de l’API Map Control
> * Visualiser le flux du trafic sur votre carte
> * Créer des requêtes d’itinéraire déclarant le mode de déplacement
> * Afficher plusieurs itinéraires sur votre carte

Vous trouverez ici un exemple de code pour ce didacticiel :

> [Itinéraires multiples avec Azure Maps](https://github.com/Azure-Samples/azure-maps-samples/blob/master/src/truckRoute.html)

Pour en savoir plus sur la couverture et les fonctionnalités d’Azure Maps :

> [!div class="nextstepaction"]
> [Niveaux de zoom et grille mosaïque](zoom-levels-and-tile-grid.md)

Pour bénéficier de davantage d’exemples de code et d’une expérience de codage interactive :

> [!div class="nextstepaction"]
> [Comment utiliser le contrôle de carte](how-to-use-map-control.md)