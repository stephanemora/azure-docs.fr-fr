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
ms.openlocfilehash: 957ce60b8519ccb1e3287232f7a5459a56b25bb7
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/08/2019
ms.locfileid: "55960613"
---
# <a name="add-a-heat-map-layer"></a>Ajouter un calque de carte thermique

Les cartes thermiques, également appelées « cartes de densité de points », sont un type de visualisation de données utilisé pour représenter la densité de données à l’aide d’une palette de couleurs. Souvent utilisées pour afficher les « points chauds » des données sur une carte, elles sont idéales pour restituer de grands jeux de données de points.  Par exemple, le rendu de dizaines de milliers de points sous la forme de symboles prend quasiment tout l’espace sur une carte : les symboles se chevaucheraient, ce qui rendrait les informations illisibles. Toutefois, la visualisation de ce même jeu de données sous forme de carte thermique permet de voir plus facilement où les points de données sont les plus denses ainsi que la densité relative des autres zones. Il existe de nombreux scénarios dans lesquels les cartes thermiques sont utilisées. En voici quelques exemples :

* Les données de température sont couramment affichées dans une carte thermique, car celle-ci fournit une estimation de la température située entre deux points de données.
* Le rendu des données de capteurs de bruit sous forme de carte thermique permet non seulement de montrer l’intensité du bruit là où se trouve le capteur, mais également de fournir des insights sur la dissipation du bruit avec la distance. Si les zones de couverture du bruit de plusieurs capteurs se chevauchent, il est possible que la zone de chevauchement obtienne des niveaux de bruit plus élevés, qui seraient alors visibles sur la carte thermique, et ce, même si le niveau de bruit n’est pas élevé sur un site en particulier.
* La visualisation d’une trace GPS de suivi de la vitesse sous forme de carte de hauteur pondérée, où l’intensité de chaque point de données dépend de la vitesse, est un excellent moyen de voir rapidement où le véhicule a accéléré.

> [!TIP]
> Les couches par défaut de la carte thermique restituent les coordonnées de toutes les données géométriques d’une source de données. Pour limiter la couche afin qu’elle n’affiche que les fonctionnalités de géométrie de point, définissez la propriété `filter` de la couche sur `['==', '$type', 'Point']`.

## <a name="add-a-heat-map-layer"></a>Ajouter un calque de carte thermique

Pour restituer une source de données de points sous forme de carte thermique, transmettez cette source à une instance de la classe `HeatMapLayer` et ajoutez-la à la carte, comme illustré ici.

<br/>

<iframe height='500' scrolling='no' title='Couche de carte thermique simple' src='//codepen.io/azuremaps/embed/gQqdQB/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consultez l’extrait de code <a href='https://codepen.io/azuremaps/pen/gQqdQB/'>Simple Heat Map Layer</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

Dans cet exemple, chaque point de température a un rayon de 10 pixels, à tous les niveaux de zoom. Lorsque cet exemple ajoute la couche de carte thermique à la carte, il l’insère sous la couche d’étiquette. Cela permet une meilleure expérience utilisateur, car les étiquettes sont clairement visibles sur la carte thermique. Les données de cet exemple sont issues du site [USGS Earthquake Hazards Program](https://earthquake.usgs.gov/) et se composent de points qui représentent les tremblements de terre majeurs qui se sont produits au cours des 30 derniers jours.

## <a name="customizing-the-heat-map-layer"></a>Personnalisation de la couche de carte thermique

Dans l’exemple précédent, la carte thermique a été personnalisée à l’aide des options de rayon et d’opacité. La couche de carte thermique offre plusieurs options de personnalisation :

* `radius`: définit un rayon de pixels dans lequel restituer chaque point de données. Le rayon peut être défini en tant que nombre fixe ou expression. Il est possible, avec une expression, d’adapter le rayon en fonction du niveau de zoom, pour représenter une zone spatiale cohérente sur la carte (par exemple, un rayon de 5 miles).
* `color`: spécifie la façon dont la carte thermique est colorisée. Une palette de dégradés de couleurs est souvent utilisée pour les cartes thermiques. Cela dit, les palettes d’échelles de couleurs peuvent également vous être utiles si vous souhaitez donner à votre carte thermique un aspect de carte de contours. Ces palettes de couleurs permettent de définir une couleur pour toutes les valeurs comprises entre la valeur minimale et la valeur maximale de densité. Les valeurs de couleurs des cartes thermiques sont spécifiées comme expressions de valeur `heatmap-density`. La couleur de l’index 0 dans une expression de dégradé ou la couleur par défaut d’une étape définit la couleur des zones dans lesquelles il n’existe aucune donnée, qui peut être utilisée comme couleur d’arrière-plan. La plupart des utilisateurs préfèrent définir cette valeur sur une couleur noire transparente ou semi-transparente. Voici des exemples d’expressions de couleurs :

| Expression de dégradé de couleurs | Expression d’échelle de couleurs | 
|---------------------------|--------------------------|
| \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;'interpolate',<br/>&nbsp;&nbsp;&nbsp;&nbsp;\['linear'\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;\['heatmap-density'\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;0, 'transparent',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.01, 'purple',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.5, '#fb00fb',<br/>&nbsp;&nbsp;&nbsp;&nbsp;1, '#00c3ff'<br/>\] | \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;'step',<br/>&nbsp;&nbsp;&nbsp;&nbsp;\['heatmap-density'\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;'transparent',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.01, 'navy',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.25, 'navy',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.50, 'green',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.75, 'yellow',<br/>&nbsp;&nbsp;&nbsp;&nbsp;1.00, 'red'<br/>\] |   

* `opacity`: spécifie le niveau d’opacité ou de transparence de la couche de carte thermique.
* `intensity`: applique un multiplicateur à l’épaisseur de chaque point de données pour augmenter l’intensité globale de la carte thermique. Cela permet de distinguer plus facilement les différences d’épaisseur des points de données.
* `weight`: par défaut, tous les points de données ont une épaisseur de 1, ils ont donc tous la même épaisseur. L’option d’épaisseur fait office de multiplicateur et peut être définie en tant que nombre ou expression. Si un nombre est défini comme poids, par exemple 2, cela équivaut à placer deux fois chaque point de données sur la carte, doublant ainsi la densité. Le fait de définir l’option d’épaisseur sur un nombre permet d’afficher la carte thermique comme vous le feriez à l’aide d’une option d’intensité. Toutefois, si une expression est utilisée, le poids de chaque point de données peut être basé sur une métrique de ses propriétés. Prenons l’exemple de données sur des tremblements de terre : chaque point de données représente un tremblement de terre et correspond à une valeur de magnitude, qui constitue un indicateur important. Des tremblements de terre se produisent constamment, mais la plupart d’entre eux ont une magnitude faible et ne peuvent pas être ressentis. Le fait d’utiliser la valeur de magnitude dans une expression dans le but d’affecter un poids à chaque point de données permet de mieux représenter les tremblements de terre majeurs sur la carte thermique.
* En plus des options de couche de base (zoom min/max, visibilité et filtre), il existe également une option `source` qui permet de mettre à jour la source de données et une option `source-layer`, utile si la source de données est une source de vignettes vectorielles.

Voici un outil permettant de tester les options de couche de carte thermique.

<br/>

<iframe height='700' scrolling='no' title='Options de la couche de carte thermique' src='//codepen.io/azuremaps/embed/WYPaXr/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consultez l’extrait de code <a href='https://codepen.io/azuremaps/pen/WYPaXr/'>Heat Map Layer Options</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Quand vous activez l’agrégation dans la source de données, les points qui sont proches les uns des autres sont regroupés pour former un point agrégé. Le nombre de points dont est constitué chaque point agrégé peut être utilisé comme une expression d’épaisseur dans la carte thermique, et réduire ainsi considérablement le nombre de points à restituer. Le nombre de points d’un cluster est stocké dans une `point_count property` de la fonctionnalité de point, comme indiqué ci-dessous. 
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
> [Ajouter une couche de symboles](./map-add-pin.md)

