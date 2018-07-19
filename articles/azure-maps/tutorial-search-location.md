---
title: Rechercher avec Azure Maps | Microsoft Docs
description: Rechercher un point d’intérêt de proximité à l’aide d’Azure Maps
author: dsk-2015
ms.author: dkshir
ms.date: 05/07/2018
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: ffc4b7625a6c43f8e2801313c61f14c785a3ec5f
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38988872"
---
# <a name="search-nearby-points-of-interest-using-azure-maps"></a>Rechercher des points d’intérêt de proximité à l’aide d’Azure Maps

Ce didacticiel montre comment configurer un compte avec Azure Maps, puis utiliser les API Maps pour rechercher un point d’intérêt. Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer un compte Azure Maps
> * Récupérer la clé primaire de votre compte Maps
> * Créer une page web à l’aide de l’API Map Control
> * Utiliser le service Azure Maps Search pour rechercher un point d’intérêt de proximité

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="log-in-to-the-azure-portal"></a>Se connecter au portail Azure
Connectez-vous au [portail Azure](https://portal.azure.com).

<a id="createaccount"></a>

## <a name="create-an-account-with-azure-maps"></a>Créer un compte avec Azure Maps

Créez un compte Maps en suivant la procédure suivante :

1. En haut à gauche du [portail Azure](https://portal.azure.com), cliquez sur **Créer une ressource**.
2. Dans la zone *Rechercher sur la Place de marché*, tapez **Maps**.
3. Dans les *Résultats*, sélectionnez **Maps**. Cliquez sur le bouton **Créer** qui s’affiche sous la carte. 
4. Sur la page **Créer un compte Maps**, entrez les valeurs suivantes :
    - Le *Nom* de votre nouveau compte. 
    - *L’Abonnement* à utiliser pour ce compte.
    - Le *Groupe de ressources* pour ce compte. Vous pouvez choisir de *Créer* ou d’utiliser un groupe de ressources *Existant*.
    - Sélectionnez *l’Emplacement du groupe de ressources*.
    - Lisez la *Licence* et la *Déclaration de confidentialité*, puis cochez la case pour accepter les conditions. 
    - Cliquez sur le bouton **Créer**.
   
    ![Créer un compte Maps sur le Portail](./media/tutorial-search-location/create-account.png)


<a id="getkey"></a>

## <a name="get-the-primary-key-for-your-account"></a>Obtenir la clé primaire de votre compte

Une fois le compte Maps créé, récupérez la clé qui vous permet d’interroger les API Maps.

1. Ouvrez votre compte Maps dans le portail.
2. Dans la section des paramètres; sélectionnez **Clés**.
3. Copiez la **Clé primaire** dans le Presse-papiers. Enregistrez-la localement, vous l’utiliserez plus tard dans ce didacticiel. 

    ![Obtenir la clé primaire dans le portail](./media/tutorial-search-location/get-key.png)


<a id="createmap"></a>

## <a name="create-a-new-map"></a>Créer une carte 
L’API Map Control est une bibliothèque cliente pratique qui vous permet d’intégrer facilement Maps à votre application web. Elle masque la complexité des appels de service REST bruts et améliore votre productivité grâce à des composants personnalisables et dont vous pouvez définir le style. Les étapes suivantes vous indiquent comment créer une page HTML statique intégrée avec l’API Map Control. 

1. Sur votre ordinateur local, créez un fichier et nommez-le **MapSearch.html**. 
2. Ajoutez les composants HTML au fichier :

    ```HTML
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="utf-8" />
        <meta name="viewport" content="width=device-width, user-scalable=no" />
        <title>Map Search</title>

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
    Notez que l’en-tête HTML inclut les fichiers de ressources CSS et JavaScript hébergés par la bibliothèque Azure Map Control. En outre, un segment *script* a été ajouté au corps (*body*) du fichier HTML. Ce segment est destiné à contenir du code JavaScript inline pour accéder aux API Azure Maps.
 
3. Ajoutez le code JavaScript suivant au bloc *script* du fichier HTML. Remplacez la chaîne **\<your account key\>** par la clé primaire copiée de votre compte Maps.

    ```JavaScript
    // Instantiate map to the div with id "map"
    var MapsAccountKey = "<your account key>";
    var map = new atlas.Map("map", {
        "subscription-key": MapsAccountKey
    });
    ```
    Ce segment lance l’API Map Control pour votre clé de compte Azure Maps. **Atlas** est l’espace de noms qui contient les API et les composants visuels associés. **atlas.Map** fournit le contrôle d’une carte web visuelle et interactive. 
    
4. Enregistrez vos modifications du fichier, puis ouvrez la page HTML dans un navigateur. Il s’agir de la carte la plus basique que vous pouvez créer en appelant **atlas.map** et en fournissant votre clé de compte. 

   ![Afficher la carte](./media/tutorial-search-location/basic-map.png)


<a id="usesearch"></a>

## <a name="add-search-capabilities"></a>Ajouter les fonctionnalités de recherche

Cette section montre comment utiliser l’API Maps Search pour rechercher un point d’intérêt sur la carte. Il s’agit d’une API RESTful destinée aux développeurs souhaitant mettre en place des fonctionnalités de recherche d’adresses, de points d’intérêt et autres informations d’ordre géographique. Search Service affecte une latitude et une longitude à une adresse spécifiée. 

1. Ajoutez une couche à votre carte pour afficher les résultats de recherche. Ajoutez le code JavaScript suivant au bloc *script*, après le code d’initialisation de la carte. 

    ```JavaScript
    // Initialize the pin layer for search results to the map
    var searchLayerName = "search-results";
    map.addPins([], {
        name: searchLayerName,
        cluster: false,
        icon: "pin-round-darkblue"
    });
    ```

2. Créez un élément [XMLHttpRequest](https://xhr.spec.whatwg.org/) et ajoutez un gestionnaire d’événements afin d’analyser la réponse JSON transmise par le service Maps Search. Cet extrait de code génère le gestionnaire d’événements afin de collecter les adresses, les noms et les données de latitude et de longitude pour chaque emplacement renvoyé dans la variable `searchPins`. Enfin, il ajoute cette collection de points d’emplacement au contrôle `map` sous la forme de marqueurs. 

    ```JavaScript
    // Perform a request to the search service and create a pin on the map for each result
    var xhttp = new XMLHttpRequest();
    xhttp.onreadystatechange = function () {
        var searchPins = [];

        if (this.readyState === 4 && this.status === 200) {
            var response = JSON.parse(this.responseText);

            var poiResults = response.results.filter((result) => { return result.type === "POI" }) || [];

            searchPins = poiResults.map((poiResult) => {
                var poiPosition = [poiResult.position.lon, poiResult.position.lat];
                return new atlas.data.Feature(new atlas.data.Point(poiPosition), {
                    name: poiResult.poi.name,
                    address: poiResult.address.freeformAddress,
                    position: poiResult.position.lat + ", " + poiResult.position.lon
                });
            });

            map.addPins(searchPins, {
                name: searchLayerName
            });

            var lons = searchPins.map((pin) => { return pin.geometry.coordinates[0] });
            var lats = searchPins.map((pin) => { return pin.geometry.coordinates[1] });

            var swLon = Math.min.apply(null, lons);
            var swLat = Math.min.apply(null, lats);
            var neLon = Math.max.apply(null, lons);
            var neLat = Math.max.apply(null, lats);

            map.setCameraBounds({
                bounds: [swLon, swLat, neLon, neLat],
                padding: 50
            });
        }
    };
    ```

3. Ajoutez le code suivant au bloc *script* afin de générer la requête et d’envoyer l’élément XMLHttpRequest au service Maps Search :

    ```JavaScript
    var url = "https://atlas.microsoft.com/search/fuzzy/json?";
    url += "api-version=1.0";
    url += "&query=gasoline%20station";
    url += "&subscription-key=" + MapsAccountKey;
    url += "&lat=47.6292";
    url += "&lon=-122.2337";
    url += "&radius=100000";

    xhttp.open("GET", url, true);
    xhttp.send();
    ``` 
    Cet extrait de code utilise l’API de recherche de base de Search Service, appelée **Fuzzy Search** (recherche approximative). Il gère le jeu d’entrées le plus approximatif, notamment les combinaisons de jetons d’adresses ou de points d’intérêt. Il recherche les **stations service** situées à proximité dans un rayon spécifique par rapport à une adresse donnée exprimée en coordonnées de latitude et de longitude. Il utilise la clé principale de votre compte fournie plus haut dans l’exemple de fichier pour appeler Maps. Il retourne les résultats sous forme de paires latitude/longitude correspondant aux emplacements trouvés. 
    
4. Enregistrez le fichier **MapSearch.html**, puis actualisez votre navigateur. Vous devriez désormais observer que la carte est centrée sur Seattle et que des repères bleus marquent l’emplacement des stations service dans la zone. 

   ![Afficher la carte avec les résultats de la recherche](./media/tutorial-search-location/pins-map.png)

5. Vous pouvez afficher les données brutes rendues par la carte, en copiant l’élément XMLHTTPRequest et en le collant dans le navigateur. Remplacez \<votre clé de compte\> par votre clé primaire. 

   ```http
   https://atlas.microsoft.com/search/fuzzy/json?api-version=1.0&query=gasoline%20station&subscription-key=<your account key>&lat=47.6292&lon=-122.2337&radius=100000
   ```

À ce stade, la page MapSearch peut afficher les emplacements des points d’intérêt renvoyés d’une requête de recherche approximative. Ajoutons quelques fonctionnalités interactives et des informations supplémentaires sur les emplacements. 

## <a name="add-interactive-data"></a>Ajouter des données interactives

La carte développée jusqu’à présent produit des résultats de recherche comportant uniquement les données de latitude/longitude. Si vous analysez le fichier brut JSON renvoyé par le service Maps Search, cependant, vous constatez qu’il contient des informations supplémentaires sur chaque station service, notamment le nom et l’adresse. Vous pouvez intégrer ces données dans la carte à l’aide de zones contextuelles interactives. 

1. Ajoutez les lignes suivantes au bloc *script* afin de créer des fenêtres contextuelles pour les points d’intérêt retournés par Search Service :

    ```JavaScript
    // Add a popup to the map which will display some basic information about a search result on hover over a pin
    var popup = new atlas.Popup();
    map.addEventListener("mouseover", searchLayerName, (e) => {
        var popupContentElement = document.createElement("div");
        popupContentElement.style.padding = "5px";

        var popupNameElement = document.createElement("div");
        popupNameElement.innerText = e.features[0].properties.name;
        popupContentElement.appendChild(popupNameElement);

        var popupAddressElement = document.createElement("div");
        popupAddressElement.innerText = e.features[0].properties.address;
        popupContentElement.appendChild(popupAddressElement);

        var popupPositionElement = document.createElement("div");
        popupPositionElement.innerText = e.features[0].properties.name;
        popupContentElement.appendChild(popupPositionElement);

        popup.setPopupOptions({
            position: e.features[0].geometry.coordinates,
            content: popupContentElement
        });

        popup.open(map);
    });
    ```
    L’API **atlas.Popup** génère une fenêtre d’informations ancrée à la position requise sur la carte. Cet extrait de code définit le contenu et la position de la fenêtre contextuelle et ajoute un écouteur d’événements au contrôle `map`, qui réagit au passage du pointeur de la _souris_ au-dessus du marqueur associé à la fenêtre contextuelle. 

4. Enregistrez le fichier et actualisez votre navigateur. Désormais, la carte dans le navigateur affiche des fenêtres contextuelles de données lorsque vous survolez les repères de recherche. 

    ![Azure Map Control et Search Service](./media/tutorial-search-location/popup-map.png)


## <a name="next-steps"></a>Étapes suivantes
Dans ce tutoriel, vous avez appris à :

> [!div class="checklist"]
> * Créer un compte avec Azure Maps
> * Obtenir la clé primaire de votre compte
> * Créer une page web à l’aide de l’API Map Control
> * Utiliser Search Service pour rechercher des points d’intérêt de proximité

Le prochain didacticiel vous explique comment afficher un itinéraire entre deux emplacements. 

> [!div class="nextstepaction"]
> [Itinéraire vers une destination](./tutorial-route-location.md)
