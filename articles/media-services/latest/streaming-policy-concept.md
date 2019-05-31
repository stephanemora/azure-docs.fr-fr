---
title: Stratégies de streaming dans Azure Media Services | Microsoft Docs
description: Cet article explique ce que sont les stratégies de streaming et comment Azure Media Services les utilise.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 05/28/2019
ms.author: juliako
ms.openlocfilehash: a813c77e81e51bfe13e75ed6c8d0e24b4d0fa645
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66392920"
---
# <a name="streaming-policies"></a>Stratégies de diffusion en continu

Dans Azure Media Services v3, les [stratégies de diffusion en continu](https://docs.microsoft.com/rest/api/media/streamingpolicies) vous permettent de définir les protocoles de diffusion en continu et les options de chiffrement pour vos [StreamingLocators](streaming-locators-concept.md). Media Services v3 fournit que certaines prédéfinies des stratégies de diffusion en continu afin que vous puissiez les utiliser directement pour la version d’évaluation ou de production. 

Actuellement disponible prédéfinies des stratégies de diffusion en continu :<br/>
* 'Predefined_DownloadOnly'
* 'Predefined_ClearStreamingOnly'
* 'Predefined_DownloadAndClearStreaming'
* 'Predefined_ClearKey'
* 'Predefined_MultiDrmCencStreaming' 
* 'Predefined_MultiDrmStreaming'

L’arbre de décision « suivant » vous permet de choisir une stratégie prédéfinie de diffusion en continu pour votre scénario.

> [!IMPORTANT]
> * Les propriétés des **stratégies de diffusion en continu** de type DateHeure sont toujours au format UTC.
> * Vous devez concevoir un ensemble limité de stratégies pour votre compte Media Services et les réutiliser pour vos éléments localisateurs de diffusion en continu chaque fois que les mêmes options sont nécessaires. Pour plus d’informations, consultez [Quotas et limitations](limits-quotas-constraints.md).

## <a name="decision-tree"></a>Arbre de décision

Cliquez sur l’image pour l’afficher en plein écran.  

<a href="./media/streaming-policy/large.png" target="_blank"><img src="./media/streaming-policy/large.png"></a> 

Si chiffrer votre contenu, vous devez créer un [stratégie de la clé de contenu](content-key-policy-concept.md), le **stratégie de la clé de contenu** n’est pas nécessaire pour effacer de diffusion en continu ou de téléchargement. 

Si vous avez des exigences particulières (par exemple, si vous souhaitez spécifier différents protocoles, avoir besoin d’utiliser un service de remise de clé personnalisé, ou que vous devez utiliser une piste audio clair), vous pouvez [créer](https://docs.microsoft.com/rest/api/media/streamingpolicies/create) une stratégie personnalisée de diffusion en continu. 

## <a name="get-a-streaming-policy-definition"></a>Obtenir une définition de stratégie de diffusion en continu  

Si vous souhaitez voir la définition d’une stratégie de diffusion en continu, utilisez [obtenir](https://docs.microsoft.com/rest/api/media/streamingpolicies/get) et spécifiez le nom de la stratégie. Exemple :

### <a name="rest"></a>REST

Demande :

```
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Media/mediaServices/contosomedia/streamingPolicies/clearStreamingPolicy?api-version=2018-07-01
```

Réponse :

```
{
  "name": "clearStreamingPolicy",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Media/mediaservices/contosomedia/streamingPolicies/clearStreamingPolicy",
  "type": "Microsoft.Media/mediaservices/streamingPolicies",
  "properties": {
    "created": "2018-08-08T18:29:30.8501486Z",
    "noEncryption": {
      "enabledProtocols": {
        "download": true,
        "dash": true,
        "hls": true,
        "smoothStreaming": true
      }
    }
  }
}
```

## <a name="filtering-ordering-paging"></a>Filtrage, tri, pagination

Consultez [Filtrage, tri et pagination des entités Media Services](entities-overview.md).

## <a name="next-steps"></a>Étapes suivantes

* [Diffuser un fichier](stream-files-dotnet-quickstart.md)
* [Utiliser le chiffrement dynamique AES-128 et le service de distribution des clés](protect-with-aes128.md)
* [Utilisation du chiffrement dynamique DRM et du service de remise des licences](protect-with-drm.md)
