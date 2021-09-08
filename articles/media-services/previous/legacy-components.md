---
title: Composants hérités Azure Media Services | Microsoft Docs
description: Cette rubrique traite des composants hérités Azure Media Services.
services: media-services
documentationcenter: ''
author: jiayali-ms
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/24/2021
ms.author: inhenkel
ms.openlocfilehash: d86a77e724bffeaea6cb39ffc1a20e8737ef6d81
ms.sourcegitcommit: d858083348844b7cf854b1a0f01e3a2583809649
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122835537"
---
# <a name="azure-media-services-legacy-components"></a>Composants hérités Azure Media Services

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Au fil du temps, nous améliorons les composants de Media Service et nous mettons hors service les composants hérités. Cet article vous aide à migrer votre application d’un composant hérité vers un composant actuel.
 
## <a name="retirement-plans-of-legacy-components-and-migration-guidance"></a>Plans de mise hors service de composants hérités et conseils de migration

Les processeurs multimédias *Windows Azure Media Encoder* (WAME) et *Azure Media Encoder* (AME) sont déconseillés.

* [Migrer de Windows Azure Media Encoder vers Media Encoder Standard](migrate-windows-azure-media-encoder.md)
* [Migrer d'Azure Media Encoder vers Media Encoder Standard](migrate-azure-media-encoder.md)

Les processeurs multimédias Media Analytics suivants sont déconseillés ou seront bientôt obsolètes :

 
| **Nom du processeur multimédia** | **Date de mise hors service** | **Remarques supplémentaires** |
| --- | --- | ---|
| Azure Media Indexer | 1er janvier 2020 | Ce processeur multimédia sera remplacé par le [mode de base Media Services V3 AudioAnalyzerPreset](../latest/analyze-video-audio-files-concept.md). Pour plus d’informations, consultez [Effectuer une migration depuis Azure Media Indexer 2 vers Azure Video Analyzer for Media](migrate-indexer-v1-v2.md) (anciennement Video Indexer). |
| Azure Media Indexer 2 | 1er mars 2023 | Ce processeur multimédia sera remplacé par le [mode de base Media Services V3 AudioAnalyzerPreset](../latest/analyze-video-audio-files-concept.md). Pour plus d’informations, consultez [Effectuer une migration depuis Azure Media Indexer 2 vers Azure Video Analyzer for Media](migrate-indexer-v1-v2.md) (anciennement Video Indexer). |
| Détection de mouvement | 1er juin 2020|Aucun plan de remplacement pour l’instant. |
| Création de résumés de vidéos |1er juin 2020|Aucun plan de remplacement pour l’instant.|
| Reconnaissance optique de caractères | 1er juin 2020 |Ce processeur multimédia a été remplacé par Azure Video Analyzer for Media. Envisagez également d’utiliser l'[API Azure Media Services V3](../latest/analyze-video-audio-files-concept.md). <br/>Consultez [Comparer les préréglages d’Azure Media Services v3 et de Video Analyzer for Media](../../azure-video-analyzer/video-analyzer-for-media-docs/compare-video-indexer-with-media-services-presets.md). |
| Face Detector | 1er juin 2020 | Ce processeur multimédia a été remplacé par Azure Video Analyzer for Media. Envisagez également d’utiliser l'[API Azure Media Services V3](../latest/analyze-video-audio-files-concept.md). <br/>Consultez [Comparer les préréglages d’Azure Media Services v3 et de Video Analyzer for Media](../../azure-video-analyzer/video-analyzer-for-media-docs/compare-video-indexer-with-media-services-presets.md). |
| Content Moderator | 1er juin 2020 |Ce processeur multimédia a été remplacé par Azure Video Analyzer for Media. Envisagez également d’utiliser l'[API Azure Media Services V3](../latest/analyze-video-audio-files-concept.md). <br/>Consultez [Comparer les préréglages d’Azure Media Services v3 et de Video Analyzer for Media](../../azure-video-analyzer/video-analyzer-for-media-docs/compare-video-indexer-with-media-services-presets.md). |
| Media Encoder Premium Workflow | 29 février 2024 | L’API AMS v2 ne prend plus en charge l’encodeur Premium. Si vous avez précédemment utilisé l’encodeur Premium basé sur un workflow pour HEVC, l’encodage doit opérer une migration vers le [nouvel encodeur Standard v3](../latest/encode-media-encoder-standard-formats-reference.md) avec prise en charge de l’encodage HEVC. <br/> Si vous avez besoin des fonctionnalités de workflow avancées de l’encodeur Premium, il est recommandé de commencer à utiliser un partenaire d’encodage avancé Azure de [Imagine Communications](https://imaginecommunications.com/), [Telestream](https://telestream.net) ou [Bitmovin](https://bitmovin.com). |

## <a name="next-steps"></a>Étapes suivantes

[Conseils de migration pour le passage de Media Services v2 à Media Services v3](../latest/migrate-v-2-v-3-migration-introduction.md)
