---
title: 'Démarrage rapide : Reconnaissance vocale, Java (Android) - Services de reconnaissance vocale'
titleSuffix: Azure Cognitive Services
description: Découvrir la reconnaissance vocale en Java sur Android à l’aide du SDK Speech
services: cognitive-services
author: fmegen
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 2/20/2019
ms.author: wolfma
ms.openlocfilehash: 690656449fdb86c200a8978f0e17db562e4abbca
ms.sourcegitcommit: e43ea344c52b3a99235660960c1e747b9d6c990e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/04/2019
ms.locfileid: "59009176"
---
# <a name="quickstart-recognize-speech-in-java-on-android-by-using-the-speech-sdk"></a>Démarrage rapide : Reconnaissance vocale en Java sur Android à l’aide du kit SDK de reconnaissance vocale

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Dans cet article, vous allez apprendre à créer une application Java pour Android à l’aide du SDK Cognitive Services Speech pour transcrire la parole en texte.
L’application est basée sur le package Maven, version 1.4.0 du SDK Speech, et sur Android Studio 3.3.
Le kit SDK Speech est actuellement compatible avec les appareils Android équipés de processeurs ARM 32/64 bits et Intel x86/x64 compatibles.

> [!NOTE]
> Pour le kit SDK Speech et l’appareil Roobo, consultez le [kit SDK Speech Devices](speech-devices-sdk.md).

## <a name="prerequisites"></a>Prérequis

Vous avez besoin d’une clé d’abonnement au service Speech pour suivre ce guide de démarrage rapide. Vous pouvez en obtenir une gratuitement. Pour plus d’informations, consultez [Essayer le service Speech gratuitement](get-started.md).

## <a name="create-and-configure-a-project"></a>Créer et configurer un projet

1. Lancez Android Studio, puis choisissez **Start a new Android Studio project** (Démarrer un nouveau projet Android Studio) dans la fenêtre d’accueil.

    ![Capture d’écran de la fenêtre d’accueil d’Android Studio](media/sdk/qs-java-android-01-start-new-android-studio-project.png)

1. L’Assistant **Choix de votre projet** apparaît : sélectionnez **Téléphone et tablette** et **Activité vide** dans la zone de sélection des activités. Sélectionnez **Suivant**.

   ![Capture d’écran de l’Assistant Choix de votre projet](media/sdk/qs-java-android-02-target-android-devices.png)

1. Dans l’écran **Configurer votre projet**, entrez **Quickstart** pour **Nom** et **samples.speech.cognitiveservices.microsoft.com** pour **Nom du package**, puis choisissez un répertoire de projet. Pour **Niveau d’API minimal** choisissez **API 23 : Android 6.0 (Marshmallow)**, laissez toutes les autres cases décochées, puis sélectionnez **Terminer**.

   ![Capture d’écran de l’Assistant Configuration de votre projet](media/sdk/qs-java-android-03-create-android-project.png)

Quelques minutes sont nécessaires à Android Studio pour préparer votre nouveau projet Android. Configurez ensuite le projet pour découvrir le kit SDK Speech et utiliser Java 8.

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

La version actuelle du kit SDK de reconnaissance vocale Cognitive Services est `1.4.0`.

Le SDK Speech pour Android est empaqueté au format [AAR (bibliothèque Android)](https://developer.android.com/studio/projects/android-library), qui inclut les bibliothèques nécessaires et les autorisations Android requises.
Il est hébergé dans un référentiel Maven sur https:\//csspeechstorage.blob.core.windows.net/maven/.

Configurez votre projet pour utiliser le kit SDK Speech. Ouvrez la fenêtre de structure du projet en choisissant **File (Fichier)** > **Project Structure (Structure de projet)** dans la barre de menus Android Studio. Dans la fenêtre de structure du projet, apportez les modifications suivantes :

1. Dans la liste située sur le côté gauche de la fenêtre, sélectionnez **Project** (Projet). Modifiez les paramètres **Default Library Repository** (Référentiel de bibliothèque par défaut) en ajoutant une virgule et l’URL du référentiel Maven, entourée de guillemets simples. 'https:\//csspeechstorage.blob.core.windows.net/maven/'

   ![Capture d’écran de la fenêtre de structure d’un projet](media/sdk/qs-java-android-06-add-maven-repository.png)

1. Dans le même écran, sur le côté gauche, sélectionnez **app** (application). Ensuite, sélectionnez l’onglet **Dependencies** (Dépendances) en haut de la fenêtre. Sélectionnez le signe plus vert (+), puis choisissez **Library dependency** (Dépendance de bibliothèque) dans le menu déroulant.

   ![Capture d’écran de la fenêtre de structure d’un projet](media/sdk/qs-java-android-07-add-module-dependency.png)

1. Dans la fenêtre qui s’affiche, entrez le nom et la version du kit SDK Speech pour Android, `com.microsoft.cognitiveservices.speech:client-sdk:1.4.0`. Sélectionnez ensuite **OK**.
   Le Kit de développement logiciel (SDK) Speech devrait à présent apparaître dans la liste des dépendances, comme indiqué ci-dessous :

   ![Capture d’écran de la fenêtre de structure d’un projet](media/sdk/qs-java-android-08-dependency-added-1.0.0.png)

1. Sélectionnez l’onglet **Properties** (Propriétés). À la fois pour **Source Compatibility** (Compatibilité source) et pour **Target Compatibility** (Compatibilité cible), sélectionnez **1.8**.

   ![](media/sdk/qs-java-android-09-dependency-added.png)

1. Sélectionnez **OK** pour fermer la fenêtre de structure du projet et appliquer vos modifications au projet.

## <a name="create-user-interface"></a>Créer une interface utilisateur

Nous allons créer une interface utilisateur de base pour l’application. Modifiez la disposition de votre activité principale, `activity_main.xml`. Initialement, la disposition comprend une barre de titre avec le nom de votre application, et un TextView contenant le texte « Hello World! ».

* Cliquez sur l’élément TextView. Remplacez son attribut ID dans le coin supérieur droit par `hello`.

* Dans la Palette, en haut à gauche de la fenêtre `activity_main.xml`, faites glisser un bouton dans l’espace vide au-dessus du texte.

* Dans les attributs du bouton à droite, entrez `onSpeechButtonClicked` comme valeur de l’attribut `onClick`. Nous allons écrire une méthode portant ce nom pour gérer l’événement de bouton.  Remplacez son attribut ID dans le coin supérieur droit par `button`.

* Utilisez l’icône de baguette magique en haut du concepteur pour calculer les contraintes de disposition.

  ![Capture d’écran de l’icône de baguette magique](media/sdk/qs-java-android-10-infer-layout-constraints.png)

Le texte et la représentation graphique de votre interface utilisateur doivent maintenant ressembler à ce qui suit :

![](media/sdk/qs-java-android-11-gui.png)

[!code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/java-android/app/src/main/res/layout/activity_main.xml)]

## <a name="add-sample-code"></a>Ajouter un exemple de code

1. Ouvrez le fichier source `MainActivity.java`. Remplacez tout le code de ce fichier par les lignes de code suivantes.

   [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java-android/app/src/main/java/com/microsoft/cognitiveservices/speech/samples/quickstart/MainActivity.java#code)]

   * La méthode `onCreate` inclut du code qui demande une autorisation d’accès au microphone et à Internet, et établit la liaison de la plateforme native. La configuration des liaisons à la plateforme native n’est à effectuer qu’une seule fois. Elle doit être réalisée au début de l’initialisation de l’application.

   * La méthode `onSpeechButtonClicked` est, comme indiqué précédemment, le gestionnaire de clic du bouton. Un appui sur le bouton déclenche la transcription de la reconnaissance vocale.

1. Dans le même fichier, remplacez la chaîne `YourSubscriptionKey` par votre clé d’abonnement.

1. De même, remplacez la chaîne `YourServiceRegion` par la [région](regions.md) associée à votre abonnement (par exemple, `westus` pour l’abonnement à un essai gratuit).

## <a name="build-and-run-the-app"></a>Générer et exécuter l’application

1. Connectez votre appareil Android à votre PC de développement. Vérifiez que vous avez activé le [mode de développement et le débogage USB](https://developer.android.com/studio/debug/dev-options) sur l’appareil.

1. Pour générer l’application, appuyez sur les touches Ctrl+F9 ou, dans la barre de menus, choisissez **Build (Générer)** > **Make Project (Créer le projet)**.

1. Pour lancer l’application, appuyez sur les touches Maj+F10, ou choisissez **Run (Exécuter)** > **Run 'app' (Exécutez 'app')**.

1. Dans la fenêtre de la cible de déploiement qui s’affiche, choisissez votre appareil Android.

   ![Capture d’écran de la fenêtre de sélection de la cible de déploiement](media/sdk/qs-java-android-12-deploy.png)

Appuyez sur le bouton dans l’application pour commencer une section de reconnaissance vocale. Les 15 secondes suivantes de paroles prononcées en anglais sont envoyées au service Speech et transcrites. Le résultat s’affiche dans l’application Android ainsi que dans la fenêtre logcat d’Android Studio.

![Capture d’écran de l’application Android](media/sdk/qs-java-android-13-gui-on-device.png)

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Explorer des exemples Java sur GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Voir aussi

- [Personnaliser les modèles acoustiques](how-to-customize-acoustic-models.md)
- [Personnaliser les modèles de langage](how-to-customize-language-model.md)
