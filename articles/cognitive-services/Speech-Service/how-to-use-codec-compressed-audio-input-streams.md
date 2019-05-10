---
title: Audio codec compressé de Stream avec le Speech SDK - Services de reconnaissance vocale
titleSuffix: Azure Cognitive Services
description: Découvrez comment diffuser des fichiers audio compressés à des Services de reconnaissance vocale de Azure avec le Speech SDK. Disponibles pour C++, C#et Java pour Linux.
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: amishu
ms.openlocfilehash: 41a55eca321cbe1bfa23a889b8e3ce7c701ce769
ms.sourcegitcommit: e6d53649bfb37d01335b6bcfb9de88ac50af23bd
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/09/2019
ms.locfileid: "65468048"
---
# <a name="using-codec-compressed-audio-input-with-the-speech-sdk"></a>À l’aide du codec compressé l’entrée audio avec le Speech SDK

Le Kit de développement Speech **compressés Audio d’entrée Stream** API permet de diffuser des fichiers audio compressés au Service de reconnaissance vocale à l’aide de PullStream ou PushStream.

> [!IMPORTANT]
> Flux audio compressé est uniquement pris en charge pour C++, C#et Java sur Linux (Ubuntu 16.04, 18.04 d’Ubuntu, Debian 9).

Pour wav/PCM consultez la documentation de la ligne directrice de reconnaissance vocale.  En dehors de wav/PCM, les formats d’entrée codec compressé suivants sont pris en charge :

- MP3
- OPUS/OGG

## <a name="prerequisites-to-using-codec-compressed-audio-input"></a>Conditions préalables à l’aide du codec compressé l’entrée audio

Installer ces dépendances supplémentaires pour utiliser l’entrée audio compressée avec le Speech SDK pour Linux :

```sh
sudo apt install libgstreamer1.0-0 gstreamer1.0-plugins-base gstreamer1.0-plugins-good gstreamer1.0-plugins-bad gstreamer1.0-plugins-ugly
```

## <a name="example-code-using-codec-compressed-audio-input"></a>Exemple de code à l’aide du codec compressé l’entrée audio

Pour diffuser en continu dans un format audio compressé pour les Services de reconnaissance vocale, créer `PullAudioInputStream` ou `PushAudioInputStream`. Ensuite, créez un `AudioConfig` à partir d’une instance de votre classe de flux, en spécifiant le format de compression du flux de données.

Supposons que vous disposez d’une classe de flux d’entrée appelée `myPushStream` et que vous utilisez OPUS/OGG. Votre code peut ressembler à ceci :

```csharp
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;

var speechConfig = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");

// Create an audio config specifying the compressed audio format and the instance of your input stream class.
var audioFormat = AudioStreamFormat.GetCompressedFormat(AudioStreamContainerFormat.OGG_OPUS);
var audioConfig = AudioConfig.FromStreamInput(myPushStream, audioFormat);

var recognizer = new SpeechRecognizer(speechConfig, audioConfig);

var result = await recognizer.RecognizeOnceAsync();

var text = result.GetText();
```

## <a name="next-steps"></a>Étapes suivantes

- [Obtenir votre abonnement d’essai gratuit au service Speech](https://azure.microsoft.com/try/cognitive-services/)
- [Découvrir comment utiliser la reconnaissance vocale en C#](quickstart-csharp-dotnet-windows.md)
