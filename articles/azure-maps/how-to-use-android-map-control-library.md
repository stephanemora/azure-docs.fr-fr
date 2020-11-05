---
title: Bien démarrer avec le contrôle de carte Android | Microsoft Azure Maps
description: Familiarisez-vous avec l’Android SDK Azure Maps. Apprenez à créer un projet dans Android Studio, à installer le Kit de développement logiciel (SDK) et à créer un mappage interactif.
author: anastasia-ms
ms.author: v-stharr
ms.date: 04/26/2019
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 72bb821c0dfed6d3f9e7e2cc222242e65a35a011
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2020
ms.locfileid: "92911050"
---
# <a name="getting-started-with-azure-maps-android-sdk"></a>Bien démarrer avec Android SDK Azure Maps

Android SDK Azure Maps est une bibliothèque de cartes de vecteur pour Android. Cet article vous guide tout au long des processus d’installation d’Android SDK Azure Maps et de chargement d’une carte.

## <a name="prerequisites"></a>Prérequis

### <a name="create-an-azure-maps-account"></a>Créer un compte Azure Maps

Pour accomplir les procédures de cet article, vous devez d’abord [créer un compte Azure Maps](quick-demo-map-app.md#create-an-azure-maps-account) de niveau tarifaire S1 et [obtenir la clé primaire](quick-demo-map-app.md#get-the-primary-key-for-your-account) de votre compte.

Pour plus d’informations sur l’authentification dans Azure Maps, voir [Gérer l’authentification dans Azure Maps](./how-to-manage-authentication.md).

### <a name="download-android-studio"></a>Télécharger Android Studio

Avant d'installer Android SDK Azure Maps, téléchargez Android Studio et créez un projet avec une activité vide. Vous pouvez [télécharger Android Studio](https://developer.android.com/studio/) gratuitement sur Google. 

## <a name="create-a-project-in-android-studio"></a>Créer un projet dans Android Studio

Commencez par créer un projet avec une activité vide. Suivez ces étapes pour créer un projet Android Studio :

1. Sous **Choose your project** (Choisissez votre projet), sélectionnez **Phone and Tablet** (Téléphone et tablette). Votre application s’exécutera sur ce facteur de forme.
2. Dans l’onglet **Phone and Tablet** (Téléphone et tablette), sélectionnez **Empty Activity** (Activité vide), puis sélectionnez **Next** (Suivant).
3. Sous **Configurer votre projet** , sélectionnez `API 21: Android 5.0.0 (Lollipop)` comme SDK minimum. Il s’agit de la version minimale prise en charge par Android SDK Azure Maps.
4. Acceptez les valeurs par défaut `Activity Name` et `Layout Name`, puis cliquez sur **Finish** (Terminer).

Pour obtenir de l’aide supplémentaire sur l’installation d’Android Studio et la création d’un projet, consultez la [documentation Android Studio](https://developer.android.com/studio/intro/).

![Créer un projet dans Android Studio ](./media/how-to-use-android-map-control-library/form-factor-android.png)

## <a name="set-up-a-virtual-device"></a>Configurer un appareil virtuel

Android Studio vous permet de configurer un appareil Android virtuel sur votre ordinateur. Cela peut vous aider à tester votre application pendant le développement. Pour configurer un appareil virtuel, sélectionnez l’icône du gestionnaire d’appareil virtuel Android (AVD, Android Virtual Device) dans le coin supérieur droit de votre écran de projet, puis sélectionnez **Create Virtual Device** (Créer un appareil virtuel). Vous pouvez également accéder au gestionnaire AVD en sélectionnant **Tools (Outils)**  > **Android** > **AVD Manager (Gestionnaire AVD)** à partir de la barre d’outils. Dans la catégorie **Phones** (Téléphones), sélectionnez **Nexus 5X** et cliquez sur **Next** (Suivant).

Pour en savoir plus sur la configuration d’un appareil virtuel Android, consultez la [documentation Android Studio](https://developer.android.com/studio/run/managing-avds).

![Émulateur Android](./media/how-to-use-android-map-control-library/android-emulator.png)

## <a name="install-the-azure-maps-android-sdk"></a>Installer Android SDK Azure Maps

L’étape suivante de la création de votre application consiste à installer Android SDK Azure Maps. Pour installer le Kit de développement logiciel (SDK), procédez comme suit :

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
        implementation "com.microsoft.azure.maps:mapcontrol:0.6"
        ```
    
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

4. Dans le fichier **MainActivity.java** , vous devez :
    
    * Ajouter des importations pour le Kit de développement logiciel (SDK) Azure Maps
    * Définir vos informations d’authentification Azure Maps
    * Obtenir l’instance de contrôle de carte dans la méthode **onCreate**

    Si vous définissez les informations d’authentification sur la classe `AzureMaps` de façon globale à l’aide des méthodes `setSubscriptionKey` ou `setAadProperties`, vous n’aurez pas besoin d’ajouter vos informations d’authentification à chaque affichage. 

    Le contrôle de carte contient ses propres méthodes de cycle de vie pour la gestion du cycle de vie OpenGL d’Android. Ces méthodes de cycle de vie doivent être appelées directement à partir de l’activité contenante. Pour que votre application appelle correctement les méthodes de cycle de vie du contrôle de carte, vous devez substituer les méthodes de cycle de vie suivantes dans l’activité qui contient le contrôle de carte. Vous devez aussi appeler la méthode de contrôle de carte correspondante. 

    * onCreate(Bundle) 
    * onStart() 
    * onResume() 
    * onPause() 
    * onStop() 
    * onDestroy() 
    * onSaveInstanceState(Bundle) 
    * onLowMemory() 

    Modifiez le fichier **MainActivity.java** comme suit :
    
    ```java
    package com.example.myapplication;

    //For older versions use: import android.support.v7.app.AppCompatActivity; 
    import androidx.appcompat.app.AppCompatActivity;
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

## <a name="import-classes"></a>Importer des classes

Après avoir accompli les étapes précédentes, vous obtiendrez probablement des avertissements d’Android Studio concernant certaines parties de code. Pour résoudre ces avertissements, importez les classes référencées dans `MainActivity.java`.

Vous pouvez importer automatiquement ces classes en appuyant sur Alt + Entrée (Option + Retour sur un Mac).

Sélectionnez le bouton d’exécution comme illustré dans la figure suivante (ou appuyez sur CTRL + R sur un Mac) pour générer votre application.

![Cliquez sur Exécuter.](./media/how-to-use-android-map-control-library/run-app.png)

La création de l’application par Android Studio prend quelques secondes. Une fois la création terminée, vous pouvez tester votre application dans l’appareil Android émulé. La carte doit ressembler à ceci :

<center>

![Azure Maps dans une application Android](./media/how-to-use-android-map-control-library/android-map.png)</center>

## <a name="localizing-the-map"></a>Localisation de la carte

Android SDK Azure Maps propose trois méthodes différentes pour définir la langue et l'affichage régional de la carte. Le code suivant montre comment définir la langue sur le français (« fr-FR ») et l'affichage régional sur « auto ». 

La première option consiste à transférer la langue et à afficher les informations régionales dans la classe `AzureMaps` en utilisant les méthodes statiques `setLanguage` et `setView` globalement. Cela permet de définir la langue et l'affichage régional par défaut pour tous les contrôles Azure Maps chargés dans votre application.

```Java
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

```XML
<com.microsoft.azure.maps.mapcontrol.MapControl
    android:id="@+id/myMap"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:mapcontrol_language="fr-FR"
    app:mapcontrol_view="auto"
    />
```

La troisième option consiste à définir la langue et l'affichage régional de la carte par programmation à l'aide de la méthode `setStyle`. Cette opération peut être effectuée à tout moment pour changer la langue et l'affichage régional de la carte.

```Java
mapControl.onReady(map -> {
    map.setStyle(StyleOptions.language("fr-FR"));
    map.setStyle(StyleOptions.view("auto"));
});
```

Voici un exemple Azure Maps dans lequel la langue est définie sur « fr-FR » et l'affichage régional sur « auto ».

<center>

![Azure Maps, image de carte affichant des étiquettes en français](./media/how-to-use-android-map-control-library/android-localization.png)
</center>

La liste complète des langues et des affichages régionaux pris en charge est disponible [ici](supported-languages.md).

## <a name="navigating-the-map"></a>Navigation dans la carte

Il existe plusieurs façons d’effectuer des zooms, des panoramiques, des rotations et des tangages de la carte. Les informations suivantes détaillent les différentes façons de naviguer dans la carte.

**Faire un zoom sur la carte**

- Touchez la carte avec deux doigts et rapprochez-les pour faire un zoom arrière ou écartez-les pour faire un zoom avant.
- Appuyez deux fois sur la carte pour faire un zoom avant d’un niveau.
- Appuyez deux fois avec deux doigts pour faire un zoom arrière d’un niveau de la carte.
- Appuyez deux fois ; sur le second appui, maintenez votre doigt sur la carte et faites glisser vers le haut pour effectuer un zoom avant, ou vers le bas pour effectuer un zoom arrière.

**Effectuer un panoramique sur la carte**

- Touchez la carte et faites-la glisser dans n’importe quelle direction.

**Faire pivoter la carte**

- Touchez la carte avec deux doigts et effectuez une rotation.

**Incliner la carte**

- Touchez la carte avec deux doigts et faites-les glisser ensemble vers le haut ou vers le bas.

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment ajouter des données superposées sur la carte :

> [!div class="nextstepaction"]
> [Add a symbol layer to an Android map](how-to-add-symbol-to-android-map.md) (Ajouter une couche de symboles à une carte Android)

> [!div class="nextstepaction"]
> [Ajouter des formes à une carte Android](./how-to-add-shapes-to-android-map.md)

> [!div class="nextstepaction"]
> [Change map styles in Android maps](./set-android-map-styles.md) (Modifier les styles de carte dans les cartes Android)