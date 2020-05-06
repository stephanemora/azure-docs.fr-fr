---
title: 'Démarrage rapide : Traduire la voix en plusieurs langues – Service Speech'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 12/09/2019
ms.author: yulili
ms.openlocfilehash: 8c8cbc4e4f531d7a06ae3a33c33df9264c2cc6f2
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "74981462"
---
Dans ce guide de démarrage rapide, vous allez utiliser le [SDK Speech](~/articles/cognitive-services/speech-service/speech-sdk.md) pour traduire de manière interactive de la parole d’une langue à l’autre. Une fois les quelques conditions préalables satisfaites, la traduction vocale dans plusieurs langues ne nécessite que six étapes :
> [!div class="checklist"]
> * Créez un objet ````SpeechTranslationConfig```` à partir de votre clé d’abonnement et de votre région.
> * Mettez à jour l’objet ````SpeechTranslationConfig```` pour spécifier la langue source de la reconnaissance vocale.
> * Mettez à jour l’objet ````SpeechTranslationConfig```` pour spécifier plusieurs langues de traduction cibles.
> * Créez un objet ````TranslationRecognizer```` à l’aide de l’objet ````SpeechTranslationConfig```` ci-dessus.
> * À l’aide de l’objet ````TranslationRecognizer````, démarrez le processus de reconnaissance pour un seul énoncé.
> * Inspectez le ````TranslationRecognitionResult```` retourné.
