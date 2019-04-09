---
title: Ajouter une forme avec Azure Maps | Microsoft Docs
description: Comment ajouter une forme à une carte Javascript
author: jingjing-z
ms.author: jinzh
ms.date: 10/30/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 1e550002948fc1320b8645bf1af635536d524fe6
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/08/2019
ms.locfileid: "59282387"
---
# <a name="add-a-shape-to-a-map"></a>Ajouter une forme à une carte

Cet article vous montre comment restituer des géométries sur la carte à l’aide de couches de ligne et de polygones. Le Kit de développement logiciel Azure Maps Web prend également en charge la création de géométries de cercle tel que défini dans le [schéma GeoJSON étendu](extend-geojson.md#circle). Toutes les géométries de fonctionnalité peuvent également être facilement mis à jour si encapsulé avec le [forme](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) classe.

<a id="addALine"></a>

## <a name="add-lines-to-the-map"></a>Ajouter des lignes à la carte

`LineString` et `MultiLineString` fonctionnalités sont utilisées pour représenter les chemins d’accès et les plans sur la carte.

## <a name="use-a-line"></a>Utiliser une ligne

<iframe height='500' scrolling='no' title='Ajouter une ligne à une carte' src='//codepen.io/azuremaps/embed/qomaKv/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consultez le stylet <a href='https://codepen.io/azuremaps/pen/qomaKv/'>Add a line to a map</a> (Ajouter une ligne à une carte) d’Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

Le premier bloc de code ci-dessus construit un objet carte. Vous pouvez consulter la section [Créer une carte](./map-create.md) pour obtenir des instructions.

Dans le deuxième bloc de code, un objet source de données est créé à l’aide de la classe [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest). Un objet [LineString](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.linestring?view=azure-iot-typescript-latest) est créé puis ajouté à la source de données.

Un élément [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) restitue les objets ligne encapsulés dans l’élément [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest). Le dernier bloc de code crée une couche de lignes et l’ajoute à la carte. Consultez les propriétés d’une couche de lignes dans [LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest). La source de données et la couche de lignes sont créées et ajoutées à la carte dans le [Gestionnaire d’événements](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) pour garantir que la ligne est affichée après charge complet de la carte.

### <a name="add-symbols-along-a-line"></a>Ajoutez des symboles de long d’une ligne

Cet exemple montre comment ajouter des icônes fléchées long d’une ligne sur la carte. Lors de l’utilisation une couche de symbole, l’option « emplacement » à « ligne », cela rend les symboles le long de la ligne et faire pivoter les icônes (0 degré = right).

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Afficher la flèche située le long de la ligne" src="//codepen.io/azuremaps/embed/drBJwX/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consultez le stylet <a href='https://codepen.io/azuremaps/pen/drBJwX/'>flèche afficher le long de la ligne</a> par Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="line-stroke-gradient"></a> Ajouter un dégradé de tracé à une ligne

En plus de pouvoir appliquer une couleur de contour unique à une ligne, vous pouvez également remplir une ligne avec un dégradé de couleurs pour afficher la transition à partir d’un segment de ligne à la suivante. Par exemple, des dégradés ligne utilisable pour représenter les modifications dans le temps et de distance ou de températures différentes sur une ligne d’objets connectés. Pour appliquer cette fonctionnalité à une ligne, la source de données doit avoir le `lineMetrics` option définie sur true, et ensuite une expression de dégradé de couleur peut être transmise à la `strokeColor` option de la ligne. L’expression de dégradé de tracé a pour référence le `['line-progress']` expression de données qui expose les mesures calculées ligne à l’expression.

<br/>

<iframe height="265" style="width: 100%;" scrolling="no" title="Ligne avec trait dégradé" src="//codepen.io/azuremaps/embed/wZwWJZ/?height=265&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consultez le stylet <a href='https://codepen.io/azuremaps/pen/wZwWJZ/'>ligne avec trait dégradé</a> par Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="customize-a-line-layer"></a>Personnaliser une couche de lignes

Plusieurs options de style sont disponibles pour les couches de lignes. Voici un outil qui va vous permettre de les essayer.

<br/>

<iframe height='700' scrolling='no' title='Options de la couche de lignes' src='//codepen.io/azuremaps/embed/GwLrgb/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consultez l’extrait de code <a href='https://codepen.io/azuremaps/pen/GwLrgb/'>Line Layer Options</a> (Options de la couche de lignes) Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

<a id="addAPolygon"></a>

## <a name="add-a-polygon-to-the-map"></a>Ajouter un polygone à la carte

`Polygon` et `MultiPolygon` fonctionnalités sont souvent utilisées pour représenter une zone sur une carte. 

### <a name="use-a-polygon-layer"></a>Utiliser une couche de polygones 

Une couche de polygones restitue la zone d’un polygone. 

<iframe height='500' scrolling='no' title='Ajouter un polygone à une carte ' src='//codepen.io/azuremaps/embed/yKbOvZ/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consultez le stylet <a href='https://codepen.io/azuremaps/pen/yKbOvZ/'>Add a polygon to a map</a> (Ajouter un polygone à une carte) d’Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

Dans le code ci-dessus, le premier bloc de code construit un objet de carte. Vous pouvez consulter la section [Créer une carte](./map-create.md) pour obtenir des instructions.

Dans le deuxième bloc de code, un objet source de données est créé à l’aide de la classe [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest). Un objet [Polygon](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest) est créé à partir d’un tableau de coordonnées, puis il est ajouté à la source de données. 

Un élément [PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) restitue les données encapsulées dans l’élément [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) sur la carte. Le dernier bloc de code crée une couche de polygones et l’ajoute à la carte. Consultez les propriétés d’une couche de polygones dans [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest). La source de données et la couche de polygones sont créés et ajoutés à la carte dans le [Gestionnaire d’événements](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) pour garantir que le polygone est affiché après charge complet de la carte.

### <a name="use-a-polygon-and-line-layer-together"></a>Utiliser une couche de polygones et de lignes ensemble

Une couche de lignes peut être utilisée pour restituer le contour d’un polygone. 

<iframe height='500' scrolling='no' title='Couches de polygones et de lignes pour ajouter un polygone' src='//codepen.io/azuremaps/embed/aRyEPy/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consultez la page <a href='https://codepen.io/azuremaps/pen/aRyEPy/'>Polygon and line layer to add polygon</a> (Couches de polygones et de lignes pour ajouter un polygone) d’Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) de la section Pens (Extraits de code) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

Dans le code ci-dessus, le premier bloc de code construit un objet de carte. Vous pouvez consulter la section [Créer une carte](./map-create.md) pour obtenir des instructions.

Dans le deuxième bloc de code, un objet source de données est créé à l’aide de la classe [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest). Un objet [Polygon](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest) est créé à partir d’un tableau de coordonnées, puis il est ajouté à la source de données. 

Un élément [PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) restitue les données encapsulées dans l’élément [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) sur la carte. Consultez les propriétés d’une couche de polygones dans [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest). Un élément [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) est un tableau de lignes. Consultez les propriétés d’une couche de lignes dans [LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest). Le troisième bloc de code crée des couches de polygones et de lignes.

Le dernier bloc de code ajoute les couches de polygones et de lignes à la carte. La source de données et les couches sont créés et ajoutés à la carte dans le [Gestionnaire d’événements](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) pour garantir que le polygone est affiché après charge complet de la carte.

> [!TIP]
> Couches de ligne par défaut affichera les coordonnées de polygones, ainsi que des lignes dans une source de données. Pour limiter la couche de sorte qu’elle s’affiche uniquement des LineString fonctionnalités ensemble la `filter` propriété de la couche pour `['==', ['geometry-type'], 'LineString']` ou `['any', ['==', ['geometry-type'], 'LineString'], ['==', ['geometry-type'], 'MultiLineString']]` si vous souhaitez inclure également les fonctionnalités de MultiLineString.

### <a name="fill-a-polygon-with-a-pattern"></a>Remplir un polygone avec un modèle

Outre le remplissage d’un polygone avec une couleur d’un modèle d’image peut également servir. Charger un modèle d’image dans les ressources de sprite maps image, puis référencez cette image avec le `fillPattern` propriété de la couche de polygones.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Motif de remplissage de polygones" src="//codepen.io/azuremaps/embed/JzQpYX/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consultez le stylet <a href='https://codepen.io/azuremaps/pen/JzQpYX/'>motif de remplissage de polygones</a> par Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="customize-a-polygon-layer"></a>Personnaliser une couche de polygones

La couche de polygones ne dispose que de quelques options de style. Voici un outil qui va vous permettre de les essayer.

<br/>

<iframe height='700' scrolling='no' title='LXvxpg' src='//codepen.io/azuremaps/embed/LXvxpg/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consultez l’extrait de code <a href='https://codepen.io/azuremaps/pen/LXvxpg/'>LXvxpg</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur le site <a href='https://codepen.io'>CodePen</a>.
</iframe>

<a id="addACircle"></a>

## <a name="add-a-circle-to-the-map"></a>Ajouter un cercle à la carte

Azure Maps utilise une version étendue du schéma GeoJSON qui fournit une définition des cercles, comme indiqué [ici](extend-geojson.md#circle). Un cercle peut être rendu sur la carte en créant un `Point` fonctionnalité qui a un `subType` propriété avec une valeur de `"Circle"` et un `radius` propriété qui possède un numéro qui représente le rayon en mètres. Par exemple : 

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

Le Kit de développement logiciel Azure Maps Web convertit ces `Pooint` fonctionnalités dans `Polygon` fonctionnalités en coulisses et peut être restitué sur la carte à l’aide de couches de polygones et de lignes comme indiqué ici.

<iframe height='500' scrolling='no' title='Ajouter un cercle à une carte' src='//codepen.io/azuremaps/embed/PRmzJX/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consultez le stylet <a href='https://codepen.io/azuremaps/pen/PRmzJX/'>Add a circle to a map</a> (Ajouter un cercle à une carte) d’Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

Le premier bloc de code ci-dessus construit un objet carte. Vous pouvez consulter la section [Créer une carte](./map-create.md) pour obtenir des instructions.

Dans le deuxième bloc de code, un objet source de données est créé à l’aide de la classe [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest). Un cercle est un [fonctionnalité](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) de [Point](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) et a un `subType` propriété définie sur `"Circle"` et un `radius` valeur de propriété en mètres. Quand une fonctionnalité de Point avec un `subType` de `"Circle"` est ajouté à une source de données, il est converti dans un polygone circulaire au sein de la carte.

Un élément [PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) restitue les données encapsulées dans l’élément [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) sur la carte. Le dernier bloc de code crée une couche de polygones et l’ajoute à la carte. Consultez les propriétés d’une couche de polygones dans [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest). La source de données et la couche de polygones sont créés et ajoutés à la carte dans le [Gestionnaire d’événements](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) pour garantir que le cercle est affiché après charge complet de la carte.

## <a name="make-a-geometry-easy-to-update"></a>Faciliter la mise à jour une géométrie

Un `Shape` classe encapsule une [Geometry](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.geometry?view=azure-iot-typescript-latest) ou [fonctionnalité](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) et facilite la mise à jour et leur gestion.
`new Shape(data: Feature<data.Geometry, any>)` Construit un objet de forme et l’initialise avec la fonctionnalité spécifiée.

<br/>

<iframe height='500' scrolling='no' title='Mettre à jour les propriétés des formes' src='//codepen.io/azuremaps/embed/ZqMeQY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consultez la page <a href='https://codepen.io/azuremaps/pen/ZqMeQY/'>Update shape properties</a> (Mettre à jour les propriétés des formes) d’Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) de la section Pens (Extraits de code) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

Le premier bloc de code ci-dessus construit un objet carte. Vous pouvez consulter la section [Créer une carte](./map-create.md) pour obtenir des instructions.

Un point est une [fonctionnalité](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) de la classe [Point](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest). Le deuxième bloc de code initialise la valeur du rayon de l’élément de curseur HTML, puis construit et encapsule un objet de point dans un objet de classe [Shape](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest).

Le troisième bloc de code crée une fonction qui prend la valeur de l’élément du curseur de plage HTML et modifie la valeur de rayon à l’aide de la méthode [addProperty](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) de la classe Shape.

Dans le quatrième bloc de code, un objet source de données est créé à l’aide de la classe [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest). Le point est alors ajouté à la source de données.

Un élément [PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) restitue les données encapsulées dans l’élément [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) sur la carte. Le troisième bloc de code crée une couche de polygones. Consultez les propriétés d’une couche de polygones dans [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest). La source de données, le hanlder d’événement de clic et la couche de polygones sont créés et ajoutés à la carte dans le [Gestionnaire d’événements](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) pour garantir que le point est affiché après charge complet de la carte.

## <a name="next-steps"></a>Étapes suivantes

Pour consulter plus d’exemples de code à ajouter à vos cartes, consultez les articles suivants :

> [!div class="nextstepaction"]
> [Utiliser des expressions de style piloté par les données](data-driven-style-expressions-web-sdk.md)
