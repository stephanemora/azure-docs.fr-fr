---
title: Ajouter une couche de symbole pour Android est mappé dans Azure Maps | Microsoft Docs
description: Comment ajouter des symboles à un mappage à l’aide du Kit de développement logiciel Android d’Azure Maps
author: walsehgal
ms.author: v-musehg
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: add6e23d023753e217c102dc946837a71a64c781
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/29/2019
ms.locfileid: "64871076"
---
# <a name="add-a-symbol-layer-to-a-map-using-azure-maps-android-sdk"></a>Ajouter une couche de symbole à un mappage à l’aide du Kit de développement logiciel Android d’Azure Maps

Cet article vous montre comment restituer les données de point à partir d’une source de données en tant que symbole couche sur une carte à l’aide d’Azure Maps Android SDK.

## <a name="prerequisites"></a>Conditions préalables

Pour complètement, effectuez les étapes dans cet article, vous devez installer [Azure Maps du Kit Android SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-android-map-control-library) charger une carte.

## <a name="add-a-symbol-layer"></a>Ajouter un calque de symboles

Pour ajouter un marqueur sur la carte à l’aide de la couche de symbole, suivez les étapes ci-dessous :

1. Modifier **res** > **disposition** > **activity_main.xml** afin d’obtenir le code XML suivant :
    
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
            app:mapcontrol_centerLat="47.64"
            app:mapcontrol_centerLng="-122.33"
            app:mapcontrol_zoom="12"
            />

    </FrameLayout>
    ```

2. Copier l’extrait de code suivant dans le **onCreate()** méthode de votre `MainActivity.java` classe.

    ```Java
    mapControl.onReady(map -> {
    
        //Create a data source and add it to the map.
        DataSource dataSource = new DataSource();
        map.sources.add(dataSource);
    
        //Create a point feature and add it to the data source.
        dataSource.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64)));
    
        //Add a custom image icon to the map resources.
        map.images.add("my-icon", R.drawable.mapcontrol_marker_red);
    
        //Create a symbol layer and add it to the map.
        map.layers.add(new SymbolLayer(dataSource,
            iconImage("my-icon")));
        });
    
    ```
    
    L’extrait de code ci-dessus obtient d’abord un Azure Maps carte contrôle instance en utilisant la **onReady()** méthode de rappel. Il crée ensuite un objet de source de données à l’aide du **DataSource** classe et l’ajoute à la carte. Il ajoute ensuite un **fonctionnalité** contenant une géométrie de Point à celui-ci. Une image de marqueur rouge est alors définie comme icône pour le symbole. Un **couche de symbole** utilise le texte ou des icônes pour restituer les données en fonction des points encapsulées dans la source de données comme symbole sur la carte. Une couche de symbole est ensuite créée et la source de données est passée à restituer et est ensuite ajoutée à des couches de la carte.
    
    Après avoir ajouté l’extrait de code ci-dessus, votre `MainActivity.java` doit ressembler à celle ci-dessous :
    
    ```Java
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
                AzureMaps.setSubscriptionKey("<Your Azure Maps subscription key>");
            }
    
        MapControl mapControl;
        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
    
            mapControl = findViewById(R.id.mapcontrol);
    
            mapControl.onCreate(savedInstanceState);
    
            mapControl.onReady(map -> {
    
                //Create a data source and add it to the map.
                DataSource dataSource = new DataSource();
                map.sources.add(dataSource);
            
                //Create a point feature and add it to the data source.
                dataSource.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64)));
            
                //Add a custom image icon to the map resources.
                map.images.add("my-icon", R.drawable.mapcontrol_marker_red);
            
                //Create a symbol layer and add it to the map.
                map.layers.add(new SymbolLayer(dataSource,
                    iconImage("my-icon")));
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
    
À ce stade, si vous exécutez votre application vous devez voir un marqueur sur la carte, comme illustré ici :

<center>

![Code confidentiel de carte Android](./media/how-to-add-symbol-to-android-map/android-map-pin.png)</center>


## <a name="next-steps"></a>Étapes suivantes

Pour ajouter davantage d’éléments à votre mappage, consultez :

> [!div class="nextstepaction"]
> [Ajouter des formes à un mappage d’Android](https://docs.microsoft.com/azure/azure-maps/how-to-add-shapes-to-android-map)