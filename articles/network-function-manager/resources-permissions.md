---
title: Comment inscrire des ressources
titleSuffix: Azure Network Function Manager
description: Découvrez comment inscrire des ressources et créer des identités gérées affectées par l’utilisateur
author: prmitt
ms.service: network-function-manager
ms.topic: how-to
ms.date: 11/02/2021
ms.author: prmitt
ms.custom: ignite-fall-2021
ms.openlocfilehash: e42527e5fdca01b58c08458bc360268b5d36d7d1
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131097676"
---
# <a name="how-to-register-resources-and-create-user-assigned-managed-identities"></a>Comment inscrire des ressources et créer des identités gérées affectées par l’utilisateur

Cet article vous aide à comprendre la configuration requise pour créer des ressources d’appareil, créer des ressources d’application gérées et des identités gérées affectées par l’utilisateur pour le déploiement de fonctions réseau.

## <a name="resource-provider-registration-and-permissions"></a><a name="permissions"></a>Enregistrement et autorisations du fournisseur de ressources

Les ressources du Gestionnaire de fonctions réseau Azure se trouvent dans le fournisseur de ressources Microsoft.HybridNetwork. Inscrivez l’ID d’abonnement auprès du fournisseur de ressources Microsoft.HybridNetwork. Pour plus d’informations sur les fournisseurs de ressources, consultez [Fournisseurs et types de ressources Azure](../azure-resource-manager/management/resource-providers-and-types.md).

### <a name="device-resource-accounts"></a>Comptes de ressources de périphérique

Les comptes que vous utilisez pour créer la ressource d’appareil du Gestionnaire de fonctions réseau doivent être affectés à un rôle personnalisé auquel sont affectés les actions nécessaires à partir de la table suivante. Pour plus d’informations, consultez [Rôles personnalisées](../role-based-access-control/custom-roles.md).

| Nom | Action|
|---|---|
| Microsoft.DataBoxEdge/dataBoxEdgeDevices/read|Obligatoire pour lire la ressource Azure Stack Edge sur laquelle les fonctions réseau seront déployées. |
|Microsoft.DataBoxEdge/dataBoxEdgeDevices/getExtendedInformation/action |Obligatoire pour lire la section des propriétés de la ressource Azure Stack Edge. |
|Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/write |Obligatoire pour créer la ressource du périphérique Gestionnaire de fonctions réseau sur la ressource Azure Stack Edge.|
| Microsoft.HybridNetwork/devices/* | Obligatoire pour créer, mettre à jour, supprimer la ressource du périphérique Gestionnaire de fonctions réseau. |

### <a name="managed-applications-resource-accounts"></a>Comptes de ressources des applications gérées

Les comptes que vous utilisez pour créer la ressource des applications managées doivent être affectés à un [rôle personnalisé](../role-based-access-control/custom-roles.md) auquel sont affectés les actions nécessaires à partir de la table suivante : 

|Nom |Action |
|---|---|
|[Rôle Contributeur d'application managée](../role-based-access-control/built-in-roles.md#managed-application-contributor-role)|Obligatoire pour créer des ressources d’applications managées.|

## <a name="managed-identity"></a><a name="managed-identity"></a>Identité managée

Les partenaires de fonctions réseau qui proposent leurs applications managées Azure avec le Gestionnaire de fonctions réseau offrent une expérience qui vous permet de déployer une application managée associée à une ressource d’appareil du Gestionnaire de fonctions réseau existante. Lorsque vous déployez l’application managée par le partenaire dans le Portail Azure, vous devez fournir une ressource d’identité gérée affectée par l’utilisateur Azure qui a accès à la ressource d’appareil du Gestionnaire de fonctions réseau. L’identité managée affectée par l'utilisateur permet au fournisseur de ressources de l’application managée et au serveur de publication de la fonction réseau d’avoir les autorisations appropriées pour la ressource d’appareil du Gestionnaire de fonctions réseau déployée en dehors du groupe de ressources managées. Pour plus d’informations, consultez [Gérer une identité managée affectée par l’utilisateur dans le Portail Azure](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md).

Pour créer une identité managée affectée par l’utilisateur pour le déploiement de fonctions réseau :

1. Créez une identité managée affectée par l’utilisateur et affectez-la à un rôle personnalisé avec des autorisations pour Microsoft.HybridNetwork/devices/join/action. Pour plus d’informations, consultez [Gérer une identité managée affectée par l’utilisateur dans le Portail Azure](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md).

1. Fournissez cette identité managée lors de la création de l’application managée d’un partenaire dans le Portail Azure. Pour plus d’informations, consultez [Attribuer à une identité managée un accès à une ressource à l’aide du Portail Azure](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez [Gestionnaire de fonctions réseau Azure](faq.md).
