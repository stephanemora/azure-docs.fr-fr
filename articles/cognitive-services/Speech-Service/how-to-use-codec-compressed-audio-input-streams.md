---
title: Diffuser en streaming du contenu audio compressé par codec avec le SDK Speech – Speech Services
titleSuffix: Azure Cognitive Services
description: Découvrez comment diffuser en streaming du contenu audio compressé vers Azure Speech Services avec le SDK Speech. Disponible pour C++, C# et Java pour Linux.
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: amishu
ms.openlocfilehash: 8717dad27786450e4275c9fd91523c0e9379f375
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67073064"
---
# <a name="using-codec-compressed-audio-input-with-the-speech-sdk"></a>Utilisation d’une entrée audio compressée par codec avec le SDK Speech

L’API **Compressed Audio Input Stream** du SDK Speech permet de diffuser en streaming du contenu audio compressé vers Speech Service à l’aide de PullStream ou PushStream.

> [!IMPORTANT]
> Le streaming de contenu audio compressé est pris en charge uniquement pour C++, C# et Java sur Linux (Ubuntu 16.04, Ubuntu 18.04, Debian 9).
> Le SDK Speech version 1.4.0 ou supérieure est nécessaire.

Pour wav/PCM, consultez la documentation Speech principale.  En dehors de wav/PCM, les formats d’entrées compressées par codec pris en charge sont les suivants :

- MP3
- OPUS/OGG

## <a name="prerequisites-to-using-codec-compressed-audio-input"></a>Prérequis pour utiliser des entrées audio compressées par codec

Installez ces dépendances supplémentaires pour utiliser des entrées audio compressées avec le SDK Speech pour Linux :

```sh
sudo apt install libgstreamer1.0-0 gstreamer1.0-plugins-base gstreamer1.0-plugins-good gstreamer1.0-plugins-bad gstreamer1.0-plugins-ugly
```

## <a name="example-code-using-codec-compressed-audio-input"></a>Exemple de code utilisant une entrée audio compressé par codec

Pour diffuser en streaming dans un format audio compressé pour Speech Services, créer un élément `PullAudioInputStream` ou `PushAudioInputStream`. Ensuite, créez un `AudioConfig` à partir d’une instance de votre classe de flux en spécifiant le format de compression du flux.

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
- [Découvrir comment utiliser la reconnaissance vocale en C#](quickstart-csharp-dotnet-windows.md)
