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
ms.date: 05/22/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 68778cea51144ec33efd4d5843a51b489ea17ca4
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66155738"
---
# <a name="content-key-policies"></a>Stratégies de clé de contenu

Media Services vous permet de transmettre votre contenu dynamique ou à la demande chiffré dynamiquement avec la norme Advanced Encryption Standard (AES-128) ou un des principaux systèmes de gestion des droits numériques (DRM) : Microsoft PlayReady, Google Widevine et Apple FairPlay. Media Services fournit également un service de distribution de clés AES et licences (PlayReady, Widevine et FairPlay) DRM aux clients autorisés.

Pour spécifier les options de chiffrement sur votre flux, vous devez créer un [stratégie de diffusion en continu](streaming-policy-concept.md) et associez-la à votre [localisateur de diffusion en continu](streaming-locators-concept.md). Vous devez créer un [stratégie de la clé de contenu](https://docs.microsoft.com/rest/api/media/contentkeypolicies) pour configurer comment la clé de contenu (qui fournit un accès sécurisé à votre [actifs](assets-concept.md)) est remis pour les clients. Le **stratégie clé de contenu** est également associé à votre **localisateur de diffusion en continu**. Vous devez définir la configuration requise (restrictions) sur la stratégie de clé de contenu qui doivent être remplies dans l’ordre des clés avec la configuration spécifiée à remettre aux clients. 

Il est recommandé de laisser Media Services pour générer automatiquement les clés de contenu. En règle générale, vous utilisez une clé de longue durée et vérifier l’existence de stratégies avec **obtenir**. Pour vous procurer la clé, vous devez appeler une méthode d'action distincte afin d'obtenir des informations d'identification ou des secrets ; reportez-vous à l'exemple ci-dessous.

Les **stratégies de clé de contenu** peuvent être mises à jour. Par exemple, vous pouvez mettre à jour la stratégie si vous avez besoin d'effectuer une rotation de clé. Vous pouvez mettre à jour la clé de vérification principale et la liste des clés de vérification alternatives dans la stratégie existante. Cela peut prendre jusqu’à 15 minutes pour que les caches de livraison de clés mettent à jour et récupèrent la stratégie mise à jour. 

> [!IMPORTANT]
> * Les propriétés des **stratégies de clé de contenu** de type DateHeure sont toujours au format UTC.
> * Vous devez concevoir un ensemble limité de stratégies pour votre compte Media Services et les réutiliser pour vos éléments StreamingLocators chaque fois que les mêmes options sont nécessaires. Pour plus d’informations, consultez [Quotas et limitations](limits-quotas-constraints.md).

## <a name="example"></a>Exemples

Pour accéder à la clé, utilisez **GetPolicyPropertiesWithSecretsAsync**, comme indiqué dans l'exemple ci-dessous.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetOrCreateContentKeyPolicy)]

## <a name="filtering-ordering-paging"></a>Filtrage, tri, pagination

Consultez [Filtrage, tri et pagination des entités Media Services](entities-overview.md).

## <a name="next-steps"></a>Étapes suivantes

* [Utiliser le chiffrement dynamique AES-128 et le service de distribution des clés](protect-with-aes128.md)
* [Utilisation du chiffrement dynamique DRM et du service de remise des licences](protect-with-drm.md)
* [EncodeHTTPAndPublishAESEncrypted](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/tree/master/NETCore/EncodeHTTPAndPublishAESEncrypted)
