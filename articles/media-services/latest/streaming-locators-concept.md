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
ms.openlocfilehash: a227c5d0edfb9b49e70452cfcfd08b29ba381857
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74969104"
---
# <a name="streaming-locators"></a>Localisateurs de diffusion en continu

Pour rendre les vidéos dans la ressource de sortie disponibles en lecture pour les clients, vous devez créer un [localisateur de diffusion en continu](https://docs.microsoft.com/rest/api/media/streaminglocators) , puis générer des URL de diffusion en continu. Pour générer une URL, vous devez concaténer le nom d’hôte du point de terminaison de streaming et le chemin du localisateur de streaming. Pour un exemple .NET, consultez [Obtenir un localisateur de diffusion en continu](stream-files-tutorial-with-api.md#get-a-streaming-locator).

Le processus de création d’un **localisateur de streaming** est appelée « publication ». Par défaut, le **localisateur de streaming** est valide immédiatement après avoir effectué les appels d’API et dure jusqu’à ce qu’il soit supprimé, sauf si vous configurez les durées de début et de fin optionnelles. 

Lors de la création d’un **Localisateur de streaming**, vous devez spécifier un nom **Ressource** et un nom **Stratégie de streaming**. Pour plus d’informations, consultez les rubriques suivantes :

* [Éléments multimédias](assets-concept.md)
* [Stratégies de diffusion en continu](streaming-policy-concept.md)
* [Stratégies de clé de contenu](content-key-policy-concept.md)

Vous pouvez également spécifier l’heure de début et de fin de votre localisateur de streaming pour permettre à votre utilisateur de lire ce contenu précis (par exemple, entre le 5/1/2019 et le 5/5/2019).  

## <a name="considerations"></a>Considérations

* Les **localisateurs de streaming** ne peuvent pas être mis à jour. 
* Les propriétés des **localisateurs de diffusion en continu** de type DateHeure sont toujours au format UTC.
* Vous devez concevoir un ensemble limité de stratégies pour votre compte Media Services et les réutiliser pour vos éléments localisateurs de diffusion en continu chaque fois que les mêmes options sont nécessaires. Pour plus d’informations, consultez [Quotas et limitations](limits-quotas-constraints.md).

## <a name="create-streaming-locators"></a>Créer des localisateurs de streaming  

### <a name="not-encrypted"></a>Non chiffré

Si vous souhaitez diffuser votre fichier en clair (sans chiffrement), configurez la stratégie de diffusion en continu en clair prédéfinie : « Predefined_ClearStreamingOnly » (dans .NET, vous pouvez utiliser l'enum PredefinedStreamingPolicy.ClearStreamingOnly).

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

Si vous devez chiffrer votre contenu avec le chiffrement CENC, définissez votre stratégie sur « Predefined_MultiDrmCencStreaming ». Le chiffrement Widevine est appliqué à un flux de données DASH et PlayReady à Smooth. La clé est envoyée à un client de lecture en fonction des licences DRM configurées.

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

Si vous souhaitez également chiffrer votre flux HLS avec CBCS (FairPlay), utilisez la stratégie « Predefined_MultiDrmStreaming ».

> [!NOTE]
> Widevine est un service fourni par Google Inc. soumis aux conditions d’utilisation du service et à la politique de confidentialité de Google Inc.

## <a name="associate-filters-with-streaming-locators"></a>Associer des filtres à des localisateurs de streaming

Consultez [Associer des filtres à des localisateurs de streaming](filters-concept.md#associating-filters-with-streaming-locator).

## <a name="filter-order-page-streaming-locator-entities"></a>Filtre, trier et paginer les entités du localisateur de streaming

Consultez [Filtrage, tri et pagination des entités Media Services](entities-overview.md).

## <a name="list-streaming-locators-by-asset-name"></a>Lister les localisateurs de streaming par nom de ressource

Pour obtenir des localisateurs de streaming en fonction du nom de ressource associé, utilisez les opérations suivantes :

|Langage|API|
|---|---|
|REST|[liststreaminglocators](https://docs.microsoft.com/rest/api/media/assets/liststreaminglocators)|
|Interface de ligne de commande|[az ams asset list-streaming-locators](https://docs.microsoft.com/cli/azure/ams/asset?view=azure-cli-latest#az-ams-asset-list-streaming-locators)|
|.NET|[ListStreamingLocators](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.assetsoperationsextensions.liststreaminglocators?view=azure-dotnet#Microsoft_Azure_Management_Media_AssetsOperationsExtensions_ListStreamingLocators_Microsoft_Azure_Management_Media_IAssetsOperations_System_String_System_String_System_String_)|
|Java|[AssetStreamingLocator](https://docs.microsoft.com/java/api/com.microsoft.azure.management.mediaservices.v2018_07_01.assetstreaminglocator?view=azure-java-stable)|
|Node.js|[listStreamingLocators](https://docs.microsoft.com/javascript/api/@azure/arm-mediaservices/assets#liststreaminglocators-string--string--string--msrest-requestoptionsbase-)|

## <a name="also-see"></a>Voir aussi

* [Éléments multimédias](assets-concept.md)
* [Stratégies de diffusion en continu](streaming-policy-concept.md)
* [Stratégies de clé de contenu](content-key-policy-concept.md)

## <a name="next-steps"></a>Étapes suivantes

[Tutoriel : Charger, encoder et diffuser en streaming des vidéos à l’aide de .NET](stream-files-tutorial-with-api.md)
