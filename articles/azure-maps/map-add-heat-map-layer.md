---
title: Ajouter une couche de carte thermique à une carte | Microsoft Azure Maps
description: Dans cet article, vous découvrirez comment ajouter une couche de carte thermique à une carte à l’aide du SDK web Microsoft Azure Maps.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: ead30a80a6568e72f922f355916d31121b49a93b
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/12/2020
ms.locfileid: "75911220"
---
# <a name="add-a-heat-map-layer"></a>Ajouter un calque de carte thermique

Les cartes thermiques, également appelées « cartes de densité de points », sont un type de visualisation de données utilisé pour représenter la densité de données à l’aide d’une palette de couleurs. Souvent utilisées pour afficher les « points chauds » des données sur une carte, elles sont idéales pour restituer de grands jeux de données de points. 

Par exemple, le rendu de dizaines de milliers de points dans la vue cartographique en tant que symboles couvre la plus grande partie de la carte. Ainsi, de nombreux symboles se chevauchent, ce qui complique grandement l’analyse des données. Toutefois, la visualisation de ce même jeu de données sous forme de carte thermique permet de voir plus facilement où les points de données sont les plus denses ainsi que la densité relative des autres zones.

Vous pouvez utiliser des cartes thermiques dans de nombreux scénarios, notamment :

- **Données de température** : Fournit des approximations de la température entre deux points de données.
- **Données pour les capteurs de bruit** : Affiche non seulement l’intensité du bruit là où se trouve le capteur, mais fournit également des insights sur la dissipation du bruit avec la distance. Le niveau de bruit sur un site n’est peut-être pas élevé. Mais si les zones de couverture du bruit de plusieurs capteurs se chevauchent, il est possible que la zone de chevauchement obtienne des niveaux de bruit plus élevés, qui seraient alors visibles sur la carte thermique.
- **Trace GPS** : Indique la vitesse sous forme de carte à hauteur pondérée, où l’intensité de chaque point de données est basée sur la vitesse. Par exemple, cela permet de voir où un véhicule roulait trop vite.

> [!TIP]
> Les couches par défaut de la carte thermique restituent les coordonnées de toutes les données géométriques d’une source de données. Pour limiter la couche afin qu’elle n’affiche que les fonctionnalités de géométrie de point, définissez la propriété `filter` de la couche sur `['==', ['geometry-type'], 'Point']`. Si vous souhaitez également inclure des fonctionnalités MultiPoint, réglez la propriété `filter` de la couche sur `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]`.

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Heat-Maps-and-Image-Overlays-in-Azure-Maps/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## <a name="add-a-heat-map-layer"></a>Ajouter un calque de carte thermique

Pour restituer une source de données de points sous forme de carte thermique, transmettez cette source à une instance de la classe `HeatMapLayer` et ajoutez-la à la carte.

Dans le code suivant, chaque point de température a un rayon de 10 pixels, à tous les niveaux de zoom. Lors de l’ajout de la couche de carte thermique à la carte, cet exemple l’insère au-dessous de la couche d’étiquette pour créer une meilleure expérience utilisateur. Les étiquettes sont clairement visibles au-dessus de la carte thermique. Les données de cet exemple sont issues du site [USGS Earthquake Hazards Program](https://earthquake.usgs.gov/) et représentent les tremblements de terre majeurs qui se sont produits au cours des 30 derniers jours.

```javascript
//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Load a dataset of points, in this case earthquake data from the USGS.
datasource.importDataFromUrl('https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson');

//Create a heatmap and add it to the map.
map.layers.add(new atlas.layer.HeatMapLayer(datasource, null, {
  radius: 10,
  opacity: 0.8
}), 'labels');
```

Voici l’exemple complet du code précédent.

<br/>

<iframe height='500' scrolling='no' title='Couche de carte thermique simple' src='//codepen.io/azuremaps/embed/gQqdQB/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consultez l’extrait de code <a href='https://codepen.io/azuremaps/pen/gQqdQB/'>Simple Heat Map Layer</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-the-heat-map-layer"></a>Personnaliser la couche de carte thermique

Dans l’exemple précédent, la carte thermique a été personnalisée à l’aide des options de rayon et d’opacité. La couche de carte thermique offre plusieurs options de personnalisation, dont :

* `radius`: définit un rayon de pixels dans lequel restituer chaque point de données. Vous pouvez définir le rayon en tant que nombre fixe ou expression. Il est possible, avec une expression, d’adapter le rayon en fonction du niveau de zoom, pour représenter une zone spatiale cohérente sur la carte (par exemple, un rayon de 5 miles).
* `color`: spécifie la façon dont la carte thermique est colorisée. Un dégradé de couleur est une fonctionnalité courante des cartes thermiques, et vous pouvez obtenir l’effet avec une expression `interpolate`. Vous pouvez également utiliser une expression `step` pour coloriser la carte thermique décompose visuellement la densité en plages. Elle ressemble ainsi davantage à une carte de style de contours ou de radars. Ces palettes de couleurs permettent de définir une couleur pour toutes les valeurs comprises entre la valeur minimale et la valeur maximale de densité. 

  Vous spécifiez les valeurs de couleurs des cartes thermiques comme expressions de valeur `heatmap-density`. La couleur de l’index 0 dans une expression d’interpolation ou la couleur par défaut d’une expression d’étape définit la couleur des zones dans lesquelles il n’existe aucune donnée. Vous pouvez l’utiliser pour définir une couleur d’arrière-plan. Souvent, cette valeur est définie sur une couleur noire transparente ou semi-transparente. 
   
  Voici des exemples d’expressions de couleurs :

  | Expression d’interpolation des couleurs | Expression d’échelle de couleurs | 
  |--------------------------------|--------------------------|
  | \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;'interpolate',<br/>&nbsp;&nbsp;&nbsp;&nbsp;\['linear'\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;\['heatmap-density'\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;0, 'transparent',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.01, 'purple',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.5, '#fb00fb',<br/>&nbsp;&nbsp;&nbsp;&nbsp;1, '#00c3ff'<br/>\] | \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;'step',<br/>&nbsp;&nbsp;&nbsp;&nbsp;\['heatmap-density'\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;'transparent',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.01, 'navy',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.25, 'green',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.50, 'yellow',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.75, 'red'<br/>\] |   

- `opacity`: spécifie le niveau d’opacité ou de transparence de la couche de carte thermique.
- `intensity`: applique un multiplicateur à l’épaisseur de chaque point de données pour augmenter l’intensité globale de la carte thermique. Cela permet de distinguer plus facilement les différences d’épaisseur des points de données.
- `weight`: par défaut, tous les points de données ont une épaisseur de 1, et ont donc tous la même épaisseur. L’option d’épaisseur fait office de multiplicateur, et vous pouvez la définir en tant que nombre ou expression. Si un nombre est défini comme poids (par exemple 2), cela équivaut à placer deux fois chaque point de données sur la carte, doublant ainsi la densité. Le fait de définir l’option d’épaisseur sur un nombre permet d’afficher la carte thermique comme vous le feriez à l’aide d’une option d’intensité. 

  Toutefois, si vous utilisez une expression, le poids de chaque point de données peut être basé sur les propriétés de chaque point de données. Par exemple, supposons que chaque point de données représente un tremblement de terre. et correspond à une valeur de magnitude, qui constitue un indicateur important. Des tremblements de terre se produisent constamment, mais la plupart d’entre eux ont une magnitude faible et ne peuvent pas être ressentis. Le fait d’utiliser la valeur de magnitude dans une expression dans le but d’affecter un poids à chaque point de données vous donne une meilleure représentation des tremblements de terre majeurs sur la carte thermique.
- `source` et `source-layer` : vous permettent de mettre à jour la source de données.

Voici un outil permettant de tester les options de couche de carte thermique.

<br/>

<iframe height='700' scrolling='no' title='Options de la couche de carte thermique' src='//codepen.io/azuremaps/embed/WYPaXr/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consultez l’extrait de code <a href='https://codepen.io/azuremaps/pen/WYPaXr/'>Heat Map Layer Options</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="consistent-zoomable-heat-map"></a>Carte thermique homogène pouvant faire l’objet d’un zoom

Par défaut, les rayons des points de données restitués dans la couche de carte thermique ont un rayon de pixels fixe pour tous les niveaux de zoom. Lorsque vous zoomez sur la carte, les données sont agrégées, et la couche de carte thermique change d’aspect. 

Utilisez une expression `zoom` pour mettre à l’échelle le rayon de chaque niveau de zoom de sorte que chaque point de données couvre la même zone physique de la carte. Cette opération fait paraître la couche de carte thermique plus statique et plus cohérente. Chaque niveau de zoom de la carte a deux fois plus de pixels verticalement et horizontalement que le niveau de zoom précédent. 

La mise à l’échelle du rayon de sorte qu’il soit multiplié par deux avec chaque niveau de zoom crée une carte thermique qui paraît cohérente sur tous les niveaux de zoom. Pour ce faire, utilisez le `zoom` avec une expression de base 2 `exponential interpolation`, comme illustré dans l’exemple suivant. Effectuez un zoom sur la carte pour voir comment la carte thermique évolue avec le niveau de zoom.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Carte thermique homogène pouvant faire l’objet d’un zoom" src="//codepen.io/azuremaps/embed/OGyMZr/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consultez <a href='https://codepen.io/azuremaps/pen/OGyMZr/'>Consistent zoomable heat map</a> (Carte thermique pouvant faire l’objet d’un zoom) d’Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Lorsque vous activez l’agrégation dans la source de données, les points qui sont proches les uns des autres sont regroupés pour former un point agrégé. Vous pouvez utiliser le nombre de points dont est constitué chaque point comme une expression d’épaisseur dans la carte thermique, et réduire ainsi considérablement le nombre de points à restituer. Le nombre de points d’un cluster est stocké dans une propriété `point_count` de la fonctionnalité de point : 
> ```JavaScript
> var layer = new atlas.layer.HeatMapLayer(datasource, null, {
>    weight: ['get', 'point_count']
> });
> ```
> Si le rayon d’agrégation n’est constitué que de quelques pixels, la différence visuelle sera légère. Un plus grand rayon regroupe plus de points dans chaque cluster et améliore les performances de la carte thermique.

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les classes et les méthodes utilisées dans cet article :

> [!div class="nextstepaction"]
> [HeatMapLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [HeatMapLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions?view=azure-iot-typescript-latest)

Pour consulter plus d’exemples de code à ajouter à vos cartes, consultez les articles suivants :

> [!div class="nextstepaction"]
> [Créer une source de données](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [Utiliser des expressions de style basées sur les données](data-driven-style-expressions-web-sdk.md)
