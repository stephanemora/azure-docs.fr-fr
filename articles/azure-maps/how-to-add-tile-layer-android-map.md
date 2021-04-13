---
title: Ajouter un calque de mosaïques à des cartes Android | Microsoft Azure Maps
description: Découvrez comment ajouter une couche de mosaïques à une carte. Consultez un exemple qui utilise l’Android SDK Azure Maps pour ajouter un calque de radar météo à une carte.
author: rbrundritt
ms.author: richbrun
ms.date: 3/25/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: ac37a4e6d68decdf6780560963a0c534689e8dbb
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105608977"
---
# <a name="add-a-tile-layer-to-a-map-android-sdk"></a>Ajout d’une couche de mosaïques à une carte (Android SDK)

Cet article explique comment afficher un calque de mosaïques sur une carte à l’aide de l’Android SDK Azure Maps. Les couches de mosaïques vous permettent de superposer des images à des mosaïques de carte de base Azure Maps. Vous trouverez plus d’informations sur le système de mosaïques Azure Maps dans la documentation [Niveaux de zoom et grille mosaïque](zoom-levels-and-tile-grid.md).

Un calque de mosaïques charge des mosaïques à partir d’un serveur. Ces images peuvent être prérendues et stockées comme toute autre image sur un serveur à l’aide d’une convention de nommage compréhensible par la couche de mosaïques. Ou ces images peuvent être rendues avec un service dynamique qui génère les images en quasi temps réel. Trois conventions de nommage de service de mosaïques sont prises en charge par la classe TileLayer d’Azure Maps :

* Notation de zoom X, Y : selon le niveau de zoom, x correspond à la position de colonne et y à la position de ligne de la mosaïque dans la grille mosaïque.
* Notation Quadkey : combinaison d’informations de zoom x, y au sein d’une valeur de chaîne qui correspond à l’identificateur unique de la mosaïque.
* Rectangle englobant : Les coordonnées du rectangle englobant peuvent servir à spécifier une image au format `{west},{south},{east},{north}`, couramment utilisé par des [services de cartographie web (WMS)](https://www.opengeospatial.org/standards/wms).

> [!TIP]
> Un calque de mosaïques est un excellent moyen de visualiser des jeux de données volumineux sur une carte. Non seulement elle peut être générée à partir d’une image, mais les données vectorielles peuvent également être affichées sous la forme d’une couche de mosaïques. Lorsque les données vectorielles sont affichées sous forme de couche de mosaïques, il suffit au contrôle de carte de charger les mosaïques, dont la taille de fichier peut être beaucoup plus petite que celle des données vectorielles qu’elles représentent. Cette technique est couramment utilisée pour afficher des millions de lignes de données sur une carte.

L’URL de la mosaïque passée à une couche de mosaïques doit être l’URL HTTP/HTTPS d’une ressource TileJSON ou d’un modèle d’URL de mosaïque qui utilise les paramètres suivants :

* `{x}` - position X de la mosaïque. Nécessite également `{y}` et `{z}`.
* `{y}` - position Y de la mosaïque. Nécessite également `{x}` et `{z}`.
* `{z}` - niveau de zoom de la mosaïque. Nécessite également `{x}` et `{y}`.
* `{quadkey}` - identificateur quadkey de la mosaïque basé sur la convention de nommage du système de mosaïques de Bing Maps.
* `{bbox-epsg-3857}` - chaîne de rectangle englobant au format `{west},{south},{east},{north}` du système SRID EPSG 3857.
* `{subdomain}` : espace réservé pour les valeurs de sous-domaine, si la valeur de sous-domaine est spécifiée.
* `azmapsdomain.invalid` - espace réservé pour aligner le domaine et l’authentification des demandes de vignette avec les mêmes valeurs que celles utilisées par la carte. Utilisez ceci lors de l’appel d’un service de vignette hébergé par Azure Maps.

## <a name="prerequisites"></a>Conditions préalables requises

Pour suivre la procédure décrite dans cet article, vous devez installer [Android SDK Azure Maps](how-to-use-android-map-control-library.md) afin de charger une carte.

## <a name="add-a-tile-layer-to-the-map"></a>Ajouter un calque de mosaïques à une carte

Cet exemple montre comment créer une couche de mosaïques qui pointe vers un ensemble de mosaïques. Cet exemple utilise le système de mosaïques « x, y, zoom ». La source de cette couche de mosaïques est le [projet OpenSeaMap](https://openseamap.org/index.php), qui contient des cartes marines crowdsourcées. Souvent, avec les couches de mosaïques, il est souhaitable de pouvoir voir clairement les étiquettes des villes sur la carte. Ce comportement peut être obtenu en insérant la couche de mosaïques sous les couches d’étiquettes de la carte.

::: zone pivot="programming-language-java-android"

```java
TileLayer layer = new TileLayer(
    tileUrl("https://tiles.openseamap.org/seamark/{z}/{x}/{y}.png"),
    opacity(0.8f),
    tileSize(256),
    minSourceZoom(7),
    maxSourceZoom(17)
);

map.layers.add(layer, "labels");
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = TileLayer(
    tileUrl("https://tiles.openseamap.org/seamark/{z}/{x}/{y}.png"),
    opacity(0.8f),
    tileSize(256),
    minSourceZoom(7),
    maxSourceZoom(17)
)

map.layers.add(layer, "labels")
```

::: zone-end

La capture d’écran suivante montre le rendu, obtenu avec le code ci-dessus, d’une couche de mosaïques d’informations marines sur une carte avec un style de nuances de gris foncé.

![Carte Android affichant la couche de mosaïques](media/how-to-add-tile-layer-android-map/xyz-tile-layer-android.png)

## <a name="add-an-ogc-web-mapping-service-wms"></a>Ajouter un service de cartographie web (WMS) OGC

Un service de cartographie web (WMTS) est une norme de l’Open Geospatial Consortium (OGC), qui permet de proposer des images de données cartographiques. Il existe de nombreux jeux de données ouverts disponibles dans ce format, que vous pouvez utiliser avec Azure Maps. Ce type de service peut être utilisé avec une couche de mosaïques si le service prend en charge le système de référence de coordonnées (CRS) `EPSG:3857`. Quand vous utilisez un service de cartographie web, définissez les paramètres de largeur et de hauteur en utilisant les mêmes valeurs que celles prises en charge par le service. Veillez à définir ces mêmes valeurs dans l’option `tileSize`. Dans l’URL mise en forme, définissez le paramètre `BBOX` du service avec l’espace réservé `{bbox-epsg-3857}`.

::: zone pivot="programming-language-java-android"

``` java
TileLayer layer = new TileLayer(
    tileUrl("https://mrdata.usgs.gov/services/gscworld?FORMAT=image/png&HEIGHT=1024&LAYERS=geology&REQUEST=GetMap&STYLES=default&TILED=true&TRANSPARENT=true&WIDTH=1024&VERSION=1.3.0&SERVICE=WMS&CRS=EPSG:3857&BBOX={bbox-epsg-3857}"),
    tileSize(1024)
);

map.layers.add(layer, "labels");
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = TileLayer(
    tileUrl("https://mrdata.usgs.gov/services/gscworld?FORMAT=image/png&HEIGHT=1024&LAYERS=geology&REQUEST=GetMap&STYLES=default&TILED=true&TRANSPARENT=true&WIDTH=1024&VERSION=1.3.0&SERVICE=WMS&CRS=EPSG:3857&BBOX={bbox-epsg-3857}"),
    tileSize(1024)
)

map.layers.add(layer, "labels")
```

::: zone-end

La capture d’écran suivante montre le code ci-dessus superposé sur un service de cartographie web de données géologiques provenant de l’[U.S. Geological Survey (USGS)](https://mrdata.usgs.gov/) au-dessus d’une carte, sous les étiquettes.

![Carte Android affichant la couche de mosaïques WMS](media/how-to-add-tile-layer-android-map/android-tile-layer-wms.jpg)

## <a name="add-an-ogc-web-mapping-tile-service-wmts"></a>Ajouter un service de mosaïques de cartographie web (WMTS) OGC

Un service de mosaïques de cartographie web (WMTS) est une norme de l’Open Geospatial Consortium (OGC), qui permet de proposer des superpositions de mosaïques sur des cartes. Il existe de nombreux jeux de données ouverts disponibles dans ce format, que vous pouvez utiliser avec Azure Maps. Ce type de service peut être utilisé avec une couche de mosaïques si le service prend en charge le système de référence de coordonnées (CRS) `EPSG:3857` ou `GoogleMapsCompatible`. Quand vous utilisez un service WMTS, définissez les paramètres de largeur et de hauteur en utilisant les mêmes valeurs que celles prises en charge par le service. Veillez à définir ces mêmes valeurs dans l’option `tileSize`. Dans l’URL mise en forme, remplacez les espaces réservés suivants de manière appropriée :

* `{TileMatrix}` => `{z}`
* `{TileRow}` => `{y}`
* `{TileCol}` => `{x}`

::: zone pivot="programming-language-java-android"

``` java
TileLayer layer = new TileLayer(
    tileUrl("https://basemap.nationalmap.gov/arcgis/rest/services/USGSImageryOnly/MapServer/WMTS/tile/1.0.0/USGSImageryOnly/default/GoogleMapsCompatible/{z}/{y}/{x}"),
    tileSize(256),
    bounds(-173.25000107492872, 0.0005794121990209753, 146.12527718104752, 71.506811402077),
    maxSourceZoom(18)
);

map.layers.add(layer, "transit");
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = TileLayer(
    tileUrl("https://basemap.nationalmap.gov/arcgis/rest/services/USGSImageryOnly/MapServer/WMTS/tile/1.0.0/USGSImageryOnly/default/GoogleMapsCompatible/{z}/{y}/{x}"),
    tileSize(256),
    bounds(-173.25000107492872, 0.0005794121990209753, 146.12527718104752, 71.506811402077),
    maxSourceZoom(18)
)

map.layers.add(layer, "transit")
```

::: zone-end

La capture d’écran suivante montre le code ci-dessus superposé sur un service de mosaïques de cartographie web pour des données d’imagerie provenant de l’[U.S. Geological Survey (USGS) National Map](https://viewer.nationalmap.gov/services/) au-dessus d’une carte, sous les routes et les étiquettes.

![Carte Android affichant la couche de mosaïques WMTS](media/how-to-add-tile-layer-android-map/android-tile-layer-wmts.jpg)

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les différentes manières de superposer des images sur une carte, consultez l’article suivant.

> [!div class="nextstepaction"]
> [Couche d’images](map-add-image-layer-android.md)
