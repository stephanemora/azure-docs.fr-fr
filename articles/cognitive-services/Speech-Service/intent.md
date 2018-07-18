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
ms.date: 06/07/2018
ms.author: wolfma
ms.openlocfilehash: 38f7f038a803546adb83245519efc5de0c0d1599
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37045008"
---
# <a name="sample-for-intent-recognition"></a>Exemple de reconnaissance de l’intention

> [!NOTE]
> Pour savoir comment télécharger cet exemple et d’autres, consultez l’article [Samples for the Cognitive Services Speech SDK](samples.md) (Exemples pour le Kit de développement logiciel (SDK) Cognitive Services Speech).

> [!NOTE]
> Commencez par obtenir une clé d’abonnement. Contrairement à d’autres services pris en charge par le Kit de développement logiciel (SDK) Cognitive Service Speech, les services Reconnaissance de l’intention requièrent une clé d’abonnement spécifique. Vous trouverez [sur cette page](https://www.luis.ai) des informations supplémentaires concernant la technologie de reconnaissance de l’intention, ainsi que des informations sur la procédure d’acquisition d’une clé d’abonnement. Remplacez votre propre clé d’abonnement, la région du service et l’AppId de votre modèle d’intention aux emplacements appropriés des exemples.

> [!NOTE]
> Les déclarations de niveau supérieur suivantes doivent être en place dans tous les exemples ci-après :
>
> [!code-cpp[](~/samples-cognitive-services-speech-sdk/Windows/cxx_samples/intent_recognition_samples.cpp#toplevel)]
>
> - - -

## <a name="intent-recognition-using-microphone"></a>Reconnaissance de l’intention à l’aide d’un micro

L’extrait de code ci-après indique comment faire fonctionner la reconnaissance de l’intention à partir d’une entrée au micro dans la langue par défaut (`en-US`).

[!code-cpp[Intent Recognition Using Microphone](~/samples-cognitive-services-speech-sdk/Windows/cxx_samples/intent_recognition_samples.cpp#IntentRecognitionWithMicrophone)]

- - -

## <a name="intent-recognition-using-microphone-in-a-specified-language"></a>Reconnaissance de l’intention à l’aide d’un micro dans une langue spécifiée

L’extrait de code ci-après indique comment faire fonctionner la reconnaissance de l’intention à partir d’une entrée au micro dans une langue spécifiée, l’allemand dans ce cas précis (`de-de`).

[!code-cpp[Intent Recognition Using Microphone In A Specified Language](~/samples-cognitive-services-speech-sdk/Windows/cxx_samples/intent_recognition_samples.cpp#IntentRecognitionWithLanguage)]

- - -

## <a name="intent-recognition-from-a-file"></a>Reconnaissance de l’intention à partir d’un fichier

L’extrait de code ci-après assure la reconnaissance de l’intention à partir d’un fichier audio dans la langue par défaut (`en-US`). Le format pris en charge est un canal WAV / PCM simple (mono) avec un taux d’échantillonnage de 16 KHz.

[!include[Sample Audio](includes/sample-audio.md)]

[!code-cpp[Intent Recognition From a File](~/samples-cognitive-services-speech-sdk/Windows/cxx_samples/intent_recognition_samples.cpp#IntentRecognitionWithFile)]

- - -

## <a name="intent-recognition-using-events"></a>Reconnaissance de l’intention à l’aide d’événements

L’extrait de code ci-après indique comment faire fonctionner la reconnaissance de l’intention de façon continue. Ce code permet d’accéder à des informations supplémentaires, telles que des résultats intermédiaires. 

[!code-cpp[Intent Recognition Using Events](~/samples-cognitive-services-speech-sdk/Windows/cxx_samples/intent_recognition_samples.cpp#IntentContinuousRecognitionUsingEvents)]

- - -

## <a name="sample-source-code"></a>Exemple de code source

Pour obtenir le dernier ensemble d’exemples, consultez le [référentiel GitHub d’exemples pour le Kit de développement logiciel (SDK) Cognitive Services Speech](https://aka.ms/csspeech/samples).

## <a name="next-steps"></a>Étapes suivantes

- [Reconnaissance vocale](./speech-to-text-sample.md)

- [Traduction](./translation.md)
