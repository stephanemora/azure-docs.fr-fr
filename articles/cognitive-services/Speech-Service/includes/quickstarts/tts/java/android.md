---
title: 'Démarrage rapide : Synthétiser la voix, Java (Android) – Service Speech'
titleSuffix: Azure Cognitive Services
description: Découvrir la synthèse vocale en Java sur Android à l’aide du SDK Speech
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 09/19/2019
ms.author: yulili
ms.openlocfilehash: e38b1bdd3258675dfac9a155f7cee338a1f8f806
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2019
ms.locfileid: "74818144"
---
## <a name="prerequisites"></a>Prérequis

Avant de commencer, assurez-vous de :

> [!div class="checklist"]
> * [Créer une ressource Azure Speech](../../../../get-started.md)
> * [Configurer votre environnement de développement](../../../../quickstarts/setup-platform.md?tabs=android)
> * [Créer un échantillon de projet vide](../../../../quickstarts/create-project.md?tabs=android)

## <a name="create-user-interface"></a>Créer une interface utilisateur

Nous allons créer une interface utilisateur de base pour l’application. Modifiez la disposition de votre activité principale, `activity_main.xml`. Initialement, la disposition comprend une barre de titre avec le nom de votre application, et un TextView contenant le texte « Hello World! ».

1. Cliquez sur l’élément TextView. Remplacez son attribut ID dans le coin supérieur droit par `outputMessage`, puis faites-le glisser vers l’écran inférieur. Supprimez son texte.

1. Dans la Palette, en haut à gauche de la fenêtre `activity_main.xml`, faites glisser un bouton dans l’espace vide au-dessus du texte.

1. Dans les attributs du bouton à droite, entrez `onSpeechButtonClicked` comme valeur de l’attribut `onClick`. Nous allons écrire une méthode portant ce nom pour gérer l’événement de bouton.  Remplacez son attribut ID dans le coin supérieur droit par `button`.

1. Faites glisser un texte brut dans l’espace situé au-dessus du bouton ; remplacez son attribut ID par `speakText`, puis remplacez l’attribut text par `Hi there!`.

1. Utilisez l’icône de baguette magique en haut du concepteur pour calculer les contraintes de disposition.


    ![Capture d’écran de l’icône de baguette magique](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-10-infer-layout-constraints.png)

Le texte et la représentation graphique de votre interface utilisateur doivent maintenant ressembler à ce qui suit :

![](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-11-2-tts-gui.png)

[!code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/java/android/text-to-speech/app/src/main/res/layout/activity_main.xml)]

## <a name="add-sample-code"></a>Ajouter un exemple de code

1. Ouvrez le fichier source `MainActivity.java`. Remplacez tout le code de ce fichier par les lignes de code suivantes.

   [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/android/text-to-speech/app/src/main/java/com/microsoft/cognitiveservices/speech/samples/quickstart/MainActivity.java#code)]

   * La méthode `onSpeechButtonClicked` est, comme indiqué précédemment, le gestionnaire de clic du bouton. Une pression sur un bouton déclenche la synthèse vocale.

1. Dans le même fichier, remplacez la chaîne `YourSubscriptionKey` par votre clé d’abonnement.

1. De même, remplacez la chaîne `YourServiceRegion` par la [région](~/articles/cognitive-services/Speech-Service/regions.md) associée à votre abonnement (par exemple, `westus` pour l’abonnement à un essai gratuit).

## <a name="build-and-run-the-app"></a>Générer et exécuter l’application

1. Connectez votre appareil Android à votre PC de développement. Vérifiez que vous avez activé le [mode de développement et le débogage USB](https://developer.android.com/studio/debug/dev-options) sur l’appareil. Vous pouvez également créer un [émulateur Android](https://developer.android.com/studio/run/emulator).

1. Pour générer l’application, appuyez sur les touches Ctrl+F9 ou, dans la barre de menus, choisissez **Build (Générer)**  > **Make Project (Créer le projet)** .

1. Pour lancer l’application, appuyez sur les touches Maj+F10, ou choisissez **Run (Exécuter)**  > **Run 'app' (Exécutez 'app')** .

1. Dans la fenêtre de la cible de déploiement qui s’affiche, choisissez votre appareil ou émulateur Android.

   ![Capture d’écran de la fenêtre de sélection de la cible de déploiement](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-12-deploy.png)

Entrez un texte et appuyez sur le bouton dans l’application pour commencer une section de synthèse vocale. Vous entendrez l’audio synthétisé à partir du haut-parleur par défaut et verrez les informations `speech synthesis succeeded` à l’écran.

![Capture d’écran de l’application Android](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-13-2-gui-on-device-tts.png)

## <a name="next-steps"></a>Étapes suivantes

[!INCLUDE [footer](./footer.md)]

## <a name="see-also"></a>Voir aussi

- [Créer une voix personnalisée Custom Voice](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Enregistrer des échantillons vocaux personnalisés](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
