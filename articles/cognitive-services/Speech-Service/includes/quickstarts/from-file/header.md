---
title: 'Démarrage rapide : Reconnaître la voix à partir d’un fichier audio – Service Speech'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/28/2019
ms.author: erhopf
ms.openlocfilehash: e333b156eaf9c4b6e09e631513ea099018f0691b
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75469150"
---
Dans ce guide de démarrage rapide, vous allez utiliser le [kit de développement logiciel (SDK) Speech](~/articles/cognitive-services/speech-service/speech-sdk.md) pour reconnaître la voix à partir d’un fichier audio. Après avoir satisfait à quelques conditions préalables, la reconnaissance vocale à partir d’un fichier ne contient que cinq étapes :
> [!div class="checklist"]
> * Créez un objet ````SpeechConfig```` à partir de votre clé d’abonnement et de votre région.
> * Créez un objet ````AudioConfig```` qui spécifie le nom du fichier WAV.
> * Créez un objet ````SpeechRecognizer```` à l’aide des objets ````SpeechConfig```` et ````AudioConfig```` ci-dessus.
> * À l’aide de l’objet ````SpeechRecognizer````, démarrez le processus de reconnaissance pour un seul énoncé.
> * Inspectez le ````SpeechRecognitionResult```` retourné.
