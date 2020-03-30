---
author: IEvangelist
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/27/2020
ms.author: dapine
ms.openlocfilehash: d59a55a9b6d57d90ac6ae893b05e1064c11eb58b
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76900356"
---
Dans ce guide de démarrage rapide, vous allez utiliser le kit [SDK Speech](~/articles/cognitive-services/speech-service/speech-sdk.md) et le service Language Understanding (LUIS) pour reconnaître des intentions dans des données audio capturées à partir d’un microphone. Plus précisément, vous allez utiliser le SDK Speech pour capturer la voix et un domaine prédéfini à partir de LUIS de façon à identifier les intentions à des fins domotiques, comme allumer et éteindre une lumière. 

Après avoir satisfait quelques prérequis, la reconnaissance vocale et l’identification des intentions à partir d’un micro se fait en quelques étapes seulement :

> [!div class="checklist"]
>
> * Créez un objet `SpeechConfig` à partir de votre clé d’abonnement et de votre région.
> * Créez un objet `IntentRecognizer` à l’aide de l’objet `SpeechConfig` ci-dessus.
> * À l’aide de l’objet `IntentRecognizer`, démarrez le processus de reconnaissance pour un seul énoncé.
> * Inspectez le `IntentRecognitionResult` retourné.
