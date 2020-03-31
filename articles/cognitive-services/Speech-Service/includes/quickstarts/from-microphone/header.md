---
title: 'Démarrage rapide : Reconnaître la voix à partir d’un microphone – Service Speech'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 11/20/2019
ms.author: erhopf
ms.openlocfilehash: ab51fe0323ea23c16cb52aa7a0570f8d51d40b2f
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75469585"
---
Dans ce guide de démarrage rapide, vous allez utiliser le [SDK Speech](~/articles/cognitive-services/speech-service/speech-sdk.md) pour effectuer une reconnaissance vocale interactive à partir d’entrées de micro, et obtenir la transcription à partir de l’audio capturé. Il est facile d’intégrer cette fonctionnalité à vos applications et à vos appareils pour les tâches de reconnaissance courantes, telles que la transcription des conversations. Elle peut également être utilisée pour des intégrations plus complexes, telles que l’utilisation de Bot Framework avec le SDK Speech pour créer des assistants vocaux.

Après avoir satisfait certaines conditions préalables, la reconnaissance vocale à partir d’un micro se fait en seulement quatre étapes :

> [!div class="checklist"]
> * Créez un objet `SpeechConfig` à partir de votre clé d’abonnement et de votre région.
> * Créez un objet `SpeechRecognizer` à l’aide de l’objet `SpeechConfig` ci-dessus.
> * À l’aide de l’objet `SpeechRecognizer`, démarrez le processus de reconnaissance pour un seul énoncé.
> * Inspectez le `SpeechRecognitionResult` retourné.
