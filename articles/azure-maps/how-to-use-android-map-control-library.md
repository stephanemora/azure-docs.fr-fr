---
title: Guide pratique d’utilisation du contrôle de carte Android dans Azure Maps | Microsoft Docs
description: Utiliser le contrôle de carte Android dans Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 02/12/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 57cc585d621c71872a4b7658c74f581c8998b245
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/18/2019
ms.locfileid: "56341077"
---
# <a name="how-to-use-azure-maps-android-sdk"></a>Guide pratique d’utilisation de Android SDK Azure Maps

Android SDK Azure Maps est une bibliothèque de cartes de vecteur pour Android. Cet article vous guide tout au long des processus d'installation du kit de développement logiciel (SDK) Android Azure Maps, de chargement d'une carte et de placement d'une épingle sur la carte.

## <a name="prerequisites-to-get-started"></a>Conditions préalables

### <a name="create-an-azure-maps-account"></a>Créer un compte Azure Maps 

Pour suivre les étapes de ce guide, vous devez tout d’abord lire [Gérer le compte et les clés](how-to-manage-account-keys.md) afin de créer et de gérer votre abonnement de compte avec le niveau tarifaire S1.

### <a name="download-android-studio"></a>Télécharger Android Studio

Vous pouvez télécharger [Android Studio](https://developer.android.com/studio/) gratuitement sur Google. Pour installer Android SDK Azure Maps, vous devez tout d’abord télécharger Android Studio et créer un projet avec une activité vide.

## <a name="create-a-project-in-android-studio"></a>Créer un projet dans Android Studio

Vous devez créer un projet avec une activité vide. Suivez les étapes ci-dessous pour créer un projet Android Studio :

1. Sous *Choisir votre projet*, sélectionnez l’option « Téléphone et tablette » comme facteur de forme sur lequel votre application sera exécutée.
2. Cliquez sur *Activité vide* sous facteur de forme, puis sur **Suivant**.
3. Sous *Configurer votre projet*, sélectionnez `API 21: Android 5.0.0 (Lollipop)` comme SDK minimum. Il s’agit de la version minimale prise en charge par Android SDK Azure Maps.
4. Acceptez les valeurs par défaut `Activity Name` et `Layout Name`, puis cliquez sur **Terminer**.

Pour obtenir de l’aide supplémentaire sur l’installation d’Android Studio et la création d’un projet, consultez la [documentation Android Studio](https://developer.android.com/studio/intro/).

![créer un projet](./media/how-to-use-android-map-control-library/form-factor-android.png)

## <a name="set-up-a-virtual-device"></a>Configurer un appareil virtuel

Android Studio vous permet de configurer un appareil Android virtuel sur votre ordinateur. Il vous permet de tester votre application pendant le développement. Pour configurer un appareil virtuel, cliquez sur l'icône d'Android Virtual Device (AVD) Manager située dans le coin supérieur droit de l'écran du projet. Cliquez ensuite sur le bouton **Créer un appareil virtuel**. Vous pouvez également accéder au gestionnaire via les options **Outils > Android > AVD Manager** de la barre d'outils. Dans la catégorie **Téléphones**, sélectionnez **Nexus 5X** et cliquez sur **Suivant**.

Pour en savoir plus sur la configuration d’un appareil virtuel Android, consultez la [documentation Android Studio](https://developer.android.com/studio/run/managing-avds).

![Émulateur Android](./media/how-to-use-android-map-control-library/android-emulator.png)

## <a name="install-azure-maps-android-sdk"></a>Installer Android SDK Azure Maps

Avant de passer à la création de votre application, suivez la procédure ci-dessous pour installer Android SDK Azure Maps. 

1. Ajoutez les éléments suivants au bloc de référentiels **all projects** de votre fichier **build.gradle**.

    ```
    maven {
            url "https://atlas.microsoft.com/sdk/android"
    }
    ```

2. Modifiez l’élément **app/build.gradle** et ajoutez-y ceci :

    1. Ajoutez ceci au bloc Android :

        ```
        compileOptions {
            sourceCompatibility JavaVersion.VERSION_1_8
            targetCompatibility JavaVersion.VERSION_1_8
        }
        ```
    2. Modifiez votre bloc de dépendances et ajoutez-y ceci :

        ```
        implementation "com.microsoft.azure.maps:mapcontrol:0.1"
        ```

3. Configurez les autorisations en ajoutant ce qui suit à votre fichier **AndroidManifest.xml**

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <manifest>
        ...
        <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
        ...
    </manifest>
    ```

4. Modifiez l'élément **res > layout > activity_main.xml** de sorte qu'il se présente comme le fichier XML ci-dessous :
    
    ```XML
    <?xml version="1.0" encoding="utf-8"?>
    <FrameLayout
        xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:app="http://schemas.android.com/apk/res-auto"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        >

        <com.microsoft.azure.maps.mapcontrol.MapControl
            android:id="@+id/mapcontrol"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            app:mapcontrol_cameraTargetLat="47.64"
            app:mapcontrol_cameraTargetLng="-122.33"
            app:mapcontrol_cameraZoom="12"
            />

    </FrameLayout>
    ```

5. Modifiez l’élément **MainActivity.java** pour créer une classe d’activité d’affichage de carte. Une fois modifié, il doit se présenter comme suit :

    ```java
    package com.example.myapplication;

    import android.support.v7.app.AppCompatActivity;
    import android.os.Bundle;
    import com.microsoft.azure.maps.mapcontrol.AzureMaps;
    import com.microsoft.azure.maps.mapcontrol.MapControl;
    import com.microsoft.azure.maps.mapcontrol.layer.SymbolLayer;
    import com.microsoft.azure.maps.mapcontrol.options.MapStyle;
    import com.microsoft.azure.maps.mapcontrol.source.DataSource;

    public class MainActivity extends AppCompatActivity {
        
        static {
            AzureMaps.setSubscriptionKey("{subscription-key}");
        }

        MapControl mapControl;

        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);

            mapControl = findViewById(R.id.mapcontrol);

            mapControl.onCreate(savedInstanceState);

        }

        @Override
        public void onResume() {
            super.onResume();
            mapControl.onResume();
        }

        @Override
        public void onPause() {
            super.onPause();
            mapControl.onPause();
        }

        @Override
        public void onStop() {
            super.onStop();
            mapControl.onStop();
        }

        @Override
        public void onLowMemory() {
            super.onLowMemory();
            mapControl.onLowMemory();
        }

        @Override
        protected void onDestroy() {
            super.onDestroy();
            mapControl.onDestroy();
        }

        @Override
        protected void onSaveInstanceState(Bundle outState) {
            super.onSaveInstanceState(outState);
            mapControl.onSaveInstanceState(outState);
        }

    }

    ```

## <a name="import-classes"></a>Importer des classes

Une fois les étapes ci-dessus effectuées, vous allez probablement recevoir des avertissements d’Android Studio sur une partie du texte du code. Pour résoudre ces avertissements, importez les classes référencées dans `MainActivity.java`.

Vous pouvez le faire de façon automatique en appuyant sur `Alt`+`Enter` (`Option`+`Return` sur Mac). 

Cliquez sur le bouton **Exécuter l’application** (ou `Control`+`R` sur Mac) pour créer votre application.

![Cliquez sur Exécuter.](./media/how-to-use-android-map-control-library/run-app.png)

La création de l’application par Android Studio prend quelques secondes. Une fois la création terminée, vous pouvez tester votre application dans l’appareil Android émulé. Une carte comme celle-ci doit apparaître.

![Carte Android](./media/how-to-use-android-map-control-library/android-map.png)

## <a name="add-a-marker-to-the-map"></a>Ajouter un marqueur sur la carte

Pour ajouter un marqueur sur votre carte, ajoutez la fonction `mapView.getMapAsync()` à l’élément `MainActivity.java`. L’élément `MainActivity.java` final doit se présenter comme suit :

```java
package com.example.myapplication;

import android.app.Activity;
import android.os.Bundle;
import com.mapbox.geojson.Feature;
import com.mapbox.geojson.Point;
import com.microsoft.azure.maps.mapcontrol.AzureMaps;
import com.microsoft.azure.maps.mapcontrol.MapControl;
import com.microsoft.azure.maps.mapcontrol.layer.SymbolLayer;
import com.microsoft.azure.maps.mapcontrol.source.DataSource;
import static com.microsoft.azure.maps.mapcontrol.options.SymbolLayerOptions.iconImage;
public class MainActivity extends AppCompatActivity {
    
    static{
            AzureMaps.setSubscriptionKey("{subscription-key}");
        }

    MapControl mapControl;
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        mapControl = findViewById(R.id.mapcontrol);

        mapControl.onCreate(savedInstanceState);

        mapControl.getMapAsync(map -> {
            DataSource dataSource = new DataSource();
            dataSource.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64)));

            SymbolLayer symbolLayer = new SymbolLayer(dataSource);
            symbolLayer.setOptions(iconImage("my-icon"));

            map.images.add("my-icon", R.drawable.mapcontrol_marker_red);
            map.sources.add(dataSource);
            map.layers.add(symbolLayer);
        });
    }

    @Override
    public void onStart() {
        super.onStart();
        mapControl.onStart();
    }

    @Override
    public void onResume() {
        super.onResume();
        mapControl.onResume();
    }

    @Override
    public void onPause() {
        super.onPause();
        mapControl.onPause();
    }

    @Override
    public void onStop() {
        super.onStop();
        mapControl.onStop();
    }

    @Override
    public void onLowMemory() {
        super.onLowMemory();
        mapControl.onLowMemory();
    }

    @Override
    protected void onDestroy() {
        super.onDestroy();
        mapControl.onDestroy();
    }

    @Override
    protected void onSaveInstanceState(Bundle outState) {
        super.onSaveInstanceState(outState);
        mapControl.onSaveInstanceState(outState);
    }
}
```

Réexécutez votre application. Un marqueur semblable à celui présenté ci-dessous doit apparaître sur la carte.

![Épingle sur une carte Android](./media/how-to-use-android-map-control-library/android-map-pin.png)