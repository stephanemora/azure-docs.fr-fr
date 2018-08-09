---
title: Attribuer des rôles d’annuaire à des utilisateurs à l’aide d’Azure AD PIM | Microsoft Docs
description: Découvrez comment attribuer des rôles d’annuaire à des utilisateurs à l’aide d’Azure Active Directory Privileged Identity Management et du portail Azure.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.component: pim
ms.date: 07/23/2018
ms.author: rolyon
ms.openlocfilehash: 1aede38cabba7f9811f2b9320bc1e9a9da857f08
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/08/2018
ms.locfileid: "39621811"
---
# <a name="assign-directory-roles-to-users-using-azure-ad-pim"></a>Attribuer des rôles d’annuaire à des utilisateurs à l’aide d’Azure AD PIM

Avec Azure Active Directory (Azure AD), un administrateur général peut effectuer des attributions de rôles d’annuaire **permanentes**. Ces attributions de rôles peuvent être créées via le [portail Azure](../users-groups-roles/directory-assign-admin-roles.md) ou à l’aide de [commandes PowerShell](/powershell/module/azuread#directory_roles).

Le service Azure AD Privileged Identity Management (PIM) permet également aux administrateurs de rôle privilégié d’établir des attributions de rôles d’annuaire permanentes. En outre, les administrateurs de rôle privilégié peuvent rendre les utilisateurs **éligibles** pour les rôles d’annuaire. Un administrateur éligible peut activer le rôle lorsqu’il en a besoin, puis l’autorisation expirera lorsqu’il aura terminé. Pour plus d’informations sur les rôles que vous pouvez gérer à l’aide de PIM, consultez l’article [Rôles d’annuaire que vous pouvez gérer à l’aide d’Azure AD PIM](pim-roles.md).

## <a name="make-a-user-eligible-for-a-role"></a>Rendre un utilisateur éligible pour un rôle

Suivez ces étapes pour rendre un utilisateur éligible pour un rôle d’annuaire Azure AD.

1. Connectez-vous au [portail Azure](https://portal.azure.com/) à l’aide d’un nom d’utilisateur qui est membre du rôle [Administrateur de rôle privilégié](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator).

    Pour savoir comment accorder un autre accès utilisateur pour gérer PIM, consultez l’article [Comment accéder à PIM](pim-how-to-give-access-to-pim.md).

1. Ouvrez **Azure AD Privileged Identity Management**.

    Si vous n’avez pas encore activé PIM dans le portail Azure, accédez à [Prise en main d’Azure AD PIM](pim-getting-started.md).

1. Cliquez sur **Rôles d’annuaire Azure AD**.

1. Cliquez sur **Rôle (préversion)** ou **Membres**.

    ![Rôles d’annuaire Azure AD](./media/pim-how-to-add-role-to-user/pim-directory-roles.png)

1. Cliquez sur **Ajouter un membre** pour ouvrir la boîte de dialogue Ajouter des membres managés.

1. Cliquez sur **Sélectionner un rôle**, sélectionnez un rôle que vous souhaitez gérer, puis cliquez sur **Sélectionner**.

    ![Sélectionnez un rôle](./media/pim-how-to-add-role-to-user/pim-select-a-role.png)

1. Cliquez sur **Sélectionner des membres**, sélectionnez les utilisateurs que vous souhaitez assigner au rôle, puis cliquez sur **Sélectionner**.

    ![Sélectionnez un rôle](./media/pim-how-to-add-role-to-user/pim-select-members.png)

1. Sous Ajouter des membres managés, cliquez sur **OK** pour ajouter l’utilisateur au rôle.

     Une fois le rôle attribué, l’utilisateur que vous avez sélectionné apparaît comme **éligible** pour ce rôle dans la liste des membres.

    ![Utilisateur éligible pour un rôle](./media/pim-how-to-add-role-to-user/pim-directory-role-eligible.png)

1. Maintenant que l’utilisateur est éligible pour le rôle, indiquez-lui qu’il peut l’activer en suivant les instructions de l’article [Comment activer ou désactiver un rôle](pim-how-to-activate-role.md).

    Les administrateurs éligibles sont invités à s’inscrire au service d’authentification multifacteur (MFA) Azure lors de l’activation. Si un utilisateur ne peut pas s’inscrire à l’authentification multifacteur (MFA) Azure ou utilise un compte Microsoft (généralement @outlook.com), vous devez le rendre permanent dans tous ses rôles.

## <a name="make-a-role-assignment-permanent"></a>Rendre une attribution de rôle permanente

Par défaut, les nouveaux utilisateurs sont uniquement éligibles pour un rôle d’annuaire. Suivez ces étapes si vous souhaitez rendre une attribution de rôle permanente.

1. Ouvrez **Azure AD Privileged Identity Management**.

1. Cliquez sur **Rôles d’annuaire Azure AD**.

1. Cliquez sur **Membres**.

    ![Liste des membres](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members.png)

1. Cliquez sur un rôle **éligible** que vous souhaitez rendre permanent.

1. Cliquez sur **Plus**, puis sur **Rendre permanent**.

    ![Rendre une attribution de rôle permanente](./media/pim-how-to-add-role-to-user/pim-make-perm.png)

    Le rôle est désormais répertorié comme **permanent**.

    ![Liste des membres avec modification permanente](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members-permanent.png)

## <a name="remove-a-user-from-a-role"></a>Supprimer un utilisateur d’un rôle

Vous pouvez supprimer des utilisateurs des attributions de rôles, mais assurez-vous qu’il reste toujours au moins un administrateur général permanent. Si vous ne savez pas quels utilisateurs ont toujours besoin de leurs attributions de rôles, vous pouvez [démarrer une révision d’accès pour le rôle](pim-how-to-start-security-review.md).

Suivez ces étapes pour supprimer un utilisateur spécifique d’un rôle d’annuaire.

1. Ouvrez **Azure AD Privileged Identity Management**.

1. Cliquez sur **Rôles d’annuaire Azure AD**.

1. Cliquez sur **Membres**.

    ![Liste des membres](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members.png)

1. Cliquez sur l’attribution de rôle que vous souhaitez supprimer.

1. Cliquez sur **Plus**, puis sur **Supprimer**.

    ![Supprimer un rôle](./media/pim-how-to-add-role-to-user/pim-remove-role.png)

1. Dans le message qui vous invite à confirmer la suppression, cliquez sur **Oui**.

    ![Supprimer un rôle](./media/pim-how-to-add-role-to-user/pim-remove-role-confirm.png)

    L’attribution de rôle est supprimée.

## <a name="next-steps"></a>Étapes suivantes
[!INCLUDE [active-directory-privileged-identity-management-toc](../../../includes/active-directory-privileged-identity-management-toc.md)]
