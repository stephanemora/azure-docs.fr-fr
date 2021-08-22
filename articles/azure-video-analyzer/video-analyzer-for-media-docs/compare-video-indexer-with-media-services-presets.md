---
title: Comparaison des préréglages d’Azure Video Analyzer for Media (anciennement Video Indexer) et d’Azure Media Services v3
description: Cet article compare les fonctionnalités d’Azure Video Analyzer for Media (anciennement Video Indexer) et les préréglages d’Azure Media Services v3.
services: azure-video-analyzer
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.subservice: azure-video-analyzer-media
ms.date: 02/24/2020
ms.author: juliako
ms.openlocfilehash: cdd354adc7a34cdcb7e0811ffd2a5d0b50018fee
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/15/2021
ms.locfileid: "112121504"
---
# <a name="compare-azure-media-services-v3-presets-and-video-analyzer-for-media"></a>Comparer les préréglages d’Azure Media Services v3 et de Video Analyzer for Media 

Cet article compare les fonctionnalités des **API Video Analyzer for Media (anciennement Video Indexer)** et des **API Media Services v3**. 

Certaines fonctionnalités actuellement proposées par les [API Video Analyzer for Media](https://api-portal.videoindexer.ai/) et les [API Media Services v3](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/Encoding.json) se chevauchent. Le tableau suivant offre des indications pour comprendre les différences et les similitudes. 

## <a name="compare"></a>Comparer

|Fonctionnalité|API Video Analyzer for Media |Préréglages de l’analyseur vidéo et de l’analyseur audio<br/>dans les API Media Services v3|
|---|---|---|
|Insights médias|[Amélioré](video-indexer-output-json-v2.md) |[Fondamentaux](../../media-services/latest/analyze-video-audio-files-concept.md)|
|Expériences|Consultez la liste complète des fonctionnalités prises en charge : <br/> [Vue d’ensemble](video-indexer-overview.md)|Retourne uniquement des insights vidéo|
|Facturation|[Tarifs de Media Services](https://azure.microsoft.com/pricing/details/media-services/#analytics)|[Tarifs de Media Services](https://azure.microsoft.com/pricing/details/media-services/#analytics)|
|Conformité|Pour obtenir des informations actualisées relatives à la conformité, consultez le fichier [Azure Compliance Offerings.pdf](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942/file/178110/23/Microsoft%20Azure%20Compliance%20Offerings.pdf) et recherchez « Video Analyzer for Media » pour voir s’il est conforme à un certificat d’intérêt.|Pour obtenir des informations actualisées relatives à la conformité, consultez le fichier [Azure Compliance Offerings.pdf](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942/file/178110/23/Microsoft%20Azure%20Compliance%20Offerings.pdf) et recherchez « Media Services » pour voir s’il est conforme à un certificat d’intérêt.|
|Version d’évaluation gratuite|USA Est|Non disponible|
|Disponibilité des régions|Consultez les informations relatives à la [disponibilité de Cognitive Services par région](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)|Consultez les informations relatives à la [disponibilité de Media Services par région](https://azure.microsoft.com/global-infrastructure/services/?products=media-services).|

## <a name="next-steps"></a>Étapes suivantes

[Vue d’ensemble de Video Analyzer for Media](video-indexer-overview.md)

[Vue d’ensemble de Media Services v3](../../media-services/latest/media-services-overview.md)
