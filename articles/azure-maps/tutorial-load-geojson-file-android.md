---
title: 'Tutoriel : Charger des données GeoJSON dans le kit Android SDK Azure Maps | Microsoft Azure Maps'
description: Tutoriel expliquant comment charger un fichier de données GeoJSON dans le kit Android SDK Azure Maps.
author: rbrundritt
ms.author: richbrun
ms.date: 12/10/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: 66214dc3a21389b779cfb5172747be0700e9d0e9
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/30/2021
ms.locfileid: "113091233"
---
# <a name="tutorial-load-geojson-data-into-azure-maps-android-sdk"></a>Tutoriel : Charger des données GeoJSON dans le kit Android SDK Azure Maps

Ce tutoriel vous guide tout au long du processus d’importation d’un fichier GeoJSON de données de localisation dans le kit Android SDK Azure Maps. Dans ce tutoriel, vous allez découvrir comment :

> [!div class="checklist"]
> * Ajouter Azure Maps dans une application Android
> * Créer une source de données et charger un fichier GeoJSON à partir d’un fichier local ou du web
> * Afficher les données sur la carte
> * Interagissez avec les données sur les mappages pour afficher ses détails.

## <a name="prerequisites"></a>Prérequis

1. Suivez le [Démarrage rapide : Créer une application Android](quick-android-map.md). Ce tutoriel va étendre le code utilisé dans ce guide de démarrage rapide.
2. Téléchargez le fichier GeoJSON [Sample Points of Interest](https://raw.githubusercontent.com/Azure-Samples/AzureMapsCodeSamples/master/AzureMapsCodeSamples/Common/data/geojson/SamplePoiDataSet.json).

### <a name="import-geojson-data-from-web-or-assets-folder"></a>Importer des données GeoJSON à partir d’un dossier web ou de ressources

La plupart des fichiers GeoJSON wrappent toutes les données dans une `FeatureCollection`. Sachant cela, si les fichiers GeoJSON sont chargés dans l’application sous la forme d’une chaîne, ils peuvent être transmis dans la méthode `fromJson` statique de la collection de fonctionnalités, qui désérialisera la chaîne en un objet `FeatureCollection` GeoJSON pouvant être ajouté à la carte.

Les étapes suivantes expliquent comment importer un fichier GeoJSON dans l’application et le désérialiser en tant qu’objet `FeatureCollection` GeoJSON.

1. Suivez le [Démarrage rapide : Créer une application Android](quick-android-map.md), car les étapes qui suivent s’appuient sur cette application.
2. Dans le panneau de projet d’Android Studio, cliquez avec le bouton droit sur le dossier **app**, puis accédez à `New > Folder > Assets Folder`.
3. Glissez-déposez le fichier GeoJSON [Sample Points of Interest](https://raw.githubusercontent.com/Azure-Samples/AzureMapsCodeSamples/master/AzureMapsCodeSamples/Common/data/geojson/SamplePoiDataSet.json) dans le dossier des ressources.

::: zone pivot="programming-language-java-android"

4. Accédez au fichier **MainActivity.java**, puis ajoutez le code suivant dans le rappel de l’événement `mapControl.onReady` à l’intérieur de la méthode `onCreate`. Ce code charge le fichier **SamplePoiDataSet.json** à partir du dossier de ressources dans une source de données à l’aide de la méthode `importDataFromUrl`, puis l’ajoute au mappage.

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();

//Import the geojson data and add it to the data source.
source.importDataFromUrl("asset://SamplePoiDataSet.json");

//Add data source to the map.
map.sources.add(source);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

4. Accédez au fichier **MainActivity.kt**, puis ajoutez le code suivant dans le rappel de l’événement `mapControl.onReady` à l’intérieur de la méthode `onCreate`. Ce code charge le fichier **SamplePoiDataSet.json** à partir du dossier de ressources dans une source de données à l’aide de la méthode `importDataFromUrl`, puis l’ajoute au mappage.

```kotlin
//Create a data source and add it to the map.
DataSource source = new DataSource();

//Import the geojson data and add it to the data source.
source.importDataFromUrl("asset://SamplePoiDataSet.json");

//Add data source to the map.
map.sources.add(source);
```

::: zone-end

5. À l’aide du code permettant de charger les données GeoJSON d’une source de données, nous devons maintenant spécifier de quelle manière ces données doivent être affichées sur la carte. Il existe différents calques de rendu pour les données de point. Les calques les plus couramment utilisés sont le [calque de bulles](map-add-bubble-layer-android.md), le [calque de symboles](how-to-add-symbol-to-android-map.md) et le [calque de carte thermique](map-add-heat-map-layer-android.md). Ajoutez le code suivant pour afficher les données dans un calque de bulles, dans le rappel de l’événement `mapControl.onReady`, après le code permettant d’importer les données.

::: zone pivot="programming-language-java-android"

```java
//Create a layer and add it to the map.
BubbleLayer layer = new BubbleLayer(source);
map.layers.add(layer);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a layer and add it to the map.
val layer = new BubbleLayer(source)
map.layers.add(layer)
```

::: zone-end

6. Dans le panneau de projet d’Android Studio, cliquez avec le bouton droit sur le dossier **layout** dans le chemin `app > res > layout`, puis accédez à `New > File`. Créez un nouveau fichier appelé **popup_text.xml**.
7. Ouvrez le fichier **popup_text.xml**. Si le fichier s’ouvre en mode concepteur, cliquez avec le bouton droit sur l’écran et sélectionnez « Go to XML » (« Atteindre XML »). Copiez-collez le code XML suivant dans ce fichier. Ce code XML crée une mise en page simple qui peut être utilisée avec une fenêtre contextuelle et contient un affichage de texte.

```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:orientation="vertical"
    android:background="#ffffff"
    android:layout_margin="8dp"
    android:padding="10dp"

    android:layout_height="match_parent">

    <TextView
        android:id="@+id/message"
        android:layout_width="wrap_content"
        android:text=""
        android:textSize="18dp"
        android:textColor="#222"
        android:layout_height="wrap_content"
        android:width="200dp"/>

</RelativeLayout>
```

::: zone pivot="programming-language-java-android"

8. Revenez au fichier  **MainActivity.java** et, après le code de la couche de bulles, ajoutez le code suivant pour créer un menu contextuel réutilisable.

```java
//Create a popup and add it to the map.
Popup popup = new Popup();
map.popups.add(popup);

//Close it initially.
popup.close();
```

::: zone-end

::: zone pivot="programming-language-kotlin"

8. Revenez au fichier  **MainActivity.kt** et, après le code de la couche de bulles, ajoutez le code suivant pour créer un menu contextuel réutilisable.

```kotlin
//Create a popup and add it to the map.
val popup = Popup()
map.popups.add(popup)
    
//Close it initially.
popup.close()
```

::: zone-end

9. Ajoutez le code suivant pour joindre un événement clic à la couche de bulles. Lorsqu’une bulle de la couche des bulles est touchée, l’événement se déclenche et récupère des détails à partir des propriétés de l’élément sélectionné, crée une vue à l’aide du fichier de mise en page **popup_text.xml**, la transmet comme contenu dans la fenêtre contextuelle, puis affiche la fenêtre contextuelle à la position des fonctionnalités.

::: zone pivot="programming-language-java-android"

```java
//Add a click event to the layer.
map.events.add((OnFeatureClick)(feature) -> {
    //Get the first feature and it's properties.
    Feature f = feature.get(0);
    JsonObject props = f.properties();

    //Retrieve the custom layout for the popup.
    View customView = LayoutInflater.from(this).inflate(R.layout.popup_text, null);

    //Display the name and entity type information of the feature into the text view of the popup layout.
    TextView tv = customView.findViewById(R.id.message);
    tv.setText("%s\n%s",
        f.getStringProperty("Name"),
        f.getStringProperty("EntityType")
    );

    //Get the position of the clicked feature.
    Position pos = MapMath.getPosition((Point)f.geometry());

    //Set the options on the popup.
    popup.setOptions(
            //Set the popups position.
            position(pos),

            //Set the anchor point of the popup content.
            anchor(AnchorType.BOTTOM),

            //Set the content of the popup.
            content(customView)
    );

    //Open the popup.
    popup.open();

    //Return a boolean indicating if event should be consumed or continue to bubble up.
    return false;
}, layer);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Add a click event to the layer.
map.events.add(OnFeatureClick { feature: List<Feature> ->
    //Get the first feature and it's properties.
    val f = feature[0]
    val props = f.properties()

    //Retrieve the custom layout for the popup.
    val customView: View = LayoutInflater.from(this).inflate(R.layout.popup_text, null)

    //Display the name and entity type information of the feature into the text view of the popup layout.
    val tv = customView.findViewById<TextView>(R.id.message)
    tv.text = String.format(
        "%s\n%s",
        f.getStringProperty("Name"),
        f.getStringProperty("EntityType")
    )

    //Get the position of the clicked feature.
    val pos = MapMath.getPosition(f.geometry() as Point?)

    //Set the options on the popup.
    popup.setOptions( //Set the popups position.
        position(pos),  //Set the anchor point of the popup content.
        anchor(AnchorType.BOTTOM),  //Set the content of the popup.
        content(customView)
    )

    //Open the popup.
    popup.open()

    //Return a boolean indicating if event should be consumed or continue to bubble up.
    false
} as OnFeatureClick, layer)
```

::: zone-end

10. Exécutez l'application. Une carte s’affichera avec des bulles superposées pour chaque localisation du fichier GeoJSON. En appuyant sur une bulle, vous affichez une fenêtre contextuelle avec le nom et le type d’entité de la fonctionnalité touchée.

    ![Mappage de données à partir d’un fichier GeoJSON affiché avec une fenêtre contextuelle ouverte lorsque l’emplacement est touché](media/tutorial-load-geojson-file-android/android-import-geojson.gif)

## <a name="clean-up-resources"></a>Nettoyer les ressources

Suivez les étapes ci-dessous pour nettoyer les ressources de ce tutoriel :

1. Fermez Android Studio, puis supprimez l’application que vous avez créée.
2. Si vous avez testé l’application sur un appareil externe, désinstallez l’application de cet appareil.

## <a name="next-steps"></a>Étapes suivantes

Pour bénéficier de davantage d’exemples de code et d’une expérience de codage interactive :

> [!div class="nextstepaction"]
> [Utiliser des expressions de style basées sur les données](data-driven-style-expressions-android-sdk.md)

> [!div class="nextstepaction"]
> [Afficher des informations sur les caractéristiques](display-feature-information-android.md)

> [!div class="nextstepaction"]
> [Ajouter une couche de symboles](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Ajouter une couche de lignes](android-map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Ajouter une couche de polygones](how-to-add-shapes-to-android-map.md)
