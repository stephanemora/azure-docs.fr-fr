---
title: Créer une source de données pour une carte | Microsoft Azure Maps
description: Dans cet article, vous allez découvrir comment créer une source de données et comment l’ajouter à une carte avec le SDK web Microsoft Azure Maps.
author: rbrundritt
ms.author: richbrun
ms.date: 08/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: codepen
ms.openlocfilehash: 1675d63fd3a65beda46042f4a78535bb4e066e62
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77190228"
---
# <a name="create-a-data-source"></a>Création d'une source de données

Le SDK web Azure Maps stocke les données dans des sources de données. L’utilisation de sources de données optimise les opérations sur les données pour l’interrogation et le rendu. Il existe actuellement deux types de sources de données :

**Source de données GeoJSON**

Une source de données GeoJSON charge et stocke des données localement en utilisant la classe `DataSource`. Les données GeoJSON peuvent être créées manuellement ou à l’aide des classes d’assistance de l’espace de noms [atlas.data](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data). La classe `DataSource` fournit des fonctions pour l’importation de fichiers GeoJSON locaux ou distants. Les fichiers GeoJSON distants doivent être hébergés sur un point de terminaison CORs. La classe `DataSource` fournit des fonctionnalités pour les données de point de clustering. Les données peuvent aussi être facilement ajoutées, supprimées et mises à jour avec la classe `DataSource`.


> [!TIP]
> Supposons que vous voulez remplacer toutes les données d’une `DataSource`. Si vous faites des appels aux fonctions `clear` puis `add`, la carte va se réafficher deux fois, ce qui peut entraîner un certain délai. Utilisez à la place la fonction `setShapes`, qui va supprimer et remplacer toutes les données de la source de données, et déclencher un seul réaffichage de la carte.

**Source de mosaïque vectorielle**

Une source de vignettes vectorielles décrit comment accéder à un calque de vignettes vectorielles. Utilisez la classe [VectorTileSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.vectortilesource) pour instancier une source de vignettes vectorielles. Les calques de vignettes vectorielles sont similaires aux calques de vignettes, mais ils ne sont pas identiques. Un calque de vignettes est une image raster. Les calques de vignettes vectorielles sont un fichier compressé au format PBF. Ce fichier compressé contient les données d’une carte vectorielle et un ou plusieurs calques. Le fichier peut être rendu et stylisé sur le client, en fonction du style de chaque calque. Les données d’une mosaïque vectorielle contiennent des caractéristiques géographiques sous forme de points, de lignes et de polygones. Il y a plusieurs avantages à utiliser les calques de vignettes vectorielles au lieu des calques de vignettes raster :

 - La taille de fichier d’une mosaïque vectorielle est généralement nettement inférieure à celle d’une mosaïque raster équivalente. Ainsi, la bande passante utilisée est inférieure. Cela signifie une latence plus faible, une carte plus rapide et une meilleure expérience utilisateur.
 - Les vignettes vectorielles étant rendues sur le client, elles peuvent s’adapter à la résolution de l’appareil où elles sont affichées. Par conséquent, les cartes rendues apparaissent mieux définies, avec des étiquettes bien nettes.
 - La modification du style des données dans les cartes vectorielles ne nécessite pas le retéléchargement des données, car le nouveau style peut être appliqué sur le client. En revanche, la modification du style d’un calque de vignettes raster nécessite généralement le chargement de vignettes à partir du serveur, puis l’application du nouveau style.
 - Les données étant fournies sous une forme vectorielle, moins de traitement côté serveur est nécessaire pour préparer les données. Ainsi, les données plus récentes peuvent être rendues disponibles plus rapidement.

Tous les calques qui utilisent une source vectorielle doivent spécifier une valeur `sourceLayer`.

Une fois créées, les sources de données peuvent être ajoutées à la carte par le biais de la propriété `map.sources`, qui est un [SourceManager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.sourcemanager). Le code suivant montre comment créer un `DataSource` et l’ajouter à la carte.

```javascript
//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);
```

Azure Maps est conforme à la [spécification Mapbox Vector Tile](https://github.com/mapbox/vector-tile-spec), qui est un standard ouvert.

## <a name="connecting-a-data-source-to-a-layer"></a>Connexion d’une source de données à un calque

Les données sont affichées sur la carte à l’aide de calques de rendu. Une source de données unique peut être référencée par un ou plusieurs calques de rendu. Les calques de rendu suivants nécessitent une source de données :

- [Calque de bulles](map-add-bubble-layer.md) : affiche les données de point sous forme de cercles à l’échelle sur la carte.
- [Calque de symboles](map-add-pin.md) : affiche les données de point sous forme d’icônes ou de texte.
- [Calque de carte thermique](map-add-heat-map-layer.md) : affiche les données de point sous forme de carte thermique de densité.
- [Calque de lignes](map-add-shape.md) : pour une ligne ou le contour de polygones. 
- [Calque de polygones](map-add-shape.md) : remplit la zone d’un polygone avec un motif d’image ou une couleur unie.

Le code suivant montre comment créer une source de données, l’ajouter à la carte et la connecter à un calque de bulles. Ensuite, il importe dans la source de données des données de points GeoJSON à partir d’un emplacement distant. 

```javascript
//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Create a layer that defines how to render points in the data source and add it to the map.
map.layers.add(new atlas.layer.BubbleLayer(datasource));

//Load the earthquake data.
datasource.importDataFromUrl('https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/significant_month.geojson');
```

Il existe des calques de rendu supplémentaires qui ne se connectent pas à ces sources de données, mais qui chargent directement les données pour les rendre. 

- [Calque d’image](map-add-image-layer.md) : affiche une image unique par-dessus la carte et lie ses angles à un ensemble de coordonnées spécifiées.
- [Calque de mosaïque](map-add-tile-layer.md) : superpose un calque de mosaïques raster par-dessus la carte.

## <a name="one-data-source-with-multiple-layers"></a>Une source de données avec plusieurs calques

Plusieurs calques peuvent être connectés à une source de données unique. Cette option est utile dans de nombreux scénarios. Par exemple, considérez le scénario où un utilisateur dessine un polygone. Nous devons afficher et remplir la surface du polygone quand l’utilisateur ajoute des points à la carte. L’ajout d’une ligne stylisée pour le contour du polygone facilite la visualisation des bords du polygone à mesure que l’utilisateur le dessine. Pour modifier facilement une position individuelle dans le polygone, nous pouvons ajouter une poignée, comme une épingle ou un marqueur, au-dessus de chaque position.

![Carte montrant plusieurs calques qui affichent des données d’une source de données unique](media/create-data-source-web-sdk/multiple-layers-one-datasource.png)

Dans la plupart des plateformes de cartographie, vous avez besoin d’un objet polygone, d’un objet ligne et d’une épingle pour chaque position dans le polygone. Quand le polygone est modifié, vous devez alors mettre à jour manuellement la ligne et les épingles, ce qui peut devenir rapidement complexe.

Avec Azure Maps, tout ce dont vous avez besoin est un seul polygone dans une source de données, comme le montre le code ci-dessous.

```javascript
//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

//Create a polygon and add it to the data source.
dataSource.add(new atlas.data.Polygon([[[/* Coordinates for polygon */]]]));

//Create a polygon layer to render the filled in area of the polygon.
var polygonLayer = new atlas.layer.PolygonLayer(dataSource, 'myPolygonLayer', {
     fillColor: 'rgba(255,165,0,0.2)'
});

//Create a line layer for greater control of rendering the outline of the polygon.
var lineLayer = new atlas.layer.LineLayer(dataSource, 'myLineLayer', {
     color: 'orange',
     width: 2
});

//Create a bubble layer to render the vertices of the polygon as scaled circles.
var bubbleLayer = new atlas.layer.BubbleLayer(dataSource, 'myBubbleLayer', {
     color: 'orange',
     radius: 5,
     outlineColor: 'white',
     outlineWidth: 2
});

//Add all layers to the map.
map.layers.add([polygonLayer, lineLayer, bubbleLayer]);
```

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les classes et les méthodes utilisées dans cet article :

> [!div class="nextstepaction"]
> [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [DataSourceOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.datasourceoptions?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [VectorTileSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.vectortilesource?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [VectorTileSourceOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.vectortilesourceoptions?view=azure-maps-typescript-latest)

Pour obtenir plus d’exemples de code à ajouter à vos cartes, consultez les articles suivants :

> [!div class="nextstepaction"]
> [Ajouter une fenêtre contextuelle](map-add-popup.md)

> [!div class="nextstepaction"]
> [Utiliser des expressions de style basées sur les données](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [Ajouter une couche de symboles](map-add-pin.md)

> [!div class="nextstepaction"]
> [Ajouter une couche de bulles](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Ajouter une couche de lignes](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Ajouter une couche de polygones](map-add-shape.md)

> [!div class="nextstepaction"]
> [Ajouter une carte thermique](map-add-heat-map-layer.md)

> [!div class="nextstepaction"]
> [Exemples de code](https://docs.microsoft.com/samples/browse/?products=azure-maps)