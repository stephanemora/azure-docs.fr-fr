---
title: Azure Policy pour l’authentification Azure Active Directory uniquement
description: Cet article fournit des informations sur la façon d’appliquer une stratégie Azure pour créer une base de données Azure SQL ou une instance managée Azure SQL avec l’authentification Azure AD (Azure Active Directory) uniquement activée.
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
ms.service: sql-db-mi
ms.subservice: security
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 09/22/2021
ms.openlocfilehash: 672a46b39a726d5fd21a8c2d740008e169cf1e2c
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128700523"
---
# <a name="azure-policy-for-azure-active-directory-only-authentication-with-azure-sql"></a>Azure Policy pour l’authentification Azure Active Directory uniquement avec Azure SQL

[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

> [!NOTE]
> L’**authentification Azure AD uniquement** et la fonctionnalité Azure Policy associée présentée dans cet article sont en **préversion publique**. 

Azure Policy peut imposer la création d’une base de données Azure SQL ou d’une instance managée Azure SQL avec l’[authentification Azure AD](authentication-azure-ad-only-authentication.md) uniquement activée pendant le provisionnement. Une fois cette stratégie en place, toute tentative de création d’un [serveur logique dans Azure](logical-servers.md) ou d’une instance managée échoue s’il ou elle n’est pas créé(e) avec l’authentification Azure AD uniquement activée.

La stratégie Azure peut être appliqué à l’ensemble de l’abonnement Azure, ou uniquement au sein d’un groupe de ressources.

Deux nouvelles stratégies intégrées ont été introduites dans Azure Policy :

- Azure SQL Database doit avoir uniquement l’authentification Azure Active Directory activée
- Azure SQL Managed Instance doit avoir uniquement l’authentification Azure Active Directory activée

Pour plus d’informations sur Azure Policy, consultez [Présentation d’Azure Policy](/azure/governance/policy/overview) et [Structure de définition Azure Policy](/azure/governance/policy/concepts/definition-structure).

## <a name="permissions"></a>Autorisations

Pour obtenir une vue d’ensemble des autorisations nécessaires à la gestion d’Azure Policy, consultez [Autorisations Azure RBAC dans Azure Policy](/azure/governance/policy/overview#azure-rbac-permissions-in-azure-policy).

### <a name="actions"></a>Actions

Si vous utilisez un rôle personnalisé pour gérer Azure Policy, les [actions](/azure/role-based-access-control/role-definitions#actions) suivantes sont nécessaires.

- */read
- Microsoft.Authorization/policyassignments/*
- Microsoft.Authorization/policydefinitions/*
- Microsoft.Authorization/policyexemptions/*
- Microsoft.Authorization/policysetdefinitions/*
- Microsoft.PolicyInsights/*

Pour plus d’informations sur les rôles personnalisés, consultez [Rôles personnalisés Azure](/azure/role-based-access-control/custom-roles).

## <a name="manage-azure-policy-for-azure-ad-only-authentication"></a>Gérer Azure Policy pour l’authentification Azure AD uniquement

Les stratégies d’authentification Azure AD uniquement peuvent être gérées en accédant au [portail Azure](https://portal.azure.com) et en recherchant le service **Stratégie**. Sous **Définitions**, recherchez *Authentification Azure Active Directory uniquement*.

:::image type="content" source="media/authentication-azure-ad-only-authentication-policy/policy-azure-ad-only-authentication.png" alt-text="Capture d’écran d’Azure Policy pour l’authentification Azure AD uniquement":::

Pour obtenir un guide expliquant comment ajouter une stratégie Azure pour l’authentification Azure AD uniquement, consultez [Utilisation d’Azure Policy pour appliquer l’authentification Azure AD uniquement avec Azure SQL](authentication-azure-ad-only-authentication-policy-how-to.md).

Il existe trois effets pour ces stratégies :

- **Audit** : paramètre par défaut, qui capture uniquement un rapport d’audit dans les journaux d’activité Azure Policy
- **Refuser** : empêche la création d’un serveur logique ou d’une instance managée avec l’[authentification Azure AD uniquement](authentication-azure-ad-only-authentication.md) activée
- **Désactivé** : désactive la stratégie et n’empêche pas les utilisateurs de créer un serveur logique ou une instance managée sans que l’authentification Azure AD uniquement soit activée

Si la stratégie Azure pour l’authentification Azure AD uniquement est définie sur **Refuser**, la création du serveur logique Azure SQL ou de l’instance managée échoue. Les détails de cet échec sont enregistrés dans le **Journal d’activité** du groupe de ressources.

## <a name="policy-compliance"></a>Conformité aux stratégies

Vous pouvez consulter le paramètre de **Conformité** sous le service **Stratégie** pour voir l’état de conformité. L’**État de conformité** indique si le serveur ou l’instance managée est actuellement conforme à l’activation de l’authentification Azure AD uniquement. 

La stratégie Azure peut empêcher la création d’un serveur logique ou d’une instance managée sans que l’authentification Azure AD uniquement soit activée, mais la fonctionnalité peut être modifiée après la création du serveur ou de l’instance managée. Si un utilisateur a désactivé l’authentification Azure AD uniquement après la création du serveur ou de l’instance managée, l’état de conformité est `Non-compliant` si la stratégie Azure est définie sur **Refuser**.

:::image type="content" source="media/authentication-azure-ad-only-authentication-policy/check-compliance-policy-azure-ad-only-authentication.png" alt-text="Capture d’écran du menu de conformité Azure Policy pour l’authentification Azure AD uniquement":::

## <a name="limitations"></a>Limites

- Actuellement, vous ne pouvez pas créer de serveur logique ou d’instance managée dans le portail Azure avec l’authentification Azure AD uniquement activée. Vous pouvez créer un serveur logique ou une instance managée avec l’authentification Azure AD uniquement activée à l’aide de l’API REST, d’Azure CLI, de PowerShell ou d’un modèle ARM. Pour plus d’informations, consultez [Créer un serveur avec l’authentification Azure AD uniquement activée dans Azure SQL](authentication-azure-ad-only-authentication-create-server.md).
- Azure Policy applique l’authentification Azure AD uniquement lors de la création du serveur logique ou de l’instance managée. Une fois le serveur créé, les utilisateurs Azure AD autorisés disposant de rôles spéciaux (par exemple Gestionnaire de sécurité SQL) peuvent désactiver la fonctionnalité d’authentification Azure AD uniquement. La stratégie Azure le permet, mais dans ce cas le serveur ou l’instance managée est listé(e) comme `Non-compliant` dans le rapport de conformité, et celui-ci indique le nom du serveur ou de l’instance managée.  
- Pour plus d’informations sur les problèmes connus et les autorisations nécessaires, consultez [Authentification Azure AD uniquement](authentication-azure-ad-only-authentication.md).

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Utilisation d’Azure Policy pour appliquer l’authentification Azure Active Directory uniquement avec Azure SQL](authentication-azure-ad-only-authentication-policy-how-to.md)
