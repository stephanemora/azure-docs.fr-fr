---
title: Localisateurs de diffusion en continu dans Azure Media Services | Microsoft Docs
description: Cet article explique ce que sont les localisateurs de diffusion en continu et comment Azure Media Services les utilise.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 05/26/2019
ms.author: juliako
ms.openlocfilehash: 5897b7df2460257784c40eb974c473573ec4003d
ms.sourcegitcommit: 8c49df11910a8ed8259f377217a9ffcd892ae0ae
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/29/2019
ms.locfileid: "66299166"
---
# <a name="streaming-locators"></a>Localisateurs de diffusion en continu

Pour rendre les vidéos dans la ressource de sortie disponibles en lecture pour les clients, vous devez créer un [localisateur de diffusion en continu](https://docs.microsoft.com/rest/api/media/streaminglocators) , puis générer des URL de diffusion en continu. Pour générer une URL, vous devez concaténer le nom d’hôte de point de terminaison de diffusion en continu et le chemin d’accès du localisateur de diffusion en continu. Pour un exemple .NET, consultez [Obtenir un localisateur de diffusion en continu](stream-files-tutorial-with-api.md#get-a-streaming-locator).

Le processus de création d’un **localisateur de streaming** est appelée « publication ». Par défaut, le **localisateur de streaming** est valide immédiatement après avoir effectué les appels d’API et dure jusqu’à ce qu’il soit supprimé, sauf si vous configurez les durées de début et de fin optionnelles. 

Lorsque vous créez un **localisateur de diffusion en continu**, vous devez spécifier un **Asset** nom et un **stratégie de diffusion en continu** nom. Pour plus d’informations, consultez les rubriques suivantes :

* [Éléments multimédias](assets-concept.md)
* [Stratégies de diffusion en continu](streaming-policy-concept.md)
* [Stratégies de clé de contenu](content-key-policy-concept.md)

Vous pouvez également spécifier l’heure de début et de fin sur le localisateur de diffusion en continu, ce qui permet uniquement de votre utilisateur lire le contenu entre ces heures (par exemple, entre 1/5/2019 à 5/5/2019).  

## <a name="considerations"></a>Considérations

* **Localisateurs de streaming** ne sont pas modifiables. 
* Les propriétés des **localisateurs de diffusion en continu** de type DateHeure sont toujours au format UTC.
* Vous devez concevoir un ensemble limité de stratégies pour votre compte Media Services et les réutiliser pour vos éléments localisateurs de diffusion en continu chaque fois que les mêmes options sont nécessaires. Pour plus d’informations, consultez [Quotas et limitations](limits-quotas-constraints.md).

## <a name="create-streaming-locators"></a>Créer des localisateurs de diffusion en continu  

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

Si vous avez besoin chiffrer votre contenu avec le chiffrement de CENC, définir votre stratégie pour « Predefined_MultiDrmCencStreaming ». Le chiffrement Widevine s’appliqueront à un flux DASH et de PlayReady à Smooth. La clé sera envoyée à un client de la lecture basé sur les licences DRM configurés.

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

Si vous souhaitez également chiffrer votre flux HLS avec CBCS (FairPlay), utilisez 'Predefined_MultiDrmStreaming'.

## <a name="associate-filters-with-streaming-locators"></a>Associer des filtres avec des localisateurs de diffusion en continu

Consultez [filtres : associer avec des localisateurs de diffusion en continu](filters-concept.md#associating-filters-with-streaming-locator).

## <a name="filter-order-page-streaming-locator-entities"></a>Filtre, par ordre, les entités de localisateurs de diffusion en continu de page

Consultez [Filtrage, tri et pagination des entités Media Services](entities-overview.md).

## <a name="list-streaming-locators-by-asset-name"></a>Liste des localisateurs de diffusion en continu par le nom de la ressource

Pour obtenir les localisateurs de diffusion en continu en fonction du nom de ressource associé, utilisez les opérations suivantes :

|Langue|API|
|---|---|
|REST|[liststreaminglocators](https://docs.microsoft.com/rest/api/media/assets/liststreaminglocators)|
|Interface de ligne de commande|[az ams asset list-streaming-locators](https://docs.microsoft.com/cli/azure/ams/asset?view=azure-cli-latest#az-ams-asset-list-streaming-locators)|
|.NET|[ListStreamingLocators](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.assetsoperationsextensions.liststreaminglocators?view=azure-dotnet#Microsoft_Azure_Management_Media_AssetsOperationsExtensions_ListStreamingLocators_Microsoft_Azure_Management_Media_IAssetsOperations_System_String_System_String_System_String_)|
|Java|[AssetStreamingLocator](https://docs.microsoft.com/java/api/com.microsoft.azure.management.mediaservices.v2018_07_01.assetstreaminglocator?view=azure-java-stable)|
|Node.js|[listStreamingLocators](https://docs.microsoft.com/javascript/api/azure-arm-mediaservices/assets?view=azure-node-latest#liststreaminglocators-string--string--string--object-)|

## <a name="also-see"></a>Voir aussi

* [Éléments multimédias](assets-concept.md)
* [Stratégies de diffusion en continu](streaming-policy-concept.md)
* [Stratégies de clé de contenu](content-key-policy-concept.md)

## <a name="next-steps"></a>Étapes suivantes

[Tutoriel : Charger, encoder et diffuser en streaming des vidéos à l’aide de .NET](stream-files-tutorial-with-api.md)
