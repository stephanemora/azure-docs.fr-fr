---
title: Ajouter un calque d’image à une carte Android | Microsoft Azure Maps
description: Découvrez comment ajouter des images à une carte. Découvrez comment utiliser l’Android SDK Azure Maps pour personnaliser des calques d’image et des images de superposition sur des ensembles fixes de coordonnées.
author: rbrundritt
ms.author: richbrun
ms.date: 02/26/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: e1d99297c0357039606149bdf7e5a526258fc7c5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102054080"
---
# <a name="add-an-image-layer-to-a-map-android-sdk"></a>Ajouter un calque d’image à une carte (Android SDK)

Cet article vous montre comment superposer une image à un ensemble fixe de coordonnées. Voici quelques exemples de différents types d’images qui peuvent être superposées sur des cartes :

* Images capturées à l’aide de drones
* Plans au sol d’un immeuble
* Images de carte historiques ou autrement spécialisées
* Plans d’un lieu de travail

> [!TIP]
> Un calque d’image permet de superposer facilement une image sur une carte. Notez que les images de grande taille peuvent consommer beaucoup de mémoire et occasionner des problèmes de performances. Dans ce cas, vous pouvez diviser votre image en mosaïque et charger celle-ci sur la carte en tant que vignette de mosaïque.

## <a name="add-an-image-layer"></a>Ajouter un calque d’images

Le code suivant montre comment superposer l’image d’une [carte de Newark dans le New Jersey datant de 1922](https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg) à la carte principale. Cette image est ajoutée au dossier `drawable` du projet. Un calque d’image est créé en définissant l’image et les coordonnées de ses quatre angles au format `[Top Left Corner, Top Right Corner, Bottom Right Corner, Bottom Left Corner]`. L’ajout de calques d’image sous le calque `label` est souvent souhaitable.

::: zone pivot="programming-language-java-android"

```java
//Create an image layer.
ImageLayer layer = new ImageLayer(
    imageCoordinates(
        new Position[] {
            new Position(-74.22655, 40.773941), //Top Left Corner
            new Position(-74.12544, 40.773941), //Top Right Corner
            new Position(-74.12544, 40.712216), //Bottom Right Corner
            new Position(-74.22655, 40.712216)  //Bottom Left Corner
        }
    ),
    setImage(R.drawable.newark_nj_1922)
);

//Add the image layer to the map, below the label layer.
map.layers.add(layer, "labels");
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create an image layer.
val layer = ImageLayer(
    imageCoordinates(
        arrayOf<Position>(
            Position(-74.22655, 40.773941),  //Top Left Corner
            Position(-74.12544, 40.773941),  //Top Right Corner
            Position(-74.12544, 40.712216),  //Bottom Right Corner
            Position(-74.22655, 40.712216)   //Bottom Left Corner
        )
    ),
    setImage(R.drawable.newark_nj_1922)
)

//Add the image layer to the map, below the label layer.
map.layers.add(layer, "labels")
```

::: zone-end

Vous pouvez également spécifier l’URL d’une image hébergée en ligne. Toutefois, si votre scénario le permet, ajoutez l’image au dossier `drawable` de votre projet, qui se chargera plus rapidement parce que l’image sera disponible localement et ne nécessitera pas de téléchargement.

::: zone pivot="programming-language-java-android"

```java
//Create an image layer.
ImageLayer layer = new ImageLayer(
    imageCoordinates(
        new Position[] {
            new Position(-74.22655, 40.773941), //Top Left Corner
            new Position(-74.12544, 40.773941), //Top Right Corner
            new Position(-74.12544, 40.712216), //Bottom Right Corner
            new Position(-74.22655, 40.712216)  //Bottom Left Corner
        }
    ),
    setUrl("https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg")
);

//Add the image layer to the map, below the label layer.
map.layers.add(layer, "labels");
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create an image layer.
val layer = ImageLayer(
    imageCoordinates(
        arrayOf<Position>(
            Position(-74.22655, 40.773941),  //Top Left Corner
            Position(-74.12544, 40.773941),  //Top Right Corner
            Position(-74.12544, 40.712216),  //Bottom Right Corner
            Position(-74.22655, 40.712216) //Bottom Left Corner
        )
    ),
    setUrl("https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg")
)

//Add the image layer to the map, below the label layer.
map.layers.add(layer, "labels")
```

::: zone-end

La capture d’écran suivante montre une carte de Newark, New Jersey, de 1922, sur laquelle un calque d’image est superposé.

![Carte de Newark, New Jersey, de 1922, sur laquelle un calque d’image est superposé](media/map-add-image-layer-android/android-image-layer.gif)

## <a name="import-a-kml-file-as-ground-overlay"></a>Importer un fichier KML en tant que calque de relief

Cet exemple montre comment ajouter des informations de calque de relief KML comme couche d’image sur la carte. Les calques de relief KML fournissent les coordonnées des points cardinaux et une rotation dans le sens inverse des aiguilles d’une montre. Toutefois, la couche d’image attend des coordonnées pour chaque coin de l’image. Le calque de relief KML de cet exemple est celui de la cathédrale de Chartres et provient de [Wikimedia](https://commons.wikimedia.org/wiki/File:Chartres.svg/overlay.kml).

```xml
<?xml version="1.0" encoding="UTF-8"?>
<kml xmlns="http://www.opengis.net/kml/2.2" xmlns:gx="http://www.google.com/kml/ext/2.2" xmlns:kml="http://www.opengis.net/kml/2.2" xmlns:atom="http://www.w3.org/2005/Atom">
<GroundOverlay>
    <name>Map of Chartres cathedral</name>
    <Icon>
        <href>https://upload.wikimedia.org/wikipedia/commons/thumb/e/e3/Chartres.svg/1600px-Chartres.svg.png</href>
        <viewBoundScale>0.75</viewBoundScale>
    </Icon>
    <LatLonBox>
        <north>48.44820923628113</north>
        <south>48.44737203258976</south>
        <east>1.488833825534365</east>
        <west>1.486788581643038</west>
        <rotation>46.44067597839695</rotation>
    </LatLonBox>
</GroundOverlay>
</kml>
```

Le code utilise la méthode statique `getCoordinatesFromEdges` de la classe `ImageLayer`. Cette méthode calcule les quatre angles de l’image à partir des informations relatives aux points cardinaux et à la rotation issues de la superposition de sol de KML.

::: zone pivot="programming-language-java-android"

```java
//Calculate the corner coordinates of the ground overlay.
Position[] corners = ImageLayer.getCoordinatesFromEdges(
    //North, south, east, west
    48.44820923628113, 48.44737203258976, 1.488833825534365, 1.486788581643038,

    //KML rotations are counter-clockwise, subtract from 360 to make them clockwise.
    360 - 46.44067597839695
);

//Create an image layer.
ImageLayer layer = new ImageLayer(
    imageCoordinates(corners),
    setUrl("https://upload.wikimedia.org/wikipedia/commons/thumb/e/e3/Chartres.svg/1600px-Chartres.svg.png")
);

//Add the image layer to the map, below the label layer.
map.layers.add(layer, "labels");
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Calculate the corner coordinates of the ground overlay.
val corners: Array<Position> =
    ImageLayer.getCoordinatesFromEdges( //North, south, east, west
        48.44820923628113,
        48.44737203258976,
        1.488833825534365,
        1.486788581643038,  //KML rotations are counter-clockwise, subtract from 360 to make them clockwise.
        360 - 46.44067597839695
    )

//Create an image layer.
val layer = ImageLayer(
    imageCoordinates(corners),
    setUrl("https://upload.wikimedia.org/wikipedia/commons/thumb/e/e3/Chartres.svg/1600px-Chartres.svg.png")
)

//Add the image layer to the map, below the label layer.
map.layers.add(layer, "labels")
```

::: zone-end

La capture d’écran suivante montre une carte avec une superposition de sol de KML effectuée à l’aide d’un calque d’image.

![Carte avec une superposition de sol de KML effectuée à l’aide d’un calque d’image](media/map-add-image-layer-android/android-ground-overlay.jpg)

> [!TIP]
> Utilisez les méthodes `getPixels` et `getPositions` de la classe de calque d’image pour convertir les coordonnées géographiques du calque d’image positionné et les coordonnées en pixels de l’image locale.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les différentes manières de superposer des images sur une carte, consultez l’article suivant.

> [!div class="nextstepaction"]
> [Ajouter une couche de mosaïques](how-to-add-tile-layer-android-map.md)