---
title: Stratégies de streaming dans Azure Media Services
description: Cet article explique ce que sont les stratégies de streaming et comment Azure Media Services les utilise.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 05/28/2019
ms.author: inhenkel
ms.openlocfilehash: 82f01b4216af8d924354ae9e9a204a8eaefb8213
ms.sourcegitcommit: 9f4510cb67e566d8dad9a7908fd8b58ade9da3b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106120841"
---
# <a name="streaming-policies"></a>Stratégies de diffusion en continu

Dans Azure Media Services v3, les [stratégies de diffusion en continu](/rest/api/media/streamingpolicies) vous permettent de définir les protocoles de diffusion en continu et les options de chiffrement pour vos [StreamingLocators](streaming-locators-concept.md). Media Services v3 fournit certaines stratégies de streaming prédéfinies pour vous permettre de les utiliser directement pour la version d’essai ou de production. 

Voici les stratégies de streaming prédéfinies actuellement disponibles :<br/>
* 'Predefined_DownloadOnly'
* 'Predefined_ClearStreamingOnly'
* 'Predefined_DownloadAndClearStreaming'
* 'Predefined_ClearKey'
* 'Predefined_MultiDrmCencStreaming' 
* 'Predefined_MultiDrmStreaming'

L’« arbre de décision » suivant vous permet de choisir une stratégie de streaming prédéfinie pour votre scénario.

> [!IMPORTANT]
> * Les propriétés des **stratégies de diffusion en continu** de type DateHeure sont toujours au format UTC.
> * Vous devez concevoir un ensemble limité de stratégies pour votre compte Media Services et les réutiliser pour vos éléments localisateurs de diffusion en continu chaque fois que les mêmes options sont nécessaires. Pour plus d’informations, consultez [Quotas et limites](limits-quotas-constraints-reference.md).

## <a name="decision-tree"></a>Arbre de décision

Cliquez sur l’image pour l’afficher en plein écran.  

[![Diagramme montrant un arbre de décision conçu pour vous aider à choisir une stratégie de diffusion en continu prédéfinie pour votre scénario.](./media/streaming-policy/large.png)](./media/streaming-policy/large.png#lightbox)

Si vous chiffrez votre contenu, vous devez créer une [stratégie de clé de contenu](drm-content-key-policy-concept.md). La **stratégie de clé de contenu** n’est pas nécessaire pour un téléchargement ou un streaming en clair. 

Si vous avez des exigences particulières (par exemple, si vous souhaitez spécifier différents protocoles, que vous avez besoin d’utiliser un service de distribution de clés personnalisé ou que vous devez utiliser une piste audio claire), vous pouvez [créer](/rest/api/media/streamingpolicies/create) une stratégie de streaming personnalisée. 

## <a name="get-a-streaming-policy-definition"></a>Obtenir une définition de stratégie de streaming  

Si vous voulez voir la définition d’une stratégie de streaming, utilisez [Get](/rest/api/media/streamingpolicies/get) et spécifiez le nom de la stratégie. Par exemple :

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

Consultez [Filtrage, tri et pagination des entités Media Services](filter-order-page-entitites-how-to.md).

## <a name="next-steps"></a>Étapes suivantes

* [Diffuser un fichier](stream-files-dotnet-quickstart.md)
* [Utiliser le chiffrement dynamique AES-128 et le service de distribution des clés](drm-playready-license-template-concept.md)
* [Utilisation du chiffrement dynamique DRM et du service de remise des licences](drm-protect-with-drm-tutorial.md)
