---
title: Comment utiliser le contrôle de carte Android dans Azure Maps | Microsoft Docs
description: Le contrôle de carte Android dans Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 02/12/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 15706addbe6b7f6310223978130158c792a47c89
ms.sourcegitcommit: 15e9613e9e32288e174241efdb365fa0b12ec2ac
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/28/2019
ms.locfileid: "57010665"
---
# <a name="how-to-use-the-azure-maps-android-sdk"></a>Comment utiliser le Kit de développement logiciel Android d’Azure Maps

Le Kit de développement logiciel Android d’Azure Maps est une bibliothèque de carte de vecteur pour Android. Cet article vous guide tout au long du processus d’installation le Kit de développement logiciel Android d’Azure Maps, le chargement d’une carte et placer un code confidentiel sur la carte.

## <a name="prerequisites"></a>Conditions préalables

### <a name="create-an-azure-maps-account"></a>Créer un compte Azure Maps

Pour effectuer les procédures décrites dans cet article, vous devez d’abord [créer un compte Azure Maps](how-to-manage-account-keys.md) dans le niveau tarifaire S1.

### <a name="download-android-studio"></a>Télécharger Android Studio

Vous devez télécharger Android Studio et créez un projet avec une activité vide avant d’installer le Kit de développement logiciel Android d’Azure Maps. Vous pouvez [télécharger Android Studio](https://developer.android.com/studio/) gratuitement à partir de Google. 

## <a name="create-a-project-in-android-studio"></a>Créer un projet dans Android Studio

Vous devez tout d’abord, créez un nouveau projet avec une activité vide. Suivez ces étapes pour créer un projet Android Studio :

1. Sous **Choisissez votre projet**, sélectionnez **téléphone et tablette**. Votre application s’exécutera sur ce facteur de forme.
2. Sur le **téléphone et tablette** onglet, sélectionnez **activité vide**, puis sélectionnez **suivant**.
3. Sous **Configurer votre projet**, sélectionnez `API 21: Android 5.0.0 (Lollipop)` comme SDK minimum. Il s’agit de la version la plus ancienne prise en charge par le Kit de développement logiciel Android d’Azure Maps.
4. Acceptez la valeur par défaut `Activity Name` et `Layout Name` et sélectionnez **Terminer**.

Consultez le [documentation Android Studio](https://developer.android.com/studio/intro/) pour plus d’aide avec l’installation d’Android Studio et la création d’un nouveau projet.

![Création d’un projet](./media/how-to-use-android-map-control-library/form-factor-android.png)

## <a name="set-up-a-virtual-device"></a>Configurer un appareil virtuel

Android Studio vous permet de configurer un appareil Android virtuel sur votre ordinateur. Cela peut vous aider à tester votre application pendant le développement. Pour configurer un appareil virtuel, sélectionnez l’icône Android Virtual Device (AVD) Manager dans le coin supérieur droit de votre écran de projet, puis **créer un appareil virtuel**. Vous pouvez également obtenir pour le gestionnaire AVD en sélectionnant **outils** > **Android** > **AVD Manager** à partir de la barre d’outils. Dans le **téléphones** catégorie, sélectionnez **Nexus 5 X**, puis sélectionnez **suivant**.

Vous trouverez plus d’informations sur la configuration d’un AVD dans le [documentation Android Studio](https://developer.android.com/studio/run/managing-avds).

![Émulateur Android](./media/how-to-use-android-map-control-library/android-emulator.png)

## <a name="install-the-azure-maps-android-sdk"></a>Installez le SDK Android Azure Maps

L’étape suivante de la création de votre application consiste à installer le Kit de développement logiciel Android d’Azure Maps. Suivez ces étapes pour installer le Kit de développement :

1. Ajoutez le code suivant à la **tous les projets**, **référentiels** bloquer votre **build.gradle** fichier.

    ```
    maven {
            url "https://atlas.microsoft.com/sdk/android"
    }
    ```

2. Mise à jour votre **App/build.gradle** et lui ajouter le code suivant :

    1. Ajoutez le code suivant au bloc Android :

        ```
        compileOptions {
            sourceCompatibility JavaVersion.VERSION_1_8
            targetCompatibility JavaVersion.VERSION_1_8
        }
        ```
    2. Mettre à jour de votre bloc de dépendances et ajoutez le code suivant à celui-ci :

        ```
        implementation "com.microsoft.azure.maps:mapcontrol:0.1"
        ```

3. Définir les autorisations en ajoutant le code XML suivant à votre **AndroidManifest.xml** fichier :

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <manifest>
        ...
        <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
        ...
    </manifest>
    ```

4. Modifier **res** > **disposition** > **activity_main.xml** afin qu’il ressemble à ce document XML :
    
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

5. Modifiez l’élément **MainActivity.java** pour créer une classe d’activité d’affichage de carte. Une fois que vous le modifiez, il doit ressembler à cette classe :

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

Après avoir terminé les étapes précédentes, vous probablement obtiendrez avertissements à partir d’Android Studio sur la partie du code. Pour résoudre ces avertissements, importer les classes référencées dans `MainActivity.java`.

Vous pouvez importer automatiquement ces classes en sélectionnant Alt + Entrée (Option + retour sur un Mac).

Sélectionnez le bouton d’exécution, comme illustré à la suivante graphique (ou appuyez sur CTRL + R sur un Mac), pour générer votre application.

![Cliquez sur Exécuter.](./media/how-to-use-android-map-control-library/run-app.png)

Android Studio prendra quelques secondes pour générer l’application. Une fois la build terminée, vous pouvez tester votre application dans l’appareil émulé Android. Vous devez voir une carte comme celle-ci :

![Carte Android](./media/how-to-use-android-map-control-library/android-map.png)

## <a name="add-a-marker-to-the-map"></a>Ajouter un marqueur sur la carte

Pour ajouter un marqueur à votre carte, ajoutez le `mapView.getMapAsync()` à fonction `MainActivity.java`. La dernière `MainActivity.java` code doit ressembler à ceci :

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

Exécutez à nouveau votre application. Vous devez voir un marqueur sur la carte, comme illustré ici :

![Épingle sur une carte Android](./media/how-to-use-android-map-control-library/android-map-pin.png)