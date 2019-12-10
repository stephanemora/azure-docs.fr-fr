---
title: 'Démarrage rapide : Reconnaître la voix à partir d’un microphone, Swift – Service Speech'
titleSuffix: Azure Cognitive Services
description: Découvrir la reconnaissance vocale en Swift sur iOS avec le SDK Speech
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: chlandsi
ms.openlocfilehash: 53ddf18c0ea102968db03bc4435b7615e724892a
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2019
ms.locfileid: "74818508"
---
# <a name="quickstart-recognize-speech-in-swift-on-ios-by-using-the-speech-sdk"></a>Démarrage rapide : Reconnaissance vocale en Swift sur iOS avec le SDK Speech

Des guides de démarrage rapide sont également disponibles pour la [synthèse vocale](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech-langs/swift-ios.md).

Dans cet article, vous allez découvrir comment créer une application iOS en Swift avec le SDK Speech d’Azure Cognitive Services pour transcrire en texte un fichier audio enregistré à partir d’un microphone.

## <a name="prerequisites"></a>Prérequis

Avant de commencer, vous avez besoin des éléments suivants :

* Une [clé d’abonnement](~/articles/cognitive-services/Speech-Service/get-started.md) pour le service Speech.
* Une machine macOS avec [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) ou ultérieur, et [CocoaPods](https://cocoapods.org/) installé.

## <a name="get-the-speech-sdk-for-ios"></a>Obtenir le SDK Speech pour iOS

[!INCLUDE [License notice](~/includes/cognitive-services-speech-service-license-notice.md)]

Ce tutoriel ne fonctionne pas avec des versions du SDK antérieures à la version 1.6.0.

Le SDK Speech de Cognitive Services pour iOS est actuellement distribué sous la forme d’un bundle de framework. Il peut être utilisé dans les projets Xcode comme [CocoaPod](https://cocoapods.org/) ou téléchargé à l’adresse https://aka.ms/csspeech/iosbinary et lié manuellement. Cet article se base sur un CocoaPod.

## <a name="create-an-xcode-project"></a>Créer un projet Xcode

Démarrez Xcode et démarrez un nouveau projet en sélectionnant **File** > **New** > **Project**.
Dans la boîte de dialogue de sélection de modèle, sélectionnez le modèle **iOS Single View App**.

Dans les boîtes de dialogue qui suivent, effectuez les sélections ci-après.

1. Dans la boîte de dialogue **Project Options** :
    1. Entrez un nom pour l’application de démarrage rapide, par exemple *helloworld*.
    1. Si vous avez déjà un compte de développeur Apple, entrez un nom d’organisation et un identificateur d’organisation appropriés. À des fins de test, utilisez un nom comme *testorg*. Pour signer l’application, vous avez besoin d’un profil de provisionnement. Pour plus d’informations, consultez le [site des développeurs Apple](https://developer.apple.com/).
    1. Vérifiez que **Swift** est choisi comme langage pour le projet.
    1. Décochez les cases pour l’utilisation de storyboards et pour la création d’une application basée sur un document. L’interface utilisateur simple pour l’exemple d’application est créée par programmation.
    1. Décochez toutes les cases pour les tests et les données principales.
1. Sélectionnez un répertoire de projet :
    1. Choisissez un répertoire où placer le projet. Cette étape permet de créer un répertoire helloworld dans le répertoire choisi pour stocker tous les fichiers du projet Xcode.
    1. Désactivez la création d’un dépôt Git pour cet exemple de projet.
1. L’application doit également déclarer l’utilisation du microphone dans le fichier `Info.plist`. Sélectionnez le fichier dans la vue d’ensemble, puis ajoutez la clé **Confidentialité - Description de l’utilisation du microphone** avec une valeur comme *Microphone is needed for speech recognition* (Le microphone est nécessaire pour la reconnaissance vocale).

    ![Paramètres dans Info.plist](~/articles/cognitive-services/Speech-Service/media/sdk/qs-swift-ios-info-plist.png)

1. Fermez le projet Xcode. Vous utiliserez une autre instance de celui-ci ultérieurement, après avoir configuré les CocoaPods.

## <a name="add-the-sample-code"></a>Ajouter l’exemple de code

1. Placez un nouveau fichier d’en-tête avec le nom `MicrosoftCognitiveServicesSpeech-Bridging-Header.h` dans le répertoire helloworld du projet helloworld. Collez-y le code suivant :

   [!code-objectivec[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/swift/ios/from-microphone/helloworld/helloworld/MicrosoftCognitiveServicesSpeech-Bridging-Header.h#code)]

1. Ajoutez le chemin relatif `helloworld/MicrosoftCognitiveServicesSpeech-Bridging-Header.h` à l’en-tête de pontage dans les paramètres de projet Swift pour la cible helloworld dans le champ **Objective-C Bridging Header**.

   ![Propriétés d’en-tête](~/articles/cognitive-services/Speech-Service/media/sdk/qs-swift-ios-bridging-header.png)

1. Remplacez le contenu du fichier `AppDelegate.swift` généré automatiquement par le code suivant :

   [!code-swift[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/swift/ios/from-microphone/helloworld/helloworld/AppDelegate.swift#code)]
1. Remplacez le contenu du fichier `ViewController.swift` généré automatiquement par le code suivant :

   [!code-swift[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/swift/ios/from-microphone/helloworld/helloworld/ViewController.swift#code)]
1. Dans `ViewController.swift`, remplacez la chaîne `YourSubscriptionKey` par votre clé d’abonnement.
1. Remplacez la chaîne `YourServiceRegion` par la [région](~/articles/cognitive-services/Speech-Service/regions.md) associée à votre abonnement. Par exemple, utilisez `westus` pour l’abonnement d’essai gratuit.

## <a name="install-the-sdk-as-a-cocoapod"></a>Installer le SDK en tant que CocoaPod

1. Installez le gestionnaire de dépendances de CocoaPod comme décrit dans ses [instructions d’installation](https://guides.cocoapods.org/using/getting-started.html).
1. Accédez au répertoire de votre exemple d’application (helloworld). Placez un fichier texte avec le nom *Podfile* et le contenu suivant dans ce répertoire :

   [!code-ruby[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/swift/ios/from-microphone/helloworld/Podfile)]
1. Accédez au répertoire helloworld dans un terminal et exécutez la commande `pod install`. Cette commande génère un espace de travail Xcode `helloworld.xcworkspace` contenant l’exemple d’application et le SDK Speech comme dépendance. Cet espace de travail sera utilisé pour les étapes suivantes.

## <a name="build-and-run-the-sample"></a>Créer et exécuter l’exemple.

1. Ouvrez l’espace de travail `helloworld.xcworkspace` dans Xcode.
1. Affichez la sortie de débogage en sélectionnant **View** > **Debug Area** > **Activate Console**.
1. Choisissez le simulateur iOS ou un appareil iOS connecté à votre machine de développement comme destination pour l’application à partir de la liste présentée dans le menu **Produit** > **Destination**.
1. Générez et exécutez l’exemple de code dans le simulateur iOS en sélectionnant **Product** > **Run** dans le menu. Vous pouvez également sélectionner le bouton **Play**.
1. Après avoir sélectionné le bouton **Recognize** dans l’application et prononcé quelques mots, vous devez voir le texte énoncé sur la partie inférieure de l’écran.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Explorer nos exemples sur GitHub](https://aka.ms/csspeech/samples)
