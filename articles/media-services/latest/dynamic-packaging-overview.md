---
title: Vue d’ensemble de l’empaquetage dynamique Azure Media Services | Microsoft Docs
description: Cet article donne une vue d’ensemble de l’empaquetage dynamique dans Azure Media Services.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 07/29/2019
ms.author: juliako
ms.openlocfilehash: 5979e34e7c186a0484c8db2d432a3c57a5ed1d15
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/31/2019
ms.locfileid: "68679154"
---
# <a name="dynamic-packaging"></a>l’empaquetage dynamique

Vous pouvez utiliser Microsoft Azure Media Services pour distribuer de nombreux formats de fichiers sources multimédias, formats de streaming de contenu multimédia et formats de protection de contenu à diverses technologies clientes (par exemple, iOS et XBOX). Ces clients comprennent différents protocoles. Par exemple, iOS nécessite un format HTTP Live Streaming (HLS), tandis que Xbox nécessite le format Smooth Streaming. Si vous voulez transmettre un ensemble de fichiers MP4 (ISO Base Media 14496-12) ou de fichiers Smooth Streaming à débit adaptatif à des clients qui utilisent le format HLS, MPEG DASH ou Smooth Streaming, vous pouvez tirer profit de l’*empaquetage dynamique*. L’empaquetage est indépendant de la résolution vidéo, SD/HD/UHD-4K sont pris en charge.

Dans Media Services, un [point de terminaison de streaming](streaming-endpoint-concept.md) représente un empaquetage dynamique (juste-à-temps) et un service d’origine qui permet de transmettre votre contenu en direct et à la demande directement à une application de lecteur cliente, à l’aide de l’un des protocoles de streaming multimédia courants (HLS ou DASH). L’empaquetage dynamique est une fonctionnalité standard sur tous les **points de terminaison de streaming** (Standard ou Premium). 

Pour tirer parti de l’empaquetage dynamique, vous devez avoir un [élément multimédia](assets-concept.md) avec un ensemble de fichiers MP4 à débit adaptatif et des fichiers de configuration de diffusion en continu (.ism, .ismc, .mpi, etc.). L’une des manières d’obtenir les fichiers consiste à encoder votre fichier mezzanine (source) avec Media Services. Pour rendre les vidéos dans l’élément multimédia encodé disponibles en lecture pour les clients, vous devez créer un [localisateur de streaming](streaming-locators-concept.md) et générer des URL de diffusion en continu. Ensuite, en fonction du format spécifié dans le manifeste du client de streaming (HLS, DASH ou Smooth Streaming), vous recevez le flux dans le protocole que vous avez choisi.

Par conséquent, il vous suffit de stocker et de payer les fichiers dans un seul format de stockage. Le service Media Services se charge de créer et de fournir la réponse appropriée en fonction des demandes des clients. 

Dans Media Services, l’empaquetage dynamique est utilisé que le streaming soit effectué en direct ou à la demande. 

> [!NOTE]
> Actuellement, vous ne pouvez pas utiliser le portail Azure pour gérer des ressources v3. Utilisez l’[API REST](https://aka.ms/ams-v3-rest-ref), l’interface [CLI](https://aka.ms/ams-v3-cli-ref) ou l’un des kits [SDK](media-services-apis-overview.md#sdks) pris en charge.

## <a name="on-demand-streaming-workflow"></a>Workflow du streaming à la demande

Voici un workflow courant pour le streaming à la demande Media Services avec l’empaquetage dynamique :

1. Chargez un fichier source ou d’entrée (appelé fichier *mezzanine*). Les fichiers MP4, MOV ou MXF en sont des exemples. 
1. Encodez votre fichier mezzanine en ensembles de fichiers MP4 à vitesse de transmission adaptative H.264. 
1. Publier l’élément multimédia de sortie qui contient le fichier au débit adaptatif MP4 défini. Vous publiez en créant un localisateur de streaming.
1. Générez des URL qui ciblent différents formats (HLS, MPEG-DASH et Smooth Streaming). Le point de terminaison de streaming s’occupe de distribuer le manifeste approprié et les demandes pour les différents formats.

Ce diagramme illustre le workflow du streaming à la demande avec l’empaquetage dynamique :

![Diagramme d’un workflow de streaming à la demande avec l’empaquetage dynamique](./media/dynamic-packaging-overview/media-services-dynamic-packaging.png)

### <a name="encoding-to-adaptive-bitrate-mp4s"></a>Encodage en fichiers MP4 à débit adaptatif

Les articles suivants donnent des exemples de l’[encodage d’une vidéo avec Media Services](encoding-concept.md) :

* [Encoder à partir d’une URL HTTPS à l’aide de préréglages intégrés](job-input-from-http-how-to.md)
* [Encoder un fichier local à l’aide de préréglages intégrés](job-input-from-local-file-how-to.md)
* [Créer un préréglage personnalisé pour les besoins de votre scénario ou votre appareil](customize-encoder-presets-how-to.md)

Consultez la liste des [codecs et formats](media-encoder-standard-formats.md) de Media Encoder Standard.

## <a name="live-streaming-workflow"></a>Workflow de streaming en direct

Un événement en direct peut être de deux types : transfert direct ou encodage en temps réel. 

Voici un workflow courant pour le streaming en direct avec l’empaquetage dynamique :

1. Créez un [événement en direct](live-events-outputs-concept.md).
1. Récupérez l’URL d’ingestion et configurez votre encodeur local afin qu’il utilise cette URL pour envoyer le flux de contribution.
1. Récupérez l’URL d’aperçu et utilisez-la pour vérifier que l’entrée de l’encodeur est bien reçue.
1. Créez un actif multimédia.
1. Créez une sortie en direct et utilisez le nom de l’actif multimédia que vous venez de créer.<br />La sortie en direct archive le flux dans l’actif multimédia.
1. Créez un localisateur de streaming avec les types intégrés de la stratégie de streaming.<br />Pour chiffrer le contenu, voir [Vue d’ensemble de la protection du contenu](content-protection-overview.md).
1. Listez les chemins dans le localisateur de streaming pour obtenir les URL à utiliser.
1. Récupérez le nom d’hôte du point de terminaison de streaming à partir duquel vous souhaitez effectuer le streaming.
1. Générez des URL qui ciblent différents formats (HLS, MPEG-DASH et Smooth Streaming). Le point de terminaison de streaming s’occupe de distribuer le manifeste approprié et les demandes pour les différents formats.

Ce diagramme illustre le workflow du streaming en direct avec l’empaquetage dynamique :

![Diagramme d’un workflow pour l’encodage par transfert direct avec l’empaquetage dynamique](./media/live-streaming/pass-through.svg)

Pour plus d’informations sur le streaming en direct dans Media Services v3, consultez [Vue d’ensemble du streaming en direct](live-streaming-overview.md).

## <a name="delivery-protocols"></a>Protocoles de remise

Vous pouvez utiliser ces protocoles de remise pour votre contenu dans l’empaquetage dynamique Media Services :

|Protocole|Exemples|
|---|---|
|HLS V4 |`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-aapl)`|
|HLS V3 |`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-aapl-v3)`|
|HLS CMAF| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-cmaf)`|
|MPEG-DASH CSF| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=mpd-time-csf)` |
|MPEG-DASH CMAF|`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=mpd-time-cmaf)` |
|Smooth Streaming| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest`|

## <a name="delivery-codecs-support"></a>Prise en charge des codecs de diffusion 

### <a name="video-codecs"></a>Codecs vidéo

L’empaquetage dynamique prend en charge les codecs vidéo suivants :
* Fichiers MP4, qui contiennent une vidéo encodée avec [H.264](https://en.m.wikipedia.org/wiki/H.264/MPEG-4_AVC) (MPEG-4 AVC ou AVC1) ou [H.265](https://en.m.wikipedia.org/wiki/High_Efficiency_Video_Coding) (HEVC, hev1 ou hvc1).

### <a name="audio-codecs"></a>Codecs audio

L’empaquetage dynamique prend en charge les protocoles audio décrits ci-dessous :

* Fichiers MP4
* Plusieurs pistes audio

L’empaquetage dynamique ne prend pas en charge les fichiers qui contiennent des données audio [Dolby Digital](https://en.wikipedia.org/wiki/Dolby_Digital) (AC3) (il s’agit d’un codec hérité).

#### <a name="mp4-files"></a>Fichiers MP4

L’empaquetage dynamique prend en charge les fichiers MP4, qui contiennent des données audio encodées avec les protocoles suivants : 

* [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding) (AAC-LC, HE-AAC v1 ou HE-AAC v2)
* [Dolby Digital Plus](https://en.wikipedia.org/wiki/Dolby_Digital_Plus) (Enhanced AC-3 ou E-AC3)
* Dolby Atmos<br />
   Le streaming de contenu Dolby Atmos est pris en charge pour les normes telles que le protocole MPEG-DASH avec MP4 fragmenté au format CSF (Common Streaming Format) ou CMAF (Common Media Application Format), et via HLS (HTTP Live Streaming) avec CMAF.

* [DTS](https://en.wikipedia.org/wiki/DTS_%28sound_system%29)<br />
   Les codecs DTS pris en charge par les formats d’empaquetage DASH-CSF, DASH-CMAF, HLS-M2TS et HLS-CMAF sont :  

    * DTS Digital Surround (dtsc)
    * DTS-HD High Resolution et DTS-HD Master Audio (dtsh)
    * DTS Express (dtse)
    * DTS-HD Lossless (pas de cœur) (dtsl)

#### <a name="multiple-audio-tracks"></a>Plusieurs pistes audio

L’empaquetage dynamique prend en charge plusieurs pistes audio pour la sortie HLS (version 4 ou ultérieure) pour le streaming des actifs multimédias qui ont plusieurs pistes audio avec plusieurs langues et codecs.

## <a name="manifests"></a>Manifestes 
 
Dans l’empaquetage dynamique Media Services, les manifestes du client de streaming pour HLS, MPEG-DASH et Smooth Streaming sont générés dynamiquement selon le sélecteur de format dans l’URL. Pour plus d’informations, consultez [Protocoles de remise](#delivery-protocols). 

Un fichier manifeste inclut des métadonnées de streaming telles que les suivantes : type de piste (audio, vidéo ou texte), nom de piste, heure de début et de fin, débit (qualités), langues de piste, fenêtre de présentation (fenêtre glissante de durée fixe) et codec vidéo (FourCC). Il indique également au lecteur de récupérer le fragment suivant en fournissant des informations sur les fragments vidéo pouvant être lus suivants disponibles et leur emplacement. Les fragments (ou segments) correspondent aux « blocs » réels d’un contenu vidéo.

### <a name="examples"></a>Exemples

#### <a name="hls"></a>HLS

Voici un exemple d’un fichier manifeste HLS, également appelé liste de lectures principale HLS : 

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

#### <a name="mpeg-dash"></a>MPEG-DASH

Voici un exemple d’un fichier manifeste MPEG-DASH, également appelé fichier MPEG-DASH MPD (Media Presentation Description) :

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
#### <a name="smooth-streaming"></a>Smooth Streaming

Voici un exemple d’un fichier manifeste Smooth Streaming :

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

### <a name="naming-of-tracks-in-the-manifest"></a>Dénomination des pistes dans le manifeste

Si un nom de piste audio est spécifié dans le fichier .ism, Media Services ajoute un élément `Label` dans un `AdaptationSet` pour spécifier les informations de texture pour la piste audio spécifique. Voici un exemple de manifeste DASH de sortie :

```xml
<AdaptationSet codecs="mp4a.40.2" contentType="audio" lang="en" mimeType="audio/mp4" subsegmentAlignment="true" subsegmentStartsWithSAP="1">
  <Label>audio_track_name</Label>
  <Role schemeIdUri="urn:mpeg:dash:role:2011" value="main"/>
  <Representation audioSamplingRate="48000" bandwidth="131152" id="German_Forest_Short_Poem_english-en-68s-2-lc-128000bps_seg">
    <BaseURL>German_Forest_Short_Poem_english-en-68s-2-lc-128000bps_seg.mp4</BaseURL>
  </Representation>
</AdaptationSet>
```

Le lecteur peut utiliser l'élément `Label` pour l’afficher sur son interface utilisateur.

### <a name="signaling-audio-description-tracks"></a>Signalisation des pistes de description audio

Un client peut annoter une piste audio en tant description audio dans le manifeste. Pour ce faire, il doit ajouter les paramètres « accessibility » et « Role » au fichier .ism. Media Services reconnaît la description audio si une piste audio a le paramètre « accessibility » avec la valeur « description » et le paramètre « role » avec la valeur « alternate ». Si Media Services détecte la description audio dans le fichier .ism, les informations de description audio sont transmises au manifeste du client en tant qu’attributs `Accessibility="description"` et `Role="alternate"` dans l’élément `StreamIndex`.

Si la combinaison « accessibility » = « description » et « role » = « alternate » est définie dans le fichier .ism, le manifeste DASH et le manifeste Smooth transmettent les valeurs définies dans les paramètres « accessibility » et « role ». Il incombe au client de définir ces deux valeurs correctement et de marquer une piste audio en tant que description audio. Selon la spécification DASH, la combinaison « accessibility » = « description » et « role » = « alternate » signifie qu'une piste audio est une description audio.

Pour TLS v7 et versions ultérieures (`format=m3u8-cmaf`), sa playlist transmet `CHARACTERISTICS="public.accessibility.describes-video"` uniquement lorsque la combinaison « accessibility » = « description » et « role » = « alternate » est définie dans le fichier .ism. 

## <a name="dynamic-manifest"></a>Manifeste dynamique

Pour contrôler le nombre de pistes, les formats, les débits et les fenêtres de temps de présentation qui sont envoyés aux lecteurs, vous pouvez utiliser un filtrage dynamique avec l’empaquetage dynamique Media Services. Pour plus d’informations, consultez [Manifestes de filtrage préalable avec l’empaquetage dynamique](filters-dynamic-manifest-overview.md).

## <a name="dynamic-encryption"></a>Chiffrement dynamique

Le *chiffrement dynamique* permet de chiffrer dynamiquement votre contenu en direct ou à la demande avec AES-128 ou l’un des trois systèmes principaux de gestion des droits numériques (DRM) : Microsoft PlayReady, Google Widevine et Apple FairPlay. Media Services fournit également un service de distribution de clés AES et de licences DRM aux clients autorisés. Pour plus d’informations, consultez [Chiffrement dynamique](content-protection-overview.md).

## <a name="more-information"></a>Plus d’informations

Consultez [Communauté Azure Media Services](media-services-community.md) pour découvrir les différentes façons dont vous pouvez poser des questions, faire des commentaires et obtenir des mises à jour sur Media Services.

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment [charger, encoder et diffuser des vidéos en continu](stream-files-tutorial-with-api.md).

