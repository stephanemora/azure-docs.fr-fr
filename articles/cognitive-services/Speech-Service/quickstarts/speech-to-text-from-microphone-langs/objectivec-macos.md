---
title: 'Démarrage rapide : Reconnaître la voix à partir d’un microphone, Objective-C – Service Speech'
titleSuffix: Azure Cognitive Services
description: Découvrir la reconnaissance vocale en Objective-C sur macOS avec le SDK Speech
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 12/23/2019
ms.author: chlandsi
ms.openlocfilehash: c2f0fbe66b26c6eca6e0c0b2530efacba9bae958
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75380590"
---
# <a name="quickstart-recognize-speech-in-objective-c-on-macos-by-using-the-speech-sdk"></a>Démarrage rapide : Reconnaissance vocale en Objective-C sur macOS avec le SDK Speech

Des guides de démarrage rapide sont également disponibles pour la [synthèse vocale](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech-langs/objectivec-macos.md).

Dans cet article, vous allez découvrir comment créer une application macOS en Objective-C avec le SDK Speech d’Azure Cognitive Services pour transcrire en texte un fichier audio enregistré à partir d’un microphone.

## <a name="prerequisites"></a>Prérequis

Avant de commencer, vous avez besoin des éléments suivants :

* Une [clé d’abonnement](~/articles/cognitive-services/Speech-Service/get-started.md) pour le service Speech.
* Ordinateur macOS avec [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) ou ultérieur et macOS 10.13 ou ultérieur

## <a name="get-the-speech-sdk-for-macos"></a>Obtenir le SDK Speech pour macOS

[!INCLUDE [License notice](~/includes/cognitive-services-speech-service-license-notice.md)]

Le SDK Speech de Cognitive Services pour Mac est actuellement distribué sous la forme d’un bundle de framework. Il peut être utilisé dans les projets Xcode comme [CocoaPod](https://cocoapods.org/) ou téléchargé à l’adresse https://aka.ms/csspeech/macosbinary et lié manuellement. Cet article se base sur un CocoaPod.

## <a name="create-an-xcode-project"></a>Créer un projet Xcode

Démarrez Xcode et démarrez un nouveau projet en sélectionnant **File** > **New** > **Project**. Dans la boîte de dialogue de sélection du modèle, sélectionnez le modèle **Application Cocoa**.

Dans les boîtes de dialogue qui suivent, effectuez les sélections ci-après.

1. Dans la boîte de dialogue **Project Options** :
    1. Entrez un nom pour l’application de démarrage rapide, par exemple *helloworld*.
    1. Si vous avez déjà un compte de développeur Apple, entrez un nom d’organisation et un identificateur d’organisation appropriés. À des fins de test, utilisez un nom comme *testorg*. Pour signer l’application, vous avez besoin d’un profil de provisionnement. Pour plus d’informations, consultez le [site des développeurs Apple](https://developer.apple.com/).
    1. Vérifiez qu’**Objective-C** est sélectionné comme langage pour le projet.
    1. Décochez les cases pour l’utilisation de storyboards et pour la création d’une application basée sur un document. L’interface utilisateur simple pour l’exemple d’application est créée par programmation.
    1. Décochez toutes les cases pour les tests et les données principales.

    ![Paramètres du projet](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-macos-project-settings.png)

1. Sélectionnez un répertoire de projet :
    1. Choisissez un répertoire où placer le projet. Cette étape permet de créer un répertoire helloworld dans le répertoire de base pour stocker tous les fichiers du projet Xcode.
    1. Désactivez la création d’un dépôt Git pour cet exemple de projet.
1. Définissez les droits d’accès au réseau et au microphone. Sélectionnez le nom de l’application dans la première ligne de la vue d’ensemble sur la gauche pour accéder à la configuration de l’application. Sélectionnez ensuite l’onglet **Capabilities**.
    1. Activez le paramètre **App Sandbox** pour l’application.
    1. Cochez les cases **Outgoing Connections** et **Microphone** pour l’accès au microphone.

    ![Paramètres du bac à sable](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-macos-sandbox.png)

1. L’application doit également déclarer l’utilisation du microphone dans le fichier `Info.plist`. Sélectionnez le fichier dans la vue d’ensemble, puis ajoutez la clé **Confidentialité - Description de l’utilisation du microphone** avec une valeur comme *Microphone is needed for speech recognition* (Le microphone est nécessaire pour la reconnaissance vocale).

    ![Paramètres dans Info.plist](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-macos-info-plist.png)

1. Fermez le projet Xcode. Vous utiliserez une autre instance de celui-ci ultérieurement, après avoir configuré les CocoaPods.

## <a name="install-the-sdk-as-a-cocoapod"></a>Installer le SDK en tant que CocoaPod

1. Installez le gestionnaire de dépendances de CocoaPod comme décrit dans ses [instructions d’installation](https://guides.cocoapods.org/using/getting-started.html).
1. Accédez au répertoire de votre exemple d’application (helloworld). Placez un fichier texte avec le nom *Podfile* et le contenu suivant dans ce répertoire :

   [!code-ruby[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec/macos/from-microphone/helloworld/Podfile)]
1. Accédez au répertoire helloworld dans un terminal et exécutez la commande `pod install`. Cette commande génère un espace de travail Xcode `helloworld.xcworkspace` contenant l’exemple d’application et le SDK Speech comme dépendance. Cet espace de travail sera utilisé pour les étapes suivantes.

## <a name="add-the-sample-code"></a>Ajouter l’exemple de code

1. Ouvrez l’espace de travail `helloworld.xcworkspace` dans Xcode.
1. Remplacez le contenu du fichier `AppDelegate.m` généré automatiquement par le code suivant :

   [!code-objectivec[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec/macos/from-microphone/helloworld/helloworld/AppDelegate.m#code)]
1. Remplacez la chaîne `YourSubscriptionKey` par votre clé d’abonnement.
1. Remplacez la chaîne `YourServiceRegion` par la [région](~/articles/cognitive-services/Speech-Service/regions.md) associée à votre abonnement. Par exemple, utilisez `westus` pour l’abonnement d’essai gratuit.

## <a name="build-and-run-the-sample"></a>Créer et exécuter l’exemple.

1. Affichez la sortie de débogage en sélectionnant **View** > **Debug Area** > **Activate Console**.
1. Générez et exécutez l’exemple de code en sélectionnant **Product** > **Run** dans le menu. Vous pouvez également sélectionner **Play** .
1. Après avoir sélectionné le bouton et prononcé quelques mots, vous devez normalement voir le texte énoncé sur la partie inférieure de l’écran. Quand vous exécutez l’application pour la première fois, vous devez normalement être invité à autoriser l’application à accéder au microphone de votre ordinateur.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Explorer des exemples Objective-C sur GitHub](https://aka.ms/csspeech/samples)
