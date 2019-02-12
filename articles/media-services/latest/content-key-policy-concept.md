---
title: Stratégies de clé de contenu dans Media Services - Azure | Microsoft Docs
description: Cet article explique ce que sont les stratégies de clé de contenu et comment Azure Media Services les utilise.
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
ms.custom: seodec18
ms.openlocfilehash: d9e86c45d535862e0c3d02b3f331bc40ebb7f6c7
ms.sourcegitcommit: 947b331c4d03f79adcb45f74d275ac160c4a2e83
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/05/2019
ms.locfileid: "55745119"
---
# <a name="content-key-policies"></a>Stratégies de clé de contenu

Media Services vous permet de transmettre votre contenu dynamique ou à la demande chiffré dynamiquement avec la norme Advanced Encryption Standard (AES-128) ou un des principaux systèmes de gestion des droits numériques (DRM) : Microsoft PlayReady, Google Widevine et Apple FairPlay. Media Services fournit également un service de distribution de clés AES et licences (PlayReady, Widevine et FairPlay) DRM aux clients autorisés.

Pour spécifier des options de chiffrement sur votre flux, vous devez créer la [stratégie de clé de contenu](https://docs.microsoft.com/rest/api/media/contentkeypolicies), puis l'associer à votre **localisateur de streaming**. La **stratégie de clé de contenu** configure la façon dont la clé de contenu est remise aux clients finaux via le composant de remise de clé de Media Services. Vous pouvez laisser Media Services générer automatiquement la clé de contenu. Généralement, vous utilisez une clé à longue durée de vie et vérifiez l'existence des stratégies avec Get. Pour vous procurer la clé, vous devez appeler une méthode d'action distincte afin d'obtenir des informations d'identification ou des secrets ; reportez-vous à l'exemple ci-dessous.

Les **stratégies de clé de contenu** peuvent être mises à jour. Par exemple, vous pouvez mettre à jour la stratégie si vous avez besoin d'effectuer une rotation de clé. Vous pouvez mettre à jour la clé de vérification principale et la liste des clés de vérification alternatives dans la stratégie existante. Cela peut prendre jusqu’à 15 minutes pour que les caches de livraison de clés mettent à jour et récupèrent la stratégie mise à jour. 

> [!IMPORTANT]
> * Les propriétés des **stratégies de clé de contenu** de type DateHeure sont toujours au format UTC.
> * Vous devez concevoir un ensemble limité de stratégies pour votre compte Media Services et les réutiliser pour vos éléments StreamingLocators chaque fois que les mêmes options sont nécessaires. 

## <a name="example"></a>Exemples

Pour accéder à la clé, utilisez **GetPolicyPropertiesWithSecretsAsync**, comme indiqué dans l'exemple ci-dessous.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetOrCreateContentKeyPolicy)]

## <a name="filtering-ordering-paging"></a>Filtrage, tri, pagination

Consultez [Filtrage, tri et pagination des entités Media Services](entities-overview.md).

## <a name="next-steps"></a>Étapes suivantes

* [Utiliser le chiffrement dynamique AES-128 et le service de distribution des clés](protect-with-aes128.md)
* [Utilisation du chiffrement dynamique DRM et du service de remise des licences](protect-with-drm.md)
* [EncodeHTTPAndPublishAESEncrypted](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/tree/master/NETCore/EncodeHTTPAndPublishAESEncrypted)
