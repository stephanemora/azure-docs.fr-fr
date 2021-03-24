---
title: Autoriser l’accès avec Azure Active Directory
description: Cet article fournit des informations sur l’autorisation d’accès aux ressources Azure SignalR Service avec Azure Active Directory.
author: terencefan
ms.author: tefa
ms.date: 08/03/2020
ms.service: signalr
ms.topic: conceptual
ms.openlocfilehash: 37c2e41b5c81f941245b895ecd144baee3b9db6d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97797382"
---
# <a name="authorize-access-to-azure-signalr-service-resources-using-azure-active-directory"></a>Autoriser l’accès aux ressources Azure SignalR Service avec Azure Active Directory
Azure SignalR Service prend en charge l’utilisation d’Azure Active Directory (Azure AD) pour autoriser les requêtes d’accès aux ressources Azure SignalR Service. Avec Azure AD, vous pouvez utiliser le contrôle d’accès en fonction du rôle (RBAC) pour accorder des autorisations à un principal de sécurité, qui peut être un utilisateur ou un principal de service d’application. Pour en savoir plus sur les rôles et les attributions de rôles, consultez [Comprendre les différents rôles](../role-based-access-control/overview.md).

## <a name="overview"></a>Vue d’ensemble
Lorsqu’un principal de sécurité (une application) tente d’accéder à une ressource Azure SignalR Service, la requête doit être autorisée. Avec Azure AD, l’accès à une ressource est un processus en deux étapes. 

 1. Pour commencer, l’identité du principal de sécurité est authentifiée, et un jeton OAuth 2.0 est renvoyé. Le nom de ressource à utiliser pour demander un jeton est `https://signalr.azure.com/`.
 2. Ensuite, ce jeton est transmis dans le cadre d’une requête adressée à Azure SignalR Service pour autoriser l’accès à la ressource spécifiée.

L’étape d’authentification nécessite qu’une requête d’application contienne un jeton d’accès OAuth 2.0 au moment de l’exécution. Si votre serveur hub s’exécute à l’intérieur d’une entité Azure telle qu’une machine virtuelle Azure, un groupe de machines virtuelles identiques ou une application Azure Function, il peut utiliser une identité managée pour accéder aux ressources. Pour plus d’informations sur l’authentification des requêtes adressées par une identité managée à Azure SignalR Service, consultez [Authentifier l’accès aux ressources Azure Service Bus avec Azure Active Directory et les identités managées pour les ressources Azure](authenticate-managed-identity.md). 

L’étape d’autorisation exige qu’un ou plusieurs rôles RBAC soient attribués au principal de sécurité. Azure SignalR Service fournit des rôles RBAC qui englobent des ensembles d’autorisations pour les ressources Azure SignalR. Les rôles qui sont attribués à un principal de sécurité déterminent les autorisations dont disposera le principal. Pour plus d’informations sur les rôles RBAC, consultez [Rôles intégrés Azure pour Azure SignalR Service](#azure-built-in-roles-for-azure-signalr-service). 

Le serveur Hub SignalR qui n’est pas exécuté dans une entité Azure peut être également autorisé avec Azure AD. Pour savoir comment demander un jeton d’accès et l’utiliser pour autoriser les requêtes aux ressources Azure SignalR Service, consultez [Authentifier l'accès à Azure SignalR Service avec Azure AD à partir d’une application](authenticate-application.md). 

## <a name="azure-built-in-roles-for-azure-signalr-service"></a>Rôles intégrés Azure pour Azure SignalR Service

- [Serveur d’application SignalR]
- [Lecteur SignalR Service]
- [Propriétaire SignalR Service]

## <a name="assign-rbac-roles-for-access-rights"></a>Attribuer des rôles RBAC pour les droits d’accès
Azure Active Directory (Azure AD) autorise les droits d’accès aux ressources sécurisées via [RBAC (contrôle d’accès en fonction du rôle)](../role-based-access-control/overview.md). Azure SignalR Service définit un ensemble de rôles intégrés Azure qui englobent les ensembles d’autorisations couramment utilisés pour accéder à Azure SignalR Service. Il est également possible de définir des rôles personnalisés pour accéder à la ressource.

Lorsqu’un rôle RBAC est attribué à un principal de sécurité Azure AD, Azure octroie l’accès à ces ressources pour ce principal de sécurité. L’accès peut être étendu au niveau de l’abonnement, du groupe de ressources ou de toute ressource Azure SignalR Service. Un principal de sécurité Azure AD peut correspondre à un utilisateur ou a une application ou à une [identité managée pour les ressources Azure](../active-directory/managed-identities-azure-resources/overview.md).

## <a name="built-in-roles-for-azure-signalr-service"></a>Rôles intégrés pour Azure SignalR Service
Azure fournit les rôles intégrés Azure suivants pour autoriser l’accès aux ressources Azure SignalR Service à l’aide d’Azure AD et d’OAuth :

### <a name="signalr-app-server"></a>Serveur d’application SignalR

Utilisez ce rôle pour autoriser l’accès à Obtenir une clé d’accès temporaire pour la signature des jetons client.

### <a name="signalr-serverless-contributor"></a>Contributeur SignalR Serverless

Utilisez ce rôle pour autoriser l’accès à Obtenir directement un jeton client auprès de Azure SignalR Service.

## <a name="next-steps"></a>Étapes suivantes

Consultez les articles associés suivants :

- [Authentifier une application avec Azure AD pour accéder à Azure SignalR Service](authenticate-application.md)
- [Authentifier une identité managée avec Azure AD pour accéder à Azure SignalR Service](authenticate-managed-identity.md)