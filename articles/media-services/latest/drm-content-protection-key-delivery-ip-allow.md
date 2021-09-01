---
title: Restreindre l’accès à la licence DRM et à la distribution de clé AES à l’aide de listes d’adresses IP autorisées
description: Découvrez comment restreindre l’accès aux clés DRM et AES à l’aide de listes d’adresses IP autorisées.
services: media-services
documentationcenter: ''
author: johndeu
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/11/2021
ms.author: johndeu
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: cc0a35c196956d1f6afaf3d3fb27b9f428bb9f6d
ms.sourcegitcommit: e1874bb73cb669ce1e5203ec0a3777024c23a486
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/16/2021
ms.locfileid: "112204758"
---
# <a name="restrict-access-to-drm-license-and-aes-key-delivery-using-ip-allowlists"></a>Restreindre l’accès à la licence DRM et à la distribution de clé AES à l’aide de listes d’adresses IP autorisées

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Lors de la sécurisation des supports avec les fonctions de [Protection du contenu](./drm-content-protection-concept.md) et DRM des services multimédia, vous pouvez rencontrer des scénarios où vous devez limiter la livraison des licences ou des requêtes de clé à une plage spécifique d’IP d’appareils client sur votre réseau. Pour limiter la lecture et la remise de contenu des clés, vous pouvez utiliser la liste d’adresses IP autorisées pour la remise de clés.

En outre, vous pouvez également utiliser la liste d’autorisation pour bloquer complètement tout l’accès Internet public au trafic de remise de clé et autoriser uniquement le trafic à partir de vos points de terminaison de réseau privé.

La liste d’adresses IP autorisées pour la remise de clés limite la remise de licences DRM et de clés AES-128 aux clients au sein de la plage d’adresses IP autorisées fournie.

## <a name="setting-the-allowlist-for-key-delivery"></a>Définition de la liste d’autorisation pour la remise de clés

Les paramètres de la liste d’adresses IP autorisées pour la remise de clés se trouvent sur la ressource de compte Media Services. Lors de la création d’un nouveau compte Media Services, vous pouvez restreindre les plages d’adresses IP autorisées grâce à la propriété **KeyDelivery** de la [ressource de compte Media Services.](/rest/api/media/mediaservices/create-or-update)

La propriété **defaultAction** peut être définie sur « Autoriser » ou « Refuser » pour contrôler la livraison des licences et des clés aux clients dans la plage de la liste d’autorisation.

La propriété **ipAllowList** se compose d’un tableau d’une seule adresse IPv4 et/ou de plages IPv4 utilisant la [notation CIDR](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing#CIDR_notation).

## <a name="setting-the-allowlist-in-the-portal"></a>Définition de la liste d’autorisation dans le portail

Le portail Azure fournit une méthode pour configurer et mettre à jour les listes d’adresses IP autorisées pour la remise de clés.  Connectez-vous à votre compte Media Services et accédez au menu **Livraison de clés** sous **Paramètres**.

## <a name="next-steps"></a>Étapes suivantes

- [Créer un compte](./account-create-how-to.md)
- [Présentation de DRM et de la protection du contenu](./drm-content-protection-concept.md)
