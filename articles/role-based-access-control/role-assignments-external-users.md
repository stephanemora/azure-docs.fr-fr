---
title: Attribuer des rôles Azure à des utilisateurs invités externes à l’aide du portail Azure - Azure RBAC
description: Découvrez comment accorder l’accès aux ressources Azure pour les utilisateurs externes à une organisation en utilisant le portail Azure et le contrôle d’accès en fonction du rôle Azure (Azure RBAC).
services: active-directory
documentationcenter: ''
author: rolyon
manager: daveba
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: how-to
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 10/15/2021
ms.author: rolyon
ms.custom: it-pro,subject-rbac-steps
ms.openlocfilehash: 808e8e1dc5bcdcdddad490f30338c8f7a9e7ffa3
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130074263"
---
# <a name="assign-azure-roles-to-external-guest-users-using-the-azure-portal"></a>Attribuer des rôles Azure à des utilisateurs invités externes à l’aide du portail Azure

La fonctionnalité de [contrôle d’accès en fonction du rôle Azure (Azure RBAC)](overview.md) permet une meilleure gestion de la sécurité pour les grandes organisations et pour les PME travaillant avec des collaborateurs, fournisseurs ou travailleurs indépendants externes qui doivent pouvoir accéder à des ressources spécifiques de votre environnement, mais pas nécessairement à l’ensemble de l’infrastructure ou à des étendues liées à la facturation. Vous pouvez utiliser les fonctionnalités disponibles dans [Azure Active Directory B2B](../active-directory/external-identities/what-is-b2b.md) pour collaborer avec des utilisateurs invités externes. Vous pouvez aussi utiliser le contrôle d’accès en fonction du rôle (RBAC) pour n’accorder que les autorisations dont les utilisateurs invités ont besoin dans votre environnement.

## <a name="prerequisites"></a>Prérequis

Pour attribuer des rôles Azure ou supprimer des attributions de rôles, voici ce dont vous devez disposer :

- d’autorisations `Microsoft.Authorization/roleAssignments/write` et `Microsoft.Authorization/roleAssignments/delete`, telles que [Administrateur de l’accès utilisateur](built-in-roles.md#user-access-administrator) ou [Propriétaire de l’accès utilisateur](built-in-roles.md#owner)

## <a name="when-would-you-invite-guest-users"></a>Quand voulez-vous convier des utilisateurs invités ?

Voici quelques exemples de scénarios où vous pourriez convier des utilisateurs invités à votre organisation pour leur octroyer des autorisations :

- Autoriser un fournisseur externe indépendant qui dispose uniquement d’un compte e-mail à accéder à vos ressources Azure dans le cadre d’un projet.
- Autoriser un partenaire externe à gérer certaines ressources ou un abonnement entier.
- Autoriser les ingénieurs du support technique ne faisant pas partie de votre organisation, par exemple les ingénieurs du support technique Microsoft, à accéder temporairement à vos ressources Azure pour résoudre des problèmes.

## <a name="permission-differences-between-member-users-and-guest-users"></a>Différences d’autorisation entre les utilisateurs membres et les utilisateurs invités

Les membres natifs d’un annuaire (utilisateurs membres) ont des autorisations différentes de celles des utilisateurs invités appartenant à un autre annuaire, comme un invité à la collaboration B2B (utilisateurs invités). Par exemple, les utilisateurs membres peuvent lire presque toutes les informations d’annuaire tandis que les utilisateurs invités disposent d’autorisations limitées sur l’annuaire. Pour plus d’informations sur les utilisateurs membres et les utilisateurs invités, consultez [Quelles sont les autorisations utilisateur par défaut dans Azure Active Directory ?](../active-directory/fundamentals/users-default-permissions.md)

## <a name="add-a-guest-user-to-your-directory"></a>Ajouter un utilisateur invité à votre annuaire

Effectuez les étapes suivantes pour ajouter un utilisateur invité à votre annuaire à l’aide de la page Azure Active Directory.

1. Connectez-vous au [portail Azure](https://portal.azure.com).

1. Assurez-vous que les paramètres de collaboration externe de votre organisation sont configurés de telle sorte que vous êtes autorisé à inviter des invités. Pour plus d’informations, consultez [Permettre une collaboration B2B externe et gérer les utilisateurs autorisés à en inviter d’autres](../active-directory/external-identities/delegate-invitations.md).

1. Cliquez sur **Azure Active Directory** > **Utilisateurs** > **Nouvel utilisateur invité**.

    ![Capture d’écran de la fonctionnalité Nouvel utilisateur invité dans le portail Azure](./media/role-assignments-external-users/invite-guest-user.png)

1. Suivez les étapes pour ajouter un nouvel utilisateur invité. Pour plus d’informations, consultez [Ajouter des utilisateurs Azure Active Directory B2B Collaboration dans le portail Azure](../active-directory/external-identities/add-users-administrator.md#add-guest-users-to-the-directory).

Après avoir ajouté un utilisateur invité dans l’annuaire, vous pouvez envoyer à l’utilisateur invité un lien direct vers une application partagée, ou l’utilisateur invité peut cliquer sur le lien d’acceptation d’invitation dans l’e-mail d’invitation.

![Capture d’écran de l’e-mail d’invitation de l’utilisateur invité.](./media/role-assignments-external-users/invite-email.png)

Pour que l’utilisateur invité soit en mesure d’accéder à votre annuaire, il doit compléter le processus d’invitation.

![Capture d’écran de la révision des autorisations de l’utilisateur invité](./media/role-assignments-external-users/invite-review-permissions.png)

Pour plus d’informations sur le processus d’invitation, consultez [Utilisation d’invitations Azure Active Directory B2B Collaboration](../active-directory/external-identities/redemption-experience.md).

## <a name="assign-a-role-to-a-guest-user"></a>Attribuer un rôle à un utilisateur invité

Dans Azure RBAC, vous attribuez un rôle pour accorder un accès. Pour attribuer un rôle à un utilisateur invité, vous suivez les [mêmes étapes](role-assignments-portal.md) que pour un utilisateur membre, un groupe, un principal de service ou une identité managée. Procédez comme suit pour attribuer un rôle à un utilisateur invité à différentes étendues.

1. Connectez-vous au [portail Azure](https://portal.azure.com).

1. Dans la zone de recherche située en haut, recherchez l’étendue à laquelle vous souhaitez accorder l’accès. Par exemple, recherchez **Groupes d’administration**, **Abonnements**, **Groupes de ressources**, ou une ressource spécifique.

1. Cliquez sur la ressource spécifique pour cette étendue.

1. Cliquez sur **Contrôle d’accès (IAM)** .

    Voici un exemple de la page Contrôle d’accès (IAM) pour un groupe de ressources.

    ![Capture d’écran de la page Contrôle d’accès (IAM) d’un groupe de ressources.](./media/shared/rg-access-control.png)

1. Cliquez sur l’onglet **Attributions de rôles** afin d’afficher les attributions de rôles pour cette étendue.

1. Cliquez sur **Ajouter** > **Ajouter une attribution de rôle**.

    Si vous n’avez pas les autorisations pour attribuer des rôles, l’option Ajouter une attribution de rôle sera désactivée.

    ![Capture d’écran du menu Ajouter > Ajouter une attribution de rôle.](./media/shared/add-role-assignment-menu.png)

    La page Ajouter une attribution de rôle s’ouvre.

1. Dans l’onglet **Rôle**, sélectionnez un rôle, tel que **Contributeur de machines virtuelles**.

   ![Capture d’écran de la page Ajouter une attribution de rôle avec l’onglet Rôles.](./media/shared/roles.png)

1. Dans l’onglet **Membres**, sélectionnez **Utilisateur, groupe ou principal du service**.

   ![Capture d’écran de la page Ajouter une attribution de rôle avec l’onglet Membres.](./media/shared/members.png)

1. Cliquez sur **Sélectionner des membres**.

1. Recherchez et sélectionnez l’utilisateur invité. Si vous ne voyez pas l’utilisateur dans la liste, vous pouvez taper dans la zone **Sélectionner** pour rechercher le nom d’affichage ou l’adresse e-mail dans l’annuaire.

    Vous pouvez entrer du texte dans la zone **Sélectionner** pour rechercher des noms d’affichage ou des adresses e-mail dans l’annuaire.

    ![Capture d’écran du volet Sélectionner des membres.](./media/role-assignments-external-users/select-members.png)

1. Cliquez sur **Sélectionner** pour ajouter l’utilisateur invité à la liste Membres.

1. Dans l’onglet **Vérifier + attribuer**, cliquez sur **Vérifier + attribuer**.

    Après quelques instants, l’utilisateur invité se voit attribuer le rôle au niveau de l’étendue sélectionnée.

    ![Capture d’écran de l’attribution de rôle pour le contributeur de machine virtuelle](./media/role-assignments-external-users/access-control-role-assignments.png)

## <a name="assign-a-role-to-a-guest-user-not-yet-in-your-directory"></a>Attribuer un rôle à un utilisateur invité ne figurant pas encore dans votre annuaire

Pour attribuer un rôle à un utilisateur invité, vous suivez les [mêmes étapes](role-assignments-portal.md) que pour un utilisateur membre, un groupe, un principal de service ou une identité managée.

Si l’utilisateur invité ne figure pas encore dans votre annuaire, vous pouvez l’inviter directement à partir du volet Sélectionner des membres.

1. Connectez-vous au [portail Azure](https://portal.azure.com).

1. Dans la zone de recherche située en haut, recherchez l’étendue à laquelle vous souhaitez accorder l’accès. Par exemple, recherchez **Groupes d’administration**, **Abonnements**, **Groupes de ressources**, ou une ressource spécifique.

1. Cliquez sur la ressource spécifique pour cette étendue.

1. Cliquez sur **Contrôle d’accès (IAM)** .

1. Cliquez sur **Ajouter** > **Ajouter une attribution de rôle**.

    Si vous n’avez pas les autorisations pour attribuer des rôles, l’option Ajouter une attribution de rôle sera désactivée.

    ![Capture d’écran du menu Ajouter > Ajouter une attribution de rôle.](./media/shared/add-role-assignment-menu.png)

    La page Ajouter une attribution de rôle s’ouvre.

1. Dans l’onglet **Rôle**, sélectionnez un rôle, tel que **Contributeur de machines virtuelles**.

1. Dans l’onglet **Membres**, sélectionnez **Utilisateur, groupe ou principal du service**.

   ![Capture d’écran de la page Ajouter une attribution de rôle avec l’onglet Membres.](./media/shared/members.png)

1. Cliquez sur **Sélectionner des membres**.

1. Dans la liste **Sélectionner**, tapez l’adresse e-mail de la personne que vous voulez inviter, puis sélectionnez cette personne.

    ![Capture d’écran de la section Inviter un utilisateur invité dans le volet Sélectionner des membres.](./media/role-assignments-external-users/select-members-new-guest.png)

1. Cliquez sur **Sélectionner** pour ajouter l’utilisateur invité à la liste Membres.

1. Dans l’onglet **Vérifier + Attribuer**, cliquez sur **Vérifier + Attribuer** pour ajouter l’utilisateur invité à votre annuaire, attribuer le rôle, puis envoyer une invitation.

    Après quelques minutes, vous voyez une notification de l’attribution de rôle et des informations sur l’invitation.

    ![Capture d’écran de la notification d’attribution de rôle et d’utilisateur invité](./media/role-assignments-external-users/invited-user-notification.png)

1. Pour inviter manuellement l’utilisateur invité, cliquez avec le bouton droit et copiez le lien d’invitation inclus dans la notification. Ne cliquez pas sur le lien d’invitation, car cela démarre le processus d’invitation.

    Le lien d’invitation est au format suivant :

    `https://login.microsoftonline.com/redeem?rd=https%3a%2f%2finvitations.microsoft.com%2fredeem%2f%3ftenant%3d0000...`

1. Envoyez le lien d’invitation à l’utilisateur invité pour qu’il termine le processus d’invitation.

    Pour plus d’informations sur le processus d’invitation, consultez [Utilisation d’invitations Azure Active Directory B2B Collaboration](../active-directory/external-identities/redemption-experience.md).

## <a name="remove-a-guest-user-from-your-directory"></a>Supprimer un utilisateur invité de votre annuaire

Avant de supprimer un utilisateur invité d’un annuaire, vous devez d’abord supprimer toutes ses attributions de rôle. Suivez ces étapes pour supprimer un utilisateur invité d’un annuaire.

1. Ouvrez **Contrôle d’accès (IAM)** dans une étendue, comme un groupe d’administration, un abonnement, un groupe de ressources ou une ressource, dans laquelle l’utilisateur invité a une attribution de rôle.

1. Cliquez sur l’onglet **Attributions de rôles** afin d’afficher toutes les attributions de rôle.

1. Dans la liste des attributions de rôle, ajoutez une coche en regard de l’utilisateur invité comportant l’attribution de rôle à supprimer.

   ![Capture d’écran de l’attribution de rôle sélectionnée à supprimer.](./media/role-assignments-external-users/remove-role-assignment-select.png)

1. Cliquez sur **Supprimer**.

   ![Capture d’écran du message de suppression d’attribution de rôle.](./media/shared/remove-role-assignment.png)

1. Dans le message d’attribution de rôle qui s’affiche, cliquez sur **Oui**.

1. Dans la barre de navigation gauche, cliquez sur **Azure Active Directory** > **Utilisateurs**.

1. Cliquez sur l’utilisateur invité à supprimer.

1. Cliquez sur **Supprimer**.

   ![Capture d’écran de la suppression de l’utilisateur invité.](./media/role-assignments-external-users/delete-guest-user.png)

1. Dans le message de suppression qui apparaît, cliquez sur **Yes** (Oui).

## <a name="troubleshoot"></a>Dépanner

### <a name="guest-user-cannot-browse-the-directory"></a>Impossible pour l’utilisateur invité de parcourir l’annuaire

Les utilisateurs invités disposent d'autorisations d'annuaire limitées. Par exemple, les utilisateurs invités ne peuvent pas parcourir l’annuaire ni rechercher des groupes ou des applications. Pour plus d’informations, consultez [Quelles sont les autorisations utilisateur par défaut dans Azure Active Directory ?](../active-directory/fundamentals/users-default-permissions.md)

![Capture d’écran montrant qu’il est impossible pour l’utilisateur invité de parcourir les utilisateurs figurant dans un annuaire.](./media/role-assignments-external-users/directory-no-users.png)

Si un utilisateur invité a besoin de privilèges supplémentaires sur l’annuaire, vous pouvez lui attribuer un rôle Azure AD. Si vous voulez vraiment qu’un utilisateur invité dispose d’un accès en lecture complet à votre annuaire, vous pouvez l’ajouter au rôle [Lecteurs de répertoire](../active-directory/roles/permissions-reference.md#directory-readers) dans Azure AD. Pour plus d’informations, consultez [Ajouter des utilisateurs Azure Active Directory B2B Collaboration dans le portail Azure](../active-directory/external-identities/add-users-administrator.md).

![Capture d’écran de l’affectation du rôle lecteurs de répertoire.](./media/role-assignments-external-users/directory-roles.png)

### <a name="guest-user-cannot-browse-users-groups-or-service-principals-to-assign-roles"></a>Impossible pour l’utilisateur invité de parcourir les utilisateurs, les groupes ou les principaux du service pour attribuer des rôles

Les utilisateurs invités disposent d'autorisations d'annuaire limitées. Même si un utilisateur invité est [propriétaire](built-in-roles.md#owner) dans une étendue, s’il essaie d’attribuer un rôle pour accorder l’accès à une autre personne, il ne peut pas parcourir la liste des utilisateurs, des groupes ou des principaux de service.

![Capture d’écran montrant qu’il est impossible pour l’utilisateur invité de parcourir les principaux de sécurité pour attribuer des rôles](./media/role-assignments-external-users/directory-no-browse.png)

Si l’utilisateur invité connaît le nom de connexion exact d’une personne dans l’annuaire, il peut lui accorder l’accès. Si vous voulez vraiment qu’un utilisateur invité dispose d’un accès en lecture complet à votre annuaire, vous pouvez l’ajouter au rôle [Lecteurs de répertoire](../active-directory/roles/permissions-reference.md#directory-readers) dans Azure AD. Pour plus d’informations, consultez [Ajouter des utilisateurs Azure Active Directory B2B Collaboration dans le portail Azure](../active-directory/external-identities/add-users-administrator.md).

### <a name="guest-user-cannot-register-applications-or-create-service-principals"></a>Impossible pour l’utilisateur invité d’inscrire des applications ou de créer des principaux du service

Les utilisateurs invités disposent d'autorisations d'annuaire limitées. Si un utilisateur invité a besoin de pouvoir inscrire des applications ou de créer des principaux du service, vous pouvez l’ajouter au rôle [Développeur d’applications](../active-directory/roles/permissions-reference.md#application-developer) dans Azure AD. Pour plus d’informations, consultez [Ajouter des utilisateurs Azure Active Directory B2B Collaboration dans le portail Azure](../active-directory/external-identities/add-users-administrator.md).

![Capture d’écran montrant qu’il est impossible pour l’utilisateur invité d’inscrire des applications.](./media/role-assignments-external-users/directory-access-denied.png)

### <a name="guest-user-does-not-see-the-new-directory"></a>Nouvel annuaire invisible pour l’utilisateur invité

Si un utilisateur invité a reçu l’autorisation d’accéder à un nouvel annuaire, mais qu’il ne voit pas cet annuaire répertorié dans le portail Azure quand il essaie de basculer vers sa page **Annuaires**, vérifiez qu’il a bien achevé le processus d’invitation. Pour plus d’informations sur le processus d’invitation, consultez [Utilisation d’invitations Azure Active Directory B2B Collaboration](../active-directory/external-identities/redemption-experience.md).

### <a name="guest-user-does-not-see-resources"></a>Ressources invisibles pour l’utilisateur

Si un utilisateur invité a reçu l’autorisation d’accéder à un annuaire, mais qu’il ne voit pas les ressources auxquelles il est autorisé à accéder dans le portail Azure, vérifiez qu’il a sélectionné le bon annuaire. Un utilisateur invité peut avoir accès à plusieurs annuaires. Pour changer d’annuaire, dans le coin supérieur gauche, cliquez sur **Paramètres** > **Annuaires**, puis cliquez sur l’annuaire approprié.

![Capture d’écran de la section Annuaires des paramètres du portail sur le portail Azure.](./media/role-assignments-external-users/directory-switch.png)

## <a name="next-steps"></a>Étapes suivantes

- [Ajouter des utilisateurs Azure Active Directory B2B Collaboration dans le Portail Azure](../active-directory/external-identities/add-users-administrator.md)
- [Propriétés d’un utilisateur Azure Active Directory B2B Collaboration](../active-directory/external-identities/user-properties.md)
- [Éléments de l’e-mail d’invitation de collaboration B2B - Azure Active Directory](../active-directory/external-identities/invitation-email-elements.md)
- [Ajouter un utilisateur invité en tant que coadministrateur](classic-administrators.md#add-a-guest-user-as-a-co-administrator)