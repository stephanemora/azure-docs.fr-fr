---
title: 'Démarrage rapide : Configuration de la plateforme du kit SDK Speech pour C++ (Linux) – Service Speech'
titleSuffix: Azure Cognitive Services
description: Utilisez ce guide pour configurer votre plateforme pour C++ sur Linux avec le kit SDK du service Speech.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/14/2019
ms.author: erhopf
ms.openlocfilehash: 6bb647273467a07786413ff4ea30cda836b7cb1b
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75469382"
---
Ce guide explique comment installer le [Kit de développement logiciel (SDK) Speech](~/articles/cognitive-services/speech-service/speech-sdk.md) pour Linux.

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="system-requirements"></a>Configuration système requise

Linux (Ubuntu 16.04, Ubuntu 18.04, Debian 9)

## <a name="prerequisites"></a>Conditions préalables requises

Pour effectuer ce démarrage rapide, vous devez disposer des éléments suivants :

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)

* Les plateformes Linux prises en charge nécessitent l’installation de certaines bibliothèques (`libssl` pour la prise en charge du protocole SSL et `libasound2` pour la prise en charge du son). Reportez-vous à votre distribution ci-dessous pour connaître les commandes nécessaires à l’installation des versions appropriées de ces bibliothèques.

   * Sur Ubuntu :

     ```sh
     sudo apt-get update
     sudo apt-get install build-essential libssl1.0.0 libasound2 wget
     ```

   * Sur Debian 9 :

     ```sh
     sudo apt-get update
     sudo apt-get install build-essential libssl1.0.2 libasound2 wget
     ```

## <a name="install-speech-sdk"></a>Installer le kit SDK Speech

Le kit SDK Speech pour Linux peut être utilisé pour créer des applications 32 bits et 64 bits. Les bibliothèques et fichiers d’en-tête nécessaires sont téléchargeables sous la forme d’un fichier tar à partir de https://aka.ms/csspeech/linuxbinary.

Téléchargez et installez le Kit de développement logiciel (SDK) comme suit :

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

   [!INCLUDE [Linux Binary Archive Content](~/includes/cognitive-services-speech-service-linuxbinary-content.md)]

Vous pouvez maintenant passer aux [étapes suivantes](#next-steps) ci-dessous.

## <a name="next-steps"></a>Étapes suivantes

[!INCLUDE [windows](../quickstart-list.md)]
