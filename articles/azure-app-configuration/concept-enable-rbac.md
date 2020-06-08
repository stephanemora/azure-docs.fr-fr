---
title: Autoriser l’accès à Azure App Configuration Azure avec Azure Active Directory
description: Activer RBAC pour autoriser l’accès à votre instance Azure App Configuration
author: lisaguthrie
ms.author: lcozzens
ms.date: 02/13/2020
ms.topic: conceptual
ms.service: azure-app-configuration
ms.openlocfilehash: 3ec30aafe63259237a89de6597970b908fb969cf
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83773438"
---
# <a name="authorize-access-to-azure-app-configuration-using-azure-active-directory"></a>Autoriser l’accès à Azure App Configuration Azure avec Azure Active Directory
Azure App Configuration prend en charge l’utilisation d’Azure Active Directory (Azure AD) pour autoriser les requêtes adressées aux instances App Configuration.  Azure AD vous permet d’utiliser le contrôle d’accès en fonction du rôle (RBAC) pour accorder des autorisations à un principal de sécurité.  Un principal de sécurité peut correspondre à un utilisateur ou à un [principal du service d’application](../active-directory/develop/app-objects-and-service-principals.md).  Pour en savoir plus sur les rôles et les attributions de rôles, consultez [Comprendre les différents rôles](../role-based-access-control/overview.md).

## <a name="overview"></a>Vue d’ensemble
Les requêtes effectuées par un principal de sécurité (un utilisateur ou une application) pour accéder à une ressource App Configuration doivent être autorisées.  Avec Azure AD, l’accès à une ressource est un processus en deux étapes.
1. L’identité du principal de sécurité est authentifiée, et un jeton OAuth 2.0 est renvoyé.  Le nom de ressource pour demander un jeton est `https://login.microsoftonline.com/{tenantID}` où `{tenantID}` correspond à l’ID de locataire Azure Active Directory auquel appartient le principal de service.
2. Le jeton est transmis dans une requête adressée au service App Configuration pour autoriser l’accès à la ressource spécifiée.

L’étape d’authentification nécessite qu’une requête d’application contienne un jeton d’accès OAuth 2.0 au moment de l’exécution.  Si une application s’exécute à partir d’une entité Azure telle qu’une application Azure Functions, une application web Azure ou une machine virtuelle Azure, elle peut utiliser une identité managée pour accéder aux ressources.  Pour plus d’informations sur l’authentification des requêtes adressées à Azure App Configuration par une identité managée, consultez [Authentifier l’accès aux ressources Azure App Configuration avec Azure Active Directory et les identités managées pour les ressources Azure](howto-integrate-azure-managed-service-identity.md).

L’étape d’autorisation exige qu’un ou plusieurs rôles RBAC soient attribués au principal de sécurité. Azure App Configuration fournit des rôles RBAC qui englobent des ensembles d’autorisations pour les ressources App Configuration. Les rôles qui sont attribués à un principal de sécurité déterminent les autorisations fournies au principal. Pour plus d’informations sur les rôles RBAC, consultez [Rôles RBAC intégrés pour Azure App Configuration](#built-in-rbac-roles-for-azure-app-configuration). 

## <a name="assign-rbac-roles-for-access-rights"></a>Attribuer des rôles RBAC pour les droits d’accès
Azure Active Directory (Azure AD) autorise les droits d’accès aux ressources sécurisées via [RBAC (contrôle d’accès en fonction du rôle)](../role-based-access-control/overview.md).

Lorsqu’un rôle RBAC est attribué à un principal de sécurité Azure AD, Azure octroie l’accès à ces ressources pour ce principal de sécurité. L’accès est limité à la ressource App Configuration. Un principal de sécurité Azure AD peut correspondre à un utilisateur, à un principal de service d’application ou à une [identité managée pour les ressources Azure](../active-directory/managed-identities-azure-resources/overview.md).

## <a name="built-in-rbac-roles-for-azure-app-configuration"></a>Rôles RBAC intégrés pour Azure App Configuration
Azure fournit les rôles RBAC intégrés suivants pour autoriser l’accès aux données App Configuration à l’aide d’Azure AD et d’OAuth :

- **Propriétaire des données App Configuration** : Utilisez ce rôle pour accorder un accès en lecture/écriture/suppression aux données App Configuration. Cela n’accorde pas l’accès à la ressource App Configuration.
- **Lecteur des données App Configuration** : Utilisez ce rôle pour accorder un accès en lecture aux données App Configuration. Cela n’accorde pas l’accès à la ressource App Configuration.
- **Collaborateur** : Utilisez ce rôle pour gérer la ressource App Configuration. Alors que les données App Configuration sont accessibles à l’aide de clés d’accès, ce rôle n’accorde pas l’accès aux données à l’aide d’Azure AD.
- **Lecteur** : Utilisez ce rôle pour accorder un accès en lecture à la ressource App Configuration. Cela n’accorde pas l’accès aux clés d’accès de la ressource ni aux données stockées dans App Configuration.

## <a name="next-steps"></a>Étapes suivantes
En savoir plus sur l’utilisation des [identités managées](howto-integrate-azure-managed-service-identity.md) pour gérer votre service App Configuration.
