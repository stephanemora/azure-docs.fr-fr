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
ms.date: 02/03/2019
ms.author: juliako
ms.openlocfilehash: 10600d8f3ff4e08b8d90f28ec15d3cb0c56bcae0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61230894"
---
# <a name="streaming-policies"></a>Stratégies de diffusion en continu

Dans Azure Media Services v3, les [stratégies de diffusion en continu](https://docs.microsoft.com/rest/api/media/streamingpolicies) vous permettent de définir les protocoles de diffusion en continu et les options de chiffrement pour vos [StreamingLocators](streaming-locators-concept.md). Vous pouvez utiliser l’une des stratégies de diffusion en continu prédéfinies ou en créer une personnalisée. Voici les stratégies de diffusion en continu prédéfinies disponibles actuellement : « Predefined_DownloadOnly », « Predefined_ClearStreamingOnly », « Predefined_DownloadAndClearStreaming », « Predefined_ClearKey », « Predefined_MultiDrmCencStreaming » et « Predefined_MultiDrmStreaming ».

> [!IMPORTANT]
> * Les propriétés des **stratégies de diffusion en continu** de type DateHeure sont toujours au format UTC.
> * Vous devez concevoir un ensemble limité de stratégies pour votre compte Media Services et les réutiliser pour vos éléments StreamingLocators chaque fois que les mêmes options sont nécessaires. 

## <a name="examples"></a>Exemples

### <a name="not-encrypted"></a>Non chiffré

Si vous souhaitez diffuser votre fichier en clair (sans chiffrement), configurez la stratégie de diffusion en continu en clair prédéfinie : « Predefined_ClearStreamingOnly » (dans .NET, vous pouvez utiliser PredefinedStreamingPolicy.ClearStreamingOnly).

```csharp
StreamingLocator locator = await client.StreamingLocators.CreateAsync(
    resourceGroup,
    accountName,
    locatorName,
    new StreamingLocator
    {
        AssetName = assetName,
        StreamingPolicyName = PredefinedStreamingPolicy.ClearStreamingOnly
    });
```

### <a name="encrypted"></a>Chiffré 

Si vous devez chiffrer votre contenu avec un chiffrement d’enveloppe ou CENC, définissez votre stratégie sur « Predefined_MultiDrmCencStreaming ». Cette stratégie indique que vous souhaitez que deux clés de contenu (enveloppe et CENC) soient générées et définies sur le localisateur. Par conséquent, les chiffrements d’enveloppe, de PlayReady et de Widevine sont appliqués (la clé est envoyée au client de la lecture en fonction des licences DRM configurées).

```csharp
StreamingLocator locator = await client.StreamingLocators.CreateAsync(
    resourceGroup,
    accountName,
    locatorName,
    new StreamingLocator
    {
        AssetName = assetName,
        StreamingPolicyName = "Predefined_MultiDrmCencStreaming",
        DefaultContentKeyPolicyName = contentPolicyName
    });
```

Si vous voulez aussi chiffrer votre flux avec CBCS (FairPlay), utilisez la stratégie « Predefined_MultiDrmStreaming ».

## <a name="filtering-ordering-paging"></a>Filtrage, tri, pagination

Consultez [Filtrage, tri et pagination des entités Media Services](entities-overview.md).

## <a name="next-steps"></a>Étapes suivantes

* [Diffuser un fichier](stream-files-dotnet-quickstart.md)
* [Utiliser le chiffrement dynamique AES-128 et le service de distribution des clés](protect-with-aes128.md)
* [Utilisation du chiffrement dynamique DRM et du service de remise des licences](protect-with-drm.md)
