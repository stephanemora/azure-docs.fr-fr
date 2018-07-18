---
title: Guide de démarrage rapide pour le Kit de développement logiciel (SDK) de reconnaissance vocale pour C++ et Linux | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: Obtenez des informations et des exemples de code pour vous aider à prendre en main rapidement le Kit de développement logiciel (SDK) de reconnaissance vocale avec Linux et C++ dans Cognitive Services.
services: cognitive-services
author: wolfma61
manager: onano
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 06/07/2018
ms.author: wolfma
ms.openlocfilehash: cee70ba585f93dda3249fc5b39f25fb613b57a45
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/25/2018
ms.locfileid: "36753583"
---
# <a name="quickstart-for-c-and-linux"></a>Démarrage rapide pour C++ et Linux

La version actuelle du Kit de développement logiciel (SDK) de reconnaissance vocale Cognitive Services est `0.4.0`.

Le Kit de développement logiciel (SDK) de reconnaissance vocale Cognitive Services pour Linux est disponible pour générer des applications 64 bits et 32 bits. Les fichiers requis peuvent être téléchargés en tant que fichier tar à partir de https://aka.ms/csspeech/linuxbinary.

> [!NOTE]
> Si vous recherchez un Démarrage rapide pour C++ et Windows, accédez [ici](quickstart-cpp-windows.md).
> Si vous recherchez un Démarrage rapide pour C# et Windows, accédez [ici](quickstart-csharp-windows.md).

[!include[Get a Subscription Key](includes/get-subscription-key.md)]

> [!NOTE]
> Ces instructions supposent que vous exécutez Ubuntu 16.04 sur un PC (x86 ou x64).
> Sur une autre version Ubuntu, ou une autre distribution Linux, vous devez adapter les étapes obligatoires.

## <a name="prerequisites"></a>Prérequis

[!include[Ubuntu Prerequisites](includes/ubuntu1604-prerequisites.md)]

## <a name="getting-the-binary-package"></a>Obtention du package binaire

[!include[License Notice](includes/license-notice.md)]

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

   [!include[Linux Binary Archive Content](includes/linuxbinary-content.md)]

## <a name="sample-code"></a>Exemple de code

Le code suivant reconnaît le discours vocal en anglais à partir de votre microphone.
Placez-le dans un fichier nommé `quickstart-linux.cpp` :

[!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/Linux/quickstart-linux/quickstart-linux.cpp#code)]

Remplacez la clé d’abonnement dans le code par celle que vous avez obtenue.

## <a name="building"></a>Génération

> [!NOTE]
> N’oubliez pas de copier et de coller les commandes de génération ci-dessous dans _une seule ligne_.

* Exécutez la commande suivante pour générer l’application sur un ordinateur x64 :

  ```sh
  g++ quickstart-linux.cpp -o quickstart-linux -I "$SPEECHSDK_ROOT/include/cxx_api" -I "$SPEECHSDK_ROOT/include/c_api" --std=c++14 -lpthread -lMicrosoft.CognitiveServices.Speech.core -L "$SPEECHSDK_ROOT/lib/x64" -l:libssl.so.1.0.0 -l:libcurl.so.4 -l:libasound.so.2
  ```

* Exécutez la commande suivante pour générer l’application sur un ordinateur x86 :

  ```sh
  g++ quickstart-linux.cpp -o quickstart-linux -I "$SPEECHSDK_ROOT/include/cxx_api" -I "$SPEECHSDK_ROOT/include/c_api" --std=c++14 -lpthread -lMicrosoft.CognitiveServices.Speech.core -L "$SPEECHSDK_ROOT/lib/x86" -l:libssl.so.1.0.0 -l:libcurl.so.4 -l:libasound.so.2
  ```

## <a name="running"></a>Exécution

Pour exécuter l’application, vous devez configurer le chemin d’accès de la bibliothèque du chargeur pour pointer vers la bibliothèque du Kit de développement logiciel (SDK) de reconnaissance vocale.

* Sur un ordinateur x64, exécutez :

  ```sh
  export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:$SPEECHSDK_ROOT/lib/x64"
  ```

* Sur un ordinateur x86, exécutez :

  ```sh
  export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:$SPEECHSDK_ROOT/lib/x86"
  ```

Exécutez l’application comme suit :

```sh
./quickstart-linux
```

Si tout se passe bien, la sortie doit ressembler à celle-ci :

```text
Say something...
We recognized: What's the weather
```

## <a name="downloading-the-sample"></a>Téléchargement de l’exemple

Pour obtenir le dernier jeu d’exemples, consultez le [référentiel GitHub d’exemples pour le Kit de développement logiciel (SDK) de reconnaissance vocale Cognitive Services](https://aka.ms/csspeech/samples).

## <a name="next-steps"></a>Étapes suivantes

* Consultez la [page d’exemples](samples.md) pour obtenir des exemples supplémentaires.
