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
ms.date: 11/22/2019
ms.author: juliako
ms.openlocfilehash: 457f5b8eb0a740800f9cc0fa69bd554f0679ae9a
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/22/2020
ms.locfileid: "76509170"
---
# <a name="azure-media-services-legacy-components"></a>Composants hérités Azure Media Services

Au fil du temps, des améliorations ont été apportées aux composants Media Service. Ainsi, certains composants hérités ont été mis hors service. Vous trouverez des instructions sur la façon de migrer votre application du composant hérité vers un composant actuel dans les articles suivants.
 
## <a name="retirement-plans-of-legacy-components-and-migration-guidance"></a>Plans de mise hors service de composants hérités et conseils de migration

Nous annonçons la dépréciation des processeurs multimédias *Windows Azure Media Encoder* (WAME) et *Azure Media Encoder* (AME). Ces processeurs seront mis hors service le 1er mars 2020.

* [Migrer de Windows Azure Media Encoder vers Media Encoder Standard](migrate-windows-azure-media-encoder.md)
* [Migrer d'Azure Media Encoder vers Media Encoder Standard](migrate-azure-media-encoder.md)

Nous annonçons également la mise hors service des processeurs multimédia Media Analytics suivants : 
 
|Nom du processeur multimédia|Date de mise hors service|Remarques supplémentaires|
|---|---|
|[Azure Media Indexer 2](media-services-process-content-with-indexer2.md)| 1er octobre 2020|Ce processeur multimédia sera remplacé par [Azure Media Services Video Indexer](https://docs.microsoft.com/azure/media-services/video-indexer/). Pour plus d’informations, consultez [Effectuer une migration depuis Azure Media Indexer 2 vers Azure Media Services Video Indexer](migrate-indexer-v1-v2.md).|
|[Azure Media Indexer](media-services-index-content.md)|1er octobre 2020|Ce processeur multimédia sera remplacé par [Azure Media Services Video Indexer](https://docs.microsoft.com/azure/media-services/video-indexer/). Pour plus d’informations, consultez [Effectuer une migration depuis Azure Media Indexer vers Azure Media Services Video Indexer](migrate-indexer-v1-v2.md).

## <a name="next-steps"></a>Étapes suivantes

[Conseils de migration pour le passage de Media Services v2 à Media Services v3](../latest/migrate-from-v2-to-v3.md)
