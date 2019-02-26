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
ms.date: 02/19/2019
ms.author: juliako
ms.openlocfilehash: d1d07402bca5f01cf63d0b039c085e46bb0f0d62
ms.sourcegitcommit: 6cab3c44aaccbcc86ed5a2011761fa52aa5ee5fa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/20/2019
ms.locfileid: "56447920"
---
# <a name="dynamic-packaging"></a>l’empaquetage dynamique

Vous pouvez utiliser Microsoft Azure Media Services pour distribuer de nombreux formats de fichiers sources multimédias, formats de streaming de contenu multimédia et formats de protection de contenu à diverses technologies clientes (par exemple, iOS et XBOX). Ces clients comprennent différents protocoles. Par exemple, iOS nécessite un format HTTP Live Streaming (HLS), tandis que Xbox nécessite le format Smooth Streaming. Si vous voulez transmettre un ensemble de fichiers MP4 (ISO Base Media 14496-12) ou de fichiers Smooth Streaming à vitesse de transmission adaptative, à des clients qui utilisent le format HLS, MPEG DASH ou Smooth Streaming, nous vous recommandons de tirer profit de l’empaquetage dynamique Media Services.

Avec l’empaquetage dynamique, il vous suffit de créer un élément contenant un ensemble de fichiers MP4 à débit adaptatif. Ensuite, en fonction du format spécifié dans le manifeste ou la demande de fragment, le serveur de diffusion en continu à la demande s'assure que vous recevez le flux conforme au protocole choisi. Par conséquent, il vous suffit de stocker et de payer les fichiers dans un seul format de stockage. Le service Media Services se charge de créer et de fournir la réponse appropriée en fonction des demandes des clients.

Le diagramme suivant illustre le flux traditionnel d'encodage et d'empaquetage statique.

![Encodage statique](./media/dynamic-packaging-overview/media-services-static-packaging.png)

Le diagramme suivant illustre le flux d'empaquetage dynamique.

![Encodage dynamique](./media/dynamic-packaging-overview/media-services-dynamic-packaging.png)

## <a name="dynamic-packaging-workflow"></a>Flux de travail de l’empaquetage dynamique

1. Téléchargez un fichier d'entrée (appelé fichier mezzanine). Par exemple, H.264, MP4 ou WMV (pour obtenir la liste des formats pris en charge, consultez [Formats pris en charge par Media Encoder Standard](media-encoder-standard-formats.md)).
2. Encodez votre fichier mezzanine en ensembles de fichiers MP4 à vitesse de transmission adaptative H.264.
3. Publier l’élément multimédia qui contient l’ensemble au débit adaptatif MP4.
4. Générez les URL de streaming pour accéder à votre contenu et le diffuser en continu.

## <a name="audio-codecs-supported-by-dynamic-packaging"></a>Codecs audio pris en charge par l’empaquetage dynamique

L’empaquetage dynamique prend en charge les fichiers MP4 qui contiennent des données audio encodées avec [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding) (AAC-LC, HE-AAC v1, HE-AAC v2), [Dolby Digital Plus](https://en.wikipedia.org/wiki/Dolby_Digital_Plus) (Enhanced AC-3 ou E-AC3) ou [ DTS](https://en.wikipedia.org/wiki/DTS_%28sound_system%29) (DTS Express, DTS LBR, DTS HD, HD DTS sans perte).

> [!NOTE]
> L’empaquetage dynamique ne prend pas en charge les fichiers qui contiennent des données audio [Dolby Digital](https://en.wikipedia.org/wiki/Dolby_Digital) (AC3) (il s’agit d’un codec hérité).

## <a name="next-steps"></a>Étapes suivantes

[Charger, encoder et diffuser des vidéos en continu](stream-files-tutorial-with-api.md)
