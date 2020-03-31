---
title: Ajouter une couche de mosaïques à une carte | Microsoft Azure Maps
description: Dans cet article, vous allez apprendre à superposer une couche de mosaïques sur une carte à l’aide du SDK web Microsoft Azure Maps. Les couches de mosaïques vous permettent d’afficher des images sur une carte.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 61d7a11df499e6b740adb45968721b6a9bb1af22
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76988598"
---
# <a name="add-a-tile-layer-to-a-map"></a>Ajouter une couche de mosaïques à une carte

Cet article vous montre comment superposer une couche de mosaïques sur une carte. Les couches de mosaïques vous permettent de superposer des images à des mosaïques de carte de base Azure Maps. Pour plus d’informations sur le système de mosaïques Azure Maps, consultez [Niveaux de zoom et grille mosaïque](zoom-levels-and-tile-grid.md).

Un calque de mosaïques charge des mosaïques à partir d’un serveur. Ces images peuvent être prérendues ou rendues dynamiquement. Les images prérendues sont stockées comme toute autre image sur un serveur à l’aide d’une convention d’affectation de noms compréhensible par la couche de mosaïques. Les images rendues dynamiquement utilisent un service pour charger les images presque en temps réel. Trois conventions de nommage de service de mosaïques sont prises en charge par la classe [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest) d’Azure Maps : 

* X, Y et notation de zoom : X correspond à la position de colonne et Y à la position de ligne de la mosaïque dans la grille mosaïque, et la valeur de la notation de zoom est basée sur le niveau de zoom.
* Notation Quadkey : combine les informations x, y et de zoom en une valeur de chaîne unique. Cette valeur de chaîne devient un identificateur unique pour une seule mosaïque.
* Cadre englobant : spécifiez une image au format de coordonnées du cadre englobant : `{west},{south},{east},{north}`. Ce format est couramment utilisé par les [services de mappage web (WMS)](https://www.opengeospatial.org/standards/wms).

> [!TIP]
> Une [couche de mosaïques](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest) est un excellent moyen de visualiser des jeux de données volumineux sur une carte. Non seulement elle peut être générée à partir d’une image, mais les données vectorielles peuvent également être affichées sous la forme d’une couche de mosaïques. En affichant des données vectorielles sous forme de couche de mosaïques, le contrôle de carte doit uniquement charger les mosaïques dont la taille de fichier est inférieure à celle des données vectorielles qu’elles représentent. Cette technique est couramment utilisée pour afficher des millions de lignes de données sur une carte.

L’URL de la mosaïque transmise à une couche de mosaïques doit être l’URL http ou https d’une ressource TileJSON ou d’un modèle d’URL de mosaïque qui utilise les paramètres suivants : 

* `{x}` - position X de la mosaïque. Nécessite également `{y}` et `{z}`.
* `{y}` - position Y de la mosaïque. Nécessite également `{x}` et `{z}`.
* `{z}` - niveau de zoom de la mosaïque. Nécessite également `{x}` et `{y}`.
* `{quadkey}` - identificateur quadkey de la mosaïque basé sur la convention de nommage du système de mosaïques de Bing Maps.
* `{bbox-epsg-3857}` - chaîne de rectangle englobant au format `{west},{south},{east},{north}` du système SRID EPSG 3857.
* `{subdomain}` - espace réservé pour les valeurs de sous-domaine ; si spécifié, le `subdomain` sera ajouté.

## <a name="add-a-tile-layer"></a>Ajouter un calque de vignettes

 Cet exemple montre comment créer une couche de mosaïques qui pointe vers un ensemble de mosaïques. Cet exemple utilise le système de mosaïques x, y et zoom. La source de cette couche de mosaïques est un calque de radar météo issu de [Iowa Environmental Mesonet of Iowa State University](https://mesonet.agron.iastate.edu/ogc/). Lors de la visualisation des données radar, l’idéal serait que les utilisateurs voient clairement les étiquettes des villes lorsqu’ils naviguent sur la carte. Ce comportement peut être implémenté en insérant la couche de mosaïques sous la couche `labels`.

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

<iframe height='500' scrolling='no' title='Couche de mosaïques utilisant X, Y et Z' src='//codepen.io/azuremaps/embed/BGEQjG/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consultez l’extrait de code <a href='https://codepen.io/azuremaps/pen/BGEQjG/'>Tile Layer using X, Y, and Z</a> (Couche de mosaïques utilisant X, Y et Z) Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-a-tile-layer"></a>Personnaliser une couche de mosaïques

La classe de calque de mosaïques dispose de nombreuses options de style. Voici un outil qui va vous permettre de les essayer.

<br/>

<iframe height='700' scrolling='no' title='Options de la couche de mosaïques' src='//codepen.io/azuremaps/embed/xQeRWX/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consultez l’extrait de code <a href='https://codepen.io/azuremaps/pen/xQeRWX/'>Tile Layer Options</a> (Options de la couche de mosaïques) Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
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
