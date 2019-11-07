---
title: 'Démarrage rapide : Reconnaître la voix à partir d’un fichier audio - Speech Services'
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
ms.openlocfilehash: bbd3880ea679dc5fc86c0fc1ece101ca3fca74d9
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73506282"
---
Dans ce guide de démarrage rapide, vous allez utiliser le [kit de développement logiciel (SDK) Speech](~/articles/cognitive-services/speech-service/speech-sdk.md) pour reconnaître la voix à partir d’un fichier audio. Après avoir satisfait à quelques conditions préalables, la reconnaissance vocale à partir d’un fichier ne contient que cinq étapes :
> [!div class="checklist"]
> * Créez un objet ````SpeechConfig```` à partir de votre clé d’abonnement et de votre région.
> * Créez un objet ````AudioConfig```` qui spécifie le nom du fichier WAV.
> * Créez un objet ````SpeechRecognizer```` à l’aide des objets ````SpeechConfig```` et ````AudioConfig```` ci-dessus.
> * À l’aide de l’objet ````SpeechRecognizer````, démarrez le processus de reconnaissance pour un seul énoncé.
> * Inspectez le ````SpeechRecognitionResult```` retourné.
