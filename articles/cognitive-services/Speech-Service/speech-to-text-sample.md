---
title: Exemple de reconnaissance vocale | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: Voici un exemple de reconnaissance vocale.
services: cognitive-services
author: wolfma61
manager: onano
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 06/07/2018
ms.author: wolfma
ms.openlocfilehash: 2a1850e6a4f3c8eebd1b947aabe1374bdaab3fc8
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37030248"
---
# <a name="sample-for-speech-to-text"></a>Exemple de reconnaissance vocale

> [!NOTE]
> Pour savoir comment télécharger cet exemple (et d’autres), consultez [Exemples de kit de développement logiciel (SDK) Speech](samples.md).

[!include[Get a Subscription Key](includes/get-subscription-key.md)]

> [!NOTE]
> Pour tous les exemples ci-dessous, les déclarations de niveau supérieur suivantes doivent être en place :
>
> [!code-csharp[](~/samples-cognitive-services-speech-sdk/Windows/csharp_samples/speech_recognition_samples.cs#toplevel)]
>
> [!code-cpp[](~/samples-cognitive-services-speech-sdk/Windows/cxx_samples/speech_recognition_samples.cpp#toplevel)]
>
> - - -

## <a name="speech-recognition-using-the-microphone"></a>Reconnaissance vocale avec le microphone

L’extrait de code ci-dessous montre comment faire fonctionner la reconnaissance vocale depuis le microphone dans la langue par défaut (`en-US`).

[!code-csharp[Speech Recognition Using Microphone](~/samples-cognitive-services-speech-sdk/Windows/csharp_samples/speech_recognition_samples.cs#recognitionWithMicrophone)]

[!code-cpp[Speech Recognition Using Microphone](~/samples-cognitive-services-speech-sdk/Windows/cxx_samples/speech_recognition_samples.cpp#SpeechRecognitionWithMicrophone)]

- - -

## <a name="speech-recognition-from-a-file"></a>Reconnaissance vocale depuis un fichier

L’extrait de code suivant reconnaît l’entrée vocale depuis un fichier audio dans la langue par défaut (`en-US`). Le format pris en charge est un canal WAV / PCM simple (mono) avec un taux d’échantillonnage de 16 KHz.

[!include[Sample Audio](includes/sample-audio.md)]

[!code-csharp[Speech Recognition From a File](~/samples-cognitive-services-speech-sdk/Windows/csharp_samples/speech_recognition_samples.cs?name=recognitionFromFile)]

[!code-cpp[Speech Recognition From a File](~/samples-cognitive-services-speech-sdk/Windows/cxx_samples/speech_recognition_samples.cpp?name=SpeechRecognitionWithFile)]

- - -

## <a name="speech-recognition-using-a-customized-model"></a>Reconnaissance vocale à l’aide d’un modèle personnalisé

Le service [Custom Speech Service (CRIS)](https://www.cris.ai/) permet de personnaliser le moteur de reconnaissance vocale de Microsoft pour votre application. L’extrait de code ci-dessous montre comment reconnaître l’entrée vocale depuis un microphone à l’aide de votre modèle CRIS. Renseignez votre clé d’abonnement CRIS et l’identification de votre déploiement avant de l’exécuter.

[!code-csharp[Speech Recognition Using a Customized Model](~/samples-cognitive-services-speech-sdk/Windows/csharp_samples/speech_recognition_samples.cs#recognitionCustomized)]

[!code-cpp[Speech Recognition Using a Customized Model](~/samples-cognitive-services-speech-sdk/Windows/cxx_samples/speech_recognition_samples.cpp#SpeechRecognitionUsingCustomizedModel)]

- - -

## <a name="continuous-speech-recognition"></a>Reconnaissance vocale continue

[!code-csharp[Continuous Speech Recognition](~/samples-cognitive-services-speech-sdk/Windows/csharp_samples/speech_recognition_samples.cs#recognitionContinuous)]

[!code-cpp[Continuous Speech Recognition](~/samples-cognitive-services-speech-sdk/Windows/cxx_samples/speech_recognition_samples.cpp#SpeechContinuousRecognitionUsingEvents)]

- - -

## <a name="sample-source-code"></a>Exemple de code source

La dernière version des exemples et des exemples encore plus avancés se trouvent dans un [référentiel GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk) dédié.

## <a name="next-steps"></a>Étapes suivantes

- [Reconnaissance de l'intention](./intent.md)

- [Traduction](./translation.md)
