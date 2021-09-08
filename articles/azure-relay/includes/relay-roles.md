---
author: spelluru
ms.service: service-bus-relay
ms.topic: include
ms.date: 07/19/2021
ms.author: spelluru
ms.openlocfilehash: e4fd2d455a294b247353e4983c379066d704c87a
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121803214"
---
## <a name="overview"></a>Vue d'ensemble
Quand un principal de sécurité (un utilisateur, un groupe ou une application) tente d’accéder à une entité de Relais, la requête doit être autorisée. Avec Azure AD, l’accès à une ressource est un processus en deux étapes.

1. Pour commencer, l’identité du principal de sécurité est **authentifiée** et un jeton OAuth 2.0 est renvoyé. Le nom de ressource à utiliser pour demander un jeton est `https://relay.azure.net`. Si une application s’exécute dans une entité Azure telle qu’une machine virtuelle Azure, un groupe de machines virtuelles identiques ou une application de fonction Azure, elle peut utiliser une identité managée pour accéder aux ressources.
2. Ensuite, ce jeton est transmis dans le cadre d’une requête adressée au Service de relais pour **autoriser** l’accès à la ressource spécifiée (connexions hybrides, relais WCF). Azure Active Directory (Azure AD) autorise les droits d’accès aux ressources sécurisées par le biais du [contrôle d’accès en fonction du rôle Azure (Azure RBAC)](../../role-based-access-control/overview.md). Azure Relay définit un ensemble de rôles intégrés Azure qui englobent les ensembles communs d’autorisations utilisés pour accéder aux entités de Relais. Vous pouvez également définir des rôles personnalisés pour l’accès aux données. Pour obtenir la liste des rôles intégrés pris en charge par Azure Relay, consultez [rôles intégrés Azure pour Azure Relay](#azure-built-in-roles-for-azure-relay). Les applications natives et les applications Web qui adressent des requêtes au Relais peuvent également autoriser l’accès avec Azure AD.  

## <a name="azure-built-in-roles-for-azure-relay"></a>Rôles intégrés Azure pour Azure Relay
Pour Azure Relay, la gestion des espaces de noms et de toutes les ressources associées via le portail Azure et l’API de gestion des ressources Azure est déjà protégée à l’aide du modèle Azure RBAC. Azure fournit les rôles Azure intégrés ci-dessous pour autoriser l’accès à un espace de noms Relay :

| Rôle | Description | 
| ---- | ----------- | 
| [Propriétaire d’Azure Relay](../../role-based-access-control/built-in-roles.md#azure-relay-owner) | Utiliser ce rôle pour octroyer l’accès **complet** aux ressources Azure Relay. |
| [Écouteur Azure Relay](../../role-based-access-control/built-in-roles.md#azure-relay-listener) | Utiliser ce rôle pour octroyer l’accès **écoute et lecture d’entité** aux ressources Azure Relay. |
| [Expéditeur Azure Relay](../../role-based-access-control/built-in-roles.md#azure-relay-sender) | Utiliser ce rôle pour octroyer l’accès **envoi et lecture d’entité** aux ressources Azure Relay. | 

## <a name="resource-scope"></a>Étendue des ressources
Avant d’attribuer un rôle Azure à un principal de sécurité, déterminez l’étendue de l’accès dont doit disposer le principal de sécurité. Selon les bonnes pratiques, il est toujours préférable d’accorder la plus petite étendue possible.

La liste suivante décrit les niveaux auxquels vous pouvez étendre l’accès aux ressources Azure Relay, en commençant par la plus petite étendue :

- **Entités de relais** : l’attribution de rôle s’applique à une entité de Relais spécifique comme une connexion hybride ou un relais WCF.
- **Espace de noms Relay** : l’attribution de rôle s’applique à toutes les entités de Relais sous l’espace de noms.
- **Groupe de ressources** :l’attribution de rôle s’applique à toutes les ressources Relay sous le groupe de ressources.
- **Abonnement** : l’attribution de rôle s’applique à toutes les ressources Relay dans tous les groupes de ressources de l’abonnement.

> [!NOTE]
> Gardez à l’esprit que la propagation des attributions de rôles Azure peut prendre cinq minutes. Pour plus d’informations sur la définition des rôles intégrés, consultez [Comprendre les définitions de rôles](../../role-based-access-control/role-definitions.md#management-and-data-operations). Pour plus d’informations sur la création de rôles personnalisés Azure, consultez [Rôles personnalisés Azure](../../role-based-access-control/custom-roles.md). 

