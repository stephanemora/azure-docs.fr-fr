---
title: Comparaison des préréglages de Video Indexer et d’Azure Media Services v3 | Microsoft Docs
description: Cette rubrique compare les préréglages de Video Indexer et d’Azure Media Services v3.
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
ms.date: 02/10/2019
ms.author: juliako
ms.openlocfilehash: b4c74a67c66b12e3a7333f3e679e7c61a750cf7e
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/11/2019
ms.locfileid: "56002389"
---
# <a name="compare-azure-media-services-v3-presets-and-video-indexer"></a>Comparer les préréglages d’Azure Media Services v3 et de Video Indexer 

Cet article compare les fonctionnalités des **API Video Indexer** et des **API Media Services v3**. 

Actuellement, il y a un chevauchement entre les fonctionnalités offertes par les [API Video Indexer v2](https://api-portal.videoindexer.ai/) et celles offertes par les [API Media Services v3](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/Encoding.json). Le tableau suivant offre des indications pour comprendre les différences et les similitudes. 

## <a name="compare"></a>Comparer

|Fonctionnalité|API Video Indexer |Préréglages de l’analyseur vidéo et de l’analyseur audio<br/>dans les API Media Services v3|
|---|---|---|
|Insights médias|[Amélioré](video-indexer-output-json-v2.md) |[Fondamentaux](../latest/intelligence-concept.md)|
|Expériences|Consultez la liste complète des fonctionnalités prises en charge : <br/> [Vue d'ensemble](video-indexer-overview.md)|Retourne uniquement des insights vidéo|
|Facturation|[Tarifs de Media Services](https://azure.microsoft.com/pricing/details/media-services/#analytics)|[Tarifs de Media Services](https://azure.microsoft.com/pricing/details/media-services/#analytics)|
|Conformité|[Conformité d'Azure](https://aka.ms/AzureCompliance)|Media Services est compatible avec de nombreuses certifications. Consultez le fichier [Azure Compliance Offerings.pdf](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942/file/178110/23/Microsoft%20Azure%20Compliance%20Offerings.pdf) et recherchez « Media Services » pour savoir s’il est conforme à un certificat d’intérêt.|
|Version d’évaluation gratuite|USA Est|Non disponible|
|Disponibilité |USA Ouest, Asie Est, Europe Nord|Consultez [État Azure](https://azure.microsoft.com/global-infrastructure/services/?products=media-services).|

## <a name="next-steps"></a>Étapes suivantes

[Présentation de Video Indexer](video-indexer-overview.md)

[Vue d’ensemble de Media Services v3](../latest/media-services-overview.md)
