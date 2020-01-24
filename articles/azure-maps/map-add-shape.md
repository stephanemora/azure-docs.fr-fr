---
title: Ajouter une couche de polygones à une carte | Microsoft Azure Maps
description: Dans cet article, vous allez apprendre à afficher un polygone et un polygone multiple sur une carte à l’aide du SDK web Microsoft Azure Maps.
author: jingjing-z
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 77d952666be12d7dea780b3aa8f094cf5f70f2d3
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/12/2020
ms.locfileid: "75911129"
---
# <a name="add-a-polygon-layer-to-the-map"></a>Ajouter une couche de polygones à la carte

Cet article vous montre comment restituer des zones des géométries de fonctionnalité `Polygon` et `MultiPolygon` sur la carte à l’aide d’une couche de polygones. Le SDK web Azure Maps prend également en charge la création de géométries Circle comme défini dans le [schéma GeoJSON étendu](extend-geojson.md#circle). Ces cercles sont transformés en polygones une fois rendus sur la carte. Toutes les géométries de fonctionnalité peuvent aussi être facilement mises à jour si elles sont encapsulées avec la classe [atlas.Shape](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest).

## <a name="use-a-polygon-layer"></a>Utiliser une couche de polygones 

Lorsqu’une couche de polygones est connectée à une source de données et chargée sur la carte, elle affiche la zone des fonctionnalités `Polygon` et `MultiPolygon`. Le code suivant montre comment créer un polygone, l’ajouter à une source de données et l’afficher avec une couche de polygones à l’aide de la classe [PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest).

```javascript
//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

//Create a rectangular polygon.
dataSource.add(new atlas.data.Feature(
    new atlas.data.Polygon([[
        [-73.98235, 40.76799],
        [-73.95785, 40.80044],
        [-73.94928, 40.7968],
        [-73.97317, 40.76437],
        [-73.98235, 40.76799]
    ]])
));

//Create and add a polygon layer to render the polygon to the map.
map.layers.add(new atlas.layer.PolygonLayer(dataSource, null,{
    fillColor: 'red',
    opacaty: 0.5
}));
```

Vous trouverez ci-dessous l’exemple de code d’exécution complet de la fonctionnalité ci-dessus.

<br/>

<iframe height='500' scrolling='no' title='Ajouter un polygone à une carte ' src='//codepen.io/azuremaps/embed/yKbOvZ/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consultez le stylet <a href='https://codepen.io/azuremaps/pen/yKbOvZ/'>Add a polygon to a map</a> (Ajouter un polygone à une carte) d’Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="use-a-polygon-and-line-layer-together"></a>Utiliser une couche de polygones et une couche de lignes ensemble

Une couche de lignes peut être utilisée pour restituer le contour des polygones. L’exemple de code suivant restitue un polygone comme l’exemple précédent, mais ajoute une couche de lignes en tant que deuxième couche connectée à la source de données.  

<iframe height='500' scrolling='no' title='Couches de polygones et de lignes pour ajouter un polygone' src='//codepen.io/azuremaps/embed/aRyEPy/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consultez la page <a href='https://codepen.io/azuremaps/pen/aRyEPy/'>Polygon and line layer to add polygon</a> (Couches de polygones et de lignes pour ajouter un polygone) d’Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) de la section Pens (Extraits de code) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="fill-a-polygon-with-a-pattern"></a>Remplir un polygone avec un modèle

Le remplissage d’un polygone peut être effectué avec une couleur, mais il est également possible d’utiliser un modèle d’image. Chargez un modèle d’image dans les ressources de sprite d’image de carte, puis référencez cette image avec la propriété `fillPattern` de la couche de polygones.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Motif de remplissage de polygones" src="//codepen.io/azuremaps/embed/JzQpYX/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consultez l’extrait de code <a href='https://codepen.io/azuremaps/pen/JzQpYX/'>Polygon fill pattern</a> (Motif de remplissage de polygones) d’Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>


> [!TIP]
> Le kit de développement logiciel web Azure Maps fournit plusieurs modèles d’image personnalisables que vous pouvez utiliser comme modèles de remplissage. Pour plus d’informations, consultez le document [Guide pratique pour utiliser des modèles d’image](how-to-use-image-templates-web-sdk.md).

## <a name="customize-a-polygon-layer"></a>Personnaliser une couche de polygones

La couche de polygones ne dispose que de quelques options de style. Voici un outil qui va vous permettre de les essayer.

<br/>

<iframe height='700' scrolling='no' title='LXvxpg' src='//codepen.io/azuremaps/embed/LXvxpg/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consultez l’extrait de code <a href='https://codepen.io/azuremaps/pen/LXvxpg/'>LXvxpg</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur le site <a href='https://codepen.io'>CodePen</a>.
</iframe>

<a id="addACircle"></a>

## <a name="add-a-circle-to-the-map"></a>Ajouter un cercle à la carte

Azure Maps utilise une version étendue du schéma GeoJSON qui fournit une définition de cercles, comme indiqué [ici](extend-geojson.md#circle). Un cercle peut être restitué sur la carte en créant une fonctionnalité `Point` qui a une propriété `subType` avec la valeur `"Circle"` et une propriété `radius` comportant un nombre qui représente le rayon en mètres. Par exemple :

```javascript
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-122.126986, 47.639754]
    },
    "properties": {
        "subType": "Circle",
        "radius": 100
    }
}  
```

Le SDK web Azure Maps convertit ces fonctionnalités `Point` en fonctionnalités `Polygon` en arrière-plan et peut être restitué sur la carte à l’aide de couches de polygones et de lignes, comme indiqué dans l’exemple de code suivant.

<br/>

<iframe height='500' scrolling='no' title='Ajouter un cercle à une carte' src='//codepen.io/azuremaps/embed/PRmzJX/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consultez le stylet <a href='https://codepen.io/azuremaps/pen/PRmzJX/'>Add a circle to a map</a> (Ajouter un cercle à une carte) d’Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="make-a-geometry-easy-to-update"></a>Faciliter la mise à jour d’une géométrie

Une classe `Shape` wrappe une géométrie ([Geometry](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.geometry?view=azure-iot-typescript-latest)) ou une fonctionnalité ([Feature](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest)), et facilite leur mise à jour et leur maintenance. Une forme peut être créée en passant une géométrie et un ensemble de propriétés, ou en passant une fonctionnalité comme indiqué dans le code suivant.

```javascript
//Creating a shape by passing in a geometry and a object containing properties.
var shape1 = new atlas.Shape(new atlas.data.Point[0,0], { myProperty: 1 });

//Creating a shape using a feature.
var shape2 = new atlas.Shape(new atlas.data.Feature(new atlas.data.Point[0,0], { myProperty: 1 });
```

L’exemple de code suivant montre comment encapsuler un objet GeoJSON de cercle avec une classe Shape et mettre à jour facilement sa propriété de rayon à l’aide d’un curseur. Lorsque la valeur de rayon change dans la forme, le rendu du cercle est automatiquement mis à jour sur la carte.

<br/>

<iframe height='500' scrolling='no' title='Mettre à jour les propriétés des formes' src='//codepen.io/azuremaps/embed/ZqMeQY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consultez la page <a href='https://codepen.io/azuremaps/pen/ZqMeQY/'>Update shape properties</a> (Mettre à jour les propriétés des formes) d’Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) de la section Pens (Extraits de code) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les classes et les méthodes utilisées dans cet article :

> [!div class="nextstepaction"]
> [Polygon](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest)

Pour consulter plus d’exemples de code à ajouter à vos cartes, consultez les articles suivants :

> [!div class="nextstepaction"]
> [Créer une source de données](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [Ajouter une fenêtre contextuelle](map-add-popup.md)

> [!div class="nextstepaction"]
> [Utiliser des expressions de style basées sur les données](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [Guide pratique pour utiliser des modèles d’image](how-to-use-image-templates-web-sdk.md)

> [!div class="nextstepaction"]
> [Ajouter une couche de lignes](map-add-line-layer.md)

Ressources supplémentaires :

> [!div class="nextstepaction"]
> [Extension de spécification GeoJSON Azure Maps](extend-geojson.md#circle)