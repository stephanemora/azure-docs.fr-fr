---
title: 'Démarrage rapide : Synthétiser la voix en un fichier audio - Speech Services'
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
ms.openlocfilehash: f69c00f9cf787a192c62f12a707927c5c51a1d31
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73504922"
---
Dans ce guide de démarrage rapide, vous allez utiliser le [SDK Speech](~/articles/cognitive-services/speech-service/speech-sdk.md) pour effectuer la synthèse vocale du texte dans un fichier audio. Après avoir satisfait à quelques conditions préalables, la synthèse vocale dans un fichier ne contient que cinq étapes :
> [!div class="checklist"]
> * Créez un objet ````SpeechConfig```` à partir de votre clé d’abonnement et de votre région.
> * Créez un objet Configuration audio qui spécifie le nom du fichier WAV.
> * Créez un objet ````SpeechSynthesizer```` à l’aide des objets de configuration ci-dessus.
> * À l’aide de l’objet ````SpeechSynthesizer````, convertissez votre texte en synthèse vocale, en l’enregistrant dans le fichier audio spécifié.
> * Inspectez le résultat ````SpeechSynthesizer```` retourné pour vérifier la présence éventuelle d’erreurs.
