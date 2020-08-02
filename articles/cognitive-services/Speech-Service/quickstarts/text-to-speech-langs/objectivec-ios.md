---
title: 'Démarrage rapide : synthèse vocale en Objective-C à l’aide du Kit de développement logiciel (SDK) Speech'
titleSuffix: Azure Cognitive Services
description: Découvrir la synthèse vocale en Objective-C sur iOS avec le SDK Speech
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 06/25/2020
ms.author: yulili
ms.openlocfilehash: fa1b62a987cb6c158c8d7f1fceb57bc47e5800eb
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87084838"
---
# <a name="quickstart-synthesize-speech-in-objective-c-on-ios-using-the-speech-sdk"></a>Démarrage rapide : Synthèse vocale en Objective-C sur iOS à l’aide du SDK Speech

Dans cet article, vous allez découvrir comment créer une application iOS en Objective-C avec le SDK Speech de Cognitive Services pour synthétiser de la voix à partir de texte.

## <a name="prerequisites"></a>Prérequis

Avant de commencer, voici une liste de prérequis :

* Une [clé d’abonnement](~/articles/cognitive-services/Speech-Service/get-started.md) pour le service Speech
* Ordinateur macOS avec [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) ou version ultérieure
* Cible définie sur iOS version 9.3 ou ultérieure

## <a name="get-the-speech-sdk-for-ios"></a>Obtenir le SDK Speech pour iOS

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

Notez que ce tutoriel ne fonctionne pas avec une version du SDK antérieure à la version 1.7.0.

Le SDK Speech de Cognitive Services pour iOS est actuellement distribué sous forme de framework Cocoa.
Il peut être utilisé dans les projets Xcode comme [CocoaPod](https://cocoapods.org/), ou téléchargé depuis https://aka.ms/csspeech/iosbinary et lié manuellement. Ce guide utilise un CocoaPod.

## <a name="create-an-xcode-project"></a>Créer un projet Xcode

Démarrez Xcode et démarrez un nouveau projet en cliquant sur **File** > **New** > **Project** (Fichier->Nouveau->Projet).
Dans la boîte de dialogue de sélection de modèle, choisissez le modèle « iOS Single View App ».

Dans les boîtes de dialogue qui suivent, effectuez les sélections suivantes :

1. Boîte de dialogue Project Options
    1. Entrez un nom pour l’application de démarrage rapide, par exemple `helloworld`.
    1. Entrez un nom d’organisation et un identificateur d’organisation appropriés, si vous avez déjà un compte de développeur Apple. À des fins de test, vous pouvez sélectionner n’importe quel nom, par exemple `testorg`. Pour signer l’application, vous avez besoin d’un profil de provisionnement. Pour plus d’informations, consultez le [site des développeurs Apple](https://developer.apple.com/).
    1. Vérifiez qu’Objective-C est choisi comme langage pour le projet.
    1. Décochez toutes les cases pour les tests et les données de base.
    ![Paramètres du projet](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-project-settings.png)
1. Sélectionner le répertoire du projet
    1. Choisissez votre répertoire de base pour y placer le projet. Cela crée un répertoire `helloworld` dans votre répertoire de base, où sont stockés tous les fichiers du projet Xcode.
    1. Désactivez la création d’un dépôt Git pour cet exemple de projet.

## <a name="install-the-sdk-as-a-cocoapod"></a>Installer le SDK en tant que CocoaPod

1. Installez le gestionnaire de dépendances de CocoaPod comme décrit dans ses [instructions d’installation](https://guides.cocoapods.org/using/getting-started.html).
1. Accédez au répertoire de votre exemple d’application (`helloworld`). Placez un fichier texte avec le nom `Podfile` et le contenu suivant dans ce répertoire :  
   [!code-ruby[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec/ios/text-to-speech/helloworld/Podfile)]
1. Accédez au répertoire `helloworld` dans un terminal et exécutez la commande `pod install`. Ceci génère espace de travail Xcode `helloworld.xcworkspace` contenant l’exemple d’application et le SDK Speech en tant que dépendance. Cet espace de travail sera utilisé par la suite.

## <a name="add-the-sample-code"></a>Ajouter l’exemple de code

1. Ouvrez l’espace de travail `helloworld.xcworkspace` dans Xcode.
1. Remplacez le contenu du fichier `AppDelegate.m` généré automatiquement par :  
   [!code-objectivec[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec/ios/text-to-speech/helloworld/helloworld/AppDelegate.m#code)]
1. Remplacez le contenu du fichier `ViewController.m` généré automatiquement par :  
   [!code-objectivec[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec/ios/text-to-speech/helloworld/helloworld/ViewController.m#code)]
1. Remplacez la chaîne `YourSubscriptionKey` par votre clé d’abonnement.
1. Remplacez la chaîne `YourServiceRegion` par la [région](~/articles/cognitive-services/Speech-Service/regions.md) associée à votre abonnement (par exemple, `westus` pour l’abonnement à un essai gratuit).

## <a name="build-and-run-the-sample"></a>Créer et exécuter l’exemple.

1. Affichez la sortie de débogage (**View** > **Debug Area** > **Activate Console**).
1. Choisissez le simulateur iOS ou un appareil iOS connecté à votre machine de développement comme destination pour l’application à partir de la liste présentée dans le menu **Produit** > **Destination**.
1. Générez et exécutez l’exemple de code dans le simulateur iOS en sélectionnant **Product** > **Run** dans le menu ou en cliquant sur le bouton **Play**.

   ![Application iOS simulée](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-simulated-app-tts.png)

1. Après avoir entré du texte et cliqué sur le bouton dans l’application, vous devez entendre la lecture de la sortie audio de synthèse.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Explorer des exemples Objective-C sur GitHub](https://aka.ms/csspeech/samples)

