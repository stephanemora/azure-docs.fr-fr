---
title: Attribuer à un utilisateur en tant qu’administrateur d’un abonnement Azure – Azure RBAC
description: Découvrez comment faire d’un utilisateur un administrateur d’un abonnement Azure à l’aide du portail Azure et du contrôle d’accès en fonction du rôle Azure (Azure RBAC).
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 06/25/2021
ms.author: rolyon
ms.custom: subject-rbac-steps
ms.openlocfilehash: af88edbb788afa3576d3317d4e1cd54dea6f4deb
ms.sourcegitcommit: 1c12bbaba1842214c6578d914fa758f521d7d485
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/28/2021
ms.locfileid: "112988064"
---
# <a name="assign-a-user-as-an-administrator-of-an-azure-subscription"></a>Attribuer à un utilisateur le rôle d’administrateur d’un abonnement Azure

Pour faire d’un utilisateur un administrateur d’un abonnement Azure, attribuez-lui le rôle [Propriétaire](built-in-roles.md#owner) au niveau de l’abonnement. Le rôle Propriétaire donne à l’utilisateur un accès total à toutes les ressources de l’abonnement, notamment l’autorisation d’accorder l’accès à d’autres personnes. Ces étapes sont les mêmes que celles de n’importe quelle autre attribution de rôle.

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [Azure role assignment prerequisites](../../includes/role-based-access-control/prerequisites-role-assignments.md)]

## <a name="step-1-open-the-subscription"></a>Étape 1 : Ouvrir l’abonnement

1. Connectez-vous au [portail Azure](https://portal.azure.com).

1. Dans la zone de recherche située en haut, recherchez les abonnements.

1. Cliquez sur l’abonnement que vous souhaitez utiliser.

    Voici un exemple d’abonnement.

    ![Vue d’ensemble de la capture d’écran des abonnements](./media/shared/sub-overview.png)

## <a name="step-2-open-the-add-role-assignment-page"></a>Étape 2 : Ouvrir la page Ajouter une attribution de rôle

La page **Contrôle d’accès (IAM)** vous permet généralement d’attribuer des rôles afin d’accorder l’accès aux ressources Azure. Elle est également connue sous le nom de gestion des identités et des accès (IAM) et apparaît à plusieurs endroits du portail Azure.

1. Cliquez sur **Contrôle d’accès (IAM)**.

    Voici un exemple de page Contrôle d'accès (IAM) pour un abonnement.

    ![Capture d’écran de la page Contrôle d’accès (IAM) d’un abonnement.](./media/shared/sub-access-control.png)

1. Cliquez sur l’onglet **Attributions de rôles** afin d’afficher les attributions de rôles pour cette étendue.

1. Cliquez sur **Ajouter** > **Ajouter une attribution de rôle (préversion)** .

   Si vous n’avez pas les autorisations pour attribuer des rôles, l’option Ajouter une attribution de rôle sera désactivée.

    ![Capture d’écran du menu Ajouter > Ajouter une attribution de rôle pour une expérience en préversion.](./media/shared/add-role-assignment-menu-preview.png)

    La page Ajouter une attribution de rôle s’ouvre.

## <a name="step-3-select-the-owner-role"></a>Étape 3 : Sélectionner le rôle Propriétaire

Le rôle [Propriétaire](built-in-roles.md#owner) octroie un accès total à la gestion de toutes les ressources, notamment la possibilité d’attribuer des rôles dans Azure RBAC. Vous devez disposer d'un maximum de trois propriétaires d’abonnement afin de réduire le risque de violation par un propriétaire compromis.

1. Dans l’onglet **Rôle**, sélectionnez le rôle **Propriétaire**.

    Vous pouvez rechercher un rôle par nom ou par description. Vous pouvez également filtrer les rôles par type et par catégorie.

   ![Capture d’écran de la page Ajouter une attribution de rôle avec l’onglet Roles pour une expérience en préversion.](./media/shared/roles.png)

1. Cliquez sur **Suivant**.

## <a name="step-4-select-who-needs-access"></a>Étape 4 : Sélectionner les utilisateurs qui ont besoin d’un accès

1. Dans l’onglet **Membres**, sélectionnez **Utilisateur, groupe ou principal du service**.

   ![Capture d’écran de la page Ajouter une attribution de rôle avec l’onglet Ajouter des membres pour une expérience en préversion.](./media/shared/members.png)

1. Cliquez sur **Sélectionner des membres**.

1. Recherchez et sélectionnez l’utilisateur.

    Vous pouvez entrer du texte dans la zone **Sélectionner** pour rechercher des noms d’affichage ou des adresses e-mail dans l’annuaire.

   ![Capture d’écran de la fenêtre Sélectionner des membres pour une expérience en préversion.](./media/shared/select-members.png)

1. Cliquez sur **Enregistrer** pour ajouter l’utilisateur à la liste Membres.

1. Dans la zone de texte **Description**, entrez une description facultative pour cette attribution de rôle.

    Par la suite, vous pouvez afficher cette description dans la liste des attributions de rôle.

1. Cliquez sur **Suivant**.

## <a name="step-5-assign-role"></a>Étape 5 : Affecter le rôle

1. Sous l’onglet **Vérifier + attribuer**, vérifiez les paramètres d’attribution de rôle.

1. Cliquez sur **Vérifier + attribuer** pour attribuer le rôle.

   Après quelques instants, le rôle Propriétaire est attribué à l’utilisateur au niveau de l’abonnement.

    ![Capture d’écran de la liste d’attribution de rôle après l’attribution d’un rôle pour une expérience en préversion.](./media/role-assignments-portal-subscription-admin/sub-role-assignments-owner.png)

## <a name="next-steps"></a>Étapes suivantes

- [Attribuer des rôles Azure à l’aide du portail Azure](role-assignments-portal.md)
- [Répertorier les attributions de rôles Azure à l’aide du portail Azure](role-assignments-list-portal.md)
- [Organiser vos ressources avec des groupes d’administration Azure](../governance/management-groups/overview.md)
