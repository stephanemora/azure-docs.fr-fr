---
title: 'Démarrage rapide : Reconnaissance vocale, C++ (Linux) - Services Speech'
titleSuffix: Azure Cognitive Services
description: Découvrez comment effectuer la synthèse vocale en C++ sur Linux avec le SDK Speech
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 05/02/2019
ms.author: yinhew
ms.openlocfilehash: 211394186fe8fc0fd1514a5ce1dbc1e0efc10b7e
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65020591"
---
# <a name="quickstart-synthesize-speech-in-c-on-linux-by-using-the-speech-sdk"></a>Démarrage rapide : Synthèse vocale en C++ sur Linux avec le SDK Speech

Dans cet article, vous créez une application console C++ pour Linux (Ubuntu 16.04, Ubuntu 18.04, Debian 9). Vous utilisez le [SDK Speech](speech-sdk.md) de Cognitive Services pour faire la synthèse vocale à partir d’un texte en temps réel et pour lire les paroles sur les haut-parleurs de votre PC. L’application est générée avec le [kit SDK Speech pour Linux](https://aka.ms/csspeech/linuxbinary) et le compilateur C++ de votre distribution Linux (par exemple, `g++`).

## <a name="prerequisites"></a>Prérequis

Vous avez besoin d’une clé d’abonnement au service Speech pour suivre ce guide de démarrage rapide. Vous pouvez en obtenir une gratuitement. Pour plus d’informations, voir [Essayer le service Speech gratuitement](get-started.md).

## <a name="install-speech-sdk"></a>Installer le kit SDK Speech

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

La version actuelle du kit SDK de reconnaissance vocale Cognitive Services est `1.5.0`.

Le kit SDK Speech pour Linux peut être utilisé pour créer des applications 32 bits et 64 bits. Les bibliothèques et fichiers d’en-tête nécessaires sont téléchargeables sous la forme d’un fichier tar à partir de https://aka.ms/csspeech/linuxbinary.

Téléchargez et installez le Kit de développement logiciel (SDK) comme suit :

1. Vérifiez que les dépendances du kit SDK sont installées.

   * Sur Ubuntu :

     ```sh
     sudo apt-get update
     sudo apt-get install build-essential libssl1.0.0 libasound2 wget
     ```

   * Sur Debian 9 :

     ```sh
     sudo apt-get update
     sudo apt-get install build-essential libssl1.0.2 libasound2 wget
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

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/cpp-linux/helloworld.cpp#code)]

1. Dans ce nouveau fichier, remplacez la chaîne `YourSubscriptionKey` par votre clé d’abonnement aux services Voix.

1. Remplacez la chaîne `YourServiceRegion` par la [région](regions.md) associée à votre abonnement (par exemple, `westus` pour l’abonnement à un essai gratuit).

## <a name="build-the-app"></a>Générer l’application

> [!NOTE]
> Veillez à entrer les commandes ci-dessous en tant que _ligne de commande unique_. Le moyen le plus simple pour ce faire consiste à copier la commande à l’aide du bouton **Copier** situé en regard de chaque commande, et de la coller dans votre invite de l’interpréteur de commandes.

* Sur un système **x64** (64 bits), exécutez la commande suivante pour générer l’application.

  ```sh
  g++ helloworld.cpp -o helloworld -I "$SPEECHSDK_ROOT/include/cxx_api" -I "$SPEECHSDK_ROOT/include/c_api" --std=c++14 -lpthread -lMicrosoft.CognitiveServices.Speech.core -L "$SPEECHSDK_ROOT/lib/x64" -l:libasound.so.2
  ```

* Sur un système **x86** (32 bits), exécutez la commande suivante pour générer l’application.

  ```sh
  g++ helloworld.cpp -o helloworld -I "$SPEECHSDK_ROOT/include/cxx_api" -I "$SPEECHSDK_ROOT/include/c_api" --std=c++14 -lpthread -lMicrosoft.CognitiveServices.Speech.core -L "$SPEECHSDK_ROOT/lib/x86" -l:libasound.so.2
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

1. Dans la fenêtre de la console, une invite s’affiche, vous demandant de taper du texte. Tapez quelques mots ou une phrase. Le texte que vous avez tapé est transmis aux services Speech et est synthétisé en paroles, qui sont lues sur votre haut-parleur.

   ```text
   Type some text that you want to speak...
   > hello
   Speech synthesized to speaker for text [hello]
   Press enter to exit...
   ```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Explorer des exemples C++ sur GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Voir aussi

- [Personnaliser les polices de la voix](how-to-customize-voice-font.md)
- [Enregistrer des échantillons vocaux](record-custom-voice-samples.md)
