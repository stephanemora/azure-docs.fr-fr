---
title: 'Démarrage rapide : Reconnaître la voix à partir d’un fichier audio – Service Speech'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/14/2020
ms.author: dapine
ms.openlocfilehash: 2b6270535c0cf69549a7412bd38d9207454e5500
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/15/2020
ms.locfileid: "76037637"
---
Dans ce guide de démarrage rapide, vous allez utiliser le [kit de développement logiciel (SDK) Speech](~/articles/cognitive-services/speech-service/speech-sdk.md) pour reconnaître la voix à partir d’un fichier audio. Une fois les prérequis remplis, la reconnaissance vocale à partir d’un fichier se fait en quelques étapes seulement :
> [!div class="checklist"]
> * Créez un objet `SpeechConfig` à partir de votre clé d’abonnement et de votre région.
> * Créez un objet `AudioConfig` qui spécifie le nom du fichier WAV.
> * Créez un objet `SpeechRecognizer` à l’aide des objets `SpeechConfig` et `AudioConfig` ci-dessus.
> * À l’aide de l’objet `SpeechRecognizer`, démarrez le processus de reconnaissance pour un seul énoncé.
> * Inspectez le `SpeechRecognitionResult` retourné.
