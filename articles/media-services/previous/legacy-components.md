---
title: Composants hérités Azure Media Services | Microsoft Docs
description: Cette rubrique traite des composants hérités Azure Media Services.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2020
ms.author: juliako
ms.openlocfilehash: 94a70a1234d902787f248890f0cb538a4ba9c2f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77921077"
---
# <a name="azure-media-services-legacy-components"></a>Composants hérités Azure Media Services

Au fil du temps, des améliorations ont été apportées aux composants Media Service. Ainsi, certains composants hérités ont été mis hors service. Vous trouverez des instructions sur la façon de migrer votre application du composant hérité vers un composant actuel dans les articles suivants.
 
## <a name="retirement-plans-of-legacy-components-and-migration-guidance"></a>Plans de mise hors service de composants hérités et conseils de migration

Nous annonçons la dépréciation des processeurs multimédias *Windows Azure Media Encoder* (WAME) et *Azure Media Encoder* (AME). Ces processeurs seront mis hors service le 31 mars 2020.

* [Migrer de Windows Azure Media Encoder vers Media Encoder Standard](migrate-windows-azure-media-encoder.md)
* [Migrer d'Azure Media Encoder vers Media Encoder Standard](migrate-azure-media-encoder.md)

Nous annonçons également la mise hors service des processeurs multimédia Media Analytics suivants : 
 
|Nom du processeur multimédia|Date de mise hors service|Remarques supplémentaires|
|---|---|
|[Azure Media Indexer 2](media-services-process-content-with-indexer2.md)|1er janvier 2020|Ce processeur multimédia est remplacé par [Azure Media Services Video Indexer](https://docs.microsoft.com/azure/media-services/video-indexer/). Pour plus d’informations, consultez [Effectuer une migration depuis Azure Media Indexer 2 vers Azure Media Services Video Indexer](migrate-indexer-v1-v2.md).|
|[Azure Media Indexer](media-services-index-content.md)|1er mars 2023|Ce processeur multimédia est remplacé par [Azure Media Services Video Indexer](https://docs.microsoft.com/azure/media-services/video-indexer/). Pour plus d’informations, consultez [Effectuer une migration depuis Azure Media Indexer vers Azure Media Services Video Indexer](migrate-indexer-v1-v2.md).|
|[Détection de mouvement](media-services-motion-detection.md)|1er juin 2020|Aucun plan de remplacement pour l’instant.|
|[Création de résumés de vidéos](media-services-video-summarization.md)|1er juin 2020|Aucun plan de remplacement pour l’instant.|
|[Reconnaissance optique de caractères](media-services-video-optical-character-recognition.md)|1er juin 2020|Ce processeur multimédia est remplacé par [Azure Media Services Video Indexer](https://docs.microsoft.com/azure/media-services/video-indexer/). Envisagez également d’utiliser l'[API Azure Media Services V3](https://docs.microsoft.com/azure/media-services/latest/analyzing-video-audio-files-concept). <br/>Voir [Comparer les préréglages d’Azure Media Services v3 et de Video Indexer](https://docs.microsoft.com/azure/media-services/video-indexer/compare-video-indexer-with-media-services-presets)|
|[Face Detector](media-services-face-and-emotion-detection.md)|1er juin 2020|Ce processeur multimédia est remplacé par [Azure Media Services Video Indexer](https://docs.microsoft.com/azure/media-services/video-indexer/). Envisagez également d’utiliser l'[API Azure Media Services V3](https://docs.microsoft.com/azure/media-services/latest/analyzing-video-audio-files-concept). <br/>Voir [Comparer les préréglages d’Azure Media Services v3 et de Video Indexer](https://docs.microsoft.com/azure/media-services/video-indexer/compare-video-indexer-with-media-services-presets)|
|[Content Moderator](media-services-content-moderation.md)|1er juin 2020|Ce processeur multimédia est remplacé par [Azure Media Services Video Indexer](https://docs.microsoft.com/azure/media-services/video-indexer/). Envisagez également d’utiliser l'[API Azure Media Services V3](https://docs.microsoft.com/azure/media-services/latest/analyzing-video-audio-files-concept). <br/>Voir [Comparer les préréglages d’Azure Media Services v3 et de Video Indexer](https://docs.microsoft.com/azure/media-services/video-indexer/compare-video-indexer-with-media-services-presets)|

## <a name="next-steps"></a>Étapes suivantes

[Conseils de migration pour le passage de Media Services v2 à Media Services v3](../latest/migrate-from-v2-to-v3.md)
