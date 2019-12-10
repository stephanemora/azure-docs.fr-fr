---
title: 'Démarrage rapide : Reconnaître la voix à partir d’un fichier audio – Service Speech'
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
ms.openlocfilehash: 2f7ec24667514fb131af29321a53f97210e58fc9
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2019
ms.locfileid: "74819427"
---
Dans ce guide de démarrage rapide, vous allez utiliser le [kit de développement logiciel (SDK) Speech](~/articles/cognitive-services/speech-service/speech-sdk.md) pour reconnaître la voix à partir d’un fichier audio. Après avoir satisfait à quelques conditions préalables, la reconnaissance vocale à partir d’un fichier ne contient que cinq étapes :
> [!div class="checklist"]
> * Créez un objet ````SpeechConfig```` à partir de votre clé d’abonnement et de votre région.
> * Créez un objet ````AudioConfig```` qui spécifie le nom du fichier WAV.
> * Créez un objet ````SpeechRecognizer```` à l’aide des objets ````SpeechConfig```` et ````AudioConfig```` ci-dessus.
> * À l’aide de l’objet ````SpeechRecognizer````, démarrez le processus de reconnaissance pour un seul énoncé.
> * Inspectez le ````SpeechRecognitionResult```` retourné.
