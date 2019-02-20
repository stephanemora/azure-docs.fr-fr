---
title: Types d’événements en temps réel Azure Media Services | Microsoft Docs
description: Cet article contient un tableau détaillé qui compare des types d’événements en temps réel.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 02/06/2019
ms.author: juliako
ms.openlocfilehash: 9d62ef2295abbb8f8fc6f45ffc0c7ab1ce9616e4
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55878355"
---
# <a name="live-event-types-comparison"></a>Comparaison des types d’événements en direct

Dans Azure Media Services, un [événement en direct](https://docs.microsoft.com/rest/api/media/liveevents) peut être de deux types : pass-through et Live Encoding. 

## <a name="types-comparison"></a>Comparaison des types 

Le tableau suivant compare les fonctionnalités des deux types d’événements en direct.

| Fonctionnalité | Événement en direct pass-through | Événement en direct standard |
| --- | --- | --- |
| L’entrée à débit binaire unique est encodée en plusieurs débits binaires dans le cloud |Non  |OUI |
| Résolution vidéo maximale pour les flux de contribution |4K (4096x2160 à 60 images/s) |1080p (1920 x 1088 à 30 images/s)|
| Couches maximales recommandées dans le flux de contribution|Jusqu’à 12|1 audio|
| Couches maximales dans la sortie| Comme dans l’entrée|Jusqu’à 7|
| Bande passante agrégée maximale du flux de contribution|60 Mbits/s|N/A|
| Débit binaire maximal pour une seule couche dans la contribution |20 Mbits/s|20 Mbits/s|
| Prise en charge de pistes audio multilingues|OUI|Non |
| Codecs vidéo d’entrée pris en charge |H.264/AVC et H.265/HEVC|H.264/AVC|
| Codecs vidéo de sortie pris en charge|Comme dans l’entrée|H.264/AVC|
| Profondeur de bit de la vidéo, entrée et sortie, prises en charge|Jusqu’à 10 bits notamment HDR 10/HLG|8 bits|
| Codecs audio d’entrée pris en charge|AAC-LC, HE-AAC v1, HE-AAC v2|AAC-LC, HE-AAC v1, HE-AAC v2|
| Codecs audio de sortie pris en charge|Comme dans l’entrée|AAC-LC|
| Résolution vidéo maximale de la vidéo de sortie|Comme dans l’entrée|720p (à 30 images/seconde)|
| Protocoles d’entrée|RTMP, MP4 fragmenté (Smooth Streaming)|RTMP, MP4 fragmenté (Smooth Streaming)|
| Prix|Consultez la [page de tarification](https://azure.microsoft.com/pricing/details/media-services/) et cliquez sur l’onglet « Vidéo en direct »|Consultez la [page de tarification](https://azure.microsoft.com/pricing/details/media-services/) et cliquez sur l’onglet « Vidéo en direct »|
| Durée maximale| 24 hrs x 365 jours, direct linéaire | Jusqu’à 24 heures|
| Capacité à transmettre directement les données des sous-titres CEA 608/708 intégrées|OUI|OUI|
| Prise en charge de l’insertion d’ardoises|Non |Non |
| Prise en charge de la signalisation des annonces via l’API| Non |Non |
| Prise en charge de la signalisation des annonces via des messages SCTE-35 intrabande|OUI|OUI|
| Capacité de récupération suite à de brèves interruptions du flux de contribution|OUI|Non (défaillance de l’événement en direct après plus de six secondes sans données d’entrée)|
| Prise en charge des groupes d’images d’entrée non uniformes|OUI|Non – l’entrée doit avoir une durée GOP fixe|
| Prise en charge de l’entrée à fréquence d’images variable|OUI|Non. L’entrée doit avoir une fréquence d’images fixe. Les variations mineures sont tolérées, par exemple pendant les scènes à mouvement élevé. Mais le flux de contribution ne peut pas faire baisser la fréquence d’images (par exemple, à 15 images par seconde).|
| Auto-fermeture de l’événement en direct en cas de perte du flux d’entrée|Non |Après 12 heures si aucune sortie en temps réel n’est en cours d’exécution|

## <a name="next-steps"></a>Étapes suivantes

[Vue d’ensemble du streaming en direct](live-streaming-overview.md)
