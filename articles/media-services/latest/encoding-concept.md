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
ms.date: 04/21/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: ed798995807f4037f0127b08e25e04bdd0340d42
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64724303"
---
# <a name="encoding-with-media-services"></a>Encodage avec Media Services

Azure Media Services vous permet d’encoder vos fichiers multimédias numériques de haute qualité dans des fichiers MP4 à débit adaptatif donc votre contenu peut être lu sur un large éventail de navigateurs et appareils. Un travail d’encodage Media Services réussi crée une sortie d’élément multimédia avec un ensemble de débit adaptatif MP4s et diffusion en continu de fichiers de configuration. Les fichiers de configuration incluent .ism, .ismc, .mpi et autres fichiers que vous ne devez pas modifier. Une fois que le travail d’encodage est terminé, vous pouvez tirer parti de [empaquetage dynamique](dynamic-packaging-overview.md) et démarrer la diffusion en continu.

Pour rendre les vidéos dans la sortie disponible pour les clients pour la lecture de la ressource, vous devez créer un **localisateur de diffusion en continu** et générer l’URL de diffusion. Ensuite, selon le format spécifié dans le manifeste, vos clients reçoivent le flux conforme au protocole choisi.

Le diagramme suivant illustre la diffusion en continu à la demande avec les flux d’empaquetage dynamique.

![Empaquetage dynamique](./media/dynamic-packaging-overview/media-services-dynamic-packaging.svg)

Cette rubrique vous explique comment encoder votre contenu avec Media Services v3.

## <a name="transforms-and-jobs"></a>Transformations et travaux

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

- **EncoderNamedPreset.AACGoodQualityAudio** - produit un fichier MP4 unique contenant seulement le contenu audio stéréo encodé à 192 Kbits/s.
- **EncoderNamedPreset.AdaptiveStreaming** (recommandé). Pour plus d’informations, consultez [Génération automatique d’une échelle de débit binaire](autogen-bitrate-ladder.md).
- **EncoderNamedPreset.ContentAwareEncodingExperimental** -expose une présélection expérimentale pour l’encodage de contenu prenant en charge. Compte tenu de tout contenu d’entrée, le service tente de déterminer automatiquement le nombre optimal de couches, vitesse de transmission appropriée et les paramètres de résolution pour la remise par diffusion en continu adaptative. Les algorithmes sous-jacent continuera à évoluer au fil du temps. La sortie contiendra des fichiers MP4 avec vidéo et audio entrelacées. Pour plus d’informations, consultez [expérimental présélection d’encodage de contenu prenant en charge](cae-experimental.md).
- **EncoderNamedPreset.H264MultipleBitrate1080** produit un ensemble de 8 fichiers MP4 alignés sur GOP, de 6 000 Kbits/s à 400 Kbits/s, et un contenu audio AAC stéréo. La résolution commence à 1 080p et descend à 360p.
- **EncoderNamedPreset.H264MultipleBitrate720p** produit un ensemble de 6 fichiers MP4 alignés sur GOP, de 3 400 Kbits/s à 400 Kbits/s, et un contenu audio AAC stéréo. La résolution commence à 720p et descend à 360p.
- **EncoderNamedPreset.H264MultipleBitrateSD** produit un ensemble de 5 fichiers MP4 alignés sur GOP, de 1 600 Kbits/s à 400 Kbits/s, et un contenu audio AAC stéréo. La résolution commence à 480p et descend à 360p.
- **EncoderNamedPreset.H264SingleBitrate1080p** -génère un fichier MP4, où la vidéo est encodée avec le codec H.264 à 6750 Kbits/s et une hauteur de l’image de 1080 pixels, et l’audio stéréo est codé avec le codec AAC-LC à 64 Kbits/s.
- **EncoderNamedPreset.H264SingleBitrate720p** -génère un fichier MP4, où la vidéo est encodée avec le codec H.264 à 4500 Kbits/s et une hauteur d’image de 720 pixels, et l’audio stéréo est codé avec le codec AAC-LC à 64 Kbits/s.
- **EncoderNamedPreset.H264SingleBitrateSD** -génère un fichier MP4, où la vidéo est encodée avec le codec H.264 à 2 200 kbit/s et une hauteur de l’image de 480 pixels, et l’audio stéréo est codé avec le codec AAC-LC à 64 Kbits/s.

Pour afficher la liste de paramètres prédéfinis plus récente, consultez [préréglages intégrés à utiliser pour l’encodage de vidéos](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#encodernamedpreset).

Pour voir comment sont utilisées les présélections, consultez [chargement, de codage et diffusion en continu de fichiers](stream-files-tutorial-with-api.md).

### <a name="standardencoderpreset-preset"></a>Préréglage StandardEncoderPreset

[StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset) décrit les paramètres utilisés lors de l’encodage de vidéo en entrée avec l’encodeur standard. Utilisez ce préréglage lors de la personnalisation des préréglages de transformation. 

#### <a name="considerations"></a>Considérations

Lorsque vous créez des paramètres prédéfinis personnalisés, les considérations suivantes s’appliquent :

- Toutes les valeurs de hauteur et la largeur du contenu de AVC doivent être un multiple de 4.
- Dans Azure Media Services v3, tous les débits binaires de codage sont en bits par seconde. Cela diffère des présélections avec nos API v2, utiliser des kilobits par seconde en tant que l’unité. Par exemple, si la vitesse de transmission dans v2 a été spécifié en tant que 128 (kilobits par seconde), dans v3 il serait défini à 128000 (bits/seconde).

#### <a name="examples"></a>Exemples

Media Services prend entièrement en charge la personnalisation de toutes les valeurs dans les présélections afin de répondre à vos exigences et vos besoins spécifiques de codage. Pour obtenir des exemples qui montrent comment personnaliser des présélections d’encodeur, consultez :

- [Personnaliser des présélections avec .NET](customize-encoder-presets-how-to.md)
- [Personnaliser des présélections avec CLI](custom-preset-cli-howto.md)
- [Personnaliser des présélections avec REST](custom-preset-rest-howto.md)

## <a name="scaling-encoding-in-v3"></a>Mise à l’échelle de l’encodage dans v3

Pour mettre à l’échelle le traitement multimédia, consultez [mise à l’échelle avec CLI](media-reserved-units-cli-how-to.md).

## <a name="provide-feedback"></a>Fournir des commentaires

Découvrez l’article [Communauté Azure Media Services](media-services-community.md) pour découvrir les différentes façons dont vous pouvez poser des questions, faire des commentaires et obtenir des mises à jour sur Media Services.

## <a name="next-steps"></a>Étapes suivantes

* [Encoder à partir d’une URL HTTPS à l’aide de paramètres prédéfinis intégrés](job-input-from-http-how-to.md)
* [Encoder un fichier local à l’aide de paramètres prédéfinis intégrés](job-input-from-local-file-how-to.md)
* [Créer un paramètre prédéfini pour cibler vos exigences de scénario ou un périphérique spécifiques](customize-encoder-presets-how-to.md)
* [Charger, encoder et diffuser en continu à l’aide de Media Services](stream-files-tutorial-with-api.md)
