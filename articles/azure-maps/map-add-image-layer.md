---
title: Ajouter une couche d’images dans Azure Maps | Microsoft Docs
description: Comment ajouter une couche d’images à un kit de développement logiciel (SDK) web Azure Maps.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 631a9e2d44b798404ee7567d3ccfed90628d2f8b
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75432862"
---
# <a name="add-an-image-layer-to-a-map"></a>Ajouter une couche d’images à une carte

Cet article vous montre comment vous pouvez superposer une image à un ensemble fixe de coordonnées sur une carte. Voici quelques exemples d’images qui sont souvent superposées à des cartes :

* Images capturées à l’aide de drones
* Plans au sol d’un immeuble
* Images de carte historiques ou autrement spécialisées
* Plans d’un lieu de travail
* Images de radar météo

> [!TIP]
> [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest) permet de superposer une image à une carte. Notez que les navigateurs peuvent avoir des difficultés à charger une grande image. Dans ce cas, vous pouvez diviser votre image en plusieurs mosaïques et les charger sur la carte en tant que [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest).

Le calque d’images prend en charge les formats d’image suivants :

- JPEG
- PNG
- BMP
- GIF (sans animation)

## <a name="add-an-image-layer"></a>Ajouter un calque d’images

Le code suivant montre comment superposer l’image d’une [carte de Newark dans le New Jersey datant de 1922](https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg) à la carte principale. Un [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest) est créé en passant une URL à une image ainsi que les coordonnées des quatre angles, au format `[Top Left Corner, Top Right Corner, Bottom Right Corner, Bottom Left Corner]`.

```javascript
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
```

Voici l’exemple complet du code précédent.

<br/>

<iframe height='500' scrolling='no' title='Couche d’images simple' src='//codepen.io/azuremaps/embed/eQodRo/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consultez l’extrait de code <a href='https://codepen.io/azuremaps/pen/eQodRo/'>Simple Image Layer</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="import-a-kml-ground-overlay"></a>Importer un calque de relief KML

L’exemple suivant montre comment superposer les informations de calque de relief KML comme couche d’images sur la carte. Les calques de relief KML fournissent les coordonnées des points cardinaux ainsi qu’une rotation à gauche, alors que la couche d’images attend des coordonnées pour chaque angle de l’image. Le calque de relief KML de cet exemple est celui de la cathédrale de Chartres et a été tiré de [Wikimedia](https://commons.wikimedia.org/wiki/File:Chartres.svg/overlay.kml).

Le code suivant utilise la fonction statique `getCoordinatesFromEdges` de la classe [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest). Il calcule les quatre coins de l’image à partir des informations relatives aux points cardinaux et à la rotation issues du calque de relief KML.

<br/>

<iframe height='500' scrolling='no' title='Calque de relief KML en tant que couche d’images' src='//codepen.io/azuremaps/embed/EOJgpj/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consultez l’extrait de code <a href='https://codepen.io/azuremaps/pen/EOJgpj/'>KML Ground Overlay as Image Layer</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-an-image-layer"></a>Personnaliser une couche d’images

La couche d’images dispose de nombreuses options de style. Voici un outil qui va vous permettre de les essayer.

<br/>

<iframe height='700' scrolling='no' title='Options de la couche d’images' src='//codepen.io/azuremaps/embed/RqOGzx/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consultez l’extrait de code <a href='https://codepen.io/azuremaps/pen/RqOGzx/'>Image Layer Options</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les classes et les méthodes utilisées dans cet article :

> [!div class="nextstepaction"]
> [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [ImageLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.imagelayeroptions?view=azure-iot-typescript-latest)

Pour obtenir plus d’exemples de code à ajouter à vos cartes, consultez les articles suivants :

> [!div class="nextstepaction"]
> [Ajouter une couche de mosaïques](./map-add-tile-layer.md)