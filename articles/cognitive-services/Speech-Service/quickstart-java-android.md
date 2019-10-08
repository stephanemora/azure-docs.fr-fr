---
title: 'Démarrage rapide : Reconnaissance vocale, Java (Android) - Service Speech'
titleSuffix: Azure Cognitive Services
description: Découvrir la reconnaissance vocale en Java sur Android à l’aide du SDK Speech
services: cognitive-services
author: fmegen
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: wolfma
ms.openlocfilehash: 2f728231c01056ecb8709f84f13e834ef3618dc8
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/02/2019
ms.locfileid: "71803311"
---
# <a name="quickstart-recognize-speech-in-java-on-android-by-using-the-speech-sdk"></a>Démarrage rapide : Reconnaissance vocale en Java sur Android à l’aide du kit SDK de reconnaissance vocale

Des guides de démarrage rapide sont également disponibles pour la [synthèse vocale](quickstart-text-to-speech-java-android.md) et l’[assistant virtuel « voice-first »](quickstart-virtual-assistant-java-android.md).

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Dans cet article, vous allez apprendre à créer une application Java pour Android à l’aide du SDK Cognitive Services Speech pour transcrire la parole en texte.
L’application est basée sur le package Maven du SDK Speech et sur Android Studio 3.3.
Le kit SDK Speech est actuellement compatible avec les appareils Android équipés de processeurs ARM 32/64 bits et Intel x86/x64 compatibles.

> [!NOTE]
> Pour le kit SDK Speech et l’appareil Roobo, consultez le [kit SDK Speech Devices](speech-devices-sdk.md).

## <a name="prerequisites"></a>Prérequis

Vous avez besoin d’une clé d’abonnement au service Speech pour suivre ce guide de démarrage rapide. Vous pouvez en obtenir une gratuitement. Pour plus d’informations, consultez [Essayer le service Speech gratuitement](get-started.md).

## <a name="create-and-configure-a-project"></a>Créer et configurer un projet

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-java-android-create-proj.md)]

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

1. Pour générer l’application, appuyez sur les touches Ctrl+F9 ou, dans la barre de menus, choisissez **Build (Générer)**  > **Make Project (Créer le projet)** .

1. Pour lancer l’application, appuyez sur les touches Maj+F10, ou choisissez **Run (Exécuter)**  > **Run 'app' (Exécutez 'app')** .

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
