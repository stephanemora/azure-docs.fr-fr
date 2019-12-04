---
title: 'Démarrage rapide : Reconnaître la voix à partir d’un micro - Service de reconnaissance vocale'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 11/20/2019
ms.author: erhopf
ms.openlocfilehash: bee3b64ece3faac6258fe4d017f12833b12e370d
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/26/2019
ms.locfileid: "74536373"
---
Dans ce guide de démarrage rapide, vous allez utiliser le [SDK Speech](~/articles/cognitive-services/speech-service/speech-sdk.md) pour effectuer une reconnaissance vocale interactive à partir d’entrées de micro, et obtenir la transcription à partir de l’audio capturé. Il est facile d’intégrer cette fonctionnalité à vos applications et à vos appareils pour les tâches de reconnaissance courantes, telles que la transcription des conversations. Elle peut également être utilisée pour des intégrations plus complexes, telles que l’utilisation de Bot Framework avec le SDK Speech pour créer des assistants vocaux.

Après avoir satisfait certaines conditions préalables, la reconnaissance vocale à partir d’un micro se fait en seulement quatre étapes :

> [!div class="checklist"]
> * Créez un objet `SpeechConfig` à partir de votre clé d’abonnement et de votre région.
> * Créez un objet `SpeechRecognizer` à l’aide de l’objet `SpeechConfig` ci-dessus.
> * À l’aide de l’objet `SpeechRecognizer`, démarrez le processus de reconnaissance pour un seul énoncé.
> * Inspectez le `SpeechRecognitionResult` retourné.
