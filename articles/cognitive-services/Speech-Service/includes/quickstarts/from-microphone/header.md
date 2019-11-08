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
ms.date: 10/28/2019
ms.author: erhopf
ms.openlocfilehash: 64f084f58ae4b12d92d6dee343a59dd56b1c351e
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73505562"
---
Dans ce démarrage rapide, vous utiliserez le [Kit de développement logiciel (SDK) Speech](~/articles/cognitive-services/speech-service/speech-sdk.md) pour une reconnaissance vocale interactive à partir de données audio capturées depuis un micro. Après avoir satisfait à quelques conditions préalables, la reconnaissance vocale à partir d’un microphone ne contient que quatre étapes :
> [!div class="checklist"]
> * Créez un objet ````SpeechConfig```` à partir de votre clé d’abonnement et de votre région.
> * Créez un objet ````SpeechRecognizer```` à l’aide de l’objet ````SpeechConfig```` ci-dessus.
> * À l’aide de l’objet ````SpeechRecognizer````, démarrez le processus de reconnaissance pour une seule énoncé.
> * Inspectez le ````SpeechRecognitionResult```` retourné.
