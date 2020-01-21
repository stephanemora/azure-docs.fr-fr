---
title: 'Tutoriel : Migrer une application Android |Microsoft Azure Maps'
description: Comment migrer une application Android de Google Maps vers Microsoft Azure Maps.
author: rbrundritt
ms.author: richbrun
ms.date: 12/17/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 643d48cb931bcec1a8a3385d2ec24a394660c368
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/12/2020
ms.locfileid: "75909185"
---
# <a name="migrate-an-android-app-from-google-maps"></a>Migrer une application Android à partir de Google Maps

Android SDK Azure Maps a une interface API qui est très similaire au SDK web. Si vous avez développé avec l’un de ces SDK, un grand nombre des mêmes concepts, bonnes pratiques et architectures s’appliquent, et vous devriez pouvoir transférer facilement vos connaissances de l’un à l’autre.

Android SDK Azure Maps prend en charge une version Android minimale de l’API 21 : Android 5.0.0 (Lollipop).

Tous les exemples sont fournis en Java ; toutefois, Kotlin peut également être utilisé avec Android SDK Azure Maps.

Pour plus d’informations sur le développement avec ce SDK, consultez également les [guides pratiques pour Android SDK Azure Maps](how-to-use-android-map-control-library.md).

## <a name="load-a-map"></a>Charger une carte

Le chargement d’une carte dans une application Android à l’aide de Google ou d’Azure Maps comprend de nombreuses étapes communes. Quand vous utilisez l’un ou l’autre SDK, vous devez :

- Vous procurer une clé API ou d’abonnement pour accéder à l’une ou l’autre plateforme.
- Ajouter du code XML à une activité pour spécifier où la carte doit être affichée et la façon dont elle doit être disposée.
- Transférer toutes les méthodes de cycle de vie de l’activité contenant la vue cartographique aux éléments correspondants dans la classe map. En particulier, vous devez faire suivre les méthodes suivantes :
    - `onCreate(Bundle)`
    - `onStart()`
    - `onResume()`
    - `onPause()`
    - `onStop()`
    - `onDestroy()`
    - `onSaveInstanceState(Bundle)`
    - `onLowMemory()`
- Attendre que la carte soit prête avant d’essayer d’y accéder par programmation.

**Avant : Google Maps**

Pour afficher une carte à l’aide du SDK Google Maps pour Android, il est nécessaire d’effectuer les étapes suivantes :

1.  Assurez-vous que les services Google Play sont installés.
2.  Ajoutez une dépendance pour le service Google Maps au fichier **gradle.build** du module : 

    `implementation 'com.google.android.gms:play-services-maps:17.0.0'`

1.  Ajoutez une clé API Google Maps à l’intérieur de la section application du fichier **google\_maps\_api.xml** :
    
    ```xml
    <meta-data android:name="com.google.android.geo.API_KEY" android:value="YOUR_GOOGLE_MAPS_KEY"/>
    ```

1.  Ajoutez un fragment de carte à l’activité principale :

    ```xml
    <com.google.android.gms.maps.MapView
            android:id="@+id/myMap"
            android:layout_width="match_parent"
            android:layout_height="match_parent"/>
    ```

1.  Dans le fichier **MainActivity.java**, vous devez ajouter des importations pour le SDK Google Maps. Transférez toutes les méthodes de cycle de vie de l’activité contenant la vue cartographique aux éléments correspondants dans la classe map. Une instance de `MapView` peut être récupérée à partir du fragment de carte à l’aide de la méthode `getMapAsync(OnMapReadyCallback)`. `MapView` initialise automatiquement le système maps et l’affichage. Modifiez le fichier **MainActivity.java** comme suit :

    ```java
    import com.google.android.gms.maps.GoogleMap;
    import com.google.android.gms.maps.MapView;
    import com.google.android.gms.maps.OnMapReadyCallback;
    
    import android.support.v7.app.AppCompatActivity;
    import android.os.Bundle;
    
    public class MainActivity extends AppCompatActivity implements OnMapReadyCallback {
    
        MapView mapView;
    
        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
    
            mapView = findViewById(R.id.myMap);
    
            mapView.onCreate(savedInstanceState);
            mapView.getMapAsync(this);
        }
    
        @Override
    
        public void onMapReady(GoogleMap map) {
            //Add your post map load code here.
        }
    
        @Override
        public void onResume() {
            super.onResume();
            mapView.onResume();
        }
    
        @Override
        protected void onStart(){
            super.onStart();
            mapView.onStart();
        }
    
        @Override
        public void onPause() {
            super.onPause();
            mapView.onPause();
        }
    
        @Override
        public void onStop() {
            super.onStop();
            mapView.onStop();
        }
    
        @Override
        public void onLowMemory() {
            super.onLowMemory();
            mapView.onLowMemory();
        }
    
        @Override
        protected void onDestroy() {
            super.onDestroy();
            mapView.onDestroy();
        }
    
        @Override
        protected void onSaveInstanceState(Bundle outState) {
            super.onSaveInstanceState(outState);
            mapView.onSaveInstanceState(outState);
        }
    }
    ```

Quand il est exécuté dans une application, le contrôle de carte se charge comme suit.

<center>

![Google Maps simple](media/migrate-google-maps-android-app/simple-google-maps.png)</center>

**Après : Azure Maps**

Pour afficher une carte à l’aide du SDK Azure Maps pour Android, il est nécessaire d’effectuer les étapes suivantes :

1. Ouvrez le fichier **build.gradle** de niveau supérieur et ajoutez le code suivant à la section de bloc **all projects** (tous les projets), **repositories** (référentiels) :

    ```
    maven {
            url "https://atlas.microsoft.com/sdk/android"
    }
    ```

2. Modifiez l’élément **app/build.gradle** et ajoutez-y le code suivant :
    
    1. Assurez-vous que la version **minSdkVersion** de votre projet est l’API 21 ou une version ultérieure.

    2. Ajoutez le code suivant à la section Android :

        ```
        compileOptions {
            sourceCompatibility JavaVersion.VERSION_1_8
            targetCompatibility JavaVersion.VERSION_1_8
        }
        ```
    3. Mettez à jour votre bloc de dépendances et ajoutez une nouvelle ligne de dépendance d’implémentation pour la dernière version d’Android SDK Azure Maps :

        ```
        implementation "com.microsoft.azure.maps:mapcontrol:0.2"
        ```

        > [!Note]
        > Android SDK Azure Maps est régulièrement mis à niveau et amélioré. Reportez-vous à la documentation [Bien démarrer avec le contrôle de carte Android](how-to-use-android-map-control-library.md) pour obtenir le dernier numéro de version d’implémentation d’Azure Maps. Vous pouvez également définir le numéro de version de « 0.2 » à « 0+ » afin qu’il pointe toujours vers la dernière version.
    
    4. Accédez à **Fichier** dans la barre d’outils, puis cliquez sur **Synchroniser le projet avec les fichiers Gradle**.
3. Ajoutez un fragment de carte à l’activité principale (res \> layout \> activity\_main.xml) :
    
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
            />
    </FrameLayout>
    ```

4. Dans le fichier **MainActivity.java**, vous devez :
    
    * Ajouter des importations pour le Kit de développement logiciel (SDK) Azure Maps
    * Définir vos informations d’authentification Azure Maps
    * Obtenir l’instance de contrôle de carte dans la méthode **onCreate**

    Si vous définissez les informations d’authentification sur la classe `AzureMaps` de façon globale à l’aide des méthodes `setSubscriptionKey` ou `setAadProperties`, vous n’aurez pas besoin d’ajouter vos informations d’authentification à chaque affichage. 

    Le contrôle de carte contient ses propres méthodes de cycle de vie pour la gestion du cycle de vie OpenGL d’Android. Elles doivent être appelées directement à partir de l’activité contenante. Pour que votre application appelle correctement les méthodes de cycle de vie du contrôle de carte, vous devez remplacer les méthodes de cycle de vie suivantes dans l'activité qui contient le contrôle de carte et appeler la méthode de contrôle de carte correspondante. 

    * `onCreate(Bundle)` 
    * `onStart()` 
    * `onResume()` 
    * `onPause()` 
    * `onStop()` 
    * `onDestroy()` 
    * `onSaveInstanceState(Bundle)` 
    * `onLowMemory()`

    Modifiez le fichier **MainActivity.java** comme suit :
    
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
            AzureMaps.setSubscriptionKey("<Your Azure Maps subscription key>");
        }

        MapControl mapControl;

        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);

            mapControl = findViewById(R.id.mapcontrol);

            mapControl.onCreate(savedInstanceState);
    
            //Wait until the map resources are ready.
            mapControl.onReady(map -> {
                //Add your post map load code here.
    
            });
        }

        @Override
        public void onResume() {
            super.onResume();
            mapControl.onResume();
        }

        @Override
        protected void onStart(){
            super.onStart();
            mapControl.onStart();
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

Si vous exécutez votre application, le contrôle de carte se charge comme suit.

<center>

![Azure Maps simple](media/migrate-google-maps-android-app/simple-azure-maps.png)</center>

Notez que le contrôle Azure Maps prend davantage en charge le zoom arrière et qu’il fournit un affichage du monde plus étendu.

> [!TIP]
> Si vous utilisez un émulateur Android dans Windows, la carte peut ne pas s’afficher en raison de conflits avec OpenGL et le rendu graphique accéléré par logiciel. Voici comment résoudre éventuellement ce problème. Ouvrez le gestionnaire AVD et sélectionnez l’appareil virtuel à modifier. Dans la section **Emulated Performance** (Performances émulées), définissez l’option **Graphics** sur **Hardware** (Matériel).

## <a name="localizing-the-map"></a>Localisation de la carte

Si votre audience est répartie dans plusieurs pays ou parle différentes langues, la localisation est importante.

**Avant : Google Maps**

Vous pouvez définir la langue de la carte dans la méthode `onCreate` de l’activité principale en ajoutant le code suivant avant de définir l’affichage contextuel de la carte. Le code suivant limite la langue au français à l’aide du code de langue « fr ».

```java
String languageToLoad = "fr";
Locale locale = new Locale(languageToLoad);
Locale.setDefault(locale);
Configuration config = new Configuration();
config.locale = locale;
getBaseContext().getResources().updateConfiguration(config,
        getBaseContext().getResources().getDisplayMetrics());
```

Voici un exemple de Google Maps avec la langue définie sur « fr ».

<center>

![Localisation de Google Maps](media/migrate-google-maps-android-app/google-maps-localization.png)</center>

**Après : Azure Maps**

Azure Maps propose trois méthodes pour définir la langue et l’affichage régional de la carte. La première option consiste à transférer les informations de la langue et de l’affichage régional dans la classe `AzureMaps` en utilisant les méthodes statiques `setLanguage` et `setView` globalement. Cela permet de définir la langue et l’affichage régional par défaut pour tous les contrôles Azure Maps chargés dans votre application. Le code suivant limite la langue au français à l’aide du code de langue « fr-FR ».

```java
static {
    //Set your Azure Maps Key.
    AzureMaps.setSubscriptionKey("<Your Azure Maps Key>");

    //Set the language to be used by Azure Maps.
    AzureMaps.setLanguage("fr-FR");

    //Set the regional view to be used by Azure Maps.
    AzureMaps.setView("auto");
}
```

La deuxième option consiste à transférer la langue et à afficher les informations dans le code XML du contrôle de carte.

```xml
<com.microsoft.azure.maps.mapcontrol.MapControl
    android:id="@+id/myMap"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:mapcontrol_language="fr-FR"
    app:mapcontrol_view="auto"
    />
```

La troisième option consiste à définir la langue et l'affichage régional de la carte par programmation à l'aide de la méthode `setStyle`. Cette opération peut être effectuée à tout moment pour changer la langue et l'affichage régional de la carte.

```java
mapControl.onReady(map -> {
    map.setStyle(StyleOptions.language("fr-FR"));
    map.setStyle(StyleOptions.view("auto"));
});
```

Voici un exemple d’Azure Maps avec la langue définie sur « fr-FR ».

<center>

![Localisation de Google Maps](media/migrate-google-maps-android-app/azure-maps-localization.png)</center>

La liste complète des langues et des affichages régionaux pris en charge est disponible [ici](supported-languages.md).

## <a name="setting-the-map-view"></a>Définition de la vue cartographique

Vous pouvez déplacer par programmation les cartes dynamiques dans Azure et Google Maps vers de nouvelles positions géographiques en appelant les méthodes appropriées. Les exemples ci-dessous montrent comment faire en sorte que la carte affiche une image aérienne satellite, centrer la carte sur une position avec des coordonnées (latitude : 35,0272, longitude : -111,0225) et définir le niveau de zoom sur 15 dans Google Maps.

> [!NOTE]
> Google Maps utilise des mosaïques de 256 pixels, tandis qu’Azure Maps utilise une mosaïque de 512 pixels. Cela réduit le nombre de demandes réseau nécessaires à Azure Maps pour charger la même zone réactive que Google Maps. Toutefois, en raison de la façon dont les pyramides de mosaïques fonctionnent dans les contrôles de carte et de la plus grande taille des mosaïques dans Azure Maps, vous devez soustraire le niveau de zoom utilisé dans Google Maps d’une unité lors de l’utilisation d’Azure Maps pour obtenir cette même zone visualisable sous forme de carte dans Google Maps.

**Avant : Google Maps**

La caméra du contrôle de carte Google Maps peut être déplacée par programmation à l’aide de la méthode `moveCamera`, qui vous permet de spécifier le centre de la carte et un niveau de zoom. La méthode `setMapType` peut être utilisée pour changer le type de carte qui est affiché.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.moveCamera(CameraUpdateFactory.newLatLngZoom(new LatLng(35.0272, -111.0225), 15));
    mapView.setMapType(GoogleMap.MAP_TYPE_SATELLITE);
}
```

<center>

![Vue définie de Google Maps](media/migrate-google-maps-android-app/google-maps-set-view.png)</center>

**Après : Azure Maps**

Comme indiqué précédemment, pour obtenir la même zone visualisable dans Azure Maps, diminuez le niveau de zoom utilisé dans Google Maps d’une unité ; en l’occurrence, utilisez un niveau de zoom de 14.

La vue cartographique initiale peut être définie dans des attributs XML sur le contrôle de carte.

```xml
<com.microsoft.azure.maps.mapcontrol.MapControl
    android:id="@+id/myMap"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:mapcontrol_cameraLat="35.0272"
    app:mapcontrol_cameraLng="-111.0225"
    app:mapcontrol_zoom="14"
    app:mapcontrol_style="satellite"
    />
```

La vue cartographique peut être mise à jour par programmation à l’aide des méthodes maps `setCamera` et `setStyle`.

```java
mapControl.onReady(map -> {
    //Set the camera of the map.
    map.setCamera(center(35.0272, -111.0225), zoom(14));

    //Set the style of the map.
    map.setStyle(style(MapStyle.SATELLITE));
});
```

<center>

![Vue définie d’Azure Maps](media/migrate-google-maps-android-app/azure-maps-set-view.png)</center>

**Ressources supplémentaires :**

- [Styles de carte pris en charge](supported-map-styles.md)

## <a name="adding-a-marker"></a>Ajout d’un marqueur

Les données de point sont souvent matérialisées sur la carte à l’aide d’une image. Ces images sont souvent appelées marqueurs, punaises, épingles ou symboles. Les exemples suivants affichent des données de point sous forme de marqueurs sur la carte à la position (latitude : 51,5, longitude : -0,2).

**Avant : Google Maps**

Avec Google Maps, les marqueurs sont ajoutés à l’aide de la méthode maps `addMarker`.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.addMarker(new MarkerOptions().position(new LatLng(47.64, -122.33)));
}
```

<center>

![Marqueur Google Maps](media/migrate-google-maps-android-app/google-maps-marker.png)</center>

**Après : Azure Maps**

Dans Azure Maps, vous pouvez afficher les données de point sur la carte en les ajoutant à une source de données, puis en connectant cette source de données à une couche de symboles. La source de données optimise la gestion des données spatiales dans le contrôle de carte, tandis que la couche de symboles spécifie comment afficher les données de point à l’aide d’une image et/ou d’un texte.

```java
mapControl.onReady(map -> {
    //Create a data source and add it to the map.
    DataSource dataSource = new DataSource();
    map.sources.add(dataSource);

    //Create a point feature and add it to the data source.
    dataSource.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64)));

    //Create a symbol layer and add it to the map.
    map.layers.add(new SymbolLayer(dataSource));
});
```

<center>

![Marqueur Azure Maps](media/migrate-google-maps-android-app/azure-maps-marker.png)</center>

## <a name="adding-a-custom-marker"></a>Ajout d’un marqueur personnalisé

Les images personnalisées peuvent être utilisées pour représenter des points sur une carte. L’image suivante est utilisée dans les exemples ci-dessous. Utilisez une image personnalisée pour afficher un point sur la carte à la position (latitude : 51,5, longitude : -0,2) et décale la position du marqueur afin que le point de l’icône de la punaise s’aligne avec la position correcte sur la carte.

<center>

![image de punaise jaune](media/migrate-google-maps-web-app/ylw_pushpin.png)<br/>
ylw\_pushpin.png</center>

Dans les deux exemples, l’image ci-dessus est ajoutée au dossier drawable des ressources des applications.

**Avant : Google Maps**

Avec Google Maps, vous pouvez utiliser des images personnalisées pour les marqueurs en les chargeant par le biais de l’option `icon` du marqueur. Pour aligner le point de l’image sur la coordonnée, vous pouvez utiliser l’option `anchor`. L’ancre est relative aux dimensions de l’image, en l’occurrence 0,2 unité de large et 1 unité de haut.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.addMarker(new MarkerOptions().position(new LatLng(47.64, -122.33))
    .icon(BitmapDescriptorFactory.fromResource(R.drawable.ylw_pushpin))
    .anchor(0.2f, 1f));
}
```

<center>

![Marqueur personnalisé Google Maps](media/migrate-google-maps-android-app/google-maps-custom-marker.png)</center>

**Après : Azure Maps**

Dans Azure Maps, les couches de symboles prennent également en charge les images personnalisées, mais l’image doit d’abord être chargée dans les ressources de carte et recevoir un ID unique. La couche de symboles peut ensuite référencer cet ID. Le symbole peut être décalé pour s’aligner sur le point correct sur l’image à l’aide de l’option `iconOffset`. Notez que le décalage de l’icône est en pixels. Par défaut, le décalage est relatif au centre inférieur de l’image, mais il peut être ajusté à l’aide de l’option `iconAnchor`. Cet exemple définit l’option `iconAnchor` sur `"center"` et utilise un décalage d’icône pour déplacer l’image de cinq pixels vers la droite et de 15 pixels vers le haut pour l’aligner sur le point de l’image de la punaise.

```java
mapControl.onReady(map -> {
    //Create a data source and add it to the map.
    DataSource dataSource = new DataSource();
    map.sources.add(dataSource);

    //Create a point feature and add it to the data source.
    dataSource.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64)));

    //Load the custom image icon into the map resources.
    map.images.add("my-yellow-pin", R.drawable.ylw_pushpin);

    //Create a symbol that uses the custom image icon and add it to the map.
    map.layers.add(new SymbolLayer(dataSource,
        iconImage("my-yellow-pin"),
        iconAnchor(AnchorType.CENTER
        iconOffset(new Float[]{5f, -15f})));
});
```

<center>

![Marqueur personnalisé Azure Maps](media/migrate-google-maps-android-app/azure-maps-custom-marker.png)</center>

## <a name="adding-a-polyline"></a>Ajout d’une polyligne

Les polylignes sont utilisées pour représenter une ligne ou un tracé sur la carte. Les exemples suivants montrent comment créer une polyligne en pointillés sur la carte.

**Avant : Google Maps**

Avec Google Maps, une polyligne peut être créée à l’aide de la classe `PolylineOptions` et ajoutée à la carte à l’aide de la méthode `addPolyline`. La couleur du trait peut être définie à l’aide de l’option `color`, l’épaisseur du trait est définie à l’aide de l’option width et un tableau de tirets de trait peut être défini à l’aide de l’option `pattern`.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    //Create the options for the polyline.
    PolylineOptions lineOptions = new PolylineOptions()
            .add(new LatLng(46, -123))
            .add(new LatLng(49, -122))
            .add(new LatLng(46, -121))
            .color(Color.RED)
            .width(10f)
            .pattern(Arrays.<PatternItem>asList(
                    new Dash(30f), new Gap(30f)));

    //Add the polyline to the map.
    Polyline polyline = mapView.addPolyline(lineOptions);
}
```

<center>

![Polyligne Google Maps](media/migrate-google-maps-android-app/google-maps-polyline.png)</center>

**Après : Azure Maps**

Dans Azure Maps, les polylignes sont appelées objets LineString ou MultiLineString. Ces objets peuvent être ajoutés à une source de données et restitués à l’aide d’une couche de lignes. Notez que les unités « pixel » de largeur de trait et de tableau de tirets s’alignent sur le SDK web Azure Maps, les mêmes valeurs dans les deux SDK produisant les mêmes résultats.

```java
mapControl.onReady(map -> {
    //Create a data source and add it to the map.
    DataSource dataSource = new DataSource();
    map.sources.add(dataSource);

    //Create an array of points.
    List<Point> points = Arrays.asList(
        Point.fromLngLat(-123, 46),
        Point.fromLngLat(-122, 49),
        Point.fromLngLat(-121, 46));

    //Create a LineString feature and add it to the data source.
    dataSource.add(Feature.fromGeometry(LineString.fromLngLats(points)));

    //Create a line layer and add it to the map.
    map.layers.add(new LineLayer(dataSource,
        strokeColor("red"),
        strokeWidth(4f),
        strokeDashArray(new Float[]{3f, 3f})));
});
```

<center>

![Polyligne Azure Maps](media/migrate-google-maps-android-app/azure-maps-polyline.png)</center>

## <a name="adding-a-polygon"></a>Ajout d’un polygone

Les polygones sont utilisés pour représenter une zone sur la carte. Les exemples suivants montrent comment créer un polygone qui forme un triangle en fonction de la coordonnée centrale de la carte.

**Avant : Google Maps**

Avec Google Maps, un polygone peut être créé à l’aide de la classe `PolygonOptions` et ajouté à la carte à l’aide de la méthode `addPolygon`. Les couleurs de remplissage et de trait peuvent être définies à l’aide de l’option `fillColor` et `strokeColor`, tandis que l’épaisseur du trait est définie à l’aide de l’option `strokeWidth`.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    //Create the options for the polygon.
    PolygonOptions polygonOptions = new PolygonOptions()
            .add(new LatLng(46, -123))
            .add(new LatLng(49, -122))
            .add(new LatLng(46, -121))
            .add(new LatLng(46, -123))  //Close the polygon.
            .fillColor(Color.argb(128, 0, 128, 0))
            .strokeColor(Color.RED)
            .strokeWidth(5f);

    //Add the polygon to the map.
    Polygon polygon = mapView.addPolygon(polygonOptions);
}
```

<center>

![Polygone Google Maps](media/migrate-google-maps-android-app/google-maps-polygon.png)</center>

**Après : Azure Maps**

Dans Azure Maps, les objets Polygone et Multipolygone peuvent être ajoutés à une source de données et restitués sur la carte à l’aide de couches. La zone d’un polygone peut être affichée dans une couche de polygones. Le contour d’un polygone peut être affiché à l’aide d’une couche de lignes. Notez que les unités « pixel » de largeur de trait et de tableau de tirets s’alignent sur le SDK web Azure Maps, les mêmes valeurs dans les deux SDK produisant les mêmes résultats.

```java
mapControl.onReady(map -> {
    //Create a data source and add it to the map.
    DataSource dataSource = new DataSource();
    map.sources.add(dataSource);

    //Create an array of point arrays to create polygon rings.
    List<List<Point>> rings = Arrays.asList(Arrays.asList(
        Point.fromLngLat(-123, 46),
        Point.fromLngLat(-122, 49),
        Point.fromLngLat(-121, 46),
        Point.fromLngLat(-123, 46)));

    //Create a Polygon feature and add it to the data source.
    dataSource.add(Feature.fromGeometry(Polygon.fromLngLats(rings)));

    //Add a polygon layer for rendering the polygon area.
    map.layers.add(new PolygonLayer(dataSource,
        fillColor("green"),
        fillOpacity(0.5f)));

    //Add a line layer for rendering the polygon outline.
    map.layers.add(new LineLayer(dataSource,
        strokeColor("red"),
        strokeWidth(2f)));
});
```

<center>

![Polygone Azure Maps](media/migrate-google-maps-android-app/azure-maps-polygon.png)</center>

## <a name="overlay-a-tile-layer"></a>Superposer une couche de mosaïques

Les couches de mosaïques, également appelées superpositions d’images dans Google Maps, vous permettent de superposer des images de grande taille qui ont été divisées en images en mosaïque plus petites qui s’alignent sur le système de mosaïque de cartes. Il s’agit d’un moyen courant de superposer des images de couche ou des jeu de données très volumineux.

Les exemples suivants superposent une couche de mosaïques radar météo issue de l’Iowa Environmental Mesonet de l’Iowa State University. La taille des mosaïques est de 256 pixels.

**Avant : Google Maps**

Avec Google Maps, vous pouvez superposer une couche de mosaïques à la carte à l’aide de la classe `TileOverlayOptions` et l’ajouter à la carte à l’aide de la méthode `addTileLauer`. Pour rendre les mosaïques semi-transparentes, l’option `transparency` est définie sur 0,2 (20 % de transparence).

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    //Create the options for the tile layer.
    TileOverlayOptions tileLayer = new TileOverlayOptions()
        .tileProvider(new UrlTileProvider(256, 256) {
            @Override
            public URL getTileUrl(int x, int y, int zoom) {

                try {
                    //Define the URL pattern for the tile images.
                    return new URL(String.format("https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/%d/%d/%d.png", zoom, x, y));
                }catch (MalformedURLException e) {
                    throw new AssertionError(e);
                }
            }
        }).transparency(0.2f);

    //Add the tile layer to the map.
    mapView.addTileOverlay(tileLayer);
}
```

<center>

![Couche de mosaïques Google Maps](media/migrate-google-maps-android-app/google-maps-tile-layer.png)</center>

**Après : Azure Maps**

Dans Azure Maps, une couche de mosaïques peut être ajoutée à la carte à peu près de la même façon que n’importe quelle autre couche. Une URL mise en forme qui a les espaces réservés x, y et zoom (`{x}`, `{y}`, `{z}` respectivement) est utilisée pour indiquer à la couche où accéder aux mosaïques. Les couches de mosaïques dans Azure Maps prennent également en charge les espaces réservés `{quadkey}`, `{bbox-epsg-3857}` et `{subdomain}`. Pour rendre la couche de mosaïques semi-transparente, une valeur d’opacité de 0,8 est utilisée. Notez que l’opacité et la transparence, bien que similaires, utilisent des valeurs inverses. Pour les convertir entre elles, il suffit de soustraire leur valeur du nombre 1.

> [!TIP]
> Dans Azure Maps, les couches peuvent facilement être rendues sous d’autres couches, y compris les couches de la carte de base. Il est souvent souhaitable de restituer des couches de mosaïques sous les étiquettes de carte afin qu’elles soient faciles à lire. La méthode `map.layers.add` prend un deuxième paramètre, qui est l’ID de la couche sous laquelle insérer la nouvelle couche. Pour insérer une couche de mosaïques sous les étiquettes de carte, le code suivant peut être utilisé : `map.layers.add(myTileLayer, "labels");`

```java
mapControl.onReady(map -> {
    //Add a tile layer to the map, below the map labels.
    map.layers.add(new TileLayer(
        tileUrl("https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png"),
        opacity(0.8f),
        tileSize(256)
    ), "labels");
});
```

<center>

![Couche de mosaïques Azure Maps](media/migrate-google-maps-android-app/azure-maps-tile-layer.png)</center>

## <a name="show-traffic"></a>Afficher le trafic

Les données de trafic peuvent être superposées à la fois dans Azure et Google Maps.

**Avant : Google Maps**

Avec Google Maps, vous pouvez superposer les données de flux de trafic à la carte en passant la valeur true à la méthode `setTrafficEnabled` de la carte.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.setTrafficEnabled(true);
}
```

<center>

![Trafic Google Maps](media/migrate-google-maps-android-app/google-maps-traffic.png)</center>

**Après : Azure Maps**

Azure Maps offre plusieurs options différentes pour l’affichage du trafic. Les incidents de trafic, tels que les fermetures de route et les accidents, peuvent être affichés sous forme d’icônes sur la carte. Des routes codées en couleurs représentant le flux de trafic peuvent être superposées sur la carte ; les couleurs peuvent être modifiées pour tenir compte de la limite de vitesse indiquée, d’un retard prévu normal ou d’un retard absolu. Les données d’incident dans Azure Maps sont mises à jour toutes les minutes et les données de transit toutes les deux minutes.

```java
mapControl.onReady(map -> {
    map.setTraffic(
        incidents(true),
        flow(TrafficFlow.RELATIVE));
});
```

<center>

![Trafic Azure Maps](media/migrate-google-maps-android-app/azure-maps-traffic.png)</center>

## <a name="next-steps"></a>Étapes suivantes

Découvrez-en plus sur Android SDK Azure Maps.

> [!div class="nextstepaction"]
> [Guide pratique pour utiliser le contrôle de carte Android](how-to-use-android-map-control-library.md)
