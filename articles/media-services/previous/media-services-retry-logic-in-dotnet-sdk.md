---
title: Logique de nouvelle tentative dans le Kit de développement logiciel (SDK) Media Services pour .NET | Microsoft Docs
description: La rubrique fournit une vue d’ensemble de la logique de nouvelle tentative dans le Kit de développement logiciel (SDK) pour .NET.
author: Juliako
manager: cfowler
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 527b61a6-c862-4bd8-bcbc-b9aea1ffdee3
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/21/2017
ms.author: juliako
ms.openlocfilehash: 34125712c59938b3a74e7cdc150f3f16b694b92f
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2018
ms.locfileid: "33785938"
---
# <a name="retry-logic-in-the-media-services-sdk-for-net"></a>Logique de nouvelle tentative dans le Kit de développement logiciel (SDK) Media Services pour .NET
Lorsque vous utilisez les services Microsoft Azure, des erreurs temporaires peuvent se produire. Si une erreur temporaire se produit, dans la plupart des cas, après plusieurs tentatives, l’opération aboutit. Le Kit de développement logiciel (SDK) Media Services pour .NET implémente la logique de nouvelle tentative pour gérer des défaillances temporaires liées à des exceptions et erreurs générées par des requêtes web, l’exécution de requêtes, l’enregistrement de modifications et des opérations de stockage.  Par défaut, le Kit de développement logiciel (SDK) Media Services pour .NET effectue quatre nouvelles tentatives avant de lever une nouvelle exception pour votre application. Le code de votre application doit ensuite gérer cette exception correctement.  

 Voici une brève indication des stratégies Web Request, Storage, Query et SaveChanges :  

* La stratégie Storage est utilisée pour les opérations de stockage d’objets blob (chargements ou téléchargement de fichiers de ressources).  
* La stratégie Web Request est utilisée pour les demandes web génériques (par exemple, pour obtenir un jeton d’authentification et résoudre le point de terminaison du cluster de l’utilisateur).  
* La stratégie Query permet d’interroger des entités à partir de REST (par exemple, mediaContext.Assets.Where(...)).  
* La stratégie SaveChanges permet d’effectuer toute opération qui modifie des données au sein du service (par exemple, création d’une entité, mise à jour d’une entité, appel d’une fonction de service pour une opération).  
  
  Cette rubrique répertorie les types d’exceptions et codes d’erreur qui sont gérés par le Kit de développement logiciel (SDK) Media Services pour la logique de nouvelle tentative .NET.  

## <a name="exception-types"></a>Types d'exceptions
Le tableau suivant décrit les exceptions que le Kit de développement logiciel (SDK) Media Services pour .NET gère ou ne gère pas pour certaines opérations susceptibles de provoquer des défaillances temporaires.  

| Exception | Web Request | Stockage | Requête | SaveChanges |
| --- | --- | --- | --- | --- |
| WebException<br/>Pour plus d’informations, voir la section [Codes d’état WebException](media-services-retry-logic-in-dotnet-sdk.md#WebExceptionStatus). |OUI |OUI |OUI |OUI |
| DataServiceClientException<br/> Pour plus d’informations, voir [Codes d’état d’erreur HTTP](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode). |Non  |OUI |OUI |OUI |
| DataServiceQueryException<br/> Pour plus d’informations, voir [Codes d’état d’erreur HTTP](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode). |Non  |OUI |OUI |OUI |
| DataServiceRequestException<br/> Pour plus d’informations, voir [Codes d’état d’erreur HTTP](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode). |Non  |OUI |OUI |OUI |
| DataServiceTransportException |Non  |Non  |OUI |OUI |
| TimeoutException |OUI |OUI |OUI |Non  |
| SocketException |OUI |OUI |OUI |OUI |
| StorageException |Non  |OUI |Non  |Non  |
| IOException |Non  |OUI |Non  |Non  |

### <a name="WebExceptionStatus"></a> Codes d’état WebException
Le tableau suivant présente les codes d’erreur WebException pour lesquels la logique de nouvelle tentative est implémentée. L’énumération [WebExceptionStatus](http://msdn.microsoft.com/library/system.net.webexceptionstatus.aspx) définit les codes d’état.  

| Statut | Web Request | Stockage | Requête | SaveChanges |
| --- | --- | --- | --- | --- |
| ConnectFailure |OUI |OUI |OUI |OUI |
| NameResolutionFailure |OUI |OUI |OUI |OUI |
| ProxyNameResolutionFailure |OUI |OUI |OUI |OUI |
| SendFailure |OUI |OUI |OUI |OUI |
| PipelineFailure |OUI |OUI |OUI |Non  |
| ConnectionClosed |OUI |OUI |OUI |Non  |
| KeepAliveFailure |OUI |OUI |OUI |Non  |
| UnknownError |OUI |OUI |OUI |Non  |
| ReceiveFailure |OUI |OUI |OUI |Non  |
| RequestCanceled |OUI |OUI |OUI |Non  |
| Délai d'expiration |OUI |OUI |OUI |Non  |
| ProtocolError <br/>La nouvelle tentative sur ProtocolError est contrôlée par la gestion des codes d’état HTTP. Pour plus d’informations, voir [Codes d’état d’erreur HTTP](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode). |OUI |OUI |OUI |OUI |

### <a name="HTTPStatusCode"></a> Codes d’état d’erreur HTTP
Lorsque les opérations Query et SaveChanges lèvent les exceptions DataServiceClientException, DataServiceQueryException ou DataServiceQueryException, le code d’état d’erreur HTTP est retourné dans la propriété StatusCode.  Le tableau suivant présente les codes d’erreur pour lesquels la logique de nouvelle tentative est implémentée.  

| Statut | Web Request | Stockage | Requête | SaveChanges |
| --- | --- | --- | --- | --- |
| 401 |Non  |OUI |Non  |Non  |
| 403 |Non  |OUI<br/>Gestion des nouvelles tentatives avec attentes plus longues. |Non  |Non  |
| 408 |OUI |OUI |OUI |OUI |
| 429 |OUI |OUI |OUI |OUI |
| 500 |OUI |OUI |OUI |Non  |
| 502 |OUI |OUI |OUI |Non  |
| 503 |OUI |OUI |OUI |OUI |
| 504 |OUI |OUI |OUI |Non  |

Pour l’implémentation réelle du Kit de développement logiciel (SDK) Media Services pour la logique de nouvelle tentative .NET, voir [azure-sdk-for-media-services](https://github.com/Azure/azure-sdk-for-media-services/tree/dev/src/net/Client/TransientFaultHandling).

## <a name="next-steps"></a>Étapes suivantes
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fournir des commentaires
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

