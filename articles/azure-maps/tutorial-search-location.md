---
title: 'Tutoriel : Rechercher avec Azure Maps'
description: 'Tutoriel : Rechercher un point d’intérêt de proximité à l’aide d’Azure Maps'
author: walsehgal
ms.author: v-musehg
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: ae2d7b3d855f1c06397d4eb0f977dd04aa841942
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75432744"
---
# <a name="tutorial-search-nearby-points-of-interest-using-azure-maps"></a>Tutoriel : Rechercher des points d’intérêt de proximité à l’aide d’Azure Maps

Ce didacticiel montre comment configurer un compte avec Azure Maps, puis utiliser les API Maps pour rechercher un point d’intérêt. Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Créer un compte Azure Maps
> * Récupérer la clé primaire de votre compte Maps
> * Créer une page web à l’aide de l’API Map Control
> * Utiliser le service Azure Maps Search pour rechercher un point d’intérêt de proximité

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Connectez-vous au [portail Azure](https://portal.azure.com).

<a id="createaccount"></a>

## <a name="create-an-account-with-azure-maps"></a>Créer un compte avec Azure Maps

Créez un compte Maps en suivant la procédure suivante :

1. En haut à gauche du [portail Azure](https://portal.azure.com), cliquez sur **Créer une ressource**.
2. Dans la zone *Rechercher sur la Place de marché*, tapez **Maps**.
3. Dans les *Résultats*, sélectionnez **Maps**. Cliquez sur le bouton **Créer** qui s’affiche sous la carte.
4. Sur la page **Créer un compte Maps**, entrez les valeurs suivantes :
    * *L’Abonnement* à utiliser pour ce compte.
    * Le *Groupe de ressources* pour ce compte. Vous pouvez choisir de *Créer* ou d’utiliser un groupe de ressources *Existant*.
    * Le *Nom* de votre nouveau compte.
    * Le *niveau tarifaire* pour ce compte.
    * Lisez la *Licence* et la *Déclaration de confidentialité*, puis cochez la case pour accepter les conditions.
    * Cliquez sur le bouton **Créer**.

![Créer un compte Maps sur le Portail](./media/tutorial-search-location/create-account.png)

<a id="getkey"></a>

## <a name="get-the-primary-key-for-your-account"></a>Obtenir la clé primaire de votre compte

Une fois le compte Maps créé, récupérez la clé qui vous permet d’interroger les API Maps. Nous vous recommandons d’utiliser la clé primaire de votre compte comme clé d’abonnement lors de l’appel de services Azure Maps.

1. Ouvrez votre compte Maps dans le portail.
2. Dans la section des paramètres, sélectionnez **Authentification**.
3. Copiez la **Clé primaire** dans le Presse-papiers. Enregistrez-la localement, vous l’utiliserez plus tard dans ce didacticiel.

![Obtenir la clé primaire dans le portail](./media/tutorial-search-location/get-key.png)

Pour plus d’informations sur l’authentification dans Azure Maps, consultez [Gérer l’authentification dans Azure Maps](how-to-manage-authentication.md).

<a id="createmap"></a>

## <a name="create-a-new-map"></a>Créer une carte

L’API Map Control est une bibliothèque cliente pratique qui vous permet d’intégrer facilement Maps à votre application web. Elle masque la complexité des appels de service REST bruts et améliore votre productivité grâce à des composants personnalisables et dont vous pouvez définir le style. Les étapes suivantes vous indiquent comment créer une page HTML statique intégrée avec l’API Map Control.

1. Sur votre ordinateur local, créez un fichier et nommez-le **MapSearch.html**.
2. Ajoutez les composants HTML suivants au fichier :

   ```HTML
    <!DOCTYPE html>
    <html>
    <head>
        <title>Map Search</title>
        <meta charset="utf-8">
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

        <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

        <!-- Add a reference to the Azure Maps Services Module JavaScript file. -->
        <script src="https://atlas.microsoft.com/sdk/javascript/service/2/atlas-service.min.js"></script>

        <script>
        function GetMap(){
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

   Notez que l’en-tête HTML inclut les fichiers de ressources CSS et JavaScript hébergés par la bibliothèque Azure Map Control. Notez l’événement `onload` sur le corps de la page, qui appelle la fonction `GetMap` lorsque le corps de la page est chargé. La fonction `GetMap` est destinée à contenir le code JavaScript inline pour accéder aux API Azure Maps.

3. Ajoutez le code JavaScript suivant à la fonction `GetMap` du fichier HTML. Remplacez la chaîne `<Your Azure Maps Key>` par la clé primaire copiée à partir de votre compte Maps.

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

   Ce segment lance l’API Map Control pour votre clé de compte Azure Maps. `atlas` est l’espace de noms qui contient l’API et les composants visuels associés. `atlas.Map` fournit le contrôle d’une carte web visuelle et interactive.

4. Enregistrez vos modifications du fichier, puis ouvrez la page HTML dans un navigateur. Il s’agit de la carte la plus basique que vous pouvez créer en appelant `atlas.Map` à l’aide de votre clé de compte.

   ![Afficher la carte](./media/tutorial-search-location/basic-map.png)

5. Dans la fonction `GetMap`, après l’initialisation de la carte, ajoutez le code JavaScript suivant.

    ```JavaScript
    //Wait until the map resources are ready.
    map.events.add('ready', function() {

        //Create a data source and add it to the map.
        datasource = new atlas.source.DataSource();
        map.sources.add(datasource);

        //Add a layer for rendering point data.
        var resultLayer = new atlas.layer.SymbolLayer(datasource, null, {
            iconOptions: {
                image: 'pin-round-darkblue',
                anchor: 'center',
                allowOverlap: true
            },
            textOptions: {
                anchor: "top"
            }
        });

        map.layers.add(resultLayer);
    });
    ```

   Dans ce segment de code, un événement `ready` est ajouté à la carte. Il est déclenché une fois que les ressources de la carte ont été chargées et que la carte est accessible. Dans le gestionnaire d’événements `ready` de la carte, une source de données est créée pour stocker des données de résultat. Une couche de symbole est créée et jointe à la source de données. Cette couche spécifie comment les données de résultat dans la source de données doivent être restituées, dans ce cas avec une icône d’épingle ronde bleue foncée centrée sur les coordonnées des résultats et qui autorise le chevauchement d’autres icônes. La couche des résultats est ajoutée aux couches de la carte.

<a id="usesearch"></a>

## <a name="add-search-capabilities"></a>Ajouter les fonctionnalités de recherche

Cette section montre comment utiliser l’[API de recherche](https://docs.microsoft.com/rest/api/maps/search) de Maps pour rechercher un point d’intérêt sur votre carte. Il s’agit d’une API RESTful destinée aux développeurs souhaitant mettre en place des fonctionnalités de recherche d’adresses, de points d’intérêt et d’autres informations d’ordre géographique. Search Service affecte une latitude et une longitude à une adresse spécifiée. Le **module de service** expliqué ci-dessous peut être utilisé pour rechercher un emplacement à l’aide de l’API Maps Search.

### <a name="service-module"></a>Module de service

1. Dans le gestionnaire d’événements `ready` de la carte, construisez l’URL du service de recherche en ajoutant le code JavaScript suivant.

    ```JavaScript
   // Use SubscriptionKeyCredential with a subscription key
   var subscriptionKeyCredential = new atlas.service.SubscriptionKeyCredential(atlas.getSubscriptionKey());

   // Use subscriptionKeyCredential to create a pipeline
   var pipeline = atlas.service.MapsURL.newPipeline(subscriptionKeyCredential);

   // Construct the SearchURL object
   var searchURL = new atlas.service.SearchURL(pipeline); 
   ```

   `SubscriptionKeyCredential` crée un `SubscriptionKeyCredentialPolicy` pour authentifier les requêtes HTTP auprès d’Azure Maps avec la clé d’abonnement. `atlas.service.MapsURL.newPipeline()` utilise la stratégie `SubscriptionKeyCredential` et crée une instance de [pipeline](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-maps-typescript-latest). `searchURL` représente une URL vers les opérations de [recherche](https://docs.microsoft.com/rest/api/maps/search) d’Azure Maps.

2. Ajoutez ensuite le bloc de script suivant pour générer la requête de recherche. Il utilise le service Fuzzy Search, qui est l’API de recherche de base de Search Service. Le service Fuzzy Search gère la plupart des entrées partielles comme des adresses, des lieux et des points d’intérêt (POI). Ce code recherche les stations-service situées à proximité dans le rayon spécifié par la latitude et la longitude fournies. Une collection de fonctionnalités GeoJSON déterminée par la réponse est ensuite extraite à l’aide de la méthode `geojson.getFeatures()` et ajoutée à la source de données. Les données sont alors automatiquement restituées sur la carte par le biais de la couche de symboles. La dernière partie du script définit la vue de caméra de la carte en utilisant le rectangle englobant des résultats à l’aide de la propriété [setCamera](/javascript/api/azure-maps-control/atlas.map#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) de la carte.

    ```JavaScript
    var query =  'gasoline-station';
    var radius = 9000;
    var lat = 47.64452336193245;
    var lon = -122.13687658309935;

    searchURL.searchPOI(atlas.service.Aborter.timeout(10000), query, {
        limit: 10,
        lat: lat,
        lon: lon,
        radius: radius
    }).then((results) => {

        // Extract GeoJSON feature collection from the response and add it to the datasource
        var data = results.geojson.getFeatures();
        datasource.add(data);

        // set camera to bounds to show the results
        map.setCamera({
            bounds: data.bbox,
            zoom: 10
        });
    });
    ```

3. Enregistrez le fichier **MapSearch.html**, puis actualisez votre navigateur. Vous devriez maintenant voir que la carte est centrée sur Seattle avec des repères ronds de couleur bleu marquant l’emplacement des stations-service dans la zone.

   ![Afficher la carte avec les résultats de la recherche](./media/tutorial-search-location/pins-map.png)

4. Vous pouvez voir les données brutes rendues par la carte en entrant la requête HTTP suivante dans votre navigateur. Remplacez \<Your Azure Maps Key\> (Votre clé Azure Maps) par votre clé primaire.

   ```http
   https://atlas.microsoft.com/search/poi/json?api-version=1.0&query=gasoline%20station&subscription-key=<subscription-key>&lat=47.6292&lon=-122.2337&radius=100000
   ```

À ce stade, la page MapSearch peut afficher les emplacements des points d’intérêt renvoyés d’une requête de recherche approximative. Ajoutons quelques fonctionnalités interactives et des informations supplémentaires sur les emplacements.

## <a name="add-interactive-data"></a>Ajouter des données interactives

La carte développée jusqu’à présent produit des résultats de recherche comportant uniquement les données de longitude/latitude. Si vous analysez le fichier brut JSON renvoyé par le service Maps Search, cependant, vous constatez qu’il contient des informations supplémentaires sur chaque station service, notamment le nom et l’adresse. Vous pouvez intégrer ces données dans la carte à l’aide de zones contextuelles interactives.

1. Ajoutez les lignes de code suivantes dans le gestionnaire d’événements `ready` de la carte après le code pour interroger le service de recherche approximative. Une instance de fenêtre contextuelle est créée et un événement mouseover est ajouté dans la couche de symbole.

    ```JavaScript
   //Create a popup but leave it closed so we can update it and display it later.
    popup = new atlas.Popup();

    //Add a mouse over event to the result layer and display a popup when this event fires.
    map.events.add('mouseover', resultLayer, showPopup);
    ```

    L’API `*atlas.Popup` fournit une fenêtre d’informations ancrée à la position exigée sur la carte. 

2. Ajoutez le code suivant dans la fonction `GetMap` pour afficher les informations de résultat de l’événement mouseover dans la fenêtre contextuelle.

    ```JavaScript
    function showPopup(e) {
        //Get the properties and coordinates of the first shape that the event occured on.

        var p = e.shapes[0].getProperties();
        var position = e.shapes[0].getCoordinates();

        //Create HTML from properties of the selected result.
        var html = `
          <div style="padding:5px">
            <div><b>${p.poi.name}</b></div>
            <div>${p.address.freeformAddress}</div>
            <div>${position[1]}, ${position[0]}</div>
          </div>`;

        //Update the content and position of the popup.
        popup.setPopupOptions({
            content: html,
            position: position
        });

        //Open the popup.
        popup.open(map);
    }
    ```

3. Enregistrez le fichier et actualisez votre navigateur. Désormais, la carte dans le navigateur affiche des fenêtres contextuelles de données lorsque vous survolez les repères de recherche.

    ![Azure Map Control et Search Service](./media/tutorial-search-location/popup-map.png)

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à :

> [!div class="checklist"]
> * Créer un compte avec Azure Maps
> * Obtenir la clé primaire de votre compte
> * Créer une page web à l’aide de l’API Map Control
> * Utiliser Search Service pour rechercher des points d’intérêt de proximité

> [!div class="nextstepaction"]
> [Afficher le code source complet](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/search.html)

> [!div class="nextstepaction"]
> [Afficher un exemple en direct](https://azuremapscodesamples.azurewebsites.net/?sample=Search%20for%20points%20of%20interest)

Le prochain didacticiel vous explique comment afficher un itinéraire entre deux emplacements.

> [!div class="nextstepaction"]
> [Itinéraire vers une destination](./tutorial-route-location.md)
