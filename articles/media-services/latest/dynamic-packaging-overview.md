---
title: Vue d’ensemble de l’empaquetage dynamique Azure Media Services | Microsoft Docs
description: Cette rubrique donne une vue d’ensemble de l’empaquetage dynamique dans Media Services.
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
ms.date: 03/25/2019
ms.author: juliako
ms.openlocfilehash: c9254c8dd629230a549dd95aba9afbd932746007
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2019
ms.locfileid: "58886452"
---
# <a name="dynamic-packaging"></a>Empaquetage dynamique

Vous pouvez utiliser Microsoft Azure Media Services pour distribuer de nombreux formats de fichiers sources multimédias, formats de streaming de contenu multimédia et formats de protection de contenu à diverses technologies clientes (par exemple, iOS et XBOX). Ces clients comprennent différents protocoles. Par exemple, iOS nécessite un format HTTP Live Streaming (HLS), tandis que Xbox nécessite le format Smooth Streaming. Si vous avez un ensemble de débit adaptatif (multidébit) MP4 les fichiers (ISO Base Media 14496-12) ou un ensemble de fichiers Smooth Streaming à débit adaptatif à distribuer aux clients qui comprennent HLS, MPEG DASH ou Smooth Streaming, vous pouvez tirer parti de dynamique Mise en package. L’empaquetage est indépendant de la résolution vidéo, SD/HD/UHD - 4K sont pris en charge.

[Points de terminaison de diffusion en continu](streaming-endpoint-concept.md) est le service d’empaquetage dynamique dans Media Services est utilisé pour distribuer du contenu multimédia aux joueurs de client. L’empaquetage dynamique est une fonctionnalité fournie en standard sur tous les **les points de terminaison de diffusion en continu** (Standard ou Premium). 

Pour tirer parti des **empaquetage dynamique**, vous devez avoir un **Asset** avec un ensemble de fichiers MP4 à débit adaptatif et de diffusion en continu des fichiers de configuration requis par l’empaquetage dynamique de Media Services. L’une des manières d’obtenir les fichiers consiste à encoder votre fichier mezzanine (source) avec Media Services. Pour rendre vidéos dans l’élément multimédia encodé aux clients pour la lecture, vous devez créer un **localisateur de diffusion en continu** et générer l’URL de diffusion. Ensuite, en fonction du format spécifié dans le manifeste du client streaming (HLS, DASH ou Smooth Streaming), vous recevez le flux du protocole que vous avez choisi.

Par conséquent, il vous suffit de stocker et de payer les fichiers dans un seul format de stockage. Le service Media Services se charge de créer et de fournir la réponse appropriée en fonction des demandes des clients. 

Dans Media Services, l’empaquetage dynamique est utilisé si vous diffusez en continu en direct ou à la demande. 

## <a name="common-on-demand-workflow"></a>Flux de travail courant à la demande

Voici un flux de travail de streaming où l’empaquetage dynamique est utilisé Media Services courants.

1. Téléchargez un fichier d'entrée (appelé fichier mezzanine). Par exemple, MP4, MOV ou MXF (pour obtenir la liste des formats pris en charge, consultez [Formats pris en charge par Media Encoder Standard](media-encoder-standard-formats.md).
2. Encodez votre fichier mezzanine en ensembles de fichiers MP4 à vitesse de transmission adaptative H.264.
3. Publier l’élément multimédia qui contient l’ensemble au débit adaptatif MP4. Vous publiez en créant un **localisateur de diffusion en continu**.
4. Création d’URL qui ciblent différents formats (HLS, Dash et Smooth Streaming). Le **le point de terminaison de diffusion en continu** se chargera de servir les requêtes pour ces différents formats et le manifeste approprié.

Le diagramme suivant illustre la diffusion en continu à la demande avec les flux d’empaquetage dynamique.

![Empaquetage dynamique](./media/dynamic-packaging-overview/media-services-dynamic-packaging.svg)

### <a name="encode-to-adaptive-bitrate-mp4s"></a>Encodage à débit adaptatif MP4s

Pour plus d’informations sur [comment encoder une vidéo avec Media Services](encoding-concept.md), consultez les exemples suivants :

* [Encoder à partir d’une URL HTTPS à l’aide de paramètres prédéfinis intégrés](job-input-from-http-how-to.md)
* [Encoder un fichier local à l’aide de paramètres prédéfinis intégrés](job-input-from-local-file-how-to.md)
* [Créer un paramètre prédéfini pour cibler vos exigences de scénario ou un périphérique spécifiques](customize-encoder-presets-how-to.md)

Pour obtenir la liste des codecs et formats Media Encoder Standard, consultez [formats et codecs](media-encoder-standard-formats.md)

## <a name="common-live-streaming-workflow"></a>Workflow courantes de diffusion en continu en direct

Voici les étapes d’un workflow de streaming en direct :

1. Créez un [événement en temps réel](live-events-outputs-concept.md).
1. Obtenir les URL de réception et configurez votre encodeur local pour utiliser l’URL pour envoyer la flux de contribution.
1. Récupérez l’URL d’aperçu et utilisez-la pour vérifier que l’entrée de l’encodeur est bien reçue.
1. Créer un nouveau **Asset**.
1. Créez un objet **LiveOutput** et utilisez le nom de l’objet Asset que vous venez de créer.<br/>La **sortie en direct** archive le flux dans l’**actif multimédia**.
1. Créez un **localisateur de streaming**  avec les types intégrés de la **stratégie de streaming**.<br/>Pour chiffrer le contenu, voir [Vue d’ensemble de la protection du contenu](content-protection-overview.md).
1. Listez les chemins d'accès dans le **Localisateur de streaming** pour récupérer les URL à utiliser.
1. Récupérez le nom d’hôte du **Point de terminaison de streaming** à partir duquel vous souhaitez effectuer le streaming.
1. Création d’URL qui ciblent différents formats (HLS, Dash et Smooth Streaming). Le **le point de terminaison de diffusion en continu** se chargera de servir les requêtes pour ces différents formats et le manifeste approprié.

Un événement en direct peut prendre l’une des deux types : encodage direct et en direct. Pour plus d’informations sur la diffusion en continu dans Media Services v3, consultez [en direct de diffusion en continu de la vue d’ensemble](live-streaming-overview.md).

Le diagramme suivant illustre la diffusion en continu en direct avec le flux d’empaquetage dynamique.

![transmission directe](./media/live-streaming/pass-through.svg)

## <a name="delivery-protocols"></a>Protocoles de remise

|Protocole|Exemples|
|---|---|
|HLS V4 |`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-aapl)`|
|HLS V3 |`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-aapl-v3)`|
|HLS CMAF| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-cmaf)`|
|MPEG DASH CSF| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=mpd-time-csf)` |
|MPEG DASH CMAF|`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=mpd-time-cmaf)` |
|Smooth Streaming| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest`|

## <a name="video-codecs-supported-by-dynamic-packaging"></a>Codecs vidéo pris en charge par l’empaquetage dynamique

Empaquetage dynamique prend en charge les fichiers MP4, qui contiennent la vidéo encodée avec [H.264](https://en.m.wikipedia.org/wiki/H.264/MPEG-4_AVC) (MPEG-4 AVC ou AVC1), [H.265](https://en.m.wikipedia.org/wiki/High_Efficiency_Video_Coding) (HEVC, hev1 ou hvc1).

## <a name="audio-codecs-supported-by-dynamic-packaging"></a>Codecs audio pris en charge par l’empaquetage dynamique

Empaquetage dynamique prend en charge les fichiers MP4, qui contiennent des données audio encodée avec [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding) (AAC-LC, HE-AAC v1, HE-AAC v2), [Dolby Digital Plus](https://en.wikipedia.org/wiki/Dolby_Digital_Plus)(Enhanced AC-3 ou E-AC3), Dolby Atmos, ou [DTS](https://en.wikipedia.org/wiki/DTS_%28sound_system%29) (DTS Express, LBR de DTS, DTS HD, HD DTS sans perte). La diffusion en continu du contenu de Dolby Atmos est prise en charge des normes comme protocole de MPEG-DASH avec le Format de diffusion en continu courants (CSF) ou Format de Application commune Media (CMAF) au format MP4 fragmenté et par le biais de HTTP Live Streaming (HLS) avec CMAF.

> [!NOTE]
> L’empaquetage dynamique ne prend pas en charge les fichiers qui contiennent des données audio [Dolby Digital](https://en.wikipedia.org/wiki/Dolby_Digital) (AC3) (il s’agit d’un codec hérité).

## <a name="dynamic-encryption"></a>Chiffrement dynamique

**Chiffrement dynamique** vous permet de chiffrer dynamiquement votre contenu en direct ou à la demande avec AES-128 ou l’un des systèmes de gestion (DRM) trois droits numériques principales : Microsoft PlayReady, Google Widevine et Apple FairPlay. Media Services fournit également un service de distribution de clés AES et licences (PlayReady, Widevine et FairPlay) DRM aux clients autorisés. Pour plus d’informations, consultez [chiffrement dynamique](content-protection-overview.md).

## <a name="manifests"></a>Manifestes 
 
Media Services prend en charge HLS, MPEG DASH, protocoles de diffusion en continu lisse. Dans le cadre de **empaquetage dynamique**, les manifestes de client de diffusion en continu (HLS Master Playlist DASH MPD Media Presentation Description () et Smooth Streaming) sont générées dynamiquement selon le sélecteur de format dans l’URL. Consultez les protocoles de remise dans [cette section](#delivery-protocols). 

Un fichier manifest inclut la diffusion en continu des métadonnées telles que : effectuer le suivi de type (audio, vidéo ou texte), effectuer le suivi du nom, heure de début et de fin, débit binaire (qualités), langues de piste, fenêtre de présentation (fenêtre glissante de durée fixe), codec vidéo (FourCC). Il indique également au lecteur de récupérer le fragment suivant en fournissant des informations sur les fragments vidéo pouvant être lus suivants disponibles et leur emplacement. Les fragments (ou segments) correspondent aux « blocs » réels d'un contenu vidéo.

### <a name="hls-master-playlist"></a>Liste de lecture HLS Master

Voici un exemple de fichier manifeste HLS : 

```
#EXTM3U
#EXT-X-VERSION:4
#EXT-X-MEDIA:TYPE=AUDIO,GROUP-ID="audio",NAME="aac_eng_2_128041_2_1",LANGUAGE="eng",DEFAULT=YES,AUTOSELECT=YES,URI="QualityLevels(128041)/Manifest(aac_eng_2_128041_2_1,format=m3u8-aapl)"
#EXT-X-STREAM-INF:BANDWIDTH=536608,RESOLUTION=320x180,CODECS="avc1.64000d,mp4a.40.2",AUDIO="audio"
QualityLevels(381048)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=536608,RESOLUTION=320x180,CODECS="avc1.64000d",URI="QualityLevels(381048)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=884544,RESOLUTION=480x270,CODECS="avc1.640015,mp4a.40.2",AUDIO="audio"
QualityLevels(721495)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=884544,RESOLUTION=480x270,CODECS="avc1.640015",URI="QualityLevels(721495)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=1327398,RESOLUTION=640x360,CODECS="avc1.64001e,mp4a.40.2",AUDIO="audio"
QualityLevels(1154816)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=1327398,RESOLUTION=640x360,CODECS="avc1.64001e",URI="QualityLevels(1154816)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=2413312,RESOLUTION=960x540,CODECS="avc1.64001f,mp4a.40.2",AUDIO="audio"
QualityLevels(2217354)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=2413312,RESOLUTION=960x540,CODECS="avc1.64001f",URI="QualityLevels(2217354)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=3805760,RESOLUTION=1280x720,CODECS="avc1.640020,mp4a.40.2",AUDIO="audio"
QualityLevels(3579827)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=3805760,RESOLUTION=1280x720,CODECS="avc1.640020",URI="QualityLevels(3579827)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=139017,CODECS="mp4a.40.2",AUDIO="audio"
QualityLevels(128041)/Manifest(aac_eng_2_128041_2_1,format=m3u8-aapl)
```

### <a name="dash-media-presentation-description-mpd"></a>Description de la présentation multimédia DASH MPD)

Voici un exemple de manifeste DASH :

```xml
<?xml version="1.0" encoding="UTF-8"?>
<MPD xmlns="urn:mpeg:dash:schema:mpd:2011" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" profiles="urn:mpeg:dash:profile:isoff-live:2011" type="static" mediaPresentationDuration="PT1M10.315S" minBufferTime="PT7S">
   <Period>
      <AdaptationSet id="1" group="5" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="audio" mimeType="audio/mp4" codecs="mp4a.40.2" lang="en">
         <SegmentTemplate timescale="10000000" media="QualityLevels($Bandwidth$)/Fragments(aac_eng_2_128041_2_1=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(aac_eng_2_128041_2_1=i,format=mpd-time-csf)">
            <SegmentTimeline>
               <S d="60160000" r="10" />
               <S d="41386666" />
            </SegmentTimeline>
         </SegmentTemplate>
         <Representation id="5_A_aac_eng_2_128041_2_1_1" bandwidth="128041" audioSamplingRate="48000" />
      </AdaptationSet>
      <AdaptationSet id="2" group="1" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="video" mimeType="video/mp4" codecs="avc1.640020" maxWidth="1280" maxHeight="720" startWithSAP="1">
         <SegmentTemplate timescale="10000000" media="QualityLevels($Bandwidth$)/Fragments(video=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(video=i,format=mpd-time-csf)">
            <SegmentTimeline>
               <S d="60060000" r="10" />
               <S d="42375666" />
            </SegmentTimeline>
         </SegmentTemplate>
         <Representation id="1_V_video_1" bandwidth="3579827" width="1280" height="720" />
         <Representation id="1_V_video_2" bandwidth="2217354" codecs="avc1.64001F" width="960" height="540" />
         <Representation id="1_V_video_3" bandwidth="1154816" codecs="avc1.64001E" width="640" height="360" />
         <Representation id="1_V_video_4" bandwidth="721495" codecs="avc1.640015" width="480" height="270" />
         <Representation id="1_V_video_5" bandwidth="381048" codecs="avc1.64000D" width="320" height="180" />
      </AdaptationSet>
   </Period>
</MPD>
```
### <a name="smooth-streaming"></a>Smooth Streaming

Voici un exemple de manifeste Smooth Streaming :

```xml
<?xml version="1.0" encoding="UTF-8"?>
<SmoothStreamingMedia MajorVersion="2" MinorVersion="2" Duration="703146666" TimeScale="10000000">
   <StreamIndex Chunks="12" Type="audio" Url="QualityLevels({bitrate})/Fragments(aac_eng_2_128041_2_1={start time})" QualityLevels="1" Language="eng" Name="aac_eng_2_128041_2_1">
      <QualityLevel AudioTag="255" Index="0" BitsPerSample="16" Bitrate="128041" FourCC="AACL" CodecPrivateData="1190" Channels="2" PacketSize="4" SamplingRate="48000" />
      <c t="0" d="60160000" r="11" />
      <c d="41386666" />
   </StreamIndex>
   <StreamIndex Chunks="12" Type="video" Url="QualityLevels({bitrate})/Fragments(video={start time})" QualityLevels="5">
      <QualityLevel Index="0" Bitrate="3579827" FourCC="H264" MaxWidth="1280" MaxHeight="720" CodecPrivateData="0000000167640020ACD9405005BB011000003E90000EA600F18319600000000168EBECB22C" />
      <QualityLevel Index="1" Bitrate="2217354" FourCC="H264" MaxWidth="960" MaxHeight="540" CodecPrivateData="000000016764001FACD940F0117EF01100000303E90000EA600F1831960000000168EBECB22C" />
      <QualityLevel Index="2" Bitrate="1154816" FourCC="H264" MaxWidth="640" MaxHeight="360" CodecPrivateData="000000016764001EACD940A02FF9701100000303E90000EA600F162D960000000168EBECB22C" />
      <QualityLevel Index="3" Bitrate="721495" FourCC="H264" MaxWidth="480" MaxHeight="270" CodecPrivateData="0000000167640015ACD941E08FEB011000003E90000EA600F162D9600000000168EBECB22C" />
      <QualityLevel Index="4" Bitrate="381048" FourCC="H264" MaxWidth="320" MaxHeight="180" CodecPrivateData="000000016764000DACD941419F9F011000003E90000EA600F14299600000000168EBECB22C" />
      <c t="0" d="60060000" r="11" />
      <c d="42375666" />
   </StreamIndex>
</SmoothStreamingMedia>
```

## <a name="dynamic-manifest"></a>Manifeste dynamique

Le filtrage dynamique est utilisé pour contrôler le nombre de pistes, des formats, des débits binaires et des fenêtres de temps de présentation qui sont envoyés aux joueurs. Pour plus d’informations, consultez [filtres et manifestes dynamiques](filters-dynamic-manifest-overview.md).

> [!NOTE]
> Actuellement, vous ne pouvez pas utiliser le portail Azure pour gérer des ressources v3. Utilisez l’[API REST](https://aka.ms/ams-v3-rest-ref), l’interface [CLI](https://aka.ms/ams-v3-cli-ref) ou l’un des kits [SDK](developers-guide.md) pris en charge.

## <a name="next-steps"></a>Étapes suivantes

[Chargez, codez, vidéos stream](stream-files-tutorial-with-api.md)

