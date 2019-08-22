---
title: Ajouter un calque de mosaïques dans Azure Maps | Microsoft Docs
description: Guide pratique pour ajouter un calque de mosaïques au SDK web Azure Maps.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 3f047ec1aced55038384cbe29bd3a4b8a948dce9
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2019
ms.locfileid: "68976453"
---
# <a name="add-a-tile-layer-to-a-map"></a>Ajouter un calque de mosaïques à une carte

Cet article vous montre comment vous pouvez superposer un calque de mosaïques à une carte. Les calques de mosaïques vous permettent de superposer des images à des mosaïques de carte de base Azure Maps. Vous trouverez plus d’informations sur le système de mosaïques Azure Maps dans la documentation [Niveaux de zoom et grille mosaïque](zoom-levels-and-tile-grid.md).

Un calque de mosaïques charge des mosaïques à partir d’un serveur. Ces images peuvent être préaffichées et stockées comme toute autre image sur un serveur, à l’aide d’une convention de nommage que le calque de mosaïques comprend, ou à l’aide d’un service dynamique qui génère les images à la volée. Trois conventions de nommage de service de mosaïques sont prises en charge par la classe [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest) d’Azure Maps : 

* Notation de zoom X, Y : selon le niveau de zoom, x correspond à la position de colonne et y à la position de ligne de la mosaïque dans la grille mosaïque.
* Notation Quadkey : combinaison d’informations de zoom x, y au sein d’une valeur de chaîne qui correspond à l’identificateur unique de la mosaïque.
* Rectangle englobant : les coordonnées du rectangle englobant peuvent servir à spécifier une image au format `{west},{south},{east},{north}`, qui est couramment utilisé par [Web Mapping Services (WMS)](https://www.opengeospatial.org/standards/wms).

> [!TIP]
> Un [calque de mosaïques](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest) est un excellent moyen de visualiser des jeux de données volumineux sur une carte. Non seulement elle peut être générée à partir d’une image, mais les données vectorielles peuvent également être affichées sous la forme d’un calque de mosaïques. En affichant des données vectorielles sous forme de calque de mosaïques, le contrôle de carte doit uniquement charger les mosaïques, qui peuvent être beaucoup plus petites que les données vectorielles qu’elles représentent. Cette technique est couramment utilisée pour afficher des millions de lignes de données sur une carte.

L’URL de la mosaïque passée à un calque de mosaïques doit être l’URL HTTP/HTTPS d’une ressource TileJSON ou d’un modèle d’URL de mosaïque qui utilise les paramètres suivants : 

* `{x}` - position X de la mosaïque. Nécessite également `{y}` et `{z}`.
* `{y}` - position Y de la mosaïque. Nécessite également `{x}` et `{z}`.
* `{z}` - niveau de zoom de la mosaïque. Nécessite également `{x}` et `{y}`.
* `{quadkey}` - identificateur quadkey de la mosaïque basé sur la convention de nommage du système de mosaïques de Bing Maps.
* `{bbox-epsg-3857}` - chaîne de rectangle englobant au format `{west},{south},{east},{north}` du système SRID EPSG 3857.
* `{subdomain}` - espace réservé où les valeurs de sous-domaine sont ajoutées, si spécifiées.

## <a name="add-a-tile-layer"></a>Ajouter un calque de vignettes

 Cet exemple montre comment créer un calque de mosaïques qui pointe vers un ensemble de mosaïques qui utilisent le système de zoom de mosaïques x, y. La source de ce calque de mosaïques est un calque de radar météo issu de [Iowa Environmental Mesonet of Iowa State University](https://mesonet.agron.iastate.edu/ogc/). Lors de l’affichage des données radar, dans l’idéal les utilisateurs doivent voir clairement les étiquettes des villes quand ils parcourent la carte, ce qui peut être fait en insérant le calque de mosaïques sous le calque `labels`.

```javascript
//Create a tile layer and add it to the map below the label layer.
//Weather radar tiles from Iowa Environmental Mesonet of Iowa State University.
map.layers.add(new atlas.layer.TileLayer({
    tileUrl: 'https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png',
    opacity: 0.8,
    tileSize: 256
}), 'labels');
```

Vous trouverez ci-dessous l’exemple de code d’exécution complet de la fonctionnalité ci-dessus.

<br/>

<iframe height='500' scrolling='no' title='Calque de mosaïques utilisant X, Y et Z' src='//codepen.io/azuremaps/embed/BGEQjG/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consultez l’extrait de code <a href='https://codepen.io/azuremaps/pen/BGEQjG/'>Tile Layer using X, Y, and Z</a> (Calque de mosaïques utilisant X, Y et Z) Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-a-tile-layer"></a>Personnaliser un calque de mosaïques

La classe de calque de mosaïques dispose de nombreuses options de style. Voici un outil qui va vous permettre de les essayer.

<br/>

<iframe height='700' scrolling='no' title='Options du calque de mosaïques' src='//codepen.io/azuremaps/embed/xQeRWX/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consultez l’extrait de code <a href='https://codepen.io/azuremaps/pen/xQeRWX/'>Tile Layer Options</a> (Options du calque de mosaïques) Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les classes et les méthodes utilisées dans cet article :

> [!div class="nextstepaction"]
> [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [TileLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.tilelayeroptions?view=azure-iot-typescript-latest)

Pour obtenir plus d’exemples de code à ajouter à vos cartes, consultez les articles suivants :

> [!div class="nextstepaction"]
> [Ajouter un calque d’images](./map-add-image-layer.md)
