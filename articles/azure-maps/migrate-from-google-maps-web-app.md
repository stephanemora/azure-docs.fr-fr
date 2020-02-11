---
title: 'Tutoriel : Migrer une application web à partir de Google Maps | Microsoft Azure Maps'
description: Guide pratique pour migrer une application web de Google Maps vers Microsoft Azure Maps.
author: rbrundritt
ms.author: richbrun
ms.date: 12/17/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: d9b873a058410219bc55abc4f575823b519a646b
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/04/2020
ms.locfileid: "76989110"
---
# <a name="migrate-a-web-app-from-google-maps"></a>Migrer une application Web à partir de Google Maps

La plupart des applications web qui utilisent Google Maps utilisent le kit SDK JavaScript Google Maps v3. Le Kit de développement logiciel (SDK) web Azure Maps est le kit de développement logiciel (SDK) Azure approprié vers lequel migrer. Le SDK web Azure Maps vous permet de personnaliser des cartes interactives avec du contenu et des images qui vous sont propres. Vous pouvez exécuter votre application sur des applications web ou mobiles. Ce contrôle utilise WebGL, ce qui vous permet d’afficher d’importants jeux de données avec des performances élevées. Développez avec le kit de développement logiciel (SDK) à l’aide de JavaScript ou de TypeScript.

Si vous migrez une application web existante, vérifiez si elle utilise une bibliothèque de contrôle de carte open source. Voici des exemples of bibliothèque de contrôle de carte open source : Cesium, Leaflet et OpenLayers. Si c’est le cas et que vous ne souhaitez pas utiliser le kit de développement logiciel (SDK) Web Azure Maps, une autre option pour la migration de votre application consiste à continuer à utiliser le contrôle de carte Open source et à le connecter aux services de vignettes de Azure Maps ([ vignettes de route](https://docs.microsoft.com/rest/api/maps/render/getmaptile) \| [vignettes satellites](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytile)). Les points suivants indiquent en détail comment utiliser Azure Maps dans certaines bibliothèques de contrôle de carte open source couramment utilisées.

- Cesium : contrôle carte 3D pour le Web. [Documentation](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Raster%20Tiles%20in%20Cesium%20JS) \| [de l’exemple de code](https://cesiumjs.org/)
- Leaflet : contrôle de carte 2D léger pour le Web. [Documentation](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Azure%20Maps%20Raster%20Tiles%20in%20Leaflet%20JS) \| [de l’exemple de code](https://leafletjs.com/)
- OpenLayers : contrôle de carte 2D pour le Web qui prend en charge les projections. [Documentation](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Raster%20Tiles%20in%20OpenLayers) \| [de l’exemple de code](https://openlayers.org/)

## <a name="key-features-support"></a>Prise en charge des fonctionnalités clés

Le tableau suivant liste les principales fonctionnalités d’API du SDK JavaScript Google Maps v3 et la fonctionnalité d’API prise en charge dans le SDK web Azure Maps.

| Fonctionnalité Google Maps     | Prise en charge du kit de développement logiciel (SDK) Web Azure Maps |
|-------------------------|:--------------------------:|
| Marqueurs                 | ✓                          |
| Clustering de marqueurs       | ✓                          |
| Polylignes et polygones    | ✓                          |
| Couches de données             | ✓                          |
| Superposition de sol         | ✓                          |
| Cartes thermiques               | ✓                          |
| Couches de mosaïques             | ✓                          |
| Couche KML               | ✓                          |
| Outils de dessin           | ✓                          |
| Service de geocoder        | ✓                          |
| Service de directions      | ✓                          |
| Service Matrice des distances | ✓                          |
| Service d’élévation       | Prévu                    |

## <a name="notable-differences-in-the-web-sdks"></a>Différences notables dans les kits de développement logiciel (SDK) Web

Voici quelques-unes des principales différences entre les SDK web Google Maps et Azure Maps à connaître :

- En plus de fournir un point de terminaison hébergé pour accéder au kit de développement logiciel (SDK) Web d’Azure Maps, un package NPM est également disponible pour incorporer le kit de développement logiciel (SDK) Web dans les applications, si vous préférez. Pour plus d’informations, consultez [cette documentation](how-to-use-map-control.md). Ce package inclut aussi des définitions de TypeScript.
- Après avoir créé une instance de la classe Map dans Azure Maps, votre code doit attendre que les mappages `ready` ou les événements `load` se déclenchent avant d’interagir avec le mappage. Cet ordre permet de garantir que toutes les ressources de carte ont été chargées et qu’elles sont accessibles.
- Les deux plates-formes utilisent un système de mosaïque similaire pour les cartes de base, mais les tuiles dans Google Maps ont une dimension de 256 pixels tandis que les tuiles dans Azure Maps ont une dimension de 512 pixels. Par conséquent, pour obtenir la même vue cartographique que Google Maps dans Azure Maps, le niveau de zoom utilisé dans Google Maps doit être soustrait d’une unité dans Azure Maps.
- Les coordonnées dans Google Maps sont appelées « latitude, longitude », tandis qu’Azure Maps utilise « longitude, latitude ». Le format Azure Maps est conforme à la norme `[x, y]` qui est suivie par la plupart des plateformes GIS.
- Les formes du kit de développement logiciel (SDK) Web Azure Maps sont basées sur le schéma GeoJSON. Les classes d’assistance sont exposées par le biais de l’espace de noms [*atlas.data*](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data?view=azure-iot-typescript-latest). Il y a également la classe [*atlas.Shape*](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape). Elle peut être utilisée pour wrapper les objets GeoJSON et faciliter leur mise à jour et leur maintenance d’une manière pouvant être liée aux données.
- Les coordonnées dans Azure Maps sont définies comme des objets Position. Une coordonnée est spécifiée sous la forme d’un tableau de nombres au format `[longitude, latitude]`, ou à l’aide de new atlas.data.Position(longitude, latitude).
    > [!TIP]
    > La classe Position a une méthode d’assistance statique pour l’importation de coordonnées qui sont au format « Latitude, longitude ». La méthode [atlas.data.Position.fromLatLng](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.position?view=azure-iot-typescript-latest) peut souvent être remplacée par la méthode `new google.maps.LatLng` dans le code Google Maps.
- Au lieu de spécifier des informations de style pour chaque forme ajoutée à la carte, Azure Maps sépare les styles des données. Les données sont stockées dans des sources de données et sont connectées aux calques de rendu. Le code Azure Maps utilise les sources de données pour restituer les données. Cette approche offre un gain de performances amélioré. De plus, de nombreux calques prennent en charge le style basé sur les données dans lequel la logique métier peut être ajoutée aux options de style de calque. Cette prise en charge change la façon dont les formes individuelles sont restituées dans un calque en fonction des propriétés définies dans la forme.

## <a name="web-sdk-side-by-side-examples"></a>Exemples côte à côte du kit de développement logiciel (SDK) Web

La collection suivante contient des exemples de code pour chaque plateforme, chacun couvrant des cas d’utilisation courants. Il est destinée à vous aider à migrer votre application web du SDK JavaScript Google Maps v3 vers le SDK web Azure Maps. Des exemples de code liés aux applications web sont fournis en JavaScript. Toutefois, Azure Maps fournit également des définitions TypeScript sous la forme d’une option supplémentaire disponible par le biais d’un module [NPM](how-to-use-map-control.md).

### <a name="load-a-map"></a>Charger une carte

Le chargement d’une carte suit le même ensemble d’étapes dans les deux SDK :

- Ajout d'une référence au Kit de développement logiciel (SDK) de la carte.
- Ajoutez une balise `div` au corps de la page, qui fera office d’espace réservé pour la carte.
- Créez une fonction JavaScript qui est appelée lorsque la page a été chargée.
- Créez une instance de la classe de carte respective.

**Quelques différences clés**

- Google Maps requiert la spécification d’une clé de compte dans la référence de script de l’API. Les informations d’authentification pour Azure Maps sont spécifiées en tant qu’options de la classe de carte. Ces informations d’identification peuvent être une clé d’abonnement ou des informations Azure Active Directory.
- Google Maps prend une fonction de rappel dans la référence de script de l’API qui est utilisée pour appeler une fonction d’initialisation afin de charger la carte. Avec Azure Maps l’événement OnLoad de la page doit être utilisé.
- Lors du référencement de l’élément `div` dans lequel la carte est rendue, la classe `Map` dans Azure Maps nécessite uniquement la valeur `id`, tandis que Google Maps requiert un objet `HTMLElement`.
- Les coordonnées dans Azure Maps sont définies comme des objets Position qui peuvent être spécifiés comme un simple tableau de nombres au format `[longitude, latitude]`.
- Le niveau de zoom dans Azure Maps est inférieur d’un niveau à celui de l’exemple Google Maps. Cette différence est due à la différence de tailles du système de mosaïques entre les deux plateformes.
- Azure Maps n’ajoute aucun contrôle de navigation au canevas de carte. Par conséquent, par défaut, une carte n’a pas de boutons de zoom et de boutons de style de carte. Il existe cependant des commandes pour ajouter un sélecteur de style de carte, des boutons de zoom, une boussole ou une commande de rotation et un réglage de vitesse.
- Un gestionnaire d’événements est ajouté dans Azure Maps pour surveiller l’événement `ready` de l’instance de la carte. Cet événement se déclenche quand la carte a terminé le chargement du contexte WebGL et de toutes les ressources nécessaires. Une fois le chargement de la carte terminé, ajoutez tout code que vous voulez exécuter à ce gestionnaire d’événements.

Les exemples ci-dessous montrent comment charger une carte de base de manière à ce qu’elle soit centrée sur les coordonnées de New York à des coordonnées (longitude : -73,985, latitude : 40,747) et est au niveau de zoom 12 dans Google Maps.

**Avant : Google Maps**

Le code suivant est un exemple d’affichage d’une carte Google centrée et zoomée sur un endroit.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map;

        function initMap() {
            map = new google.maps.Map(document.getElementById('myMap'), {
                center: new google.maps.LatLng(40.747, -73.985),
                zoom: 12
            });
        }
    </script>

    <!-- Google Maps Script Reference -->
    <script src="https://maps.googleapis.com/maps/api/js?callback=initMap&key=[Your Google Maps Key]" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

L’exécution de ce code dans un navigateur affichera une carte ressemblant à l’image suivante :

<center>

![Google Maps simple](media/migrate-google-maps-web-app/simple-google-map.png)</center>

**Après : Azure Maps**

Le code suivant montre comment charger une carte avec la même vue dans Azure Maps, ainsi qu’un contrôle de style cartographique et des boutons de zoom.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map;

        function initMap() {
            map = new atlas.Map('myMap', {
                center: [-73.985, 40.747],  //Format coordinates as longitude, latitude.
                zoom: 11,   //Subtract the zoom level by one.

                //Specify authentication information when loading the map.
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {
                //Add zoom controls to bottom right of map.
                map.controls.add(new atlas.control.ZoomControl(), {
                    position: 'bottom-right'
                });

                //Add map style control in top left corner of map.
                map.controls.add(new atlas.control.StyleControl(), {
                    position: 'top-left'
                });
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

L’exécution de ce code dans un navigateur affichera une carte ressemblant à l’image suivante :

<center>

![Azure Maps simple](media/migrate-google-maps-web-app/simple-azure-maps.png)</center>

Vous trouverez des informations détaillées sur la façon de configurer et d’utiliser le contrôle de carte Azure Maps dans une application Web [ici](how-to-use-map-control.md).

> [!NOTE]
> Contrairement à Google Maps, Azure Maps ne nécessite pas de spécification d’un niveau de zoom et de centre initial lors du chargement de la carte. Si ces informations ne sont pas fournies lors du chargement de la carte, la carte essaiera de déterminer dans quelle ville l'utilisateur se trouve et y centrera et zoomera la carte.

**Ressources supplémentaires :**

- Azure Maps fournit également des contrôles de navigation pour faire pivoter et mettre en forme la vue cartographique comme indiqué [ici](map-add-controls.md).

### <a name="localizing-the-map"></a>Localisation de la carte

Si votre audience est répartie dans plusieurs pays ou parle différentes langues, la localisation est importante.

**Avant : Google Maps**

Pour localiser les paramètres Google Maps, la langue et la région sont ajoutés à

```html
<script type="text/javascript" src=" https://maps.googleapis.com/maps/api/js?callback=initMap&key=[api_key]& language=[language_code]&region=[region_code]" async defer></script>
```

Voici un exemple de Google Maps avec la langue définie sur « fr-FR ».

<center>

![Localisation de Google Maps](media/migrate-google-maps-web-app/google-maps-localization.png)</center>

**Après : Azure Maps**

Azure Maps propose deux méthodes pour définir la langue et l’affichage régional de la carte. La première option consiste à ajouter ces informations à l’espace de noms *atlas* global, ce qui a pour effet d’attribuer ces paramètres par défaut à toutes les instances de contrôle de carte dans votre application. Le code suivant définit la langue sur le français (« fr-FR ») et l’affichage régional sur « auto » :

```javascript
atlas.setLanguage('fr-FR');
atlas.setView('auto');
```

La deuxième option consiste à transmettre ces informations dans les options de mappage lors du chargement de la carte, comme suit :

```javascript
map = new atlas.Map('myMap', {
    language: 'fr-FR',
    view: 'auto',

    authOptions: {
        authType: 'subscriptionKey',
        subscriptionKey: '<Your Azure Maps Key>'
    }
});
```

> [!NOTE]
> Avec Azure Maps, il est possible de charger plusieurs instances de carte sur la même page avec des paramètres de langue et de région différents. En outre, il est également possible de mettre à jour ces paramètres dans la carte après son chargement. Vous trouverez une liste détaillée des langues prises en charge dans Azure Maps [ici](supported-languages.md).

Voici un exemple de Azure Maps avec la langue définie sur « fr » et la région de l’utilisateur définie sur « fr-FR ».

<center>

![Localisation de Google Maps](media/migrate-google-maps-web-app/azure-maps-localization.png)</center>

### <a name="setting-the-map-view"></a>Définition de la vue cartographique

Les cartes dynamiques dans Azure et Google Maps peuvent être déplacées par programmation vers de nouveaux emplacements géographiques. Pour ce faire, appelez les fonctions appropriées en JavaScript. Les exemples montrent comment faire en sorte que la carte affiche une image aérienne satellite, centrer la carte sur un emplacement et définir le niveau de zoom sur 15 dans Google Maps. Les coordonnées d’emplacement suivantes sont utilisées : longitude : -111,0225 et latitude : 35.0272.

> [!NOTE]
> Google Maps utilise des mosaïques de 256 pixels, tandis qu'Azure Maps utilise des mosaïques de 512 pixels. Cela réduit le nombre de demandes réseau nécessaires à Azure Maps pour charger la même zone réactive que Google Maps. Toutefois, en raison de la façon dont les pyramides de mosaïques fonctionnent dans les contrôles de carte, les plus grandes vignettes dans Azure Maps signifie que pour obtenir cette même zone visualisable en tant que carte dans Google Maps, vous devez soustraire le niveau de zoom utilisé dans Google Maps d’une unité lors de l’utilisation de Azure Maps.

**Avant : Google Maps**

Le contrôle de carte Google Maps peut être déplacé programmatiquement en utilisant la méthode `setOptions`. Cette méthode vous permet de spécifier le centre de la carte et un niveau de zoom.

```javascript
map.setOptions({
    mapTypeId: google.maps.MapTypeId.SATELLITE,
    center: new google.maps.LatLng(35.0272, -111.0225),
    zoom: 15
});
```

<center>

![Vue définie de Google Maps](media/migrate-google-maps-web-app/google-maps-set-view.png)</center>

**Après : Azure Maps**

Dans Azure Maps, vous pouvez changer programmatiquement la position sur la carte en utilisant la méthode `setCamera` de la carte, ainsi que le style de la carte en utilisant la méthode `setStyle`. Les coordonnées dans Azure Maps sont au format « longitude, latitude » et la valeur de niveau de zoom est soustraite d’une unité.

```javascript
map.setCamera({
    center: [-111.0225, 35.0272],
    zoom: 14
});

map.setStyle({
    style: 'satellite'
});
```

<center>

![Vue définie d’Azure Maps](media/migrate-google-maps-web-app/azure-maps-set-view.jpeg)</center>

**Ressources supplémentaires :**

- [Choisir un style de carte](choose-map-style.md)
- [Styles de carte pris en charge](supported-map-styles.md)

### <a name="adding-a-marker"></a>Ajout d’un marqueur

Dans Azure Maps il existe plusieurs façons de faire en sorte que les données de point soient rendues sur la carte.

- **Marqueurs HTML** : effectue le rendu des points à l’aide d’éléments DOM traditionnels. Les marqueurs HTML prennent en charge le glissement.
- **Calque de symbole** : effectue le rendu des points avec une icône et/ou un texte dans le contexte WebGL.
- **Calque de bulles** : affiche les données de point sous forme de cercles sur la carte. Les rayons des cercles peuvent être mis à l’échelle en fonction des propriétés des données.

Les calques de symboles et de bulles sont rendus dans le contexte WebGL. Les deux calques peuvent afficher de très grands ensembles de points sur la carte. Ces calques requièrent que les données soient stockées dans une source de données. Les sources de données et les calques de rendu doivent être ajoutées à la carte après le déclenchement de l’événement `ready`. Les marqueurs HTML sont rendus sous la forme d’éléments DOM dans la page et n’utilisent pas de source de données. Plus le nombre d’éléments DOM d’une page est élevé, plus la page devient rapide. En cas de rendu de plus de quelques centaines de points sur une carte, il est recommandé d’utiliser à la place l’un des calques de rendu.

Les exemples suivants montrent comment ajouter un marqueur à la carte (longitude : -0,2, latitude : 51,5) avec le nombre 10 superposé sous la forme d’une étiquette.

**Avant : Google Maps**

Avec Google Maps, des marqueurs sont ajoutés à la carte à l’aide de la classe `google.maps.Marker` et en spécifiant la carte comme l’une des options.

```javascript
//Create a marker and add it to the map.
var marker = new google.maps.Marker({
    position: new google.maps.LatLng(51.5, -0.2),
    label: '10',
    map: map
});
```

<center>

![Marqueur Google Maps](media/migrate-google-maps-web-app/google-maps-marker.png)</center>

**Après : Azure Maps à l’aide de marqueurs HTML**

Dans Azure Maps, les marqueurs HTML peuvent être utilisés pour afficher un point sur la carte. Ils sont recommandés pour les applications qui doivent simplement afficher un petit nombre de points sur la carte. Pour utiliser un marqueur HTML, créez une instance de la classe `atlas.HtmlMarker`, définissez les options de texte et de position, puis ajoutez le marqueur à la carte à l’aide de la méthode `map.markers.add`.

```javascript
//Create a HTML marker and add it to the map.
map.markers.add(new atlas.HtmlMarker({
    text: '10',
    position: [-0.2, 51.5]
}));
```

<center>

![Marqueur HTML Azure Maps](media/migrate-google-maps-web-app/azure-maps-html-marker.png)</center>

**Après : Azure Maps à l’aide d’un calque de symbole**

Lorsque vous utilisez un calque de symbole, les données doivent être ajoutées à une source de données et à la source de données associée au calque. En outre, la source de données et la couche doivent être ajoutées à la carte après le déclenchement de l’événement `ready`. Pour restituer une valeur de texte unique au-dessus d’un symbole, les informations de texte doivent être stockées en tant que propriété du point de données et cette propriété doit être référencée dans l’option `textField` du calque. Cette approche représente un peu plus de travail que l’utilisation des marqueurs HTML, mais offre de nombreux avantages en termes de performances.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map, datasource;

        function initMap() {
            map = new atlas.Map('myMap', {
                center: [-0.2, 51.5],
                zoom: 9,
                
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create a data source and add it to the map.
                datasource = new atlas.source.DataSource();
                map.sources.add(datasource);

                //Create a point feature, add a property to store a label for it, and add it to the data source.
                datasource.add(new atlas.data.Feature(new atlas.data.Point([-0.2, 51.5]), {
                    label: '10'
                }));

                //Add a layer for rendering point data as symbols.
                map.layers.add(new atlas.layer.SymbolLayer(datasource, null, {
                    textOptions: {
                        //Use the label property to populate the text for the symbols.
                        textField: ['get', 'label'],
                        color: 'white',
                        offset: [0, -1]
                    }
                }));
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Calque de symbole Azure Maps](media/migrate-google-maps-web-app/azure-maps-symbol-layer.png)</center>

**Ressources supplémentaires :**

- [Créer une source de données](create-data-source-web-sdk.md)
- [Ajouter une calque de symbole](map-add-pin.md)
- [Ajouter un calque de bulles](map-add-bubble-layer.md)
- [Données de point de cluster](clustering-point-data-web-sdk.md)
- [Ajouter des marqueurs HTML](map-add-custom-html.md)
- [Utiliser des expressions de style basées sur les données](data-driven-style-expressions-web-sdk.md)
- [Options de l’icône de calque de symbole](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions?view=azure-iot-typescript-latest)
- [Option de texte du calque de symbole](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.textoptions?view=azure-iot-typescript-latest)
- [Classe de marqueur HTML](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)
- [Options du marqueur HTML](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkeroptions?view=azure-iot-typescript-latest)

### <a name="adding-a-custom-marker"></a>Ajout d’un marqueur personnalisé

Les images personnalisées peuvent être utilisées pour représenter des points sur une carte. L’image de carte suivante utilise une image personnalisée pour afficher un point sur la carte. Le point est affiché à la latitude : 51.5, longitude :  -0.2. L’ancre décale la position du marqueur afin que le point de l’icône de punaise s’aligne sur la position correcte sur la carte.

<center>

![image de punaise jaune](media/migrate-google-maps-web-app/ylw_pushpin.png)<br/>
ylw\_pushpin.png</center>

**Avant : Google Maps**

Dans Google Maps, un marqueur personnalisé est créé en spécifiant un objet `Icon` qui contient le `url` à l'image, un point `anchor` pour aligner le point de l'image punaise avec les coordonnées sur la carte. La valeur d’ancrage dans Google Maps est relative à l’angle supérieur gauche de l’image.

```javascript
var marker = new google.maps.Marker({
    position: new google.maps.LatLng(51.5, -0.2),
    icon: {
        url: 'https://azuremapscodesamples.azurewebsites.net/Common/images/icons/ylw-pushpin.png',
        anchor: new google.maps.Point(5, 30)
    },
    map: map
});
```

<center>

![Marqueur personnalisé Google Maps](media/migrate-google-maps-web-app/google-maps-custom-marker.png)</center>

**Après : Azure Maps à l’aide de marqueurs HTML**

Pour personnaliser un marqueur HTML dans Azure Maps, un `string` ou un `HTMLElement` HTML peut être passé à l’option `htmlContent` du marqueur. Dans Azure Maps, une option `anchor` est utilisée pour spécifier la position relative du marqueur par rapport à la coordonnée de position à l’aide de l’un des neuf points de référence définis. Les points de référence définis sont : « center », « top », « bottom », « left », « right », « top-left », « top-right », « bottom-left », « bottom-right ». Le contenu est ancré par défaut au centre en bas du contenu HTML. Pour faciliter la migration du code à partir de Google Maps, définissez le `anchor` sur « top-left », puis utilisez l’option `pixelOffset` avec le même décalage que celui utilisé dans Google Maps. Les décalages dans Azure Maps se déplacent dans la direction opposée de Google Maps. Par conséquent, multipliez-les par moins un.

> [!TIP]
> Ajoutez `pointer-events:none` en tant que style au contenu HTML pour désactiver le comportement de glissement par défaut dans Microsoft Edge, qui affichera une icône indésirable.

```javascript
map.markers.add(new atlas.HtmlMarker({
    htmlContent: '<img src="https://azuremapscodesamples.azurewebsites.net/Common/images/icons/ylw-pushpin.png" style="pointer-events: none;" />',
    anchor: 'top-left',
    pixelOffset: [-5, -30],
    position: [-0.2, 51.5]
}));
```

<center>

![Marqueur HTML personnalisé Azure Maps](media/migrate-google-maps-web-app/azure-maps-custom-html-marker.png)</center>

**Après : Azure Maps à l’aide d’un calque de symbole**

Dans Azure Maps, les calques de symboles prennent également en charge les images personnalisées, mais l’image doit d’abord être chargée dans les ressources de carte et recevoir un ID unique. La couche de symboles peut ensuite référencer cet ID. Le symbole peut être décalé pour s’aligner sur le point correct sur l’image à l’aide de l’option `offset` de l’icône. Dans Azure Maps, une option `anchor` est utilisée pour spécifier la position relative du symbole par rapport à la coordonnée de position à l’aide de l’un des neuf points de référence définis. Les coordonnées de position définies sont : « center », « top », « bottom », « left », « right », « top-left », « top-right », « bottom-left », « bottom-right ». Le contenu est ancré par défaut au centre en bas du contenu HTML. Pour faciliter la migration du code à partir de Google Maps, définissez le `anchor` sur « top-left », puis utilisez l’option `offset` avec le même décalage que celui utilisé dans Google Maps. Les décalages dans Azure Maps se déplacent dans la direction opposée de Google Maps. Par conséquent, multipliez-les par moins un.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map, datasource;

        function initMap() {
            map = new atlas.Map('myMap', {
                center: [-0.2, 51.5],
                zoom: 9,
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Load the custom image icon into the map resources.
                map.imageSprite.add('my-yellow-pin', 'https://azuremapscodesamples.azurewebsites.net/Common/images/icons/ylw-pushpin.png').then(function () {

                    //Create a data source and add it to the map.
                    datasource = new atlas.source.DataSource();
                    map.sources.add(datasource);

                    //Create a point and add it to the data source.
                    datasource.add(new atlas.data.Point([-0.2, 51.5]));

                    //Add a layer for rendering point data as symbols.
                    map.layers.add(new atlas.layer.SymbolLayer(datasource, null, {
                        iconOptions: {
                            //Set the image option to the id of the custom icon that was loaded into the map resources.
                            image: 'my-yellow-pin',
                            anchor: 'top-left',
                            offset: [-5, -30]
                        }
                    }));
                });
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Calque de symbole d'icône personnalisée Azure Maps](media/migrate-google-maps-web-app/azure-maps-custom-icon-symbol-layer.png)</center>

> [!TIP]
> Pour créer un rendu personnalisé avancé des points, utilisez plusieurs calques de rendu ensemble. Supposons, par exemple, que vous souhaitiez avoir plusieurs punaises qui ont la même icône sur des cercles de couleurs différentes. Au lieu de créer une série d’images pour chaque superposition de couleurs, ajoutez un calque de symboles au-dessus d’un calque de bulles et faites en sorte que les punaises référencent la même source de données. Cette approche est plus efficace que de créer une série d’images et de maintenir une série d’images différentes.

**Ressources supplémentaires :**

- [Créer une source de données](create-data-source-web-sdk.md)
- [Ajouter une calque de symbole](map-add-pin.md)
- [Ajouter des marqueurs HTML](map-add-custom-html.md)
- [Utiliser des expressions de style basées sur les données](data-driven-style-expressions-web-sdk.md)
- [Options de l’icône de calque de symbole](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions?view=azure-iot-typescript-latest)
- [Option de texte du calque de symbole](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.textoptions?view=azure-iot-typescript-latest)
- [Classe de marqueur HTML](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)
- [Options du marqueur HTML](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkeroptions?view=azure-iot-typescript-latest)

### <a name="adding-a-polyline"></a>Ajout d’une polyligne

Les polylignes sont utilisées pour représenter une ligne ou un tracé sur la carte. Les exemples suivants montrent comment créer une polyligne en pointillés sur la carte.

**Avant : Google Maps**

Dans Google Maps, la classe Polyline prend un ensemble d’options. Un tableau de coordonnées est passé dans l’option `path` de la polyligne.

```javascript
//Get the center of the map.
var center = map.getCenter();

//Define a symbol using SVG path notation, with an opacity of 1.
var lineSymbol = {
    path: 'M 0,-1 0,1',
    strokeOpacity: 1,
    scale: 4
};

//Create the polyline.
var line = new google.maps.Polyline({
    path: [
        center,
        new google.maps.LatLng(center.lat() - 0.5, center.lng() - 1),
        new google.maps.LatLng(center.lat() - 0.5, center.lng() + 1)
    ],
    strokeColor: 'red',
    strokeOpacity: 0,
    strokeWeight: 4,
    icons: [{
        icon: lineSymbol,
        offset: '0',
        repeat: '20px'
    }]
});

//Add the polyline to the map.
line.setMap(map);
```

<center>

![Polyligne Google Maps](media/migrate-google-maps-web-app/google-maps-polyline.png)</center>

**Après : Azure Maps**

Dans Azure Maps, les polylignes sont appelées objets `LineString` ou `MultiLineString`. Ces objets peuvent être ajoutés à une source de données et restitués à l’aide d’une couche de lignes.

```javascript
//Get the center of the map.
var center = map.getCamera().center;

//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Create a line and add it to the data source.
datasource.add(new atlas.data.LineString([
    center,
    [center[0] - 1, center[1] - 0.5],
    [center[0] + 1, center[1] - 0.5]
]));

//Add a layer for rendering line data.
map.layers.add(new atlas.layer.LineLayer(datasource, null, {
    strokeColor: 'red',
    strokeWidth: 4,
    strokeDashArray: [3, 3]
}));
```

<center>

![Polyligne Azure Maps](media/migrate-google-maps-web-app/azure-maps-polyline.png)</center>

**Ressources supplémentaires :**

- [Ajouter des lignes à la carte](map-add-line-layer.md)
- [Options du calque de ligne](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)
- [Utiliser des expressions de style basées sur les données](data-driven-style-expressions-web-sdk.md)

### <a name="adding-a-polygon"></a>Ajout d’un polygone

Les polygones sont utilisés pour représenter une zone sur la carte. Azure Maps et Google Maps offrent une prise en charge similaire des polygones. Les exemples suivants montrent comment créer un polygone qui forme un triangle en fonction de la coordonnée centrale de la carte.

**Avant : Google Maps**

Dans Google Maps, la classe Polygone prend un ensemble d’options. Un tableau de coordonnées est passé dans l’option `paths` du polygone.

```javascript
//Get the center of the map.
var center = map.getCenter();

//Create a polygon.
var polygon = new google.maps.Polygon({
    paths: [
        center,
        new google.maps.LatLng(center.lat() - 0.5, center.lng() - 1),
        new google.maps.LatLng(center.lat() - 0.5, center.lng() + 1),
        center
    ],
    strokeColor: 'red',
    strokeWeight: 2,
    fillColor: 'rgba(0, 255, 0, 0.5)'
});

//Add the polygon to the map
polygon.setMap(map);
```

<center>

![Polygone Google Maps](media/migrate-google-maps-web-app/google-maps-polygon.png)</center>

**Après : Azure Maps**

Dans Azure Maps, les objets `Polygon` et `MultiPolygon` peuvent être ajoutés à une source de données et restitués sur la carte à l’aide de calques. La zone d’un polygone peut être affichée dans une couche de polygones. Le contour d’un polygone peut être affiché à l’aide d’un calque de lignes.

```javascript
//Get the center of the map.
var center = map.getCamera().center;

//Create a data source and add it to the map.
datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Create a polygon and add it to the data source.
datasource.add(new atlas.data.Polygon([
    center,
    [center[0] - 1, center[1] - 0.5],
    [center[0] + 1, center[1] - 0.5],
    center
]));

//Add a polygon layer for rendering the polygon area.
map.layers.add(new atlas.layer.PolygonLayer(datasource, null, {
    fillColor: 'rgba(0, 255, 0, 0.5)'
}));

//Add a line layer for rendering the polygon outline.
map.layers.add(new atlas.layer.LineLayer(datasource, null, {
    strokeColor: 'red',
    strokeWidth: 2
}));
```

<center>

![Polygone Azure Maps](media/migrate-google-maps-web-app/azure-maps-polygon.png)</center>

**Ressources supplémentaires :**

- [Ajouter un polygone à la carte](map-add-shape.md)
- [Ajouter un cercle à la carte](map-add-shape.md#add-a-circle-to-the-map)
- [Options du calque de polygones](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest)
- [Options du calque de ligne](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)
- [Utiliser des expressions de style basées sur les données](data-driven-style-expressions-web-sdk.md)

### <a name="display-an-info-window"></a>Afficher une fenêtre d’info

Des informations supplémentaires pour une entité peuvent être affichées sur la carte en tant que classe `google.maps.InfoWindow` dans Google Maps. Dans Azure Maps, cette fonctionnalité peut être obtenue à l’aide de la classe `atlas.Popup`. Les exemples suivants ajoutent un marqueur à la carte et, en cas de clic, une fenêtre d’informations/fenêtre contextuelle s’affiche.

**Avant : Google Maps**

Avec Google Maps, une fenêtre d'informations est créée à l'aide du constructeur `google.maps.InfoWindow`.

```javascript
//Add a marker in which to display an infowindow for.
var marker = new google.maps.Marker({
    position: new google.maps.LatLng(47.6, -122.33),
    map: map
});

//Create an infowindow.
var infowindow = new google.maps.InfoWindow({
    content: '<div style="padding:5px"><b>Hello World!</b></div>'
});

//Add a click event to the marker to open the infowindow.
marker.addListener('click', function () {
    infowindow.open(map, marker);
});
```

<center>

![Fenêtre contextuelle Google Maps](media/migrate-google-maps-web-app/google-maps-popup.png)</center>

**Après : Azure Maps**

Dans Azure Maps une fenêtre contextuelle peut être utilisée pour afficher des informations supplémentaires sur un endroit. Un objet `string` ou `HTMLElement` HTML peut être passé dans l’option `content` de la fenêtre contextuelle. Si vous le souhaitez, les fenêtres contextuelles peuvent être affichées indépendamment de toute forme. Par conséquent, elles nécessitent la spécification d’une valeur `position`. Pour afficher une fenêtre contextuelle, appelez la méthode `open` et transmettez le `map` dans lequel la fenêtre contextuelle doit être affichée.

```javascript
//Add a marker to the map in which to display a popup for.
var marker = new atlas.HtmlMarker({
    position: [-122.33, 47.6]
});

//Add the marker to the map.
map.markers.add(marker);

//Create a popup.
var popup = new atlas.Popup({
    content: '<div style="padding:5px"><b>Hello World!</b></div>',
    position: [-122.33, 47.6],
    pixelOffset: [0, -35]
});

//Add a click event to the marker to open the popup.
map.events.add('click', marker, function () {
    //Open the popup
    popup.open(map);
});
```

<center>

![Fenêtre contextuelle Azure Maps](media/migrate-google-maps-web-app/azure-maps-popup.png)</center>

> [!NOTE]
> Pour effectuer la même opération avec un calque de symboles, de bulles, de lignes ou de polygones, transmettez simplement le calque au code d’événement de la carte plutôt qu’à un marqueur.

**Ressources supplémentaires :**

- [Ajouter une fenêtre contextuelle](map-add-popup.md)
- [Fenêtre contextuelle avec contenu multimédia](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Popup%20with%20Media%20Content)
- [Fenêtres contextuelles sur les formes](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Popups%20on%20Shapes)
- [Réutilisation d’une fenêtre contextuelle avec plusieurs épingles](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Reusing%20Popup%20with%20Multiple%20Pins)
- [Classe de fenêtre contextuelle](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)
- [Options de la fenêtre contextuelle](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popupoptions?view=azure-iot-typescript-latest)

### <a name="import-a-geojson-file"></a>Importer un fichier GeoJSON

Google Maps prend en charge le chargement et le stylisation dynamique des données GeoJSON via la classe `google.maps.Data`. La fonctionnalité de cette classe s'aligne beaucoup plus sur le style basé sur les données d'Azure Maps. L’une des principales différences réside dans le fait qu’avec Google Maps, vous spécifiez une fonction de rappel et la logique métier pour appliquer un style à chaque fonctionnalité traitée individuellement dans le thread d’interface utilisateur. Dans Azure Maps, les calques prennent en charge la spécification d’expressions pilotées par les données sous forme d’options de style. Ces expressions sont traitées au moment du rendu sur un thread distinct. Par conséquent, cette approche offre des performances de rendu accrues. Cet avantage est observé quand des jeux de données plus volumineux sont restitués rapidement.

Les exemples suivants chargent un flux GeoJSON de tous les séismes au cours des sept derniers jours à partir des groupes universels de sécurité (USG). Il les affiche sous forme de cercles à l’échelle sur la carte. La couleur et l’échelle de chaque cercle sont basées sur l’ampleur de chaque tremblement de terre, qui est stockée dans la propriété `"mag"` de chaque fonctionnalité du jeu de données. Si la magnitude est supérieure ou égale à 5, le cercle est rouge. S’il est supérieur ou égal à 3 mais inférieur à 5, le cercle est orange. S’il est inférieur à 3, le cercle est vert. Le rayon de chaque cercle est l’exponentiel de la grandeur multiplié par 0,1.

**Avant : Google Maps**

Dans Google Maps, une seule fonction de rappel peut être spécifiée dans la méthode `map.data.setStyle`. Cette méthode est utilisée pour appliquer la logique métier à chaque fonctionnalité chargée à partir du flux GeoJSON par le biais de la méthode `map.data.loadGeoJson`.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson';

        function initMap() {
            map = new google.maps.Map(document.getElementById('myMap'), {
                center: new google.maps.LatLng(20, -160),
                zoom: 2
            });

            //Define a callback to style each feature.
            map.data.setStyle(function (feature) {

                //Extract the 'mag' property from the feature.
                var mag = parseFloat(feature.getProperty('mag'));

                //Set the color value to 'green' by default.
                var color = 'green';

                //If the mag value is greater than 5, set the color to 'red'.
                if (mag >= 5) {
                    color = 'red';
                }
                //If the mag value is greater than 3, set the color to 'orange'.
                else if (mag >= 3) {
                    color = 'orange';
                }

                return /** @type {google.maps.Data.StyleOptions} */({
                    icon: {
                        path: google.maps.SymbolPath.CIRCLE,

                        //Scale the radius based on an exponential of the 'mag' value.
                        scale: Math.exp(mag) * 0.1,
                        fillColor: color,
                        fillOpacity: 0.75,
                        strokeWeight: 2,
                        strokeColor: 'white'
                    }
                });
            });

            //Load the data feed.
            map.data.loadGeoJson(earthquakeFeed);
        }
    </script>

    <!-- Google Maps Script Reference -->
    <script src="https://maps.googleapis.com/maps/api/js?callback=initMap&key=[Your Google Maps Key]" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![GeoJSON de Google Maps](media/migrate-google-maps-web-app/google-maps-geojson.png)</center>

**Après : Azure Maps**

GeoJSON est le type de données de base dans Azure Maps et peut facilement être importé dans une source de données à l’aide de la méthode `datasource.importFromUrl`. Un calque de bulles fournit des fonctionnalités pour le rendu des cercles mis à l’échelle en fonction des propriétés des fonctionnalités d’une source de données. Au lieu d’avoir une fonction de rappel, la logique métier est convertie en expression et transmise dans les options de style. Les expressions définissent le fonctionnement de la logique métier. Elles peuvent être passées dans un autre thread et évaluées par rapport aux données des fonctionnalités. Plusieurs sources de données et calques peuvent être ajoutés à Azure Maps, chacun avec une logique métier différente. Cette fonctionnalité permet de restituer plusieurs jeux de données sur la carte de différentes façons.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson';

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                center: [-160, 20],
                zoom: 1,

                //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create a data source and add it to the map.
                datasource = new atlas.source.DataSource();
                map.sources.add(datasource);

                //Load the earthquake data.
                datasource.importDataFromUrl(earthquakeFeed);

                //Create a layer to render the data points as scaled circles.
                map.layers.add(new atlas.layer.BubbleLayer(datasource, null, {
                    //Make the circles semi-transparent.
                    opacity: 0.75,

                    color: [
                        'case',

                        //If the mag value is greater than 5, return 'red'.
                        ['>=', ['get', 'mag'], 5],
                        'red',

                        //If the mag value is greater than 3, return 'orange'.
                        ['>=', ['get', 'mag'], 3],
                        'orange',

                        //Return 'green' as a fallback.
                        'green'
                    ],

                    //Scale the radius based on an exponential of the 'mag' value.
                    radius: ['*', ['^', ['e'], ['get', 'mag']], 0.1]
                }));
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![GeoJSON d’Azure Maps](media/migrate-google-maps-web-app/azure-maps-geojson.png)</center>

**Ressources supplémentaires :**

- [Ajouter une calque de symbole](map-add-pin.md)
- [Ajouter un calque de bulles](map-add-bubble-layer.md)
- [Données de point de cluster](clustering-point-data-web-sdk.md)
- [Utiliser des expressions de style basées sur les données](data-driven-style-expressions-web-sdk.md)

### <a name="marker-clustering"></a>Clustering de marqueurs

Lorsque vous examinez plusieurs points de données sur la carte, les points se chevauchent et la carte semble surchargée, ce qui nuit à la visibilité et à l’utilisation. Le clustering de données point peut être utilisé pour améliorer cette expérience utilisateur et améliorer les performances. Le clustering de point de données est le processus permettant de combiner des données de point proches les unes des autres et de les représenter sur une carte en tant qu’un point de données en cluster unique. Lorsque l’utilisateur effectue un zoom avant sur la carte, les clusters se décomposent pour afficher les points de données individuels qui les composent.

Dans les exemples suivants, le code charge un flux GeoJSON de données relatives aux tremblements de terre de la semaine dernière et l’ajoute à la carte. Les clusters sont rendus sous forme de cercles mis à l’échelle et colorés en fonction du nombre de points qu’ils contiennent.

> [!NOTE]
> Il existe plusieurs algorithmes différents utilisés pour le clustering de marqueurs. Google et Azure Maps utilisent des algorithmes légèrement différents. Par conséquent, la distribution de points dans les clusters peut parfois varier.

**Avant : Google Maps**

Dans Google Maps, les marqueurs peuvent être mis en cluster en chargeant dans la bibliothèque MarkerClusterer. Les icônes de cluster sont limitées aux images, qui ont les numéros 1 à 5 comme nom, et elles sont hébergées dans le même répertoire.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson';

        function initMap() {
            map = new google.maps.Map(document.getElementById('myMap'), {
                center: new google.maps.LatLng(20, -160),
                zoom: 2
            });

            //Download the GeoJSON data.
            fetch(earthquakeFeed)
                .then(function (response) {
                    return response.json();
                }).then(function (data) {
                    //Loop through the GeoJSON data and create a marker for each data point.
                    var markers = [];

                    for (var i = 0; i < data.features.length; i++) {

                        markers.push(new google.maps.Marker({
                            position: new google.maps.LatLng(data.features[i].geometry.coordinates[1], data.features[i].geometry.coordinates[0])
                        }));
                    }

                    //Create a marker clusterer instance and tell it where to find the cluster icons.
                    var markerCluster = new MarkerClusterer(map, markers,
                        { imagePath: 'https://developers.google.com/maps/documentation/javascript/examples/markerclusterer/m' });
                });
        }
    </script>

    <!-- Load the marker cluster library. -->
    <script src="https://developers.google.com/maps/documentation/javascript/examples/markerclusterer/markerclusterer.js"></script>

    <!-- Google Maps Script Reference -->
    <script src="https://maps.googleapis.com/maps/api/js?callback=initMap&key=[Your Google Maps Key]" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Clusters Google Maps](media/migrate-google-maps-web-app/google-maps-clustering.png)</center>

**Après : Azure Maps**

Dans Azure Maps, les données sont ajoutées et gérées par une source de données. Les calques se connectent aux sources de données et affichent les données qu’elles contiennent. La classe `DataSource` dans Azure Maps fournit plusieurs options de clustering.

- `cluster` : Indique à la source de données les données de point de cluster.
- `clusterRadius` : Rayon, en pixels, des points de cluster.
- `clusterMaxZoom` : Niveau de zoom maximal dans lequel le clustering se produit. Si vous effectuez un zoom avant plus grand que ce niveau, tous les points sont restitués sous forme de symboles.
- `clusterProperties` : Définit des propriétés personnalisées qui sont calculées à l’aide d’expressions sur tous les points de chaque cluster et ajoutées aux propriétés de chaque point de cluster.

Lorsque le clustering est activé, la source de données envoie des points de données en cluster et non cluster aux calques pour le rendu. La source de données est en charge de la mise en cluster de centaines de milliers de points de données. Un point de données en cluster possède les propriétés suivantes :

| Nom de la propriété             | Type    | Description   |
|---------------------------|---------|---------------|
| `cluster`                 | boolean | Indique si la fonctionnalité représente un cluster. |
| `cluster_id`              | string  | Un ID unique pour le cluster qui peut être utilisé avec les méthodes `getClusterExpansionZoom`, `getClusterChildren` et `getClusterLeaves` de DataSource. |
| `point_count`             | nombre  | Le nombre de points que contient le cluster.  |
| `point_count_abbreviated` | string  | Une chaîne qui abrège la valeur `point_count` si elle est trop longue. (par exemple, 4 000 devient 4K)  |

La classe `DataSource` possède la fonction d’assistance suivante pour accéder à des informations supplémentaires sur un cluster à l’aide de `cluster_id`.

| Méthode | Type de retour | Description |
|--------|-------------|-------------|
| `getClusterChildren(clusterId: number)` | Promise&lt;Array&lt;Feature&lt;Geometry, any&gt;\| Shape&gt;&gt; | Récupère les enfants du cluster donné sur le niveau de zoom suivant. Ces enfants peuvent être une combinaison de formes et sous-clusters. Les sous-clusters seront des fonctionnalités dont les propriétés correspondent à ClusteredProperties. |
| `getClusterExpansionZoom(clusterId: number)` | Promise&lt;number&gt; | Calcule un niveau de zoom à partir duquel le cluster commence à se développer ou à se décomposer. |
| `getClusterLeaves(clusterId: number, limit: number, offset: number)` | Promise&lt;Array&lt;Feature&lt;Geometry, any&gt;\| Shape&gt;&gt; | Récupère tous les points dans un cluster. Définissez le paramètre `limit` de manière à renvoyer un sous-ensemble des points et utilisez `offset` pour parcourir les points. |

Lors du rendu des données en cluster sur la carte, il est souvent plus facile d’utiliser au moins deux calques. L’exemple suivant utilise trois calques : un calque de bulles pour tracer des cercles de couleur à l’échelle en fonction de la taille des clusters, un calque de symboles pour afficher la taille de cluster sous forme de texte et un deuxième calque de symboles pour le rendu des points non-cluster. Il existe de nombreuses autres façons de restituer des données en cluster. Pour plus d’informations, consultez la documentation [Données de point de cluster](clustering-point-data-web-sdk.md).

Les données GeoJSON peuvent être importées directement dans Azure Maps à l’aide de la fonction `importDataFromUrl` sur la classe `DataSource`.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map, datasource;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson';

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                center: [-160, 20],
                zoom: 1,

                //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create a data source and add it to the map.
                datasource = new atlas.source.DataSource(null, {
                    //Tell the data source to cluster point data.
                    cluster: true
                });
                map.sources.add(datasource);

                //Create layers for rendering clusters, their counts and unclustered points and add the layers to the map.
                map.layers.add([
                    //Create a bubble layer for rendering clustered data points.
                    new atlas.layer.BubbleLayer(datasource, null, {
                        //Scale the size of the clustered bubble based on the number of points inthe cluster.
                        radius: [
                            'step',
                            ['get', 'point_count'],
                            20,         //Default of 20 pixel radius.
                            100, 30,    //If point_count >= 100, radius is 30 pixels.
                            750, 40     //If point_count >= 750, radius is 40 pixels.
                        ],

                        //Change the color of the cluster based on the value on the point_cluster property of the cluster.
                        color: [
                            'step',
                            ['get', 'point_count'],
                            'lime',            //Default to lime green. 
                            100, 'yellow',     //If the point_count >= 100, color is yellow.
                            750, 'red'         //If the point_count >= 750, color is red.
                        ],
                        strokeWidth: 0,
                        filter: ['has', 'point_count'] //Only rendered data points which have a point_count property, which clusters do.
                    }),

                    //Create a symbol layer to render the count of locations in a cluster.
                    new atlas.layer.SymbolLayer(datasource, null, {
                        iconOptions: {
                            image: 'none' //Hide the icon image.
                        },
                        textOptions: {
                            textField: ['get', 'point_count_abbreviated'],
                            offset: [0, 0.4]
                        }
                    }),

                    //Create a layer to render the individual locations.
                    new atlas.layer.SymbolLayer(datasource, null, {
                        filter: ['!', ['has', 'point_count']] //Filter out clustered points from this layer.
                    })
                ]);

                //Retrieve a GeoJSON data set and add it to the data source. 
                datasource.importDataFromUrl(earthquakeFeed);
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Clusters Azure Maps](media/migrate-google-maps-web-app/azure-maps-clustering.png)</center>

**Ressources supplémentaires :**

- [Ajouter une calque de symbole](map-add-pin.md)
- [Ajouter un calque de bulles](map-add-bubble-layer.md)
- [Données de point de cluster](clustering-point-data-web-sdk.md)
- [Utiliser des expressions de style basées sur les données](data-driven-style-expressions-web-sdk.md)

### <a name="add-a-heat-map"></a>Ajouter une carte thermique

Les cartes thermiques, également appelées « cartes de densité de points », sont un type de visualisation de données. Elles servent à représenter la densité de données à l’aide d’une palette de couleurs. De plus, elles sont souvent utilisées pour afficher les « points chauds » des données sur une carte. Les cartes thermiques sont idéales pour restituer des jeux de données de points volumineux.

Les exemples suivants chargent un flux GeoJSON de tous les tremblements de terre au cours du mois précédent à partir des groupes de sécurité universels (USG) et les affichent sous la forme d’une carte thermique pondérée où la propriété `"mag"` est utilisée comme poids.

**Avant : Google Maps**

Dans Google Maps, pour créer une carte thermique, la bibliothèque de « visualisation » doit être chargée en ajoutant `&libraries=visualization` à l’URL du script API. La couche de carte thermique dans Google Maps ne prend pas directement en charge les données GeoJSON. Les données doivent d’abord être téléchargées et converties en un tableau de points de données pondérés.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_month.geojson';

        function initMap() {

            var map = new google.maps.Map(document.getElementById('myMap'), {
                center: new google.maps.LatLng(20, -160),
                zoom: 2,
                mapTypeId: 'satellite'
            });

            //Download the GeoJSON data.
            fetch(url).then(function (response) {
                return response.json();
            }).then(function (res) {
                var points = getDataPoints(res);

                var heatmap = new google.maps.visualization.HeatmapLayer({
                    data: points
                });
                heatmap.setMap(map);
            });
        }

        function getDataPoints(geojson, weightProp) {
            var points = [];

            for (var i = 0; i < geojson.features.length; i++) {
                var f = geojson.features[i];

                if (f.geometry.type === 'Point') {
                    points.push({
                        location: new google.maps.LatLng(f.geometry.coordinates[1], f.geometry.coordinates[0]),
                        weight: f.properties[weightProp]
                    });
                }
            }

            return points;
        } 
    </script>

    <!-- Google Maps Script Reference -->
    <script src="https://maps.googleapis.com/maps/api/js?callback=initMap&key=[Your Google Maps Key]&libraries=visualization" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Carte thermique Google Maps](media/migrate-google-maps-web-app/google-maps-heatmap.png)</center>

**Après : Azure Maps**

Dans Azure Maps, chargez les données GeoJSON dans une source de données et connectez la source de données à une couche de carte thermique. La propriété qui sera utilisée pour le poids peut être passée dans l’option `weight` à l’aide d’une expression. Les données GeoJSON peuvent être importées directement dans Azure Maps à l’aide de la fonction `importDataFromUrl` sur la classe `DataSource`.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_month.geojson';

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                center: [-160, 20],
                zoom: 1,
                style: 'satellite',

                //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create a data source and add it to the map.
                datasource = new atlas.source.DataSource();
                map.sources.add(datasource);

                //Load the earthquake data.
                datasource.importDataFromUrl(earthquakeFeed);

                //Create a layer to render the data points as a heat map.
                map.layers.add(new atlas.layer.HeatMapLayer(datasource, null, {
                    weight: ['get', 'mag'],
                    intensity: 0.005,
                    opacity: 0.65,
                    radius: 10
                }));
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Carte thermique Azure Maps](media/migrate-google-maps-web-app/azure-maps-heatmap.png)</center>

**Ressources supplémentaires :**

- [Ajouter une couche de carte thermique](map-add-heat-map-layer.md)
- [Classe de couche de carte thermique](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.heatmaplayer?view=azure-iot-typescript-latest)
- [Options de la couche de carte thermique](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions?view=azure-iot-typescript-latest)
- [Utiliser des expressions de style basées sur les données](data-driven-style-expressions-web-sdk.md)

### <a name="overlay-a-tile-layer"></a>Superposer une couche de mosaïques

Les couches de mosaïques sont également appelées « superpositions d’images » dans Google Maps. Les couches de mosaïques vous permettent de superposer des images de grande taille qui ont été divisées en images en mosaïque plus petites qui s’alignent sur le système de mosaïque de cartes. Cette méthode est couramment utilisée pour superposer des images de grande taille ou des jeux de données volumineux.

Les exemples suivants superposent une couche de mosaïques radar météo issue de l’Iowa Environmental Mesonet de l’Iowa State University.

**Avant : Google Maps**

Dans Google Maps, les couches de mosaïques peuvent être créées à l’aide de la classe `google.maps.ImageMapType`.

```javascript
map.overlayMapTypes.insertAt(0, new google.maps.ImageMapType({
    getTileUrl: function (coord, zoom) {
        return "https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/" + zoom + "/" + coord.x + "/" + coord.y;
    },
    tileSize: new google.maps.Size(256, 256),
    opacity: 0.8
}));
```

<center>

![Couche de mosaïques Google Maps](media/migrate-google-maps-web-app/google-maps-tile-layer.png)</center>

**Après : Azure Maps**

Dans Azure Maps, une couche de mosaïques peut être ajoutée à la carte de la même façon que n’importe quel autre calque. URL mise en forme qui a des espaces réservés de zoom x, y ; `{x}`, `{y}`, `{z}` sont utilisés respectivement pour indiquer à la couche où accéder aux vignettes. Les couches de mosaïques Azure Maps prennent également en charge les espaces réservés `{quadkey}`, `{bbox-epsg-3857}` et `{subdomain}`.

> [!TIP]
> Dans Azure Maps, les couches peuvent facilement être rendues sous d’autres couches, y compris les couches de la carte de base. Il est souvent souhaitable de restituer des couches de mosaïques sous les étiquettes de carte afin qu’elles soient faciles à lire. La méthode `map.layers.add` prend un deuxième paramètre, qui est l’ID de la couche sous laquelle insérer la nouvelle couche. Pour insérer une couche de mosaïques sous les étiquettes de carte, le code suivant peut être utilisé : `map.layers.add(myTileLayer, "labels");`

```javascript
//Create a tile layer and add it to the map below the label layer.
map.layers.add(new atlas.layer.TileLayer({
    tileUrl: 'https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png',
    opacity: 0.8,
    tileSize: 256
}), 'labels');
```

<center>

![Couche de mosaïques Azure Maps](media/migrate-google-maps-web-app/azure-maps-tile-layer.png)</center>

> [!TIP]
> Les demandes de mosaïque peuvent être capturées à l’aide de l’option `transformRequest` de la carte. Cela vous permettra de modifier ou d’ajouter des en-têtes à la demande, si vous le souhaitez.

**Ressources supplémentaires :**

- [Ajouter des couches de vignettes](map-add-tile-layer.md)
- [Classe de couche de mosaïque](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest)
- [Options de la couche de mosaïques](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.tilelayeroptions?view=azure-iot-typescript-latest)

### <a name="show-traffic"></a>Afficher le trafic

Les données de trafic peuvent être superposées à la fois dans Azure et Google Maps.

**Avant : Google Maps**

Dans Google Maps, les données de trafic peuvent être superposées sur la carte à l’aide du calque de trafic.

```javascript
var trafficLayer = new google.maps.TrafficLayer();
trafficLayer.setMap(map);
```

<center>

![Trafic Google Maps](media/migrate-google-maps-web-app/google-maps-traffic.png)</center>

**Après : Azure Maps**

Azure Maps offre plusieurs options différentes pour l’affichage du trafic. Les incidents de trafic, tels que les fermetures de route et les accidents, peuvent être affichés sous forme d’icônes sur la carte. Des routes codées en couleurs représentant le flux de trafic peuvent être superposées sur la carte ; les couleurs peuvent être modifiées pour tenir compte de la limite de vitesse indiquée, d’un retard prévu normal ou d’un retard absolu. Les données d’incident dans Azure Maps sont mises à jour toutes les minutes et les données de circulation le sont toutes les deux minutes.

```javascript
map.setTraffic({
    incidents: true,
    flow: 'relative'
});
```

<center>

![Trafic Azure Maps](media/migrate-google-maps-web-app/azure-maps-traffic.png)</center>

Si vous cliquez sur l’une des icônes de trafic dans Azure Maps, des informations supplémentaires s’affichent dans une fenêtre contextuelle.

<center>

![Incident de trafic Azure Maps](media/migrate-google-maps-web-app/azure-maps-traffic-incident.png)</center>

**Ressources supplémentaires :**

- [Afficher le trafic sur la carte](map-show-traffic.md)
- [Options de superposition du trafic](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Traffic%20Overlay%20Options)

### <a name="add-a-ground-overlay"></a>Ajouter une superposition de sol

Azure et Google Maps prennent en charge la superposition des images géoréférencées sur la carte afin qu’elles se déplacent et évoluent au fur et à mesure que vous effectuez un panoramique et un zoom sur la carte. Dans Google Maps, ces superpositions sont appelées « calques de relief », alors qu’elles sont appelées « calques d’images » dans Azure Maps. Ils sont très utiles pour créer des plans d’étage, superposer d’anciennes cartes ou images à partir d’un drone.

**Avant : Google Maps**

Lors de la création d’un calque de relief dans Google Maps, vous devez spécifier l’URL de l’image à superposer et un cadre englobant auquel lier l’image sur la carte. Cet exemple superpose une image de carte de [Newark New Jersey en 1922](https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg) sur la carte.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map, historicalOverlay;

        function initMap() {
            map = new google.maps.Map(document.getElementById('myMap'), {
                center: new google.maps.LatLng(40.740, -74.18),
                zoom: 12
            });

            var imageBounds = {
                north: 40.773941,
                south: 40.712216,
                east: -74.12544,
                west: -74.22655
            };

            historicalOverlay = new google.maps.GroundOverlay(
                'https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg',
                imageBounds);
            historicalOverlay.setMap(map);
        }
    </script>

    <!-- Google Maps Script Reference -->
    <script src="https://maps.googleapis.com/maps/api/js?callback=initMap&key=[Your Google Maps Key]" async defer></script>
</head>
<body>
    <div id="myMap" style="position:relative;width:600px;height:400px;"></div>
</body>
</html>
```

L’exécution de ce code dans un navigateur affichera une carte ressemblant à l’image suivante :

<center>

![Superposition d’images Google Maps](media/migrate-google-maps-web-app/google-maps-image-overlay.png)</center>

**Après : Azure Maps**

Dans Azure Maps, les images géoréférencées peuvent être superposées à l’aide de la classe `atlas.layer.ImageLayer`. Cette classe requiert une URL vers une image et un ensemble de coordonnées pour les quatre coins de l’image. L’image doit être hébergée soit sur le même domaine, soit avec CORs activé.

> [!TIP]
> Si vous avez uniquement des informations relatives au nord, au sud, à l’est, à l’ouest et à la rotation, et non à des coordonnées pour chaque angle de l’image, vous pouvez utiliser la méthode statique [`atlas.layer.ImageLayer.getCoordinatesFromEdges`](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest#getcoordinatesfromedges-number--number--number--number--number-).

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map;

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                center: [-74.18, 40.740],
                zoom: 12,

                //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create an image layer and add it to the map.
                map.layers.add(new atlas.layer.ImageLayer({
                    url: 'newark_nj_1922.jpg',
                    coordinates: [
                        [-74.22655, 40.773941], //Top Left Corner
                        [-74.12544, 40.773941], //Top Right Corner
                        [-74.12544, 40.712216], //Bottom Right Corner
                        [-74.22655, 40.712216]  //Bottom Left Corner
                    ]
                }));
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Superposition d’images Azure Maps](media/migrate-google-maps-web-app/azure-maps-image-overlay.png)</center>

**Ressources supplémentaires :**

- [Superposer une image](map-add-image-layer.md)
- [Classe de couche image](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest)

## <a name="additional-code-samples"></a>Exemples de code supplémentaires

Voici quelques exemples de code supplémentaires liés à la migration de Google Maps :

- [Outils de dessin](map-add-drawing-toolbar.md)
- [Limiter le mappage au panoramique à deux doigts](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Limit%20Map%20to%20Two%20Finger%20Panning)
- [Limiter le zoom de la roulette de défilement](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Limit%20Scroll%20Wheel%20Zoom)
- [Créer un contrôle fullscreen](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Create%20a%20Fullscreen%20Control)

**Services :**

- [Utiliser le module de service Azure Maps](how-to-use-services-module.md)
- [Rechercher des points d’intérêt](map-search-location.md)
- [Obtenir des informations à partir d’une coordonnée (géocode inversé)](map-get-information-from-coordinate.md)
- [Afficher des directions de A à B](map-route.md)
- [Rechercher la suggestion automatique avec JQuery UI](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Search%20Autosuggest%20and%20JQuery%20UI)

## <a name="google-maps-v3-to-azure-maps-web-sdk-class-mapping"></a>Mappage de la classe SDK de Google Maps v3 vers Azure Maps

L’annexe suivante fournit une référence croisée des classes couramment utilisées dans Google Maps v3 et l’équivalence du SDK web Azure Maps.

### <a name="core-classes"></a>Classes principales

| Google Maps   | Azure Maps  |
|---------------|-------------|
| `google.maps.Map` | [atlas.Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)  |
| `google.maps.InfoWindow` | [atlas.Popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)  |
| `google.maps.InfoWindowOptions` | [atlas.PopupOptions](https://docs.microsoft.com/) |
| `google.maps.LatLng`  | [atlas.data.Position](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.position?view=azure-iot-typescript-latest)  |
| `google.maps.LatLngBounds` | [atlas.data.BoundingBox](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.boundingbox?view=azure-iot-typescript-latest) |
| `google.maps.MapOptions`  | [atlas.CameraOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.cameraoptions?view=azure-iot-typescript-latest)<br/>[atlas.CameraBoundsOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.cameraboundsoptions?view=azure-iot-typescript-latest)<br/>[atlas.ServiceOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.serviceoptions?view=azure-iot-typescript-latest)<br/>[atlas.StyleOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.styleoptions?view=azure-iot-typescript-latest)<br/>[atlas.UserInteractionOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.userinteractionoptions?view=azure-iot-typescript-latest) |
| `google.maps.Point`  | [atlas.Pixel](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.pixel?view=azure-iot-typescript-latest)   |

## <a name="overlay-classes"></a>Classes de superposition

| Google Maps  | Azure Maps  |
|--------------|-------------|
| `google.maps.Marker` | [atlas.HtmlMarker](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)<br/>[atlas.data.Point](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest)  |
| `google.maps.MarkerOptions`  | [atlas.HtmlMarkerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkeroptions?view=azure-iot-typescript-latest)<br/>[atlas.layer.SymbolLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest)<br/>[atlas.SymbolLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions?view=azure-iot-typescript-latest)<br/>[atlas.IconOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions?view=azure-iot-typescript-latest)<br/>[atlas.TextOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.textoptions?view=azure-iot-typescript-latest)<br/>[atlas.layer.BubbleLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.bubblelayer?view=azure-iot-typescript-latest)<br/>[atlas.BubbleLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.bubblelayeroptions?view=azure-iot-typescript-latest) |
| `google.maps.Polygon`  | [atlas.data.Polygon](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest)               |
| `google.maps.PolygonOptions` |[atlas.layer.PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest)<br/> [atlas.PolygonLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest)<br/> [atlas.layer.LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest)<br/> [atlas.LineLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)|
| `google.maps.Polyline` | [atlas.data.LineString](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.linestring?view=azure-iot-typescript-latest)         |
| `google.maps.PolylineOptions` | [atlas.layer.LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-maps-typescript-latest)<br/>[atlas.LineLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-maps-typescript-latest) |
| `google.maps.Circle`  | Consultez [Ajouter un cercle à la carte](map-add-shape.md#add-a-circle-to-the-map)                                     |
| `google.maps.ImageMapType`  | [atlas.TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest)         |
| `google.maps.ImageMapTypeOptions` | [atlas.TileLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.tilelayeroptions?view=azure-iot-typescript-latest) |
| `google.maps.GroundOverlay`  | [atlas.layer.ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest)<br/>[atlas.ImageLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.imagelayeroptions?view=azure-iot-typescript-latest) |

## <a name="service-classes"></a>Classes de service

Le SDK web Azure Maps comprend un module de services qui peut être chargé séparément. Ce module wrappe les services REST Azure Maps avec une API web et peut être utilisé dans des applications JavaScript, TypeScript et Node.js.

| Google Maps | Azure Maps  |
|-------------|-------------|
| `google.maps.Geocoder` | [atlas.service.SearchUrl](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchurl?view=azure-iot-typescript-latest)  |
| `google.maps.GeocoderRequest`  | [atlas.SearchAddressOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchaddressoptions?view=azure-iot-typescript-latest)<br/>[atlas.SearchAddressRevrseOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchaddressreverseoptions?view=azure-iot-typescript-latest)<br/>[atlas.SearchAddressReverseCrossStreetOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchaddressreversecrossstreetoptions?view=azure-iot-typescript-latest)<br/>[atlas.SearchAddressStructuredOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchaddressstructuredoptions?view=azure-iot-typescript-latest)<br/>[atlas.SearchAlongRouteOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchalongrouteoptions?view=azure-iot-typescript-latest)<br/>[atlas.SearchFuzzyOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchfuzzyoptions?view=azure-iot-typescript-latest)<br/>[atlas.SearchInsideGeometryOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchinsidegeometryoptions?view=azure-iot-typescript-latest)<br/>[atlas.SearchNearbyOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchnearbyoptions?view=azure-iot-typescript-latest)<br/>[atlas.SearchPOIOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchpoioptions?view=azure-iot-typescript-latest)<br/>[atlas.SearchPOICategoryOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchpoicategoryoptions?view=azure-iot-typescript-latest) |
| `google.maps.DirectionsService`  | [atlas.service.RouteUrl](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.routeurl?view=azure-iot-typescript-latest)  |
| `google.maps.DirectionsRequest`  | [atlas.CalculateRouteDirectionsOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.calculateroutedirectionsoptions?view=azure-iot-typescript-latest) |
| `google.maps.places.PlacesService` | [atlas.service.SearchUrl](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchurl?view=azure-iot-typescript-latest)  |

## <a name="libraries"></a>Bibliothèques

Les bibliothèques ajoutent des fonctionnalités supplémentaires à la carte. Un grand nombre d’entre eux se trouvent dans le kit de développement logiciel (SDK) principal de Azure Maps. Voici quelques-unes des classes équivalentes à utiliser à la place de ces bibliothèques Google Maps

| Google Maps           | Azure Maps   |
|-----------------------|--------------|
| Bibliothèque de dessins       | [Module Outils de dessin](set-drawing-options.md) |
| Bibliothèque Geometry      | [atlas.math](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.math?view=azure-iot-typescript-latest)   |
| Bibliothèque de visualisation | [Couche de carte thermique](map-add-heat-map-layer.md) |

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur le SDK web Azure Maps.

> [!div class="nextstepaction"]
> [Comment utiliser le contrôle de carte](how-to-use-map-control.md)

> [!div class="nextstepaction"]
> [Comment utiliser le module de services](how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [Comment utiliser le module Outils de dessin](set-drawing-options.md)

> [!div class="nextstepaction"]
> [Exemples de code](https://docs.microsoft.com/samples/browse/?products=azure-maps)

