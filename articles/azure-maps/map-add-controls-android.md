---
title: Ajouter des contrôles à une carte Android | Microsoft Azure Maps
description: Comment ajouter un contrôle de zoom, un contrôle de tonalité, un contrôle de rotation et un sélecteur de styles à une carte dans l’Android SDK Microsoft Azure Maps.
author: rbrundritt
ms.author: richbrun
ms.date: 02/26/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: 90d037fc02bdc1c4d6fe682386790561c890c1e6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102100217"
---
# <a name="add-controls-to-a-map-android-sdk"></a>Ajouter des contrôles à une carte (Android SDK)

Cet article décrit comment ajouter des contrôles d’IU à la carte.

## <a name="add-zoom-control"></a>Ajouter un contrôle de zoom

Un contrôle de zoom ajoute des boutons pour effectuer un zoom avant et arrière sur la carte. L’exemple de code suivant crée une instance de la classe `ZoomControl` et l’ajoute à une carte.

::: zone pivot="programming-language-java-android"

```java
//Construct a zoom control and add it to the map.
map.controls.add(new ZoomControl());
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Construct a zoom control and add it to the map.
map.controls.add(ZoomControl())
```

::: zone-end

La capture d’écran ci-dessous est un contrôle de zoom chargé sur une carte.

![Contrôle de zoom ajouté à une carte](media/map-add-controls-android/android-zoom-control.jpg)

## <a name="add-pitch-control"></a>Ajouter un contrôle de tonalité

Un contrôle de pas ajoute des boutons pour faire pivoter le pas de la carte par rapport à l’horizon. L’exemple de code suivant crée une instance de la classe `PitchControl` et l’ajoute à une carte.

::: zone pivot="programming-language-java-android"

```java
//Construct a pitch control and add it to the map.
map.controls.add(new PitchControl());
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Construct a pitch control and add it to the map.
map.controls.add(PitchControl())
```

::: zone-end

La capture d’écran ci-dessous est un contrôle de tangage chargé sur une carte.

![Contrôle de tangage ajouté à une carte](media/map-add-controls-android/android-pitch-control.jpg)

## <a name="add-compass-control"></a>Ajouter un contrôle de boussole

Un contrôle de boussole ajoute un bouton pour faire pivoter la carte. L’exemple de code suivant crée une instance de la classe `CompassControl` et l’ajoute à une carte.

::: zone pivot="programming-language-java-android"

```java
//Construct a compass control and add it to the map.
map.controls.add(new CompassControl());
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Construct a compass control and add it to the map.
map.controls.add(CompassControl())
```

::: zone-end

La capture d’écran ci-dessous est un contrôle de boussole chargé sur une carte.

![Contrôle de boussole ajouté à une carte](media/map-add-controls-android/android-compass-control.jpg)

## <a name="add-traffic-control"></a>Ajouter un contrôle de trafic

Un contrôle de trafic ajoute un bouton permettant de basculer la visibilité des données de trafic sur la carte. L’exemple de code suivant crée une instance de la classe `TrafficControl` et l’ajoute à une carte.

::: zone pivot="programming-language-java-android"

```java
//Construct a traffic control and add it to the map.
map.controls.add(new TrafficControl());
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Construct a traffic control and add it to the map.
map.controls.add(TrafficControl())
```

::: zone-end

La capture d’écran ci-dessous est un contrôle de trafic chargé sur une carte.

![Contrôle de trafic ajouté à une carte](media/map-add-controls-android/android-traffic-control.jpg)

## <a name="a-map-with-all-controls"></a>Une carte avec tous les contrôles

Il est possible de placer plusieurs contrôles dans un tableau et de les ajouter à la carte en même temps et de les placer dans la même zone de la carte afin de simplifier le développement. L’exemple suivant ajoute les contrôles de navigation standard à la carte à l’aide de cette approche.

::: zone pivot="programming-language-java-android"

```java
map.controls.add(
    new Control[]{
        new ZoomControl(),
        new CompassControl(),
        new PitchControl(),
        new TrafficControl()
    }
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
map.controls.add(
    arrayOf<Control>(
        ZoomControl(),
        CompassControl(),
        PitchControl(),
        TrafficControl()
    )
)
```

::: zone-end

La capture d’écran ci-dessous montre tous les contrôles chargés sur une carte. Notez que l’ordre de leur ajout à la carte est l’ordre dans lequel ils s’affichent.

![Tous les contrôles ajoutés à une carte](media/map-add-controls-android/android-all-controls.jpg)

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir plus d’exemples de code à ajouter à vos cartes, consultez les articles suivants :

> [!div class="nextstepaction"]
> [Ajouter une couche de symboles](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Ajouter une couche de bulles](map-add-bubble-layer-android.md)

> [!div class="nextstepaction"]
> [Ajouter une couche de lignes](android-map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Ajouter une couche de polygones](how-to-add-shapes-to-android-map.md)
