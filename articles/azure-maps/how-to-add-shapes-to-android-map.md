---
title: Ajout d’une couche de polygones à des cartes Android | Microsoft Azure Maps
description: Découvrez comment ajouter des polygones ou des cercles aux cartes. Découvrez comment utiliser Azure Maps Android SDK pour personnaliser les formes géométriques et les rendre plus faciles à mettre à jour et à gérer.
author: rbrundritt
ms.author: richbrun
ms.date: 12/08/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 1712cedab9cef23108fcc48b8e09bdc3e33065c4
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/18/2020
ms.locfileid: "97679487"
---
# <a name="add-a-polygon-layer-to-the-map-android-sdk"></a>Ajout d’une couche de polygones à la carte (Android SDK)

Cet article vous montre comment restituer des zones des géométries de fonctionnalité `Polygon` et `MultiPolygon` sur la carte à l’aide d’une couche de polygones.

## <a name="prerequisites"></a>Conditions préalables requises

Veillez à suivre la procédure du document [Démarrage rapide : Création d’une application Android](quick-android-map.md). Les blocs de code de cet article peuvent être insérés dans le gestionnaire d’événements `onReady` des cartes.

## <a name="use-a-polygon-layer"></a>Utiliser une couche de polygones

Lorsqu’une couche de polygones est connectée à une source de données et chargée sur la carte, elle affiche la zone avec les entités `Polygon` et `MultiPolygon`. Pour créer un polygone, ajoutez-le à une source de données et affichez-le avec une couche de polygones à l’aide de la classe `PolygonLayer`.

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a rectangular polygon.
source.add(Polygon.fromLngLats(
    Arrays.asList(
        Arrays.asList(
            Point.fromLngLat(-73.98235, 40.76799),
            Point.fromLngLat(-73.95785, 40.80044),
            Point.fromLngLat(-73.94928, 40.79680),
            Point.fromLngLat(-73.97317, 40.76437),
            Point.fromLngLat(-73.98235, 40.76799)
        )
    )
));

//Create and add a polygon layer to render the polygon on the map, below the label layer.
map.layers.add(new PolygonLayer(source, 
    fillColor("red"),
    fillOpacity(0.7f)
), "labels");
```

La capture d’écran suivante montre le rendu, obtenu avec le code ci-dessus, de la zone d’un polygone à l’aide d’une couche de polygones.

![Polygone avec zone de remplissage](media/how-to-add-shapes-to-android-map/android-polygon-layer.png)

## <a name="use-a-polygon-and-line-layer-together"></a>Utiliser une couche de polygones et une couche de lignes ensemble

Une couche de lignes est utilisée pour restituer le contour de polygones. L’exemple de code suivant restitue un polygone comme l’exemple précédent, mais ajoute à présent une couche de lignes. Cette couche de lignes est une deuxième couche connectée à la source de données.  

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a rectangular polygon.
source.add(Polygon.fromLngLats(
    Arrays.asList(
        Arrays.asList(
            Point.fromLngLat(-73.98235, 40.76799),
            Point.fromLngLat(-73.95785, 40.80044),
            Point.fromLngLat(-73.94928, 40.79680),
            Point.fromLngLat(-73.97317, 40.76437),
            Point.fromLngLat(-73.98235, 40.76799)
        )
    )
));

//Create and add a polygon layer to render the polygon on the map, below the label layer.
map.layers.add(new PolygonLayer(source,
    fillColor("rgba(0, 200, 200, 0.5)")
), "labels");

//Create and add a line layer to render the outline of the polygon.
map.layers.add(new LineLayer(source,
    strokeColor("red"),
    strokeWidth(2f)
));
```

La capture d’écran suivante montre le rendu, obtenu avec le code ci-dessus, d’un polygone avec son contour restitué à l’aide d’une couche de lignes.

![Polygone avec zone de remplissage et contour](media/how-to-add-shapes-to-android-map/android-polygon-and-line-layer.png)

> [!TIP]
> Lorsque vous dessinez le contour d’un polygone avec une couche de lignes, veillez à fermer tous les anneaux du polygone de sorte que chaque tableau de points ait les mêmes points de début et de fin. Sinon, la couche de lignes ne risquerait de ne pas relier le dernier point du polygone au premier.

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir plus d’exemples de code à ajouter à vos cartes, consultez les articles suivants :

> [!div class="nextstepaction"]
> [Créer une source de données](create-data-source-android-sdk.md)

> [!div class="nextstepaction"]
> [Utiliser des expressions de style basées sur les données](data-driven-style-expressions-android-sdk.md)

> [!div class="nextstepaction"]
> [Ajouter une couche de lignes](android-map-add-line-layer.md)
