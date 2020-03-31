---
title: 'Démarrage rapide : Synthétiser la voix, Objective-C – Service Speech'
titleSuffix: Azure Cognitive Services
description: Découvrir la synthèse vocale en Objective-C sur macOS avec le SDK Speech
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 12/09/2019
ms.author: yulili
ms.openlocfilehash: 37eed03ed839411f1acf5d963d4118a3c6d2c379
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74975922"
---
# <a name="quickstart-synthesize-speech-in-objective-c-on-macos-using-the-speech-sdk"></a>Démarrage rapide : Synthèse vocale en Objective-C sur macOS avec le SDK Speech

Dans cet article, vous allez découvrir comment créer une application macOS en Objective-C avec le SDK Speech de Cognitive Services pour synthétiser de la voix à partir de texte et la lire avec la sortie audio par défaut.

## <a name="prerequisites"></a>Prérequis

Avant de commencer, voici une liste de prérequis :

* Une [clé d’abonnement](~/articles/cognitive-services/Speech-Service/get-started.md) pour le service Speech
* Une machine macOS avec [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) ou ultérieur, et macOS 10.13 ou ultérieur

## <a name="get-the-speech-sdk-for-macos"></a>Obtenir le SDK Speech pour macOS

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

Notez que ce tutoriel ne fonctionne pas avec une version du SDK antérieure à la version 1.7.0.

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
    ![Paramètres du projet](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-macos-project-settings.png)
1. Sélectionner le répertoire du projet
    1. Choisissez un répertoire où placer le projet. Cela crée un répertoire `helloworld` dans votre répertoire de base, où sont stockés tous les fichiers du projet Xcode.
    1. Désactivez la création d’un dépôt Git pour cet exemple de projet.
1. Définissez les droits d’accès au réseau. Cliquez sur le nom de l’application dans la première ligne de la vue d’ensemble sur la gauche pour accéder à la configuration de l’application, puis choisissez l’onglet « Fonctionnalités ».
    1. Activez le paramètre « App Sandbox » pour l’application.
    1. Cochez la case pour l’accès « Connexions sortantes ».
    ![Paramètres du bac à sable (sandbox)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-macos-sandbox-tts.png)
1. Fermez le projet Xcode. Vous allez utiliser une autre instance de celui-ci ultérieurement, après avoir configuré les CocoaPods.

## <a name="install-the-sdk-as-a-cocoapod"></a>Installer le SDK en tant que CocoaPod

1. Installez le gestionnaire de dépendances de CocoaPod comme décrit dans ses [instructions d’installation](https://guides.cocoapods.org/using/getting-started.html).
1. Accédez au répertoire de votre exemple d’application (`helloworld`). Placez un fichier texte avec le nom `Podfile` et le contenu suivant dans ce répertoire :  
   [!code-ruby[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec/macos/text-to-speech/helloworld/Podfile)]
1. Accédez au répertoire `helloworld` dans un terminal et exécutez la commande `pod install`. Ceci génère espace de travail Xcode `helloworld.xcworkspace` contenant l’exemple d’application et le SDK Speech en tant que dépendance. Cet espace de travail sera utilisé par la suite.

## <a name="add-the-sample-code"></a>Ajouter l’exemple de code

1. Ouvrez l’espace de travail `helloworld.xcworkspace` dans Xcode.
1. Remplacez le contenu du fichier `AppDelegate.m` généré automatiquement par :  
   [!code-objectivec[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec/macos/text-to-speech/helloworld/helloworld/AppDelegate.m#code)]
1. Remplacez la chaîne `YourSubscriptionKey` par votre clé d’abonnement.
1. Remplacez la chaîne `YourServiceRegion` par la [région](~/articles/cognitive-services/Speech-Service/regions.md) associée à votre abonnement (par exemple, `westus` pour l’abonnement à un essai gratuit).

## <a name="build-and-run-the-sample"></a>Créer et exécuter l’exemple.

1. Affichez la sortie de débogage (**View** > **Debug Area** > **Activate Console**).
1. Générez et exécutez l’exemple de code en sélectionnant **Product** -> **Run** dans le menu, ou en cliquant sur le bouton **Play**.
1. Après avoir entré du texte et cliqué sur le bouton dans l’application, vous devez entendre la lecture de la sortie audio de synthèse.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Explorer des exemples Objective-C sur GitHub](https://aka.ms/csspeech/samples)

