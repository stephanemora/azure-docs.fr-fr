---
title: 'Démarrage rapide : Synthétiser la voix – Service Speech'
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
ms.openlocfilehash: d19f779f67cc0dea8cc7f06aa275885d75c3092e
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2019
ms.locfileid: "74818206"
---
Dans ce guide de démarrage rapide, vous allez utiliser le [SDK Speech](~/articles/cognitive-services/speech-service/speech-sdk.md) pour effectuer la synthèse vocale du texte. Après avoir satisfait à quelques conditions préalables, il suffit de quatre étapes pour diriger la synthétisées vers les haut-parleurs par défaut :
> [!div class="checklist"]
> * Créez un objet ````SpeechConfig```` à partir de votre clé d’abonnement et de votre région.
> * Créez un objet ````SpeechSynthesizer```` à l’aide de l’objet ````SpeechConfig```` ci-dessus.
> * Utilisation de l’objet ````SpeechSynthesizer```` pour le rendu audio du texte.
> * Vérifiez le résultat ````SpeechSynthesisResult```` retourné pour les erreurs.
