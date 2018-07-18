---
title: Exemple de traduction | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: Voici un exemple de traduction vocale.
services: cognitive-services
author: wolfma61
manager: onano
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 06/07/2018
ms.author: wolfma
ms.openlocfilehash: 1151628ed695e74e2196c20b08e33fa5eaf33282
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37028385"
---
# <a name="sample-for-translation"></a>Exemple de traduction

> [!NOTE]
> Pour savoir comment télécharger cet exemple et d’autres, consultez l’article [Samples for the Cognitive Services Speech SDK](samples.md) (Exemples pour le Kit de développement logiciel (SDK) Cognitive Services Speech).

[!include[Get a Subscription Key](includes/get-subscription-key.md)]

> [!NOTE]
> Les déclarations de niveau supérieur suivantes doivent être en place dans tous les exemples ci-après :
>
> [!code-csharp [Using Statements](~/samples-cognitive-services-speech-sdk/Windows/csharp_samples/translation_samples.cs#toplevel)]
>
> - - -

## <a name="translation-using-the-microphone"></a>Traduction à l’aide du micro

L’extrait de code ci-après indique comment traduire une entrée vocale de l’anglais vers l’allemand, et comment obtenir la sortie vocale du texte traduit. Ce code utilise le micro.

[!code-csharp[Translation Using Microphone](~/samples-cognitive-services-speech-sdk/Windows/csharp_samples/translation_samples.cs#TranslationWithMicrophoneAsync)]

- - -

## <a name="translation-using-file-input"></a>Traduction à l’aide d’une entrée de fichier

L’extrait de code ci-après indique comment traduire une entrée vocale de l’anglais vers l’allemand et vers le français.
Il utilise un fichier en guise d’entrée.

[!code-csharp[Translation Using File Input](~/samples-cognitive-services-speech-sdk/Windows/csharp_samples/translation_samples.cs#TranslationWithFileAsync)]

- - -

## <a name="sample-source-code"></a>Exemple de code source

La dernière version des exemples et des exemples encore plus avancés se trouvent dans un [référentiel GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk) dédié.

## <a name="next-steps"></a>Étapes suivantes

- [Reconnaissance vocale](./speech-to-text-sample.md)

- [Reconnaissance de l'intention](./intent.md)
