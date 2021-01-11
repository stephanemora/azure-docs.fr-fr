---
title: Ajouter un calque de bulles à des cartes Android | Microsoft Azure Maps
description: Découvrez comment restituer des points sur des cartes en tant que cercles avec des tailles fixes. Découvrez comment utiliser l’Android SDK Azure Maps pour ajouter et personnaliser des calques de bulles à cet effet.
author: rbrundritt
ms.author: richbrun
ms.date: 12/07/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 7506a2083a34832ee3f6f6222f86d35d10228728
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/18/2020
ms.locfileid: "97681319"
---
# <a name="add-a-bubble-layer-to-a-map-android-sdk"></a>Ajouter un calque de bulles à une carte (Android SDK)

Cet article vous montre comment afficher des données de point sur une carte à partir d’une source de données telle qu’un calque de bulles sur une carte. Les couches de bulles affichent sur la carte des points sous la forme de cercles, avec un rayon de pixels fixe.

> [!TIP]
> Les couches de bulles par défaut affichent les coordonnées de toutes les données géométriques d’une source de données. Pour limiter le calque afin qu’il n’affiche que les fonctionnalités de géométrie de point, définissez l’option `filter` du calque sur `eq(geometryType(), "Point")`. Si vous souhaitez également inclure des fonctionnalités MultiPoint, réglez l’option `filter` du calque sur `any(eq(geometryType(), "Point"), eq(geometryType(), "MultiPoint"))`.

## <a name="prerequisites"></a>Prérequis

Veillez à suivre les étapes décrites dans le [Démarrage rapide : Document Créer une application Android](quick-android-map.md). Les blocs de code de cet article peuvent être insérés dans le gestionnaire d’événements `onReady` des cartes.

## <a name="add-a-bubble-layer"></a>Ajouter un calque de bulles

Le code suivant charge un tableau de points dans une source de données. Ensuite, il connecte les points de données à une couche de bulles. La couche de bulles affiche le rayon de chaque bulle avec cinq pixels et le blanc comme couleur de remplissage. Il utilise également le bleu comme couleur de trait et une largeur de trait de six pixels.

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create point locations.
Point[] points = new Point[] {
    Point.fromLngLat(-73.985708, 40.75773),
    Point.fromLngLat(-73.985600, 40.76542),
    Point.fromLngLat(-73.985550, 40.77900),
    Point.fromLngLat(-73.975550, 40.74859),
    Point.fromLngLat(-73.968900, 40.78859)
};

//Add multiple points to the data source.
source.add(points);

//Create a bubble layer to render the filled in area of the circle, and add it to the map.
BubbleLayer layer = new BubbleLayer(source, 
    bubbleRadius(5f),
    bubbleColor("white"),
    bubbleStrokeColor("#4288f7"),
    bubbleStrokeWidth(6f)
);

map.layers.add(layer);
```

La capture d’écran suivante montre le code ci-dessus restituant des points dans un calque de bulles.

![Carte avec points restitués par le calque de bulles](media/map-add-bubble-layer-android/android-bubble-layer.png)

## <a name="show-labels-with-a-bubble-layer"></a>Afficher les étiquettes avec une couche de bulles

Ce code montre comment utiliser une couche de bulles pour afficher un point sur la carte. Il montre également comment utiliser une couche de symboles pour afficher une étiquette. Pour masquer l’icône de la couche de symboles, définissez l’option `iconImage` sur `"none"`.

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Add a data point to the map.
source.add(Point.fromLngLat(-122.336641,47.627631));

//Add a bubble layer.
map.layers.add(new BubbleLayer(source,
    bubbleRadius(5f),
    bubbleColor("white"),
    bubbleStrokeColor("#4288f7"),
    bubbleStrokeWidth(6f)
));

//Add a symbol layer to display text, hide the icon image.
map.layers.add(new SymbolLayer(source,
    //Hide the icon image.
    iconImage("none"),
    textField("Museum of History & Industry (MOHAI)"),
    textColor("#005995"),
    textOffset(new Float[]{0f, -2.2f})
));
```

La capture d’écran suivante montre le code ci-dessus restituant un point dans un calque de bulles et une étiquette de texte pour le point avec une couche de symboles.

![Carte avec point restitué à l’aide d’un calque de bulles et une étiquette de texte avec une couche de symboles](media/map-add-bubble-layer-android/android-bubble-symbol-layer.png)

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir plus d’exemples de code à ajouter à vos cartes, consultez les articles suivants :

> [!div class="nextstepaction"]
> [Créer une source de données](create-data-source-android-sdk.md)

> [!div class="nextstepaction"]
> [Ajouter une couche de symboles](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Utiliser des expressions de style basées sur les données](data-driven-style-expressions-android-sdk.md)

> [!div class="nextstepaction"]
> [Afficher des informations sur les caractéristiques](display-feature-information-android.md)
