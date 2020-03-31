---
title: Utiliser Video Indexer pour identifier automatiquement les langues parlées - Azure
titleSuffix: Azure Media Services
description: Cet article décrit comment le modèle d’identification de langue Video Indexer est utilisé pour identifier automatiquement la langue parlée dans une vidéo.
services: media-services
author: juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 09/12/2019
ms.author: ellbe
ms.openlocfilehash: 7a2e03b8dacbf6c3ff20e02c804804b671e86d97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76513879"
---
# <a name="automatically-identify-the-spoken-language-with-language-identification-model"></a>Identifier automatiquement la langue parlée avec le modèle d’identification de langue

Video Indexer prend en charge l’identification automatique de la langue, qui est le processus qui consiste à identifier automatiquement le contenu de la langue parlée à partir de l’audio et à envoyer le fichier multimédia à la transcription dans le langage dominant identifié. Les langues prises en charge incluent l’anglais, l’espagnol, le français, l’allemand, l’italien, le chinois (simplifié), le japonais, le russe et le portugais (Brésil). 

## <a name="choosing-auto-language-identification-on-indexing"></a>Choix de l’identification de la langue automatique pour l’indexation

Lors de l’indexation ou de la [réindexation](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-Index-Video?) d’une vidéo à l’aide de l’API , choisissez l’option `auto detect` dans le paramètre `sourceLanguage`.

Lorsque vous utilisez le portail, accédez à vos **vidéos de compte** sur la page d’acceuil de [Video Indexer](https://www.videoindexer.ai/) et pointez sur le nom de la vidéo que vous souhaitez réindexer. Dans le coin inférieur droit, cliquez sur le bouton réindexer. Dans la boîte de dialogue **Réindexer la vidéo**, choisissez *Détection automatique* dans la liste déroulante **Langue de la source vidéo**.

![détection automatique](./media/language-identification-model/auto-detect.png)

## <a name="model-output"></a>Sortie du modèle

Video Indexer fait la transcription de la vidéo en fonction de la langue la plus probable, si le niveau de confiance est `> 0.6`. Si la langue ne peut pas être identifiée en toute confiance, l’outil suppose que la langue parlée est l’anglais. 

Le langage dominant du modèle est disponible dans Insights JSON en tant qu’attribut `sourceLanguage` (sous root/videos/insights). Un score de confiance correspondant est également disponible sous l’attribut `sourceLanguageConfidence`.

```json
"insights": {
        "version": "1.0.0.0",
        "duration": "0:05:30.902",
        "sourceLanguage": "fr-FR",
        "language": "fr-FR",
        "transcript": [...],
        . . .
        "sourceLanguageConfidence": 0.8563
      },
```

## <a name="guidelines-and-limitations"></a>Instructions et limitations

* Les langues prises en charge incluent l’anglais, l’espagnol, le français, l’allemand, l’italien, le chinois (simplifié), le japonais, le russe et le portugais (Brésil).
* Si l’audio contient des langues autres que la liste prise en charge ci-dessus, le résultat est incertain.
* Si Video Indexer ne parvient pas à identifier la langue avec un niveau de confiance suffisant (`>0.6`), la langue par défaut est l’anglais.
* Il n’existe pas de prise en charge actuelle du fichier avec des langues audio mixtes. Si l’audio contient des langues mixtes, le résultat est incertain. 
* Un audio de faible qualité peut avoir un impact sur les résultats du modèle.
* Le modèle requiert au moins une minute de parole dans l’audio.
* Le modèle est conçu pour reconnaître une parole de conversation spontanée (pas les commandes vocales, le chant, etc.).

## <a name="next-steps"></a>Étapes suivantes

* [Vue d'ensemble](video-indexer-overview.md)
* [Identifier et transcrire automatiquement du contenu multilingue](multi-language-identification-transcription.md)
