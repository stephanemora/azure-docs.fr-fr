---
title: Ajouter une couche de carte thermique dans Azure Maps | Microsoft Docs
description: Comment ajouter une couche de carte thermique à une carte Javascript
author: rbrundritt
ms.author: richbrun
ms.date: 12/2/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 82a6d6b2af7df91696844b09fb7650c547cb6bd1
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/08/2019
ms.locfileid: "59258859"
---
# <a name="add-a-heat-map-layer"></a>Ajouter un calque de carte thermique

Les cartes thermiques, également appelées « cartes de densité de points », sont un type de visualisation de données utilisé pour représenter la densité de données à l’aide d’une palette de couleurs. Souvent utilisées pour afficher les « points chauds » des données sur une carte, elles sont idéales pour restituer de grands jeux de données de points.  Par exemple, le rendu des dizaines de milliers de points dans la vue cartographique en tant que symboles, couvre la plupart de la zone réactive et entraînerait le chevauchement des instantanés, ce qui complique la quantité Familiarisez-vous avec les données de nombreux symboles. Toutefois, la visualisation de ce même jeu de données sous forme de carte thermique permet de voir plus facilement où les points de données sont les plus denses ainsi que la densité relative des autres zones. Il existe de nombreux scénarios dans lesquels les cartes thermiques sont utilisées. En voici quelques exemples :

* Les données de température sont couramment affichées dans une carte thermique, car celle-ci fournit une estimation de la température située entre deux points de données.
* Le rendu des données de capteurs de bruit sous forme de carte thermique permet non seulement de montrer l’intensité du bruit là où se trouve le capteur, mais également de fournir des insights sur la dissipation du bruit avec la distance. Si les zones de couverture du bruit de plusieurs capteurs se chevauchent, il est possible que la zone de chevauchement obtienne des niveaux de bruit plus élevés, qui seraient alors visibles sur la carte thermique, et ce, même si le niveau de bruit n’est pas élevé sur un site en particulier.
* Visualisation d’un système GPS trace qui inclut la vitesse comme une carte de hauteur pondérée où l’intensité de chaque point de données est basée sur la vitesse est un excellent moyen pour voir où de l’accélération de véhicule.

> [!TIP]
> Les couches par défaut de la carte thermique restituent les coordonnées de toutes les données géométriques d’une source de données. Pour limiter la couche afin qu’elle uniquement rendus pointent les fonctionnalités de géométrie, valeur le `filter` propriété de la couche pour `['==', ['geometry-type'], 'Point']` ou `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]` si vous souhaitez inclure également les fonctionnalités MultiPoint.

## <a name="add-a-heat-map-layer"></a>Ajouter un calque de carte thermique

Pour restituer une source de données de points sous forme de carte thermique, transmettez cette source à une instance de la classe `HeatMapLayer` et ajoutez-la à la carte, comme illustré ici.

<br/>

<iframe height='500' scrolling='no' title='Couche de carte thermique simple' src='//codepen.io/azuremaps/embed/gQqdQB/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consultez l’extrait de code <a href='https://codepen.io/azuremaps/pen/gQqdQB/'>Simple Heat Map Layer</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

Dans cet exemple, chaque point de température a un rayon de 10 pixels, à tous les niveaux de zoom. Lors de l’ajout de la couche de carte thermique à la carte, cet exemple insère dessous de la couche d’étiquette pour créer une meilleure expérience utilisateur comme les étiquettes sont clairement visibles au-dessus de la carte thermique. Les données dans cet exemple provient de la [USGS tremblement de terre dangers programme](https://earthquake.usgs.gov/) et représente les tremblements de terre significatives qui se sont produites au cours des 30 derniers jours.

## <a name="customizing-the-heat-map-layer"></a>Personnalisation de la couche de carte thermique

Dans l’exemple précédent, la carte thermique a été personnalisée à l’aide des options de rayon et d’opacité. La couche de carte thermique offre plusieurs options de personnalisation :

* `radius`: définit un rayon de pixels dans lequel restituer chaque point de données. Le rayon peut être défini en tant que nombre fixe ou expression. À l’aide d’une expression, il est possible de mettre à l’échelle le rayon en fonction du niveau de zoom, qui s’affiche pour représenter une zone spatiale cohérente sur la carte (par exemple, le rayon de 5 miles).
* `color`: spécifie la façon dont la carte thermique est colorisée. Un dégradé de couleur est souvent utilisé pour les cartes thermiques et peut être obtenu avec un `interpolate` expression. À l’aide un `step` expression pour la colorisation de la carte thermique décompose la densité visuellement en plages plus semblable à une carte de style de contour ou en radar. Ces palettes de couleurs permettent de définir une couleur pour toutes les valeurs comprises entre la valeur minimale et la valeur maximale de densité. Les valeurs de couleurs des cartes thermiques sont spécifiées comme expressions de valeur `heatmap-density`. La couleur à l’index 0 dans une expression d’interpolation ou la couleur par défaut d’une expression d’étape, définit la couleur de la zone où il n’existe aucune donnée et peut être utilisé pour définir une couleur d’arrière-plan. La plupart des utilisateurs préfèrent définir cette valeur sur une couleur noire transparente ou semi-transparente. Voici des exemples d’expressions de couleurs :

| Expression couleur d’interpolation | Expression d’échelle de couleurs | 
|--------------------------------|--------------------------|
| \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;'interpolate',<br/>&nbsp;&nbsp;&nbsp;&nbsp;\['linear'\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;\['heatmap-density'\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;0, 'transparent',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.01, 'purple',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.5, '#fb00fb',<br/>&nbsp;&nbsp;&nbsp;&nbsp;1, '#00c3ff'<br/>\] | \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;'step',<br/>&nbsp;&nbsp;&nbsp;&nbsp;\['heatmap-density'\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;'transparent',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.01, 'navy',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,25, « vert »,<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,50, « jaune »,<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.75, 'red'<br/>\] | 

* `opacity`: spécifie le niveau d’opacité ou de transparence de la couche de carte thermique.
* `intensity`: Applique un multiplicateur pour le poids de chaque point de données pour augmenter l’intensité globale de la carte thermique et contribue à rendre les petites différences dans le poids de points de données deviennent plus faciles à visualiser.
* `weight`: par défaut, tous les points de données ont une épaisseur de 1, ils ont donc tous la même épaisseur. L’option d’épaisseur fait office de multiplicateur et peut être définie en tant que nombre ou expression. Si un nombre est défini comme poids, par exemple 2, cela équivaut à placer deux fois chaque point de données sur la carte, doublant ainsi la densité. Le fait de définir l’option d’épaisseur sur un nombre permet d’afficher la carte thermique comme vous le feriez à l’aide d’une option d’intensité. Toutefois, si une expression est utilisée, le poids de chaque point de données peuvent reposer sur les propriétés de chaque point de données. Prenons l’exemple de données sur des tremblements de terre : chaque point de données représente un tremblement de terre et correspond à une valeur de magnitude, qui constitue un indicateur important. Des tremblements de terre se produisent constamment, mais la plupart d’entre eux ont une magnitude faible et ne peuvent pas être ressentis. À l’aide de la valeur de magnitude dans une expression à assigner le poids à chaque données point sera permet de tremblements de terre plus significatives à être mieux représentés au sein de la carte thermique.
* En plus des options de couche de base (zoom min/max, visibilité et filtre), il existe également une option `source` qui permet de mettre à jour la source de données et une option `source-layer`, utile si la source de données est une source de vignettes vectorielles.

Voici un outil permettant de tester les options de couche de carte thermique.

<br/>

<iframe height='700' scrolling='no' title='Options de la couche de carte thermique' src='//codepen.io/azuremaps/embed/WYPaXr/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consultez l’extrait de code <a href='https://codepen.io/azuremaps/pen/WYPaXr/'>Heat Map Layer Options</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="consistent-zoomable-heat-map"></a>Carte thermique zoomable cohérent

Par défaut, le rayon des points de données restitué dans la couche de carte thermique ont un rayon de pixel fixe pour tous les niveaux de zoom. Comme la carte est agrandi ou réduite l’ensemble des agrégats de données et la couche de carte thermique est différente. Un `zoom` expression peut être utilisée pour mettre à l’échelle le rayon pour chaque niveau de zoom de telle sorte que chaque point de données couvre la même zone physique de la carte. Cela rendra la couche de carte thermique rechercher plus statique et cohérente. Chaque niveau de zoom de la carte a deux fois en tant que nombre de pixels verticalement et horizontalement en tant que le niveau de zoom précédent. Mise à l’échelle le rayon de sorte qu’elle double avec chaque niveau de zoom créera une carte thermique qui ont l’air cohérente sur tous les niveaux de zoom. Cela est possible à l’aide de la `zoom` avec une base 2 `exponential interpolation` expression, comme illustré dans l’exemple ci-dessous. Effectuer un zoom de la carte pour voir comment la carte thermique évolue avec le niveau de zoom.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Carte thermique zoomable cohérent" src="//codepen.io/azuremaps/embed/OGyMZr/?height=500&theme-id=light&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consultez le stylet <a href='https://codepen.io/azuremaps/pen/OGyMZr/'>carte thermique zoomable cohérente</a> par Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Quand vous activez l’agrégation dans la source de données, les points qui sont proches les uns des autres sont regroupés pour former un point agrégé. Le nombre de points dont est constitué chaque point agrégé peut être utilisé comme une expression d’épaisseur dans la carte thermique, et réduire ainsi considérablement le nombre de points à restituer. Le nombre de points d’un cluster est stocké dans un `point_count` propriété de la fonctionnalité de point, comme indiqué ci-dessous. 
> ```JavaScript
> var layer = new atlas.layer.HeatMapLayer(datasource, null, {
>    weight: ['get', 'point_count']
> });
> ```
> Si le rayon d’agrégation n’est constitué que de quelques pixels, la différence visuelle sera légère. Un rayon plus important va regrouper plus de points au sein d’un agrégat et améliorer les performances de la carte thermique, mais les différences seront plus visibles.

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les classes et les méthodes utilisées dans cet article :

> [!div class="nextstepaction"]
> [HeatMapLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [HeatMapLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions?view=azure-iot-typescript-latest)

Pour consulter plus d’exemples de code à ajouter à vos cartes, consultez les articles suivants :

> [!div class="nextstepaction"]
> [Ajouter un calque de symboles](./map-add-pin.md)

> [!div class="nextstepaction"]
> [Utiliser des expressions de style piloté par les données](data-driven-style-expressions-web-sdk.md)