---
title: Attribuer des rôles Azure AD dans PIM - Azure Active Directory | Microsoft Docs
description: Découvrez comment attribuer des rôles Azure AD dans Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 04/09/2019
ms.author: rolyon
ms.collection: M365-identity-device-management
ms.openlocfilehash: e1760d0e0bd356a05d84c07eda005e0526da5d13
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/29/2019
ms.locfileid: "67476525"
---
# <a name="assign-azure-ad-roles-in-pim"></a>Attribuer des rôles Azure AD dans PIM

Avec Azure Active Directory (Azure AD), un administrateur général peut effectuer des attributions de rôles d'administrateur Azure AD **permanentes**. Ces attributions de rôles peuvent être créées via le [portail Azure](../users-groups-roles/directory-assign-admin-roles.md) ou à l’aide de [commandes PowerShell](/powershell/module/azuread#directory_roles).

Le service Azure AD Privileged Identity Management (PIM) permet également aux administrateurs de rôle privilégié d’établir des attributions de rôles d'administrateur permanentes. En outre, les administrateurs de rôle privilégié peuvent rendre les utilisateurs **éligibles** pour les rôles d’administrateur Azure AD. Un administrateur éligible peut activer le rôle lorsqu’il en a besoin, puis l’autorisation expirera lorsqu’il aura terminé.

## <a name="make-a-user-eligible-for-a-role"></a>Rendre un utilisateur éligible pour un rôle

Suivez ces étapes pour rendre un utilisateur éligible pour un rôle d'administrateur Azure AD.

1. Connectez-vous au [portail Azure](https://portal.azure.com/) à l’aide d’un nom d’utilisateur qui est membre du rôle [Administrateur de rôle privilégié](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator).

    Pour plus d’informations sur la façon d’accorder l’accès à un autre administrateur pour la gestion de PIM, consultez [Accorder l’accès à d’autres administrateurs pour la gestion de PIM](pim-how-to-give-access-to-pim.md).

1. Ouvrez **Azure AD Privileged Identity Management**.

    Si vous n’avez pas encore démarré PIM dans le portail Azure, accédez à [commencer à utiliser PIM](pim-getting-started.md).

1. Cliquez sur **Rôles Azure AD**.

1. Cliquez sur **Rôles** ou sur **Membres**.

    ![Rôles Azure AD avec options de menu Rôles et Membres en surbrillance](./media/pim-how-to-add-role-to-user/pim-directory-roles.png)

1. Cliquez sur **Ajouter un membre** pour ouvrir la boîte de dialogue Ajouter des membres managés.

1. Cliquez sur **Sélectionner un rôle**, sélectionnez un rôle que vous souhaitez gérer, puis cliquez sur **Sélectionner**.

    ![Sélectionnez un volet de rôle répertoriant les rôles Azure AD](./media/pim-how-to-add-role-to-user/pim-select-a-role.png)

1. Cliquez sur **Sélectionner des membres**, sélectionnez les utilisateurs que vous souhaitez assigner au rôle, puis cliquez sur **Sélectionner**.

    ![Sélectionnez le volet des membres dans lequel vous pouvez sélectionner un utilisateur](./media/pim-how-to-add-role-to-user/pim-select-members.png)

1. Sous Ajouter des membres managés, cliquez sur **OK** pour ajouter l’utilisateur au rôle.

1. Dans la liste des rôles, cliquez sur le rôle que vous venez d’attribuer pour afficher la liste des membres.

     Une fois le rôle attribué, l’utilisateur que vous avez sélectionné apparaît comme **éligible** pour ce rôle dans la liste des membres.

    ![Les membres d’un rôle sont répertoriés avec leur état d’activation](./media/pim-how-to-add-role-to-user/pim-directory-role-eligible.png)

1. Maintenant que l’utilisateur est éligible pour le rôle, indiquez-lui qu’il peut l’activer en suivant les instructions contenues dans [Activer des rôles Azure AD dans PIM](pim-how-to-activate-role.md).

    Les administrateurs éligibles sont invités à s’inscrire au service d’authentification multifacteur (MFA) Azure lors de l’activation. Si un utilisateur ne peut pas s’inscrire à l’authentification multifacteur (MFA) Azure ou utilise un compte Microsoft (généralement @outlook.com), vous devez le rendre permanent dans tous ses rôles.

## <a name="make-a-role-assignment-permanent"></a>Rendre une attribution de rôle permanente

Par défaut, les nouveaux utilisateurs sont uniquement éligibles pour un rôle d’administrateur Azure AD. Suivez ces étapes si vous souhaitez rendre une attribution de rôle permanente.

1. Ouvrez **Azure AD Privileged Identity Management**.

1. Cliquez sur **Rôles Azure AD**.

1. Cliquez sur **Membres**.

    ![Rôles Azure AD - Liste des membres affichant le rôle et l’état d’activation](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members.png)

1. Cliquez sur un rôle **éligible** que vous souhaitez rendre permanent.

1. Cliquez sur **Plus**, puis sur **Rendre permanent**.

    ![Volet répertoriant un utilisateur éligible à un rôle avec les options de menu Plus ouvertes](./media/pim-how-to-add-role-to-user/pim-make-perm.png)

    Le rôle est désormais répertorié comme **permanent**.

    ![Liste des membres affichant le rôle et l’état d’activation désormais permanent](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members-permanent.png)

## <a name="remove-a-user-from-a-role"></a>Supprimer un utilisateur d’un rôle

Vous pouvez supprimer des utilisateurs des attributions de rôles, mais assurez-vous qu’il reste toujours au moins un administrateur général permanent. Si vous ne savez pas quels utilisateurs ont toujours besoin de leurs attributions de rôles, vous pouvez [démarrer une révision d’accès pour le rôle](pim-how-to-start-security-review.md).

Suivez ces étapes pour supprimer un utilisateur spécifique d’un rôle d'administrateur Azure AD.

1. Ouvrez **Azure AD Privileged Identity Management**.

1. Cliquez sur **Rôles Azure AD**.

1. Cliquez sur **Membres**.

    ![Rôles Azure AD - Liste des membres affichant le rôle et l’état d’activation](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members.png)

1. Cliquez sur l’attribution de rôle que vous souhaitez supprimer.

1. Cliquez sur **Plus**, puis sur **Supprimer**.

    ![Volet répertoriant un utilisateur disposant d’un rôle permanent avec les options de menu Plus ouvertes](./media/pim-how-to-add-role-to-user/pim-remove-role.png)

1. Dans le message qui vous invite à confirmer la suppression, cliquez sur **Oui**.

    ![Message demandant si vous souhaitez supprimer un membre d’un rôle](./media/pim-how-to-add-role-to-user/pim-remove-role-confirm.png)

    L’attribution de rôle est supprimée.

## <a name="authorization-error-when-assigning-roles"></a>Erreur d’autorisation lors de l’attribution de rôles

Si vous avez récemment activé PIM pour un abonnement et que vous obtenez une erreur d'autorisation lorsque vous essayez de rendre un utilisateur éligible pour un rôle d'administrateur Azure AD, c'est peut-être parce que le principe du service MS-PIM n'a pas encore les autorisations appropriées. Le principe du service MS-PIM doit avoir le rôle d’[administrateur de l’accès utilisateur](../../role-based-access-control/built-in-roles.md#user-access-administrator) pour affecter des rôles à d’autres personnes. Au lieu d'attendre que le rôle d’administrateur de l’accès utilisateur soit affecté à MS-PIM, vous pouvez l'affecter manuellement.

Suivez ces étapes pour affecter le rôle d’administrateur de l’accès utilisateur au principal du service MS-PIM pour un abonnement.

1. Connectez-vous au portail Azure en tant qu’administrateur général.

1. Sélectionnez **Tous les services**, puis **Abonnements**.

1. Choisissez votre abonnement.

1. Cliquez sur **Contrôle d’accès (IAM)** .

1. Choisissez **Attributions de rôles** pour afficher la liste actuelle des attributions de rôles au niveau de l’étendue de l’abonnement.

   ![Panneau Contrôle d’accès (IAM) pour un abonnement](./media/pim-how-to-add-role-to-user/ms-pim-access-control.png)

1. Vérifiez si le rôle d’**administrateur de l’accès utilisateur** est affecté au principal du service **MS-PIM**.

1. Sinon, choisissez **Ajouter une attribution de rôle** pour ouvrir le volet **Ajouter une attribution de rôle**.

1. Dans la liste déroulante **Rôle**, sélectionnez le rôle d’**administrateur de l’accès utilisateur**.

1. Dans la liste **Sélectionner**, recherchez et sélectionnez le principal du service **MS-PIM**.

   ![Volet Ajouter une attribution de rôle - Ajouter des autorisations pour le principal du service MS-PIM](./media/pim-how-to-add-role-to-user/ms-pim-add-permissions.png)

1. Choisissez **Enregistrer** pour attribuer le rôle.

   Après quelques instants, vérifiez si le rôle d’administrateur de l’accès utilisateur est affecté au principal du service MS-PIM au niveau de l’abonnement.

   ![Panneau de contrôle d’accès (IAM) affichant l’attribution de rôle Administrateur d’accès utilisateur pour MS-PIM](./media/pim-how-to-add-role-to-user/ms-pim-user-access-administrator.png)


## <a name="next-steps"></a>Étapes suivantes

- [Configurer les paramètres des rôles d’administrateur Azure AD dans PIM](pim-how-to-change-default-settings.md)
- [Attribuer des rôles de ressources Azure dans PIM](pim-resource-roles-assign-roles.md)
