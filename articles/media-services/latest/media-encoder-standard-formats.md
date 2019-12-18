---
title: Codecs et formats de Media Encoder Standard - Azure
description: Cet article contient la liste des formats de fichier d’importation et d’exportation les plus courants que vous pouvez utiliser avec StandardEncoderPreset.
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
ms.reviewer: anilmur
ms.openlocfilehash: f1d4d4f4006702ebe0d057e56cf24a022e73b83e
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/06/2019
ms.locfileid: "74888068"
---
# <a name="standard-encoder-formats-and-codecs"></a>Codecs et formats de Media Encoder Standard

Cet article contient la liste des formats de fichier d’importation et d’exportation les plus courants que vous pouvez utiliser avec [StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset). Pour plus d’informations sur la façon de créer des préréglages personnalisés à l’aide de **StandardEncoderPreset**, consultez [Créer une transformation avec un préréglage personnalisé](customize-encoder-presets-how-to.md).

## <a name="input-containerfile-formats"></a>Formats de conteneurs/fichiers d’entrée

| Formats de fichier (extensions de fichier) | Pris en charge |
| --- | --- |
| FLV (avec les codecs H.264 et AAC) (.flv) |OUI |
| MXF    (.mxf) |OUI |
| GXF    (.gxf) |OUI |
| MPEG2-PS, MPEG2-TS, 3GP (.ts, .ps, .3gp, .3gpp, .mpg) |OUI |
| Windows Media Video (WMV)/ASF (.wmv, .asf) |OUI |
| AVI (8 bits/10 bits non compressé) (.avi) |OUI |
| MP4 (.mp4, .m4a, .m4v)/ISMV (.isma, .ismv) |OUI |
| [Microsoft Digital Video Recording (DVR-MS)](https://msdn.microsoft.com/library/windows/desktop/dd692984) (.dvr-ms) |OUI |
| Matroska/WebM (.mkv) |OUI |
| WAVE/WAV (.wav) |OUI |
| QuickTime (.mov) |OUI |

### <a name="audio-formats-in-input-containers"></a>Formats audio dans des conteneurs d’entrée

Media Encoder Standard prend en charge la transmission des formats audio suivants dans des conteneurs d’entrée :

* Fichiers MXF, GXF et QuickTime contenant des pistes audio avec des échantillons Interleaved Stereo ou 5.1

or

* Fichiers MXF, GXF et QuickTime où l’audio est transmis sous forme de pistes PCM distinctes, mais où le mappage de canaux (vers la stéréo ou 5.1) peut être déduit des métadonnées du fichier

## <a name="input-video-codecs"></a>Codecs vidéo d’entrée
| Codecs vidéo d’entrée | Pris en charge |
| --- | --- |
| AVC 8 bits/10 bits, jusqu'à 4:2:2, y compris AVCIntra |8 bits 4:2:0 et 4:2:2 |
| Avid DNxHD (dans MXF) |OUI |
| DVCPro/DVCProHD (dans MXF) |OUI |
| Vidéo numérique (dans les fichiers AVI) |OUI |
| JPEG 2000 |OUI |
| MPEG-2 (jusqu’au profil 422 et haut niveau ; y compris les variantes telles que XDCAM, XDCAM HD, XDCAM IMX, CableLabs® et D10) |Jusqu’à un profil de 422 |
| MPEG-1 |OUI |
| VC-1/WMV9 |OUI |
| Canopus HQ/HQX |Non |
| MPEG-4 partie 2 |OUI |
| [Theora](https://en.wikipedia.org/wiki/Theora) |OUI |
| YUV420 non compressé ou mezzanine |OUI |
| Apple ProRes 422 |OUI |
| Apple ProRes 422 LT |OUI |
| Apple ProRes 422 HQ |OUI |
| Apple ProRes Proxy |OUI |
| Apple ProRes 4444 |OUI |
| Apple ProRes 4444 XQ |OUI |
| HEVC/H.265| Profil Main|

## <a name="input-audio-codecs"></a>Codecs audio d’entrée
| Codecs audio d’entrée | Pris en charge |
| --- | --- |
| AAC (AAC-LC, AAC-HE et AAC-HEv2 ; jusqu’à 5.1) |OUI |
| MPEG Layer 2 |OUI |
| MP3 (MPEG-1 Audio Layer 3) |OUI |
| Windows Media Audio |OUI |
| WAV/PCM |OUI |
| [FLAC](https://en.wikipedia.org/wiki/FLAC)</a> |OUI |
| [Opus](https://go.microsoft.com/fwlink/?LinkId=822667) |OUI |
| [Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a> |OUI |
| AMR (adaptive multi-rate) |OUI |
| AES (SMPTE 331M et 302M, AES3-2003) |Non |
| Dolby® E |Non |
| Dolby® Digital (AC3) |Non |
| Dolby® Digital Plus (E-AC3) |Non |

## <a name="output-formats-and-codecs"></a>Codecs et formats de sortie
Le tableau suivant répertorie les codecs et les formats de fichiers pris en charge pour l'exportation.

| Format de fichier | Codec vidéo | Codec audio |
| --- | --- | --- |
| MP4 <br/><br/>(y compris les conteneurs MP4 à vitesses de transmission multiples) |H.264 (profils High, Main et Baseline) |AAC-LC, HE-AAC v1, HE-AAC v2 |
| MPEG2-TS |H.264 (profils High, Main et Baseline) |AAC-LC, HE-AAC v1, HE-AAC v2 |

## <a name="next-steps"></a>Étapes suivantes

[Créer une transformation avec un préréglage personnalisé](customize-encoder-presets-how-to.md)
