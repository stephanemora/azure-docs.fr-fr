---
title: Utiliser Azure Video Analyzer for Media (anciennement Video Indexer) pour identifier automatiquement les langues parlées - Azure
titleSuffix: Azure Video Analyzer for Media
description: Cet article décrit comment le modèle d’identification de langue Video Analyzer for Media (anciennement Video Indexer) est utilisé pour identifier automatiquement la langue parlée dans une vidéo.
services: azure-video-analyzer
author: juliako
manager: femila
ms.topic: article
ms.subservice: azure-video-analyzer-media
ms.date: 04/12/2020
ms.author: ellbe
ms.openlocfilehash: 8caada82b0c4b0c99e2d18b8586cc0b2c9d575f4
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/15/2021
ms.locfileid: "112119546"
---
# <a name="automatically-identify-the-spoken-language-with-language-identification-model"></a>Identifier automatiquement la langue parlée avec le modèle d’identification de langue

Video Analyzer for Media (anciennement Video Indexer) prend en charge l’identification automatique de la langue, qui est le processus qui consiste à identifier automatiquement le contenu de la langue parlée à partir de l’audio et à envoyer le fichier multimédia à la transcription dans le langage dominant identifié. 

L’identification de la langue (LID) prend actuellement en charge : l’anglais, l’espagnol, le français, l’allemand, l’italien, le chinois (mandarin), le japonais, le russe et le portugais (Brésil). 

Veillez à consulter la section [Instructions et limitations](#guidelines-and-limitations) ci-dessous.

## <a name="choosing-auto-language-identification-on-indexing"></a>Choix de l’identification de la langue automatique pour l’indexation

Lors de l’indexation ou de la [réindexation](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Re-Index-Video) d’une vidéo à l’aide de l’API , choisissez l’option `auto detect` dans le paramètre `sourceLanguage`.

Lorsque vous utilisez le portail, accédez à vos **vidéos de compte** sur la page d’accueil de [Video Analyzer for Media](https://www.videoindexer.ai/) et pointez sur le nom de la vidéo que vous souhaitez réindexer. Dans le coin inférieur droit, cliquez sur le bouton réindexer. Dans la boîte de dialogue **Réindexer la vidéo**, choisissez *Détection automatique* dans la liste déroulante **Langue de la source vidéo**.

![détection automatique](./media/language-identification-model/auto-detect.png)

## <a name="model-output"></a>Sortie du modèle

Video Analyzer for Media fait la transcription de la vidéo en fonction de la langue la plus probable, si le niveau de confiance est `> 0.6`. Si la langue ne peut pas être identifiée en toute confiance, l’outil suppose que la langue parlée est l’anglais. 

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

* L’identification automatique de la langue (LID) prend en charge les langues suivantes : 

    l’anglais, l’espagnol, le français, l’allemand, l’italien, le chinois (mandarin), le japonais, le russe et le portugais (Brésil).
* Même si Video Analyzer for Media prend en charge l’arabe (moderne standard et levantin), l’hindi et le coréen, ces langues ne sont pas prises en charge dans l’identification de la langue.
* Si l’audio contient des langues autres que la liste prise en charge ci-dessus, le résultat est incertain.
* Si Video Analyzer for Media ne parvient pas à identifier la langue avec un niveau de confiance suffisant (`>0.6`), la langue par défaut est l’anglais.
* Il n’existe pas de prise en charge actuelle du fichier avec des langues audio mixtes. Si l’audio contient des langues mixtes, le résultat est incertain. 
* Un audio de faible qualité peut avoir un impact sur les résultats du modèle.
* Le modèle requiert au moins une minute de parole dans l’audio.
* Le modèle est conçu pour reconnaître une parole de conversation spontanée (pas les commandes vocales, le chant, etc.).

## <a name="next-steps"></a>Étapes suivantes

* [Vue d'ensemble](video-indexer-overview.md)
* [Identifier et transcrire automatiquement du contenu multilingue](multi-language-identification-transcription.md)
