---
title: Ajouter une couche de lignes à des cartes Android | Microsoft Azure Maps
description: Découvrez comment ajouter des lignes aux cartes. Consultez les exemples qui utilisent le Kit de développement logiciel (SDK) Android Azure Maps pour ajouter des couches de lignes aux cartes et pour personnaliser les lignes avec des symboles et des dégradés de couleurs.
author: rbrundritt
ms.author: richbrun
ms.date: 2/26/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: ff071d03e00a0380d1ab6642828b0940931d3302
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102097446"
---
# <a name="add-a-line-layer-to-the-map-android-sdk"></a>Ajouter une couche de lignes à la carte (SDK Android)

Vous pouvez utiliser un calque de lignes pour afficher des caractéristiques `LineString` et `MultiLineString` sous forme de chemins ou d’itinéraires sur la carte. Vous pouvez aussi utiliser un calque de lignes pour afficher le contour de caractéristiques `Polygon` et `MultiPolygon`. Une source de données est connectée à une couche de lignes afin de lui fournir des données à afficher.

> [!TIP]
> Les couches de lignes par défaut affichent les coordonnées de polygones ainsi que de lignes dans une source de données. Pour limiter la couche afin qu’elle n’affiche que les fonctionnalités de géométrie de LineString, définissez l’option `filter` de la couche sur `eq(geometryType(), "LineString")`. Si vous souhaitez également inclure des fonctionnalités MultiLineString, réglez l’option `filter` de la couche sur `any(eq(geometryType(), "LineString"), eq(geometryType(), "MultiLineString"))`.

## <a name="prerequisites"></a>Prérequis

Veillez à suivre la procédure du document [Démarrage rapide : Création d’une application Android](quick-android-map.md). Les blocs de code de cet article peuvent être insérés dans le gestionnaire d’événements `onReady` des cartes.

## <a name="add-a-line-layer"></a>Ajouter une couche de lignes

Le code suivant montre comment créer une ligne. Ajoutez la ligne à une source de données, puis affichez-la avec une couche de lignes à l’aide de la classe `LineLayer`.

::: zone pivot="programming-language-java-android"

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a list of points.
List<Point> points = Arrays.asList(
    Point.fromLngLat(-73.97234, 40.74327),
    Point.fromLngLat(-74.00442, 40.75680));

//Create a LineString geometry and add it to the data source.
source.add(LineString.fromLngLats(points));

//Create a line layer and add it to the map.
LineLayer layer = new LineLayer(source,
    strokeColor("blue"),
    strokeWidth(5f)
);

map.layers.add(layer);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a data source and add it to the map.
val source = DataSource()
map.sources.add(source)

//Create a list of points.
val points = asList(
    Point.fromLngLat(-73.97234, 40.74327),
    Point.fromLngLat(-74.00442, 40.75680)
)

//Create a LineString geometry and add it to the data source.
source.add(LineString.fromLngLats(points))

//Create a line layer and add it to the map.
val layer = LineLayer(
    source,
    strokeColor("blue"),
    strokeWidth(5f)
)

map.layers.add(layer)
```

::: zone-end

La capture d’écran suivante montre le code ci-dessus restituant une ligne dans une couche de lignes.

![Mapper avec une ligne affichée à l’aide d’une couche de lignes](media/android-map-add-line-layer/android-line-layer.png)

## <a name="data-driven-line-style"></a>Style de ligne piloté par les données

Le code suivant crée deux fonctionnalités de ligne et ajoute une valeur de limite de vitesse en tant que propriété à chaque ligne. Une couche de lignes utilise une expression de style reposant sur les données pour colorer les lignes en fonction de la valeur de limite de vitesse. Comme les données de ligne sont superposées sur des routes, le code ci-dessous ajoute la couche de lignes sous la couche d’étiquettes afin que les étiquettes de route soient toujours lisibles.

::: zone pivot="programming-language-java-android"

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a line feature.
Feature feature = Feature.fromGeometry(
    LineString.fromLngLats(Arrays.asList(
        Point.fromLngLat(-122.131821, 47.704033),
        Point.fromLngLat(-122.099919, 47.703678))));

//Add a property to the feature.
feature.addNumberProperty("speedLimitMph", 35);

//Add the feature to the data source.
source.add(feature);

//Create a second line feature.
Feature feature2 = Feature.fromGeometry(
    LineString.fromLngLats(Arrays.asList(
        Point.fromLngLat(-122.126662, 47.708265),
        Point.fromLngLat(-122.126877, 47.703980))));

//Add a property to the second feature.
feature2.addNumberProperty("speedLimitMph", 15);

//Add the second feature to the data source.
source.add(feature2);

//Create a line layer and add it to the map.
LineLayer layer = new LineLayer(source,
    strokeColor(
        interpolate(
            linear(),
            get("speedLimitMph"),
            stop(0, color(Color.GREEN)),
            stop(30, color(Color.YELLOW)),
            stop(60, color(Color.RED))
        )
    ),
    strokeWidth(5f)
);

map.layers.add(layer, "labels");
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a data source and add it to the map.
val source = DataSource()
map.sources.add(source)

//Create a line feature.
val feature = Feature.fromGeometry(
    LineString.fromLngLats(
        Arrays.asList(
            Point.fromLngLat(-122.131821, 47.704033),
            Point.fromLngLat(-122.099919, 47.703678)
        )
    )
)

//Add a property to the feature.
feature.addNumberProperty("speedLimitMph", 35)

//Add the feature to the data source.
source.add(feature)

//Create a second line feature.
val feature2 = Feature.fromGeometry(
    LineString.fromLngLats(
        Arrays.asList(
            Point.fromLngLat(-122.126662, 47.708265),
            Point.fromLngLat(-122.126877, 47.703980)
        )
    )
)

//Add a property to the second feature.
feature2.addNumberProperty("speedLimitMph", 15)

//Add the second feature to the data source.
source.add(feature2)

//Create a line layer and add it to the map.
val layer = LineLayer(
    source,
    strokeColor(
        interpolate(
            linear(),
            get("speedLimitMph"),
            stop(0, color(Color.GREEN)),
            stop(30, color(Color.YELLOW)),
            stop(60, color(Color.RED))
        )
    ),
    strokeWidth(5f)
)

map.layers.add(layer, "labels")
```

::: zone-end

La capture d’écran suivante montre le code ci-dessus représentant deux lignes dans une couche de lignes avec leur couleur récupérée à partir d’une expression de style reposant sur les données en fonction d’une propriété dans les fonctionnalités de ligne.

![Mapper avec les lignes mises en forme en fonction des données représentées dans une couche de lignes](media/android-map-add-line-layer/android-line-layer-data-drive-style.png)

## <a name="add-a-stroke-gradient-to-a-line"></a>Ajouter un dégradé de traits à une ligne

Vous pouvez appliquer une couleur de trait à une ligne. Vous pouvez également remplir une ligne avec un dégradé de couleurs pour représenter la transition d’un segment de ligne au segment suivant. Par exemple, les dégradés de ligne peuvent être utilisés pour représenter des changements dans le temps et la distance, ou différentes températures sur une ligne d’objets connectés. Pour appliquer cette fonctionnalité à une ligne, la source de données doit avoir l’option `lineMetrics` définie sur `true`. Une expression de dégradé de couleurs peut alors être transmise à l’option `strokeColor` de la ligne. L’expression de dégradé de traits a pour référence l’expression de données `lineProgress` qui expose les métriques de ligne calculées à l’expression.

::: zone pivot="programming-language-java-android"

```java
//Create a data source and add it to the map.
DataSource source = new DataSource(
    //Enable line metrics on the data source. This is needed to enable support for strokeGradient.
    withLineMetrics(true)
);
map.sources.add(source);

//Create a line and add it to the data source.
source.add(LineString.fromLngLats(
    Arrays.asList(
        Point.fromLngLat(-122.18822, 47.63208),
        Point.fromLngLat(-122.18204, 47.63196),
        Point.fromLngLat(-122.17243, 47.62976),
        Point.fromLngLat(-122.16419, 47.63023),
        Point.fromLngLat(-122.15852, 47.62942),
        Point.fromLngLat(-122.15183, 47.62988),
        Point.fromLngLat(-122.14256, 47.63451),
        Point.fromLngLat(-122.13483, 47.64041),
        Point.fromLngLat(-122.13466, 47.64422),
        Point.fromLngLat(-122.13844, 47.65440),
        Point.fromLngLat(-122.13277, 47.66515),
        Point.fromLngLat(-122.12779, 47.66712),
        Point.fromLngLat(-122.11595, 47.66712),
        Point.fromLngLat(-122.11063, 47.66735),
        Point.fromLngLat(-122.10668, 47.67035),
        Point.fromLngLat(-122.10565, 47.67498)
    )
));

//Create a line layer and pass in a gradient expression for the strokeGradient property.
map.layers.add(new LineLayer(source,
    strokeWidth(6f),

    //Pass an interpolate or step expression that represents a gradient.
    strokeGradient(
        interpolate(
            linear(),
            lineProgress(),
            stop(0, color(Color.BLUE)),
            stop(0.1, color(Color.argb(255, 65, 105, 225))), //Royal Blue
            stop(0.3, color(Color.CYAN)),
            stop(0.5, color(Color.argb(255,0, 255, 0))), //Lime
            stop(0.7, color(Color.YELLOW)),
            stop(1, color(Color.RED))
        )
    )
));
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a data source and add it to the map.
val source = DataSource(
    //Enable line metrics on the data source. This is needed to enable support for strokeGradient.
    withLineMetrics(true)
)
map.sources.add(source)

//Create a line and add it to the data source.
source.add(
    LineString.fromLngLats(
        Arrays.asList(
            Point.fromLngLat(-122.18822, 47.63208),
            Point.fromLngLat(-122.18204, 47.63196),
            Point.fromLngLat(-122.17243, 47.62976),
            Point.fromLngLat(-122.16419, 47.63023),
            Point.fromLngLat(-122.15852, 47.62942),
            Point.fromLngLat(-122.15183, 47.62988),
            Point.fromLngLat(-122.14256, 47.63451),
            Point.fromLngLat(-122.13483, 47.64041),
            Point.fromLngLat(-122.13466, 47.64422),
            Point.fromLngLat(-122.13844, 47.65440),
            Point.fromLngLat(-122.13277, 47.66515),
            Point.fromLngLat(-122.12779, 47.66712),
            Point.fromLngLat(-122.11595, 47.66712),
            Point.fromLngLat(-122.11063, 47.66735),
            Point.fromLngLat(-122.10668, 47.67035),
            Point.fromLngLat(-122.10565, 47.67498)
        )
    )
)

//Create a line layer and pass in a gradient expression for the strokeGradient property.
map.layers.add(
    LineLayer(
        source,
        strokeWidth(6f),  

        //Pass an interpolate or step expression that represents a gradient.
        strokeGradient(
            interpolate(
                linear(),
                lineProgress(),
                stop(0, color(Color.BLUE)),
                stop(0.1, color(Color.argb(255, 65, 105, 225))),  //Royal Blue
                stop(0.3, color(Color.CYAN)),
                stop(0.5, color(Color.argb(255, 0, 255, 0))),  //Lime
                stop(0.7, color(Color.YELLOW)),
                stop(1, color(Color.RED))
            )
        )
    )
)
```

::: zone-end

La capture d’écran suivante montre le code ci-dessus affichant une ligne rendue à l’aide d’une couleur de trait de dégradée.

![Mapper avec une ligne rendue sous la forme d’un tracé en dégradé dans une couche de lignes](media/android-map-add-line-layer/android-line-layer-gradient.jpg)

## <a name="add-symbols-along-a-line"></a>Ajouter des symboles le long d’une ligne

Cet exemple montre comment ajouter des icônes en forme de flèches le long d’une ligne sur la carte. Lorsque vous utilisez une couche de symboles, affectez à l’option `symbolPlacement` la valeur `SymbolPlacement.LINE`. Cette option affiche les symboles le long de la ligne et fait pivoter les icônes (0 degré = droite).

::: zone pivot="programming-language-java-android"

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Load a image of an arrow into the map image sprite and call it "arrow-icon".
map.images.add("arrow-icon", R.drawable.purple_arrow_right);

//Create and add a line to the data source.
source.add(LineString.fromLngLats(Arrays.asList(
    Point.fromLngLat(-122.18822, 47.63208),
    Point.fromLngLat(-122.18204, 47.63196),
    Point.fromLngLat(-122.17243, 47.62976),
    Point.fromLngLat(-122.16419, 47.63023),
    Point.fromLngLat(-122.15852, 47.62942),
    Point.fromLngLat(-122.15183, 47.62988),
    Point.fromLngLat(-122.14256, 47.63451),
    Point.fromLngLat(-122.13483, 47.64041),
    Point.fromLngLat(-122.13466, 47.64422),
    Point.fromLngLat(-122.13844, 47.65440),
    Point.fromLngLat(-122.13277, 47.66515),
    Point.fromLngLat(-122.12779, 47.66712),
    Point.fromLngLat(-122.11595, 47.66712),
    Point.fromLngLat(-122.11063, 47.66735),
    Point.fromLngLat(-122.10668, 47.67035),
    Point.fromLngLat(-122.10565, 47.67498)))
);

//Create a line layer and add it to the map.
map.layers.add(new LineLayer(source,
    strokeColor("DarkOrchid"),
    strokeWidth(5f)
));

//Create a symbol layer and add it to the map.
map.layers.add(new SymbolLayer(source,
    //Space symbols out along line.
    symbolPlacement(SymbolPlacement.LINE),

    //Spread the symbols out 100 pixels apart.
    symbolSpacing(100f),
    
    //Use the arrow icon as the symbol.
    iconImage("arrow-icon"),

    //Allow icons to overlap so that they aren't hidden if they collide with other map elements.
    iconAllowOverlap(true),

    //Center the symbol icon.
    iconAnchor(AnchorType.CENTER),

    //Scale the icon size.
    iconSize(0.8f)
));
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a data source and add it to the map.
val source = DataSource()
map.sources.add(source)

//Load a image of an arrow into the map image sprite and call it "arrow-icon".
map.images.add("arrow-icon", R.drawable.purple_arrow_right)

//Create and add a line to the data source.
//Create and add a line to the data source.
source.add(
    LineString.fromLngLats(
        Arrays.asList(
            Point.fromLngLat(-122.18822, 47.63208),
            Point.fromLngLat(-122.18204, 47.63196),
            Point.fromLngLat(-122.17243, 47.62976),
            Point.fromLngLat(-122.16419, 47.63023),
            Point.fromLngLat(-122.15852, 47.62942),
            Point.fromLngLat(-122.15183, 47.62988),
            Point.fromLngLat(-122.14256, 47.63451),
            Point.fromLngLat(-122.13483, 47.64041),
            Point.fromLngLat(-122.13466, 47.64422),
            Point.fromLngLat(-122.13844, 47.65440),
            Point.fromLngLat(-122.13277, 47.66515),
            Point.fromLngLat(-122.12779, 47.66712),
            Point.fromLngLat(-122.11595, 47.66712),
            Point.fromLngLat(-122.11063, 47.66735),
            Point.fromLngLat(-122.10668, 47.67035),
            Point.fromLngLat(-122.10565, 47.67498)
        )
    )
)

//Create a line layer and add it to the map.
map.layers.add(
    LineLayer(
        source,
        strokeColor("DarkOrchid"),
        strokeWidth(5f)
    )
)

//Create a symbol layer and add it to the map.
map.layers.add(
    SymbolLayer(
        source,  //Space symbols out along line.
        symbolPlacement(SymbolPlacement.LINE),  //Spread the symbols out 100 pixels apart.
        symbolSpacing(100f),  //Use the arrow icon as the symbol.
        iconImage("arrow-icon"),  //Allow icons to overlap so that they aren't hidden if they collide with other map elements.
        iconAllowOverlap(true),  //Center the symbol icon.
        iconAnchor(AnchorType.CENTER),  //Scale the icon size.
        iconSize(0.8f)
    )
)
```

::: zone-end

Pour cet exemple, l’image suivante a été chargée dans le dossier drawable de l’application.

| ![Image d’icône de flèche violette](media/android-map-add-line-layer/purple-arrow-right.png)|
|:-----------------------------------------------------------------------:|
|                           `purple_arrow_right.png`                       |

La capture d’écran ci-dessous montre le code ci-dessus affichant une ligne avec des icônes de flèches affichées tout le long.

![Mapper avec les lignes mises en forme en fonction des données avec des flèches représentées dans une couche de lignes](media/android-map-add-line-layer/android-symbols-along-line-path.png)

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir plus d’exemples de code à ajouter à vos cartes, consultez les articles suivants :

> [!div class="nextstepaction"]
> [Créer une source de données](create-data-source-android-sdk.md)

> [!div class="nextstepaction"]
> [Utiliser des expressions de style basées sur les données](data-driven-style-expressions-android-sdk.md)

> [!div class="nextstepaction"]
> [Ajouter une couche de polygones](how-to-add-shapes-to-android-map.md)
