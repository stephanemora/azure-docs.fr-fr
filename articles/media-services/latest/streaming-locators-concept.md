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
ms.date: 02/03/2019
ms.author: juliako
ms.openlocfilehash: be66dcf8115258b6f593ec913e75785a3f8dbe1f
ms.sourcegitcommit: 947b331c4d03f79adcb45f74d275ac160c4a2e83
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/05/2019
ms.locfileid: "55743478"
---
# <a name="streaming-locators"></a>Localisateurs de diffusion en continu

Pour rendre les vidéos dans la ressource de sortie disponibles en lecture pour les clients, vous devez créer un [localisateur de diffusion en continu](https://docs.microsoft.com/rest/api/media/streaminglocators) , puis générer des URL de diffusion en continu. Pour un exemple .NET, consultez [Obtenir un localisateur de diffusion en continu](stream-files-tutorial-with-api.md#get-a-streaming-locator).

Le processus de création d’un **localisateur de streaming** est appelée « publication ». Par défaut, le **localisateur de streaming** est valide immédiatement après avoir effectué les appels d’API et dure jusqu’à ce qu’il soit supprimé, sauf si vous configurez les durées de début et de fin optionnelles. 

Lors de la création d’un **Localisateur de diffusion en continu**, vous devez spécifier le nom [Ressource](https://docs.microsoft.com/rest/api/media/assets) et le nom [Stratégie de diffusion en continu](https://docs.microsoft.com/rest/api/media/streamingpolicies). Vous pouvez utiliser l’une des stratégies de diffusion en continu prédéfinies ou en créer une personnalisée. Voici les stratégies prédéfinies disponibles actuellement : « Predefined_DownloadOnly », « Predefined_ClearStreamingOnly », « Predefined_DownloadAndClearStreaming », « Predefined_ClearKey », « Predefined_MultiDrmCencStreaming » et « Predefined_MultiDrmStreaming ». Lorsque vous utilisez une stratégie de diffusion en continu personnalisée, vous devez concevoir un ensemble limité de ces stratégies pour votre compte Media Services et les réutiliser pour vos localisateurs de diffusion en continu chaque fois que les mêmes protocoles et options sont nécessaires. 

Si vous voulez spécifier des options de chiffrement sur votre flux, créez la [stratégie de clé de contenu](https://docs.microsoft.com/rest/api/media/contentkeypolicies) qui configure la façon dont la clé de contenu est remise aux clients finaux via le composant de remise de clé de Media Services. Associez votre localisateur de diffusion en continu à la **stratégie de la clé de contenu** et à la clé de contenu. Vous pouvez laisser Media Services générer automatiquement la clé. L’exemple .NET suivant montre comment configurer le chiffrement AES avec une restriction par jeton dans Media Services v3 : [EncodeHTTPAndPublishAESEncrypted](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/tree/master/NETCore/EncodeHTTPAndPublishAESEncrypted). Les **stratégies de clé de contenu** peuvent être mises à jour, vous souhaiterez peut-être mettre à jour la stratégie si vous avez besoin d’effectuer une rotation de clé. Cela peut prendre jusqu’à 15 minutes pour que les caches de livraison de clés mettent à jour et récupèrent la stratégie mise à jour. Il est recommandé de ne pas créer une nouvelle stratégie de clé de contenu pour chaque localisateur de diffusion en continu. Vous devez tenter de réutiliser les stratégies existantes chaque fois que les mêmes options sont nécessaires.

> [!IMPORTANT]
> * Les propriétés des **localisateurs de diffusion en continu** de type DateHeure sont toujours au format UTC.
> * Vous devez concevoir un ensemble limité de stratégies pour votre compte Media Services et les réutiliser pour vos éléments localisateurs de diffusion en continu chaque fois que les mêmes options sont nécessaires. 

## <a name="filtering-ordering-paging"></a>Filtrage, tri, pagination

Consultez [Filtrage, tri et pagination des entités Media Services](entities-overview.md).

## <a name="next-steps"></a>Étapes suivantes

* [Tutoriel : Charger, encoder et diffuser en streaming des vidéos à l’aide de .NET](stream-files-tutorial-with-api.md)
* [Utilisation du chiffrement dynamique DRM et du service de remise des licences](protect-with-drm.md)
