---
title: 'Démarrage rapide : Synthétiser la voix - Service de reconnaissance vocale'
titleSuffix: Azure Cognitive Services
description: Découvrez comment effectuer la synthèse vocale avec le SDK Speech
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 09/20/2019
ms.author: yulili
ms.openlocfilehash: 263a8e98c4c029728272c020efe00e82be20f5e2
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73505074"
---
Dans ce guide de démarrage rapide, vous allez utiliser le [SDK Speech](~/articles/cognitive-services/speech-service/speech-sdk.md) pour effectuer la synthèse vocale du texte. Après avoir satisfait à quelques conditions préalables, il suffit de quatre étapes pour diriger la synthétisées vers les haut-parleurs par défaut :
> [!div class="checklist"]
> * Créez un objet ````SpeechConfig```` à partir de votre clé d’abonnement et de votre région.
> * Créez un objet ````SpeechSynthesizer```` à l’aide de l’objet ````SpeechConfig```` ci-dessus.
> * Utilisation de l’objet ````SpeechSynthesizer```` pour le rendu audio du texte.
> * Vérifiez le résultat ````SpeechSynthesisResult```` retourné pour les erreurs.
