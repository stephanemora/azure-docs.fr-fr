---
title: Ajouter ou supprimer des attributions de rôle Azure avec le portail Azure - Azure RBAC
description: Découvrez comment accorder l’accès aux ressources Azure pour les utilisateurs, groupes, principaux de service ou identités managées à l’aide du portail Azure et du contrôle d’accès en fonction du rôle Azure (Azure RBAC).
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 01/11/2021
ms.author: rolyon
ms.openlocfilehash: f1753e7bc50fa9ff2c5512696a37dae7578f23b4
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/12/2021
ms.locfileid: "98117442"
---
# <a name="add-or-remove-azure-role-assignments-using-the-azure-portal"></a>Ajouter ou supprimer des attributions de rôle Azure à l’aide du portail Azure

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control/definition-grant.md)] Cet article explique comment attribuer des rôles à l’aide du portail Azure.

Si vous devez attribuer des rôles d’administrateur dans Azure Active Directory, consultez [Afficher et attribuer des rôles d’administrateur dans Azure Active Directory](../active-directory/roles/manage-roles-portal.md).

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [Azure role assignment prerequisites](../../includes/role-based-access-control/prerequisites-role-assignments.md)]

## <a name="add-a-role-assignment"></a>Ajouter une attribution de rôle

Dans Azure RBAC, vous devez ajouter une attribution de rôle pour accorder l’accès à une ressource Azure. Suivez ces étapes pour attribuer un rôle. Pour une vue détaillée des étapes, consultez [Étapes pour ajouter une attribution de rôle](role-assignments-steps.md).

### <a name="step-1-identify-the-needed-scope"></a>Étape 1 : Identifier l’étendue nécessaire

[!INCLUDE [Scope for Azure RBAC introduction](../../includes/role-based-access-control/scope-intro.md)]

[!INCLUDE [Scope for Azure RBAC least privilege](../../includes/role-based-access-control/scope-least.md)] Pour plus d’informations sur l’étendue, consultez [Comprendre l’étendue](scope-overview.md).

![Niveaux de l’étendue de RBAC Azure](../../includes/role-based-access-control/media/scope-levels.png)

1. Connectez-vous au [portail Azure](https://portal.azure.com).

1. Dans la zone de recherche située en haut, recherchez l’étendue à laquelle vous souhaitez accorder l’accès. Par exemple, recherchez **Groupes d’administration**, **Abonnements**, **Groupes de ressources**, ou une ressource spécifique.

    ![Portail Azure, rechercher un groupe de ressources](./media/shared/rg-portal-search.png)

1. Cliquez sur la ressource spécifique pour cette étendue.

    Voici l’illustration d’un exemple de groupe de ressources.

    ![Vue d’ensemble du groupe de ressources](./media/shared/rg-overview.png)

### <a name="step-2-open-the-add-role-assignment-pane"></a>Étape 2 : Ouvrir le volet Ajouter une attribution de rôle

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

### <a name="step-3-select-the-appropriate-role"></a>Étape 3 : Sélectionner le rôle approprié

1. Dans la liste **Rôle**, recherchez le rôle que vous souhaitez affecter, ou faites-le défiler.

    Pour vous aider à déterminer le rôle approprié, vous pouvez pointer sur l’icône d’informations afin d’afficher une description du rôle. Pour plus d’informations, consultez l’article [Rôles intégrés Azure](built-in-roles.md).

   ![Sélectionner un rôle dans Ajouter une attribution de rôle](./media/role-assignments-portal/add-role-assignment-role.png)

1. Cliquez ici pour sélectionner le rôle.

### <a name="step-4-select-who-needs-access"></a>Étape 4 : Sélectionner les utilisateurs qui ont besoin d’un accès

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

### <a name="step-5-assign-role"></a>Étape 5 : Affecter le rôle

1. Pour attribuer le rôle, cliquez sur **Enregistrer**.

   Après quelques instants, le principal de sécurité est attribué au rôle dans l’étendue sélectionnée.

1. Dans l’onglet **Attributions de rôle**, vérifiez que vous voyez l’attribution de rôle dans la liste.

    ![Enregistrement Ajouter une attribution de rôle](./media/role-assignments-portal/rg-role-assignments.png)

## <a name="remove-a-role-assignment"></a>Supprimer une attribution de rôle

Dans Azure RBAC, vous devez supprimer une attribution de rôle pour supprimer l’accès à une ressource Azure. Suivez ces étapes pour supprimer une attribution de rôle.

1. Ouvrez **Contrôle d’accès (IAM)** dans une étendue, par exemple un groupe d’administration, un abonnement, un groupe de ressources ou une ressource pour lesquels vous souhaitez supprimer l’accès.

1. Cliquez sur l’onglet **Attributions de rôles** afin d’afficher toutes les attributions de rôles pour cette étendue.

1. Dans la liste des attributions de rôle, ajoutez une coche à côté du principal de sécurité comportant l’attribution de rôle que vous souhaitez supprimer.

   ![Attribution de rôle sélectionnée pour être supprimée](./media/role-assignments-portal/rg-role-assignments-select.png)

1. Cliquez sur **Supprimer**.

   ![Supprimer le message d’attribution de rôle](./media/role-assignments-portal/remove-role-assignment.png)

1. Dans le message d’attribution de rôle qui s’affiche, cliquez sur **Oui**.

    Si vous voyez un message indiquant que les attributions de rôles héritées ne peuvent pas être supprimées, cela signifie que vous tentez de supprimer une attribution de rôle dans une étendue enfant. Vous devez ouvrir le contrôle d’accès (IAM) dans l’étendue où le rôle a été attribué, puis réessayer. Un moyen rapide d’ouvrir le contrôle d’accès (IAM) à l’étendue correcte consiste à examiner la colonne **Étendue**, puis à cliquer sur le lien à côté de **(Héritée)** .

   ![Message Supprimer l’attribution de rôle pour les attributions de rôles héritées](./media/role-assignments-portal/remove-role-assignment-inherited.png)

## <a name="next-steps"></a>Étapes suivantes

- [Attribuer à un utilisateur le rôle d’administrateur d’un abonnement Azure](role-assignments-portal-subscription-admin.md)
- [Ajouter une attribution de rôle pour une identité managée](role-assignments-portal-managed-identity.md)
- [Résoudre les problèmes liés à Azure RBAC](troubleshooting.md)
