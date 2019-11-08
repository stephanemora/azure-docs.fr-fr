---
title: 'Démarrage rapide : Kit de développement logiciel (SDK) Speech pour la configuration de la plateforme C++ (macOS) – Service de reconnaissance vocale'
titleSuffix: Azure Cognitive Services
description: Utilisez ce guide pour configurer votre plateforme pour C++ sur macOS avec Speech Services SDK.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 10/14/2019
ms.author: erhopf
ms.openlocfilehash: 2c6a186eef631372f08b876083dacede31cf1077
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73504466"
---
Ce guide explique comment installer le [Kit de développement logiciel (SDK) Speech](~/articles/cognitive-services/speech-service/speech-sdk.md) pour C++ sur macOS 10.13 et versions ultérieures.

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="system-requirements"></a>Conditions requises pour le système

macOS 10.13 et versions ultérieures

## <a name="install-speech-sdk"></a>Installer le kit SDK Speech

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

Vous pouvez maintenant passer aux [étapes suivantes](#next-steps) ci-dessous.

## <a name="next-steps"></a>Étapes suivantes

[!INCLUDE [windows](../quickstart-list.md)]
