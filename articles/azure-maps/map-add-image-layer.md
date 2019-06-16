---
title: Ajouter une couche d’images dans Azure Maps | Microsoft Docs
description: Comment ajouter une couche d’images à une carte Javascript
author: rbrundritt
ms.author: richbrun
ms.date: 12/3/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 5396fefca3a60dea7a503f8b4e84cc575753ea30
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60769564"
---
# <a name="add-an-image-layer-to-a-map"></a>Ajouter une couche d’images à une carte

Cet article vous montre comment vous pouvez superposer une image à un ensemble fixe de coordonnées sur une carte. Il existe de nombreux scénarios dans lesquels une image est superposée à une carte. Voici quelques exemples d’images qui sont souvent superposées à des cartes :

* Images capturées à l’aide de drones
* Plans au sol d’un immeuble
* Images de carte historiques ou autrement spécialisées
* Plans d’un lieu de travail
* Images de radar météo

> [!TIP]
> [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest) permet de superposer rapidement une image à une carte. Toutefois, si l’image est grande, le navigateur peut avoir du mal à la charger. Dans ce cas, vous pouvez diviser votre image en plusieurs mosaïques et les charger sur la carte en tant que [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest).

## <a name="add-an-image-layer"></a>Ajouter un calque d’images

Cet exemple montre comment superposer l’image d’une [carte de Newark dans le New Jersey datant de 1922](https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg) à la carte principale.

<br/>

<iframe height='500' scrolling='no' title='Couche d’images simple' src='//codepen.io/azuremaps/embed/eQodRo/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consultez l’extrait de code <a href='https://codepen.io/azuremaps/pen/eQodRo/'>Simple Image Layer</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

Dans le code ci-dessus, le premier bloc de code construit un objet de carte. Vous pouvez consulter la section [Créer une carte](./map-create.md) pour obtenir des instructions.

Dans le deuxième bloc de code, un [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest) est créé en passant une URL à une image ainsi que les coordonnées des quatre angles, au format `[Top Left Corner, Top Right Corner, Bottom Right Corner, Bottom Left Corner]`.

## <a name="import-a-kml-ground-overlay"></a>Importer un calque de relief KML

Cet exemple montre comment superposer les informations de calque de relief KML comme couche d’images sur la carte. Les calques de relief KML fournissent les coordonnées des points cardinaux ainsi qu’une rotation à gauche, alors que la couche d’images attend des coordonnées pour chaque angle de l’image. Le calque de relief KML de cet exemple est celui de la cathédrale de Chartres et a été tiré de [Wikimedia](https://commons.wikimedia.org/wiki/File:Chartres.svg/overlay.kml).

<br/>

<iframe height='500' scrolling='no' title='Calque de relief KML en tant que couche d’images' src='//codepen.io/azuremaps/embed/EOJgpj/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consultez l’extrait de code <a href='https://codepen.io/azuremaps/pen/EOJgpj/'>KML Ground Overlay as Image Layer</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

Le code ci-dessus utilise la fonction statique `getCoordinatesFromEdges` de la classe [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest) pour calculer les quatre angles de l’image à partir des informations relatives aux points cardinaux et à la rotation issues du calque de relief KML.


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