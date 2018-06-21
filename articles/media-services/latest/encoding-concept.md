---
title: Encodage dans le cloud avec Azure Media Services | Microsoft Docs
description: Cette rubrique décrit le processus d’encodage lors de l’utilisation d’Azure Media Services
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 04/21/2018
ms.author: juliako
ms.openlocfilehash: e1c7536c59b110ae3dd753ff5f4b01195f8dadca
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34659526"
---
# <a name="encoding-with-azure-media-services"></a>Encodage avec Azure Media Services

Azure Media Services vous permet d’encoder vos fichiers multimédias numériques haute définition dans des formats pouvant être lus sur un large choix de navigateurs et d’appareils. Par exemple, vous pouvez streamer votre contenu au format HLS ou MPEG DASH d’Apple. Media Services vous permet également d’analyser le contenu de vos fichiers vidéo ou audio. Cette rubrique vous explique comment encoder votre contenu avec Media Services v3.

Pour encoder avec Media Services v3, vous devez créer une transformation et un travail. Une transformation définit la recette à appliquer pour vos paramètres et sorties d’encodage, et la tâche est une instance de la recette. Pour plus d’informations, consultez [Transformations et travaux](transform-concept.md).

Lors de l’encodage avec Azure Media Services, vous utilisez des préréglages pour indiquer comment traiter les fichiers multimédias en entrée. Par exemple, vous pouvez spécifier la résolution vidéo et/ou le nombre de canaux audio souhaité dans le contenu encodé. 

Vous pouvez commencer rapidement à utiliser un des préréglages intégrés recommandés et basés sur les bonnes pratiques du secteur, ou vous pouvez choisir de créer un préréglage personnalisé pour les besoins de votre scénario ou de votre appareil. 

Pour plus d’informations sur l’encodeur, consultez [Spécification OpenAPI](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/preview/2018-03-30-preview). 

## <a name="built-in-presets"></a>Préréglages intégrés

Actuellement, Media Services prend en charge les préréglages d’encodage intégrés suivants :  

|**Nom du préréglage**|**Scénario**|**Détails**|
|---|---|---|
|**AudioAnalyzerPreset**|Analyse de contenu audio|Ce préréglage applique un ensemble prédéfini d’opérations d’analyse basée sur l’IA, notamment la transcription de la parole. Actuellement, le préréglage prend en charge le traitement du contenu avec une seule piste audio.<br/>Vous pouvez spécifier la langue de la charge utile audio de l’entrée en utilisant le format BCP-47 « étiquette langue-région » (par exemple « en-US »). La liste des langues prises en charge est : « en-US », « en-GB », « es-ES », « es-MX », « fr-FR », « it-IT », « ja-JP », « pt-BR », « zh-CN ».|
|**VideoAnalyzerPreset**|Analyse de contenu audio et vidéo|Extrait des insights (métadonnées enrichies) des contenus audio et vidéo, et génère en sortie un fichier au format JSON. Vous pouvez spécifier si vous voulez extraire seulement des insights audio lors du traitement d’un fichier vidéo. Pour plus d’informations, consultez [Analyser un contenu vidéo](analyze-videos-tutorial-with-api.md).|
|**BuiltInStandardEncoderPreset**|Streaming|Utilisé pour définir un préréglage intégré pour l’encodage de la vidéo en entrée avec l’encodeur standard. <br/>Les préréglages suivants sont actuellement pris en charge :<br/>**EncoderNamedPreset.AdaptiveStreaming** (recommandé). Pour plus d’informations, consultez [Génération automatique d’une échelle de débit binaire](autogen-bitrate-ladder.md).<br/>**EncoderNamedPreset.AACGoodQualityAudio** - produit un fichier MP4 unique contenant seulement le contenu audio stéréo encodé à 192 Kbits/s.<br/>**EncoderNamedPreset.H264MultipleBitrate1080** produit un ensemble de 8 fichiers MP4 alignés sur GOP, de 6 000 Kbits/s à 400 Kbits/s, et un contenu audio AAC stéréo. La résolution commence à 1 080p et descend à 360p.<br/>**EncoderNamedPreset.H264MultipleBitrate720p** produit un ensemble de 6 fichiers MP4 alignés sur GOP, de 3 400 Kbits/s à 400 Kbits/s, et un contenu audio AAC stéréo. La résolution commence à 720p et descend à 360p.<br/>**EncoderNamedPreset.H264MultipleBitrateSD** produit un ensemble de 5 fichiers MP4 alignés sur GOP, de 1 600 Kbits/s à 400 Kbits/s, et un contenu audio AAC stéréo. La résolution commence à 480p et descend à 360p.<br/><br/>Pour plus d’informations, consultez [Chargement, encodage et streaming de fichiers](stream-files-tutorial-with-api.md).|
|**StandardEncoderPreset**|Streaming|Décrit les paramètres utilisés lors de l’encodage de vidéo en entrée avec l’encodeur standard. <br/>Utilisez ce préréglage lors de la personnalisation des préréglages de transformation. Pour plus d’informations, consultez [Guide pratique pour personnaliser les paramètres de transformation prédéfinis](customize-encoder-presets-how-to.md).|

## <a name="custom-presets"></a>Préréglages personnalisés

Media Services prend entièrement en charge la personnalisation de toutes les valeurs dans les présélections afin de répondre à vos exigences et vos besoins spécifiques de codage. Vous utilisez le préréglage **StandardEncoderPreset** lors de la personnalisation des préréglages de transformation. Pour obtenir des explications détaillées et un exemple, consultez [Guide pratique pour personnaliser les préréglages de l’encodeur](customize-encoder-presets-how-to.md).

## <a name="scaling-encoding-in-v3"></a>Mise à l’échelle de l’encodage dans v3

Actuellement, les clients doivent utiliser le portail Azure ou les API d’AMS v2 pour définir des RU (comme décrit dans [Mise à l’échelle du traitement multimédia](../previous/media-services-scale-media-processing-overview.md). 

## <a name="next-steps"></a>Étapes suivantes

### <a name="tutorials"></a>Tutoriels

Les tutoriels suivants montrent comment encoder votre contenu avec Media Services :

* [Charger, encoder et diffuser en continu à l’aide d’Azure Media Services](stream-files-tutorial-with-api.md)
* [Analyser des vidéos avec Azure Media Services](analyze-videos-tutorial-with-api.md)

### <a name="code-samples"></a>Exemples de code

Les exemples de code suivant contiennent du code qui montre comment encoder avec Media Services :

* [.NET Core](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/tree/master/NETCore)
* [CLI 2.0](https://github.com/Azure/azure-docs-cli-python-samples/tree/master/media-services)

### <a name="sdks"></a>Kits SDK

Vous pouvez utiliser un des kits SDK Media Services v3 pris en charge suivants pour encoder votre contenu.

* [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
* [REST](https://docs.microsoft.com/rest/api/media/transforms)
* [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/mediaservices/management?view=azure-dotnet)
* [Java](https://docs.microsoft.com/java/api/overview/azure/mediaservices)
* [Python](https://docs.microsoft.com/python/api/overview/azure/media-services?view=azure-python)

