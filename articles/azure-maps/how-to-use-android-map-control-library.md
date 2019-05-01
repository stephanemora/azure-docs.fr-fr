---
title: Mise en route avec le contrôle de carte Android dans Azure Maps | Microsoft Docs
description: Le contrôle de carte Android dans Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: e655b442ba9290d4b4525108521f2d1a0c766b48
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/29/2019
ms.locfileid: "64869825"
---
# <a name="getting-started-with-azure-maps-android-sdk"></a>Mise en route avec le Kit de développement logiciel Android d’Azure Maps

Le Kit de développement logiciel Android d’Azure Maps est une bibliothèque de carte de vecteur pour Android. Cet article vous guide tout au long du processus d’installation du SDK Android de Azure Maps et le chargement d’une carte.

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

1. Ouvrez le niveau supérieur **build.gradle** fichier, puis ajoutez le code suivant à la **tous les projets**, **référentiels** bloquer la section :

    ```
    maven {
            url "https://atlas.microsoft.com/sdk/android"
    }
    ```

2. Mise à jour votre **App/build.gradle** et lui ajouter le code suivant :
    
    1. Assurez-vous que l’option de votre projet **minSdkVersion** est à l’API 21 ou version ultérieure.

    2. Ajoutez le code suivant à la section Android :

        ```
        compileOptions {
            sourceCompatibility JavaVersion.VERSION_1_8
            targetCompatibility JavaVersion.VERSION_1_8
        }
        ```
    3. Mettre à jour de votre bloc de dépendances et ajouter une nouvelle ligne de dépendance d’implémentation pour la dernière version du SDK Android de Azure Maps :

        ```
        implementation "com.microsoft.azure.maps:mapcontrol:0.2"
        ```

    > [!Note]
    > Le Kit de développement logiciel Android d’Azure Maps est régulièrement mis à niveau et améliorée. Vous pouvez voir le [mise en route avec contrôle de carte Android](https://docs.microsoft.com/azure/azure-maps/how-to-use-android-map-control-library) documentation, pour obtenir le dernier numéro de version de mise en œuvre d’Azure Maps. En outre, vous pouvez définir le numéro de version à partir de « 0.2 » à « 0 » afin qu’il pointe toujours vers la dernière version.

3. Modifier **res** > **disposition** > **activity_main.xml** et remplacez-le par le code suivant :
    
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

4. Dans le **MainActivity.java** fichier, vous devez :
    
    * Ajoutez les importations pour le Kit de développement logiciel Azure Maps
    * Définissez vos informations d’authentification Azure Maps
    * obtenir l’instance de contrôle de carte le **onCreate** (méthode)

    Définir les informations d’authentification sur la classe AzureMaps globalement à l’aide de méthodes setSubscriptionKey ou setAadProperties rend vous ne devez donc ajouter vos informations d’authentification sur chaque vue. Le contrôle de carte contient ses propres méthodes de cycle de vie pour la gestion OpenGL du cycle de vie d’Android, qui doit être appelée directement à partir de l’activité conteneur. Dans l’ordre de votre application appeler correctement, les méthodes de cycle de vie du contrôle de carte, vous devez substituer les méthodes de cycle de vie suivantes dans l’activité qui contient le contrôle de carte et appelez la méthode de contrôle de carte respectifs. 

    Modifier le **MainActivity.java** de fichiers comme suit :
    
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

<center>

![Carte Android](./media/how-to-use-android-map-control-library/android-map.png)</center>

## <a name="next-steps"></a>Étapes suivantes

Pour ajouter des éléments à votre carte, consultez :

> [!div class="nextstepaction"]
> [Ajouter une couche de symbole à un mappage d’Android](https://review.docs.microsoft.com/azure/azure-maps/how-to-add-symbol-to-android-map)

> [!div class="nextstepaction"]
> [Ajouter des formes à un mappage d’Android](https://docs.microsoft.com/azure/azure-maps/how-to-add-shapes-to-android-map)

> [!div class="nextstepaction"]
> [Modifier les styles de carte dans les mappages Android](https://docs.microsoft.com/azure/azure-maps/set-android-map-styles)


