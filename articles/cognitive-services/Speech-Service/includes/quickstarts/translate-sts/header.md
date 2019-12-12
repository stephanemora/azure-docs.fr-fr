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
ms.date: 12/09/2019
ms.author: yulili
ms.openlocfilehash: c5f0a0fe032d18cd4f01aebe9a5c736d6d511a74
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74980822"
---
Dans ce guide de démarrage rapide, vous allez utiliser le [Kit de développement logiciel (SDK) Speech](~/articles/cognitive-services/speech-service/speech-sdk.md) pour traduire de manière interactive de la parole d’une langue dans une autre. Une fois les quelques conditions préalables satisfaites, la traduction de parole en parole ne nécessite que six étapes :
> [!div class="checklist"]
> * Créez un objet ````SpeechTranslationConfig```` à partir de votre clé d’abonnement et de votre région.
> * Mettez à jour l’objet ````SpeechTranslationConfig```` pour spécifier les langues source et cible.
> * Mettez à jour l’objet ````SpeechTranslationConfig```` pour spécifier le nom de la voix de sortie de la parole.
> * Créez un objet ````TranslationRecognizer```` à l’aide de l’objet ````SpeechTranslationConfig```` ci-dessus.
> * À l’aide de l’objet ````TranslationRecognizer````, démarrez le processus de reconnaissance pour un seul énoncé.
> * Inspectez le ````TranslationRecognitionResult```` retourné.
