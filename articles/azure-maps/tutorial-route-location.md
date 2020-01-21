---
title: 'Tutoriel : Trouver une route vers une position | Microsoft Azure Maps'
description: Ce tutoriel vous montre comment afficher la route vers une position (point d’intérêt) sur une carte à l’aide du service de routage Microsoft Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 7f13e5342e880a9ed5e2cb35ebaf558a1641618b
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/12/2020
ms.locfileid: "75910836"
---
# <a name="tutorial-route-to-a-point-of-interest-using-azure-maps"></a>Tutoriel : Établir un itinéraire vers un point d’intérêt avec Azure Maps

Ce didacticiel montre comment utiliser votre compte Azure Maps et le SDK Route Service pour rechercher l’itinéraire vers votre point d’intérêt. Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Créer une page web à l’aide de l’API Map Control
> * Définir les coordonnées d’une adresse
> * Interroger Route Service afin d’obtenir des indications pour rejoindre un point d’intérêt

## <a name="prerequisites"></a>Conditions préalables requises

Avant de poursuivre, suivez les instructions mentionnées dans [Créer un compte](quick-demo-map-app.md#create-an-account-with-azure-maps) pour créer un abonnement de compte Azure Maps avec le niveau tarifaire S1 et effectuez les étapes indiquées dans [Obtenir la clé primaire](quick-demo-map-app.md#get-the-primary-key-for-your-account) afin d’obtenir la clé primaire pour votre compte. Pour plus d’informations sur l’authentification dans Azure Maps, consultez [Gérer l’authentification dans Azure Maps](how-to-manage-authentication.md).

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

    `atlas.Map` fournit le contrôle d’une carte web visuelle et interactive et est un composant de l’API Azure Map Control.

4. Enregistrez le fichier et ouvrez-le dans votre navigateur. À ce stade, vous disposez d’une carte de base que vous pouvez développer.

   ![Afficher la carte de base](media/tutorial-route-location/basic-map.png)

## <a name="define-how-the-route-will-be-rendered"></a>Définir le rendu de l’itinéraire

Dans ce tutoriel, un itinéraire simple sera affiché à l’aide d’une icône de symbole pour le début et la fin de l’itinéraire et d’une ligne pour le chemin d’accès de l’itinéraire.

1. Une fois la carte initialisée, ajoutez le code JavaScript suivant.

    ```JavaScript
    //Wait until the map resources are ready.
    map.events.add('ready', function() {

        //Create a data source and add it to the map.
        datasource = new atlas.source.DataSource();
        map.sources.add(datasource);

        //Add a layer for rendering the route lines and have it render under the map labels.
        map.layers.add(new atlas.layer.LineLayer(datasource, null, {
            strokeColor: '#2272B9',
            strokeWidth: 5,
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
    
    Dans le gestionnaire d’événements `ready` des cartes, une source de données est créée pour stocker la ligne d’itinéraire ainsi que les points de départ et d’arrivée. Une couche de ligne est créée et jointe à la source de données pour définir le rendu de la ligne d’itinéraire. La ligne de l’itinéraire sera affichée en un beau bleu avec une largeur de 5 pixels et jonction de lignes arrondie et en majuscules. Lors de l’ajout de la couche sur la carte, un deuxième paramètre avec la valeur `'labels'` est transmis et spécifie le rendu de cette couche sous les étiquettes de carte. Cela garantit que la ligne d’itinéraire ne couvre pas les étiquettes de route. Une couche de symbole est créée et jointe à la source de données. Cette couche spécifie comment les points de départ et d’arrivée sont affichés, dans ce cas des expressions ont été ajoutées pour récupérer les informations d’image d’icône et d’étiquette de texte des propriétés de chaque objet de point. 
    
2. Pour ce tutoriel, définissez Microsoft comme point de départ, et une station-service de Seattle comme point de destination. Dans le gestionnaire d’événements `ready` des cartes, ajoutez le code suivant.

    ```JavaScript
    //Create the GeoJSON objects which represent the start and end points of the route.
    var startPoint = new atlas.data.Feature(new atlas.data.Point([-122.130137, 47.644702]), {
        title: "Redmond",
        icon: "pin-blue"
    });

    var endPoint = new atlas.data.Feature(new atlas.data.Point([-122.3352, 47.61397]), {
        title: "Seattle",
        icon: "pin-round-blue"
    });

    //Add the data to the data source.
    datasource.add([startPoint, endPoint]);

    map.setCamera({
        bounds: atlas.data.BoundingBox.fromData([startPoint, endPoint]),
        padding: 80
    });
    ```

    Ce code crée deux [objets point GeoJSON](https://en.wikipedia.org/wiki/GeoJSON) pour représenter les points de départ et d’arrivée de l’itinéraire, et ajoute ceux-ci à la source de données. Une propriété `title` et `icon` est ajoutée à chaque point. Le dernier bloc définit la vue de l’appareil photo sur la base des informations de latitude et longitude des points de départ et d’arrivée, à l’aide de la propriété [setCamera](/javascript/api/azure-maps-control/atlas.map#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) de la carte.

3. Enregistrez le fichier **MapRoute.html** et actualisez votre navigateur. À présent, la carte est centrée sur Seattle, et vous pouvez observer l’épingle bleue marquant le point de départ et l’épingle bleue arrondie marquant le point d’arrivée.

   ![Voir le point de départ et d’arrivée des routes sur la carte](media/tutorial-route-location/map-pins.png)

<a id="getroute"></a>

## <a name="get-directions"></a>Obtenir les directions

Cette section montre comment utiliser les API Route Service d’Azure Maps pour rechercher l’itinéraire entre un point de départ et un point d’arrivée donnés. Route Service fournit les API dédiées à la planification des itinéraires les plus *rapides*, *courts*, *économiques*, ou *intéressants* entre deux emplacements. Grâce à la base de données de trafic historique complète d’Azure, il permet également aux utilisateurs de planifier des itinéraires, durées comprises, pour n’importe quels jour et heure. Pour plus d’informations, voir [Get route directions](https://docs.microsoft.com/rest/api/maps/route/getroutedirections) (Obtenir les itinéraires). Toutes les fonctionnalités suivantes doivent être ajoutées **dans l’EventListener ready de la carte** pour garantir que les ressources de carte sont accessibles.

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

2. Après avoir configuré les informations d’identification et l’URL, ajoutez le code JavaScript suivant pour construire l’itinéraire du point de départ au point d’arrivée. `routeURL` demande au service d’itinéraire Azure Maps de calculer des itinéraires. Une collection de fonctionnalités GeoJSON de la réponse est alors extraite à l’aide de la méthode `geojson.getFeatures()` et ajoutée à la source de données.

    ```JavaScript
    //Start and end point input to the routeURL
    var coordinates= [[startPoint.geometry.coordinates[0], startPoint.geometry.coordinates[1]], [endPoint.geometry.coordinates[0], endPoint.geometry.coordinates[1]]];

    //Make a search route request
    routeURL.calculateRouteDirections(atlas.service.Aborter.timeout(10000), coordinates).then((directions) => {
        //Get data features from response
        var data = directions.geojson.getFeatures();
        datasource.add(data);
    });
    ```

3. Enregistrez le fichier **MapRoute.html** et actualisez votre navigateur web. Dans le cadre d’une connexion efficace avec les API Maps, vous devriez observer une carte similaire au contenu suivant.

    ![Azure Map Control et Route Service](./media/tutorial-route-location/map-route.png)

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à :

> [!div class="checklist"]
> * Créer une page web à l’aide de l’API Map Control
> * Définir les coordonnées d’une adresse
> * Interroger Route Service afin d’obtenir des indications pour rejoindre un point d’intérêt

> [!div class="nextstepaction"]
> [Afficher le code source complet](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/route.html)

> [!div class="nextstepaction"]
> [Afficher un exemple en direct](https://azuremapscodesamples.azurewebsites.net/?sample=Route%20to%20a%20destination)

Le didacticiel suivant vous explique comment créer une requête d’itinéraire avec des restrictions comme le mode de déplacement ou le type de chargement, puis affiche les différents itinéraires sur la même carte.

> [!div class="nextstepaction"]
> [Rechercher des itinéraires pour différents modes de déplacements](./tutorial-prioritized-routes.md)
