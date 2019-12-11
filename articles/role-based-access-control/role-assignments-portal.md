---
title: Ajouter ou supprimer des attributions de rôles avec le RBAC Azure et le portail Azure
description: Découvrez comment accorder l’accès aux ressources Azure pour les utilisateurs, groupes, principaux de service ou identités managées à l’aide du contrôle d’accès en fonction du rôle (RBAC) Azure et du portail Azure.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 8078f366-a2c4-4fbb-a44b-fc39fd89df81
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/25/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 17a325e96e9709b60da2f23d1dc68e3300fde80c
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74707866"
---
# <a name="add-or-remove-role-assignments-using-azure-rbac-and-the-azure-portal"></a>Ajouter ou supprimer des attributions de rôles avec le RBAC Azure et le portail Azure

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)] Cet article explique comment attribuer des rôles à l’aide du portail Azure.

Si vous devez attribuer des rôles d’administrateur dans Azure Active Directory, consultez [Afficher et attribuer des rôles d’administrateur dans Azure Active Directory](../active-directory/users-groups-roles/directory-manage-roles-portal.md).

## <a name="prerequisites"></a>Prérequis

Pour ajouter ou supprimer des attributions de rôles, vous devez disposer :

- d’autorisations `Microsoft.Authorization/roleAssignments/write` et `Microsoft.Authorization/roleAssignments/delete`, telles que [Administrateur de l’accès utilisateur](built-in-roles.md#user-access-administrator) ou [Propriétaire de l’accès utilisateur](built-in-roles.md#owner)

## <a name="overview-of-access-control-iam"></a>Vue d’ensemble du contrôle d’accès (IAM)

Le panneau **Contrôle d’accès (IAM)** vous permet d’attribuer des rôles. Il permet de gérer les identités et les accès et apparaît dans plusieurs emplacements du portail Azure. Voici un exemple de panneau Contrôle d'accès (IAM) pour un abonnement.

![Panneau Contrôle d’accès (IAM) pour un abonnement](./media/role-assignments-portal/access-control-subscription.png)

Pour utiliser le panneau Contrôle d’accès (IAM) avec une efficacité optimale, il est judicieux de pouvoir répondre aux trois questions suivantes quand vous essayez d’attribuer un rôle :

1. **Qui a besoin d’accéder ?**

    Qui fait référence à un utilisateur, à un groupe, à un principal de service ou à une identité gérée ? C’est également ce qu’on appelle un *principal de sécurité*.

1. **De quel rôle ont-ils besoin ?**

    Les autorisations sont regroupées dans des rôles. Vous pouvez effectuer une sélection dans une liste de plusieurs [rôles intégrés](built-in-roles.md) ou utiliser vos propres rôles personnalisés.

1. **Où ont-ils besoin d’accéder ?**

    Where représente l’ensemble des ressources auxquelles l’accès s’applique. Where peut être un groupe d’administration, un abonnement, un groupe de ressources ou une ressource unique telle qu’un compte de stockage. C’est ce qu’on appelle l’*étendue*.

## <a name="add-a-role-assignment"></a>Ajouter une attribution de rôle

Suivez ces étapes pour attribuer un rôle à différentes étendues.

1. Dans le portail Azure, cliquez sur **Tous les services**, puis sélectionnez l’étendue. Par exemple, vous pouvez sélectionner **Groupes d’administration**, **Abonnements**, **Groupes de ressources**, ou une ressource.

1. Cliquez sur la ressource spécifique.

1. Cliquez sur **Contrôle d’accès (IAM)** .

1. Cliquez sur l’onglet **Attributions de rôles** afin d’afficher toutes les attributions de rôles pour cette étendue.

1. Cliquez sur **Ajouter** > **Ajouter une attribution de rôle** pour ouvrir le volet Ajouter une attribution de rôle.

   Si vous n’avez pas les autorisations pour attribuer des rôles, l’option Ajouter une attribution de rôle sera désactivée.

   ![Ajoutez un menu](./media/role-assignments-portal/add-menu.png)

   ![Volet Ajouter une attribution de rôle](./media/role-assignments-portal/add-role-assignment.png)

1. Dans la liste déroulante **Rôle**, sélectionnez un rôle, tel que **Contributeur de machines virtuelles**.

1. Dans la liste **Sélectionner**, sélectionnez un utilisateur, un groupe, un principal de service ou une identité gérée. Si vous ne voyez pas le principal de sécurité dans la liste, vous pouvez saisir du texte dans la zone **Sélectionner** pour rechercher des noms d’affichage, des adresses de messagerie et des identificateurs d’objet dans le répertoire.

1. Cliquez sur **Enregistrer** pour attribuer le rôle.

   Après quelques instants, le principal de sécurité est attribué au rôle dans l’étendue sélectionnée.

## <a name="assign-a-user-as-an-administrator-of-a-subscription"></a>Attribuer à un utilisateur en tant qu’administrateur d’un abonnement

Pour faire d’un utilisateur un administrateur d’un abonnement Azure, attribuez-lui le rôle [Propriétaire](built-in-roles.md#owner) au niveau de l’abonnement. Le rôle Propriétaire donne à l’utilisateur un accès total à toutes les ressources de l’abonnement, notamment l’autorisation d’accorder l’accès à d’autres personnes. Ces étapes sont les mêmes que celles de n’importe quelle autre attribution de rôle.

1. Dans le portail Azure, cliquez sur **Tous les services**, puis sur **Abonnements**.

1. Cliquez sur l’abonnement auquel vous voulez ajouter une attribution de rôle.

1. Cliquez sur **Contrôle d’accès (IAM)** .

1. Cliquez sur l’onglet **Attributions de rôles** afin d’afficher toutes les attributions de rôles pour cet abonnement.

1. Cliquez sur **Ajouter** > **Ajouter une attribution de rôle** pour ouvrir le volet Ajouter une attribution de rôle.

   Si vous n’avez pas les autorisations pour attribuer des rôles, l’option Ajouter une attribution de rôle sera désactivée.

   ![Ajoutez un menu](./media/role-assignments-portal/add-menu.png)

   ![Volet Ajouter une attribution de rôle](./media/role-assignments-portal/add-role-assignment.png)

1. Dans la liste déroulante **Rôle**, sélectionnez le rôle **Propriétaire**.

1. Dans la liste **Sélectionner**, sélectionnez un utilisateur. Si vous ne voyez pas l’utilisateur dans la liste, vous pouvez taper dans la zone **Sélectionner** pour rechercher des noms d’affichage et des adresses e-mail dans l’annuaire.

1. Cliquez sur **Enregistrer** pour attribuer le rôle.

   Après quelques instants, le rôle Propriétaire est attribué à l’utilisateur au niveau de l’abonnement.

## <a name="remove-a-role-assignment"></a>Supprimer une attribution de rôle

Dans le RBAC, vous supprimez une attribution de rôle pour supprimer un accès. Suivez ces étapes pour supprimer une attribution de rôle.

1. Ouvrez **Contrôle d’accès (IAM)** dans une étendue, par exemple un groupe d’administration, un abonnement, un groupe de ressources ou une ressource pour lesquels vous souhaitez supprimer l’accès.

1. Cliquez sur l’onglet **Attributions de rôles** afin d’afficher toutes les attributions de rôles pour cet abonnement.

1. Dans la liste des attributions de rôle, ajoutez une coche à côté du principal de sécurité comportant l’attribution de rôle que vous souhaitez supprimer.

   ![Supprimer le message d’attribution de rôle](./media/role-assignments-portal/remove-role-assignment-select.png)

1. Cliquez sur **Supprimer**.

   ![Supprimer le message d’attribution de rôle](./media/role-assignments-portal/remove-role-assignment.png)

1. Dans le message d’attribution de rôle qui s’affiche, cliquez sur **Oui**.

    Les attributions de rôle héritées ne peuvent pas être supprimées. Si vous avez besoin de supprimer une attribution de rôle héritée, vous devez le faire au niveau de l’étendue où l’affectation de rôle a été créée. Dans la colonne **Étendue**, en regard de **Hérité**, un lien vous dirige vers l’étendue à laquelle ce rôle a été affecté. Accédez à l’étendue répertoriée ici pour supprimer l’attribution de rôle.

   ![Supprimer le message d’attribution de rôle](./media/role-assignments-portal/remove-role-assignment-inherited.png)

## <a name="next-steps"></a>Étapes suivantes

- [Lister les attributions de rôles à l’aide du RBAC Azure et du portail Azure](role-assignments-list-portal.md)
- [Tutoriel : Accorder un accès utilisateur aux ressources Azure à l’aide du contrôle RBAC et du portail Azure](quickstart-assign-role-user-portal.md)
- [Résoudre des problèmes liés au contrôle d'accès en fonction du rôle pour les ressources Azure](troubleshooting.md)
- [Organiser vos ressources avec des groupes d’administration Azure](../governance/management-groups/overview.md)
