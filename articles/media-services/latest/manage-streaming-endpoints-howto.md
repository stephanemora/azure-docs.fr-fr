---
title: Gérer les points de terminaison de streaming avec Azure Media Services v3
description: Cet article montre comment gérer les points de terminaison de streaming avec Azure Media Services v3.
services: media-services
documentationcenter: ''
author: Juliako
writer: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/11/2020
ms.author: juliako
ms.openlocfilehash: 75ba2ad87eabd7ff6b0625ad95ab24a8ae58dd0f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79461042"
---
# <a name="manage-streaming-endpoints-with--media-services-v3"></a>Gérer les points de terminaison de streaming avec Media Services v3

Une fois votre compte Media Services créé, un [point de terminaison de streaming](streaming-endpoint-concept.md) **par défaut** est ajouté à votre compte dans l’état **Arrêté**. Pour démarrer le streaming de votre contenu et tirer parti de l'[empaquetage dynamique](dynamic-packaging-overview.md) et du [chiffrement dynamique](content-protection-overview.md), le point de terminaison de streaming à partir duquel vous souhaitez diffuser du contenu doit se trouver à l'état **En cours d'exécution**.

Cet article vous montre comment exécuter la commande [start](https://docs.microsoft.com/rest/api/media/streamingendpoints/start) sur votre point de terminaison de streaming en utilisant des technologies différentes. 
 
> [!NOTE]
> Vous êtes facturé uniquement lorsque votre point de terminaison de streaming est en cours d’exécution.
    
## <a name="prerequisites"></a>Prérequis

Révision : 

* [Concepts Media Services](concepts-overview.md)
* [Concept de point de terminaison de streaming](streaming-endpoint-concept.md)
* [mise en package dynamique](dynamic-packaging-overview.md)

## <a name="use-rest"></a>Utiliser REST

```rest
POST https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaservices/slitestmedia10/streamingEndpoints/myStreamingEndpoint1/start?api-version=2018-07-01
```

Pour plus d'informations, consultez les pages suivantes : 

* Documentation de référence de l’exemple [Démarrer un StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints/start).
* Le démarrage d’un point de terminaison de streaming est une opération asynchrone. 

    Pour plus d’informations sur la manière de superviser les opérations de longue durée, consultez [Opérations de longue durée](media-services-apis-overview.md).
* Cette [collection Postman](https://github.com/Azure-Samples/media-services-v3-rest-postman/blob/master/Postman/Media%20Services%20v3.postman_collection.json) contient des exemples de plusieurs opérations REST, notamment sur la façon de démarrer un point de terminaison de streaming.

## <a name="use-the-azure-portal"></a>Utilisation du portail Azure 
 
1. Connectez-vous au [portail Azure](https://portal.azure.com/).
1. Accédez à votre compte Azure Media Services.
1. Dans le volet de gauche, sélectionnez **Points de terminaison de streaming**.
1. Sélectionnez le point de terminaison de streaming que vous voulez démarrer, puis sélectionnez **Démarrer**.

## <a name="use-the-azure-cli"></a>Utilisation de l’interface de ligne de commande Microsoft Azure

```cli
az ams streaming-endpoint start [--account-name]
                                [--ids]
                                [--name]
                                [--no-wait]
                                [--resource-group]
                                [--subscription]
```

Pour plus d’informations, consultez [az ams streaming-endpoint start](https://docs.microsoft.com/cli/azure/ams/streaming-endpoint?view=azure-cli-latest#az-ams-streaming-endpoint-start).

## <a name="use-sdks"></a>Utiliser les kits de développement logiciel

### <a name="java"></a>Java
    
```java
if (streamingEndpoint != null) {
// Start The Streaming Endpoint if it is not running.
if (streamingEndpoint.resourceState() != StreamingEndpointResourceState.RUNNING) {
    manager.streamingEndpoints().startAsync(config.getResourceGroup(), config.getAccountName(), STREAMING_ENDPOINT_NAME).await();
}
```

Consultez l’ensemble de l’[exemple de code Java](https://github.com/Azure-Samples/media-services-v3-java/blob/master/DynamicPackagingVODContent/StreamHLSAndDASH/src/main/java/sample/StreamHLSAndDASH.java#L128).

### <a name="net"></a>.NET

```csharp
StreamingEndpoint streamingEndpoint = await client.StreamingEndpoints.GetAsync(config.ResourceGroup, config.AccountName, DefaultStreamingEndpointName);

if (streamingEndpoint != null)
{
    if (streamingEndpoint.ResourceState != StreamingEndpointResourceState.Running)
    {
        await client.StreamingEndpoints.StartAsync(config.ResourceGroup, config.AccountName, DefaultStreamingEndpointName);
    }
```

Consultez l’ensemble de l’[exemple de code .NET](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/master/DynamicPackagingVODContent/StreamHLSAndDASH/Program.cs#L112).

---

## <a name="next-steps"></a>Étapes suivantes

* [Spécification OpenAPI Media Services v3 (Swagger)](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01)
* [Opérations de point de terminaison de streaming](https://docs.microsoft.com/rest/api/media/streamingendpoints)
