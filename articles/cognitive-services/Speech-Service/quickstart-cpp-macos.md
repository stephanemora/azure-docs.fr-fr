---
title: 'Démarrage rapide : Reconnaissance vocale, C++ (macOS) - Services Speech'
titleSuffix: Azure Cognitive Services
description: Découvrir la reconnaissance vocale en C++ sur macOS avec le SDK Speech
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 04/03/2019
ms.author: wolfma
ms.openlocfilehash: b602a26c83be1ffd4bd55a8eff9ff8d83aac5919
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65020810"
---
# <a name="quickstart-recognize-speech-in-c-on-macos-by-using-the-speech-sdk"></a>Démarrage rapide : Reconnaissance vocale en C++ sur macOS avec le SDK Speech

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Dans cet article, vous créez une application console C++ pour macOS 10.13 et ultérieur. Vous utilisez le [SDK Speech](speech-sdk.md) de Cognitive Services pour transcrire la reconnaissance vocale en temps réel à partir du microphone de votre Mac. L’application est générée avec le [SDK Speech pour macOS](https://aka.ms/csspeech/macosbinary) et le compilateur C++ par défaut de votre Mac (par exemple `g++`).

## <a name="prerequisites"></a>Prérequis

Vous avez besoin d’une clé d’abonnement au service Speech pour suivre ce guide de démarrage rapide. Vous pouvez en obtenir une gratuitement. Pour plus d’informations, voir [Essayer le service Speech gratuitement](get-started.md).

## <a name="install-speech-sdk"></a>Installer le kit SDK Speech

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

La version actuelle du kit SDK de reconnaissance vocale Cognitive Services est `1.5.0`.

Le SDK Speech pour macOS peut être téléchargé sous la forme d’un bundle de framework compressé à partir de https://aka.ms/csspeech/macosbinary.

Téléchargez et installez le Kit de développement logiciel (SDK) comme suit :

1. Choisissez un répertoire dans lequel extraire les fichiers du kit SDK Speech, et définissez la variable d’environnement `SPEECHSDK_ROOT` pour qu’elle pointe vers ce répertoire. Cette variable facilite ensuite la référence au répertoire dans les futures commandes. Par exemple, si vous souhaitez utiliser le répertoire `speechsdk` dans votre répertoire de base, utilisez une commande semblable à la suivante :

   ```sh
   export SPEECHSDK_ROOT="$HOME/speechsdk"
   ```

1. S’il n’existe pas encore, créez ce répertoire.

   ```sh
   mkdir -p "$SPEECHSDK_ROOT"
   ```

1. Téléchargez et extrayez l’archive `.zip` contenant le framework SDK Speech :

   ```sh
   wget -O SpeechSDK-macOS.zip https://aka.ms/csspeech/macosbinary
   unzip SpeechSDK-macOS.zip -d "$SPEECHSDK_ROOT"
   ```

1. Validez le contenu du répertoire de niveau supérieur du package extrait :

   ```sh
   ls -l "$SPEECHSDK_ROOT"
   ```

   La liste des répertoires doit contenir les fichiers de notification et de licence de tiers, ainsi qu’un répertoire `MicrosoftCognitiveServicesSpeech.framework`.

## <a name="add-sample-code"></a>Ajouter un exemple de code

1. Créez un fichier source C++ nommé `helloworld.cpp`, et collez le code suivant dedans.

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp-macos/helloworld.cpp#code)]

1. Dans ce nouveau fichier, remplacez la chaîne `YourSubscriptionKey` par votre clé d’abonnement aux services Voix.

1. Remplacez la chaîne `YourServiceRegion` par la [région](regions.md) associée à votre abonnement (par exemple, `westus` pour l’abonnement à un essai gratuit).

## <a name="build-the-app"></a>Générer l’application

> [!NOTE]
> Veillez à entrer les commandes ci-dessous en tant que _ligne de commande unique_. Le moyen le plus simple pour ce faire consiste à copier la commande à l’aide du bouton **Copier** situé en regard de chaque commande, et de la coller dans votre invite de l’interpréteur de commandes.

* Exécutez la commande suivante pour générer l’application.

  ```sh
  g++ helloworld.cpp -o helloworld --std=c++11 -F${SPEECHSDK_ROOT} -framework MicrosoftCognitiveServicesSpeech
  ```

## <a name="run-the-app"></a>Exécution de l'application

1. Configurez le chemin de la bibliothèque du chargeur pour qu’il pointe vers la bibliothèque du kit SDK Speech.

    ```sh
    export DYLD_FRAMEWORK_PATH="$DYLD_FRAMEWORK_PATH:$SPEECHSDK_ROOT"
    ```

1. Exécutez l'application.

   ```sh
   ./helloworld
   ```

1. Dans la fenêtre de la console, une invite s’affiche, demandant que vous disiez quelque chose. Prononcez une phrase ou quelques mots en anglais. Votre production orale est transmise au service Speech, et transcrite en texte qui apparaît dans la même fenêtre.

   ```text
   Say something...
   We recognized: What's the weather like?
   ```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Explorer des exemples C++ sur GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Voir aussi

- [Personnaliser les modèles acoustiques](how-to-customize-acoustic-models.md)
- [Personnaliser les modèles de langage](how-to-customize-language-model.md)

