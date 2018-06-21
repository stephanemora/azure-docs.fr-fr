---
title: Comparaison d’encodeurs multimédia à la demande Azure | Microsoft Docs
description: Cette rubrique compare les fonctionnalités d’encodage de **Media Encoder Standard** et de **Media Encoder Premium Workflow**.
services: media-services
documentationcenter: ''
author: juliako
manager: cfowler
editor: ''
ms.assetid: a79437c0-4832-423a-bca8-82632b2c47cc
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2017
ms.author: juliako;anilmur
ms.openlocfilehash: cf420c6b5f72b2109016bdb8b86d6cfcc506f4e5
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34639518"
---
# <a name="comparison-of-azure-on-demand-media-encoders"></a>Comparaison d’encodeurs multimédia à la demande Azure

Cette rubrique compare les fonctionnalités d’encodage de **Media Encoder Standard** et de **Media Encoder Premium Workflow**.

## <a name="video-and-audio-processing-capabilities"></a>Fonctionnalités de traitement vidéo et audio

Le tableau suivant compare les fonctionnalités de Media Encoder Standard (MES) et Media Encoder Premium Workflow (MEPW). 

|Fonctionnalité|Media Encoder Standard|Media Encoder Premium Workflow|
|---|---|---|
|Appliquer une logique conditionnelle lors de l’encodage<br/>(par exemple, si l’entrée est en HD, encodez le type audio 5.1)|Non |OUI|
|Sous-titres|Non |[Oui](media-services-premium-workflow-encoder-formats.md#closed_captioning)|
|[Correction professionnelle du niveau sonore Dolby®](http://www.dolby.com/us/en/technologies/dolby-professional-loudness-solutions.pdf)<br/> avec Dialogue Intelligence™|Non |OUI|
|Désentrelacement, inverse telecine|De base|Qualité de diffusion|
|Détection et suppression des bordures noires <br/>(pillar box, cadres)|Non |OUI|
|Génération de miniatures|[Oui](media-services-dotnet-generate-thumbnail-with-mes.md)|[Oui](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4)|
|Détourage/rognage et panorama de vidéos|[Oui](media-services-advanced-encoding-with-mes.md#trim_video)|OUI|
|Superpositions d’audio ou de vidéo|[Oui](media-services-advanced-encoding-with-mes.md#overlay)|[Oui](media-services-media-encoder-premium-workflow-multiplefilesinput.md#example-1--overlay-an-image-on-top-of-the-video)|
|Superpositions d’images|À partir d’images sources|À partir d’images et de textes sources|
|Plusieurs pistes de langue audio|Limité|[Oui](media-services-media-encoder-premium-workflow-multiplefilesinput.md#example-2--multiple-audio-language-encoding)|

## <a id="billing"></a>Compteur de facturation utilisé par chaque encodeur
| Nom du processeur multimédia | Tarification applicable | Notes |
| --- | --- | --- |
| **Media Encoder Standard** |ENCODEUR |Les tâches d’encodage seront facturées en fonction de la durée totale, en minutes, de tous les fichiers multimédias produits, au débit spécifié [ici][1], sous la colonne ENCODEUR. |
| **Media Encoder Premium Workflow** |ENCODEUR PREMIUM |Les tâches d’encodage seront facturées en fonction de la durée totale, en minutes, de tous les fichiers multimédias produits, au débit spécifié [ici][1], sous la colonne ENCODEUR PREMIUM. |

## <a name="input-containerfile-formats"></a>Formats de conteneurs/fichiers d’entrée
| Formats de conteneurs/fichiers d’entrée | Media Encoder Standard | Media Encoder Premium Workflow |
| --- | --- | --- |
| Adobe® Flash® F4V |OUI |OUI |
| MXF/SMPTE 377M |OUI |OUI |
| GXF |OUI |OUI |
| MPEG-2 Transport Streams |OUI |OUI |
| MPEG-2 Program Streams |OUI |OUI |
| MPEG-4/MP4 |OUI |OUI |
| Windows Media/ASF |OUI |OUI |
| AVI (8 bits/10 bits non compressé) |OUI |OUI |
| 3GPP/3GPP2 |OUI |Non  |
| Format de fichier Smooth Streaming (PIFF 1.3) |OUI |Non  |
| [Enregistrement vidéo numérique Microsoft (DVR-MS)](https://msdn.microsoft.com/library/windows/desktop/dd692984) |OUI |Non  |
| Matroska/WebM |OUI |Non  |
| QuickTime (.mov) |OUI |Non  |

## <a name="input-video-codecs"></a>Codecs vidéo d’entrée
| Codecs vidéo d’entrée | Media Encoder Standard | Media Encoder Premium Workflow |
| --- | --- | --- |
| AVC 8 bits/10 bits, jusqu'à 4:2:2, y compris AVCIntra |8 bits 4:2:0 et 4:2:2 |OUI |
| Avid DNxHD (dans MXF) |OUI |OUI |
| DVCPro/DVCProHD (dans MXF) |OUI |OUI |
| JPEG2000 |OUI |OUI |
| MPEG-2 (jusqu’au profil 422 et haut niveau ; y compris les variantes telles que XDCAM, XDCAM HD, XDCAM IMX, CableLabs® et D10) |Jusqu’à un profil de 422 |OUI |
| MPEG-1 |OUI |OUI |
| Windows Media Video/VC-1 |OUI |OUI |
| Canopus HQ/HQX |Non  |Non  |
| MPEG-4 partie 2 |OUI |Non  |
| [Theora](https://en.wikipedia.org/wiki/Theora) |OUI |Non  |
| Apple ProRes 422 |OUI |Non  |
| Apple ProRes 422 LT |OUI |Non  |
| Apple ProRes 422 HQ |OUI |Non  |
| Apple ProRes Proxy |OUI |Non  |
| Apple ProRes 4444 |OUI |Non  |
| Apple ProRes 4444 XQ |OUI |Non  |
| HEVC/H.265|Profil Main|Profil Main et Main 10|

## <a name="input-audio-codecs"></a>Codecs audio d’entrée
| Codecs audio d’entrée | Media Encoder Standard | Media Encoder Premium Workflow |
| --- | --- | --- |
| AES (SMPTE 331M et 302M, AES3-2003) |Non  |OUI |
| Dolby® E |Non  |OUI |
| Dolby® Digital (AC3) |Non  |OUI |
| Dolby® Digital Plus (E-AC3) |Non  |OUI |
| AAC (AAC-LC, AAC-HE et AAC-HEv2 ; jusqu’à 5.1) |OUI |OUI |
| MPEG Layer 2 |OUI |OUI |
| MP3 (MPEG-1 Audio Layer 3) |OUI |OUI |
| Windows Media Audio |OUI |OUI |
| WAV/PCM |OUI |OUI |
| [FLAC](https://en.wikipedia.org/wiki/FLAC)</a> |OUI |Non  |
| [Opus](https://en.wikipedia.org/wiki/Opus_\(audio_format\)) |OUI |Non  |
| [Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a> |OUI |Non  |

## <a name="output-containerfile-formats"></a>Formats de conteneurs/fichiers de sortie
| Formats de conteneurs/fichiers de sortie | Media Encoder Standard | Media Encoder Premium Workflow |
| --- | --- | --- |
| Adobe® Flash® F4V |Non  |OUI |
| MXF (OP1a, XDCAM et AS02) |Non  |OUI |
| DPP (y compris AS11) |Non  |OUI |
| GXF |Non  |OUI |
| MPEG-4/MP4 |OUI |OUI |
| MPEG-TS |OUI |OUI |
| Windows Media/ASF |Non  |OUI |
| AVI (8 bits/10 bits non compressé) |Non  |OUI |
| Format de fichier Smooth Streaming (PIFF 1.3) |Non  |OUI |

## <a name="output-video-codecs"></a>Codecs vidéo de sortie
| Codecs vidéo de sortie | Media Encoder Standard | Media Encoder Premium Workflow |
| --- | --- | --- |
| AVC (H.264 ; 8 bits ; jusqu'au profil High, niveau 5.2 ; Ultra HD 4K ; AVC Intra) |Uniquement 8 bits 4:2:0 |OUI |
| HEVC (H.265 ; 8 bits et 10 bits ;)  |Non  |OUI |
| Avid DNxHD (dans MXF) |Non  |OUI |
| MPEG-2 (jusqu’au profil 422 et haut niveau ; y compris les variantes telles que XDCAM, XDCAM HD, XDCAM IMX, CableLabs® et D10) |Non  |OUI |
| MPEG-1 |Non  |OUI |
| Windows Media Video/VC-1 |Non  |OUI |
| Création de miniatures JPEG |OUI |OUI |
| Création de miniatures PNG |OUI |OUI |
| Création de miniatures BMP |OUI |Non  |

## <a name="output-audio-codecs"></a>Codecs audio de sortie
| Codecs audio de sortie | Media Encoder Standard | Media Encoder Premium Workflow |
| --- | --- | --- |
| AES (SMPTE 331M et 302M, AES3-2003) |Non  |OUI |
| Dolby® Digital (AC3) |Non  |OUI |
| Dolby® Digital Plus (E-AC3) jusqu'à 7.1 |Non  |OUI |
| AAC (AAC-LC, AAC-HE et AAC-HEv2 ; jusqu’à 5.1) |OUI |OUI |
| MPEG Layer 2 |Non  |OUI |
| MP3 (MPEG-1 Audio Layer 3) |Non  |OUI |
| Windows Media Audio |Non  |OUI |

>[!NOTE]
>Si vous encodez en Dolby® Digital (AC3), la sortie peut uniquement être écrite dans un fichier MP4 ISO.

## <a name="media-services-learning-paths"></a>Parcours d’apprentissage de Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fournir des commentaires
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>Articles connexes
* [Exécution de tâches d’encodage avancées via la personnalisation des présélections Media Encoder Standard](media-services-custom-mes-presets-with-dotnet.md)
* [Quotas et limitations](media-services-quotas-and-limitations.md)

<!--Reference links in article-->
[1]: http://azure.microsoft.com/pricing/details/media-services/
