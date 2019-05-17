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
ms.date: 05/10/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 25b3209bed98ea217db9e414caa6f08cee6d8c89
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/16/2019
ms.locfileid: "65761885"
---
# <a name="encoding-with-media-services"></a>Encodage avec Media Services

L’encodage du terme dans Media Services s’applique au processus de conversion des fichiers contenant numérique vidéo et/ou audio d’un format standard vers un autre, dans le but de (a) en réduisant la taille des fichiers et/ou (b) produisant un format qui est compatible avec un large éventail de périphériques et applications. Ce processus est également appelé compression vidéo ou de transcodage. Consultez le [la compression des données](https://en.wikipedia.org/wiki/Data_compression) et [nouveautés, encodage et transcodage ?](https://www.streamingmedia.com/Articles/Editorial/What-Is-/What-Is-Encoding-and-Transcoding-75025.aspx) pour plus d’informations sur les concepts.

Vidéos sont généralement fournies aux appareils et aux applications par [le téléchargement progressif](https://en.wikipedia.org/wiki/Progressive_download) ou via [streaming à débit adaptatif](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming). 

* Pour distribuer par téléchargement progressif, vous pouvez utiliser Azure Media Services pour convertir un votre fichier multimédia numérique (mezzanine) dans un [MP4](https://en.wikipedia.org/wiki/MPEG-4_Part_14) fichier qui contient la vidéo qui a été encodée avec le [H.264](https://en.wikipedia.org/wiki/H.264/MPEG-4_AVC) codec, et audio qui a été encodée avec le [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding) codec. Ce fichier MP4 est écrit dans une ressource dans votre compte de stockage. Vous pouvez utiliser l’API de stockage Azure ou les kits de développement logiciel (par exemple, [API REST de stockage](../../storage/common/storage-rest-api-auth.md), [JAVA SDK](../../storage/blobs/storage-quickstart-blobs-java-v10.md), ou [.NET SDK](../../storage/blobs/storage-quickstart-blobs-dotnet.md)) pour télécharger le fichier directement. Si vous avez créé la sortie actif avec un nom de conteneur spécifique dans le stockage, utilisez cet emplacement. Sinon, vous pouvez utiliser Media Services à [répertorie les URL de conteneur actif](https://docs.microsoft.com/rest/api/media/assets/listcontainersas). 
* Pour préparer le contenu pour la remise par streaming à débit adaptatif, le fichier mezzanine doit être encodé à plusieurs débits binaires (score élevé à bas). Pour garantir une transition sans perte de données de la qualité, comme la vitesse de transmission est réduite, est donc la résolution de la vidéo. Il en résulte une échelle encodage ce que l'on appelle – une table de résolutions et vitesses de transmission (consultez [généré automatiquement à débit adaptatif échelle](autogen-bitrate-ladder.md)). Vous pouvez utiliser Media Services pour encoder vos fichiers mezzanine à plusieurs débits binaires – ce faisant, vous obtenez un ensemble de fichiers MP4 et diffusion en continu configuration fichiers associés, écrites dans un élément multimédia dans votre compte de stockage. Vous pouvez ensuite utiliser le [empaquetage dynamique](dynamic-packaging-overview.md) fonctionnalité dans Media Services pour fournir la vidéo via la diffusion en continu de protocoles tels que [MPEG-DASH](https://en.wikipedia.org/wiki/Dynamic_Adaptive_Streaming_over_HTTP) et [HLS](https://en.wikipedia.org/wiki/HTTP_Live_Streaming). Cela vous oblige à créer un [localisateur de diffusion en continu](streaming-locators-concept.md) et générer l’URL correspondant aux protocoles pris en charge, ce qui peuvent ensuite être remis aux appareils/applications basées sur leurs fonctionnalités de diffusion.

Le diagramme suivant illustre le flux de travail pour l’encodage de la demande avec l’empaquetage dynamique.

![Empaquetage dynamique](./media/dynamic-packaging-overview/media-services-dynamic-packaging.png)

Cette rubrique vous explique comment encoder votre contenu avec Media Services v3.

## <a name="transforms-and-jobs"></a>Transformations et travaux

Pour encoder avec Media Services v3, vous devez créer une [transformation](https://docs.microsoft.com/rest/api/media/transforms) et un [travail](https://docs.microsoft.com/rest/api/media/jobs). La transformation définit une Recipe (Recette) pour les paramètres de codage et les sorties ; le travail est une instance de la recette. Pour plus d’informations, consultez [Transformations et travaux](transforms-jobs-concept.md).

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

## <a name="preset-schema"></a>Schéma prédéfini

Dans Media Services v3, Présélections sont des entités fortement typées dans l’API elle-même. Vous trouverez la définition de « schema » pour ces objets dans [Open API Specification (ou Swagger)](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01). Vous pouvez également afficher les définitions prédéfinies (comme **StandardEncoderPreset**) dans le [API REST](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset), [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.standardencoderpreset?view=azure-dotnet) (ou autres documents de référence SDK Media Services v3).

## <a name="scaling-encoding-in-v3"></a>Mise à l’échelle de l’encodage dans v3

Pour mettre à l’échelle le traitement multimédia, consultez [mise à l’échelle avec CLI](media-reserved-units-cli-how-to.md).

## <a name="ask-questions-give-feedback-get-updates"></a>Poser des questions, envoyer des commentaires, obtenir des mises à jour

Découvrez l’article [Communauté Azure Media Services](media-services-community.md) pour découvrir les différentes façons dont vous pouvez poser des questions, faire des commentaires et obtenir des mises à jour sur Media Services.

## <a name="next-steps"></a>Étapes suivantes

* [Encoder à partir d’une URL HTTPS à l’aide de paramètres prédéfinis intégrés](job-input-from-http-how-to.md)
* [Encoder un fichier local à l’aide de paramètres prédéfinis intégrés](job-input-from-local-file-how-to.md)
* [Créer un paramètre prédéfini pour cibler vos exigences de scénario ou un périphérique spécifiques](customize-encoder-presets-how-to.md)
* [Charger, encoder et diffuser en continu à l’aide de Media Services](stream-files-tutorial-with-api.md)
