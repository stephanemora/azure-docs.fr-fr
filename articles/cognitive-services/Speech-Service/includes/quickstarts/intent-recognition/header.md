---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/27/2020
ms.author: trbye
ms.openlocfilehash: 4725a1a9cf2cb74655a37ac27a0a86f10d7f4bb9
ms.sourcegitcommit: e46f9981626751f129926a2dae327a729228216e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/08/2021
ms.locfileid: "98052915"
---
Dans ce guide de démarrage rapide, vous allez utiliser le kit [SDK Speech](~/articles/cognitive-services/speech-service/speech-sdk.md) et le service Language Understanding (LUIS) pour reconnaître des intentions dans des données audio capturées à partir d’un microphone. Plus précisément, vous allez utiliser le SDK Speech pour capturer la voix et un domaine prédéfini à partir de LUIS de façon à identifier les intentions à des fins domotiques, comme allumer et éteindre une lumière. 

Après avoir satisfait quelques prérequis, la reconnaissance vocale et l’identification des intentions à partir d’un micro se fait en quelques étapes seulement :

> [!div class="checklist"]
>
> * Créez un objet `SpeechConfig` à partir de votre clé d’abonnement et de votre région.
> * Créez un objet `IntentRecognizer` à l’aide de l’objet `SpeechConfig` ci-dessus.
> * À l’aide de l’objet `IntentRecognizer`, démarrez le processus de reconnaissance pour un seul énoncé.
> * Inspectez le `IntentRecognitionResult` retourné.

