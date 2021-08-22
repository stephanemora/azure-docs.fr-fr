---
title: Vue d’ensemble de l’empaquetage dynamique Azure Media Services | Microsoft Docs
description: Cet article offre une vue d’ensemble de l’empaquetage dynamique de Microsoft Azure Media Services.
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 397af7a327657cda11c25ade08e93a8f13fe3fdb
ms.sourcegitcommit: e6de87b42dc320a3a2939bf1249020e5508cba94
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/27/2021
ms.locfileid: "114706262"
---
# <a name="dynamic-packaging"></a>l’empaquetage dynamique

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector" title1="Sélectionnez la version de Media Services que vous utilisez :"]
> * [Version 3](../latest/encode-dynamic-packaging-concept.md)
> * [Version 2](media-services-dynamic-packaging-overview.md)

[!INCLUDE [v2 deprecation notice](../latest/includes/v2-deprecation-notice.md)]

Vous pouvez utiliser Microsoft Azure Media Services pour distribuer de nombreux formats de fichiers sources multimédias, formats de streaming de contenu multimédia et formats de protection de contenu à diverses technologies clientes (par exemple, iOS, XBOX, Silverlight, Windows 8). Ces clients comprennent différents protocoles. Par exemple, iOS nécessite un format HTTP Live Streaming (HLS) V4, tandis que Silverlight et Xbox nécessitent le format Smooth Streaming. Si vous voulez transmettre un ensemble de fichiers MP4 (ISO Base Media 14496-12) ou de fichiers Smooth Streaming à vitesse de transmission adaptative, à des clients qui utilisent le format MPEG DASH, HLS ou Smooth Streaming, nous vous recommandons de tirer profit de l'empaquetage dynamique Media Services.

Avec l'empaquetage dynamique, il vous suffit de créer un élément multimédia contenant un ensemble de fichiers MP4 ou de fichiers Smooth Streaming à vitesse de transmission adaptative. Ensuite, en fonction du format spécifié dans le manifeste ou la demande de fragment, le serveur de diffusion en continu à la demande s'assure que vous recevez le flux conforme au protocole choisi. Par conséquent, il vous suffit de stocker et de payer les fichiers dans un seul format de stockage. Le service Media Services se charge de créer et de fournir la réponse appropriée en fonction des demandes des clients.

Le diagramme suivant illustre le flux traditionnel d'encodage et d'empaquetage statique.

![Encodage statique](./media/media-services-dynamic-packaging-overview/media-services-static-packaging.png)

Le diagramme suivant illustre le flux d'empaquetage dynamique.

![Encodage dynamique](./media/media-services-dynamic-packaging-overview/media-services-dynamic-packaging.png)

## <a name="common-scenario"></a>Scénario courant

1. Téléchargez un fichier d'entrée (appelé fichier mezzanine). Par exemple, H.264, MP4 ou WMV (pour obtenir la liste des formats pris en charge, consultez [Formats pris en charge par Media Encoder Standard](media-services-media-encoder-standard-formats.md)).
2. Encodez votre fichier mezzanine en ensembles de fichiers MP4 à vitesse de transmission adaptative H.264.
3. Publiez l'élément multimédia qui contient l'ensemble de fichiers MP4 à vitesse de transmission adaptative en créant le localisateur à la demande.
4. Générez les URL de streaming pour accéder à votre contenu et le diffuser en continu.

## <a name="preparing-assets-for-dynamic-streaming"></a>Préparation des éléments multimédias pour un streaming dynamique

Pour préparer un élément multimédia à un streaming dynamique, vous disposez des options suivantes :

- [Chargez un fichier maître](media-services-dotnet-upload-files.md).
- [Utilisez l’encodeur Media Encoder Standard pour produire des ensembles de fichiers MP4 à vitesse de transmission adaptative H.264](media-services-dotnet-encode-with-media-encoder-standard.md).
- [Diffusez votre contenu](media-services-deliver-content-overview.md).

## <a name="audio-codecs-supported-by-dynamic-packaging"></a>Codecs audio pris en charge par l’empaquetage dynamique

L’empaquetage dynamique prend en charge les fichiers MP4 qui contiennent des données audio encodées avec [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding) (AAC-LC, HE-AAC v1, HE-AAC v2), [Dolby Digital Plus](https://en.wikipedia.org/wiki/Dolby_Digital_Plus) (Enhanced AC-3 ou E-AC3), Dolby Atmos ou [ DTS](https://en.wikipedia.org/wiki/DTS_%28sound_system%29) (DTS Express, DTS LBR, DTS HD, HD DTS sans perte). La diffusion en continu de contenu Dolby Atmos est prise en charge pour les normes telles que le protocole MPEG-DASH avec MP4 fragmenté au format CSF (Common Streaming Format) ou CMAF (Common Media Application Format), et via HLS (HTTP Live Streaming) avec CMAF.

> [!NOTE]
> L’empaquetage dynamique ne prend pas en charge les fichiers qui contiennent des données audio [Dolby Digital](https://en.wikipedia.org/wiki/Dolby_Digital) (AC3) (il s’agit d’un codec hérité).

## <a name="media-services-learning-paths"></a>Parcours d’apprentissage de Media Services

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fournir des commentaires

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
