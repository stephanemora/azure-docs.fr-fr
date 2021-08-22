---
title: Ajouter une couche de mosaïques à une carte | Microsoft Azure Maps
description: Découvrez comment superposer des images sur des cartes. Consultez un exemple qui utilise le Kit de développement logiciel (SDK) web Azure Maps pour ajouter une couche de mosaïques contenant un calque de radar météo à une carte.
author: rbrundritt
ms.author: richbrun
ms.date: 3/25/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-js
ms.openlocfilehash: c28eb1bdbb0069a831f397150d6479380df8b3a3
ms.sourcegitcommit: d9a2b122a6fb7c406e19e2af30a47643122c04da
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/24/2021
ms.locfileid: "114665155"
---
# <a name="add-a-tile-layer-to-a-map"></a>Ajouter une couche de mosaïques à une carte

Cet article vous montre comment superposer une couche de mosaïques sur une carte. Les couches de mosaïques vous permettent de superposer des images à des mosaïques de carte de base Azure Maps. Pour plus d’informations sur le système de mosaïques Azure Maps, consultez [Niveaux de zoom et grille mosaïque](zoom-levels-and-tile-grid.md).

Un calque de mosaïques charge des mosaïques à partir d’un serveur. Ces images peuvent être prérendues ou rendues dynamiquement. Les images prérendues sont stockées comme toute autre image sur un serveur à l’aide d’une convention d’affectation de noms compréhensible par la couche de mosaïques. Les images rendues dynamiquement utilisent un service pour charger les images presque en temps réel. Trois conventions de nommage de service de mosaïques sont prises en charge par la classe [TileLayer](/javascript/api/azure-maps-control/atlas.layer.tilelayer) d’Azure Maps :

* X, Y et notation de zoom : X correspond à la position de colonne et Y à la position de ligne de la mosaïque dans la grille mosaïque, et la valeur de la notation de zoom est basée sur le niveau de zoom.
* Notation Quadkey : combine les informations x, y et de zoom en une valeur de chaîne unique. Cette valeur de chaîne devient un identificateur unique pour une seule mosaïque.
* Cadre englobant : spécifiez une image au format de coordonnées du cadre englobant : `{west},{south},{east},{north}`. Ce format est couramment utilisé par les [services de cartographie web (WMS)](https://www.opengeospatial.org/standards/wms).

> [!TIP]
> Une [couche de mosaïques](/javascript/api/azure-maps-control/atlas.layer.tilelayer) est un excellent moyen de visualiser des jeux de données volumineux sur une carte. Non seulement elle peut être générée à partir d’une image, mais les données vectorielles peuvent également être affichées sous la forme d’une couche de mosaïques. En affichant des données vectorielles sous forme de couche de mosaïques, le contrôle de carte doit uniquement charger les mosaïques dont la taille de fichier est inférieure à celle des données vectorielles qu’elles représentent. Cette technique est couramment utilisée pour afficher des millions de lignes de données sur une carte.

L’URL de la mosaïque transmise à une couche de mosaïques doit être l’URL http ou https d’une ressource TileJSON ou d’un modèle d’URL de mosaïque qui utilise les paramètres suivants :

* `{x}` - position X de la mosaïque. Nécessite également `{y}` et `{z}`.
* `{y}` - position Y de la mosaïque. Nécessite également `{x}` et `{z}`.
* `{z}` - niveau de zoom de la mosaïque. Nécessite également `{x}` et `{y}`.
* `{quadkey}` - identificateur quadkey de la mosaïque basé sur la convention de nommage du système de mosaïques de Bing Maps.
* `{bbox-epsg-3857}` - chaîne de rectangle englobant au format `{west},{south},{east},{north}` du système SRID EPSG 3857.
* `{subdomain}` - espace réservé pour les valeurs de sous-domaine ; si spécifié, le `subdomain` sera ajouté.
* `{azMapsDomain}` - espace réservé pour aligner le domaine et l’authentification des demandes de vignette avec les mêmes valeurs que celles utilisées par la carte.

## <a name="add-a-tile-layer"></a>Ajouter un calque de vignettes

 Cet exemple montre comment créer une couche de mosaïques qui pointe vers un ensemble de mosaïques. Cet exemple utilise le système de mosaïques x, y et zoom. La source de ce calque de vignette est le [projet OpenSeaMap](https://openseamap.org/index.php), qui contient des cartes marines « crowdsourcées ». Lors de la visualisation des données radar, l’idéal serait que les utilisateurs voient clairement les étiquettes des villes lorsqu’ils naviguent sur la carte. Ce comportement peut être implémenté en insérant la couche de mosaïques sous la couche `labels`.

```javascript
//Create a tile layer and add it to the map below the label layer.
map.layers.add(new atlas.layer.TileLayer({
    tileUrl: 'https://tiles.openseamap.org/seamark/{z}/{x}/{y}.png',
    opacity: 0.8,
    tileSize: 256,
    minSourceZoom: 7,
    maxSourceZoom: 17
}), 'labels');
```

Vous trouverez ci-dessous l’exemple de code d’exécution complet de la fonctionnalité ci-dessus.

<br/>

<iframe height='500' scrolling='no' title='Couche de mosaïques utilisant X, Y et Z' src='//codepen.io/azuremaps/embed/BGEQjG/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consultez l’extrait de code <a href='https://codepen.io/azuremaps/pen/BGEQjG/'>Tile Layer using X, Y, and Z</a> (Couche de mosaïques utilisant X, Y et Z) Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-an-ogc-web-mapping-service-wms"></a>Ajouter un service de cartographie web (WMS) OGC

Un service de cartographie web (WMS) est une norme de l’Open Geospatial Consortium (OGC), qui permet de proposer des images de données cartographiques. Il existe de nombreux jeux de données ouverts disponibles dans ce format, que vous pouvez utiliser avec Azure Maps. Ce type de service peut être utilisé avec un calque de vignette, si le service prend en charge le système de référence de coordonnées (CRS) `EPSG:3857`. Quand vous utilisez un service de cartographie web, définissez les paramètres de largeur et de hauteur en utilisant les mêmes valeurs que celles prises en charge par le service. Veillez à définir ces mêmes valeurs dans l’option `tileSize`. Dans l’URL mise en forme, définissez le paramètre `BBOX` du service avec l’espace réservé `{bbox-epsg-3857}`.

La capture d’écran suivante montre le code ci-dessus superposé sur un service de cartographie web de données géologiques provenant de l’[U.S. Geological Survey (USGS)](https://mrdata.usgs.gov/) au-dessus d’une carte, sous les étiquettes.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Calque de vignette d’un service de cartographie web" src="https://codepen.io/azuremaps/embed/BapjZqr?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
Consultez le <a href='https://codepen.io/azuremaps/pen/BapjZqr'>calque de vignette du service de cartographie web</a> Pen d’Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-an-ogc-web-mapping-tile-service-wmts"></a>Ajouter un service de vignette de cartographie web (WMTS) OGC

Un service de calque de cartographie web (WMTS) est une norme de l’Open Geospatial Consortium (OGC), qui permet de proposer des superpositions de vignettes sur des cartes. Il existe de nombreux jeux de données ouverts disponibles dans ce format, que vous pouvez utiliser avec Azure Maps. Ce type de service peut être utilisé avec un calque de vignette, si le service prend en charge le système de référence de coordonnées (CRS) `EPSG:3857` ou `GoogleMapsCompatible`. Quand vous utilisez un service de calque de cartographie web, définissez les paramètres de largeur et de hauteur en utilisant les mêmes valeurs que celles prises en charge par le service. Veillez à définir ces mêmes valeurs dans l’option `tileSize`. Dans l’URL mise en forme, remplacez les espaces réservés suivants de manière appropriée :

* `{TileMatrix}` => `{z}`
* `{TileRow}` => `{y}`
* `{TileCol}` => `{x}`

La capture d’écran suivante montre le code ci-dessus superposé sur un service de vignette de cartographie web pour des données d’imagerie provenant de l’[U.S. Geological Survey (USGS) National Map](https://viewer.nationalmap.gov/services/) au-dessus d’une carte, sous les routes et les étiquettes.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Calque de vignette d’un service de calque de cartographie web" src="https://codepen.io/azuremaps/embed/BapjZVY?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
Consultez le <a href='https://codepen.io/azuremaps/pen/BapjZVY'>calque de vignette du service de calque de cartographie web</a> Pen d’Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-a-tile-layer"></a>Personnaliser une couche de mosaïques

La classe de calque de mosaïques dispose de nombreuses options de style. Voici un outil qui va vous permettre de les essayer.

<br/>

<iframe height='700' scrolling='no' title='Options de la couche de mosaïques' src='//codepen.io/azuremaps/embed/xQeRWX/?height=700&theme-id=0&default-tab=result' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consultez l’extrait de code <a href='https://codepen.io/azuremaps/pen/xQeRWX/'>Tile Layer Options</a> (Options de la couche de mosaïques) Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les classes et les méthodes utilisées dans cet article :

> [!div class="nextstepaction"]
> [TileLayer](/javascript/api/azure-maps-control/atlas.layer.tilelayer)

> [!div class="nextstepaction"]
> [TileLayerOptions](/javascript/api/azure-maps-control/atlas.tilelayeroptions)

Pour obtenir plus d’exemples de code à ajouter à vos cartes, consultez les articles suivants :

> [!div class="nextstepaction"]
> [Ajouter un calque d’images](./map-add-image-layer.md)
