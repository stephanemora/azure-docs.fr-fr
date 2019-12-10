---
title: 'Démarrage rapide : Traduire la voix en voix – Service Speech'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 09/20/2019
ms.author: yulili
ms.openlocfilehash: 805193c08e57ddc18dfca0a78c6b58cf895baec1
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2019
ms.locfileid: "74817381"
---
Dans ce guide de démarrage rapide, vous allez utiliser le [Kit de développement logiciel (SDK) Speech](~/articles/cognitive-services/speech-service/speech-sdk.md) pour traduire de manière interactive de la parole d’une langue dans une autre. Une fois les quelques conditions préalables satisfaites, la traduction de parole en parole ne nécessite que six étapes :
> [!div class="checklist"]
> * Créez un objet ````SpeechTranslationConfig```` à partir de votre clé d’abonnement et de votre région.
> * Mettez à jour l’objet ````SpeechTranslationConfig```` pour spécifier les langues source et cible.
> * Mettez à jour l’objet ````SpeechTranslationConfig```` pour spécifier le nom de la voix de sortie de la parole.
> * Créez un objet ````TranslationRecognizer```` à l’aide de l’objet ````SpeechTranslationConfig```` ci-dessus.
> * À l’aide de l’objet ````TranslationRecognizer````, démarrez le processus de reconnaissance pour un seul énoncé.
> * Inspectez le ````TranslationRecognitionResult```` retourné.
