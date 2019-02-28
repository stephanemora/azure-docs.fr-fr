---
title: Rechercher un itinéraire avec Azure Maps | Microsoft Docs
description: Établir un itinéraire vers un point d’intérêt avec Azure Maps
author: walsehgal
ms.author: v-musehg
ms.date: 11/14/2018
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 7b5b82e80ab4998f7cd106f469bf7ac8e271285d
ms.sourcegitcommit: 7723b13601429fe8ce101395b7e47831043b970b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2019
ms.locfileid: "56588362"
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
    <html>
    <head>
        <title>Map Route</title>
        <meta charset="utf-8" />
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />
        
        <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/css/atlas.min.css?api-version=1" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/js/atlas.min.js?api-version=1"></script>

        <!-- Add a reference to the Azure Maps Services Module JavaScript file. -->
        <script src="https://atlas.microsoft.com/sdk/js/atlas-service.js?api-version=1"></script>
        
        <script>
            var map, datasource, client;

            function GetMap() {
                //Add Map Control JavaScript code here.
            }
        </script>
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
    <body onload="GetMap()">
        <div id="myMap"></div>
    </body>
    </html>
    ```
    
    Notez que l’en-tête HTML inclut les fichiers de ressources CSS et JavaScript hébergés par la bibliothèque Azure Map Control. Notez l’événement `onload` sur le corps de la page, qui appelle la fonction `GetMap` lorsque le corps de la page est chargé. Cette fonction est destinée à contenir du code JavaScript inline pour accéder aux API Azure Maps. 

3. Ajoutez le code JavaScript suivant à la fonction `GetMap`. Remplacez la chaîne **\<Your Azure Maps Key\>** (Votre clé Azure Maps) par la clé primaire copiée de votre compte Maps.

    ```JavaScript
    //Add your Azure Maps subscription key to the map SDK. 
    atlas.setSubscriptionKey('<Your Azure Maps Key>');

    //Initialize a map instance.
    map = new atlas.Map('myMap');
    ```

    `atlas.Map` fournit le contrôle d’une carte web visuelle et interactive et est un composant de l’API Azure Map Control.

4. Enregistrez le fichier et ouvrez-le dans votre navigateur. À ce stade, vous disposez d’une carte de base que vous pouvez développer.

   ![Afficher la carte de base](./media/tutorial-route-location/basic-map.png)

## <a name="define-how-the-route-will-be-rendered"></a>Définir le rendu de l’itinéraire

Dans ce tutoriel, un itinéraire simple sera affiché à l’aide d’une icône de symbole pour le début et la fin de l’itinéraire et d’une ligne pour le chemin d’accès de l’itinéraire.

1. Dans la fonction GetMap, après l’initialisation de la carte, ajoutez le code JavaScript suivant.

    ```JavaScript
    //Wait until the map resources have fully loaded.
    map.events.add('load', function () {

        //Create a data source and add it to the map.
        datasource = new atlas.source.DataSource();
        map.sources.add(datasource);

        //Add a layer for rendering the route lines and have it render under the map labels.
        map.layers.add(new atlas.layer.LineLayer(datasource, null, {
            strokeColor: '#2272B9',
            strokeWidth: 5,
            lineJoin: 'round',
            lineCap: 'round',
            filter: ['==', '$type', 'LineString']
        }), 'labels');

        //Add a layer for rendering point data.
        map.layers.add(new atlas.layer.SymbolLayer(datasource, null, {
            iconOptions: {
                image: ['get', 'icon'],
                allowOverlap: true
           },
            textOptions: {
                textField: ['get', 'title'],
                offset: [0, 1.2]
            },
            filter: ['==', '$type', 'Point']
        }));
    });
    ```
    
    Un événement de chargement est ajouté à la carte, qui est déclenché lorsque les ressources de la carte ont été entièrement chargées. Dans le gestionnaire d’événements de chargement de la carte, une source de données est créée pour stocker les lignes d’itinéraire ainsi que les points de départ et d’arrivée. Une couche de ligne est créée et jointe à la source de données pour définir le rendu de la ligne d’itinéraire. La ligne de l’itinéraire sera affichée en un beau bleu avec une largeur de 5 pixels et jonction de lignes arrondie et en majuscules. Un filtre est ajouté pour s’assurer que cette couche n’affiche que les données GeoJSON LineString. Lors de l’ajout de la couche sur la carte, un deuxième paramètre avec la valeur `'labels'` est transmis et spécifie le rendu de cette couche sous les étiquettes de carte. Cela garantit que la ligne d’itinéraire ne couvre pas les étiquettes de route. Une couche de symbole est créée et jointe à la source de données. Cette couche spécifie comment les points de départ et d’arrivée sont affichés, dans ce cas des expressions ont été ajoutées pour récupérer les informations d’image d’icône et d’étiquette de texte des propriétés de chaque objet de point. 
    
2. Pour ce tutoriel, définissez Microsoft comme point de départ, et une station-service de Seattle comme point de destination. Dans le gestionnaire d’événements de chargement de la carte, ajoutez le code suivant.

    ```JavaScript
    //Create the GeoJSON objects which represent the start and end point of the route.
    var startPoint = new atlas.data.Feature(new atlas.data.Point([-122.130137, 47.644702]), {
        title: 'Microsoft',
        icon: 'pin-round-blue'
    });

    var endPoint = new atlas.data.Feature(new atlas.data.Point([-122.3352, 47.61397]), {
        title: 'Contoso Oil & Gas',
        icon: 'pin-blue'
    });    
    ```

    Ce code crée deux [objets point GeoJSON](https://en.wikipedia.org/wiki/GeoJSON) pour représenter les points de départ et d’arrivée de l’itinéraire. Une propriété `title` et `icon` est ajoutée à chaque point.
    
3. Ajoutez ensuite le code JavaScript suivant pour ajouter à la carte les marqueurs des points de départ et d’arrivée :

    ```JavaScript
    //Add the data to the data source.
    datasource.add([startPoint, endPoint]);
    
    //Fit the map window to the bounding box defined by the start and end positions.
    map.setCamera({
        bounds: atlas.data.BoundingBox.fromData([startPoint, endPoint]),
        padding: 100
    });
    ```
    
    Les points de départ et d’arrivée sont ajoutés à la source de données. Le rectangle englobant des points de départ et d’arrivée est calculé à l’aide de la fonction `atlas.data.BoundingBox.fromData`. Ce rectangle englobant est utilisé pour définir la vue de caméra de la carte sur le point de départ et d’arrivée à l’aide de la fonction **map.setCamera**. Une marge intérieure est ajoutée pour compenser les dimensions en pixels des icônes de symbole.

3. Enregistrez le fichier **MapRoute.html** et actualisez votre navigateur. Désormais, la carte est centrée sur Seattle, et vous pouvez observer le repère rond bleu marquant le point de départ et l’autre repère bleu marquant le point d’arrivée.

   ![Afficher la carte avec les points de départ et d’arrivée marqués](./media/tutorial-route-location/map-pins.png)

<a id="getroute"></a>

## <a name="get-directions"></a>Obtenir les directions

Cette section montre comment utiliser les API Route Service d’Azure Maps pour rechercher l’itinéraire entre un point de départ donné et une destination. Route Service fournit les API dédiées à la planification des itinéraires les plus *rapides*, *courts*, *économiques*, ou *intéressants* entre deux emplacements. Grâce à la base de données de trafic historique complète d’Azure, il permet également aux utilisateurs de planifier des itinéraires, durées comprises, pour n’importe quels jour et heure. Pour plus d’informations, voir [Get route directions](https://docs.microsoft.com/rest/api/maps/route/getroutedirections) (Obtenir les itinéraires). Toutes les fonctionnalités suivantes doivent être ajoutées **dans l’eventListener du chargement de la carte** pour s’assurer de leur chargement complet une fois la carte chargée.

1. Instanciez le service client en ajoutant le code JavaScript suivant dans le gestionnaire d’événements de chargement de la carte.

    ```JavaScript
    //If the service client hasn't already been created, create an instance.
    if (!client) {
        client = new atlas.service.Client(atlas.getSubscriptionKey());
    }
    ```

2. Ajoutez le bloc de code suivant pour construire une chaîne de requête d’itinéraire.
    ```JavaScript
    //Create the route request with the query being the start and end point in the format 'startLongitude,startLatitude:endLongitude,endLatitude'.
    var routeQuery = startPoint.geometry.coordinates[1] +
        ',' +
        startPoint.geometry.coordinates[0] +
        ':' +
        endPoint.geometry.coordinates[1] +
        ',' +
        endPoint.geometry.coordinates[0];
    ```

3. Pour obtenir l’itinéraire, ajoutez le bloc de code suivant au script. Il interroge le service de routage Azure Maps via la méthode [getRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.models.routedirectionsrequestbody?view=azure-iot-typescript-latest), puis analyse la réponse au format GeoJSON à l’aide de [getGeoJsonRoutes](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.routegeojson?view=azure-iot-typescript-latest). Il ajoute ensuite la ligne de l’itinéraire dans la réponse à la source de données, qui l’affiche automatiquement sur la carte.

    ```JavaScript
    //Execute the car route query then add the route to the map once a response is received.
    client.route.getRouteDirections(routeQuery).then(function (response) {
        // Parse the response into GeoJSON
        var geoJsonResponse = new atlas.service.geojson.GeoJsonRouteDirectionsResponse(response);

        //Add the route line to the data source.
        datasource.add(geoJsonResponse.getGeoJsonRoutes().features[0]);
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

Vous trouverez l’exemple de code de ce didacticiel à cet emplacement :

> [Rechercher un itinéraire avec Azure Maps](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/route.html)

[Consulter cet exemple en direct ici](https://azuremapscodesamples.azurewebsites.net/?sample=Route%20to%20a%20destination)

Le didacticiel suivant vous explique comment créer une requête d’itinéraire avec des restrictions comme le mode de déplacement ou le type de chargement, puis affiche les différents itinéraires sur la même carte.

> [!div class="nextstepaction"]
> [Rechercher des itinéraires pour différents modes de déplacements](./tutorial-prioritized-routes.md)
