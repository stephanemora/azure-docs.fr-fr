---
title: Gérer les événements cartographiques sur les cartes Android | Microsoft Azure Maps
description: Découvrez les événements qui sont déclenchés lorsque les utilisateurs interagissent avec des cartes. Affichez une liste de tous les événements cartographiques pris en charge. Découvrez comment utiliser le kit de développement logiciel (SDK) Android Azure Maps pour gérer les événements.
author: rbrundritt
ms.author: richbrun
ms.date: 2/26/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: 86d1b9ec8a507a5cfaa5502efcb239bceabca665
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102097344"
---
# <a name="interact-with-the-map-android-sdk"></a>Interagir avec la carte (SDK Android)

Cet article explique comment utiliser le gestionnaire des événements cartographiques.

## <a name="interact-with-the-map"></a>Interagir avec la carte

La carte gère tous les événements par le biais de sa propriété `events`. Le tableau suivant répertorie tous les événements cartographiques pris en charge.

| Événement                  | Format du gestionnaire d’événements | Description |
|------------------------|----------------------|-------------|
| `OnCameraIdle`         | `()`                 | <p>Déclenché après le rendu de la dernière image avant que la carte n’entre dans un état « inactif » :<ul><li>Aucune transition de caméra n’est en cours.</li><li>Toutes les vignettes actuellement demandées ont été chargées.</li><li>Toutes les animations de fondu/transition sont terminées.</li></ul></p> |
| `OnCameraMove`         | `()`                 | Déclenché à plusieurs reprises pendant une transition animée d’une vue à l’autre, en raison de l’interaction de l’utilisateur ou de méthodes. |
| `OnCameraMoveCanceled` | `()`                 | Déclenché lorsqu’une demande de mouvement faite à la caméra a été annulée. |
| `OnCameraMoveStarted`  | `(int reason)`       | Déclenché avant que la carte ne commence une transition animée d’une vue à l’autre, en raison de l’interaction de l’utilisateur ou de méthodes. L’argument `reason` de l’écouteur d’événements retourne une valeur entière qui fournit des détails sur la manière dont le mouvement de la caméra a été initié. Voici la liste des raisons possibles :<ul><li>1 : Mouvement</li><li>2 : Animation développeur</li><li>3 : Animation API</li></ul>   |
| `OnClick`              | `(double lat, double lon)` | Déclenché lorsque la carte est enfoncée et relâchée au même point sur la carte. |
| `OnFeatureClick`       | `(List<Feature>)`    | Déclenché lorsque la carte est enfoncée et relâchée au même point sur une fonctionnalité.  |
| `OnLayerAdded` | `(Layer layer)` | Déclenché lorsqu’une couche est ajoutée à la carte. |
| `OnLayerRemoved` | `(Layer layer)` | Déclenché lorsqu’une couche est supprimée de la carte. |
| `OnLoaded` | `()` | Déclenché immédiatement après que toutes les ressources nécessaires ont été téléchargées et que le premier rendu visuellement complet du mappage s’est produit. |
| `OnLongClick`          | `(double lat, double lon)` | Déclenché lorsque la carte est enfoncée, maintenue pendant un certain temps, puis relâchée au même point sur la carte. |
| `OnLongFeatureClick `  | `(List<Feature>)`    | Déclenché lorsque la carte est enfoncée, maintenue pendant un certain temps, puis relâchée au même point sur une fonctionnalité. |
| `OnReady`              | `(AzureMap map)`     | Déclenché lors du chargement initial de la carte ou en cas de modification de l’orientation de l’application et de chargement des ressources de mappage minimales alors que la carte est prête à être manipulée par programmation. |
| `OnSourceAdded` | `(Source source)` | Déclenché lorsqu’une `DataSource` ou `VectorTileSource` est ajoutée à la carte. |
| `OnSourceRemoved` | `(Source source)` | Déclenché lorsqu’une `DataSource` ou `VectorTileSource` est supprimée de la carte. |
| `OnStyleChange` | `()` | Déclenché lorsque le style de la carte est chargé ou modifié. |

Le code suivant montre comment ajouter les événements `OnClick`, `OnFeatureClick` et `OnCameraMove` à la carte.

::: zone pivot="programming-language-java-android"

```java
map.events.add((OnClick) (lat, lon) -> {
    //Map clicked.
});

map.events.add((OnFeatureClick) (features) -> {
    //Feature clicked.
});

map.events.add((OnCameraMove) () -> {
    //Map camera moved.
});
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
map.events.add(OnClick { lat: Double, lon: Double -> 
    //Map clicked.
})

map.events.add(OnFeatureClick { features: List<Feature?>? -> 
    //Feature clicked.
})

map.events.add(OnCameraMove {
    //Map camera moved.
})
```

::: zone-end

Pour plus d’informations, consultez la documentation [Navigation dans la carte](how-to-use-android-map-control-library.md#navigating-the-map) pour savoir comment interagir avec les événements de la carte et du déclencheur.

## <a name="scope-feature-events-to-layer"></a>Étendre les événements de fonctionnalité à la couche

Lors de l’ajout des événements `OnFeatureClick` ou `OnLongFeatureClick` à la carte, une instance de couche ou un ID de couche peuvent être transmis en tant que second paramètre. Quand une couche est transmise, l’événement se déclenche uniquement s’il se produit sur cette couche. Les événements inclus dans l’étendue de couches sont pris en charge par les couches de symboles, de bulles, de lignes et de polygones.

::: zone pivot="programming-language-java-android"

```java
//Create a data source.
DataSource source = new DataSource();
map.sources.add(source);

//Add data to the data source.
source.add(Point.fromLngLat(0, 0));

//Create a layer and add it to the map.
BubbleLayer layer = new BubbleLayer(source);
map.layers.add(layer);

//Add a feature click event to the map and pass the layer ID to limit the event to the specified layer.
map.events.add((OnFeatureClick) (features) -> {
    //One or more features clicked.
}, layer);

//Add a long feature click event to the map and pass the layer ID to limit the event to the specified layer.
map.events.add((OnLongFeatureClick) (features) -> {
    //One or more features long clicked.
}, layer);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a data source.
val source = DataSource()
map.sources.add(source)

//Add data to the data source.
source.add(Point.fromLngLat(0, 0))

//Create a layer and add it to the map.
val layer = BubbleLayer(source)
map.layers.add(layer)

//Add a feature click event to the map and pass the layer ID to limit the event to the specified layer.
map.events.add(
    OnFeatureClick { features: List<Feature?>? -> 
        //One or more features clicked.
    },
    layer
)

//Add a long feature click event to the map and pass the layer ID to limit the event to the specified layer.
map.events.add(
    OnLongFeatureClick { features: List<Feature?>? -> 
         //One or more features long clicked.
    },
    layer
)
```

::: zone-end

## <a name="next-steps"></a>Étapes suivantes

Pour voir des exemples de codes complets, consultez les articles suivants :

> [!div class="nextstepaction"]
> [Navigation dans la carte](how-to-use-android-map-control-library.md#navigating-the-map)

> [!div class="nextstepaction"]
> [Ajouter une couche de symboles](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Ajouter une couche de bulles](map-add-bubble-layer-android.md)

> [!div class="nextstepaction"]
> [Ajouter une couche de lignes](android-map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Ajouter une couche de polygones](how-to-add-shapes-to-android-map.md)
