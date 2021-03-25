---
title: Ajouter un calque de mosaïques à des cartes Android | Microsoft Azure Maps
description: Découvrez comment ajouter une couche de mosaïques à une carte. Consultez un exemple qui utilise l’Android SDK Azure Maps pour ajouter un calque de radar météo à une carte.
author: rbrundritt
ms.author: richbrun
ms.date: 2/26/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: 6a920dc222cae4aedd77b667644de317637bbb69
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102047500"
---
# <a name="add-a-tile-layer-to-a-map-android-sdk"></a>Ajout d’une couche de mosaïques à une carte (Android SDK)

Cet article explique comment afficher un calque de mosaïques sur une carte à l’aide de l’Android SDK Azure Maps. Les couches de mosaïques vous permettent de superposer des images à des mosaïques de carte de base Azure Maps. Vous trouverez plus d’informations sur le système de mosaïques Azure Maps dans la documentation [Niveaux de zoom et grille mosaïque](zoom-levels-and-tile-grid.md).

Un calque de mosaïques charge des mosaïques à partir d’un serveur. Ces images peuvent être prérendues et stockées comme toute autre image sur un serveur à l’aide d’une convention de nommage compréhensible par la couche de mosaïques. Ou ces images peuvent être rendues avec un service dynamique qui génère les images en quasi temps réel. Trois conventions de nommage de service de mosaïques sont prises en charge par la classe TileLayer d’Azure Maps :

* Notation de zoom X, Y : selon le niveau de zoom, x correspond à la position de colonne et y à la position de ligne de la mosaïque dans la grille mosaïque.
* Notation Quadkey : combinaison d’informations de zoom x, y au sein d’une valeur de chaîne qui correspond à l’identificateur unique de la mosaïque.
* Rectangle englobant : les coordonnées du rectangle englobant peuvent servir à spécifier une image au format `{west},{south},{east},{north}`, couramment utilisé par [Web Mapping Services (WMS)](https://www.opengeospatial.org/standards/wms).

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

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les différentes manières de superposer des images sur une carte, consultez l’article suivant.

> [!div class="nextstepaction"]
> [Couche d’images](map-add-image-layer-android.md)
