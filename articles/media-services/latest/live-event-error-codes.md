---
title: Codes d'erreur des événements en direct Azure Media Services | Microsoft Docs
description: Cet article répertorie les codes d'erreur des événements en direct.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/21/2020
ms.author: juliako
ms.openlocfilehash: bfbef771d33ad4d63ec8eaef83331e497d476071
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/25/2020
ms.locfileid: "77599686"
---
# <a name="media-services-live-event-error-codes"></a>Codes d'erreur des événements en direct Media Services

Le tableau ci-dessous répertorie les codes d'erreur des [événements en direct](live-events-outputs-concept.md) :

|Error|Description|
|---|---| 
|MPE_INGEST_FRAMERATE_EXCEEDED|Cette erreur se produit lorsque l'encodeur entrant envoie des flux dépassant les 30 images par seconde pour l'encodage des canaux/événements en direct.|
|MPE_INGEST_VIDEO_RESOLUTION_NOT_SUPPORTED|Cette erreur se produit lorsque l'encodeur entrant envoie des flux dépassant les résolutions suivantes : 1920 x 1088 pour l'encodage des canaux/événements en direct et 4096 x 2160 pour le transfert des canaux/événements en direct.|

## <a name="see-also"></a>Voir aussi

[Codes d'erreur du point de terminaison de streaming (origine)](streaming-endpoint-error-codes.md)

## <a name="next-steps"></a>Étapes suivantes

[Tutoriel : Diffuser en direct avec Media Services](stream-live-tutorial-with-api.md)
