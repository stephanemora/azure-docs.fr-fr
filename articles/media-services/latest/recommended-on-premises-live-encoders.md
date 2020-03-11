---
title: Encodeurs locaux de streaming en direct recommandés par Media Services – Azure | Microsoft Docs
description: Découvrez le streaming en direct d’encodeurs locaux recommandé par Media Services
services: media-services
keywords: encodage;encodeurs;média
author: johndeu
manager: johndeu
ms.author: johndeu
ms.date: 02/10/2020
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 60f9209129c75e329b283045d19b4b5140b40ec2
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/04/2020
ms.locfileid: "78268196"
---
# <a name="recommended-on-premises-live-streaming-encoders"></a>Encodeurs de streaming en direct locaux recommandés

Dans Azure Media Services, un [Événement en temps réel](https://docs.microsoft.com/rest/api/media/liveevents) (canal) représente un pipeline de traitement du contenu de streaming en direct. L’Événement en temps réel reçoit les flux d’entrée en direct de l’une des deux manières suivantes.

* Un encodeur live local envoie à l'Événement en temps réel un flux RTMP ou Smooth Streaming (MP4 fragmenté) multidébit qui n'est pas activé pour effectuer un encodage en temps réel avec Media Services. Les flux ingérés transitent par les Événements en temps réel sans traitement supplémentaire. Cette méthode est appelée **pass-through**. Nous recommandons que l’encodeur live envoie des flux multidébits au lieu d’un flux à vitesse de transmission unique vers un événement en direct pass-through pour permettre le streaming à débit adaptatif vers le client. 

    Si vous utilisez des flux multidébits pour l’événement en direct pass-through, la taille de la vidéo GOP et les fragments vidéo sur différentes vitesses de transmission doivent être synchronisés pour éviter un comportement inattendu côté lecture.

  > [!NOTE]
  > L’utilisation d’une méthode pass-through est le moyen le plus économique de diffuser une vidéo en flux continu.
 
* Un encodeur live local envoie un flux à débit unique à l'Événement en temps réel activé pour effectuer un encodage en temps réel avec Media Services dans l'un des formats suivants : RTMP ou Smooth Streaming (MP4 fragmenté). L'Événement en temps réel procède ensuite à l'encodage en temps réel du flux à débit unique entrant en flux vidéo multidébit (adaptatif).

Pour des informations détaillées sur l’encodage en temps réel avec Media Services, voir [Streaming en direct avec Media Services v3](live-streaming-overview.md).

## <a name="encoder-requirements"></a>Spécifications de l’encodeur

Les encodeurs doivent prendre en charge le protocole TLS 1.2 lors de l’utilisation des protocoles HTTPS ou RTMPS.

## <a name="live-encoders-that-output-rtmp"></a>Encodeurs live qui génèrent une sortie RTMP

Media Services recommande l’utilisation d’un des encodeurs live suivants, qui génèrent une sortie RTMP : Les schémas d’URL pris en charge sont `rtmp://` ou `rtmps://`.

Lors de la diffusion en continu via RTMP, vérifiez les paramètres de pare-feu et/ou de proxy pour confirmer que les ports TCP sortants 1935 et 1936 sont ouverts.<br/><br/>
Lors de la diffusion en flux continu via RTMP, vérifiez les paramètres de pare-feu et/ou de proxy pour confirmer que les ports TCP sortants 2935 et 2936 sont ouverts.

> [!NOTE]
> Les encodeurs doivent prendre en charge le protocole TLS 1.2 lors de l’utilisation de protocoles RTMPS.

- Adobe Flash Media Live Encoder 3.2
- [Cambria Live 4.3](https://www.capellasystems.net/products/cambria-live/)
- Elemental Live (version 2.14.15 et versions ultérieures)
- Haivision KB
- Haivision Makito X HEVC
- OBS Studio
- Switcher Studio (iOS)
- Telestream Wirecast (version 13.0.2 ou et versions ultérieures en raison de la configuration TLS 1.2)
- Telestream Wirecast S (seul RTMP est pris en charge)
- Teradek Slice 756
- TriCaster 8000
- Tricaster Mini HD-4
- VMIX
- xStream
- [Ffmpeg](https://www.ffmpeg.org)
- [GoPro](https://gopro.com/help/articles/block/getting-started-with-live-streaming) Hero 7 et Hero 8
- [Restream.io](https://restream.io/)

## <a name="live-encoders-that-output-fragmented-mp4"></a>Encodeurs live qui génèrent une sortie MP4 fragmenté

Media Services recommande l’utilisation d’un des encodeurs live suivants, qui génèrent une sortie Smooth Streaming multidébit (MP4 fragmenté). Les schémas d’URL pris en charge sont `http://` ou `https://`.

> [!NOTE]
> Les encodeurs doivent prendre en charge le protocole TLS 1.2 lors de l’utilisation de protocoles HTTPS.

- Ateme TITAN Live
- Cisco Digital Media Encoder 2200
- Elemental Live (version 2.14.15 et versions ultérieures en raison de la configuration TLS 1.2)
- Envivio 4Caster C4 Gen III 
- Imagine Communications Selenio MCP3
- Media Excel Hero Live et Hero 4K (UHD/HEVC)
- [Ffmpeg](https://www.ffmpeg.org)

> [!TIP]
>  L’encodeur live Media Excel configuré pour envoyer du flux en temps réel à un événement en direct transmis directement permet de diffuser en continu des événements en direct dans plusieurs langues (par exemple, une piste audio en anglais et une piste audio en espagnol).

## <a name="configuring-on-premises-live-encoder-settings"></a>Configuration des paramètres de l'encodeur live local

Pour plus d'informations sur les paramètres valides pour votre type d'événement en temps réel, consultez [Comparaison des types d'événements en temps réel](live-event-types-comparison.md).

### <a name="playback-requirements"></a>Exigences de lecture

Pour pouvoir lire le contenu, un flux audio et un flux vidéo doivent être présents. La lecture de flux en vidéo uniquement n'est pas prise en charge.

### <a name="configuration-tips"></a>Conseils de configuration

- Si possible, utilisez une connexion Internet câblée.
- Lorsque vous déterminez les besoins en bande passante, doublez les débits binaires de diffusion en continu. Bien que facultative, cette règle simple permet de réduire l’impact de l’encombrement du réseau.
- Lors de l’utilisation d’encodeurs logiciels, fermez tous les programmes inutiles.
- La modification de la configuration de votre encodeur a des effets négatifs sur l’événement lorsque celui-ci a commencé à effectuer des transmissions de type push. Les modifications de configuration peuvent entraîner de l’instabilité. 
- Prévoyez suffisamment de temps pour configurer votre événement. Pour les événements à grande échelle, nous recommandons d’entamer la configuration une heure à l’avance.

## <a name="see-also"></a>Voir aussi

[Devenir partenaire d’encodeur local](become-on-premises-encoder-partner.md)

## <a name="next-steps"></a>Étapes suivantes

[Streaming en direct avec Media Services v3](live-streaming-overview.md)
