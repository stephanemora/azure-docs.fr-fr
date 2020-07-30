---
title: Vue d’ensemble d’encodeurs multimédia à la demande Azure | Microsoft Docs
description: Azure Media Services fournit plusieurs options pour l’encodage de fichiers multimédias dans le cloud. Cet article donne une vue d’ensemble des encodeurs multimédias à la demande Azure.
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
ms.date: 06/25/2019
ms.author: juliako
ms.openlocfilehash: ef558b9339fe1d4525156cf58efe5056862de0a2
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87052751"
---
# <a name="overview-of-azure-on-demand-media-encoders"></a>Vue d’ensemble d’encodeurs multimédia à la demande Azure 

> [!NOTE]
> Aucune nouvelle fonctionnalité ni fonction n’est ajoutée à Media Services v2. <br/>Découvrez la dernière version, [Media Services v3](../latest/index.yml). Consultez aussi [Conseils de migration de v2 vers v3](../latest/migrate-from-v2-to-v3.md).

Azure Media Services fournit plusieurs options pour l’encodage de fichiers multimédias dans le cloud.

Quand vous commencez à utiliser Media Services, il est important de bien comprendre la différence entre les codecs et les formats de fichiers.
Les codecs sont les logiciels qui implémentent les algorithmes de compression/décompression, tandis que les formats de fichiers sont des conteneurs qui contiennent la vidéo compressée.

Media Services fournit l’empaquetage dynamique qui permet de distribuer un contenu en diffusion continue en MP4 ou Smooth Streaming dans un format pris en charge par Media Services (MPEG DASH, HLS, Smooth Streaming) sans avoir à recréer de nouveaux packages dans ces formats.

Une fois votre compte Media Services créé, un point de terminaison de streaming **par défaut** est ajouté à votre compte à l’état **Arrêté**. Pour démarrer la diffusion en continu de votre contenu et tirer parti de l’empaquetage et du chiffrement dynamiques, le point de terminaison de streaming à partir duquel vous souhaitez diffuser du contenu doit se trouver à l’état **En cours d’exécution**. La facturation des points de terminaison de streaming se produit chaque fois que le point de terminaison est **en cours d’exécution**.

Media Services prend en charge les éléments suivants sur les encodeurs à la demande décrits dans cet article :

* [Media Encoder Standard](media-services-encode-asset.md#media-encoder-standard)
* [Media Encoder Premium Workflow](media-services-encode-asset.md#media-encoder-premium-workflow)

Cet article donne un bref aperçu des encodeurs multimédia à la demande et fournit des liens vers des articles fournissant des informations plus détaillées. Cette rubrique compare également les encodeurs.

Par défaut, chaque compte Media Services peut avoir une tâche d’encodage active à la fois. Vous pouvez réserver des unités d’encodage qui vous permettent d’exécuter plusieurs tâches d’encodage simultanément, une pour chaque unité réservée d’encodage que vous achetez. Pour plus d’informations, consultez [Mise à l’échelle des unités d’encodage](media-services-scale-media-processing-overview.md).

## <a name="media-encoder-standard"></a>Media Encoder Standard

### <a name="how-to-use"></a>Utilisation
[Encodage avec Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md)

### <a name="formats"></a>Formats
[Formats et codecs](media-services-media-encoder-standard-formats.md)

### <a name="presets"></a>Présélections
Media Encoder Standard se configure à l’aide d’une des présélections d’encodeur décrites [ici](https://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409).

### <a name="input-and-output-metadata"></a>Métadonnées d’entrée et de sortie
Les métadonnées d’entrée des encodeurs sont décrites [ici](media-services-input-metadata-schema.md).

Les métadonnées de sortie des encodeurs sont décrites [ici](media-services-output-metadata-schema.md).

### <a name="generate-thumbnails"></a>Génération de miniatures
Pour plus d’informations, consultez [Génération de miniatures à l’aide de Media Encoder Standard](media-services-advanced-encoding-with-mes.md#thumbnails).

### <a name="trim-videos-clipping"></a>Découpage de vidéos (extrait)
Pour plus d’informations, consultez [Découpage de vidéos à l’aide de Media Encoder Standard](media-services-advanced-encoding-with-mes.md#trim_video).

### <a name="create-overlays"></a>Création de superpositions
Pour plus d’informations, consultez [Création de superpositions à l’aide de Media Encoder Standard](media-services-advanced-encoding-with-mes.md#overlay).

### <a name="see-also"></a>Voir aussi
[Blog sur Media Services](https://azure.microsoft.com/blog/2015/07/16/announcing-the-general-availability-of-media-encoder-standard/)

## <a name="media-encoder-premium-workflow"></a>Media Encoder Premium Workflow
### <a name="overview"></a>Vue d’ensemble
[Présentation de l’encodage Premium dans Azure Media Services](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services/)

### <a name="how-to-use"></a>Utilisation
Media Encoder Premium Workflow se configure à l’aide de flux de travail complexes. Les fichiers de flux de travail peuvent être créés et mis à jour à l’aide de l’outil [Concepteur de flux de travail](media-services-workflow-designer.md) .

[Utilisation de l’encodage Premium dans Azure Media Services](https://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services/)

### <a name="known-issues"></a>Problèmes connus
Si votre vidéo d’entrée ne contient pas de sous-titres, l’élément multimédia de sortie actif comportera toujours un fichier TTML vide.

## <a name="media-services-learning-paths"></a>Parcours d’apprentissage de Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fournir des commentaires
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>Articles connexes
* [Exécution de tâches d’encodage avancées via la personnalisation des présélections Media Encoder Standard](media-services-custom-mes-presets-with-dotnet.md)
* [Quotas et limitations](media-services-quotas-and-limitations.md)

<!--Reference links in article-->
[1]: https://azure.microsoft.com/pricing/details/media-services/
