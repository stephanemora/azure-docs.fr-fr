---
title: Gérer les événements cartographiques sur les cartes Android | Microsoft Azure Maps
description: Découvrez les événements qui sont déclenchés lorsque les utilisateurs interagissent avec des cartes. Affichez une liste de tous les événements cartographiques pris en charge. Découvrez comment utiliser le kit de développement logiciel (SDK) Android Azure Maps pour gérer les événements.
author: rbrundritt
ms.author: richbrun
ms.date: 12/08/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 818d33947fa079a130c3009a34dcb9b72ad52f91
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/18/2020
ms.locfileid: "97681263"
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
| `OnLongClick`          | `(double lat, double lon)` | Déclenché lorsque la carte est enfoncée, maintenue pendant un certain temps, puis relâchée au même point sur la carte. |
| `OnLongFeatureClick `  | `(List<Feature>)`    | Déclenché lorsque la carte est enfoncée, maintenue pendant un certain temps, puis relâchée au même point sur une fonctionnalité. |
| `OnReady`              | `(AzureMap map)`     | Déclenché lors du chargement initial de la carte ou en cas de modification de l’orientation de l’application et de chargement des ressources de mappage minimales alors que la carte est prête à être manipulée par programmation. |

Le code suivant montre comment ajouter les événements `OnClick`, `OnFeatureClick` et `OnCameraMove` à la carte.

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

Pour plus d’informations, consultez la documentation [Navigation dans la carte](how-to-use-android-map-control-library.md#navigating-the-map) pour savoir comment interagir avec les événements de la carte et du déclencheur.

## <a name="scope-feature-events-to-layer"></a>Étendre les événements de fonctionnalité à la couche

Lors de l’ajout des événements `OnFeatureClick` ou `OnLongFeatureClick` à la carte, un ID de couche peut être transmis en tant que second paramètre. Lorsqu’un ID de couche est transmis, l’événement se déclenche uniquement s’il se produit sur cette couche. Les événements étendus aux couches sont pris en charge par les couches de symboles, de bulles, de lignes et de polygones.

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
}, layer.getId());

//Add a long feature click event to the map and pass the layer ID to limit the event to the specified layer.
map.events.add((OnLongFeatureClick) (features) -> {
    //One or more features long clicked.
}, layer.getId());
```

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
