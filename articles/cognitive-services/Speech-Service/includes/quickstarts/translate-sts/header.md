---
title: 'Démarrage rapide : Traduire la parole en parole – Service de reconnaissance vocale'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 09/20/2019
ms.author: yulili
ms.openlocfilehash: 77b060a5caf9865a1296cd6644cf0c51e3253a0e
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73504842"
---
Dans ce guide de démarrage rapide, vous allez utiliser le [Kit de développement logiciel (SDK) Speech](~/articles/cognitive-services/speech-service/speech-sdk.md) pour traduire de manière interactive de la parole d’une langue dans une autre. Une fois les quelques conditions préalables satisfaites, la traduction de parole en parole ne nécessite que six étapes :
> [!div class="checklist"]
> * Créez un objet ````SpeechTranslationConfig```` à partir de votre clé d’abonnement et de votre région.
> * Mettez à jour l’objet ````SpeechTranslationConfig```` pour spécifier les langues source et cible.
> * Mettez à jour l’objet ````SpeechTranslationConfig```` pour spécifier le nom de la voix de sortie de la parole.
> * Créez un objet ````TranslationRecognizer```` à l’aide de l’objet ````SpeechTranslationConfig```` ci-dessus.
> * À l’aide de l’objet ````TranslationRecognizer````, démarrez le processus de reconnaissance pour un seul énoncé.
> * Inspectez le ````TranslationRecognitionResult```` retourné.
