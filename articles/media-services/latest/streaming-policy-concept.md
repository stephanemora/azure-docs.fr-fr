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
ms.date: 05/15/2019
ms.author: juliako
ms.openlocfilehash: 510899e44e4ea4a90e21473ee6af546744c2be2a
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66120232"
---
# <a name="streaming-policies"></a>Stratégies de diffusion en continu

Dans Azure Media Services v3, les [stratégies de diffusion en continu](https://docs.microsoft.com/rest/api/media/streamingpolicies) vous permettent de définir les protocoles de diffusion en continu et les options de chiffrement pour vos [StreamingLocators](streaming-locators-concept.md). Media Services v3 fournit que certaines prédéfinies des stratégies de diffusion en continu afin que vous puissiez les utiliser directement pour la version d’évaluation ou de production. 

Actuellement disponible prédéfinies des stratégies de diffusion en continu :<br/>« Predefined_DownloadOnly », « Predefined_ClearStreamingOnly », « Predefined_DownloadAndClearStreaming », « Predefined_ClearKey », « Predefined_MultiDrmCencStreaming » et « Predefined_MultiDrmStreaming ».

Si vous avez des exigences particulières (par exemple, si vous souhaitez spécifier différents protocoles, avoir besoin d’utiliser un service de remise de clé personnalisé, ou que vous devez utiliser une piste audio clair), vous pouvez créer une stratégie personnalisée de diffusion en continu. 

 
> [!IMPORTANT]
> * Les propriétés des **stratégies de diffusion en continu** de type DateHeure sont toujours au format UTC.
> * Vous devez concevoir un ensemble limité de stratégies pour votre compte Media Services et les réutiliser pour vos éléments localisateurs de diffusion en continu chaque fois que les mêmes options sont nécessaires. Pour plus d’informations, consultez [Quotas et limitations](limits-quotas-constraints.md).

## <a name="decision-tree"></a>Arbre de décision

L’arbre de décision suivant vous aidera à choisir une stratégie prédéfinie de diffusion en continu pour votre scénario.

Cliquez sur l’image pour l’afficher en plein écran.  <br/>
<a href="./media/streaming-policy/large.png" target="_blank"><img src="./media/streaming-policy/small.png"></a> 

## <a name="examples"></a>Exemples

### <a name="not-encrypted"></a>Non chiffré

Si vous souhaitez diffuser votre fichier dans-the-clear (non chiffrés), définir la stratégie de diffusion en continu clair prédéfinie : à « Predefined_ClearStreamingOnly » (dans .NET, vous pouvez utiliser l’énumération PredefinedStreamingPolicy.ClearStreamingOnly).

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
