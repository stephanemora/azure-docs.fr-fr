---
title: Affichage d’informations sur les caractéristiques dans les cartes Android | Microsoft Azure Maps
description: Découvrez comment afficher des informations lorsque les utilisateurs interagissent avec les fonctionnalités de la carte. Utilisez Android SDK Azure Maps pour afficher des messages Toast et d’autres types de messages.
author: rbrundritt
ms.author: richbrun
ms.date: 08/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 37b5ab1c144ed81d995da40b87edeaccdcad7253
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/18/2020
ms.locfileid: "97679995"
---
# <a name="display-feature-information"></a>Afficher des informations sur les fonctionnalités

Les données spatiales sont souvent représentées à l’aide de points, de lignes et de polygones. Ces données sont souvent associées à des informations de métadonnées. Par exemple, un point peut représenter l’emplacement d’un restaurant, dont le nom, l’adresse et le type de nourriture proposé constituent les métadonnées. Ces métadonnées peuvent être ajoutées dans les propriétés d’une `Feature` GeoJSON. Le code suivant crée une caractéristique de point simple avec une propriété `title` dont la valeur est « Hello World! ».

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a point feature.
Feature feature = Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64));

//Add a property to the feature.
feature.addStringProperty("title", "Hello World!");

//Create a point feature, pass in the metadata properties, and add it to the data source.
source.add(feature);
```

Pour savoir comment créer et ajouter des données à la carte, consultez la documentation [Création d’une source de données](create-data-source-android-sdk.md).

Quand un utilisateur interagit avec une caractéristique sur la carte, des événements peuvent être utilisés pour réagir à ces actions. Un scénario courant consiste à afficher un message constitué des propriétés de métadonnées d’une caractéristique avec laquelle l’utilisateur a interagi. L’événement `OnFeatureClick` est l’événement principal utilisé pour détecter quand l’utilisateur a appuyé sur une caractéristique sur la carte. Il existe également un événement `OnLongFeatureClick`. Quand vous ajoutez l’événement `OnFeatureClick` à la carte, vous pouvez le limiter à un seul calque en transmettant l’ID d’un calque auquel le limiter. Si aucun ID de calque n’est transmis, le fait de cliquer sur n’importe quelle caractéristique de la carte, quelle que soit le calque dans lequel elle se trouve, déclenche cet événement. Le code suivant crée un calque de symboles pour afficher des données de point sur la carte, puis ajoute un événement `OnFeatureClick` et le limite à ce calque de symboles.

```java
//Create a symbol and add it to the map.
SymbolLayer layer = new SymbolLayer(source);
map.layers.add(layer);

//Add a feature click event to the map.
map.events.add((OnFeatureClick) (features) -> {
    //Retrieve the title property of the feature as a string.
    String msg = features.get(0).getStringProperty("title");

    //Do something with the message.
}, layer.getId());    //Limit this event to the symbol layer.
```

## <a name="display-a-toast-message"></a>Afficher un message Toast

Les messages Toast sont l’un des moyens les plus simples de présenter des informations à l’utilisateur. Ils sont disponibles dans toutes les versions d’Android. Ils ne prennent en charge aucun type d’entrée utilisateur et sont affichés uniquement pendant un court instant. Si vous souhaitez fournir rapidement une information à l’utilisateur concernant l’objet sur lequel il a appuyé, un message Toast peut être une bonne option. Le code suivant montre comment utiliser un message Toast avec l’événement `OnFeatureClick`.

```java
//Add a feature click event to the map.
map.events.add((OnFeatureClick) (features) -> {
    //Retrieve the title property of the feature as a string.
    String msg = features.get(0).getStringProperty("title");

    //Display a toast message with the title information.
    Toast.makeText(this, msg, Toast.LENGTH_SHORT).show();
}, layer.getId());    //Limit this event to the symbol layer.
```

![Animation d’une caractéristique faisant l’objet d’un appui et de l’affichage d’un message Toast](./media/display-feature-information-android/symbol-layer-click-toast-message.gif)

Outre les messages Toast, il existe de nombreuses autres façons de présenter les propriétés de métadonnées d’une caractéristique, par exemple :

- [Widget Snackbar](https://developer.android.com/training/snackbar/showing.html) : les `Snackbars` donnent des retours d’informations légers concernant une opération. Ils affichent un bref message en bas de l’écran sur l’appareil mobile et en bas à gauche sur les appareils plus grands. Les `Snackbars` apparaissent au-dessus de tous les autres éléments à l’écran. Il ne peut y en avoir qu’un seul affiché à la fois.
- [Boîtes de dialogue](https://developer.android.com/guide/topics/ui/dialogs) : une boîte de dialogue est une petite fenêtre qui invite l’utilisateur à prendre une décision ou à entrer des informations supplémentaires. Une boîte de dialogue ne remplit pas l’écran et est normalement utilisée pour les événements modaux qui exigent que les utilisateurs effectuent une action avant de pouvoir continuer.
- Ajouter un [fragment](https://developer.android.com/guide/components/fragments) à l’activité en cours.
- Accéder à une autre activité ou vue.

## <a name="next-steps"></a>Étapes suivantes

Pour ajouter des données à votre carte :

> [!div class="nextstepaction"]
> [Réagir aux événements de carte](android-map-events.md)

> [!div class="nextstepaction"]
> [Créer une source de données](create-data-source-android-sdk.md)

> [!div class="nextstepaction"]
> [Ajouter une couche de symboles](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Ajouter une couche de bulles](map-add-bubble-layer-android.md)

> [!div class="nextstepaction"]
> [Ajouter une couche de lignes](android-map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Ajouter une couche de polygones](how-to-add-shapes-to-android-map.md)
