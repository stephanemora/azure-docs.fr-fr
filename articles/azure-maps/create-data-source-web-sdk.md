---
title: Créer une source de données dans Azure Maps | Microsoft Docs
description: Découvrez comment créer une source de données et l’utiliser avec le kit SDK web Azure Maps.
author: rbrundritt
ms.author: richbrun
ms.date: 08/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: codepen
ms.openlocfilehash: b83a66296d54a179a56e37de199ec900ae23a1db
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75433017"
---
# <a name="create-a-data-source"></a>Création d'une source de données

Le SDK web Azure Maps stocke des données dans des sources de données qui optimisent les données pour l’interrogation et l’affichage. Il existe actuellement deux types de sources de données :

**Source de données GeoJSON**

Une source de données GeoJSON peut charger et stocker des données localement à l’aide de la classe `DataSource`. Les données GeoJSON peuvent être créées manuellement ou à l’aide des classes d’assistance de l’espace de noms [atlas.data](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data). La classe `DataSource` fournit des fonctions pour l’importation de fichiers GeoJSON locaux ou distants. Les fichiers GeoJSON distants doivent être hébergés sur un point de terminaison CORs. La classe `DataSource` fournit des fonctionnalités pour les données de point de clustering. Les données peuvent être facilement ajoutées, supprimées et mises à jour avec la classe `DataSource`.


> [!TIP]
> Si vous souhaitez remplacer toutes les données d’un `DataSource`, si vous effectuez des appels aux fonctions `clear` puis `add`, la carte tentera de se réafficher deux fois, ce qui peut entraîner un certain délai. Utilisez plutôt la fonction `setShapes` qui supprimera et remplacera toutes les données de la source de données et déclenchera uniquement un nouveau réaffichage de la carte.

**Source de mosaïque vectorielle**

Une source de mosaïque vectorielle décrit comment accéder à un calque de mosaïques vectorielles et peut être créée à l’aide de la classe [VectorTileSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.vectortilesource). Azure Maps s’aligne sur la [spécification Mapbox Vector Tile](https://github.com/mapbox/vector-tile-spec), qui est une norme ouverte. Les calques de mosaïques vectorielles sont similaires aux calques de mosaïques. Toutefois, au lieu que chaque mosaïque soit une image raster, il s’agit d’un fichier compressé (format PBF) qui contient des données de carte vectorielle et un ou plusieurs calques qui peuvent être affichés et stylisés sur le client en fonction du style de chaque calque. Les données d’une mosaïque vectorielle contiennent des caractéristiques géographiques sous forme de points, de lignes et de polygones. Les calques de mosaïques vectorielles offrent plusieurs avantages par rapport aux calques de mosaïques raster.

 - La taille de fichier d’une mosaïque vectorielle est généralement nettement inférieure à celle d’une mosaïque raster équivalente. Par conséquent, moins de bande passante est utilisée, ce qui signifie une latence plus faible et une carte plus rapide. Cela crée une meilleure expérience utilisateur.
 - Les mosaïques vectorielles étant affichées sur le client, elles peuvent s’adapter à la résolution de l’appareil sur lequel elles sont affichées. Les cartes affichées sont ainsi bien mieux définies, avec des étiquettes parfaitement claires. 
 - La modification du style des données dans les cartes vectorielles ne nécessite pas le retéléchargement des données, car le nouveau style peut être appliqué sur le client. En revanche, la modification du style d’un calque de mosaïques raster exige généralement le chargement à partir du serveur de mosaïques auxquelles le nouveau style est appliqué.
 - Les données étant remises sous forme vectorielle, moins de traitement côté serveur est nécessaire pour préparer les données, ce qui signifie que les données les plus récentes peuvent être affichées plus rapidement.

Tous les calques qui utilisent une source vectorielle doivent spécifier une valeur `sourceLayer`. 

Une fois créées, les sources de données peuvent être ajoutées à la carte par le biais de la propriété `map.sources`, qui est un [SourceManager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.sourcemanager). Le code suivant montre comment créer un `DataSource` et l’ajouter à la carte.

```javascript
//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);
```

## <a name="connecting-a-data-source-to-a-layer"></a>Connexion d’une source de données à un calque

Les données sont affichées sur la carte à l’aide de calques de rendu. Une source de données unique peut être référencée par un ou plusieurs calques de rendu. Les calques de rendu suivants nécessitent une source de données :

- [Calque de bulles](map-add-bubble-layer.md) : affiche les données de point sous forme de cercles à l’échelle sur la carte.
- [Calque de symboles](map-add-pin.md) : affiche les données de point sous forme d’icônes et/ou de texte.
- [Calque de carte thermique](map-add-heat-map-layer.md) : affiche les données de point sous forme de carte thermique de densité.
- [Calque de lignes](map-add-shape.md) : peut être utilisé pour afficher les lignes et/ou le contour des polygones. 
- [Calque de polygones](map-add-shape.md) : remplit la zone d’un polygone avec un motif d’image ou une couleur unie.

Le code suivant montre comment créer une source de données, l’ajouter à la carte et la connecter à un calque de bulles, puis y importer des données de point GeoJSON à partir d’un emplacement distant. 

```javascript
//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Create a layer that defines how to render points in the data source and add it to the map.
map.layers.add(new atlas.layer.BubbleLayer(datasource));

//Load the earthquake data.
datasource.importDataFromUrl('https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/significant_month.geojson');
```

Il existe des calques de rendu supplémentaires qui ne se connectent pas à ces sources de données, mais chargent les données qu’elles affichent directement. 

- [Calque d’image](map-add-image-layer.md) : affiche une image unique par-dessus la carte et lie ses angles à un ensemble de coordonnées spécifiées.
- [Calque de mosaïque](map-add-tile-layer.md) : superpose un calque de mosaïques raster par-dessus la carte.

## <a name="one-data-source-with-multiple-layers"></a>Une source de données avec plusieurs calques

Plusieurs calques peuvent être connectés à une source de données unique. Cela peut sembler étrange, mais il existe de nombreux scénarios où cela peut s’avérer utile. Prenez par exemple le scénario de création d’une expérience de dessin d’un polygone. Quand un utilisateur est autorisé à dessiner un polygone, nous devons afficher la zone de remplissage du polygone à mesure que l’utilisateur ajoute des points sur la carte. L’ajout d’une ligne stylisée qui dresse le contour du polygone facilite la visualisation des bords du polygone à mesure qu’il est dessiné. Pour finir, l’ajout d’une poignée, comme un repère ou un marqueur, au-dessus de chaque position du polygone facilite la modification de chaque position individuelle. Voici une image illustrant ce scénario.

![Carte montrant plusieurs calques qui affichent des données d’une source de données unique](media/create-data-source-web-sdk/multiple-layers-one-datasource.png)

Pour accomplir ce scénario dans la plupart des plateformes de mappage, vous auriez besoin de créer un objet polygone, un objet ligne et un repère pour chaque position du polygone. Quand le polygone est modifié, il vous faudrait mettre à jour manuellement la ligne et les repères, ce qui peut rapidement devenir complexe.

Avec Azure Maps, il vous suffit de disposer d’un seul polygone dans une source de données, comme illustré dans le code ci-dessous.

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