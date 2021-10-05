---
title: Vue d’ensemble du contrôle d’accès en fonction du rôle (RBAC) Azure Active Directory
description: Découvrez quelles sont les différentes parties d’une attribution de rôle et ce qu’est la limitation de l’étendue dans Azure Active Directory.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: overview
ms.date: 09/13/2021
ms.author: rolyon
ms.reviewer: abhijeetsinha
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 21e1b91c10d687b0b82626372510dcaf2a0611ef
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128601789"
---
# <a name="overview-of-role-based-access-control-in-azure-active-directory"></a>Vue d’ensemble du contrôle d’accès en fonction du rôle dans Azure Active Directory

Cet article explique ce qu’est le contrôle d’accès en fonction du rôle Azure AD (Azure Active Directory). Les rôles Azure AD vous permettent d’accorder des autorisations précises à vos administrateurs, en respectant le principe du moindre privilège. Les rôles personnalisés et intégrés Azure AD utilisent des concepts similaires à ceux du [système de contrôle d’accès en fonction du rôle pour les ressources Azure](../../role-based-access-control/overview.md) (rôles Azure). La [différence entre ces deux systèmes de contrôle d’accès en fonction du rôle](../../role-based-access-control/rbac-and-directory-admin-roles.md) est la suivante :

- Les rôles Azure AD contrôlent l’accès aux ressources Azure AD telles que les utilisateurs, les groupes et les applications à l’aide de l’API Graph.
- Les rôles Azure contrôlent l’accès aux ressources Azure, telles que les machines virtuelles ou le stockage, à l’aide de Gestion des ressources Azure.

Les deux systèmes contiennent des définitions de rôles et des attributions de rôles à l’usage similaire. Toutefois, les autorisations de rôle Azure AD ne peuvent pas être utilisées dans les rôles personnalisés Azure, et vice versa.

## <a name="understand-azure-ad-role-based-access-control"></a>Présentation du contrôle d’accès en fonction du rôle Azure AD
Azure AD prend en charge 2 types de définitions de rôles :
* [Rôles intégrés](./permissions-reference.md)
* [Rôles personnalisés](./custom-create.md)

Les rôles intégrés sont des rôles prêts à l’emploi qui possèdent un ensemble fixe d’autorisations. Ces définitions de rôles ne peuvent pas être modifiées. Il existe de nombreux rôles [intégrés](./permissions-reference.md) pris en charge par Azure AD, et la liste s’agrandit. Pour s’ajuster au mieux et satisfaire à vos exigences plus complexes, Azure AD prend également en charge les [rôles personnalisés](./custom-create.md). L’octroi d’une autorisation à l’aide de rôles Azure AD personnalisés est un processus en deux étapes qui implique la création d’une définition de rôle personnalisé, puis son affectation à l’aide d’une attribution de rôle. Une définition de rôle personnalisé est une collection d’autorisations que vous ajoutez à partir d’une liste prédéfinie. Il s’agit des mêmes autorisations que celles utilisées dans les rôles intégrés.  

Une fois que vous avez créé votre définition de rôle personnalisé (ou à l’aide d’un rôle intégré), affectez-la à un utilisateur en créant une attribution de rôle. Une attribution de rôle accorde à l’utilisateur les autorisations dans une définition de rôle au niveau d’une étendue spécifiée. Ce processus en deux étapes vous permet de créer une définition de rôle unique et de l’attribuer plusieurs fois à différentes étendues. Une étendue définit l’ensemble des ressources Azure AD auxquelles le membre du rôle a accès. L’étendue la plus courante est l’étendue à l’échelle de l’organisation (échelle-org). Un rôle personnalisé peut être attribué à une étendue d’échelle de l’organisation, ce qui signifie que le membre du rôle dispose des autorisations de rôle sur toutes les ressources de l’organisation. Un rôle personnalisé peut également être attribué au niveau de l’étendue d’objet. Un exemple d’étendue d’objet est une application unique. Le même rôle peut être attribué à un utilisateur sur toutes les applications de l’organisation, puis à un autre utilisateur avec une étendue limitée à l’application Contoso Expense Reports.  

### <a name="how-azure-ad-determines-if-a-user-has-access-to-a-resource"></a>Comment Azure AD détermine si un utilisateur a accès à une ressource

Voici les principales étapes suivies par Azure AD pour déterminer si vous avez accès à une ressource de gestion. Utilisez ces informations pour résoudre les problèmes d’accès.

1. Un utilisateur (ou un principal de service) acquiert un jeton pour le point de terminaison Microsoft Graph ou Azure AD Graph.
1. L’utilisateur effectue un appel d’API à Azure Active Directory (Azure AD) via Microsoft Graph ou Azure AD Graph à l’aide du jeton émis.
1. Selon les circonstances, Azure AD effectue l’une des actions suivantes :
   - Évalue les appartenances aux rôles de l’utilisateur en fonction de la [revendication wids](../develop/access-tokens.md) dans le jeton d’accès de l’utilisateur.
   - Récupère toutes les attributions de rôles qui s’appliquent à l’utilisateur, directement ou via l’appartenance à un groupe, à la ressource sur laquelle l’action est effectuée.
1. Azure AD détermine si l’action contenue dans l’appel d’API est incluse dans les rôles dont l’utilisateur dispose pour cette ressource.
1. Si l’utilisateur n’a aucun rôle avec l’action appropriée dans l’étendue demandée, l’accès n’est pas octroyé. Autrement, l’accès est accordé.

## <a name="role-assignment"></a>Attribution de rôle

Une attribution de rôle est une ressource Azure AD qui attache une *définition de rôle* à un *principal de sécurité* dans une *étendue* donnée pour accorder l’accès aux ressources Azure AD. La création d’une attribution de rôle permet d’accorder un accès, qui peut être révoqué par la suppression d’une attribution de rôle. À son cœur, une attribution de rôle se compose de trois éléments :

- Principal de sécurité - identité qui obtient les autorisations. Il peut s’agir d’un utilisateur, d’un groupe ou d’un principal de service. 
- Définition de rôle - collection d’autorisations. 
- Étendue - moyen de limiter où ces autorisations peuvent être appliquées.

Vous pouvez [créer des attributions de rôles](manage-roles-portal.md) à l’aide du portail Azure, d’Azure AD PowerShell ou de l’API Graph. Vous pouvez également [énumérer les attributions de rôles](view-assignments.md).

Le diagramme suivant montre un exemple d’attribution de rôle. Dans cet exemple, Chris s’est vu attribuer le rôle personnalisé Administrateur d’inscription d’application dans l’étendue de l’inscription de l’application Contoso Widget Builder. Cette attribution accorde à Chris les autorisations du rôle Administrateur d’inscription d’application sur cette inscription d’application spécifique uniquement.

![L’attribution de rôle est le moyen dont les autorisations sont appliquées et comporte trois parties.](./media/custom-overview/rbac-overview.png)

### <a name="security-principal"></a>Principal de sécurité

Un principal de sécurité représente un utilisateur, un groupe ou un principal de service à qui l’accès aux ressources Azure AD est attribué. Un utilisateur est une personne disposant d’un profil utilisateur dans Azure Active Directory. Un groupe est un nouveau groupe Microsoft 365 ou de sécurité dont la propriété isAssignableToRole a la valeur true (actuellement en préversion). Un principal de service est une identité créée pour être utilisée avec des applications, des services hébergés et des outils automatisés pour accéder aux ressources Azure AD.

### <a name="role-definition"></a>Définition de rôle

Une définition de rôle, ou rôle, est une collection d’autorisations. Une définition de rôle répertorie les opérations qui peuvent être effectuées sur des ressources Azure AD, telles que créer, lire, mettre à jour et supprimer. Il existe deux types de rôles dans Azure AD :

- Les rôles intégrés créés par Microsoft qui ne peuvent pas être modifiés.
- Rôles personnalisés créés et gérés par votre organisation.

### <a name="scope"></a>Étendue

Une étendue est un moyen de limiter les actions autorisées à un ensemble spécifique de ressources lors d’une attribution de rôle. Par exemple, si vous souhaitez accorder un rôle personnalisé à un développeur, mais uniquement pour gérer une inscription d’application spécifique, vous pouvez inclure l’inscription d’application spécifique comme étendue dans l’attribution de rôle.

Lorsque vous attribuez un rôle, vous spécifiez l’un des types d’étendue suivants :

- Locataire
- [Unité administrative](administrative-units.md)
- Ressource Azure AD

Si vous spécifiez une ressource Azure AD comme étendue, cela peut être des :

- Groupes Azure AD
- Applications d’entreprise
- Inscriptions d’applications

Pour plus d’informations, consultez [Attribuer des rôles Azure AD à différentes étendues](assign-roles-different-scopes.md).

## <a name="license-requirements"></a>Conditions de licence :

L’utilisation de rôles intégrés dans Azure AD est gratuite, par contre les rôles personnalisés nécessitent une licence Azure AD Premium P1. Pour trouver la licence appropriée à vos besoins, consultez [Comparaison des fonctionnalités mises à la disposition générale des éditions Gratuite et Premium](https://www.microsoft.com/security/business/identity-access-management/azure-ad-pricing).

## <a name="next-steps"></a>Étapes suivantes

- [Comprendre les rôles Azure AD](concept-understand-roles.md)
- [Attribuer des rôles Azure AD aux utilisateurs](manage-roles-portal.md)
- [Créer et affecter un rôle personnalisé](custom-create.md)
