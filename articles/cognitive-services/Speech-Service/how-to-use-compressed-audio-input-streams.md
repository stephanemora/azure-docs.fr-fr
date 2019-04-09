---
title: Audio Stream compressée avec le Speech SDK - Services de reconnaissance vocale
titleSuffix: Azure Cognitive Services
description: Découvrez comment diffuser des fichiers audio compressés à des Services de reconnaissance vocale de Azure avec le Speech SDK. Disponibles pour C++, C#et Java pour Linux.
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: amishu
ms.openlocfilehash: 2066dc3e20ab9fc92b23fd071728ea6a920d3324
ms.sourcegitcommit: e43ea344c52b3a99235660960c1e747b9d6c990e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/04/2019
ms.locfileid: "59012421"
---
# <a name="stream-compressed-audio-with-the-speech-sdk"></a>Audio Stream compressée avec le Speech SDK

Le Kit de développement Speech **compressés Audio d’entrée Stream** API permet de diffuser des fichiers audio compressés au Service de reconnaissance vocale à l’aide de PullStream ou PushStream.

> [!IMPORTANT]
> Flux audio compressé est uniquement pris en charge pour C++, C#et Java sur Linux (Ubuntu 16.04 ou Ubuntu 18.04).
> Prise en charge est limitée à OPUS/OGG et MP3.

## <a name="prerequisites"></a>Conditions préalables

Vous devez installer ces dépendances pour utiliser l’entrée audio compressée avec le Speech SDK pour Linux :

```sh
sudo apt install libgstreamer1.0-0 gstreamer1.0-plugins-base gstreamer1.0-plugins-good gstreamer1.0-plugins-bad gstreamer1.0-plugins-ugly
```

## <a name="streaming-compressed-audio"></a>Diffusion en continu audio compressé

Pour diffuser en continu dans un format audio compressé pour les Services de reconnaissance vocale, créer `PullAudioInputStream` ou `PushAudioInputStream`. Ensuite, créez un `AudioConfig` à partir d’une instance de votre classe de flux, en spécifiant le format de compression du flux de données.

Supposons que vous disposez d’une classe de flux d’entrée appelée `myPushStream` et que vous utilisez OPUS/OGG. Voici à quoi peut ressembler le code : 

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

* [Obtenez votre abonnement d’essai de reconnaissance vocale](https://azure.microsoft.com/try/cognitive-services/)
* [Apprenez à reconnaître la parole dansC#](quickstart-csharp-dotnet-windows.md)
