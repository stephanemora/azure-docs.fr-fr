---
title: 'Démarrage rapide : Reconnaissance vocale, Objective-C - Services de reconnaissance vocale'
titleSuffix: Azure Cognitive Services
description: Découvrir la reconnaissance vocale en Objective-C sur macOS avec le SDK Speech
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 04/03/2019
ms.author: chlandsi
ms.openlocfilehash: 67f2531b24796de1e00505fdc757f3c2244c5054
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/22/2019
ms.locfileid: "66002347"
---
# <a name="quickstart-recognize-speech-in-objective-c-on-macos-using-the-speech-sdk"></a>Démarrage rapide : Reconnaissance vocale en Objective-C sur macOS avec le SDK Speech

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Dans cet article, vous allez découvrir comment créer une application macOS en Objective-C avec le SDK Speech de Cognitive Services pour transcrire en texte un fichier audio enregistré à partir d’un microphone.

## <a name="prerequisites"></a>Prérequis

Avant de commencer, voici une liste de prérequis :

* [Clé d’abonnement](get-started.md) pour le service de reconnaissance vocale
* Une machine macOS avec [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) ou ultérieur, et macOS 10.13 ou ultérieur

## <a name="get-the-speech-sdk-for-macos"></a>Obtenir le SDK Speech pour macOS

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

La version actuelle du kit SDK de reconnaissance vocale Cognitive Services est `1.5.1`.

Le SDK Speech de Cognitive Services pour Mac est actuellement distribué sous la forme d’un bundle de framework.
Il peut être utilisé dans les projets Xcode comme [CocoaPod](https://cocoapods.org/), ou téléchargé depuis https://aka.ms/csspeech/macosbinary et lié manuellement. Ce guide utilise un CocoaPod.

## <a name="create-an-xcode-project"></a>Créer un projet Xcode

Démarrez Xcode et démarrez un nouveau projet en cliquant sur **File** > **New** > **Project** (Fichier->Nouveau->Projet).
Dans la boîte de dialogue de sélection du modèle, choisissez le modèle « Application Cocoa ».

Dans les boîtes de dialogue qui suivent, effectuez les sélections suivantes :

1. Boîte de dialogue Project Options
    1. Entrez un nom pour l’application de démarrage rapide, par exemple `helloworld`.
    1. Si vous avez déjà un compte de développeur Apple, entrez un nom d’organisation et un identificateur d’organisation appropriés. À des fins de test, vous pouvez sélectionner n’importe quel nom, par exemple `testorg`. Pour signer l’application, vous avez besoin d’un profil de provisionnement. Pour plus d’informations, consultez le [site des développeurs Apple](https://developer.apple.com/).
    1. Vérifiez qu’Objective-C est choisi comme langage pour le projet.
    1. Décochez les cases pour l’utilisation de storyboards et pour la création d’une application basée sur un document. L’interface utilisateur simple pour l’exemple d’application est créée par programmation.
    1. Décochez toutes les cases pour les tests et les données de base.
    ![Paramètres du projet](media/sdk/qs-objectivec-macos-project-settings.png)
1. Sélectionner le répertoire du projet
    1. Choisissez un répertoire où placer le projet. Cela crée un répertoire `helloworld` dans votre répertoire de base, où sont stockés tous les fichiers du projet Xcode.
    1. Désactivez la création d’un dépôt Git pour cet exemple de projet.
1. Définissez les droits d’accès au réseau et au microphone. Cliquez sur le nom de l’application dans la première ligne de la vue d’ensemble sur la gauche pour accéder à la configuration de l’application, puis choisissez l’onglet « Fonctionnalités ».
    1. Activez le paramètre « App Sandbox » pour l’application.
    1. Cochez les cases pour l’accès « Connexions sortantes » et « Microphone ».
    ![Paramètres du bac à sable (sandbox)](media/sdk/qs-objectivec-macos-sandbox.png)
1. L’application doit également déclarer l’utilisation du microphone dans le fichier `Info.plist`. Cliquez sur le fichier dans la vue d’ensemble, puis ajoutez la clé « Confidentialité - Description de l’utilisation du microphone » avec une valeur comme « Microphone is needed for speech recognition » (Le microphone est nécessaire pour la reconnaissance vocale).
    ![Paramètres dans Info.plist](media/sdk/qs-objectivec-macos-info-plist.png)
1. Fermez le projet Xcode. Vous allez utiliser une autre instance de celui-ci ultérieurement, après avoir configuré les CocoaPods.

## <a name="install-the-sdk-as-a-cocoapod"></a>Installer le SDK en tant que CocoaPod

1. Installez le gestionnaire de dépendances de CocoaPod comme décrit dans ses [instructions d’installation](https://guides.cocoapods.org/using/getting-started.html).
1. Accédez au répertoire de votre exemple d’application (`helloworld`). Placez un fichier texte avec le nom `Podfile` et le contenu suivant dans ce répertoire :
    ```
    target 'helloworld' do
        platform :osx, '10.13'
        pod 'MicrosoftCognitiveServicesSpeech-macOS', '~> 1.5.1'
    end
    ```
1. Accédez au répertoire `helloworld` dans un terminal et exécutez la commande `pod install`. Ceci génère espace de travail Xcode `helloworld.xcworkspace` contenant l’exemple d’application et le SDK Speech en tant que dépendance. Cet espace de travail sera utilisé par la suite.

## <a name="add-the-sample-code"></a>Ajouter l’exemple de code

1. Ouvrez l’espace de travail `helloworld.xcworkspace` dans Xcode.
1. Remplacez le contenu du fichier `AppDelegate.m` généré automatiquement par :  
   [!code-objectivec[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec-macos/helloworld/helloworld/AppDelegate.m#code)]
1. Remplacez la chaîne `YourSubscriptionKey` par votre clé d’abonnement.
1. Remplacez la chaîne `YourServiceRegion` par la [région](regions.md) associée à votre abonnement (par exemple, `westus` pour l’abonnement à un essai gratuit).

## <a name="build-and-run-the-sample"></a>Créer et exécuter l’exemple.

1. Affichez la sortie de débogage (**View** > **Debug Area** > **Activate Console**).
1. Générez et exécutez l’exemple de code en sélectionnant **Product** -> **Run** dans le menu, ou en cliquant sur le bouton **Play**.
1. Après avoir cliqué sur le bouton et prononcé quelques mots, vous devez normalement voir le texte énoncé sur la partie inférieure de l’écran. Quand vous exécutez l’application pour la première fois, vous devez normalement être invité à autoriser l’application à accéder au microphone de votre ordinateur.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Explorer des exemples Objective-C sur GitHub](https://aka.ms/csspeech/samples)

