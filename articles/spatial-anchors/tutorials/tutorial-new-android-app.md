---
title: 'Tutoriel : Créer une application Android'
description: Dans ce tutoriel, vous allez apprendre à créer une application Android à l’aide d’Azure Spatial Anchors.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 11/20/2020
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: e88654f0dc118fa09d6c1d83287519f3cdbe7363
ms.sourcegitcommit: da9335cf42321b180757521e62c28f917f1b9a07
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/16/2021
ms.locfileid: "122229052"
---
# <a name="tutorial-step-by-step-instructions-to-create-a-new-android-app-using-azure-spatial-anchors"></a>Tutoriel : Instructions pas à pas pour créer une application Android à l’aide d’Azure Spatial Anchors

Ce tutoriel vous montre comment créer une application Android qui intègre la fonctionnalité ARCore avec Azure Spatial Anchors.

## <a name="prerequisites"></a>Prérequis

Pour suivre ce tutoriel, veillez à disposer des éléments suivants :

- Un ordinateur Windows ou macOS avec <a href="https://developer.android.com/studio/" target="_blank">Android Studio 3.4+</a>.
- Un appareil Android <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">prêt pour le développement</a> et <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">compatible ARCore</a>.

## <a name="getting-started"></a>Prise en main

Démarrez Android Studio. Dans la fenêtre d’accueil **Welcome to Android Studio**, cliquez sur **Start a new Android Studio project**. 
1. Sélectionnez **Fichier**->**Nouveau projet**.
1. Dans la fenêtre **Create New Projets**, sous la section **Phone and Tablet** , choisissez **Empty Activity**, puis cliquez sur **Next**.
1. Dans la fenêtre Nouveau projet - Activité vide, modifiez les valeurs suivantes :
   - Modifiez le **Nom**, le **Nom du package** et l’**Emplacement d’enregistrement** aux valeurs souhaitées
   - Réglez **Langage** sur `Java`
   - Réglez **Niveau d’API minimal** sur `API 26: Android 8.0 (Oreo)`
   - Laissez les autres options telles quelles
   - Cliquez sur **Terminer**. 
1. Le programme d’installation de composants **Component Installer** s’exécute. Après quelques traitements, Android Studio ouvre l’IDE.

![Android Studio - Nouveau projet](../../../includes/media/spatial-anchors-androidstudio/androidstudio-newproject.png)

<!-- maybe snapshot here -->

## <a name="trying-it-out"></a>Essai

Pour tester votre nouvelle application, connectez votre appareil prêt pour le développement à votre ordinateur de développement à l’aide d’un câble USB. En haut à droite d’Android Studio, sélectionnez votre appareil connecté, puis cliquez sur l’icône **Exécuter l’application**. Android Studio installe l’application sur votre appareil connecté et le démarre. « Hello World! » doit maintenant s’afficher dans l’application qui s’exécute sur votre appareil. Cliquez sur **Run**->**Stop 'app'** .
![Android Studio - Exécuter](../../../includes/media/spatial-anchors-androidstudio/androidstudio-run.png)


## <a name="integrating-_arcore_"></a>Intégration d’_ARCore_

<a href="https://developers.google.com/ar/discover/" target="_blank">_ARCore_</a> est la plateforme de Google dédiée à la création d’expériences de réalité augmentée, ce qui permet à votre appareil de suivre sa position au fur et à mesure de ses déplacements et de développer sa propre compréhension du monde réel.

Modifiez `app\manifests\AndroidManifest.xml` pour inclure les entrées suivantes au sein du nœud `<manifest>` racine. Cet extrait de code fait plusieurs choses :

- Il permet à votre application d’accéder à la caméra de votre appareil.
- Il garantit aussi que seuls les appareils qui prennent en charge ARCore peuvent voir votre application sur le Google Play Store.
- Il configure le Google Play Store de façon à télécharger et à installer ARCore, si ce n’est déjà fait, pendant l’installation de votre application.

```xml
<manifest ...>

    <uses-permission android:name="android.permission.CAMERA" />
    <uses-feature android:name="android.hardware.camera.ar" />

    <application>
        ...
        <meta-data android:name="com.google.ar.core" android:value="required" />
        ...
    </application>

</manifest>
```

Modifiez `Gradle Scripts\build.gradle (Module: app)` pour y inclure l’entrée ci-dessous. Ce code garantit que votre application cible ARCore version 1.25. Après cette modification, il se peut que vous obteniez une notification de Gradle qui vous invite à effectuer une synchronisation : cliquez sur **Sync now**.

```gradle
dependencies {
    ...
    implementation 'com.google.ar:core:1.25.0'
    ...
}
```

## <a name="integrating-_sceneform_"></a>Intégration de _Sceneform_

[_Sceneform_](https://developers.google.com/sceneform/develop/) simplifie le rendu de scènes 3D réalistes dans les applications de réalité augmentée sans qu’il soit nécessaire d’apprendre OpenGL.

Modifiez `Gradle Scripts\build.gradle (Module: app)` pour y inclure les entrées ci-dessous. Ce code permet à votre application d’utiliser des constructions de langage à partir de Java 8, ce qui est une exigence de `Sceneform`. Cela permet également de garantir que votre application cible `Sceneform` version 1.15. Après cette modification, il se peut que vous obteniez une notification de Gradle qui vous invite à effectuer une synchronisation : cliquez sur **Sync now**.

```gradle
android {
    ...

    compileOptions {
        sourceCompatibility JavaVersion.VERSION_1_8
        targetCompatibility JavaVersion.VERSION_1_8
    }
}

dependencies {
    ...
    implementation 'com.google.ar.sceneform.ux:sceneform-ux:1.15.0'
    ...
}
```

Ouvrez votre fichier `app\res\layout\activity_main.xml` et remplacez l’élément `<TextView ... />` Hello World existant par l’ArFragment suivant. Ce code a pour effet d’afficher le flux de caméra sur votre écran, ce qui permet à ARCore de suivre la position de votre appareil au fur et à mesure de ses déplacements.


```xml
<fragment android:name="com.google.ar.sceneform.ux.ArFragment"
    android:id="@+id/ux_fragment"
    android:layout_width="match_parent"
    android:layout_height="match_parent" />
```

> [!NOTE]
> Pour afficher les données XML brutes de votre activité principale, cliquez sur le bouton « Code » ou « Fractionner » dans le coin supérieur droit d’Android Studio.

[Redéployez](#trying-it-out) votre application sur votre appareil pour la vérifier une nouvelle fois. Cette fois, vous êtes invité à fournir des autorisations pour la caméra. Après approbation, le rendu du flux de caméra doit s’afficher sur votre écran.

## <a name="place-an-object-in-the-real-world"></a>Placer un objet dans le monde réel

Vous allez maintenant créer et placer un objet à l’aide de votre application. Pour commencer, ajoutez les importations suivantes à `app\java\<PackageName>\MainActivity` :

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=21-23,27-33,17-18)]

Ajoutez ensuite les variables de membre suivantes dans votre classe `MainActivity` :

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=47-52)]

Ajoutez maintenant le code suivant à la méthode `onCreate()` de votre `app\java\<PackageName>\MainActivity`. Ce code raccorde un écouteur, appelé `handleTap()`, qui détecte les appuis de l’utilisateur sur l’écran de votre appareil. S’il appuie sur une surface du monde réel qui a déjà été reconnue par le suivi d’ARCore, l’écouteur s’exécute.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=63-69,80&highlight=6-7)]

Enfin, ajoutez la méthode `handleTap()` suivante pour tout lier ensemble. Une sphère est alors créée et placée là où l’utilisateur a appuyé. Au départ, la sphère est noire, car à cet instant, `this.recommendedSessionProgress` est défini sur zéro. Cette valeur sera ajustée par la suite.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=159-167,179-180,183-192,209)]

[Redéployez](#trying-it-out) votre application sur votre appareil pour la vérifier une nouvelle fois. Cette fois, vous pouvez déplacer votre appareil pour permettre à ARCore de commencer à reconnaître votre environnement. Appuyez ensuite sur l’écran pour créer et placer votre sphère noire sur la surface de votre choix.

## <a name="attach-a-local-azure-spatial-anchor"></a>Attacher une ancre spatiale Azure locale

Modifiez `Gradle Scripts\build.gradle (Module: app)` pour y inclure l’entrée ci-dessous. Cet exemple d’extrait de code cible la version 2.10.2 du kit de développement logiciel (SDK) d’Azure Spatial Anchors. Notez que la version du kit de développement logiciel (SDK) 2.7.0 est affichage la version minimale prise en charge et que vous référencez une version plus récente d’Azure Spatial Anchors. Nous vous recommandons d’utiliser la dernière version du kit de développement logiciel (SDK) d’Azure Spatial Anchors. Vous trouverez les notes de publication du kit de développement logiciel (SDK) [ici](https://github.com/Azure/azure-spatial-anchors-samples/releases).

```gradle
dependencies {
    ...
    implementation 'com.microsoft.azure.spatialanchors:spatialanchors_jni:[2.10.2]'
    implementation 'com.microsoft.azure.spatialanchors:spatialanchors_java:[2.10.2]'
    ...
}
```

Si vous ciblez le kit de développement logiciel (SDK) d’Azure Spatial Anchors 2.10.0 ou une version ultérieure, incluez l’entrée suivante dans la section des référentiels du fichier `settings.gradle` de votre projet. Cela inclura l’URL du flux de package Maven qui héberge les packages Android d’Azure Spatial Anchors pour le SDK 2.10.0 ou une version ultérieure : 

```gradle
dependencyResolutionManagement {
    ...
    repositories {
        ...
        maven {
            url 'https://pkgs.dev.azure.com/aipmr/MixedReality-Unity-Packages/_packaging/Maven-packages/maven/v1'
        }
        ...
    }
}
```

Cliquez avec le bouton droit sur `app\java\<PackageName>`->**New**->**Java Class**. Affectez à **Nom** la valeur _MyFirstApp_, puis sélectionnez **Classe**. Un fichier appelé `MyFirstApp.java` est alors créé. Ajoutez-y l’importation suivante :

```java
import com.microsoft.CloudServices;
```

Définissez `android.app.Application` comme sa superclasse.
```java
public class MyFirstApp extends android.app.Application {...
```

Ajoutez ensuite le code suivant à l’intérieur de la nouvelle classe `MyFirstApp`, qui fait en sorte qu’Azure Spatial Anchors soit initialisé avec le contexte de votre application.

```java
    @Override
    public void onCreate() {
        super.onCreate();
        CloudServices.initialize(this);
    }
```

À présent, modifiez `app\manifests\AndroidManifest.xml` pour inclure l’entrée suivante au sein du nœud `<application>` racine. Ce code raccorde la classe Application que vous avez créée dans votre application.

```xml
    <application
        android:name=".MyFirstApp"
        ...
    </application>
```

De retour dans `app\java\<PackageName>\MainActivity`, ajoutez les importations suivantes :

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=17,16,18,24,26,35,36,38&highlight=2-8)]

Ajoutez ensuite les variables de membre suivantes dans votre classe `MainActivity` :

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=52-56&highlight=3-5)]

Ajoutez maintenant la méthode `initializeSession()` suivante au sein de la classe `mainActivity`. Une fois appelée, elle assurera la création et l’initialisation d’une session Azure Spatial Anchors pendant le démarrage de votre application. Ce code permet de s’assurer que la session sceneview transmise à la session ASA via l’appel `cloudSession.setSession` n’est pas null en faisant un retour anticipé.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=92-107,155)]

Étant donné que `initializeSession()` peut effectuer un retour anticipé si la session sceneView n’est pas encore configurée (c’est-à-dire, si `sceneView.getSession()` a la valeur null), nous ajoutons un appel OnUpdate pour s’assurer que la session ASA est initialisée une fois la session sceneView créée.
[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?name=scene_OnUpdate)]

Maintenant, raccordons les méthodes `initializeSession()` et `scene_OnUpdate(...)` à la méthode `onCreate()`. De même, veillez à ce que les images issues de votre flux de caméra sont envoyées au kit SDK Azure Spatial Anchors à des fins de traitement.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=63-80&highlight=9-17)]

Enfin, ajoutez le code suivant à votre méthode `handleTap()`. Ce code a pour effet d’attache une ancre spatiale Azure locale à la sphère noire que vous placez dans le monde réel.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=159-167,179-192,209&highlight=12-13)]

[Redéployez](#trying-it-out) une nouvelle fois votre application. Déplacez votre appareil, appuyez sur l’écran, puis placez une sphère noire. Cette fois, votre code crée une ancre spatiale Azure locale et l’attache à votre sphère.

Avant de poursuivre, vous devez créer un compte Azure Spatial Anchors pour obtenir l’identificateur, la clé et le domaine du compte, si vous ne les avez pas. Pour vous en procurer, suivez les instructions dans la section ci-dessous.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="upload-your-local-anchor-into-the-cloud"></a>Charger votre ancre locale dans le cloud

Une fois que vous disposez de l’identificateur, de la clé et du domaine du compte Azure Spatial Anchors, revenez dans `app\java\<PackageName>\MainActivity` et ajoutez-y les importations suivantes :

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=38-43&highlight=3-6)]

Ajoutez ensuite les variables de membre suivantes dans votre classe `MainActivity` :

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=56-61&highlight=3-6)]

À présent, ajoutez le code suivant à votre méthode `initializeSession()`. En premier lieu, ce code permet à votre application de surveiller la progression du kit SDK Azure Spatial Anchors à mesure qu’il collecte des images à partir de votre flux de caméra. Pendant ce temps, la couleur de votre sphère commence à passer de sa couleur noire initiale au gris. Ensuite, elle prend la couleur blanche une fois que le nombre d’images collectées est suffisant pour soumettre l’ancre au cloud. En second lieu, ce code fournit les informations d’identification nécessaires pour communiquer avec le back-end cloud. Voici où vous allez configurer votre application pour qu’elle utilise l’identificateur, la clé et le domaine de votre compte. Vous les avez copiés dans un éditeur de texte pendant la [configuration de la ressource Spatial Anchors](#create-a-spatial-anchors-resource).

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=92-130,151-155&highlight=17-43)]

Ensuite, ajoutez la méthode `uploadCloudAnchorAsync()` suivante à l’intérieur de votre classe `mainActivity`. Une fois appelée, cette méthode attend de manière asynchrone que le nombre d’images collectées à partir de votre appareil soit suffisant. Dès lors, elle fait passer la couleur de votre sphère au jaune et commence à charger l’ancre spatiale Azure locale dans le cloud. À l’issue du chargement, le code retourne un identificateur d’ancre.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?name=uploadCloudAnchorAsync)]

Enfin, raccordez tout ensemble. Dans votre méthode `handleTap()`, ajoutez le code suivant. Il appelle la méthode `uploadCloudAnchorAsync()` aussitôt que la sphère est créée. Une fois la méthode retournée, le code ci-dessous apporte une dernière mise à jour à la sphère en lui donnant la couleur bleue.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=159-167,179-209&highlight=26-39)]

[Redéployez](#trying-it-out) une nouvelle fois votre application. Déplacez votre appareil, appuyez sur l’écran, puis placez la sphère. Cette fois, la sphère change de couleur, passant du noir à blanc à mesure que les images de la caméra sont collectées. Une fois que le nombre d’images est suffisant, la sphère devient jaune et le chargement cloud débute. Assurez-vous que votre téléphone est connecté à Internet. À l’issue du chargement, la sphère passe au bleu. Si vous le souhaitez, vous pouvez aussi surveiller la fenêtre `Logcat` dans Android Studio pour voir les messages de journal envoyés par votre application. Des exemples de messages qui seraient enregistrés incluent la progression de la session pendant la capture de trames et l’identificateur d’ancre que le cloud renvoie une fois le téléchargement terminé.

> [!NOTE]
> Si vous ne voyez pas la valeur de `recommendedSessionProgress` (dans vos journaux de débogage comme `Session progress`) modification, assurez-vous que vous **déplacez et faites pivoter** votre téléphone autour de la sphère que vous avez placée.


## <a name="locate-your-cloud-spatial-anchor"></a>Localiser l’ancre spatiale cloud

Une fois que votre ancre est chargée dans le cloud, vous pouvez essayer de la localiser à nouveau. Pour commencer, ajoutez les importations suivantes à votre code.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=43,44,34,37&highlight=3-4)]

Ensuite, ajoutez le code suivant à votre méthode `handleTap()`. Ce code a pour effet de :

- Supprimer la sphère bleue existante de l’écran.
- Réinitialiser la session Azure Spatial Anchors. Cette action garantit que l’ancre à localiser provient du cloud et qu’il ne s’agit pas de l’ancre locale que vous avez créée.
- Émettez une requête pour l’ancre que vous avez chargée dans le cloud.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?name=handleTap&highlight=10-19)]

Maintenant, raccordez le code qui sera appelé dès que l’ancre recherchée sera localisée. À l’intérieur de votre méthode `initializeSession()`, ajoutez le code suivant. Cet extrait de compte crée et place une sphère verte une fois que l’ancre spatiale cloud est localisée. Il réactive également les appuis sur l’écran, ce qui vous permet de répéter une nouvelle fois le scénario dans son intégralité : création, chargement et relocalisation d’une nouvelle ancre.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?name=initializeSession&highlight=40-59)]

Et voilà ! [Redéployez](#trying-it-out) votre application une dernière fois pour essayer l’ensemble du scénario de bout en bout. Déplacez votre appareil, puis placez votre sphère noire. Continuez de déplacer votre appareil pour capturer les images de la caméra jusqu’à ce que la sphère devienne jaune. Votre ancre locale est alors chargée et la sphère devient bleue. Enfin, appuyez une nouvelle fois sur l’écran de façon à supprimer l’ancre locale, puis recherchez son équivalent cloud. Continuez de déplacer votre appareil jusqu’à ce que l’ancre spatiale cloud soit localisée. Une sphère verte doit alors apparaître à l’emplacement approprié, et vous pouvez effacer et répéter une nouvelle fois l’ensemble du scénario.

## <a name="putting-everything-together"></a>Regroupement

Voici comment le fichier de classe `MainActivity` complet doit se présenter une fois que les différents éléments ont été mis en ensemble. Vous pouvez vous en servir d’élément de comparaison pour votre propre fichier et pour repérer les différences.

[!INCLUDE [Share Anchors Sample Prerequisites](../../../includes/spatial-anchors-new-android-app-finished.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à créer une application Android qui intègre la fonctionnalité ARCore avec Azure Spatial Anchors. Pour en savoir plus sur la bibliothèque Azure Spatial Anchors, passez à notre guide sur la création et la localisation des ancres.

> [!div class="nextstepaction"]
> [Créer et localiser des ancres à l’aide d’Azure Spatial Anchors](../../../articles/spatial-anchors/create-locate-anchors-overview.md)