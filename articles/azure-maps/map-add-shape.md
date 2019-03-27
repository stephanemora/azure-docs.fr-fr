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
ms.openlocfilehash: 7598bbc879351752580247e46bc986ee84fa0d56
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2019
ms.locfileid: "58497242"
---
# <a name="add-a-shape-to-a-map"></a>Ajouter une forme à une carte

Cet article vous montre comment ajouter une [forme](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) à la carte et mettre à jour les propriétés d’une forme existante sur la carte.

<a id="addALine"></a>

## <a name="add-a-line"></a>Ajouter une ligne

<iframe height='500' scrolling='no' title='Ajouter une ligne à une carte' src='//codepen.io/azuremaps/embed/qomaKv/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consultez le stylet <a href='https://codepen.io/azuremaps/pen/qomaKv/'>Add a line to a map</a> (Ajouter une ligne à une carte) d’Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

Le premier bloc de code ci-dessus construit un objet carte. Vous pouvez consulter la section [Créer une carte](./map-create.md) pour obtenir des instructions.

Dans le deuxième bloc de code, un objet source de données est créé à l’aide de la classe [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest). Un objet [LineString](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.linestring?view=azure-iot-typescript-latest) est créé puis ajouté à la source de données.

Un élément [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) restitue les objets ligne encapsulés dans l’élément [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest). Le dernier bloc de code crée une couche de lignes et l’ajoute à la carte. Consultez les propriétés d’une couche de lignes dans [LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest). La source de données et la couche de lignes sont créées et ajoutées à la carte dans la fonction de [détecteur d’événements](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) pour garantir que la ligne s’affiche après le chargement complet de la carte.

## <a name="customize-a-line-layer"></a>Personnaliser une couche de lignes

Plusieurs options de style sont disponibles pour les couches de lignes. Voici un outil qui va vous permettre de les essayer.

<br/>

<iframe height='700' scrolling='no' title='Options de la couche de lignes' src='//codepen.io/azuremaps/embed/GwLrgb/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consultez l’extrait de code <a href='https://codepen.io/azuremaps/pen/GwLrgb/'>Line Layer Options</a> (Options de la couche de lignes) Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

<a id="addACircle"></a>

## <a name="add-a-circle"></a>Ajouter un cercle

<iframe height='500' scrolling='no' title='Ajouter un cercle à une carte' src='//codepen.io/azuremaps/embed/PRmzJX/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consultez le stylet <a href='https://codepen.io/azuremaps/pen/PRmzJX/'>Add a circle to a map</a> (Ajouter un cercle à une carte) d’Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

Le premier bloc de code ci-dessus construit un objet carte. Vous pouvez consulter la section [Créer une carte](./map-create.md) pour obtenir des instructions.

Dans le deuxième bloc de code, un objet source de données est créé à l’aide de la classe [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest). Un cercle est une [fonctionnalité](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) de [point](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) qui comprend une propriété `subType` définie sur « cercle » et un compteur de valeurs pour la propriété `radius`. Lorsqu’une fonctionnalité de point ayant un cercle comme sous-type est ajoutée à une source de données, elle le convertit en un polygone circulaire sur la carte.

Un élément [PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) restitue les données encapsulées dans l’élément [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) sur la carte. Le dernier bloc de code crée une couche de polygones et l’ajoute à la carte. Consultez les propriétés d’une couche de polygones dans [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest). La source de données et la couche de polygones sont créées et ajoutées à la carte dans la fonction de [détecteur d’événements](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) pour garantir que le cercle s’affiche après le chargement complet de la carte.

<a id="addAPolygon"></a>

## <a name="add-a-polygon"></a>Ajouter un polygone

Deux méthodes permettant d’ajouter un polygone à la carte. Les deux sont expliquées dans les exemples suivants.

### <a name="use-polygon-layer"></a>Utiliser une couche de polygones 

<iframe height='500' scrolling='no' title='Ajouter un polygone à une carte ' src='//codepen.io/azuremaps/embed/yKbOvZ/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consultez le stylet <a href='https://codepen.io/azuremaps/pen/yKbOvZ/'>Add a polygon to a map</a> (Ajouter un polygone à une carte) d’Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

Dans le code ci-dessus, le premier bloc de code construit un objet de carte. Vous pouvez consulter la section [Créer une carte](./map-create.md) pour obtenir des instructions.

Dans le deuxième bloc de code, un objet source de données est créé à l’aide de la classe [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest). Un objet [Polygon](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest) est créé à partir d’un tableau de coordonnées, puis il est ajouté à la source de données. 

Un élément [PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) restitue les données encapsulées dans l’élément [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) sur la carte. Le dernier bloc de code crée une couche de polygones et l’ajoute à la carte. Consultez les propriétés d’une couche de polygones dans [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest). La source de données et la couche de polygones sont créées et ajoutées à la carte dans la fonction de [détecteur d’événements](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) pour garantir que le polygone s’affiche après le chargement complet de la carte.

### <a name="use-polygon-and-line-layer"></a>Utiliser des couches de polygones et de lignes

<iframe height='500' scrolling='no' title='Couches de polygones et de lignes pour ajouter un polygone' src='//codepen.io/azuremaps/embed/aRyEPy/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consultez la page <a href='https://codepen.io/azuremaps/pen/aRyEPy/'>Polygon and line layer to add polygon</a> (Couches de polygones et de lignes pour ajouter un polygone) d’Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) de la section Pens (Extraits de code) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

Dans le code ci-dessus, le premier bloc de code construit un objet de carte. Vous pouvez consulter la section [Créer une carte](./map-create.md) pour obtenir des instructions.

Dans le deuxième bloc de code, un objet source de données est créé à l’aide de la classe [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest). Un objet [Polygon](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest) est créé à partir d’un tableau de coordonnées, puis il est ajouté à la source de données. 

Un élément [PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) restitue les données encapsulées dans l’élément [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) sur la carte. Consultez les propriétés d’une couche de polygones dans [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest). Un élément [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) est un tableau de lignes. Consultez les propriétés d’une couche de lignes dans [LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest). Le troisième bloc de code crée des couches de polygones et de lignes.

Le dernier bloc de code ajoute les couches de polygones et de lignes à la carte. La source de données et les couches sont créées et ajoutées à la carte dans la fonction de [détecteur d’événements](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) pour garantir que le polygone s’affiche après le chargement complet de la carte.

## <a name="customize-a-polygon-layer"></a>Personnaliser une couche de polygones

La couche de polygones ne dispose que de quelques options de style. Voici un outil qui va vous permettre de les essayer.

<br/>

<iframe height='700' scrolling='no' title='LXvxpg' src='//codepen.io/azuremaps/embed/LXvxpg/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consultez l’extrait de code <a href='https://codepen.io/azuremaps/pen/LXvxpg/'>LXvxpg</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur le site <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="update-a-shape"></a>Mettre à jour une forme

Une classe Shape encapsule une [géométrie](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.geometry?view=azure-iot-typescript-latest) ou une [fonctionnalité](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) et facilite sa mise à jour et sa gestion.
`new Shape(data: Feature<data.Geometry, any>)` construit un objet forme et l’initialise avec la fonctionnalité spécifiée.

<br/>

<iframe height='500' scrolling='no' title='Mettre à jour les propriétés des formes' src='//codepen.io/azuremaps/embed/ZqMeQY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consultez la page <a href='https://codepen.io/azuremaps/pen/ZqMeQY/'>Update shape properties</a> (Mettre à jour les propriétés des formes) d’Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) de la section Pens (Extraits de code) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

Le premier bloc de code ci-dessus construit un objet carte. Vous pouvez consulter la section [Créer une carte](./map-create.md) pour obtenir des instructions.

Un point est une [fonctionnalité](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) de la classe [Point](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest). Le deuxième bloc de code initialise la valeur du rayon de l’élément de curseur HTML, puis construit et encapsule un objet de point dans un objet de classe [Shape](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest).

Le troisième bloc de code crée une fonction qui prend la valeur de l’élément du curseur de plage HTML et modifie la valeur de rayon à l’aide de la méthode [addProperty](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) de la classe Shape.

Dans le quatrième bloc de code, un objet source de données est créé à l’aide de la classe [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest). Le point est alors ajouté à la source de données.

Un élément [PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) restitue les données encapsulées dans l’élément [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) sur la carte. Le troisième bloc de code crée une couche de polygones. Consultez les propriétés d’une couche de polygones dans [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest). La source de données, le détecteur d’événements de clic et la couche de polygones sont créés et ajoutés à la carte dans la fonction de [détecteur d’événements](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) pour garantir que le point s’affiche après le chargement complet de la carte.

## <a name="next-steps"></a>Étapes suivantes

Pour consulter plus d’exemples de code à ajouter à vos cartes, consultez les articles suivants :

> [!div class="nextstepaction"]
> [Marqueurs HTML](./map-add-custom-html.md)

> [!div class="nextstepaction"]
> [Ajouter une couche de carte thermique](./map-add-heat-map-layer.md)
