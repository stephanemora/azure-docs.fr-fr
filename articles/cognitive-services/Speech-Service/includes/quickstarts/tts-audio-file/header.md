---
title: 'Démarrage rapide : Synthétiser la voix en un fichier audio – Service Speech'
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
ms.openlocfilehash: 7d9a03f5a57473ce651560b261a4cf84b4ca4824
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2019
ms.locfileid: "74818015"
---
Dans ce guide de démarrage rapide, vous allez utiliser le [SDK Speech](~/articles/cognitive-services/speech-service/speech-sdk.md) pour effectuer la synthèse vocale du texte dans un fichier audio. Après avoir satisfait à quelques conditions préalables, la synthèse vocale dans un fichier ne contient que cinq étapes :
> [!div class="checklist"]
> * Créez un objet ````SpeechConfig```` à partir de votre clé d’abonnement et de votre région.
> * Créez un objet Configuration audio qui spécifie le nom du fichier WAV.
> * Créez un objet ````SpeechSynthesizer```` à l’aide des objets de configuration ci-dessus.
> * À l’aide de l’objet ````SpeechSynthesizer````, convertissez votre texte en synthèse vocale, en l’enregistrant dans le fichier audio spécifié.
> * Inspectez le résultat ````SpeechSynthesizer```` retourné pour vérifier la présence éventuelle d’erreurs.
