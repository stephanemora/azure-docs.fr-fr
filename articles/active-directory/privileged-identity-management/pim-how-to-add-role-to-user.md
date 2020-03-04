---
title: Attribuer des rôles Azure AD dans PIM - Azure Active Directory | Microsoft Docs
description: Découvrez comment attribuer des rôles Azure AD dans Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 02/07/2020
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5048cefaae10cd55091dd72f0b73a3cf9d731a35
ms.sourcegitcommit: 0a9419aeba64170c302f7201acdd513bb4b346c8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/20/2020
ms.locfileid: "77498857"
---
# <a name="assign-azure-ad-roles-in-privileged-identity-management"></a>Attribuer des rôles dans Azure AD dans Privileged Identity Management

Avec Azure Active Directory (Azure AD), un administrateur général peut effectuer des attributions de rôles d'administrateur Azure AD **permanentes**. Ces attributions de rôles peuvent être créées via le [portail Azure](../users-groups-roles/directory-assign-admin-roles.md) ou à l’aide de [commandes PowerShell](/powershell/module/azuread#directory_roles).

Le service Azure AD Privileged Identity Management (PIM) permet également aux Administrateurs de rôle privilégié d’établir des attributions permanentes du rôle Administrateur. En outre, les Administrateurs de rôle privilégié peuvent rendre les utilisateurs **éligibles** pour les rôles Administrateur d’Azure AD. Un administrateur éligible peut activer le rôle lorsqu’il en a besoin, puis l’autorisation expirera lorsqu’il aura terminé.

## <a name="determine-your-version-of-pim"></a>Déterminer votre version de PIM

Depuis novembre 2019, la partie Rôles Azure AD de Privileged Identity Management est mise à jour vers une nouvelle version qui correspond à l’expérience des rôles de ressources Azure. Cela permet de créer des fonctionnalités supplémentaires et d’apporter des [modifications à l’API existante](azure-ad-roles-features.md#api-changes). Pendant que la nouvelle version est déployée, les procédures que vous suivez dans cet article dépendent de la version de Privileged Identity Management que vous possédez actuellement. Pour déterminer la version de Privileged Identity Management dont vous disposez, procédez de la manière décrite dans cette section. Une fois que vous connaissez votre version de Privileged Identity Management, vous pouvez sélectionner les procédures de cet article qui correspondent à cette version.

1. Connectez-vous au [portail Azure](https://portal.azure.com/) à l’aide d’un utilisateur avec le rôle [Administrateur de rôle privilégié](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator).
1. Ouvrez **Azure AD Privileged Identity Management**. Si une bannière figure en haut de la page de présentation, suivez les instructions sous l’onglet **Nouvelle version** de cet article. Sinon, suivez les instructions sous l’onglet **Version précédente**.

  [![](media/pim-how-to-add-role-to-user/pim-new-version.png "Select Azure AD > Privileged Identity Management")](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

# <a name="new-version"></a>[Nouvelle version](#tab/new)

## <a name="assign-a-role"></a>Attribuer un rôle

Suivez ces étapes pour rendre un utilisateur éligible pour un rôle d'administrateur Azure AD.

1. Connectez-vous au [Portail Azure](https://portal.azure.com/) à l’aide d’un nom d’utilisateur qui est membre du rôle [Administrateur de rôle privilégié](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator).

    Pour plus d’informations sur la façon d’accorder l’accès à un autre administrateur pour la gestion de Privileged Identity Management, consultez [Accorder l’accès à d’autres administrateurs pour la gestion de Privileged Identity Management](pim-how-to-give-access-to-pim.md).

1. Ouvrez **Azure AD Privileged Identity Management**.

1. Sélectionnez des **rôles Azure AD**.

1. Sélectionnez **Rôles** pour afficher la liste des rôles pour les autorisations Azure AD.

    ![Rôles Azure AD](./media/pim-how-to-add-role-to-user/roles-list.png)

1. Sélectionnez **Ajouter un membre** pour ouvrir la page **Nouvelle affectation**.

1. Choisissez **Sélectionner un rôle** pour ouvrir la page Sélectionner un rôle.

    ![Volet Nouvelle affectation](./media/pim-how-to-add-role-to-user/select-role.png)

1. Sélectionnez un rôle que vous souhaitez attribuer et cliquez sur **Sélectionner**.

1. Sélectionnez un membre à affecter au rôle, puis cliquez sur **Sélectionner**.

1. Dans la liste **Type d’affectation** du volet **Paramètres d’appartenance**, sélectionnez **Éligible** ou **Actif**.

    - Les attributions **éligibles** exigent des membres qu’ils effectuent une action pour utiliser ce rôle. Il peut s’agir de procéder à une vérification de l’authentification multifacteur (MFA), de fournir une justification professionnelle ou de demander une approbation aux approbateurs désignés.

    - Les attributions **actives** n’exigent pas des membres qu’ils effectuent une action pour utiliser ce rôle. Les membres attribués comme étant actifs détiennent à tout moment les privilèges affectés au rôle.

1. Si l’attribution doit être permanente (éligible de façon permanente ou attribuée définitivement), cochez la case **Définitivement**.

    Selon les paramètres du rôle, cette case à cocher peut ne pas être affichée ou être non modifiable.

1. Pour spécifier une durée d’attribution spécifique, décochez la case et modifiez les champs des heures et dates de début et/ou de fin. Quand vous avez terminé, sélectionnez **Terminé**.

    ![Paramètres des appartenances - date et heure](./media/pim-how-to-add-role-to-user/start-and-end-dates.png)

1. Pour créer la nouvelle attribution de rôle, sélectionnez **Ajouter**. Une notification de l’état s’affiche.

    ![Nouvelle affectation - Notification](./media/pim-how-to-add-role-to-user/assignment-notification.png)

## <a name="update-or-remove-an-existing-role-assignment"></a>Mettre à jour ou supprimer une attribution de rôle existante

Suivez ces étapes pour mettre à jour ou supprimer une attribution de rôle existante.

1. Ouvrez **Azure AD Privileged Identity Management**.

1. Sélectionnez des **rôles Azure AD**.

1. Sélectionnez **Rôles** pour afficher la liste des rôles pour Azure AD.

1. Sélectionnez le rôle que vous souhaitez mettre à jour ou supprimer.

1. Recherchez l’attribution de rôle sous les onglets **Rôles éligibles** et **Rôles actifs**.

    ![Mettre à jour ou supprimer une attribution de rôle](./media/pim-how-to-add-role-to-user/remove-update-assignments.png)

1. Sélectionnez **Mettre à jour** ou **Supprimer** pour mettre à jour ou supprimer l’attribution de rôle.

# <a name="previous-version"></a>[Version précédente](#tab/previous)

## <a name="make-a-user-eligible-for-a-role"></a>Rendre un utilisateur éligible pour un rôle

Suivez ces étapes pour rendre un utilisateur éligible pour un rôle d'administrateur Azure AD.

1. Sélectionnez **Rôles** ou **Membres**.

    ![Rôles Azure AD](./media/pim-how-to-add-role-to-user/pim-directory-roles.png)

1. Sélectionnez **Ajouter un membre** pour ouvrir **Ajouter des membres managés**.

1. Sélectionnez **Sélectionner un rôle**, sélectionnez un rôle que vous souhaitez gérer, puis sélectionnez **Sélectionner**.

    ![Sélectionnez un rôle](./media/pim-how-to-add-role-to-user/pim-select-a-role.png)

1. Sélectionnez **Sélectionner des membres**, sélectionnez les utilisateurs que vous souhaitez assigner au rôle, puis sélectionnez **Sélectionner**.

    ![Sélectionnez un rôle](./media/pim-how-to-add-role-to-user/pim-select-members.png)

1. Sous **Ajouter des membres managés**, sélectionnez **OK** pour ajouter l’utilisateur au rôle.

1. Dans la liste des rôles, cliquez sur le rôle que vous venez d’attribuer pour afficher la liste des membres.

     Une fois le rôle attribué, l’utilisateur que vous avez sélectionné apparaît comme **éligible** pour ce rôle dans la liste des membres.

    ![Utilisateur éligible pour un rôle](./media/pim-how-to-add-role-to-user/pim-directory-role-eligible.png)

1. Maintenant que l’utilisateur est éligible pour le rôle, indiquez-lui qu’il peut l’activer en suivant les instructions contenues dans [Activer mes rôles Azure AD dans Privileged Identity Management](pim-how-to-activate-role.md).

    Les administrateurs éligibles sont invités à s’inscrire au service d’authentification multifacteur (MFA) Azure lors de l’activation. Si un utilisateur ne peut pas s’inscrire pour l’authentification multifacteur (MFA) Azure ou utilise un compte Microsoft (tel que @outlook.com), vous devez le rendre permanent dans tous ses rôles.

## <a name="make-a-role-assignment-permanent"></a>Rendre une attribution de rôle permanente

Par défaut, les nouveaux utilisateurs sont uniquement *éligibles* pour un rôle d’administrateur Azure AD. Suivez ces étapes si vous souhaitez rendre une attribution de rôle permanente.

1. Ouvrez **Azure AD Privileged Identity Management**.

1. Sélectionnez des **rôles Azure AD**.

1. Sélectionnez **Membres**.

    ![Liste des membres](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members.png)

1. Sélectionnez un rôle **Éligible** que vous souhaitez rendre permanent.

1. Sélectionnez **Plus**, puis **Rendre permanent**.

    ![Rendre une attribution de rôle permanente](./media/pim-how-to-add-role-to-user/pim-make-perm.png)

    Le rôle est désormais répertorié comme **permanent**.

    ![Liste des membres avec modification permanente](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members-permanent.png)

## <a name="remove-a-user-from-a-role"></a>Supprimer un utilisateur d’un rôle

Vous pouvez supprimer des utilisateurs des attributions de rôles, mais assurez-vous qu’il reste toujours au moins un administrateur général permanent. Si vous ne savez pas quels utilisateurs ont toujours besoin de leurs attributions de rôles, vous pouvez [démarrer une révision d’accès pour le rôle](pim-how-to-start-security-review.md).

Suivez ces étapes pour supprimer un utilisateur spécifique d’un rôle d'administrateur Azure AD.

1. Ouvrez **Azure AD Privileged Identity Management**.

1. Sélectionnez des **rôles Azure AD**.

1. Sélectionnez **Membres**.

    ![Liste des membres](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members.png)

1. Sélectionnez une attribution de rôle à supprimer.

1. Sélectionnez **Plus**, puis **Supprimer**.

    ![Supprimer un rôle](./media/pim-how-to-add-role-to-user/pim-remove-role.png)

1. Dans le message qui vous invite à confirmer la suppression, sélectionnez **Oui**.

    ![Supprimer un rôle](./media/pim-how-to-add-role-to-user/pim-remove-role-confirm.png)

    L’attribution de rôle est supprimée.

## <a name="authorization-error-when-assigning-roles"></a>Erreur d’autorisation lors de l’attribution de rôles

Si vous avez récemment activé Privileged Identity Management pour un abonnement et obtenez une erreur d’autorisation lorsque vous essayez de rendre un utilisateur éligible pour un rôle d’administrateur Azure AD, c’est peut-être parce que le principal du service MS-PIM n’a pas encore les autorisations appropriées. Pour pouvoir attribuer des rôles à d’autres personnes, le principal du service MS-PIM doit avoir le rôle [Administrateur de l’accès utilisateur](../../role-based-access-control/built-in-roles.md#user-access-administrator). Au lieu d'attendre que le rôle d’administrateur de l’accès utilisateur soit affecté à MS-PIM, vous pouvez l'affecter manuellement.

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

   ![Page de contrôle d’accès indiquant l’attribution du rôle Administration de l’accès utilisateur pour le principal du service MS-PIM](./media/pim-how-to-add-role-to-user/ms-pim-user-access-administrator.png)

 ---

## <a name="next-steps"></a>Étapes suivantes

- [Configurer les paramètres des rôles d’administrateur Azure AD dans Privileged Identity Management](pim-how-to-change-default-settings.md)
- [Attribuer des rôles de ressources Azure dans Privileged Identity Management](pim-resource-roles-assign-roles.md)