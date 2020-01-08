---
title: 'Démarrage rapide : Synthétiser la voix en un fichier audio – Service Speech'
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
ms.openlocfilehash: 9b13d8fc3b77426a59dea5399223b79c4bb4b1a1
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75468007"
---
Dans ce guide de démarrage rapide, vous allez utiliser le [SDK Speech](~/articles/cognitive-services/speech-service/speech-sdk.md) pour effectuer la synthèse vocale du texte dans un fichier audio. Après avoir satisfait à quelques conditions préalables, la synthèse vocale dans un fichier ne contient que cinq étapes :
> [!div class="checklist"]
> * Créez un objet ````SpeechConfig```` à partir de votre clé d’abonnement et de votre région.
> * Créez un objet Configuration audio qui spécifie le nom du fichier WAV.
> * Créez un objet ````SpeechSynthesizer```` à l’aide des objets de configuration ci-dessus.
> * À l’aide de l’objet ````SpeechSynthesizer````, convertissez votre texte en synthèse vocale, en l’enregistrant dans le fichier audio spécifié.
> * Inspectez le résultat ````SpeechSynthesizer```` retourné pour vérifier la présence éventuelle d’erreurs.
