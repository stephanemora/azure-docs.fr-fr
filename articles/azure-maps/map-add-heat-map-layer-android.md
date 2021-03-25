---
title: Ajouter une couche de carte thermique à des cartes Android | Microsoft Azure Maps
description: Découvrez comment créer une carte thermique. Découvrez comment utiliser le Kit de développement logiciel (SDK) Android Azure Maps pour ajouter une couche thermique à une carte. Découvrez comment personnaliser les couches thermiques.
author: rbrundritt
ms.author: richbrun
ms.date: 02/26/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: fce2c2d007f92c43e763826f9345f773324e885e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102100183"
---
# <a name="add-a-heat-map-layer-android-sdk"></a>Ajouter une couche de carte thermique (SDK Android)

Les cartes thermiques, également appelées « cartes de densité de points », sont un type de visualisation de données. Elles servent à représenter la densité de données à l’aide d’une palette de couleurs et affichent les « zones réactives » des données sur une carte. Les cartes thermiques sont un excellent moyen de restituer des jeux de données dotés d’un grand nombre de points. 

Le rendu de dizaines de milliers de points sous forme de symboles peut couvrir la plus grande partie de la surface de la carte. Dans ce cas, il est probable que de nombreux symboles se chevauchent. Il est alors difficile d’avoir une meilleure compréhension des données. Toutefois, la visualisation de ce même jeu de données sous forme de carte thermique permet de voir plus facilement où la densité ainsi que la densité relative de chaque point de données.

Vous pouvez utiliser des cartes thermiques dans de nombreux scénarios, notamment :

- **Données de température** : Fournit des approximations de la température entre deux points de données.
- **Données pour les capteurs de bruit** : Affiche non seulement l’intensité du bruit là où se trouve le capteur, mais fournit également un insight sur la dissipation du bruit avec la distance. Le niveau de bruit sur un site n’est peut-être pas élevé. Si les zones de couverture du bruit de plusieurs capteurs se chevauchent, il est possible que la zone de chevauchement obtienne des niveaux de bruit plus élevés. Ainsi, la zone de chevauchement serait visible sur la carte thermique.
- **Trace GPS** : Indique la vitesse sous forme de carte à hauteur pondérée, où l’intensité de chaque point de données est basée sur la vitesse. Par exemple, cette fonctionnalité permet de voir où un véhicule roulait plus vite.

> [!TIP]
> Les couches par défaut de la carte thermique restituent les coordonnées de toutes les données géométriques d’une source de données. Pour limiter la couche afin qu’elle n’affiche que les fonctionnalités de géométrie de point, définissez l’option `filter` de la couche sur `eq(geometryType(), "Point")`. Si vous souhaitez également inclure des caractéristiques MultiPoint, donnez-lui la valeur `any(eq(geometryType(), "Point"), eq(geometryType(), "MultiPoint"))`.

</br>

> [!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Heat-Maps-and-Image-Overlays-in-Azure-Maps/player?format=ny]

## <a name="prerequisites"></a>Prérequis

Veillez à suivre la procédure du document [Démarrage rapide : Création d’une application Android](quick-android-map.md). Les blocs de code de cet article peuvent être insérés dans le gestionnaire d’événements `onReady` des cartes.

## <a name="add-a-heat-map-layer"></a>Ajouter un calque de carte thermique

Pour restituer une source de données de points sous forme de carte thermique, transmettez cette source à une instance de la classe `HeatMapLayer` et ajoutez-la à la carte.

L’exemple de code suivant charge un flux GeoJSON de tremblements de terre de la semaine passée et les affiche sous forme de carte thermique. Chaque point de données est rendu avec un rayon de 10 pixels à tous les niveaux de zoom. Pour garantir une meilleure expérience utilisateur, la carte thermique se trouve sous de la couche d’étiquettes afin que les étiquettes restent clairement visibles. Les données de cet exemple sont issues du [USGS Earthquake Hazards Program](https://earthquake.usgs.gov/). Cet exemple charge des données GeoJSON à partir du web à l’aide du bloc de code de l’utilitaire d’importation de données fourni dans le document [Créer une source de données](create-data-source-android-sdk.md).

::: zone pivot="programming-language-java-android"

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a heat map layer.
HeatMapLayer layer = new HeatMapLayer(source,
  heatmapRadius(10f),
  heatmapOpacity(0.8f)
);

//Add the layer to the map, below the labels.
map.layers.add(layer, "labels");

//Import the geojson data and add it to the data source.
Utils.importData("https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson",
    this,
    (String result) -> {
        //Parse the data as a GeoJSON Feature Collection.
        FeatureCollection fc = FeatureCollection.fromJson(result);

        //Add the feature collection to the data source.
        source.add(fc);

        //Optionally, update the maps camera to focus in on the data.

        //Calculate the bounding box of all the data in the Feature Collection.
        BoundingBox bbox = MapMath.fromData(fc);

        //Update the maps camera so it is focused on the data.
        map.setCamera(
            bounds(bbox),
            padding(20));
    });
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a data source and add it to the map.
val source = DataSource()
map.sources.add(source)

//Create a heat map layer.
val layer = HeatMapLayer(
    source,
    heatmapRadius(10f),
    heatmapOpacity(0.8f)
)

//Add the layer to the map, below the labels.
map.layers.add(layer, "labels")

//Import the geojson data and add it to the data source.
Utils.importData("https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson",
    this
) { result: String? ->
    //Parse the data as a GeoJSON Feature Collection.
    val fc = FeatureCollection.fromJson(result!!)

    //Add the feature collection to the data source.
    source.add(fc)

    //Optionally, update the maps camera to focus in on the data.
    //Calculate the bounding box of all the data in the Feature Collection.
    val bbox = MapMath.fromData(fc)

    //Update the maps camera so it is focused on the data.
    map.setCamera(
        bounds(bbox),
        padding(20)
    )
}
```

::: zone-end

La capture d’écran suivante montre une carte chargeant une carte thermique à l’aide du code ci-dessus.

![Mapper avec la couche de carte thermique des tremblements de terre récents](media/map-add-heat-map-layer-android/android-heat-map-layer.png)

## <a name="customize-the-heat-map-layer"></a>Personnaliser la couche de carte thermique

Dans l’exemple précédent, la carte thermique a été personnalisée à l’aide des options de rayon et d’opacité. La couche de carte thermique offre plusieurs options de personnalisation, dont :

- `heatmapRadius`: définit un rayon de pixels dans lequel restituer chaque point de données. Vous pouvez définir le rayon en tant que nombre fixe ou expression. Il est possible, avec une expression, d’adapter le rayon en fonction du niveau de zoom, pour représenter une zone spatiale cohérente sur la carte (par exemple, un rayon de 5 miles).
- `heatmapColor`: spécifie la façon dont la carte thermique est colorisée. Un dégradé de couleur est une caractéristique courante des cartes thermiques. Vous pouvez obtenir l’effet grâce à une expression `interpolate`. Vous pouvez également utiliser une expression `step` pour coloriser la carte thermique décompose visuellement la densité en plages. Elle ressemble ainsi davantage à une carte de style de contours ou de radars. Ces palettes de couleurs permettent de définir une couleur pour toutes les valeurs comprises entre la valeur minimale et la valeur maximale de densité.

  Vous spécifiez les valeurs de couleurs des cartes thermiques comme expressions de valeur `heatmapDensity`. La couleur de la zone dans laquelle aucune donnée n’existe est définie au niveau de l’index 0 de l’expression « Interpolation » ou par la couleur par défaut d’une expression « en étapes ». Vous pouvez utiliser cette valeur pour définir une couleur d’arrière-plan. Souvent, cette valeur est définie sur une couleur noire transparente ou semi-transparente. 

  Voici des exemples d’expressions de couleurs :

  | Expression d’interpolation des couleurs | Expression d’échelle de couleurs |
  |--------------------------------|--------------------------|
  | interpolate(<br/>&nbsp;&nbsp;&nbsp;&nbsp;linear(), <br/>&nbsp;&nbsp;&nbsp;&nbsp;heatmapDensity(),<br/>&nbsp;&nbsp;&nbsp;&nbsp;stop(0, color(Color.TRANSPARENT)),<br/>&nbsp;&nbsp;&nbsp;&nbsp;stop(0.01, color(Color.MAGENTA)),<br/>&nbsp;&nbsp;&nbsp;&nbsp;stop(0.5, color(parseColor("#fb00fb"))),<br/>&nbsp;&nbsp;&nbsp;&nbsp;stop(1, color(parseColor("#00c3ff")))<br/>)` | step(<br/>&nbsp;&nbsp;&nbsp;&nbsp;heatmapDensity(),<br/>&nbsp;&nbsp;&nbsp;&nbsp;color(Color.TRANSPARENT),<br/>&nbsp;&nbsp;&nbsp;&nbsp;stop(0.01, color(parseColor("#000080"))),<br/>&nbsp;&nbsp;&nbsp;&nbsp;stop(0.25, color(parseColor("#000080"))),<br/>&nbsp;&nbsp;&nbsp;&nbsp;stop(0.5, color(Color.GREEN)),<br/>&nbsp;&nbsp;&nbsp;&nbsp;stop(0.5, color(Color.YELLOW)),<br/>&nbsp;&nbsp;&nbsp;&nbsp;stop(1, color(Color.RED))<br/>) |

- `heatmapOpacity`: spécifie le niveau d’opacité ou de transparence de la couche de carte thermique.
- `heatmapIntensity`: applique un multiplicateur à l’épaisseur de chaque point de données pour augmenter l’intensité globale de la carte thermique. Cela différencie l’épaisseur des points de données et facilite la visualisation.
- `heatmapWeight`: par défaut, tous les points de données ont une épaisseur de 1, et ont donc tous la même épaisseur. L’option d’épaisseur fait office de multiplicateur, et vous pouvez la définir en tant que nombre ou expression. Si un nombre est défini comme épaisseur, cela équivaut à placer deux fois chaque point de données sur la carte. Par exemple, si l’épaisseur est de `2`, alors la densité est doublée. Le fait de définir l’option d’épaisseur sur un nombre permet d’afficher la carte thermique comme vous le feriez à l’aide d’une option d’intensité.

  Toutefois, si vous utilisez une expression, le poids de chaque point de données peut être basé sur les propriétés de chaque point de données. Par exemple, supposons que chaque point de données représente un tremblement de terre. La valeur de magnitude est une métrique importante de chaque point de données de tremblement de terre. Des tremblements de terre se produisent constamment, mais la plupart d’entre eux ont une magnitude faible et ne sont pas remarqués. Utilisez la valeur de magnitude dans une expression pour attribuer l’épaisseur à chaque point de données. Le fait d’utiliser la valeur de magnitude pour attribuer une épaisseur vous donne une meilleure représentation de la précision des tremblements de terre sur la carte thermique.
- `minZoom` et `maxZoom` : La plage du niveau de zoom dans laquelle la couche doit être affichée.
- `filter` : Une expression de filtre utilisée pour limiter les données extraites de la source et représentées dans la couche.
- `sourceLayer` : Si la source de données connectée à la couche est une source de vignette vectorielle, une couche source dans les vignettes vectorielles doit être spécifiée.
- `visible` : Masque ou affiche la couche.

Voici un exemple de carte thermique dans laquelle une expression d’interpolation linéaire est utilisée pour créer un dégradé de couleur lisse. La propriété `mag` définie dans les données est utilisée avec une interpolation exponentielle pour définir la pondération ou la pertinence de chaque point de données.

::: zone pivot="programming-language-java-android"

```java
HeatMapLayer layer = new HeatMapLayer(source,
    heatmapRadius(10f),

    //A linear interpolation is used to create a smooth color gradient based on the heat map density.
    heatmapColor(
        interpolate(
            linear(),
            heatmapDensity(),
            stop(0, color(Color.TRANSPARENT)),
            stop(0.01, color(Color.BLACK)),
            stop(0.25, color(Color.MAGENTA)),
            stop(0.5, color(Color.RED)),
            stop(0.75, color(Color.YELLOW)),
            stop(1, color(Color.WHITE))
        )
    ),

    //Using an exponential interpolation since earthquake magnitudes are on an exponential scale.
    heatmapWeight(
       interpolate(
            exponential(2),
            get("mag"),
            stop(0,0),

            //Any earthquake above a magnitude of 6 will have a weight of 1
            stop(6, 1)
       )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = HeatMapLayer(source,
    heatmapRadius(10f),

    //A linear interpolation is used to create a smooth color gradient based on the heat map density.
    heatmapColor(
        interpolate(
            linear(),
            heatmapDensity(),
            stop(0, color(Color.TRANSPARENT)),
            stop(0.01, color(Color.BLACK)),
            stop(0.25, color(Color.MAGENTA)),
            stop(0.5, color(Color.RED)),
            stop(0.75, color(Color.YELLOW)),
            stop(1, color(Color.WHITE))
        )
    ),

    //Using an exponential interpolation since earthquake magnitudes are on an exponential scale.
    heatmapWeight(
       interpolate(
            exponential(2),
            get("mag"),
            stop(0,0),

            //Any earthquake above a magnitude of 6 will have a weight of 1
            stop(6, 1)
       )
    )
)
```

::: zone-end

La capture d’écran suivante montre la couche de carte thermique personnalisée ci-dessus à l’aide des mêmes données de l’exemple de carte thermique précédent.

![Mapper avec la couche de carte thermique personnalisée des tremblements de terre récents](media/map-add-heat-map-layer-android/android-custom-heat-map-layer.png)

## <a name="consistent-zoomable-heat-map"></a>Carte thermique homogène pouvant faire l’objet d’un zoom

Par défaut, les rayons des points de données restitués dans la couche de carte thermique ont un rayon de pixels fixe pour tous les niveaux de zoom. Lorsque vous zoomez sur la carte, les données sont agrégées, et la couche de carte thermique change d’aspect. La vidéo suivante montre le comportement par défaut de la carte thermique où elle conserve un rayon de pixels lors du zoom sur la carte.

![Animation montrant une carte avec une couche de carte thermique présentant une taille de pixel cohérente](media/map-add-heat-map-layer-android/android-heat-map-layer-zoom.gif)

Utilisez une expression `zoom` pour mettre à l’échelle le rayon de chaque niveau de zoom de sorte que chaque point de données couvre la même zone physique de la carte. Cette expression fait paraître la couche de carte thermique plus statique et plus cohérente. Chaque niveau de zoom de la carte a deux fois plus de pixels verticalement et horizontalement que le niveau de zoom précédent.

La mise à l’échelle du rayon de sorte qu’il soit multiplié par deux avec chaque niveau de zoom crée une carte thermique qui paraît cohérente sur tous les niveaux de zoom. Pour appliquer cette mise à l’échelle, utilisez `zoom` avec une expression `exponential interpolation` de base 2, avec le rayon de pixels défini pour le niveau de zoom minimal et un rayon mis à l’échelle pour le niveau de zoom maximal calculé en tant que `2 * Math.pow(2, minZoom - maxZoom)` comme indiqué dans l’exemple suivant. Effectuez un zoom sur la carte pour voir comment la carte thermique évolue avec le niveau de zoom.

::: zone pivot="programming-language-java-android"

```java
HeatMapLayer layer = new HeatMapLayer(source,
  heatmapRadius(
    interpolate(
      exponential(2),
      zoom(),

      //For zoom level 1 set the radius to 2 pixels.
      stop(1, 2f),

      //Between zoom level 1 and 19, exponentially scale the radius from 2 pixels to 2 * (maxZoom - minZoom)^2 pixels.
      stop(19, Math.pow(2, 19 - 1) * 2f)
    )
  ),
  heatmapOpacity(0.75f)
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = HeatMapLayer(source,
  heatmapRadius(
    interpolate(
      exponential(2),
      zoom(),

      //For zoom level 1 set the radius to 2 pixels.
      stop(1, 2f),

      //Between zoom level 1 and 19, exponentially scale the radius from 2 pixels to 2 * (maxZoom - minZoom)^2 pixels.
      stop(19, Math.pow(2.0, 19 - 1.0) * 2f)
    )
  ),
  heatmapOpacity(0.75f)
)
```

::: zone-end

La vidéo suivante montre une carte exécutant le code ci-dessus, qui met à l’échelle le rayon pendant que la carte est en cours de zoom pour créer un rendu de carte thermique cohérent entre les niveaux de zoom.

![Animation montrant une carte avec une couche de carte thermique présentant une taille géospatiale cohérente](media/map-add-heat-map-layer-android/android-consistent-zoomable-heat-map-layer.gif)

## <a name="next-steps"></a>Étapes suivantes

Pour consulter plus d’exemples de code à ajouter à vos cartes, consultez les articles suivants :

> [!div class="nextstepaction"]
> [Créer une source de données](create-data-source-android-sdk.md)

> [!div class="nextstepaction"]
> [Utiliser des expressions de style basées sur les données](data-driven-style-expressions-android-sdk.md)
