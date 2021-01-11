---
title: Ajouter une couche de symboles à des cartes Android | Microsoft Azure Maps
description: Découvrez comment ajouter un marqueur à une carte. Consultez un exemple qui utilise l’Android SDK Azure Maps pour ajouter une couche de symboles qui contient des données basées sur des points à partir d’une source de données.
author: rbrundritt
ms.author: richbrun
ms.date: 12/08/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 040fcde35707074ffaf102ed6c224b2f47a084bb
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/18/2020
ms.locfileid: "97679348"
---
# <a name="add-a-symbol-layer-android-sdk"></a>Ajout d’une couche de symboles (Android SDK)

Cet article montre comment restituer des données de points à partir d’une source de données sous la forme d’une couche de symboles sur une carte à l’aide de l’Android SDK Azure Maps. Les couches de symboles affichent les points sous forme d’image et de texte sur la carte.

> [!TIP]
> Les couches de symboles par défaut affichent les coordonnées de toutes les données géométriques d’une source de données. Pour limiter la couche de sorte qu’elle n’affiche que les caractéristiques géométriques de type Point, définissez l’option `filter` de la couche sur `eq(geometryType(), "Point")`. Si vous souhaitez également inclure des caractéristiques MultiPoint, donnez-lui la valeur `any(eq(geometryType(), "Point"), eq(geometryType(), "MultiPoint"))`.

## <a name="prerequisites"></a>Prérequis

Veillez à suivre la procédure du document [Démarrage rapide : Création d’une application Android](quick-android-map.md). Les blocs de code de cet article peuvent être insérés dans le gestionnaire d’événements `onReady` des cartes.

## <a name="add-a-symbol-layer"></a>Ajouter un calque de symboles

Avant de pouvoir ajouter une couche de symboles à la carte, vous devez effectuer quelques étapes. Commencez par créer une source de données et l’ajouter à la carte. Créez une couche de symboles. Ensuite, transmettez la source de données à la couche de symboles pour récupérer les données de la source de données. Enfin, ajoutez des données à la source de données, afin qu’il y ait quelque chose à afficher.

Le code ci-dessous montre ce qui doit être ajouté à la carte après son chargement. Cet exemple restitue un point unique sur la carte à l’aide d’une couche de symboles.

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a point and add it to the data source.
source.add(Point.fromLngLat(0, 0));

//Create a symbol layer to render icons and/or text at points on the map.
SymbolLayer layer = new SymbolLayer(source);

//Add the layer to the map.
map.layers.add(layer);
```

Trois types de données de point peuvent être ajoutés à la carte :

- Géométrie des points GeoJSON - cet objet contient uniquement une coordonnée d’un point et rien d’autre. La méthode statique `Point.fromLngLat` peut être utilisée pour créer facilement ces objets.
- Géométrie MultiPoint GeoJSON - cet objet contient les coordonnées de plusieurs points et rien d’autre. Transmettez un tableau de points dans la classe `MultiPoint` pour créer ces objets.
- Fonctionnalité GeoJSON - cet objet se compose d’une géométrie GeoJSON et d’un ensemble de propriétés qui contiennent des métadonnées associées à la géométrie.

Pour plus d’informations, consultez le document [Création d’une source de données](create-data-source-android-sdk.md) sur la création et l’ajout de données à la carte.

Dans l’exemple de code suivant est créée une géométrie de type Point GeoJSON, qui est transmise dans la caractéristique GeoJSON. Une valeur `title` est ajoutée à ses propriétés. La propriété `title` s’affiche sous forme de texte au-dessus de l’icône de symbole sur la carte.

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a point feature.
Feature feature = Feature.fromGeometry(Point.fromLngLat(0, 0));

//Add a property to the feature.
feature.addStringProperty("title", "Hello World!");

//Add the feature to the data source.
source.add(feature);

//Create a symbol layer to render icons and/or text at points on the map.
SymbolLayer layer = new SymbolLayer(source, 
    //Get the title property of the feature and display it on the map.
    textField(get("title"))
);

//Add the layer to the map.
map.layers.add(layer);
```

La capture d’écran suivante montre le rendu, obtenu avec le code ci-dessus, d’une caractéristique Point à l’aide d’une icône et d’une étiquette de texte avec une couche de symboles.

![Carte avec Point affiché à l’aide d’une couche de symboles présentant une icône et une étiquette de texte pour une caractéristique Point](media/how-to-add-symbol-to-android-map/android-map-pin.png)

> [!TIP]
> Par défaut, les couches de symboles optimisent l’affichage des symboles en masquant ceux qui se chevauchent. Quand vous effectuez un zoom avant, les symboles masqués deviennent visibles. Pour désactiver cette fonctionnalité et afficher tous les symboles en permanence, définissez les options `iconAllowOverlap` et `textAllowOverlap` sur `true`.

## <a name="add-a-custom-icon-to-a-symbol-layer"></a>Ajouter une icône personnalisée à une couche de symboles

Les couches de symboles sont affichées à l’aide de WebGL. Toutes les ressources, telles que les images d’icône, doivent donc être chargées dans le contexte de WebGL. Cet exemple montre comment ajouter une icône personnalisée aux ressources de carte. Cette icône est ensuite utilisée pour afficher des données de point avec un symbole personnalisé sur la carte. La propriété `textField` de la couche de symboles nécessite une expression. Dans ce cas, nous souhaitons afficher la propriété de température. La température étant un nombre, elle doit être convertie en chaîne. De plus, nous voulons y ajouter la mention « °F ». Pour effectuer cette concaténation, nous pouvons utiliser une expression : `concat(Expression.toString(get("temperature")), literal("°F"))`.

```java
//Load a custom icon image into the image sprite of the map.
map.images.add("my-custom-icon", R.drawable.showers);

//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a point feature.
Feature feature = Feature.fromGeometry(Point.fromLngLat(-73.985708, 40.75773));

//Add a property to the feature.
feature.addNumberProperty("temperature", 64);

//Add the feature to the data source.
source.add(feature);

//Create a symbol layer to render icons and/or text at points on the map.
SymbolLayer layer = new SymbolLayer(source,
    iconImage("my-custom-icon"),
    iconSize(0.5f),

    //Get the title property of the feature and display it on the map.
    textField(concat(Expression.toString(get("temperature")), literal("°F"))),
    textOffset(new Float[]{0f, -1.5f})
);
```

Pour cet exemple, l’image suivante a été chargée dans le dossier drawable de l’application.

| ![Image d’icône météo d’averse](media/how-to-add-symbol-to-android-map/showers.png)|
|:-----------------------------------------------------------------------:|
| showers.png                                                  |

La capture d’écran suivante montre le rendu, obtenu avec le code ci-dessus, d’une caractéristique Point à l’aide d’une icône personnalisée et d’une étiquette de texte mise en forme avec une couche de symboles.

![Carte avec Point affiché à l’aide d’une couche de symboles présentant une icône personnalisée et une étiquette de texte mise en forme pour une caractéristique Point](media/how-to-add-symbol-to-android-map/android-custom-symbol-layer.png)

> [!TIP]
> Si vous souhaitez afficher uniquement le texte avec une couche de symboles, vous pouvez masquer l’icône en définissant la propriété `iconImage` des options d’icône sur `"none"`.

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir plus d’exemples de code à ajouter à vos cartes, consultez les articles suivants :

> [!div class="nextstepaction"]
> [Créer une source de données](create-data-source-android-sdk.md)

> [!div class="nextstepaction"]
> [Ajouter une couche de bulles](map-add-bubble-layer-android.md)

> [!div class="nextstepaction"]
> [Utiliser des expressions de style basées sur les données](data-driven-style-expressions-android-sdk.md)

> [!div class="nextstepaction"]
> [Afficher des informations sur les caractéristiques](display-feature-information-android.md)
