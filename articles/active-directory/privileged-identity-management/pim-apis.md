---
title: Concepts d’API dans Privileged Identity Management – Azure AD | Microsoft Docs
description: Informations pour comprendre les API dans Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: pim
ms.topic: how-to
ms.date: 05/14/2021
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6b5debb1b0146127238304db5f54a86e38d95edb
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110069451"
---
# <a name="understand-the-privileged-identity-management-apis"></a>Comprendre les API Privileged Identity Management

Vous pouvez effectuer des tâches Privileged Identity Management (PIM) à l’aide des API Microsoft Graph pour les rôles Azure Active Directory (Azure AD) et à l’aide de l’API Azure Resource Manager pour les rôles de ressources Azure (parfois appelés rôles Azure RBAC). Cet article décrit des concepts importants pour l’utilisation des API pour Privileged Identity Management.

Pour toute demande et autres informations sur les API PIM, consultez :

- [Informations de référence sur l’API PIM pour les rôles Azure AD](/graph/api/resources/unifiedroleeligibilityschedulerequest?view=graph-rest-beta&preserve-view=true)
- [Informations de référence sur l’API PIM pour les rôles de ressources Azure](/rest/api/authorization/roleeligibilityschedulerequests)

> [!IMPORTANT]
> API PIM [!INCLUDE [PREVIEW BOILERPLATE](../../../includes/active-directory-develop-preview.md)]

## <a name="pim-api-history"></a>Historique de l’API PIM

Il y a eu plusieurs itérations de l’API PIM au cours des dernières années. Vous trouverez quelques chevauchements dans les fonctionnalités, mais ils ne représentent pas une progression linéaire des versions.

### <a name="iteration-1--only-supports-azure-ad-roles-deprecating"></a>Itération 1 : Prise en charge uniquement des rôles Azure AD, déconseillée

Sous le point de terminaison /beta/privilegedRoles, Microsoft avait une version classique de l’API PIM qui n’est plus prise en charge par la plupart des locataires. Nous sommes en train de déprécier l’accès restant à cette API d’ici le 31 mai.

### <a name="iteration-2--supports-azure-ad-roles-and-azure-resource-roles"></a>Itération 2 : Prise en charge des rôles Azure AD et des rôles de ressources Azure

Sous le point de terminaison /beta/privilegedAccess, Microsoft prenait en charge à la fois /aadRoles et /azureResources. Ce point de terminaison est toujours disponible dans votre locataire, mais Microsoft déconseille de commencer tout nouveau développement avec cette API. Cette API bêta ne sera jamais mise en disponibilité générale et sera un jour dépréciée.

### <a name="current-iteration--azure-ad-roles-in-microsoft-graph-and-azure-resource-roles-in-azure-resource-manager"></a>Itération actuelle : Rôles Azure AD dans Microsoft Graph et rôles de ressources Azure dans Azure Resource Manager

Maintenant en version bêta, Microsoft dispose de la dernière itération de l’API PIM avant sa publication en disponibilité générale. À la suite des commentaires des clients, l’API PIM Azure AD est maintenant sous l’ensemble d’API unifiedRoleManagement et l’API PIM Azure Resource est désormais sous l’API d’attribution de rôles Azure Resource Manager. Ces emplacements offrent également quelques avantages supplémentaires, notamment :

- L’alignement de l’API PIM sur l’API d’attribution de rôle standard pour les rôles Azure AD et les rôles Azure Resource.
- La nécessité moindre d’appeler une API PIM supplémentaire pour intégrer une ressource, obtenir une ressource ou obtenir une définition de rôle.
- La prise en charge des autorisations d’application uniquement.
- De nouvelles fonctionnalités telles que l’approbation et la configuration des notifications par e-mail.

Dans l’itération actuelle, l’API ne prend pas en charge les alertes PIM et les groupes d’accès privilégiés.

## <a name="current-permissions-required"></a>Autorisations requises actuellement

### <a name="azure-ad-roles"></a>Rôles Azure AD

  Pour appeler l’API Graph PIM pour les rôles Azure AD, vous aurez besoin d’au moins l’une des permissions suivantes :

- RoleManagement.ReadWrite.Directory
- RoleManagement.Read.Directory

  Pour spécifier les autorisations requises, le plus simple consiste à utiliser le framework de consentement Azure AD.

### <a name="azure-resource-roles"></a>Rôles de ressources Azure

  L’API PIM pour les rôles de ressources Azure est développée sur l’infrastructure Azure Resource Manager. Vous devrez donner votre consentement à Azure Resource Management, mais n’aurez pas besoin d’autorisation pour l’API Graph. Vous devrez également vous assurer que l’utilisateur ou le principal de service qui appelle l’API a au moins le rôle Propriétaire ou Administrateur de l’accès utilisateur sur la ressource que vous essayez d’administrer.

## <a name="calling-pim-api-with-an-app-only-token"></a>Appel de l’API PIM avec un jeton d’application uniquement

### <a name="azure-ad-roles"></a>Rôles Azure AD

  L’API PIM prend désormais en charge les autorisations d’application uniquement, en plus des autorisations déléguées.

- Pour les autorisations d’application uniquement, vous devez appeler l’API avec une application qui a déjà été consentie avec les autorisations Azure AD ou de rôle Azure requises.
- Pour une autorisation déléguée, vous devez appeler l’API PIM avec un utilisateur et un jeton d’application. L’utilisateur doit être affecté au rôle Administrateur général ou Administrateur de rôle privilégié et s’assurer que le principal de service qui appelle l’API dispose au moins du rôle Propriétaire ou Administrateur de l’accès utilisateur sur la ressource que vous essayez d’administrer.

### <a name="azure-resource-roles"></a>Rôles de ressources Azure

  L’API PIM pour les ressources Azure prend en charge les appels utilisateur uniquement et application uniquement. Assurez-vous simplement que le principal de service a le rôle Propriétaire ou Administrateur de l’accès utilisateur sur la ressource.

## <a name="design-of-current-api-iteration"></a>Conception de l’itération actuelle de l’API

L’API PIM se compose de deux catégories qui sont cohérentes pour l’API des rôles Azure AD et des rôles de ressources Azure : les demandes d’API d’attribution et d’activation et les paramètres de stratégie.

### <a name="assignment-and-activation-api"></a>API d’attribution et d’activation

Pour effectuer des attributions éligibles et des attributions éligibles/actives limitées dans le temps et pour activer les attributions, PIM fournit les entités suivantes :

- RoleAssignmentSchedule
- RoleEligibilitySchedule
- RoleAssignmentScheduleInstance
- RoleEligibilityScheduleInstance
- RoleAssignmentScheduleRequest
- RoleEligibilityScheduleRequest

Ces entités fonctionnent avec les entités préexistantes roleDefinition et roleAssignment pour les rôles Azure AD et les rôles Azure afin de vous permettre de créer des scénarios de bout en bout.

- Si vous essayez de créer ou de récupérer une attribution de rôle persistante (active) qui n’a pas de planification (heure de début ou de fin), vous devez éviter ces entités PIM et vous concentrer sur les opérations de lecture/écriture sous l’entité roleAssignment.

- Pour créer une attribution éligible avec ou sans délai d’expiration, vous pouvez utiliser l’opération d’écriture sur roleEligibilityScheduleRequest.

- Pour créer une attribution persistante (active) avec une planification (heure de début ou de fin), vous pouvez utiliser l’opération d’écriture sur roleAssignmentScheduleRequest.  

- Pour activer une attribution éligible, vous devez également utiliser l’opération d’écriture sur roleAssignmentScheduleRequest avec un paramètre d’action modifié appelé selfActivate.

Chacun des objets de requête crée un objet roleAssignmentSchedule ou roleEligibilitySchedule. Ces objets sont en lecture seule et affichent une planification de toutes les attributions actuelles et futures.

Quand une attribution éligible est activée, l’entité roleEligibilityScheduleInstance continue d’exister. L’entité roleAssignmentScheduleRequest pour l’activation crée un roleAssignmentSchedule et un roleAssignmentScheduleInstance distincts pour cette durée d’activation.

Les objets d’instance sont les attributions réelles qui existent actuellement, qu’il s’agisse d’une attribution éligible ou d’une attribution active. Vous devez utiliser l’opération GET sur l’entité d’instance pour récupérer une liste d’attributions éligibles/attributions actives sur un rôle/utilisateur.

### <a name="policy-setting-api"></a>API de paramètre de stratégie

Pour gérer le paramètre, nous fournissons les entités suivantes :

- roleManagementPolicy
- roleManagementPolicyAssignment

La *stratégie de gestion des rôles* définit le paramètre de la règle. Par exemple, si l’authentification multifacteur/approbation est obligatoire, s’il faut envoyer des notifications par e-mail et à qui ou si les attributions persistantes sont autorisées ou non. L’*attribution de stratégie* associe la stratégie à un rôle spécifique.

Utilisez cette API pour obtenir une liste de tous les objets roleManagementPolicyAssignments, filtrez-la en fonction de l’objet roleDefinitionID que vous souhaitez modifier, puis mettez à jour la stratégie associée à l’objet policyAssignment.

## <a name="relationship-between-pim-entities-and-role-assignment-entities"></a>Relation entre les entités PIM et les entités d’attribution de rôle

Le seul lien entre l’entité PIM et l’entité d’attribution de rôle pour l’attribution persistante (active) pour les rôles Azure AD ou les rôles Azure est l’objet roleAssignmentScheduleInstance. Il existe un mappage univoque entre les deux entités. Ce mappage signifie que roleAssignment et roleAssignmentScheduleInstance comprennent tous deux :  

- Des attributions persistantes (actives) effectuées en dehors de PIM
- Des attributions persistantes (actives) avec une planification effectuée dans PIM
- Des attributions éligibles activées

## <a name="next-steps"></a>Étapes suivantes

- [Référence sur les API Azure AD Privileged Identity Management](/graph/api/resources/privilegedidentitymanagement-root?view=graph-rest-beta&preserve-view=true)