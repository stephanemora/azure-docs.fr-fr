---
title: Déléguer la gouvernance des accès aux créateurs de catalogue dans la gestion des droits d’utilisation Azure AD - Azure Active Directory
description: Découvrez comment déléguer la gouvernance des accès des administrateurs informatiques aux créateurs de catalogue et chefs de projet pour qu’ils puissent gérer les accès eux-mêmes.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 07/6/2021
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5afa05b8a529d8a9e9fceeb4a113f0b743acfc05
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/16/2021
ms.locfileid: "114286574"
---
# <a name="delegate-access-governance-to-catalog-creators-in-azure-ad-entitlement-management"></a>Déléguer la gouvernance des accès aux créateurs de catalogue dans la gestion des droits d’utilisation Azure AD

Un catalogue est un conteneur de ressources et de packages d’accès. Vous créez un catalogue lorsque vous souhaitez regrouper des ressources et packages d’accès liés. Par défaut, un administrateur général ou un administrateur de gouvernance des identités peut [créer un catalogue](entitlement-management-catalog-create.md) et peut ajouter des utilisateurs supplémentaires en tant que propriétaires du catalogue.

Pour déléguer à des utilisateurs qui ne sont pas administrateurs, pour qu’ils puissent créer leurs propres catalogues, vous pouvez ajouter ces utilisateurs au rôle de créateur de catalogue défini par la gestion des droits d’utilisation Azure AD. Vous pouvez ajouter des utilisateurs individuels ou un groupe, dont les membres sont alors en mesure de créer des catalogues.  Après la création d’un catalogue, les utilisateurs peuvent ajouter par la suite des ressources dont ils sont propriétaires dans leur catalogue.

## <a name="as-an-it-administrator-delegate-to-a-catalog-creator"></a>En tant qu’administrateur informatique, déléguer à un créateur de catalogue

Suivez ces étapes pour affecter un utilisateur au rôle de créateur de catalogue.

**Rôle prérequis :** administrateur général, administrateur de la gouvernance des identités ou administrateur d’utilisateurs

1. Dans le portail Azure, cliquez sur **Azure Active Directory**, puis sur **Identity Governance**.

1. Dans le menu de gauche, dans la section **Gestion des droits d’utilisation**, cliquez sur **Paramètres**.

1. Cliquez sur **Modifier**.

    ![Paramètres pour ajouter des créateurs de catalogue](./media/entitlement-management-delegate-catalog/settings-delegate.png)

1. Dans la section **Déléguer la gestion des droits d’utilisation**, cliquez sur **Ajouter des créateurs de catalogue** pour sélectionner les utilisateurs ou groupes auxquels vous voulez déléguer cette gestion des droits d’utilisation.

1. Cliquez sur **Sélectionner**.

1. Cliquez sur **Enregistrer**.

## <a name="allow-delegated-roles-to-access-the-azure-portal"></a>Autoriser les rôles délégués à accéder au Portail Azure

Pour permettre aux rôles délégués, tels que les créateurs de catalogue et les gestionnaires de packages d’accès, d’accéder au Portail Azure afin de gérer les packages d’accès, vous devez vérifier le paramètre du portail d’administration.

**Rôle prérequis :** Administrateur général ou Administrateur d’utilisateurs

1. Dans le Portail Azure, cliquez sur **Azure Active Directory**, puis sur **Utilisateurs**.

1. Dans le menu de gauche, cliquez sur **Paramètres utilisateur**.

1. Vérifiez que **Limiter l’accès au portail d’administration Azure AD** est défini sur **Non**.

    ![Paramètres utilisateur Azure AD - Portail d’administration](./media/entitlement-management-delegate-catalog/user-settings.png)

## <a name="manage-role-assignments-programmatically-preview"></a>Gérer les attributions de rôles de manière programmatique (préversion)

Vous pouvez également afficher et mettre à jour les créateurs de catalogue ainsi que les attributions de rôles propres au catalogue de gestion des droits d’utilisation à l’aide de Microsoft Graph.  Un utilisateur disposant d’un rôle approprié et d’une application qui a l’autorisation déléguée `EntitlementManagement.ReadWrite.All` peut appeler l’API Graph pour [lister les définitions de rôles](/graph/api/rbacapplication-list-roledefinitions?view=graph-rest-beta&preserve-view=true) de la gestion des droits d’utilisation et [lister les attributions de rôles](/graph/api/rbacapplication-list-roleassignments?view=graph-rest-beta&preserve-view=true) de ces définitions de rôles.

Pour récupérer une liste des utilisateurs et des groupes affectés au rôle créateurs de catalogue, le rôle ayant l’ID de définition `ba92d953-d8e0-4e39-a797-0cbedb0a89e8`, utilisez la requête Graph

```http
GET https://graph.microsoft.com/beta/roleManagement/entitlementManagement/roleAssignments?$filter=roleDefinitionId eq 'ba92d953-d8e0-4e39-a797-0cbedb0a89e8'&$expand=principal
```


## <a name="next-steps"></a>Étapes suivantes

- [Créer et gérer un catalogue de ressources](entitlement-management-catalog-create.md)
- [Déléguer la gouvernance des accès aux gestionnaires de package d’accès](entitlement-management-delegate-managers.md)
- [Déléguer la gouvernance d’accès aux propriétaires de ressources](entitlement-management-delegate.md)

