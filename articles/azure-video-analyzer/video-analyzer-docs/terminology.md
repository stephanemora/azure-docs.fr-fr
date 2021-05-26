---
title: Terminologie d’Azure Video Analyzer
description: Cette article fournit une vue d’ensemble de la terminologie d’Azure Video Analyzer.
ms.service: azure-video-analyzer
ms.topic: conceptual
ms.date: 05/10/2021
ms.openlocfilehash: b29e2c788654a5445c0e14c00ad64690c405b003
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110386075"
---
# <a name="azure-video-analyzer-terminology"></a>Terminologie d’Azure Video Analyzer

Cet article fournit une vue d’ensemble de la terminologie relative à [Azure Video Analyzer](overview.md).

## <a name="pipeline-topology"></a>Topologie de pipeline

Une [topologie de pipeline](pipeline.md) vous permet de définir l’emplacement à partir duquel les médias doivent être capturés, la manière dont ils doivent être traités et où les résultats doivent être livrés. Il vous permet de définir un pipeline constitué de sources, de processeurs et de nœuds récepteurs grâce auxquels vous pouvez créer des applications d’analytique vidéo en direct. 

## <a name="rtsp"></a>RTSP

[RTSP](https://tools.ietf.org/html/rfc2326) (Real-Time Streaming Protocol) fait référence au protocole de streaming en temps réel. Il s’agit d’un protocole au niveau de l’application pour contrôler la remise des données avec des propriétés en temps réel. Le protocole RTSP fournit un cadre extensible qui permet une remise contrôlée à la demande de données en temps réel, comme des données audio et vidéo. Video Analyzer [prend en charge](pipeline.md#rtsp-source) la capture, l’analyse et l’enregistrement de vidéos à partir de caméras IP prenant en charge le protocole RTSP.


## <a name="recording"></a>Enregistrement

Dans le contexte d’un système de gestion vidéo pour caméras de sécurité, l’enregistrement vidéo fait référence au processus de capture de vidéos à partir des caméras, et à leur stockage afin de les regarder ultérieurement via des applications mobiles et de navigateur. L’enregistrement vidéo peut être classé dans la catégorie de l’[enregistrement vidéo en continu](continuous-video-recording.md) et l’[enregistrement vidéo basé sur des événements](event-based-video-recording-concept.md). Ces catégories sont expliquées plus en détail dans la page relative au concept d’[enregistrement vidéo](video-recording.md).

## <a name="video"></a>Vidéo

Lorsque vous créez un compte Video Analyzer, vous devez y associer un compte de stockage Azure. Vous pouvez utiliser le service Video Analyzer pour enregistrer la vidéo en direct d’une caméra et conserver ces données sur un disque ou un stockage cloud. Dans ce cas, les données sont stockées en tant que blobs dans un conteneur du compte de stockage. Les vidéos sont des ressources dans votre compte Video Analyzer, qui sont mappées à de tels conteneurs d’objets blob. Tout le contenu associé à de telles ressources vidéo est stocké sous la forme d’objets blob dans les conteneurs correspondants, tandis que Video Analyzer contient les métadonnées (telles qu’un nom, une description, une heure de création).

Vous pouvez utiliser le service Video Analyzer pour créer des ressources vidéo et ajouter des données à des vidéos existantes. Cela rend possible les scénarios d’enregistrement et lecture vidéo en continu basés sur des événements (avec capture vidéo sur l’appareil périphérique, enregistrement dans Video Analyzer et lecture via les fonctionnalités de diffusion en continu de Video Analyzer).

## <a name="grpc"></a>gRPC

[gRPC](https://grpc.io/docs/guides/) est un Framework d’appel de procédure distante (RPC) indépendant du langage et de haute performance. Il utilise des schémas structurés basés sur une session via [Protocol Buffers 3](https://developers.google.com/protocol-buffers/docs/proto3) en tant que format d’échange de messages sous-jacent pour la communication.

## <a name="streaming"></a>Diffusion en continu

Vous pouvez utiliser le service Video Analyzer pour diffuser en continu des enregistrements vidéo à des clients, en utilisant des protocoles de diffusion multimédia en continu basés sur HTTP, comme [HTTP Live Streaming (TLS)](https://developer.apple.com/streaming/) et [MPEG-DASH](https://dashif.org/about/). Vous pouvez utiliser les [widgets du lecteur Azure Video Analyzer](https://github.com/Azure/video-analyzer/blob/main/widgets/readme.md) (composants web) pour lire des ressources vidéo. Par ailleurs, HLS est pris en charge par des lecteurs web comme [JW Player](https://www.jwplayer.com/), [hls.js](https://github.com/video-dev/hls.js/), [VideoJS](https://videojs.com/), [Shaka Player de Google](https://github.com/google/shaka-player), et vous pouvez effectuer un rendu en mode natif dans des applications mobiles avec [Exoplayer](https://github.com/google/ExoPlayer) d’Android et [AV Foundation](https://developer.apple.com/av-foundation/) d’iOS. MPEG-DASH est également pris en charge par [une série de clients mentionnés sur cette page](https://dashif.org/clients/).

## <a name="vms"></a>VMS

[VMS](https://en.wikipedia.org/wiki/Video_management_system) (Video Management System) fait référence à un système de gestion vidéo. De tels systèmes sont utilisés pour configurer et contrôler des caméras de vidéosurveillance, ainsi que pour capturer et enregistrer des vidéos à partir de ces caméras. Ces systèmes fournissent également des applications clientes pour lire les vidéos enregistrées.

## <a name="next-steps"></a>Étapes suivantes

- Découvrez les [pipelines](pipeline.md).
