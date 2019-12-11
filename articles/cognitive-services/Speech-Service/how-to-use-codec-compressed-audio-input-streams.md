---
title: Diffuser en streaming du contenu audio compressé par codec avec le SDK Speech – Service Speech
titleSuffix: Azure Cognitive Services
description: Découvrez comment diffuser en streaming du contenu audio compressé sur le service Speech avec le SDK Speech. Disponible pour C++, C#et Java pour Linux, Java dans Android et Objective-C dans iOS.
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/20/2019
ms.author: amishu
ms.openlocfilehash: 36e4506ea290d6109e1d1ae874b7e0f7c11bf50d
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2019
ms.locfileid: "74805822"
---
# <a name="using-codec-compressed-audio-input-with-the-speech-sdk"></a>Utilisation d’une entrée audio compressée par codec avec le SDK Speech

L’API **Compressed Audio Input Stream** du SDK Speech permet de diffuser en streaming du contenu audio compressé sur le service Speech à l’aide de PullStream ou PushStream.

> [!IMPORTANT]
> La diffusion en continu d’entrée audio compressée est actuellement prise en charge pour C++, C# et Java sur Linux (Ubuntu 16.04, Ubuntu 18.04 et Debian 9). Elle est également prise en charge pour les plateformes [Java dans Android](how-to-use-codec-compressed-audio-input-streams-android.md) et [Objective-C dans iOS](how-to-use-codec-compressed-audio-input-streams-ios.md).
> Le Kit de développement logiciel (SDK) Speech version 1.7.0 ou supérieure est nécessaire.

Pour wav/PCM, consultez la documentation Speech principale.  En dehors de wav/PCM, les formats d’entrées compressées par codec pris en charge sont les suivants :

- MP3
- OPUS/OGG
- FLAC
- ALAW dans un conteneur wav
- MULAW dans un conteneur wav

## <a name="prerequisites"></a>Prérequis

La gestion d’audio compressé est implémentée à l’aide de [GStreamer](https://gstreamer.freedesktop.org). Pour une raison liée à la gestion des licences, les fichiers binaires GStreamer ne sont pas compilés et liés avec le Kit de développement logiciel (SDK) Speech. Ainsi, le développeur d’application doit installer les éléments suivants sur Ubuntu 16.04, Ubuntu 18.04 et Debian 9 pour utiliser l’entrée audio compressée.

```sh
sudo apt install libgstreamer1.0-0 gstreamer1.0-plugins-base gstreamer1.0-plugins-good gstreamer1.0-plugins-bad gstreamer1.0-plugins-ugly
```

## <a name="example-code-using-codec-compressed-audio-input"></a>Exemple de code utilisant une entrée audio compressé par codec

Pour diffuser en streaming des données dans un format audio compressé sur le service Speech, créez un élément `PullAudioInputStream` ou `PushAudioInputStream`. Ensuite, créez un `AudioConfig` à partir d’une instance de votre classe de flux en spécifiant le format de compression du flux.

Supposons que vous disposez d’une classe de flux d’entrée appelée `myPushStream` et que vous utilisez OPUS/OGG. Voici à quoi peut ressembler votre code :

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
* [Modalités de la reconnaissance vocale dans Java](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-java)
