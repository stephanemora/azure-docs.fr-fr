---
title: 'Tutoriel : Trouver plusieurs routes en fonction du mode de transport | Microsoft Azure Maps'
description: Dans ce tutoriel, vous allez apprendre à trouver des itinéraires pour différents modes de transport en utilisant Microsoft Azure Maps.
author: farah-alyasari
ms.author: v-faalya
ms.date: 01/14/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 628a3003cec2cc2ca58f1b133cf3236417dfa94e
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/14/2020
ms.locfileid: "77209492"
---
# <a name="tutorial-find-routes-for-different-modes-of-travel-using-azure-maps"></a>Tutoriel : Rechercher des itinéraires pour différents modes de déplacement avec Azure Maps

Ce tutoriel montre comment utiliser votre compte Azure Maps et le service d’itinéraire. Le service d’itinéraire peut trouver l’itinéraire vers le point d’intérêt de votre choix, en hiérarchisant le mode de déplacement, en fonction de votre mode de déplacement. Vous pouvez afficher deux itinéraires différents sur votre carte, un pour les voitures et l’autre pour les camions. Le service d’itinéraire prend en compte les limitations liées à la hauteur et au poids du véhicule, ou au transport de chargements dangereux. Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Créer une page web à l’aide de l’API Map Control
> * Visualiser le flux du trafic sur votre carte
> * Créer des requêtes d’itinéraire déclarant le mode de déplacement
> * Afficher plusieurs itinéraires sur votre carte

## <a name="prerequisites"></a>Prérequis
Avant de continuer, suivez les instructions mentionnées dans [Créer un compte](quick-demo-map-app.md#create-an-account-with-azure-maps), puis sélectionnez le niveau tarifaire S1. Effectuez les étapes décrites dans [Obtenir la clé primaire](quick-demo-map-app.md#get-the-primary-key-for-your-account) pour obtenir la clé primaire de votre compte. Pour plus d’informations sur l’authentification dans Azure Maps, voir [Gérer l’authentification dans Azure Maps](how-to-manage-authentication.md).

## <a name="create-a-new-map"></a>Créer une carte

Les étapes suivantes vous indiquent comment créer une page HTML statique intégrée avec l’API Map Control.

1. Sur votre ordinateur local, créez un fichier et nommez-le **MapTruckRoute.html**.
2. Ajoutez les composants HTML suivants au fichier :

    ```HTML
    <!DOCTYPE html>
    <html>
    <head>
        <title>Map Route</title>
        <meta charset="utf-8">
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

        <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

        <!-- Add a reference to the Azure Maps Services Module JavaScript file. -->
        <script src="https://atlas.microsoft.com/sdk/javascript/service/2/atlas-service.min.js"></script>

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

            #myMap {
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

3. Ajoutez le code JavaScript suivant à la fonction `GetMap`. Remplacez la chaîne `<Your Azure Maps Key>` par la clé primaire copiée à partir de votre compte Maps.

    ```JavaScript
    //Instantiate a map object
    var map = new atlas.Map("myMap", {
        //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
        authOptions: {
            authType: 'subscriptionKey',
            subscriptionKey: '<Your Azure Maps Key>'
        }
    });
    ```

    La classe `atlas.Map` fournit le contrôle d’une carte web visuelle et interactive. Elle est un composant de l’API Azure Map Control.

4. Enregistrez le fichier et ouvrez-le dans votre navigateur. À ce stade, vous disposez d’une carte de base que vous pouvez développer.

   ![Afficher la carte de base](./media/tutorial-prioritized-routes/basic-map.png)

## <a name="visualize-traffic-flow"></a>Visualiser le flux du trafic

1. Ajoutez l’affichage du trafic à la carte. L’événement `ready` des cartes attend que les ressources des cartes soient chargées et prêtes à interagir de manière sécurisée avec lui.

    ```javascript
    map.events.add("ready", function() {
        // Add Traffic Flow to the Map
        map.setTraffic({
            flow: "relative"
        });
    });
    ```

    Dans le gestionnaire d’événements `ready` de la carte, le paramètre de flux de trafic sur la carte est défini sur `relative`, c’est-à-dire la vitesse de la route correspondant à un trafic fluide. Vous pouvez également le définir sur la vitesse `absolute` de la route, ou sur `relative-delay` qui affiche la vitesse relative lorsqu’elle diffère du trafic fluide.

2. Enregistrez le fichier **MapTruckRoute.html** et actualisez la page dans votre navigateur. Si vous interagissez avec la carte et que vous faites un zoom sur Los Angeles, vous devriez voir les rues avec les données de trafic actuelles.

   ![Voir le trafic sur une carte](./media/tutorial-prioritized-routes/traffic-map.png)

<a id="queryroutes"></a>

## <a name="define-how-the-route-will-be-rendered"></a>Définir le rendu de l’itinéraire

Dans ce didacticiel, deux itinéraires sont calculés et affichés sur la carte. Un itinéraire utilise des routes accessibles aux voitures et l’autre des routes accessibles aux camions. Lors du rendu, nous affichons une icône de symbole pour le départ et l’arrivée de l’itinéraire, et des lignes de couleurs différentes pour chaque tracé d’itinéraire.

1. Une fois la carte initialisée, ajoutez le code JavaScript suivant au gestionnaire d’événements `ready` des cartes.

    ```JavaScript
    //Wait until the map resources have fully loaded.
    map.events.add('ready', function () {

        //Create a data source and add it to the map.
        datasource = new atlas.source.DataSource();
        map.sources.add(datasource);

        //Add a layer for rendering the route lines and have it render under the map labels.
        map.layers.add(new atlas.layer.LineLayer(datasource, null, {
            strokeColor: ['get', 'strokeColor'],
            strokeWidth: ['get', 'strokeWidth'],
            lineJoin: 'round',
            lineCap: 'round'
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
            filter: ['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']] //Only render Point or MultiPoints in this layer.
        }));
    });
    ```
    
    Dans le gestionnaire d’événements `ready` des cartes, une source de données est créée pour stocker les tracés d’itinéraire, ainsi que les points de départ et d’arrivée. Une couche de ligne est créée et jointe à la source de données pour définir le rendu de la ligne d’itinéraire. Des expressions sont utilisées pour récupérer la largeur et la couleur de ligne des propriétés de la fonctionnalité de ligne d’itinéraire. Lors de l’ajout de la couche sur la carte, un deuxième paramètre avec la valeur `'labels'` est transmis et spécifie le rendu de cette couche sous les étiquettes de carte. Cela garantit que la ligne d’itinéraire ne couvre pas les étiquettes de route. Une couche de symbole est créée et jointe à la source de données. Ce calque spécifie la manière dont les points de départ et d’arrivée sont affichés. Dans le cas présent, des expressions ont été ajoutées pour récupérer les informations d’image d’icône et d’étiquette de texte des propriétés de chaque objet de point. 
    
2. Pour ce didacticiel, définissez le point de départ sur une entreprise fictive de Seattle appelée Fabrikam et le point d’arrivée sur un siège Microsoft. Dans le gestionnaire d’événements `ready` des cartes, ajoutez le code suivant.

    ```JavaScript
    //Create the GeoJSON objects which represent the start and end point of the route.
    var startPoint = new atlas.data.Feature(new atlas.data.Point([-122.356099, 47.580045]), {
        title: 'Fabrikam, Inc.',
        icon: 'pin-blue'
    });

    var endPoint = new atlas.data.Feature(new atlas.data.Point([-122.201164, 47.616940]), {
        title: 'Microsoft - Lincoln Square',
        icon: 'pin-round-blue'
    });
    ```

    Ce code crée deux [objets GeoJSON](https://en.wikipedia.org/wiki/GeoJSON) pour représenter les points de départ et d’arrivée de l’itinéraire. Une propriété `title` et `icon` est ajoutée à chaque point.

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

    Les points de départ et d’arrivée sont ajoutés à la source de données. Le rectangle englobant des points de départ et d’arrivée est calculé à l’aide de la fonction `atlas.data.BoundingBox.fromData`. Ce rectangle englobant est utilisé pour définir la vue de caméra de la carte sur l’itinéraire entier à l’aide de la fonction `map.setCamera`. Une marge intérieure est ajoutée pour compenser les dimensions en pixels des icônes de symbole.

4. Enregistrez le fichier et actualisez votre navigateur afin d’afficher les repères sur votre carte. À présent, la carte est centrée sur Seattle. Vous pouvez observer le repère rond bleu marquant le point de départ et l’autre repère bleu marquant le point d’arrivée.

   ![Afficher la carte avec les points de départ et d’arrivée](./media/tutorial-prioritized-routes/pins-map.png)

<a id="multipleroutes"></a>

## <a name="render-routes-prioritized-by-mode-of-travel"></a>Afficher les itinéraires priorisés par mode de déplacement

Cette section montre comment utiliser l’API de service d’itinéraire Maps. L’API d’itinéraire permet de rechercher plusieurs itinéraires entre un point de départ et une destination donnés, en fonction de votre mode de transport. Le service d’itinéraire fournit les API dédiées à la planification des itinéraires les plus *rapides*, *courts*, *économiques*, ou *intéressants*. Non seulement les API planifient des itinéraires entre deux localisations, mais elles considèrent également les conditions de trafic actuelles. 

Avec la base de données de trafic historique complète d’Azure, L’API d’itinéraire permet également aux utilisateurs de planifier des itinéraires. L’API peut prédire les durées d’itinéraire pour un jour et une heure donnés. Pour plus d’informations, consultez [Get route directions](https://docs.microsoft.com/rest/api/maps/route/getroutedirections) (Obtenir les itinéraires). 

Tous les blocs de code suivants doivent être ajoutés **dans l’eventListener du chargement de la carte** pour assurer leur chargement une fois la carte complètement chargée.

1. Dans la fonction GetMap, ajoutez le code JavaScript suivant.

    ```JavaScript
    // Use SubscriptionKeyCredential with a subscription key
    var subscriptionKeyCredential = new atlas.service.SubscriptionKeyCredential(atlas.getSubscriptionKey());

    // Use subscriptionKeyCredential to create a pipeline
    var pipeline = atlas.service.MapsURL.newPipeline(subscriptionKeyCredential);

    // Construct the RouteURL object
    var routeURL = new atlas.service.RouteURL(pipeline);
    ```

   `SubscriptionKeyCredential` crée un `SubscriptionKeyCredentialPolicy` pour authentifier les requêtes HTTP auprès d’Azure Maps avec la clé d’abonnement. `atlas.service.MapsURL.newPipeline()` utilise la stratégie `SubscriptionKeyCredential` et crée une instance de [pipeline](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-maps-typescript-latest). `routeURL` représente une URL des opérations sur l’[itinéraire](https://docs.microsoft.com/rest/api/maps/route) d’Azure Maps.

2. Après avoir configuré les informations d’identification et l’URL, ajoutez le code JavaScript suivant pour construire un itinéraire du point de départ au point d’arrivée pour un camion transportant USHazmatClass2 classé cargo et afficher les résultats.

    ```JavaScript
    //Start and end point input to the routeURL
    var coordinates= [[startPoint.geometry.coordinates[0], startPoint.geometry.coordinates[1]], [endPoint.geometry.coordinates[0], endPoint.geometry.coordinates[1]]];

    //Make a search route request for a truck vehicle type
    routeURL.calculateRouteDirections(atlas.service.Aborter.timeout(10000), coordinates,{
        travelMode: 'truck',
        vehicleWidth: 2,
        vehicleHeight: 2,
        vehicleLength: 5,
        vehicleLoadType: 'USHazmatClass2'
    }).then((directions) => {
        //Get data features from response
        var data = directions.geojson.getFeatures();

        //Get the route line and add some style properties to it.  
        var routeLine = data.features[0];
        routeLine.properties.strokeColor = '#2272B9';
        routeLine.properties.strokeWidth = 9;

        //Add the route line to the data source. We want this to render below the car route which will likely be added to the data source faster, so insert it at index 0.
        datasource.add(routeLine, 0);
    });
    ```

    L’extrait de code ci-dessus interroge le service d’itinéraire d’Azure Maps via la méthode [getRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.models.routedirectionsrequestbody?view=azure-maps-typescript-latest). La ligne d’itinéraire est ensuite extraite de la collection de fonctionnalités GeoJSON à partir de la réponse extraite à l’aide de la méthode `geojson.getFeatures()`. La ligne d’itinéraire est ensuite ajoutée à la source de données. Un index de 0 garantit que la ligne est affichée avant toutes les autres lignes dans la source de données. Cela est fait car le calcul d’un itinéraire de camion sera souvent plus lent que le calcul d’un itinéraire de voiture. Si la ligne d’itinéraire de camion est ajoutée à la source de données après l’itinéraire de voiture, elle s’affiche au-dessus d’elle. Deux propriétés sont ajoutées à la ligne d’itinéraire réservé aux camions : un trait de couleur bleue et une épaisseur de trait de neuf pixels.

3. Ajoutez le code JavaScript suivant pour construire un itinéraire pour une voiture et afficher les résultats.

    ```JavaScript
    routeURL.calculateRouteDirections(atlas.service.Aborter.timeout(10000), coordinates).then((directions) => {

        //Get data features from response
        var data = directions.geojson.getFeatures();

        //Get the route line and add some style properties to it.  
        var routeLine = data.features[0];
        routeLine.properties.strokeColor = '#B76DAB';
        routeLine.properties.strokeWidth = 5;

        //Add the route line to the data source. We want this to render below the car route which will likely be added to the data source faster, so insert it at index 0.  
        datasource.add(routeLine);
    });
    ```

    L’extrait de code ci-dessus interroge le service d’itinéraire d’Azure Maps via la méthode [getRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.models.routedirectionsrequestbody?view=azure-maps-typescript-latest). La ligne d’itinéraire est ensuite extraite de la collection de fonctionnalités GeoJSON à partir de la réponse extraite à l’aide de la méthode `geojson.getFeatures()`. La ligne d’itinéraire est ensuite ajoutée à la source de données. Deux propriétés sont ajoutées à la ligne d’itinéraire réservé aux voitures : un trait de couleur violette et une épaisseur de trait de cinq pixels.  

4. Enregistrez le fichier **MapTruckRoute.html** et actualisez votre navigateur afin d’afficher les résultats. Dans le cadre d’une connexion efficace avec les API Maps, vous devriez observer une carte similaire au contenu suivant.

    ![Itinéraires priorisés avec Azure Route Service](./media/tutorial-prioritized-routes/prioritized-routes.png)

    L’itinéraire des camions est bleu, tandis que l’itinéraire des voitures est violet et fin. L’itinéraire réservé aux voitures passe au-dessus du Lac Washington via l’I-90, qui traverse des tunnels installés sous des zones résidentielles. Les tunnels étant proches de zones résidentielles, tout transport de déchets dangereux est interdit. L’itinéraire des camions, pour lequel est défini le type de chargement USHazmatClass2, utilise une voie de circulation différente.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à :

> [!div class="checklist"]
> * Créer une page web à l’aide de l’API Map Control
> * Visualiser le flux du trafic sur votre carte
> * Créer des requêtes d’itinéraire déclarant le mode de déplacement
> * Afficher plusieurs itinéraires sur votre carte

> [!div class="nextstepaction"]
> [Afficher le code source complet](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/truckRoute.html)

> [!div class="nextstepaction"]
> [Afficher un exemple en direct](https://azuremapscodesamples.azurewebsites.net/?sample=Multiple%20routes%20by%20mode%20of%20travel)

Le tutoriel suivant montre comment créer un localisateur de magasin simple à l’aide d’Azure Maps.

> [!div class="nextstepaction"]
> [Créer un localisateur de magasin à l’aide d’Azure Maps](./tutorial-create-store-locator.md)

> [!div class="nextstepaction"]
> [Utiliser des expressions de style basées sur les données](data-driven-style-expressions-web-sdk.md)