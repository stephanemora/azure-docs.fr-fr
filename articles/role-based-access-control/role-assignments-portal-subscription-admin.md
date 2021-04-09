---
title: Attribuer à un utilisateur en tant qu’administrateur d’un abonnement Azure – Azure RBAC
description: Découvrez comment faire d’un utilisateur un administrateur d’un abonnement Azure à l’aide du portail Azure et du contrôle d’accès en fonction du rôle Azure (Azure RBAC).
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 01/11/2021
ms.author: rolyon
ms.openlocfilehash: dec5888127ed1fc291bec244a44cfb71e343e3bb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100556829"
---
# <a name="assign-a-user-as-an-administrator-of-an-azure-subscription"></a>Attribuer à un utilisateur le rôle d’administrateur d’un abonnement Azure

Pour faire d’un utilisateur un administrateur d’un abonnement Azure, attribuez-lui le rôle [Propriétaire](built-in-roles.md#owner) au niveau de l’abonnement. Le rôle Propriétaire donne à l’utilisateur un accès total à toutes les ressources de l’abonnement, notamment l’autorisation d’accorder l’accès à d’autres personnes. Ces étapes sont les mêmes que celles de n’importe quelle autre attribution de rôle.

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [Azure role assignment prerequisites](../../includes/role-based-access-control/prerequisites-role-assignments.md)]

## <a name="step-1-open-the-subscription"></a>Étape 1 : Ouvrir l’abonnement

1. Connectez-vous au [portail Azure](https://portal.azure.com).

1. Dans la zone de recherche située en haut, recherchez les abonnements.

    ![Portail Azure, rechercher un groupe de ressources](./media/shared/sub-portal-search.png)

1. Cliquez sur l’abonnement que vous souhaitez utiliser.

    Voici un exemple d’abonnement.

    ![Vue d’ensemble du groupe de ressources](./media/shared/sub-overview.png)

## <a name="step-2-open-the-add-role-assignment-pane"></a>Étape 2 : Ouvrir le volet Ajouter une attribution de rôle

La page **Contrôle d’accès (IAM)** vous permet généralement d’attribuer des rôles afin d’accorder l’accès aux ressources Azure. Elle est également connue sous le nom de gestion des identités et des accès (IAM) et apparaît à plusieurs endroits du portail Azure.

1. Cliquez sur **Contrôle d’accès (IAM)**.

    Voici un exemple de page Contrôle d'accès (IAM) pour un abonnement.

    ![Page Contrôle d’accès (IAM) pour un groupe de ressources](./media/shared/sub-access-control.png)

1. Cliquez sur l’onglet **Attributions de rôles** afin d’afficher les attributions de rôles pour cette étendue.

1. Cliquez sur **Ajouter** > **Ajouter une attribution de rôle**.
   Si vous n’avez pas les autorisations pour attribuer des rôles, l’option Ajouter une attribution de rôle sera désactivée.

   ![Menu Ajouter une attribution de rôle](./media/shared/add-role-assignment-menu.png)

    Le volet Ajouter une attribution de rôle s’ouvre.

   ![Volet Ajouter une attribution de rôle](./media/shared/add-role-assignment.png)

## <a name="step-3-select-the-owner-role"></a>Étape 3 : Sélectionner le rôle Propriétaire

Le rôle [Propriétaire](built-in-roles.md#owner) octroie un accès total à la gestion de toutes les ressources, notamment la possibilité d’attribuer des rôles dans Azure RBAC. Vous devez disposer d'un maximum de trois propriétaires d’abonnement afin de réduire le risque de violation par un propriétaire compromis.

- Dans la liste **Rôle**, sélectionnez le rôle **Propriétaire**.

   ![Sélectionner le rôle Propriétaire dans le volet Ajouter une attribution de rôle](./media/role-assignments-portal-subscription-admin/add-role-assignment-role-owner.png)

## <a name="step-4-select-who-needs-access"></a>Étape 4 : Sélectionner les utilisateurs qui ont besoin d’un accès

1. Dans la liste **Attribuer l’accès à**, sélectionnez **Utilisateur, groupe ou principal de service**.

1. Dans la section **Sélectionner**, recherchez l’utilisateur en entrant une chaîne de caractères ou en faisant défiler la liste.

   ![Sélectionner un utilisateur dans Ajouter une attribution de rôle](./media/role-assignments-portal-subscription-admin/add-role-assignment-user-admin.png)

1. Une fois que vous avez trouvé l’utilisateur, cliquez dessus pour le sélectionner.

## <a name="step-5-assign-role"></a>Étape 5 : Affecter le rôle

1. Pour attribuer le rôle, cliquez sur **Enregistrer**.

   Après quelques instants, l’utilisateur se voit attribuer le rôle au niveau de l’étendue sélectionnée.

1. Dans l’onglet **Attributions de rôle**, vérifiez que vous voyez l’attribution de rôle dans la liste.

    ![Enregistrement Ajouter une attribution de rôle](./media/role-assignments-portal-subscription-admin/sub-role-assignments-owner.png)

## <a name="next-steps"></a>Étapes suivantes

- [Attribuer des rôles Azure à l’aide du portail Azure](role-assignments-portal.md)
- [Répertorier les attributions de rôle Azure à l’aide du portail Azure](role-assignments-list-portal.md)
- [Organiser vos ressources avec des groupes d’administration Azure](../governance/management-groups/overview.md)
