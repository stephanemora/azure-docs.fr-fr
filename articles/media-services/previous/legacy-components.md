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
ms.openlocfilehash: cfc9d944f7851f9b802e18c861fd16438171b8e7
ms.sourcegitcommit: a460fdc19d6d7af6d2b5a4527e1b5c4e0c49942f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/07/2020
ms.locfileid: "77069575"
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
|[Azure Media Indexer](media-services-index-content.md)|1er mars 2023|Ce processeur multimédia est remplacé par [Azure Media Services Video Indexer](https://docs.microsoft.com/azure/media-services/video-indexer/). Pour plus d’informations, consultez [Effectuer une migration depuis Azure Media Indexer vers Azure Media Services Video Indexer](migrate-indexer-v1-v2.md).

## <a name="next-steps"></a>Étapes suivantes

[Conseils de migration pour le passage de Media Services v2 à Media Services v3](../latest/migrate-from-v2-to-v3.md)
