---
title: 'Démarrage rapide : Reconnaître la voix à partir d’un microphone, Objective-C – Service Speech'
titleSuffix: Azure Cognitive Services
description: Découvrir la reconnaissance vocale en Objective-C sur iOS avec le SDK Speech
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 06/25/2020
ms.author: chlandsi
ms.openlocfilehash: c355071c4bbaff5ddb22b2db5fc4a3a0fdb561d4
ms.sourcegitcommit: b56226271541e1393a4b85d23c07fd495a4f644d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85391551"
---
# <a name="quickstart-recognize-speech-in-objective-c-on-ios-by-using-the-speech-sdk"></a>Démarrage rapide : Reconnaissance vocale en Objective-C sur iOS avec le SDK Speech

Des guides de démarrage rapide sont également disponibles pour la [synthèse vocale](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech-langs/objectivec-ios.md).

Dans cet article, vous allez découvrir comment créer une application iOS en Objective-C avec le SDK Speech d’Azure Cognitive Services pour transcrire de la voix en texte à partir du microphone ou d’un fichier avec de l’audio enregistré.

## <a name="prerequisites"></a>Prérequis

Avant de commencer, vous avez besoin des éléments suivants :

* Une [clé d’abonnement](~/articles/cognitive-services/Speech-Service/get-started.md) pour le service Speech.
* Ordinateur macOS avec [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) ou version ultérieure
* Cible définie sur iOS version 9.3 ou ultérieure

## <a name="get-the-speech-sdk-for-ios"></a>Obtenir le SDK Speech pour iOS

[!INCLUDE [License notice](~/includes/cognitive-services-speech-service-license-notice.md)]

Le SDK Speech de Cognitive Services pour iOS est actuellement distribué sous forme de framework Cocoa.
Vous pouvez le télécharger à partir de [ce site web](https://aka.ms/csspeech/iosbinary). Téléchargez le fichier dans votre répertoire de base.

## <a name="create-an-xcode-project"></a>Créer un projet Xcode

Démarrez Xcode et démarrez un nouveau projet en sélectionnant **File** > **New** > **Project**.
Dans la boîte de dialogue de sélection de modèle, sélectionnez le modèle **iOS Single View App**.

Dans les boîtes de dialogue qui suivent, effectuez les sélections ci-après.

1. Dans la boîte de dialogue **Project Options** :
    1. Entrez un nom pour l’application de démarrage rapide, par exemple *helloworld*.
    1. Entrez un nom d’organisation et un identificateur d’organisation appropriés, si vous avez déjà un compte de développeur Apple. À des fins de test, utilisez un nom comme *testorg*. Pour signer l’application, vous avez besoin d’un profil de provisionnement. Pour plus d’informations, consultez le [site des développeurs Apple](https://developer.apple.com/).
    1. Vérifiez qu’**Objective-C** est sélectionné comme langage pour le projet.
    1. Décochez toutes les cases pour les tests et les données principales.

    ![Paramètres du projet](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-project-settings.png)

1. Sélectionnez un répertoire de projet :
   1. Choisissez votre répertoire de base pour y placer le projet. Cette étape permet de créer un répertoire helloworld dans le répertoire de base pour stocker tous les fichiers du projet Xcode.
   1. Désactivez la création d’un dépôt Git pour cet exemple de projet.
   1. Définissez les chemins du SDK dans l’écran des paramètres du projet.
      1. Dans l’onglet **General**, sous l’en-tête **Embedded Binaries** (Binaires incorporés), ajoutez la bibliothèque du SDK en tant que framework en sélectionnant **Add embedded binaries (Ajouter des binaires incorporés)**  > **Add other**. Accédez au répertoire de base et sélectionnez le fichier `MicrosoftCognitiveServicesSpeech.framework`. Ceci ajoute automatiquement la bibliothèque du SDK à l’en-tête **Linked Framework and Libraries** (Bibliothèques et framework liés).
         ![Framework ajouté](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-framework.png)
      1. Accédez à l’onglet **Build Settings** (Paramètres de build) et sélectionnez le paramètre **All**.
      1. Ajoutez le répertoire $(SRCROOT)/.. aux chemins de recherche de framework (**Framework Search Paths**) sous l’en-tête **Search Paths** (Chemins de recherche).

      ![Paramétrage des chemins de recherche de framework](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-framework-search-paths.png)

## <a name="set-up-the-ui"></a>Configurer l’interface utilisateur

L’exemple d’application repose sur une interface utilisateur très simple. Deux boutons permettent de démarrer la reconnaissance vocale à partir du fichier ou à partir de l’entrée du microphone, et une étiquette de texte présente le résultat. L’interface utilisateur est définie dans la partie `Main.storyboard` du projet. Ouvrez la vue XML du storyboard en cliquant avec le bouton droit sur l’entrée `Main.storyboard` de l’arborescence de projet et en sélectionnant **Open As** > **Source Code**.

Remplacez le code XML généré automatiquement par ce code :

[!code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/objectivec/ios/from-microphone/helloworld/helloworld/Base.lproj/Main.storyboard)]

## <a name="add-the-sample-code"></a>Ajouter l’exemple de code

1. Téléchargez l’[exemple de fichier wav](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-speech-sdk/f9807b1079f3a85f07cbb6d762c6b5449d536027/samples/cpp/windows/console/samples/whatstheweatherlike.wav) en cliquant avec le bouton droit sur le lien et en sélectionnant **Save target as**.
   Ajoutez le fichier wav au projet en tant que ressource en le faisant glisser à partir d’une fenêtre Finder vers le niveau racine de la vue du projet.
   Sélectionnez **Finish** dans la boîte de dialogue suivante sans modifier les paramètres.
1. Remplacez le contenu du fichier `ViewController.m` généré automatiquement par le code suivant :

   [!code-objectivec[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec/ios/from-microphone/helloworld/helloworld/ViewController.m#code)]
1. Remplacez la chaîne `YourSubscriptionKey` par votre clé d’abonnement.
1. Remplacez la chaîne `YourServiceRegion` par la [région](~/articles/cognitive-services/Speech-Service/regions.md) associée à votre abonnement. Par exemple, utilisez `westus` pour l’abonnement d’essai gratuit.
1. Ajoutez la requête pour l’accès au microphone. Cliquez avec le bouton droit sur l’entrée `Info.plist` de l’arborescence du projet, puis sélectionnez **Open As** > **Source Code**. Ajoutez les lignes suivantes dans la section `<dict>`, puis enregistrez le fichier.

    ```xml
    <key>NSMicrophoneUsageDescription</key>
    <string>Need microphone access for speech recognition from microphone.</string>
    ```

## <a name="build-and-run-the-sample"></a>Créer et exécuter l’exemple.

1. Affichez la sortie de débogage en sélectionnant **View** > **Debug Area** > **Activate Console**.
1. Choisissez le simulateur iOS ou un appareil iOS connecté à votre machine de développement comme destination pour l’application à partir de la liste présentée dans le menu **Produit** > **Destination**.
1. Générez et exécutez l’exemple de code dans le simulateur iOS en sélectionnant **Product** > **Run** dans le menu. Vous pouvez également sélectionner le bouton **Play**.
1. Après avoir sélectionné le bouton **Recognize (Fichier)** dans l’application, vous devez voir le contenu du fichier audio « What’s the weather like? ». dans la partie inférieure de l’écran.

   ![Application iOS simulée](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-simulated-app.png)

1. Après avoir sélectionné le bouton **Recognize (Microphone)** dans l’application et prononcé quelques mots, vous devez voir le texte énoncé sur la partie inférieure de l’écran.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Explorer des exemples Objective-C sur GitHub](https://aka.ms/csspeech/samples)
