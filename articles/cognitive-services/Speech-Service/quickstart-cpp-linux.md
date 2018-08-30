---
title: 'Démarrage rapide : reconnaissance vocale dans C++ sur Linux à l’aide du Kit de développement logiciel Speech (SDK) de Cognitive Services'
titleSuffix: Microsoft Cognitive Services
description: Découvrez comment effectuer une reconnaissance vocale dans C++ sur Linux à l’aide du kit de développement logiciel (SDK) Cognitive Services Speech
services: cognitive-services
author: wolfma61
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 07/16/2018
ms.author: wolfma
ms.openlocfilehash: 92bd5980ac2e6befbe352df6ddf8644f04d37d34
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/28/2018
ms.locfileid: "43126863"
---
# <a name="quickstart-recognize-speech-in-c-on-linux-using-the-speech-sdk"></a>Démarrage rapide : reconnaissance vocale dans C++ sur Linux à l’aide du kit de développement logiciel (SDK) Speech

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Dans cet article, vous allez apprendre à créer une application console C++ sur Linux (Ubuntu 16.04) à l’aide du Kit de développement logiciel (SDK) Cognitive Services Speech pour transcrire de la parole en texte.

## <a name="prerequisites"></a>Prérequis

* Clé d’abonnement pour le service Speech. Consultez l’article [Try the speech service for free](get-started.md) (Essayer le service Speech gratuitement).
* Un PC Ubuntu 16.04 avec un microphone opérationnel.
* Pour installer les packages nécessaires pour générer et exécuter cet exemple, exécutez la commande suivante :

  ```sh
  sudo apt-get update
  sudo apt-get install build-essential libssl1.0.0 libcurl3 libasound2 wget
  ```

## <a name="get-the-speech-sdk"></a>Obtenir le Kit de développement logiciel (SDK) de reconnaissance vocale

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

La version actuelle du kit SDK de reconnaissance vocale Cognitive Services est `0.6.0`.

Le Kit de développement logiciel (SDK) de reconnaissance vocale Cognitive Services pour Linux est disponible pour générer des applications 64 bits et 32 bits.
Les fichiers requis peuvent être téléchargés en tant que fichier tar à partir de https://aka.ms/csspeech/linuxbinary.
Téléchargez et installez le Kit de développement logiciel (SDK) comme suit :

1. Choisissez un répertoire (chemin d’accès absolu) où vous voulez placer les fichiers binaires et les en-têtes du Kit de développement logiciel (SDK) de reconnaissance vocale.
   Par exemple, choisissez le chemin d’accès `speechsdk` sous votre répertoire de base :

   ```sh
   export SPEECHSDK_ROOT="$HOME/speechsdk"
   ```

1. S’il n’existe pas encore, créez le répertoire :

   ```sh
   mkdir -p "$SPEECHSDK_ROOT"
   ```

1. Téléchargez et extrayez l’archive `.tar.gz` avec les fichiers binaires du Kit de développement logiciel (SDK) de reconnaissance vocale :

   ```sh
   wget -O SpeechSDK-Linux.tar.gz https://aka.ms/csspeech/linuxbinary
   tar --strip 1 -xzf SpeechSDK-Linux.tar.gz -C "$SPEECHSDK_ROOT"
   ```

1. Validez le contenu du répertoire de niveau supérieur du package extrait :

   ```sh
   ls -l "$SPEECHSDK_ROOT"
   ```

   Les fichiers de licence et des avis tiers doivent s’afficher, ainsi qu’un répertoire `include` pour les en-têtes et un répertoire `lib` pour les bibliothèques.

   [!INCLUDE [Linux Binary Archive Content](../../../includes/cognitive-services-speech-service-linuxbinary-content.md)]

## <a name="add-the-sample-code"></a>Ajouter l’exemple de code

1. Ajoutez le code suivant dans un fichier nommé `helloworld.cpp` :

  [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp-linux/helloworld.cpp#code)]

1. Remplacez la chaîne `YourSubscriptionKey` par votre clé d’abonnement.

1. Remplacez la chaîne `YourServiceRegion` par la [région](regions.md) associée à votre abonnement (par exemple, `westus` pour l’abonnement à un essai gratuit).

## <a name="building"></a>Génération

> [!NOTE]
> N’oubliez pas de copier et de coller les commandes de génération ci-dessous dans _une seule ligne_.

* Sur un ordinateur **x64**, exécutez la commande suivante pour générer l’application :

  ```sh
  g++ helloworld.cpp -o helloworld -I "$SPEECHSDK_ROOT/include/cxx_api" -I "$SPEECHSDK_ROOT/include/c_api" --std=c++14 -lpthread -lMicrosoft.CognitiveServices.Speech.core -L "$SPEECHSDK_ROOT/lib/x64" -l:libssl.so.1.0.0 -l:libcurl.so.4 -l:libasound.so.2
  ```

* Sur un ordinateur **x86**, exécutez la commande suivante pour générer l’application :

  ```sh
  g++ helloworld.cpp -o helloworld -I "$SPEECHSDK_ROOT/include/cxx_api" -I "$SPEECHSDK_ROOT/include/c_api" --std=c++14 -lpthread -lMicrosoft.CognitiveServices.Speech.core -L "$SPEECHSDK_ROOT/lib/x86" -l:libssl.so.1.0.0 -l:libcurl.so.4 -l:libasound.so.2
  ```

## <a name="run-the-sample"></a>Exécution de l'exemple

1. Configurez le chemin d’accès de la bibliothèque du chargeur pour pointer vers la bibliothèque du Kit de développement logiciel (SDK) de reconnaissance vocale.

   * Sur un ordinateur **x64**, exécutez :

     ```sh
     export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:$SPEECHSDK_ROOT/lib/x64"
     ```

   * Sur un ordinateur **x86**, exécutez :

     ```sh
     export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:$SPEECHSDK_ROOT/lib/x86"
     ```

1. Exécutez l’application comme suit :

   ```sh
   ./helloworld
   ```

1. La sortie doit ressembler à celle-ci :

   ```text
   Say something...
   We recognized: What's the weather
   ```

[!INCLUDE [Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Recherchez cet exemple dans le dossier `quickstart/cpp-linux`.

## <a name="next-steps"></a>Étapes suivantes

* [Découvrez nos exemples](speech-sdk.md#get-the-samples)
