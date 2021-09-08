---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/27/2020
ms.author: pafarley
ms.openlocfilehash: 3620bcff2724607e4cbc7537479d45817461f19c
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/07/2021
ms.locfileid: "123543241"
---
Dans ce guide de démarrage rapide, vous allez utiliser le kit [SDK Speech](~/articles/cognitive-services/speech-service/speech-sdk.md) et le service Language Understanding (LUIS) pour reconnaître des intentions dans des données audio capturées à partir d’un microphone. Plus précisément, vous allez utiliser le SDK Speech pour capturer la voix et un domaine prédéfini à partir de LUIS de façon à identifier les intentions à des fins domotiques, comme allumer et éteindre une lumière. 

Après avoir satisfait quelques prérequis, la reconnaissance vocale et l’identification des intentions à partir d’un micro se fait en quelques étapes seulement :

> [!div class="checklist"]
>
> * Créez un objet `SpeechConfig` à partir de votre clé d’abonnement et de votre région.
> * Créez un objet `IntentRecognizer` à l’aide de l’objet `SpeechConfig` ci-dessus.
> * À l’aide de l’objet `IntentRecognizer`, démarrez le processus de reconnaissance pour un seul énoncé.
> * Inspectez le `IntentRecognitionResult` retourné.

