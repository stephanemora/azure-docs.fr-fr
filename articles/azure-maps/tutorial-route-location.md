---
title: Rechercher un itinéraire avec Azure Maps | Microsoft Docs
description: Établir un itinéraire vers un point d’intérêt avec Azure Maps
services: azure-maps
keywords: ''
author: kgremban
ms.author: kgremban
ms.date: 05/07/2018
ms.topic: tutorial
ms.service: azure-maps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 73ca61140f05a65ca75cd703ed226773b9a43dfa
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2018
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
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/css/atlas.min.css?api-version=1.0" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/js/atlas.min.js?api-version=1.0"></script>
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

    // Add pins to the map for the start and end point of the route
    map.addPins([startPin, destinationPin], {
        name: "route-pins",
        textFont: "SegoeUi-Regular",
        textOffset: [0, -20]
    });
    ``` 
    L’instance **map.setCameraBounds** ajuste la fenêtre de la carte selon les coordonnées des points de départ et d’arrivée. L’API **map.addPins** ajoute les points au contrôle de carte sous la forme de composants visuels.

7. Enregistrez le fichier **MapRoute.html** et actualisez votre navigateur. Désormais, la carte est centrée sur Seattle, et vous pouvez observer le repère rond bleu marquant le point de départ et l’autre repère bleu marquant le point d’arrivée.

   ![Afficher la carte avec les points de départ et d’arrivée marqués](./media/tutorial-route-location/map-pins.png)

<a id="getroute"></a>

## <a name="get-directions"></a>Obtenir les directions

Cette section montre comment utiliser les API Route Service d’Azure Maps pour rechercher l’itinéraire entre un point de départ donné et une destination. Route Service fournit les API dédiées à la planification des itinéraires les plus *rapides*, *courts*, *économiques*, ou *intéressants* entre deux emplacements. Grâce à la base de données de trafic historique complète d’Azure, il permet également aux utilisateurs de planifier des itinéraires, durées comprises, pour n’importe quels jour et heure. Pour plus d’informations, voir [Get route directions](https://docs.microsoft.com/rest/api/maps/route/getroutedirections) (Obtenir les itinéraires).


1. Tout d’abord, ajoutez une nouvelle couche sur la carte pour afficher l’itinéraire, ou *linestring*. Ajoutez le code JavaScript suivant au bloc *script* :

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

2. Créez un élément [XMLHttpRequest](https://xhr.spec.whatwg.org/) et ajoutez un gestionnaire d’événements afin d’analyser la réponse JSON transmise par le service Route Service d’Azure Maps. Ce code construit un tableau de coordonnées dédié aux segments de ligne de l’itinéraire, puis ajoute l’ensemble de coordonnées à la couche linestrings de la carte. 

    ```JavaScript
    // Perform a request to the route service and draw the resulting route on the map
    var xhttp = new XMLHttpRequest();
    xhttp.onreadystatechange = function () {
        if (this.readyState == 4 && this.status == 200) {
            var response = JSON.parse(xhttp.responseText);

            var route = response.routes[0];
            var routeCoordinates = [];
            for (var leg of route.legs) {
                var legCoordinates = leg.points.map((point) => [point.longitude, point.latitude]);
                routeCoordinates = routeCoordinates.concat(legCoordinates);
            }

            var routeLinestring = new atlas.data.LineString(routeCoordinates);
            map.addLinestrings([new atlas.data.Feature(routeLinestring)], { name: routeLinesLayerName });
        }
    };
    ```

3. Ajoutez le code suivant afin de générer la requête et d’envoyer l’élément XMLHttpRequest au service Route Service d’Azure Maps :

    ```JavaScript
    var url = "https://atlas.microsoft.com/route/directions/json?";
    url += "api-version=1.0";
    url += "&subscription-key=" + MapsAccountKey;
    url += "&query=" + startPoint.coordinates[1] + "," + startPoint.coordinates[0] + ":" +
        destinationPoint.coordinates[1] + "," + destinationPoint.coordinates[0];

    xhttp.open("GET", url, true);
    xhttp.send();
    ```

3. Enregistrez le fichier **MapRoute.html** et actualisez votre navigateur web. Dans le cadre d’une connexion efficace avec les API Maps, vous devriez observer une carte similaire au contenu suivant. 

    ![Azure Map Control et Route Service](./media/tutorial-route-location/map-route.png)


## <a name="next-steps"></a>Étapes suivantes
Dans ce didacticiel, vous avez appris à :

> [!div class="checklist"]
> * Créer une page web à l’aide de l’API Map Control
> * Définir les coordonnées d’une adresse
> * Interroger Route Service afin d’obtenir des indications pour rejoindre un point d’intérêt

Le didacticiel suivant vous explique comment créer une requête d’itinéraire avec des restrictions comme le mode de déplacement ou le type de chargement, puis affiche les différents itinéraires sur la même carte. 

> [!div class="nextstepaction"]
> [Rechercher des itinéraires pour différents modes de déplacements](./tutorial-prioritized-routes.md)
