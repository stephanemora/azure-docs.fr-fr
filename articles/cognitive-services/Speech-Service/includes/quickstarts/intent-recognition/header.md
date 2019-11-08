---
title: 'Démarrage rapide : Reconnaissance vocale, intentions et entités - Service de reconnaissance vocale'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/28/2019
ms.author: erhopf
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: 3b50ba8f2c3d21fb5bb0ab2c26cedb6bda0fab16
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73505962"
---
Dans ce démarrage rapide, vous utiliserez le [Kit de développement logiciel (SDK) Speech](~/articles/cognitive-services/speech-service/speech-sdk.md) pour une reconnaissance vocale interactive à partir de données audio capturées depuis un micro. Après avoir satisfait certaines conditions préalables, la reconnaissance vocale à partir d’un micro se fait en seulement quatre étapes :
> [!div class="checklist"]
> * Créez un objet ````SpeechConfig```` à partir de votre clé d’abonnement et de votre région.
> * Créez un objet ````IntentRecognizer```` à l’aide de l’objet ````SpeechConfig```` ci-dessus.
> * À l’aide de l’objet ````IntentRecognizer````, démarrez le processus de reconnaissance pour un seul énoncé.
> * Inspectez le ````IntentRecognitionResult```` retourné.