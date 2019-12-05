---
title: Transcription en direct Azure Media Services | Microsoft Docs
description: Cet article explique ce qu’est la transcription en direct d’Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 11/19/2019
ms.author: juliako
ms.openlocfilehash: a85f9f8b9d98f77cf673778f031d8f47f132fbe1
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74327345"
---
# <a name="live-transcription-preview"></a>Transcription en direct (préversion)

Azure Media Services diffuse de la vidéo, de l’audio et maintenant du texte dans différents protocoles. Lorsque vous publiez votre stream en direct en MPEG-DASH ou HLS/CMAF, notre service diffuse le texte transcrit en IMSC1.1 compatible TTML, sous forme de fragments MPEG-4 Partie 30 (ISO/CEI 14496-30), avec la vidéo et le son. Si vous utilisez la diffusion via HLS/TS, le texte est diffusé sous forme de VTT segmenté. 

Cet article explique comment activer la transcription en direct lors de la diffusion en continu d’un événement en direct grâce à Azure Media Services v3. Avant de continuer, assurez-vous de connaître l’utilisation des API REST de Media Services v3 (pour plus d’informations, consultez [ce tutoriel](stream-files-tutorial-with-rest.md)). Vous devez également maîtriser le concept de [streaming en direct](live-streaming-overview.md). Nous vous conseillons de suivre le tutoriel [Streaming en direct avec Media Services](stream-live-tutorial-with-api.md). 

> [!NOTE]
> Actuellement, la transcription en direct est disponible uniquement en tant que fonctionnalité d’évaluation dans la région USA Ouest 2. Elle prend en charge la transcription en texte des mots parlés en anglais. La référence API de cette fonctionnalité se trouve dans ce document : étant donné qu’il s’agit d’une version préliminaire, les détails ne sont pas disponibles avec nos documents REST. 

## <a name="creating-the-live-event"></a>Création de l’événement en direct 

Pour créer l’événement en direct, vous devez envoyer l’opération PUT à la version 2019-05-01, par exemple : 

```
PUT https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/liveEvents/:liveEventName?api-version=2019-05-01-preview&autoStart=true 
```

L’opération a le corps suivant (où un événement en direct pass-through est créé avec RTMP comme protocole de réception). Notez l’ajout d’une propriété de transcriptions. La seule valeur autorisée pour la langue est en-US. 

```
{ 
  "properties": { 
    "description": "Demonstrate how to enable live transcriptions", 
    "input": { 
      "streamingProtocol": "RTMP", 
      "accessControl": { 
        "ip": { 
          "allow": [ 
            { 
              "name": "Allow All", 
              "address": "0.0.0.0", 
              "subnetPrefixLength": 0 
            } 
          ] 
        } 
      } 
    }, 
    "preview": { 
      "accessControl": { 
        "ip": { 
          "allow": [ 
            { 
              "name": "Allow All", 
              "address": "0.0.0.0", 
              "subnetPrefixLength": 0 
            } 
          ] 
        } 
      } 
    }, 
    "encoding": { 
      "encodingType": "None" 
    }, 
    "transcriptions": [ 
      { 
        "language": "en-US" 
      } 
    ], 
    "vanityUrl": false, 
    "streamOptions": [ 
      "Default" 
    ] 
  }, 
  "location": "West US 2" 
} 
```

Vous devez interroger l’état de l’événement en direct jusqu’à ce qu’il passe à l’état « En cours d’exécution », ce qui indique que vous pouvez à présent envoyer un flux RTMP de contribution. Vous pouvez maintenant suivre les mêmes étapes que dans ce tutoriel, telles que la vérification de l’aperçu du flux et la création de sorties en direct. 

## <a name="delivery-and-playback"></a>Diffusion et lecture 

Consultez l’article [Vue d’ensemble de l’empaquetage dynamique](dynamic-packaging-overview.md#to-prepare-your-source-files-for-delivery) sur la façon dont notre service utilise l’empaquetage dynamique pour diffuser de la vidéo, de l’audio et du texte dans différents protocoles. Lorsque vous publiez votre stream en direct en MPEG-DASH ou HLS/CMAF, notre service diffuse le texte transcrit en IMSC1.1 compatible TTML, sous forme de fragments MPEG-4 Partie 30 (ISO/CEI 14496-30), avec la vidéo et le son. Si vous utilisez la diffusion via HLS/TS, le texte est diffusé sous forme de VTT segmenté. Vous pouvez utiliser un lecteur Web, tel que le [Lecteur multimédia Azure](use-azure-media-player.md) pour lire le flux.  

> [!NOTE]
>  Si vous utilisez le Lecteur multimédia Azure, utilisez la version 2.3.3 ou une version ultérieure.

## <a name="known-issues"></a>Problèmes connus 

En préversion, voici les problèmes connus liés à la transcription en direct 

* La fonctionnalité est disponible uniquement dans la région USA Ouest 2.
* Les applications doivent utiliser les API de préversion, décrites dans la documentation [Spécification OpenAPI de Media Services v3](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/preview/2019-05-01-preview/streamingservice.json).
* L’anglais est la seule langue prise en charge.
* En ce qui concerne la protection du contenu, seul le chiffrement de l’enveloppe AES est pris en charge.

## <a name="next-steps"></a>Étapes suivantes

[Présentation de Media Services](media-services-overview.md)
