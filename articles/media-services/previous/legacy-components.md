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
ms.date: 09/26/2019
ms.author: juliako
ms.openlocfilehash: e4ff157f58a68c68f8610c6c473f5d69897650ad
ms.sourcegitcommit: e1b6a40a9c9341b33df384aa607ae359e4ab0f53
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71338415"
---
# <a name="azure-media-services-legacy-components"></a>Composants hérités Azure Media Services

Au fil du temps, des améliorations ont été apportées aux composants Media Service. Ainsi, certains composants hérités ont été mis hors service. Vous trouverez des instructions sur la façon de migrer votre application du composant hérité vers un composant actuel dans les articles suivants.

## <a name="legacy-components-and-migration-guidance"></a>Composants hérités et conseils de migration

Nous annonçons la dépréciation des processeurs multimédias *Windows Azure Media Encoder* (WAME) et *Azure Media Encoder* (AME). Ces processeurs seront mis hors service le 30 novembre 2019.

* [Migrer de Windows Azure Media Encoder vers Media Encoder Standard](migrate-windows-azure-media-encoder.md)
* [Migrer d'Azure Media Encoder vers Media Encoder Standard](migrate-azure-media-encoder.md)

Nous annonçons également la dépréciation d’*Azure Media Indexer v1* et d’*Azure Media Indexer v2 Preview*. Le processeur multimédia [Azure Media Indexer v1](media-services-index-content.md) sera mis hors service le 1er octobre 2020. Les processeurs multimédias [Azure Media Indexer v2 Preview](media-services-process-content-with-indexer2.md) ont été mis hors service le 1er janvier 2019.  [Azure Media Services Video Indexer](https://docs.microsoft.com/azure/media-services/video-indexer/) remplace ces processeurs multimédias hérités.

* [Effectuer une migration depuis Azure Media Indexer v1 et Azure Media Indexer v2 vers Azure Media Services Video Indexer](migrate-indexer-v1-v2.md).

## <a name="next-steps"></a>Étapes suivantes

[Conseils de migration pour le passage de Media Services v2 à Media Services v3](../latest/migrate-from-v2-to-v3.md)
