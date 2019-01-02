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
ms.openlocfilehash: ec1343f85216171adac22f873f9be2e72bb4c282
ms.sourcegitcommit: 2bb46e5b3bcadc0a21f39072b981a3d357559191
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/05/2018
ms.locfileid: "52893063"
---
# <a name="add-a-heat-map-layer"></a>Ajouter un calque de carte thermique

Les cartes thermiques, également appelées « cartes de densité de points », sont un type de visualisation de données utilisé pour représenter la densité de données à l’aide d’une palette de couleurs. Elles sont souvent utilisées pour afficher des « points chauds » sur une carte et sont idéales pour restituer de grands jeux de données de points.  Par exemple, le rendu de dizaines de milliers de points sous la forme de symboles prendrait quasiment tout l’espace sur une carte, avec des symboles qui se chevaucheraient, rendant les informations illisibles et l’obtention d’insights difficile. Toutefois, la visualisation de ce même jeu de données sous forme de carte thermique permet de voir plus facilement où les points de données sont les plus denses ainsi que la densité relative des autres zones. Il existe de nombreux scénarios dans lesquels les cartes thermiques sont utilisées. En voici quelques exemples :

* Les données de température sont couramment affichées dans une carte thermique, car celle-ci fournit une estimation de la température située entre deux points de données.
* Le rendu des données de capteurs de bruit sous forme de carte thermique permet non seulement de montrer le caractère nocif du bruit là où se trouve le capteur, mais également de fournir des insights sur la dissipation du bruit avec la distance. Sur un site, le niveau de bruit peut ne pas être élevé, toutefois, si les zones de couverture de plusieurs capteurs se chevauchent, il est possible que la zone de chevauchement obtienne des niveaux de bruit plus élevés, qui seraient alors visibles sur la carte thermique.
* La visualisation d’une trace GPS de suivi de la vitesse sous forme de carte poids-hauteur, où l’intensité de chaque point de données est basée sur la vitesse, est un excellent moyen de voir rapidement où un véhicule a accéléré.

> [!TIP]
> Les couches de bulles par défaut affichent les coordonnées de toutes les données géométriques d’une source de données. Pour limiter la couche afin qu’elle n’affiche que les fonctionnalités de géométrie de point, définissez la propriété `filter` de la couche sur `['==', '$type', 'Point']`.

## <a name="add-a-heat-map-layer"></a>Ajouter un calque de carte thermique

Pour afficher une source de données de points sous forme de carte thermique, il vous suffit de passer votre source de données à une instance de la classe HeatMapLayer et de l’ajouter à la carte, comme illustré ici.

<br/>

<iframe height='500' scrolling='no' title='Couche de carte thermique simple' src='//codepen.io/azuremaps/embed/gQqdQB/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consultez l’extrait de code <a href='https://codepen.io/azuremaps/pen/gQqdQB/'>Simple Heat Map Layer</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

Dans cet exemple, chaque point de température a un rayon de 10 pixels, à tous les niveaux de zoom. Lorsque cet exemple ajoute la couche de carte thermique à la carte, il l’insère sous la couche d’étiquette. Cela permet une meilleure expérience utilisateur, car les étiquettes sont clairement visibles sur la carte thermique. Les données de cet exemple sont issues du site [USGS Earthquake Hazards Program](https://earthquake.usgs.gov/) et se composent de points qui représentent les tremblements de terre majeurs qui se sont produits au cours des 30 derniers jours.

## <a name="customizing-the-heat-map-layer"></a>Personnalisation de la couche de carte thermique

Dans l’exemple précédent, la carte thermique a été personnalisée à l’aide des options de rayon et d’opacité. La couche de carte thermique offre plusieurs options de personnalisation :

* `radius`: définit un rayon de pixels dans lequel restituer chaque point de données. Le rayon peut être défini en tant que nombre fixe ou expression. Le fait de définir le rayon en tant qu’expression qui définit le rayon en fonction du niveau de zoom peut avoir pour résultat des cartes thermiques dont le rayon représente une zone cohérente sur la carte.
* `color`: spécifie la façon dont la carte thermique est colorisée. Une palette de dégradés de couleurs est souvent utilisée pour les cartes thermiques. Cela dit, les palettes d’échelles de couleurs peuvent également vous être utiles si vous souhaitez donner à votre carte thermique un aspect de carte de contours. Ces palettes de couleurs permettent de définir une couleur pour toutes les valeurs comprises entre la valeur minimale et la valeur maximale de densité. Les valeurs de couleurs des cartes thermiques sont spécifiées comme expressions de valeur `heatmap-density`. La couleur de l’index 0 dans une expression de dégradé ou la couleur par défaut d’une couleur d’échelle définit la couleur d’une zone où il n’existe aucune donnée ou couleur d’arrière-plan. La plupart des utilisateurs préfèrent définir cette valeur sur une couleur noire transparente ou semi-transparente. Voici des exemples d’expressions de couleurs :

| Expression de dégradé de couleurs | Expression d’échelle de couleurs | 
|---------------------------|--------------------------|
| \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;'interpolate',<br/>&nbsp;&nbsp;&nbsp;&nbsp;\['linear'\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;\['heatmap-density'\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;0, 'transparent',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.01, 'purple',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.5, '#fb00fb',<br/>&nbsp;&nbsp;&nbsp;&nbsp;1, '#00c3ff'<br/>\] | \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;'step',<br/>&nbsp;&nbsp;&nbsp;&nbsp;\['heatmap-density'\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;'transparent',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.01, 'navy',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.25, 'navy',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.50, 'green',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.75, 'yellow',<br/>&nbsp;&nbsp;&nbsp;&nbsp;1.00, 'red'<br/>\] |   

* `opacity`: spécifie le niveau d’opacité ou de transparence de la couche de carte thermique.
* `intensity`: applique un multiplicateur à l’épaisseur de chaque point de données pour augmenter l’intensité globale de la carte thermique. Cela permet de distinguer plus facilement les différences d’épaisseur des points de données.
* `weight`: par défaut, tous les points de données ont une épaisseur de 1, ils ont donc tous la même épaisseur. L’option d’épaisseur fait office de multiplicateur et peut être définie en tant que nombre ou expression. Si un nombre est défini comme nombre, par exemple 2, cela équivaut à placer deux fois chaque point de données sur la carte, doublant ainsi leur densité. Le fait de définir l’option d’épaisseur sur un nombre permet d’afficher la carte thermique comme vous le feriez à l’aide d’une option d’intensité. Toutefois, si une expression est utilisée, l’épaisseur de chaque point de données peut être basée sur une métrique des propriétés du point. Prenons comme exemple les données relatives aux tremblements de terre. Chaque point de données représente un tremblement de terre, et l’une des métriques importantes associées aux tremblements de terre est leur magnitude. Des tremblements de terre se produisent constamment, mais la plupart d’entre eux ont une magnitude faible et ne peuvent pas être ressentis. L’utilisation d’une valeur de magnitude dans une expression dans le but d’affecter l’option d’épaisseur permet aux tremblements de terre majeurs d’être mieux représentés sur la carte thermique.
* En plus des options de couche de base (zoom min/max, visibilité et filtrage), il existe également une option `source` qui permet de mettre à jour la source de données, et l’option `source-layer` que vous pouvez utiliser si votre source de données est une source de mosaïques vectorielles.

Voici un outil permettant de tester les options de couche de carte thermique.

<br/>

<iframe height='700' scrolling='no' title='Options de la couche de carte thermique' src='//codepen.io/azuremaps/embed/WYPaXr/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consultez l’extrait de code <a href='https://codepen.io/azuremaps/pen/WYPaXr/'>Heat Map Layer Options</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Quand vous activez l’agrégation dans la source de données, les points qui sont proches les uns des autres sont regroupés pour former un point agrégé. Le nombre de points dont est constitué chaque point agrégé peut être utilisé comme une expression d’épaisseur dans la carte thermique, et réduire ainsi considérablement le nombre de points à restituer. Le nombre de points d’un point agrégé est stocké dans une propriété point_count de la fonctionnalité de point, comme indiqué ci-dessous. 
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

