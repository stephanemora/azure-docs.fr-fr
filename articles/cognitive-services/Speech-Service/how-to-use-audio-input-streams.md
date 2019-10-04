---
title: Concepts de flux d’entrée audio du SDK Speech
titleSuffix: Azure Cognitive Services
description: Vue d’ensemble des fonctionnalités de l’API de flux d’entrée audio du SDK Speech
services: cognitive-services
author: fmegen
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: fmegen
ms.openlocfilehash: 06b69da7f7435ce8a1e32150b7abe161ebdf527c
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67606498"
---
# <a name="about-the-speech-sdk-audio-input-stream-api"></a>À propos de l’API de flux d’entrée audio du SDK Speech

L’API de **flux d’entrée audio** du SDK Speech permet de diffuser des flux audio en continu dans les modules de reconnaissance au lieu d’utiliser le microphone ou les API de fichier d’entrée.

Les étapes suivantes sont nécessaires à l’utilisation des flux d’entrée audio :

- Identifiez le format du flux audio. Le format doit être pris en charge par le SDK et le service Speech. Seule la configuration suivante est prise en charge :

  Échantillons audio au format PCM, un canal, 16 000 échantillons par seconde, 32 000 octets par seconde, alignement de deux blocs (16 bits, y compris le remplissage d’un échantillon), 16 bits par échantillon

  Dans le SDK, le code correspondant permettant de créer le format audio se présente ainsi :

  ```
  byte channels = 1;
  byte bitsPerSample = 16;
  int samplesPerSecond = 16000;
  var audioFormat = AudioStreamFormat.GetWaveFormatPCM(samplesPerSecond, bitsPerSample, channels);
  ```

- Vérifiez que votre code peut fournir les données audio RAW respectant ces spécifications. Si vos données sources audio ne correspondent pas aux formats pris en charge, le fichier audio doit être converti au format requis.

- Créez votre propre classe de flux d’entrée audio dérivée de `PullAudioInputStreamCallback`. Implémentez les membres `Read()` et `Close()`. La signature de fonction exacte dépend de la langue, mais le code doit ressembler à cet exemple :

  ```
   public class ContosoAudioStream : PullAudioInputStreamCallback {
      ContosoConfig config;

      public ContosoAudioStream(const ContosoConfig& config) {
          this.config = config;
      }

      public int Read(byte[] buffer, uint size) {
          // returns audio data to the caller.
          // e.g. return read(config.YYY, buffer, size);
      }

      public void Close() {
          // close and cleanup resources.
      }
   };
  ```

- Créez une configuration audio basée sur le format audio et le flux d’entrée. Lorsque vous créez votre module de reconnaissance, passez la configuration de la reconnaissance vocale et la configuration de l’entrée audio. Par exemple :

  ```
  var audioConfig = AudioConfig.FromStreamInput(new ContosoAudioStream(config), audioFormat);

  var speechConfig = SpeechConfig.FromSubscription(...);
  var recognizer = new SpeechRecognizer(speechConfig, audioConfig);

  // run stream through recognizer
  var result = await recognizer.RecognizeOnceAsync();

  var text = result.GetText();
  ```

## <a name="next-steps"></a>Étapes suivantes

* [Obtenir votre abonnement d’essai gratuit au service Speech](https://azure.microsoft.com/try/cognitive-services/)
* [Découvrir comment utiliser la reconnaissance vocale en C#](quickstart-csharp-dotnet-windows.md)
