---
title: Attribuer des rôles Azure à l’aide du portail Azure - Azure RBAC
description: Découvrez comment accorder l’accès aux ressources Azure pour les utilisateurs, groupes, principaux de service ou identités managées à l’aide du portail Azure et du contrôle d’accès en fonction du rôle Azure (Azure RBAC).
services: active-directory
author: rolyon
manager: daveba
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 02/15/2021
ms.author: rolyon
ms.custom: contperf-fy21q3-portal
ms.openlocfilehash: e25bbe4e1a96e4efaaa13732aea571d26d4b006e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100555278"
---
# <a name="assign-azure-roles-using-the-azure-portal"></a>Attribuer des rôles Azure à l’aide du portail Azure

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control/definition-grant.md)] Cet article explique comment attribuer des rôles à l’aide du portail Azure.

Si vous devez attribuer des rôles d’administrateur dans Azure Active Directory, consultez [Afficher et attribuer des rôles d’administrateur dans Azure Active Directory](../active-directory/roles/manage-roles-portal.md).

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [Azure role assignment prerequisites](../../includes/role-based-access-control/prerequisites-role-assignments.md)]

## <a name="step-1-identify-the-needed-scope"></a>Étape 1 : Identifier l’étendue nécessaire

[!INCLUDE [Scope for Azure RBAC introduction](../../includes/role-based-access-control/scope-intro.md)]

[!INCLUDE [Scope for Azure RBAC least privilege](../../includes/role-based-access-control/scope-least.md)] Pour plus d’informations sur l’étendue, consultez [Comprendre l’étendue](scope-overview.md).

![Niveaux de l’étendue de RBAC Azure](../../includes/role-based-access-control/media/scope-levels.png)

1. Connectez-vous au [portail Azure](https://portal.azure.com).

1. Dans la zone de recherche située en haut, recherchez l’étendue à laquelle vous souhaitez accorder l’accès. Par exemple, recherchez **Groupes d’administration**, **Abonnements**, **Groupes de ressources**, ou une ressource spécifique.

    ![Portail Azure, rechercher un groupe de ressources](./media/shared/rg-portal-search.png)

1. Cliquez sur la ressource spécifique pour cette étendue.

    Voici l’illustration d’un exemple de groupe de ressources.

    ![Vue d’ensemble du groupe de ressources](./media/shared/rg-overview.png)

## <a name="step-2-open-the-add-role-assignment-pane"></a>Étape 2 : Ouvrir le volet Ajouter une attribution de rôle

La page **Contrôle d’accès (IAM)** vous permet généralement d’attribuer des rôles afin d’accorder l’accès aux ressources Azure. Elle est également connue sous le nom de gestion des identités et des accès (IAM) et apparaît à plusieurs endroits du portail Azure.

1. Cliquez sur **Contrôle d’accès (IAM)**.

    Voici un exemple de la page Contrôle d’accès (IAM) pour un groupe de ressources.

    ![Page Contrôle d’accès (IAM) pour un groupe de ressources](./media/shared/rg-access-control.png)

1. Cliquez sur l’onglet **Attributions de rôles** afin d’afficher les attributions de rôles pour cette étendue.

1. Cliquez sur **Ajouter** > **Ajouter une attribution de rôle**.
   Si vous n’avez pas les autorisations pour attribuer des rôles, l’option Ajouter une attribution de rôle sera désactivée.

   ![Menu Ajouter une attribution de rôle](./media/shared/add-role-assignment-menu.png)

    Le volet Ajouter une attribution de rôle s’ouvre.

   ![Volet Ajouter une attribution de rôle](./media/shared/add-role-assignment.png)

## <a name="step-3-select-the-appropriate-role"></a>Étape 3 : Sélectionner le rôle approprié

1. Dans la liste **Rôle**, recherchez le rôle que vous souhaitez affecter, ou faites-le défiler.

    Pour vous aider à déterminer le rôle approprié, vous pouvez pointer sur l’icône d’informations afin d’afficher une description du rôle. Pour plus d’informations, consultez l’article [Rôles intégrés Azure](built-in-roles.md).

   ![Sélectionner un rôle dans Ajouter une attribution de rôle](./media/role-assignments-portal/add-role-assignment-role.png)

1. Cliquez ici pour sélectionner le rôle.

## <a name="step-4-select-who-needs-access"></a>Étape 4 : Sélectionner les utilisateurs qui ont besoin d’un accès

1. Dans la liste **Attribuer l’accès à**, sélectionnez le type de principal de sécurité auquel attribuer l’accès.

    | Type | Description |
    | --- | --- |
    | **Utilisateur, groupe ou principal de service** | Si vous souhaitez attribuer le rôle à un utilisateur, un groupe ou un principal de service (application), sélectionnez ce type. |
    | **Identité managée affectée par l’utilisateur** | Si vous souhaitez affecter le rôle à une [identité managée affectée par l’utilisateur](../active-directory/managed-identities-azure-resources/overview.md), sélectionnez ce type. |
    | *Identité managée affectée par le système* | Si vous souhaitez affecter le rôle à une [identité managée affectée par le système](../active-directory/managed-identities-azure-resources/overview.md), sélectionnez l’instance de service Azure où se trouve l’identité managée. |

   ![Sélectionner le type de principal de sécurité dans Ajouter une attribution de rôle](./media/role-assignments-portal/add-role-assignment-type.png)

1. Si vous avez sélectionné une identité managée affectée par l’utilisateur ou une identité managée affectée par le système, sélectionnez l’**abonnement** où se trouve l’identité managée.

1. Dans la section **Sélectionner**, recherchez le principal de sécurité en entrant une chaîne ou en faisant défiler la liste.

   ![Sélectionner un utilisateur dans Ajouter une attribution de rôle](./media/role-assignments-portal/add-role-assignment-user.png)

1. Cliquez sur le principal de sécurité trouvé pour le sélectionner.

## <a name="step-5-assign-role"></a>Étape 5 : Affecter le rôle

1. Pour attribuer le rôle, cliquez sur **Enregistrer**.

   Après quelques instants, le principal de sécurité est attribué au rôle dans l’étendue sélectionnée.

1. Dans l’onglet **Attributions de rôle**, vérifiez que vous voyez l’attribution de rôle dans la liste.

    ![Enregistrement Ajouter une attribution de rôle](./media/role-assignments-portal/rg-role-assignments.png)

## <a name="next-steps"></a>Étapes suivantes

- [Attribuer à un utilisateur le rôle d’administrateur d’un abonnement Azure](role-assignments-portal-subscription-admin.md)
- [Supprimer des attributions de rôles Azure](role-assignments-remove.md)
- [Résoudre les problèmes liés à Azure RBAC](troubleshooting.md)
