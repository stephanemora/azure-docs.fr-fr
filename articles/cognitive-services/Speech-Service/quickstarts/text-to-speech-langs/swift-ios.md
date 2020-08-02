---
title: 'Démarrage rapide : Synthèse vocale en Swift sur iOS - Service Speech'
titleSuffix: Azure Cognitive Services
description: Découvrir la synthèse vocale en Swift sur iOS avec le SDK Speech
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 06/25/2020
ms.author: yulili
ms.openlocfilehash: e71717bdacbc3c6eb08fbdc8d56ec19c26a1d114
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87062279"
---
# <a name="quickstart-synthesize-speech-in-swift-on-ios-using-the-speech-sdk"></a>Démarrage rapide : Synthèse vocale en Swift sur iOS avec le SDK Speech

Dans cet article, vous allez découvrir comment créer une application iOS en Swift avec le SDK Speech de Cognitive Services pour synthétiser de la voix à partir de texte.

## <a name="prerequisites"></a>Prérequis

Avant de commencer, voici une liste de prérequis :

* Une [clé d’abonnement](~/articles/cognitive-services/Speech-Service/get-started.md) pour le service Speech.
* Une machine macOS avec [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) ou ultérieur, et [CocoaPods](https://cocoapods.org/) installé.

## <a name="get-the-speech-sdk-for-ios"></a>Obtenir le SDK Speech pour iOS

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

Notez que ce tutoriel ne fonctionne pas avec une version du SDK antérieure à la version 1.7.0.

Le SDK Speech de Cognitive Services pour iOS est actuellement distribué sous la forme d’un bundle de framework.
Il peut être utilisé dans les projets Xcode comme [CocoaPod](https://cocoapods.org/), ou téléchargé depuis https://aka.ms/csspeech/macosbinary et lié manuellement. Ce guide utilise un CocoaPod.

## <a name="create-an-xcode-project"></a>Créer un projet Xcode

Démarrez Xcode et démarrez un nouveau projet en cliquant sur **File** > **New** > **Project** (Fichier->Nouveau->Projet).
Dans la boîte de dialogue de sélection de modèle, choisissez le modèle « iOS Single View App ».

Dans les boîtes de dialogue qui suivent, effectuez les sélections suivantes :

1. Boîte de dialogue Project Options
    1. Entrez un nom pour l’application de démarrage rapide, par exemple `helloworld`.
    1. Si vous avez déjà un compte de développeur Apple, entrez un nom d’organisation et un identificateur d’organisation appropriés. À des fins de test, vous pouvez sélectionner n’importe quel nom, par exemple `testorg`. Pour signer l’application, vous avez besoin d’un profil de provisionnement. Pour plus d’informations, consultez le [site des développeurs Apple](https://developer.apple.com/).
    1. Vérifiez que Swift est choisi comme langage pour le projet.
    1. Décochez les cases pour l’utilisation de storyboards et pour la création d’une application basée sur un document. L’interface utilisateur simple pour l’exemple d’application est créée par programmation.
    1. Décochez toutes les cases pour les tests et les données de base.
1. Sélectionner le répertoire du projet
    1. Choisissez un répertoire où placer le projet. Cela crée un répertoire `helloworld` dans le répertoire choisi pour stocker tous les fichiers du projet Xcode.
    1. Désactivez la création d’un dépôt Git pour cet exemple de projet.
1. Fermez le projet Xcode. Vous allez utiliser une autre instance de celui-ci ultérieurement, après avoir configuré les CocoaPods.

## <a name="add-the-sample-code"></a>Ajouter l’exemple de code

1. Placez un nouveau fichier d’en-tête avec le nom `MicrosoftCognitiveServicesSpeech-Bridging-Header.h` dans le répertoire `helloworld` dans le projet helloworld, puis collez le code suivant dedans :  
   [!code-objectivec[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/swift/ios/text-to-speech/helloworld/helloworld/MicrosoftCognitiveServicesSpeech-Bridging-Header.h#code)]
1. Ajoutez le chemin d’accès relatif `helloworld/MicrosoftCognitiveServicesSpeech-Bridging-Header.h` à l’en-tête de pontage dans les paramètres de projet Swift pour la cible helloworld dans le champ *En-tête de pontage Objective-C*![Propriétés d’en-tête](~/articles/cognitive-services/Speech-Service/media/sdk/qs-swift-ios-bridging-header.png)
1. Remplacez le contenu du fichier `AppDelegate.swift` généré automatiquement par :  
   [!code-swift[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/swift/ios/text-to-speech/helloworld/helloworld/AppDelegate.swift#code)]
1. Remplacez le contenu du fichier `ViewController.swift` généré automatiquement par :  
   [!code-swift[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/swift/ios/text-to-speech/helloworld/helloworld/ViewController.swift#code)]
1. Dans `ViewController.swift`, remplacez la chaîne `YourSubscriptionKey` par votre clé d’abonnement.
1. Remplacez la chaîne `YourServiceRegion` par la [région](~/articles/cognitive-services/Speech-Service/regions.md) associée à votre abonnement (par exemple, `westus` pour l’abonnement à un essai gratuit).

## <a name="install-the-sdk-as-a-cocoapod"></a>Installer le SDK en tant que CocoaPod

1. Installez le gestionnaire de dépendances de CocoaPod comme décrit dans ses [instructions d’installation](https://guides.cocoapods.org/using/getting-started.html).
1. Accédez au répertoire de votre exemple d’application (`helloworld`). Placez un fichier texte avec le nom `Podfile` et le contenu suivant dans ce répertoire :  
   [!code-ruby[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/swift/ios/text-to-speech/helloworld/Podfile)]
1. Accédez au répertoire `helloworld` dans un terminal et exécutez la commande `pod install`. Ceci génère espace de travail Xcode `helloworld.xcworkspace` contenant l’exemple d’application et le SDK Speech en tant que dépendance. Cet espace de travail sera utilisé par la suite.

## <a name="build-and-run-the-sample"></a>Créer et exécuter l’exemple.

1. Ouvrez l’espace de travail `helloworld.xcworkspace` dans Xcode.
1. Affichez la sortie de débogage (**View** > **Debug Area** > **Activate Console**).
1. Choisissez le simulateur iOS ou un appareil iOS connecté à votre machine de développement comme destination pour l’application à partir de la liste présentée dans le menu **Produit** > **Destination**.
1. Générez et exécutez l’exemple de code dans le simulateur iOS en sélectionnant **Product** > **Run** dans le menu ou en cliquant sur le bouton **Play**.
1. Après avoir entré du texte et cliqué sur le bouton dans l’application, vous devez entendre la lecture de la sortie audio de synthèse.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Explorer nos exemples sur GitHub](https://aka.ms/csspeech/samples)
