---
title: 'Démarrage rapide : Traduire la voix en texte – Service Speech'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 12/09/2019
ms.author: yulili
ms.openlocfilehash: d4781808ce8e80f62e86ce1d0c6db9c38b2636d0
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "74980682"
---
Dans ce guide de démarrage rapide, vous utiliserez le [Kit de développement logiciel (SDK) Speech](~/articles/cognitive-services/speech-service/speech-sdk.md) pour traduire interactivement la parole d’une langue en texte dans une autre langue. Après avoir satisfait à quelques conditions préalables, la traduction de la parole en texte ne contient que cinq étapes :
> [!div class="checklist"]
> * Créez un objet ````SpeechConfig```` à partir de votre clé d’abonnement et de votre région.
> * Mettez à jour l’objet ````SpeechConfig```` pour spécifier les langues source et cible.
> * Créez un objet ````TranslationRecognizer```` à l’aide de l’objet ````SpeechConfig```` ci-dessus.
> * À l’aide de l’objet ````TranslationRecognizer````, démarrez le processus de reconnaissance pour un seul énoncé.
> * Inspectez le ````TranslationRecognitionResult```` retourné.
