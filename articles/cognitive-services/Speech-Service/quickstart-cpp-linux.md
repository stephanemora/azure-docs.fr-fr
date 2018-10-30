---
title: 'Démarrage rapide : Reconnaissance vocale en C++ sur Linux à l’aide du kit SDK Speech Service'
titleSuffix: Azure Cognitive Services
description: Découvrez comment exécuter la reconnaissance vocale en C++ sur Linux à l’aide du kit SDK Speech Servcie
services: cognitive-services
author: wolfma61
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: quickstart
ms.date: 10/12/2018
ms.author: wolfma
ms.openlocfilehash: c637659c58ba518b8d1efca11bfa5303f1e1dde0
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/19/2018
ms.locfileid: "49469110"
---
# <a name="quickstart-recognize-speech-in-c-on-linux-by-using-the-speech-sdk"></a>Démarrage rapide : Reconnaissance vocale en C++ sur Linux à l’aide du kit SDK Speech

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Dans cet article, vous créez une application console C++ pour Ubuntu Linux 16.04. Vous utilisez le [kit SDK Speech](speech-sdk.md) de Cognitive Services pour transcrire la reconnaissance vocale en temps réel à partir du microphone de votre PC. L’application est générée avec le [kit SDK Speech pour Linux](https://aka.ms/csspeech/linuxbinary) et le compilateur C++ de votre distribution Linux (par exemple, `g++`).

## <a name="prerequisites"></a>Prérequis

Vous avez besoin d’une clé d’abonnement au service Speech pour suivre ce guide de démarrage rapide. Vous pouvez en obtenir une gratuitement. Consultez [Essayer le service Speech gratuitement](get-started.md) pour plus d’informations.

## <a name="install-speech-sdk"></a>Installer le kit SDK Speech

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

La version actuelle du kit SDK de reconnaissance vocale Cognitive Services est `1.0.1`.

Le kit SDK Speech pour Linux peut être utilisé pour créer des applications 32 bits et 64 bits. Les bibliothèques et fichiers d’en-tête nécessaires sont téléchargeables sous la forme d’un fichier .tar à partir de https://aka.ms/csspeech/linuxbinary.

Téléchargez et installez le Kit de développement logiciel (SDK) comme suit :

1. Vérifiez que les dépendances du kit SDK sont installées.

   ```sh
   sudo apt-get update
   sudo apt-get install build-essential libssl1.0.0 libcurl3 libasound2 wget
   ```

1. Choisissez un répertoire dans lequel extraire les fichiers du kit SDK Speech, et définissez la variable d’environnement `SPEECHSDK_ROOT` pour qu’elle pointe vers ce répertoire. Cette variable facilite ensuite la référence au répertoire dans les futures commandes. Par exemple, si vous souhaitez utiliser le répertoire `speechsdk` dans votre répertoire de base, utilisez une commande semblable à la suivante :

   ```sh
   export SPEECHSDK_ROOT="$HOME/speechsdk"
   ```

1. S’il n’existe pas encore, créez ce répertoire.

   ```sh
   mkdir -p "$SPEECHSDK_ROOT"
   ```

1. Téléchargez et extrayez l’archive `.tar.gz` contenant les fichiers binaires du kit SDK Speech :

   ```sh
   wget -O SpeechSDK-Linux.tar.gz https://aka.ms/csspeech/linuxbinary
   tar --strip 1 -xzf SpeechSDK-Linux.tar.gz -C "$SPEECHSDK_ROOT"
   ```

1. Validez le contenu du répertoire de niveau supérieur du package extrait :

   ```sh
   ls -l "$SPEECHSDK_ROOT"
   ```

   La liste du répertoire doit contenir les fichiers de licence et des avis tiers, ainsi qu’un répertoire `include` contenant des fichiers d’en-tête (`.h`) et un répertoire `lib` regroupant les bibliothèques.

   [!INCLUDE [Linux Binary Archive Content](../../../includes/cognitive-services-speech-service-linuxbinary-content.md)]

## <a name="add-sample-code"></a>Ajouter un exemple de code

1. Créez un fichier source C++ nommé `helloworld.cpp`, et collez le code suivant dedans.

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp-linux/helloworld.cpp#code)]

1. Dans ce nouveau fichier, remplacez la chaîne `YourSubscriptionKey` par votre clé d’abonnement au service Speech.

1. Remplacez la chaîne `YourServiceRegion` par la [région](regions.md) associée à votre abonnement (par exemple, `westus` pour l’abonnement à un essai gratuit).

## <a name="build-the-app"></a>Générer l’application

> [!NOTE]
> Veillez à entrer les commandes ci-dessous en tant que _ligne de commande unique_. Le moyen le plus simple pour ce faire consiste à copier la commande à l’aide du bouton **Copier** situé en regard de chaque commande, et de la coller dans votre invite de l’interpréteur de commandes.

* Sur un système **x64** (64 bits), exécutez la commande suivante pour générer l’application.

  ```sh
  g++ helloworld.cpp -o helloworld -I "$SPEECHSDK_ROOT/include/cxx_api" -I "$SPEECHSDK_ROOT/include/c_api" --std=c++14 -lpthread -lMicrosoft.CognitiveServices.Speech.core -L "$SPEECHSDK_ROOT/lib/x64" -l:libssl.so.1.0.0 -l:libcurl.so.4 -l:libasound.so.2
  ```

* Sur un système **x86** (32 bits), exécutez la commande suivante pour générer l’application.

  ```sh
  g++ helloworld.cpp -o helloworld -I "$SPEECHSDK_ROOT/include/cxx_api" -I "$SPEECHSDK_ROOT/include/c_api" --std=c++14 -lpthread -lMicrosoft.CognitiveServices.Speech.core -L "$SPEECHSDK_ROOT/lib/x86" -l:libssl.so.1.0.0 -l:libcurl.so.4 -l:libasound.so.2
  ```

## <a name="run-the-app"></a>Exécution de l'application

1. Configurez le chemin de la bibliothèque du chargeur pour qu’il pointe vers la bibliothèque du kit SDK Speech.

   * Sur un système **x64** (64 bits), entrez la commande suivante.

     ```sh
     export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:$SPEECHSDK_ROOT/lib/x64"
     ```

   * Sur un système **x86** (32 bits), entrez cette commande.

     ```sh
     export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:$SPEECHSDK_ROOT/lib/x86"
     ```

1. Exécutez l'application.

   ```sh
   ./helloworld
   ```

1.  Dans la fenêtre de la console, une invite s’affiche, demandant que vous disiez quelque chose. Prononcez une phrase ou quelques mots en anglais. Votre production orale est transmise au service Speech, et transcrite en texte qui apparaît dans la même fenêtre.

   ```text
   Say something...
   We recognized: What's the weather like?
   ```

[!INCLUDE [Download this sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Recherchez cet exemple dans le dossier `quickstart/cpp-linux`.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Effectuer une reconnaissance des intentions vocales à l’aide du kit SDK Speech pour C++](how-to-recognize-intents-from-speech-cpp.md)

## <a name="see-also"></a>Voir aussi

- [Traduction vocale](how-to-translate-speech-csharp.md)
- [Personnaliser les modèles acoustiques](how-to-customize-acoustic-models.md)
- [Personnaliser les modèles de langage](how-to-customize-language-model.md)
