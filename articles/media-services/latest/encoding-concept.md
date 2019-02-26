---
title: Encodage dans le cloud avec Azure Media Services - Azure | Microsoft Docs
description: Cette rubrique décrit le processus d’encodage lors de l’utilisation d’Azure Media Services
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 02/17/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 52e7fdf6de25300d4f78ee9822aca4ad83f646e9
ms.sourcegitcommit: 4bf542eeb2dcdf60dcdccb331e0a336a39ce7ab3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/19/2019
ms.locfileid: "56408423"
---
# <a name="encoding-with-media-services"></a>Encodage avec Media Services

Azure Media Services vous permet d’encoder vos fichiers multimédias numériques haute définition dans des formats pouvant être lus sur un large choix de navigateurs et d’appareils. Par exemple, vous pouvez streamer votre contenu au format HLS ou MPEG DASH d’Apple. Cette rubrique vous explique comment encoder votre contenu avec Media Services v3.

Pour encoder avec Media Services v3, vous devez créer une [transformation](https://docs.microsoft.com/rest/api/media/transforms) et un [travail](https://docs.microsoft.com/rest/api/media/jobs). Une transformation définit la recette à appliquer pour vos paramètres et sorties d’encodage, et la tâche est une instance de la recette. Pour plus d’informations, consultez [Transformations et travaux](transforms-jobs-concept.md).

Lors de l’encodage avec Media Services, vous utilisez des préréglages pour indiquer comment traiter les fichiers multimédias en entrée. Par exemple, vous pouvez spécifier la résolution vidéo et/ou le nombre de canaux audio souhaité dans le contenu encodé. 

Vous pouvez commencer rapidement à utiliser un des préréglages intégrés recommandés et basés sur les bonnes pratiques du secteur, ou vous pouvez choisir de créer un préréglage personnalisé pour les besoins de votre scénario ou de votre appareil. Pour plus d’informations, consultez [Encoder avec une transformation personnalisée](customize-encoder-presets-how-to.md). 

À partir de janvier 2019, en cas d'encodage avec Media Encoder Standard pour produire des fichiers MP4, un nouveau fichier .mpi est généré et ajouté à la ressource de sortie. Ce fichier MPI est destiné à améliorer les performances pour les scénarios d’[empaquetage dynamique](dynamic-packaging-overview.md) et de diffusion en continu.

> [!NOTE]
> Vous ne devez ni modifier ni supprimer le fichier MPI, ni dépendre de l'existence (ou non) d'un tel fichier dans votre service.

## <a name="built-in-presets"></a>Préréglages intégrés

Actuellement, Media Services prend en charge les préréglages d’encodage intégrés suivants :  

### <a name="builtinstandardencoderpreset-preset"></a>Préréglage BuiltInStandardEncoderPreset

[BuiltInStandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) est utilisé pour définir un préréglage intégré pour l’encodage de la vidéo en entrée avec l’encodeur standard. 

Les préréglages suivants sont actuellement pris en charge :

- **EncoderNamedPreset.AdaptiveStreaming** (recommandé). Pour plus d’informations, consultez [Génération automatique d’une échelle de débit binaire](autogen-bitrate-ladder.md).
- **EncoderNamedPreset.AACGoodQualityAudio** - produit un fichier MP4 unique contenant seulement le contenu audio stéréo encodé à 192 Kbits/s.
- **EncoderNamedPreset.H264MultipleBitrate1080** produit un ensemble de 8 fichiers MP4 alignés sur GOP, de 6 000 Kbits/s à 400 Kbits/s, et un contenu audio AAC stéréo. La résolution commence à 1 080p et descend à 360p.
- **EncoderNamedPreset.H264MultipleBitrate720p** produit un ensemble de 6 fichiers MP4 alignés sur GOP, de 3 400 Kbits/s à 400 Kbits/s, et un contenu audio AAC stéréo. La résolution commence à 720p et descend à 360p.
- **EncoderNamedPreset.H264MultipleBitrateSD** produit un ensemble de 5 fichiers MP4 alignés sur GOP, de 1 600 Kbits/s à 400 Kbits/s, et un contenu audio AAC stéréo. La résolution commence à 480p et descend à 360p.<br/><br/>Pour plus d’informations, consultez [Chargement, encodage et streaming de fichiers](stream-files-tutorial-with-api.md).

### <a name="standardencoderpreset-preset"></a>Préréglage StandardEncoderPreset

[StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset) décrit les paramètres utilisés lors de l’encodage de vidéo en entrée avec l’encodeur standard. Utilisez ce préréglage lors de la personnalisation des préréglages de transformation. 

#### <a name="custom-presets"></a>Préréglages personnalisés

Media Services prend entièrement en charge la personnalisation de toutes les valeurs dans les présélections afin de répondre à vos exigences et vos besoins spécifiques de codage. Vous utilisez le préréglage **StandardEncoderPreset** lors de la personnalisation des préréglages de transformation. Pour obtenir des explications détaillées et un exemple, consultez [Guide pratique pour personnaliser les préréglages de l’encodeur](customize-encoder-presets-how-to.md).

## <a name="scaling-encoding-in-v3"></a>Mise à l’échelle de l’encodage dans v3

Actuellement, les clients doivent utiliser le portail Azure ou des API Media Services v2 pour définir des unités de demande (comme décrit dans [Mise à l’échelle du traitement multimédia](../previous/media-services-scale-media-processing-overview.md). 

## <a name="next-steps"></a>Étapes suivantes

* [Transformations et travaux](transforms-jobs-concept.md)
* [Charger, encoder et diffuser en continu à l’aide de Media Services](stream-files-tutorial-with-api.md)
