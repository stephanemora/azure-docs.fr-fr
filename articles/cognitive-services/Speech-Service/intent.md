---
title: Exemple de reconnaissance de l’intention | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: Voici un exemple de reconnaissance de l’intention.
services: cognitive-services
author: wolfma61
manager: onano
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 07/16/2018
ms.author: wolfma
ms.openlocfilehash: 3e9afc990d6bfa73eb045e7ed76dfd194df309c6
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2018
ms.locfileid: "39213196"
---
# <a name="sample-for-intent-recognition"></a>Exemple de reconnaissance de l’intention

Commencez par obtenir une clé d’abonnement. Contrairement à d’autres services pris en charge par le Kit de développement logiciel (SDK) Cognitive Service Speech, les services Reconnaissance de l’intention requièrent une clé d’abonnement spécifique. Vous trouverez [sur cette page](https://www.luis.ai) des informations supplémentaires concernant la technologie de reconnaissance de l’intention, ainsi que des informations sur la procédure d’acquisition d’une clé d’abonnement. Remplacez votre propre clé d’abonnement Language Understanding, la [région de votre abonnement](regions.md) et l’AppId de votre modèle d’intention aux emplacements appropriés des exemples.

## <a name="top-level-declarations"></a>Déclarations de niveau supérieur

Les déclarations de niveau supérieur suivantes doivent être en place dans tous les exemples ci-après :

[!code-csharp[Top-level declarations](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#toplevel)]

[!code-cpp[Top-level declarations](~/samples-cognitive-services-speech-sdk/samples/cpp/windows/console/samples/intent_recognition_samples.cpp#toplevel)]

[!code-java[Top-level declarations](~/samples-cognitive-services-speech-sdk/samples/java/jre/console/src/com/microsoft/cognitiveservices/speech/samples/console/IntentRecognitionSamples.java#toplevel)]

## <a name="intent-recognition-using-microphone"></a>Reconnaissance de l’intention à l’aide d’un micro

L’extrait de code ci-après indique comment faire fonctionner la reconnaissance de l’intention à partir d’une entrée au micro dans la langue par défaut (`en-US`).

[!code-csharp[Intent Recognition Using Microphone](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#intentRecognitionWithMicrophone)]

[!code-cpp[Intent Recognition Using Microphone](~/samples-cognitive-services-speech-sdk/samples/cpp/windows/console/samples/intent_recognition_samples.cpp#IntentRecognitionWithMicrophone)]

[!code-java[Intent Recognition Using Microphone](~/samples-cognitive-services-speech-sdk/samples/java/jre/console/src/com/microsoft/cognitiveservices/speech/samples/console/IntentRecognitionSamples.java#IntentRecognitionWithMicrophone)]

## <a name="intent-recognition-using-microphone-in-a-specified-language"></a>Reconnaissance de l’intention à l’aide d’un micro dans une langue spécifiée

L’extrait de code ci-après indique comment faire fonctionner la reconnaissance de l’intention à partir d’une entrée au micro dans une langue spécifiée, l’allemand dans ce cas précis (`de-de`).

[!code-csharp[Intent Recognition Using Microphone In A Specified Language](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#intentRecognitionWithLanguage)]

[!code-cpp[Intent Recognition Using Microphone In A Specified Language](~/samples-cognitive-services-speech-sdk/samples/cpp/windows/console/samples/intent_recognition_samples.cpp#IntentRecognitionWithLanguage)]

[!code-java[Intent Recognition Using Microphone In A Specified Language](~/samples-cognitive-services-speech-sdk/samples/java/jre/console/src/com/microsoft/cognitiveservices/speech/samples/console/IntentRecognitionSamples.java#IntentRecognitionWithLanguage)]

## <a name="intent-recognition-from-a-file-using-events"></a>Reconnaissance de l’intention à partir d’un fichier à l’aide d’événements

L’extrait de code ci-après indique comment faire fonctionner la reconnaissance de l’intention de façon continue dans le langue par défaut (`en-US`). Ce code permet d’accéder à des informations supplémentaires, telles que des résultats intermédiaires. L’entrée est effectuée à partir d’un fichier audio, le format pris en charge est WAV à canal unique (mono)/PCM avec un taux d’échantillonnage de 16 KHz.

[!code-csharp[Intent Recognition Using Events From a File](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#intentContinuousRecognitionWithFile)]

[!code-cpp[Intent Recognition Using Events From a File](~/samples-cognitive-services-speech-sdk/samples/cpp/windows/console/samples/intent_recognition_samples.cpp#IntentContinuousRecognitionWithFile)]

[!code-java[Intent Recognition Using Events From a File](~/samples-cognitive-services-speech-sdk/samples/java/jre/console/src/com/microsoft/cognitiveservices/speech/samples/console/IntentRecognitionSamples.java#IntentContinuousRecognitionWithFile)]

[!include[Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]

## <a name="next-steps"></a>Étapes suivantes

- [Reconnaissance vocale](./speech-to-text-sample.md)

- [Traduction](./translation.md)
