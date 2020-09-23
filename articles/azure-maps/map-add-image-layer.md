---
title: Ajouter une couche d’images à une carte | Microsoft Azure Maps
description: Découvrez comment ajouter des images à une carte. Découvrez comment utiliser le Kit de développement logiciel (SDK) web Azure Maps pour personnaliser les couches d’images et les images de superposition sur des ensembles fixes de coordonnées.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-javascript
ms.openlocfilehash: 9bf34b9abd78b62a5ded16ae41b028bf61a3b51d
ms.sourcegitcommit: 07166a1ff8bd23f5e1c49d4fd12badbca5ebd19c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/15/2020
ms.locfileid: "90086094"
---
# <a name="add-an-image-layer-to-a-map"></a>Ajouter une couche d’images à une carte

Cet article vous montre comment superposer une image à un ensemble fixe de coordonnées. Voici quelques exemples de différents types d’images qui peuvent être superposées sur des cartes :

* Images capturées à l’aide de drones
* Plans au sol d’un immeuble
* Images de carte historiques ou autrement spécialisées
* Plans d’un lieu de travail
* Images de radar météo

> [!TIP]
> [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer) permet de superposer une image à une carte. Notez que les navigateurs peuvent avoir des difficultés à charger une grande image. Dans ce cas, vous pouvez diviser votre image en plusieurs mosaïques et les charger sur la carte en tant que [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer).

Le calque d’images prend en charge les formats d’image suivants :

- JPEG
- PNG
- BMP
- GIF (sans animation)

## <a name="add-an-image-layer"></a>Ajouter un calque d’images

Le code suivant montre comment superposer l’image d’une [carte de Newark dans le New Jersey datant de 1922](https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg) à la carte principale. Un [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer) est créé en passant une URL à une image ainsi que les coordonnées des quatre angles, au format `[Top Left Corner, Top Right Corner, Bottom Right Corner, Bottom Left Corner]`.

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

<iframe height='500' scrolling='no' title='Couche d’images simple' src='//codepen.io/azuremaps/embed/eQodRo/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consultez l’extrait de code <a href='https://codepen.io/azuremaps/pen/eQodRo/'>Simple Image Layer</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="import-a-kml-file-as-ground-overlay"></a>Importer un fichier KML en tant que calque de relief

Cet exemple montre comment ajouter des informations de calque de relief KML comme couche d’image sur la carte. Les calques de relief KML fournissent les coordonnées des points cardinaux et une rotation dans le sens inverse des aiguilles d’une montre. Toutefois, la couche d’image attend des coordonnées pour chaque coin de l’image. Le calque de relief KML de cet exemple est celui de la cathédrale de Chartres et provient de [Wikimedia](https://commons.wikimedia.org/wiki/File:Chartres.svg/overlay.kml).

Le code utilise la fonction statique `getCoordinatesFromEdges` de la classe [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer). Il calcule les quatre coins de l’image à partir des informations relatives aux points cardinaux et à la rotation issues du calque de relief KML.

<br/>

<iframe height='500' scrolling='no' title='Calque de relief KML en tant que couche d’images' src='//codepen.io/azuremaps/embed/EOJgpj/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consultez l’extrait de code <a href='https://codepen.io/azuremaps/pen/EOJgpj/'>KML Ground Overlay as Image Layer</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-an-image-layer"></a>Personnaliser une couche d’images

La couche d’images dispose de nombreuses options de style. Voici un outil qui va vous permettre de les essayer.

<br/>

<iframe height='700' scrolling='no' title='Options de la couche d’images' src='//codepen.io/azuremaps/embed/RqOGzx/?height=700&theme-id=0&default-tab=result' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consultez l’extrait de code <a href='https://codepen.io/azuremaps/pen/RqOGzx/'>Image Layer Options</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les classes et les méthodes utilisées dans cet article :

> [!div class="nextstepaction"]
> [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer)

> [!div class="nextstepaction"]
> [ImageLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.imagelayeroptions)

Pour obtenir plus d’exemples de code à ajouter à vos cartes, consultez les articles suivants :

> [!div class="nextstepaction"]
> [Ajouter une couche de mosaïques](./map-add-tile-layer.md)