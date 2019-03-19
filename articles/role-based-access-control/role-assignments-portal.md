---
title: Gérer l’accès aux ressources Azure à l’aide de RBAC et le portail Azure | Microsoft Docs
description: Découvrez comment gérer l’accès aux ressources Azure pour les utilisateurs, groupes, principaux de service et identités managées à l’aide du Contrôle d’accès en fonction du rôle (RBAC) et du portail Azure. Apprenez notamment à lister, à accorder et à supprimer des accès.
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
ms.date: 02/24/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: bb23cbc275e01eab5361504c547c020b0a29f4c3
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/25/2019
ms.locfileid: "56805288"
---
# <a name="manage-access-to-azure-resources-using-rbac-and-the-azure-portal"></a>Gérer l’accès aux ressources Azure à l’aide du contrôle d’accès en fonction du rôle et du portail Azure

Le [contrôle d'accès en fonction du rôle (RBAC)](overview.md) vous permet de gérer l'accès aux ressources Azure. Cet article décrit la façon dont vous gérez l’accès à l’aide du portail Azure. Si vous avez besoin gérer l’accès à Azure Active Directory, consultez [vue et assigner des rôles d’administrateur dans Azure Active Directory](../active-directory/users-groups-roles/directory-manage-roles-portal.md).

## <a name="prerequisites"></a>Conditions préalables

Pour ajouter et supprimer des attributions de rôles, vous devez avoir :

- `Microsoft.Authorization/roleAssignments/write` et `Microsoft.Authorization/roleAssignments/delete` autorisations, telles que [administrateur des accès utilisateur](built-in-roles.md#user-access-administrator) ou [propriétaire](built-in-roles.md#owner)

## <a name="overview-of-access-control-iam"></a>Vue d’ensemble du contrôle d’accès (IAM)

**Contrôle d’accès (IAM)** est le panneau que vous utilisez pour gérer l’accès aux ressources Azure. Il est également appelé gestion des identités et des accès et s’affiche dans plusieurs emplacements dans le portail Azure. Voici un exemple de panneau Contrôle d'accès (IAM) pour un abonnement.

![Panneau Contrôle d’accès (IAM) pour un abonnement](./media/role-assignments-portal/access-control-numbers.png)

Le tableau suivant décrit ce que certains éléments sont utilisés pour :

| # | Élément | Ce que vous utilisez pour |
| --- | --- | --- |
| 1 | Ressources dans lequel le contrôle d’accès (IAM) est ouvert | Identifier l’étendue (abonnement dans cet exemple) |
| 2 | **Ajouter** bouton | Ajouter des attributions de rôle |
| 3 | **Vérifier l’accès** onglet | Afficher les attributions de rôle pour un seul utilisateur |
| 4 | **Attributions de rôles** onglet | Afficher les attributions de rôles au niveau de la portée actuelle |
| 5. | **Rôles** onglet | Afficher tous les rôles et autorisations |

Pour être plus efficace avec le panneau de contrôle (IAM) d’accès, il est utile si vous pouvez répondre aux trois questions suivantes lorsque vous essayez de gérer l’accès :

1. **Qui a besoin d’accéder ?**

    Qui fait référence à un utilisateur, un groupe, un principal de service ou une identité gérée. Elle est également appelée un *principal de sécurité*.

1. **Quelles sont les autorisations a-t-il besoin ?**

    Les autorisations sont regroupées dans des rôles. Vous pouvez sélectionner dans une liste de plusieurs rôles intégrés.

1. **Où ils besoin d’accéder ?**

    Où désigne l’ensemble de ressources auquel l’accès s’applique. Où peut être un groupe d’administration, abonnement, groupe de ressources ou une ressource unique tel qu’un compte de stockage. Il s’agit du *étendue*.

## <a name="open-access-control-iam"></a>Ouvrir Contrôle d’accès (IAM)

La première chose que vous devez déterminer est là ouvrir le panneau de contrôle (IAM) d’accès. Il varie selon les ressources auxquelles vous souhaitez gérer l’accès. Vous souhaitez gérer l’accès pour tous les éléments dans un groupe d’administration, tous les éléments dans un abonnement, tous les éléments dans un groupe de ressources ou une ressource unique ?

1. Dans le portail Azure, cliquez sur **tous les services** , puis sélectionnez l’étendue. Par exemple, vous pouvez sélectionner **Groupes d’administration**, **Abonnements**, **Groupes de ressources**, ou une ressource.

1. Cliquez sur la ressource spécifique.

1. Cliquez sur **Contrôle d’accès (IAM)**.

    Voici un exemple de panneau Contrôle d'accès (IAM) pour un abonnement. Si vous apportez des modifications de contrôle d’accès, ils s’appliquera à l’ensemble de l’abonnement.

    ![Panneau Contrôle d’accès (IAM) pour un abonnement](./media/role-assignments-portal/access-control-subscription.png)

## <a name="view-roles-and-permissions"></a>Afficher les rôles et les autorisations

Une définition de rôle est une collection d’autorisations que vous utilisez pour les attributions de rôle. Azure propose plus de 70 [rôles intégrés pour les ressources Azure](built-in-roles.md). Suivez ces étapes pour afficher les rôles disponibles et les autorisations.

1. Ouvrez **contrôle d’accès (IAM)** à n’importe quelle étendue.

1. Cliquez sur l’onglet **Rôles** pour afficher une liste de tous les rôles intégrés et personnalisés.

   Vous pouvez voir le nombre d’utilisateurs et groupes qui sont affectés à chaque rôle au niveau de la portée actuelle.

   ![Liste de rôles](./media/role-assignments-portal/roles-list.png)

1. Cliquez sur un rôle individuel pour voir à qui ce rôle a été attribué et pour afficher les autorisations du rôle.

   ![Attributions de rôles](./media/role-assignments-portal/role-assignments.png)

## <a name="view-role-assignments"></a>Voir les attributions de rôles

Lors de la gestion des accès, vous souhaitez savoir qui a accès, quelles sont leurs autorisations et à quelle étendue. Pour répertorier les accès pour un utilisateur, groupe, principal du service ou une identité gérée, vous permet d’afficher leurs attributions de rôles.

### <a name="view-role-assignments-for-a-single-user"></a>Afficher les attributions de rôles d’un utilisateur

Suivez ces étapes pour afficher l’accès d’un utilisateur, groupe, principal de service ou d’une identité gérée avec une étendue spécifique.

1. Ouvrez **Contrôle d’accès (IAM)** dans une étendue, par exemple un groupe d’administration, un abonnement, un groupe de ressources ou une ressource pour lesquels vous souhaitez afficher l’accès.

1. Cliquez sur l’onglet **Vérifier l’accès**.

    ![Contrôle d’accès - onglet Vérifier l’accès](./media/role-assignments-portal/access-control-check-access.png)

1. Dans la liste **Rechercher**, sélectionnez le type principal de sécurité dont vous souhaitez contrôler l’accès.

1. Dans la zone de recherche, entrez une chaîne afin de rechercher, dans le répertoire, des noms d’affichage, des adresses e-mail ou des identificateurs d’objet.

    ![Liste de sélection Vérifier l’accès](./media/role-assignments-portal/check-access-select.png)

1. Cliquez sur le principal de sécurité pour ouvrir le volet **Affectations**.

    ![Volet Affectations](./media/role-assignments-portal/check-access-assignments.png)

    Dans ce volet, vous pouvez voir les rôles attribués au principal de sécurité sélectionné et la portée. S’il existe des affectations de refus dans cette étendue ou transmises à cette étendue, elles sont répertoriées.

### <a name="view-all-role-assignments-at-a-scope"></a>Afficher toutes les attributions de rôle dans une étendue

1. Ouvrez **Contrôle d’accès (IAM)** dans une étendue, par exemple un groupe d’administration, un abonnement, un groupe de ressources ou une ressource pour lesquels vous souhaitez afficher l’accès.

1. Cliquez sur l’onglet **Attributions de rôles** afin d’afficher toutes les attributions de rôles pour cette étendue.

   ![Contrôle d’accès - Onglet Attributions de rôles](./media/role-assignments-portal/access-control-role-assignments.png)

   L’onglet Attributions de rôles indique les personnes qui ont accès à cette étendue. Notez que certains rôles sont inclus dans l’étendue de **cette ressource**, tandis que d’autres sont **hérités** à partir d’une autre étendue. L’accès est attribué spécifiquement à cette ressource ou hérité d’une affectation à l’étendue parente.

## <a name="add-a-role-assignment"></a>Ajouter une attribution de rôle

Dans RBAC, pour accorder l’accès, vous affectez un rôle à un utilisateur, un groupe, un principal de service ou une identité gérée. Suivez ces étapes pour accorder l’accès à différentes étendues.

### <a name="assign-a-role-at-a-scope"></a>Affecter un rôle dans une étendue

1. Ouvrez **Contrôle d’accès (IAM)** dans une étendue, par exemple un groupe d’administration, un abonnement, un groupe de ressources ou une ressource auxquels vous souhaitez donner l’accès.

1. Cliquez sur l’onglet **Attributions de rôles** afin d’afficher toutes les attributions de rôles pour cette étendue.

1. Cliquez sur **Ajouter** > **Ajouter une attribution de rôle** pour ouvrir le volet Ajouter une attribution de rôle.

   Si vous n’avez pas les autorisations pour attribuer des rôles, l’option Ajouter une attribution de rôle sera désactivée.

   ![Ajoutez un menu](./media/role-assignments-portal/add-menu.png)

   ![Volet Ajouter une attribution de rôle](./media/role-assignments-portal/add-role-assignment.png)

1. Dans la liste déroulante **Rôle**, sélectionnez un rôle, tel que **Contributeur de machines virtuelles**.

1. Dans la liste **Sélectionner**, sélectionnez un utilisateur, un groupe, un principal de service ou une identité gérée. Si vous ne voyez pas le principal de sécurité dans la liste, vous pouvez saisir du texte dans la zone **Sélectionner** pour rechercher des noms d’affichage, des adresses de messagerie et des identificateurs d’objet dans le répertoire.

1. Cliquez sur **Enregistrer** pour attribuer le rôle.

   Après quelques instants, le principal de sécurité est attribué au rôle dans l’étendue sélectionnée.

### <a name="assign-a-user-as-an-administrator-of-a-subscription"></a>Attribuer à un utilisateur en tant qu’administrateur d’un abonnement

Pour faire d’un utilisateur un administrateur d’un abonnement Azure, attribuez-lui le rôle [Propriétaire](built-in-roles.md#owner) au niveau de l’abonnement. Le rôle Propriétaire donne à l’utilisateur un accès total à toutes les ressources de l’abonnement, ainsi que le droit de déléguer l’accès à d’autres personnes. Ces étapes sont les mêmes que celles de n’importe quelle autre attribution de rôle.

1. Dans le portail Azure, cliquez sur **Tous les services**, puis sur **Abonnements**.

1. Cliquez sur l’abonnement pour lequel vous souhaitez accorder l’accès.

1. Cliquez sur **Contrôle d’accès (IAM)**.

1. Cliquez sur l’onglet **Attributions de rôles** afin d’afficher toutes les attributions de rôles pour cet abonnement.

1. Cliquez sur **Ajouter** > **Ajouter une attribution de rôle** pour ouvrir le volet Ajouter une attribution de rôle.

   Si vous n’avez pas les autorisations pour attribuer des rôles, l’option Ajouter une attribution de rôle sera désactivée.

   ![Ajoutez un menu](./media/role-assignments-portal/add-menu.png)

   ![Volet Ajouter une attribution de rôle](./media/role-assignments-portal/add-role-assignment.png)

1. Dans la liste déroulante **Rôle**, sélectionnez le rôle **Propriétaire**.

1. Dans la liste **Sélectionner**, sélectionnez un utilisateur. Si vous ne voyez pas l’utilisateur dans la liste, vous pouvez taper dans la zone **Sélectionner** pour rechercher des noms d’affichage et des adresses e-mail dans l’annuaire.

1. Cliquez sur **Enregistrer** pour attribuer le rôle.

   Après quelques instants, le rôle Propriétaire est attribué à l’utilisateur au niveau de l’abonnement.

## <a name="remove-role-assignments"></a>Supprimer les attributions de rôles

Dans le RBAC, vous supprimez une attribution de rôle pour supprimer un accès. Suivez ces étapes pour supprimer l’accès.

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

* [Tutoriel : Accorder un accès utilisateur aux ressources Azure à l’aide du contrôle RBAC et du portail Azure](quickstart-assign-role-user-portal.md)
* [Tutoriel : Accorder un accès utilisateur aux ressources Azure à l’aide du contrôle RBAC et d’Azure PowerShell](tutorial-role-assignments-user-powershell.md)
* [Résoudre des problèmes liés au contrôle d'accès en fonction du rôle pour les ressources Azure](troubleshooting.md)
* [Organiser vos ressources avec des groupes d’administration Azure](../governance/management-groups/index.md)
