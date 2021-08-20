---
title: 'Tutoriel : Utiliser Microsoft Azure Maps pour créer des applications web de localisation de magasins'
description: Tutoriel sur la façon d’utiliser Microsoft Azure Maps pour créer des applications web de localisation de magasins.
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/07/2021
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc, devx-track-js
ms.openlocfilehash: 0830bb35befbd2c841eb10c4e94a8ef64f2bf019
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111757378"
---
# <a name="tutorial-use-azure-maps-to-create-a-store-locator"></a>Tutoriel : Utiliser Azure Maps pour créer un localisateur de magasins

Ce tutoriel vous guide tout au long du processus de création d’un localisateur simple de magasins à l’aide d’Azure Maps. Ce didacticiel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer une page web à l’aide de l’API Azure Map Control.
> * Charger des données personnalisées à partir d’un fichier et les afficher sur une carte.
> * Utiliser le service Azure Maps Search pour rechercher une adresse ou entrer une requête.
> * Obtenir l’emplacement de l’utilisateur à partir du navigateur et l’afficher sur la carte.
> * Combiner plusieurs couches pour créer des symboles personnalisés sur la carte.  
> * Regrouper des points de données.  
> * Ajouter des contrôles de zoom à la carte.

<a id="Intro"></a>

## <a name="prerequisites"></a>Prérequis

1. [Créer un compte Azure Maps dans le niveau tarifaire Gen1 (S1) ou Gen2](quick-demo-map-app.md#create-an-azure-maps-account).
2. [Obtenir une clé d’abonnement principale](quick-demo-map-app.md#get-the-primary-key-for-your-account), également appelée clé primaire ou clé d’abonnement.

Pour plus d’informations sur l’authentification Azure Maps, consultez [Gérer l’authentification dans Azure Maps](how-to-manage-authentication.md).

Ce tutoriel utilise l’application [Visual Studio Code](https://code.visualstudio.com/), mais vous pouvez utiliser un autre environnement de codage.

## <a name="sample-code"></a>Exemple de code

Dans ce tutoriel, nous allons créer un localisateur de magasins (ici des cafés) pour une société fictive nommée Contoso Coffee. Le tutoriel comprend également quelques conseils pour vous aider à étendre le localisateur de magasins avec d’autres fonctionnalités facultatives.

Vous pouvez consulter l’[exemple de localisateur de magasins Live ici](https://azuremapscodesamples.azurewebsites.net/?sample=Simple%20Store%20Locator).

Pour suivre et utiliser plus facilement ce tutoriel, vous devez télécharger les ressources suivantes :

* [Le code source complet de l’exemple de localisateur simple de magasins](https://github.com/Azure-Samples/AzureMapsCodeSamples/tree/master/AzureMapsCodeSamples/Tutorials/Simple%20Store%20Locator)
* [Les données de localisation des magasins à importer dans le jeu de données du localisateur de magasins](https://github.com/Azure-Samples/AzureMapsCodeSamples/tree/master/AzureMapsCodeSamples/Tutorials/Simple%20Store%20Locator/data)
* [Images de carte](https://github.com/Azure-Samples/AzureMapsCodeSamples/tree/master/AzureMapsCodeSamples/Tutorials/Simple%20Store%20Locator/images)

## <a name="store-locator-features"></a>Fonctionnalités du localisateur de magasins

Cette section répertorie les fonctionnalités prises en charge par l’application de localisation de cafés Contoso Coffee.

### <a name="user-interface-features"></a>Fonctionnalités de l’interface utilisateur

* Logo du magasin sur l’en-tête
* La carte prend en charge les panoramiques et les zooms
* Un bouton Ma position pour effectuer une recherche sur la position actuelle de l’utilisateur
* La mise en page s’adapte à la largeur de l’écran de l’appareil
* Une zone de recherche et un bouton de recherche

### <a name="functionality-features"></a>Caractéristiques des fonctionnalités

* Un événement `keypress` ajouté à la zone de recherche déclenche une recherche lorsque l’utilisateur appuie sur **Entrée**.
* La distance entre le centre de la carte et chaque emplacement est calculée à chaque déplacement de la carte. La liste des résultats est mise à jour pour afficher les lieux les plus proches en haut de la carte.  
* Lorsque l’utilisateur sélectionne un résultat dans la liste des résultats, la carte est centrée sur le lieu sélectionné et des informations sur ce lieu apparaissent dans une fenêtre contextuelle.  
* Lorsque l’utilisateur sélectionne un lieu spécifique, la carte déclenche une fenêtre contextuelle.
* Quand l’utilisateur effectue un zoom arrière, les emplacements sont regroupés ensemble. Chaque groupe est représenté par un cercle avec un numéro à l’intérieur du cercle. Les groupes se font et se défont à mesure que l’utilisateur change de facteur de zoom.
* En sélectionnant un groupe, l’utilisateur effectue un zoom avant de deux niveaux sur la carte et se centre sur l’emplacement du groupe.

## <a name="store-locator-design"></a>Conception du localisateur de magasins

L’illustration suivante correspond à la maquette de la disposition générale de notre localisateur de magasins. Vous pouvez visualiser la maquette en direct [ici](https://azuremapscodesamples.azurewebsites.net/?sample=Simple%20Store%20Locator).

:::image type="content" source="./media/tutorial-create-store-locator/store-locator-wireframe.png" alt-text="Maquette de l’application de localisation de cafés Contoso Coffee.":::

Pour optimiser l’utilité de ce localisateur de magasin, nous allons inclure une disposition réactive qui s’adapte à la taille de l’écran de l’utilisateur quand sa largeur est inférieure à 700 pixels. La disposition réactive facilite l’utilisation du localisateur de magasin sur un petit écran, comme celui d’un appareil mobile. Voici une maquette de la disposition pour petit écran :  

:::image type="content" source="./media/tutorial-create-store-locator/store-locator-wireframe-mobile.png" alt-text="Maquette de l’application de localisation de cafés Contoso Coffee sur un appareil mobile.":::

<a id="create a data-set"></a>

## <a name="create-the-store-location-dataset"></a>Créer le jeu de données d’emplacements de magasins

Cette section décrit comment créer un jeu de données des magasins que vous souhaitez afficher sur la carte. Le jeu de données pour le localisateur des cafés Contoso Coffee est créé dans un classeur Excel. Le jeu de données contient 10 213 localisations de cafés Contoso Coffee répartis dans neuf pays ou régions : États-Unis, Canada, Royaume-Uni, France, Allemagne, Italie, Pays-Bas, Danemark et Espagne. Voici une capture d’écran qui montre à quoi ressemblent les données :

:::image type="content" source="./media/tutorial-create-store-locator/store-locator-data-spreadsheet.png" alt-text="Capture d’écran des données du localisateur de magasins dans un classeur Excel.":::

Pour visualiser le jeu de données complet, [téléchargez le classeur Excel ici](https://github.com/Azure-Samples/AzureMapsCodeSamples/tree/master/AzureMapsCodeSamples/Tutorials/Simple%20Store%20Locator/data).

À l’examen de la capture d’écran des données, voici les observations que nous pouvons faire :

* Les informations sur les emplacements sont stockées dans les colonnes **AddressLine** (adresse), **City** (ville), **Municipality** (chef-lieu), **AdminDivision** (État/province), **PostCode** (code postal) et **Country** (pays).  
* Les colonnes **Latitude** et **Longitude** contiennent les coordonnées géographiques de chaque localisation de Contoso Coffee. Si vous ne disposez pas des coordonnées, vous pouvez utiliser les services de recherche d’Azure Maps pour déterminer les coordonnées d’emplacement.
* D’autres colonnes contiennent des métadonnées sur les cafés : un numéro de téléphone, des colonnes booléennes, ainsi que les heures d’ouverture et de fermeture des magasins au format 24 heures. Les colonnes booléennes sont destinées au Wi-Fi et à l’accessibilité pour les personnes en fauteuil roulant. Vous pouvez créer vos propres colonnes contenant des métadonnées plus en rapport avec vos données d’emplacement.

> [!NOTE]
> Azure Maps affiche les données dans la projection Mercator sphérique « EPSG:3857 », mais lit les données dans « EPSG:4326 » qui utilisent la donnée WGS84.

## <a name="load-the-store-location-dataset"></a>Charger le jeu de données de localisation de magasins

 Le jeu de données de localisation des cafés Contoso Coffee est petit. Nous allons donc convertir la feuille de calcul Excel en un fichier texte délimité par des tabulations. Ce fichier peut ensuite être téléchargé par le navigateur lors du chargement de l’application.

 >[!TIP]
>Si votre jeu de données est trop volumineux pour être téléchargé par le client, ou s’il est fréquemment mis à jour, vous pouvez envisager de le stocker dans une base de données. Une fois vos données chargées dans une base de données, vous pouvez configurer un service web qui accepte les requêtes pour les données, puis envoie les résultats au navigateur de l’utilisateur.

### <a name="convert-data-to-tab-delimited-text-file"></a>Convertir les données en fichier texte délimité par des tabulations

Pour convertir les données relatives à la localisation des cafés Contoso Coffee d’un classeur Excel en un fichier texte plat :

1. [Téléchargez le classeur Excel](https://github.com/Azure-Samples/AzureMapsCodeSamples/tree/master/AzureMapsCodeSamples/Tutorials/Simple%20Store%20Locator/data).

2. Enregistrez le classeur sur votre disque dur.

3. Chargez l’application Excel.

4. Ouvrez le classeur téléchargé.

5. Sélectionnez **Enregistrer sous**.

6. Dans la liste déroulante **Type de fichier**, sélectionnez **Texte (délimité par des tabulations)(*.txt)** . 

7. Nommez le fichier *ContosoCoffee*.

:::image type="content" source="./media/tutorial-create-store-locator/data-delimited-text.png" alt-text="Capture d’écran de la boîte de dialogue Type de fichier.":::

Si vous ouvrez le fichier texte dans Bloc-notes, il se présente comme suit :

:::image type="content" source="./media/tutorial-create-store-locator/data-delimited-file.png" alt-text="Capture d’écran d’un fichier Bloc-notes qui montre un jeu de données délimité par des tabulations.":::

## <a name="set-up-the-project"></a>Configuration du projet

1. Ouvrez l’application Visual Studio Code.

2. Sélectionnez **Fichier**, puis **Ouvrir un espace de travail…**

3. Créez un nouveau dossier et nommez-le « ContosoCoffee ».

4. Sélectionnez **CONTOSOCOFFEE** dans l’explorateur.

5. Créez les trois fichiers suivants qui définissent la disposition, le style et la logique de l’application :

    * *index.html*
    * *index.css*
    * *index.js*

6. Créez un dossier intitulé *data*.

7. Ajoutez *ContosoCoffee.txt* au dossier *data*.

8. Créez un autre dossier nommé *images*.

9. Si vous ne l’avez pas déjà fait, [téléchargez ces 10 images](https://github.com/Azure-Samples/AzureMapsCodeSamples/tree/master/AzureMapsCodeSamples/Tutorials/Simple%20Store%20Locator/images).

10. Ajoutez les images téléchargées au *images*.

    Le dossier de votre espace de travail doit maintenant ressembler à la capture d’écran suivante :

    :::image type="content" source="./media/tutorial-create-store-locator/store-locator-workspace.png" alt-text="Capture d’écran du dossier d’espace de travail du localisateur simple de magasins.":::

## <a name="create-the-html"></a>Créer le HTML

Pour créer le HTML :

1. Ajoutez les balises `meta` suivantes à la section `head` du fichier *index.html* :

    ```HTML
    <meta charset="utf-8">
    <meta http-equiv="x-ua-compatible" content="IE=Edge">
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
    ```

2. Ajoutez des références aux fichiers CSS et JavaScript de contrôle web Azure Maps :

    ```HTML
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>
    ```

3. Ajoutez une référence au module Azure Maps Services. Le module est une bibliothèque JavaScript qui encapsule les services REST Azure Maps et facilite leur utilisation en JavaScript. Le module est utile pour faire tourner la fonctionnalité de recherche.

    ```HTML
    <script src="https://atlas.microsoft.com/sdk/javascript/service/2/atlas-service.min.js"></script>
    ```

4. Ajoutez des références à *index.js* et *index.css*.

    ```HTML
    <link rel="stylesheet" href="index.css" type="text/css">
    <script src="index.js"></script>
    ```

5. Dans le corps du document, ajoutez une balise `header`. À l’intérieur de la balise `header`, ajoutez le logo et nom de la société.

    ```HTML
    <header>
        <img src="images/Logo.png" />
        <span>Contoso Coffee</span>
    </header>
    ```

6. Ajoutez une balise `main` et créez un volet de recherche qui comprend une zone de texte et un bouton de recherche. De même, ajoutez des références `div` pour la carte, le volet de liste, puis le bouton de localisation de l’utilisateur (My Location).

    ```HTML
    <main>
        <div class="searchPanel">
            <div>
                <input id="searchTbx" type="search" placeholder="Find a store" />
                <button id="searchBtn" title="Search"></button>
            </div>
        </div>
        <div id="listPanel"></div>
        <div id="myMap"></div>
        <button id="myLocationBtn" title="My Location"></button>
    </main>
    ```

Lorsque vous avez terminé, *index.html* doit ressembler à [cet exemple de fichier index.html](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/Simple%20Store%20Locator/index.html).

## <a name="define-the-css-styles"></a>Définir les styles CSS

L’étape suivante consiste à définir les styles CSS. Les styles CSS définissent la façon dont les composants de l’application sont disposés ainsi que son apparence.

1. Ouvrez le fichier *index.css*.

2. Ajoutez le code css suivant :

    >[!NOTE]
    > Le style `@media` définit les options de style secondaire à utiliser quand la largeur d’écran est inférieure à 700 pixels.  

   ```CSS
    html, body {
        padding: 0;
        margin: 0;
        font-family: Gotham, Helvetica, sans-serif;
        overflow-x: hidden;
    } 

    header {
        width: calc(100vw - 10px);
        height: 30px;
        padding: 15px 0 20px 20px;
        font-size: 25px;
        font-style: italic;
        font-family: "Comic Sans MS", cursive, sans-serif;
        line-height: 30px;
        font-weight: bold;
        color: white;
        background-color: #007faa;
    }

    header span {
        vertical-align: middle;
    }

    header img {
        height: 30px;
        vertical-align: middle;
    }

    .searchPanel {
        position: relative;
        width: 350px;
    }

    .searchPanel div {
        padding: 20px;
    }

    .searchPanel input {
        width: calc(100% - 50px);
        font-size: 16px;
        border: 0;
        border-bottom: 1px solid #ccc;
    }

    #listPanel {
        position: absolute;
        top: 135px;
        left: 0px;
        width: 350px;
        height: calc(100vh - 135px);
        overflow-y: auto;
    }

    #myMap { 
        position: absolute;
        top: 65px;
        left: 350px;
        width: calc(100vw - 350px);
        height: calc(100vh - 65px);
    }

    .statusMessage {
        margin: 10px;
    }

    #myLocationBtn, #searchBtn {
        margin: 0;
        padding: 0;
        border: none;
        border-collapse: collapse;
        width: 32px;
        height: 32px; 
        text-align: center;
        cursor: pointer;
        line-height: 32px;
        background-repeat: no-repeat;
        background-size: 20px;
        background-position: center center;
        z-index: 200;
    }

    #myLocationBtn {
        position: absolute;
        top: 150px;
        right: 10px;
        box-shadow: 0px 0px 4px rgba(0,0,0,0.16);
        background-color: white;
        background-image: url("images/GpsIcon.png");
    }

    #myLocationBtn:hover {
        background-image: url("images/GpsIcon-hover.png");
    }

    #searchBtn {
        background-color: transparent;
        background-image: url("images/SearchIcon.png");
    }

    #searchBtn:hover {
        background-image: url("images/SearchIcon-hover.png");
    }

    .listItem {
        height: 50px;
        padding: 20px;
        font-size: 14px;
    }

    .listItem:hover {
        cursor: pointer;
        background-color: #f1f1f1;
    }

    .listItem-title {
        color: #007faa;
        font-weight: bold;
    }

    .storePopup {
        min-width: 150px;
    }

    .storePopup .popupTitle {
        border-top-left-radius: 4px;
        border-top-right-radius: 4px;
        padding: 8px;
        height: 30px;
        background-color: #007faa;
        color: white;
        font-weight: bold;
    }

    .storePopup .popupSubTitle {
        font-size: 10px;
        line-height: 12px;
    }

    .storePopup .popupContent {
        font-size: 11px;
        line-height: 18px;
        padding: 8px;
    }

    .storePopup img {
        vertical-align:middle;
        height: 12px;
        margin-right: 5px;
    }

    /* Adjust the layout of the page when the screen width is fewer than 700 pixels. */
    @media screen and (max-width: 700px) {
        .searchPanel {
            width: 100vw;
        }

        #listPanel {
            top: 385px;
            width: 100%;
            height: calc(100vh - 385px);
        }

        #myMap {
            width: 100vw;
            height: 250px;
            top: 135px;
            left: 0px;
        }

        #myLocationBtn {
            top: 220px;
        }
    }

    .mapCenterIcon {
        display: block;
        width: 10px;
        height: 10px;
        border-radius: 50%;
        background: orange;
        border: 2px solid white;
        cursor: pointer;
        box-shadow: 0 0 0 rgba(0, 204, 255, 0.4);
        animation: pulse 3s infinite;
    }

    @keyframes pulse {
        0% {
            box-shadow: 0 0 0 0 rgba(0, 204, 255, 0.4);
        }

        70% {
            box-shadow: 0 0 0 50px rgba(0, 204, 255, 0);
        }

        100% {
            box-shadow: 0 0 0 0 rgba(0, 204, 255, 0);
        }
    }
   ```

Exécutez l'application. L’en-tête, la zone de recherche et le bouton de recherche s’affichent. Toutefois, la carte n’est pas visible, car elle n’a pas encore été chargée. Si vous essayez d’effectuer une recherche, il ne se passe rien. Nous devons configurer la logique JavaScript, décrite dans la section suivante. Cette logique accède à toutes les fonctionnalités du localisateur de magasin.

## <a name="add-javascript-code"></a>Ajouter du code JavaScript

Le code JavaScript de l’application de localisateur de cafés Contoso Coffee permet d’effectuer les processus suivants :

1. Il ajoute un [écouteur d’événements](/javascript/api/azure-maps-control/atlas.map#events) appelé `ready` pour attendre que la page ait terminé son processus de chargement. Une fois le chargement de la page terminé, le gestionnaire d’événements crée d’autres écouteurs d’événements pour surveiller le chargement de la carte et donner une fonctionnalité aux boutons de recherche et **Ma position**.

2. Quand l’utilisateur sélectionne le bouton de recherche ou saisit une localisation dans la zone de recherche puis appuie sur Entrée, une recherche approximative par rapport à la requête de l’utilisateur est démarrée. Le code transmet un tableau de valeurs ISO 2 de pays/régions à l’option `countrySet` pour limiter les résultats de la recherche à ces pays/régions. Le fait de limiter la recherche de pays/régions a pour effet d’accroître la précision des résultats renvoyés.
  
3. Une fois la recherche terminée, le premier résultat de localisation est utilisé comme point central de la caméra cartographique. Lorsque l’utilisateur sélectionne le bouton Ma position, le code récupère la localisation de l’utilisateur à l’aide de l’*API de géolocalisation HTML5* intégrée au navigateur. Après avoir récupéré la localisation, le code centre la carte sur la localisation de l’utilisateur.  

Pour ajouter le code JavaScript :

1. Ouvrez le fichier *index.js*.

2. Ajoutez des options globales pour faciliter la mise à jour des paramètres. Définissez les variables de la carte, de la fenêtre contextuelle, de la source de données, de la couche d’icônes et du marqueur HTML. Définissez le marqueur HTML pour indiquer le centre d’une zone de recherche. Et définissez une instance du client du service de recherche Azure Maps.

    ```JavaScript
    //The maximum zoom level to cluster data point data on the map.
    var maxClusterZoomLevel = 11;

    //The URL to the store location data.
    var storeLocationDataUrl = 'data/ContosoCoffee.txt';

    //The URL to the icon image.
    var iconImageUrl = 'images/CoffeeIcon.png';
    var map, popup, datasource, iconLayer, centerMarker, searchURL;
    ```

3. Ajoutez le code d’initialisation suivant. Remplacez `<Your Azure Maps Key>` par la clé de votre abonnement principal.

   > [!Tip]
   > Si vous utilisez des fenêtres indépendantes, il est préférable de créer une seule instance de `Popup` et de la réutiliser en mettant à jour son contenu et la position. Pour chaque instance de `Popup` que vous ajoutez à votre code, plusieurs éléments DOM sont ajoutés à la page. Et plus les éléments DOM sont nombreux dans une page, plus le navigateur doit effectuer un suivi important. La présence d’un trop grand nombre d’éléments peut ralentir le navigateur.

    ```JavaScript

    function initialize() {
        //Initialize a map instance.
        map = new atlas.Map('myMap', {
            center: [-90, 40],
            zoom: 2,

            //Add your Azure Maps primary subscription key to the map SDK.
            authOptions: {
                authType: 'subscriptionKey',
                subscriptionKey: '<Your Azure Maps Key>'
            }
        });

        //Create a pop-up window, but leave it closed so we can update it and display it later.
        popup = new atlas.Popup();

        //Use SubscriptionKeyCredential with a subscription key
        const subscriptionKeyCredential = new atlas.service.SubscriptionKeyCredential(atlas.getSubscriptionKey());

        //Use subscriptionKeyCredential to create a pipeline
        const pipeline = atlas.service.MapsURL.newPipeline(subscriptionKeyCredential, {
            retryOptions: { maxTries: 4 } // Retry options
        });

        //Create an instance of the SearchURL client.
        searchURL = new atlas.service.SearchURL(pipeline);

        //If the user selects the search button, geocode the value the user passed in.
        document.getElementById('searchBtn').onclick = performSearch;

        //If the user presses Enter in the search box, perform a search.
        document.getElementById('searchTbx').onkeyup = function(e) {
            if (e.keyCode === 13) {
                performSearch();
            }
        };

        //If the user selects the My Location button, use the Geolocation API (Preview) to get the user's location. Center and zoom the map on that location.
        document.getElementById('myLocationBtn').onclick = setMapToUserLocation;

        //Wait until the map resources are ready.
        map.events.add('ready', function() {

            //Add your post-map load functionality.

        });
    }

    //Create an array of country/region ISO 2 values to limit searches to. 
    var countrySet = ['US', 'CA', 'GB', 'FR','DE','IT','ES','NL','DK'];

    function performSearch() {
        var query = document.getElementById('searchTbx').value;

        //Perform a fuzzy search on the users query.
        searchURL.searchFuzzy(atlas.service.Aborter.timeout(3000), query, {
            //Pass in the array of country/region ISO2 for which we want to limit the search to.
            countrySet: countrySet
        }).then(results => {
            //Parse the response into GeoJSON so that the map can understand.
            var data = results.geojson.getFeatures();

            if (data.features.length > 0) {
                //Set the camera to the bounds of the results.
                map.setCamera({
                    bounds: data.features[0].bbox,
                    padding: 40
                });
            } else {
                document.getElementById('listPanel').innerHTML = '<div class="statusMessage">Unable to find the location you searched for.</div>';
            }
        });
    }

    function setMapToUserLocation() {
        //Request the user's location.
        navigator.geolocation.getCurrentPosition(function(position) {
            //Convert the Geolocation API (Preview) position to a longitude and latitude position value that the map can interpret and center the map over it.
            map.setCamera({
                center: [position.coords.longitude, position.coords.latitude],
                zoom: maxClusterZoomLevel + 1
            });
        }, function(error) {
            //If an error occurs when the API tries to access the user's position information, display an error message.
            switch (error.code) {
                case error.PERMISSION_DENIED:
                    alert('User denied the request for geolocation.');
                    break;
                case error.POSITION_UNAVAILABLE:
                    alert('Position information is unavailable.');
                    break;
                case error.TIMEOUT:
                    alert('The request to get user position timed out.');
                    break;
                case error.UNKNOWN_ERROR:
                    alert('An unknown error occurred.');
                    break;
            }
        });
    }

    //Initialize the application when the page is loaded.
    window.onload = initialize;
    ```

4. Dans l’écouteur d’événements `ready` de la carte, ajoutez un contrôle de zoom et un marqueur HTML pour afficher le centre d’une zone de recherche.

    ```JavaScript
    //Add a zoom control to the map.
    map.controls.add(new atlas.control.ZoomControl(), {
        position: 'top-right'
    });

    //Add an HTML marker to the map to indicate the center to use for searching.
    centerMarker = new atlas.HtmlMarker({
        htmlContent: '<div class="mapCenterIcon"></div>',
        position: map.getCamera().center
    });

    map.markers.add(centerMarker);
    ```

5. Dans l’écouteur d’événements `ready` de la carte, ajoutez une source de données. Formulez ensuite un appel pour charger et analyser le jeu de données. Activez le regroupement sur la source de données. Le regroupement sur la source de données a pour effet de rassembler les points qui se chevauchent. Lorsque l’utilisateur effectue un zoom avant, les groupes se séparent en points individuels. Ce comportement offre une meilleure expérience utilisateur et améliore les performances.

    ```JavaScript
    //Create a data source, add it to the map, and then enable clustering.
    datasource = new atlas.source.DataSource(null, {
        cluster: true,
        clusterMaxZoom: maxClusterZoomLevel - 1
    });

    map.sources.add(datasource);

    //Load all the store data now that the data source is defined.  
    loadStoreData();
    ```

6. Après le chargement du jeu de données dans l’écouteur d’événements `ready` de la carte, définissez un ensemble de couches pour afficher les données. Une couche de bulles affiche des points de données groupés. Une couche de symboles affiche le nombre de points présents dans chaque groupe au-dessus de la couche de bulles. Une deuxième couche de symboles restitue une icône personnalisée pour chaque emplacement sur la carte.

   Ajoutez des événements `mouseover` et `mouseout` aux couches de bulles et d’icônes pour modifier le curseur de la souris quand l’utilisateur pointe sur un groupe ou une icône de la carte. Ajoutez un événement `click` à la couche de bulles du groupe. Cet événement `click` effectue un zoom avant de deux niveaux sur la carte et centre celle-ci sur le groupe que l’utilisateur sélectionne. Ajoutez un événement `click` à la couche d’icônes. Cet événement `click` affiche une fenêtre indépendante qui affiche les détails d’un café quand un utilisateur sélectionne une icône d’emplacement individuel. Ajoutez un événement à la carte pour surveiller à quel moment la carte cesse de se déplacer. Quand cet événement se déclenche, mettez à jour les éléments dans le volet de liste.  

    ```JavaScript
    //Create a bubble layer to render clustered data points.
    var clusterBubbleLayer = new atlas.layer.BubbleLayer(datasource, null, {
        radius: 12,
        color: '#007faa',
        strokeColor: 'white',
        strokeWidth: 2,
        filter: ['has', 'point_count'] //Only render data points that have a point_count property; clusters have this property.
    });

    //Create a symbol layer to render the count of locations in a cluster.
    var clusterLabelLayer = new atlas.layer.SymbolLayer(datasource, null, {
        iconOptions: {
            image: 'none' //Hide the icon image.
        },

        textOptions: {
            textField: ['get', 'point_count_abbreviated'],
            size: 12,
            font: ['StandardFont-Bold'],
            offset: [0, 0.4],
            color: 'white'
        }
    });

    map.layers.add([clusterBubbleLayer, clusterLabelLayer]);

    //Load a custom image icon into the map resources.
    map.imageSprite.add('myCustomIcon', iconImageUrl).then(function() {

    //Create a layer to render a coffee cup symbol above each bubble for an individual location.
    iconLayer = new atlas.layer.SymbolLayer(datasource, null, {
        iconOptions: {
            //Pass in the ID of the custom icon that was loaded into the map resources.
            image: 'myCustomIcon',

            //Optionally, scale the size of the icon.
            font: ['SegoeUi-Bold'],

            //Anchor the center of the icon image to the coordinate.
            anchor: 'center',

            //Allow the icons to overlap.
            allowOverlap: true
        },

        filter: ['!', ['has', 'point_count']] //Filter out clustered points from this layer.
    });

    map.layers.add(iconLayer);

    //When the mouse is over the cluster and icon layers, change the cursor to a pointer.
    map.events.add('mouseover', [clusterBubbleLayer, iconLayer], function() {
        map.getCanvasContainer().style.cursor = 'pointer';
    });

    //When the mouse leaves the item on the cluster and icon layers, change the cursor back to the default (grab).
    map.events.add('mouseout', [clusterBubbleLayer, iconLayer], function() {
        map.getCanvasContainer().style.cursor = 'grab';
    });

    //Add a click event to the cluster layer. When the user selects a cluster, zoom into it by two levels.  
    map.events.add('click', clusterBubbleLayer, function(e) {
        map.setCamera({
            center: e.position,
            zoom: map.getCamera().zoom + 2
        });
    });

    //Add a click event to the icon layer and show the shape that was selected.
    map.events.add('click', iconLayer, function(e) {
        showPopup(e.shapes[0]);
    });

    //Add an event to monitor when the map is finished rendering the map after it has moved.
    map.events.add('render', function() {
        //Update the data in the list.
        updateListItems();
    });
    ```

7. Une fois que le jeu de données des cafés est chargé, il doit d’abord être téléchargé. Ensuite, le fichier texte doit être décomposé en lignes. La première ligne contient les informations d’en-tête. Pour que le code soit plus facile à suivre, nous analysons l’en-tête dans un objet, dont nous pouvons ensuite nous servir pour rechercher l’index de cellule de chaque propriété. Après la première ligne, parcourez en boucle les lignes restantes et créez une fonctionnalité de point. Ajoutez la fonctionnalité de point à la source de données. Pour finir, mettez à jour le volet de liste.

    ```JavaScript
    function loadStoreData() {

    //Download the store location data.
    fetch(storeLocationDataUrl)
        .then(response => response.text())
        .then(function(text) {

            //Parse the tab-delimited file data into GeoJSON features.
            var features = [];

            //Split the lines of the file.
            var lines = text.split('\n');

            //Grab the header row.
            var row = lines[0].split('\t');

            //Parse the header row and index each column to make the code for parsing each row easier to follow.
            var header = {};
            var numColumns = row.length;
            for (var i = 0; i < row.length; i++) {
                header[row[i]] = i;
            }

            //Skip the header row and then parse each row into a GeoJSON feature.
            for (var i = 1; i < lines.length; i++) {
                row = lines[i].split('\t');

                //Ensure that the row has the correct number of columns.
                if (row.length >= numColumns) {

                    features.push(new atlas.data.Feature(new atlas.data.Point([parseFloat(row[header['Longitude']]), parseFloat(row[header['Latitude']])]), {
                        AddressLine: row[header['AddressLine']],
                        City: row[header['City']],
                        Municipality: row[header['Municipality']],
                        AdminDivision: row[header['AdminDivision']],
                        Country: row[header['Country']],
                        PostCode: row[header['PostCode']],
                        Phone: row[header['Phone']],
                        StoreType: row[header['StoreType']],
                        IsWiFiHotSpot: (row[header['IsWiFiHotSpot']].toLowerCase() === 'true') ? true : false,
                        IsWheelchairAccessible: (row[header['IsWheelchairAccessible']].toLowerCase() === 'true') ? true : false,
                        Opens: parseInt(row[header['Opens']]),
                        Closes: parseInt(row[header['Closes']])
                    }));
                }
            }

            //Add the features to the data source.
            datasource.add(new atlas.data.FeatureCollection(features));

            //Initially, update the list items.
            updateListItems();
        });
    }
    ```

8. Une fois le volet de liste mis à jour, la distance est calculée. Il s’agit de la distance entre le centre de la carte et tous les points présents dans la carte actuelle. Les points sont ensuite triés par distance. Du code HTML est généré pour afficher chaque emplacement dans le volet de liste.

    ```JavaScript
    var listItemTemplate = '<div class="listItem" onclick="itemSelected(\'{id}\')"><div class="listItem-title">{title}</div>{city}<br />Open until {closes}<br />{distance} miles away</div>';

    function updateListItems() {
        //Hide the center marker.
        centerMarker.setOptions({
            visible: false
        });

        //Get the current camera and view information for the map.
        var camera = map.getCamera();
        var listPanel = document.getElementById('listPanel');

        //Check to see whether the user is zoomed out a substantial distance. If they are, tell the user to zoom in and to perform a search or select the My Location button.
        if (camera.zoom < maxClusterZoomLevel) {
            //Close the pop-up window; clusters might be displayed on the map.  
            popup.close(); 
            listPanel.innerHTML = '<div class="statusMessage">Search for a location, zoom the map, or select the My Location button to see individual locations.</div>';
        } else {
            //Update the location of the centerMarker property.
            centerMarker.setOptions({
                position: camera.center,
                visible: true
            });

            //List the ten closest locations in the side panel.
            var html = [], properties;

            /*
            Generating HTML for each item that looks like this:
            <div class="listItem" onclick="itemSelected('id')">
                <div class="listItem-title">1 Microsoft Way</div>
                Redmond, WA 98052<br />
                Open until 9:00 PM<br />
                0.7 miles away
            </div>
            */

            //Get all the shapes that have been rendered in the bubble layer. 
            var data = map.layers.getRenderedShapes(map.getCamera().bounds, [iconLayer]);

            //Create an index of the distances of each shape.
            var distances = {};

            data.forEach(function (shape) {
                if (shape instanceof atlas.Shape) {

                    //Calculate the distance from the center of the map to each shape and store in the index. Round to 2 decimals.
                    distances[shape.getId()] = Math.round(atlas.math.getDistanceTo(camera.center, shape.getCoordinates(), 'miles') * 100) / 100;
                }
            });

            //Sort the data by distance.
            data.sort(function (x, y) {
                return distances[x.getId()] - distances[y.getId()];
            });

            data.forEach(function(shape) {
                properties = shape.getProperties();
                html.push('<div class="listItem" onclick="itemSelected(\'', shape.getId(), '\')"><div class="listItem-title">',
                properties['AddressLine'],
                '</div>',
                //Get a formatted addressLine2 value that consists of City, Municipality, AdminDivision, and PostCode.
                getAddressLine2(properties),
                '<br />',

                //Convert the closing time to a format that is easier to read.
                getOpenTillTime(properties),
                '<br />',

                //Get the distance of the shape.
                distances[shape.getId()],
                ' miles away</div>');
            });

            listPanel.innerHTML = html.join('');

            //Scroll to the top of the list panel in case the user has scrolled down.
            listPanel.scrollTop = 0;
        }
    }

    //This converts a time that's in a 24-hour format to an AM/PM time or noon/midnight string.
    function getOpenTillTime(properties) {
        var time = properties['Closes'];
        var t = time / 100;
        var sTime;

        if (time === 1200) {
            sTime = 'noon';
        } else if (time === 0 || time === 2400) {
            sTime = 'midnight';
        } else {
            sTime = Math.round(t) + ':';

            //Get the minutes.
            t = (t - Math.round(t)) * 100;

            if (t === 0) {
                sTime += '00';
            } else if (t < 10) {
                sTime += '0' + t;
            } else {
                sTime += Math.round(t);
            }

            if (time < 1200) {
                sTime += ' AM';
            } else {
                sTime += ' PM';
            }
        }

        return 'Open until ' + sTime;
    }

    //Create an addressLine2 string that contains City, Municipality, AdminDivision, and PostCode.
    function getAddressLine2(properties) {
        var html = [properties['City']];

        if (properties['Municipality']) {
            html.push(', ', properties['Municipality']);
        }

        if (properties['AdminDivision']) {
            html.push(', ', properties['AdminDivision']);
        }

        if (properties['PostCode']) {
            html.push(' ', properties['PostCode']);
        }

        return html.join('');
    }
    ```

9. Quand l’utilisateur sélectionne un élément dans le volet de liste, la forme à laquelle l’élément est associé est récupérée dans la source de données. Une fenêtre indépendante est générée en fonction des informations de propriété stockées dans la forme. La carte est centrée sur la forme. Si la largeur de la carte est inférieure à 700 pixels, la carte est décalée de sorte que la fenêtre contextuelle soit visible.

    ```JavaScript
    //When a user selects a result in the side panel, look up the shape by its ID value and display the pop-up window.
    function itemSelected(id) {
        //Get the shape from the data source by using its ID.  
        var shape = datasource.getShapeById(id);
        showPopup(shape);

        //Center the map over the shape on the map.
        var center = shape.getCoordinates();
        var offset;

        //If the map is fewer than 700 pixels wide, then the layout is set for small screens.
        if (map.getCanvas().width < 700) {
            //When the map is small, offset the center of the map relative to the shape so that there is room for the popup to appear.
            offset = [0, -80];
        }

        map.setCamera({
            center: center,
            centerOffset: offset
        });
    }

    function showPopup(shape) {
        var properties = shape.getProperties();

        /* Generating HTML for the pop-up window that looks like this:

            <div class="storePopup">
                <div class="popupTitle">
                    3159 Tongass Avenue
                    <div class="popupSubTitle">Ketchikan, AK 99901</div>
                </div>
                <div class="popupContent">
                    Open until 22:00 PM<br/>
                    <img title="Phone Icon" src="images/PhoneIcon.png">
                    <a href="tel:1-800-XXX-XXXX">1-800-XXX-XXXX</a>
                    <br>Amenities:
                    <img title="Wi-Fi Hotspot" src="images/WiFiIcon.png">
                    <img title="Wheelchair Accessible" src="images/WheelChair-small.png">
                </div>
            </div>
        */

         //Calculate the distance from the center of the map to the shape in miles, round to 2 decimals.
        var distance = Math.round(atlas.math.getDistanceTo(map.getCamera().center, shape.getCoordinates(), 'miles') * 100)/100;

        var html = ['<div class="storePopup">'];
        html.push('<div class="popupTitle">',
            properties['AddressLine'],
            '<div class="popupSubTitle">',
            getAddressLine2(properties),
            '</div></div><div class="popupContent">',

            //Convert the closing time to a format that's easier to read.
            getOpenTillTime(properties),

            //Add the distance information.  
            '<br/>', distance,
            ' miles away',
            '<br /><img src="images/PhoneIcon.png" title="Phone Icon"/><a href="tel:',
            properties['Phone'],
            '">',  
            properties['Phone'],
            '</a>'
        );

        if (properties['IsWiFiHotSpot'] || properties['IsWheelchairAccessible']) {
            html.push('<br/>Amenities: ');

            if (properties['IsWiFiHotSpot']) {
                html.push('<img src="images/WiFiIcon.png" title="Wi-Fi Hotspot"/>');
            }

            if (properties['IsWheelchairAccessible']) {
                html.push('<img src="images/WheelChair-small.png" title="Wheelchair Accessible"/>');
            }
        }

        html.push('</div></div>');

        //Update the content and position of the pop-up window for the specified shape information.
        popup.setOptions({

            //Create a table from the properties in the feature.
            content:  html.join(''),
            position: shape.getCoordinates()
        });

        //Open the pop-up window.
        popup.open(map);
    }
    ```

Maintenant, vous disposez d’un localisateur de magasin entièrement opérationnel. Dans un navigateur web, ouvrez le fichier *index.html* du localisateur de magasin. Une fois les groupes affichés sur la carte, vous pouvez rechercher un emplacement. Pour cela, utilisez la zone de recherche, sélectionnez le bouton de localisation de l’utilisateur (My Location), sélectionnez un groupe ou effectuez un zoom avant sur la carte pour afficher les emplacements individuels.

La première fois qu’un utilisateur sélectionne le bouton de localisation (My Location), le navigateur affiche un avertissement de sécurité qui demande l’autorisation d’accéder à l’emplacement de l’utilisateur. Si l’utilisateur accepte de partager son emplacement, la carte effectue un zoom avant sur cet emplacement, et les cafés qui se trouvent à proximité sont affichés.

![Capture d’écran de la demande du navigateur à accéder à l’emplacement de l’utilisateur](./media/tutorial-create-store-locator/geolocation-api-warning.png)

Quand vous zoomez suffisamment sur une zone où se trouvent des cafés, les groupes se séparent en emplacements individuels. Sélectionnez l’une des icônes sur la carte ou sélectionnez un élément dans le volet latéral pour afficher une fenêtre indépendante. Celle-ci montre des informations sur la localisation correspondante.

![Capture d’écran du localisateur de magasin finalisé](./media/tutorial-create-store-locator/finished-simple-store-locator.png)

Si vous redimensionnez la fenêtre du navigateur en définissant une largeur de moins de 700 pixels ou que vous ouvrez l’application sur un appareil mobile, la disposition change pour s’adapter à la taille inférieure de l’écran.

![Capture d’écran de la version « petit écran » du localisateur de magasin](./media/tutorial-create-store-locator/finished-simple-store-locator-mobile.png)

Ce tutoriel vous a appris à créer un localisateur de magasin simple à l’aide d’Azure Maps. Le localisateur de magasin que vous créez offre peut-être de toutes fonctionnalités dont vous avez besoin. Vous pouvez en ajouter d’autres ou utiliser des fonctionnalités plus avancées de façon à personnaliser davantage d’expérience utilisateur : 

 * Activez les [suggestions pendant que vous tapez](https://azuremapscodesamples.azurewebsites.net/?sample=Search%20Autosuggest%20and%20JQuery%20UI) dans la zone de recherche.  
 * Ajoutez la [prise en charge multilingue](https://azuremapscodesamples.azurewebsites.net/?sample=Map%20Localization). 
 * Autorisez l’utilisateur à [filtrer les emplacements le long d’un itinéraire](https://azuremapscodesamples.azurewebsites.net/?sample=Filter%20Data%20Along%20Route). 
 * Ajoutez la possibilité de [définir des filtres](https://azuremapscodesamples.azurewebsites.net/?sample=Filter%20Symbols%20by%20Property). 
 * Ajoutez la possibilité de spécifier une valeur de recherche initiale en utilisant une chaîne de requête. Quand vous incluez cette option dans votre localisateur de magasins, les utilisateurs sont alors en mesure d’ajouter les recherches aux favoris et les partager. Elle offre aussi un moyen simple de transférer les recherches de la page active vers une autre.  
 * Déployez votre localisateur de magasin en tant qu’[application web Azure App Service](../app-service/quickstart-html.md). 
 * Stockez vos données dans une base de données et recherchez les emplacements à proximité. Pour en savoir plus, consultez la [présentation des types de données spatiales SQL Server](/sql/relational-databases/spatial/spatial-data-types-overview?preserve-view=true&view=sql-server-2017) et [Interroger des données spatiales au sujet du plus proche voisin](/sql/relational-databases/spatial/query-spatial-data-for-nearest-neighbor?preserve-view=true&view=sql-server-2017).

Vous pouvez [consulter le code source complet ici](https://github.com/Azure-Samples/AzureMapsCodeSamples/tree/master/AzureMapsCodeSamples/Tutorials/Simple%20Store%20Locator). [Consultez un exemple en direct](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Simple%20Store%20Locator) et apprenez-en davantage sur la couverture et les capacités d’Azure Maps en utilisant les [niveaux de zoom et la grille de mosaïques](zoom-levels-and-tile-grid.md). Vous pouvez également [utiliser des expressions de style axées sur les données](data-driven-style-expressions-web-sdk.md) à appliquer à votre logique métier.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Aucune ressource ne nécessite un nettoyage.

## <a name="next-steps"></a>Étapes suivantes

Pour bénéficier de davantage d’exemples de code et d’une expérience de codage interactive :

> [!div class="nextstepaction"]
> [Comment utiliser le contrôle de carte](how-to-use-map-control.md)
