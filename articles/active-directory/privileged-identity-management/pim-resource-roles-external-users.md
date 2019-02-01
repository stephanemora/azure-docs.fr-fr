---
title: Inviter des utilisateurs externes et attribuer des rôles de ressources Azure dans PIM | Microsoft Docs
description: Découvrez comment inviter des utilisateurs externes et attribuer des rôles de ressources Azure dans Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 11/29/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: f3cfb792b9c9befcbc2ee869ef5b31903e5c10f9
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55188607"
---
# <a name="invite-external-users-and-assign-azure-resource-roles-in-pim"></a>Inviter des utilisateurs externes et attribuer des rôles de ressources Azure dans PIM

Azure Activity Directory (Azure AD) B2B regroupe, au sein d’Azure AD, plusieurs fonctionnalités qui permettent aux organisations de collaborer avec des utilisateurs et des fournisseurs externes avec n’importe quel compte. Quand vous combinez B2B et Azure AD Privileged Identity Management (PIM), vous pouvez continuer à appliquer vos critères de conformité et de gouvernance aux utilisateurs externes. Par exemple, vous pouvez utiliser ces fonctionnalités PIM pour les ressources Azure avec des utilisateurs externes :

- Attribuer l’accès à des ressources Azure spécifiques
- Activer l’accès juste-à-temps
- Spécifier la durée de l’attribution et la date de fin
- Exiger MFA sur une affectation active ou une activation
- Effectuer des révisions d’accès
- Utiliser des alertes et des journaux d’audit

Cet article décrit comment inviter un utilisateur externe à votre annuaire et gérer son accès aux ressources Azure à l’aide de PIM.

## <a name="when-would-you-invite-external-users"></a>Quand pouvez-vous être amené à inviter des utilisateurs externes ?

Voici quelques exemples de scénarios dans lesquels vous pouvez inviter des utilisateurs externes à votre annuaire :

- Autoriser un fournisseur externe indépendant qui dispose uniquement d’un compte e-mail à accéder à vos ressources Azure dans le cadre d’un projet.
- Autoriser un partenaire externe d’une grande organisation qui utilise les services de fédération Active Directory (AD FS) locaux à accéder à votre application de notes de frais.
- Autoriser les ingénieurs du support technique ne faisant pas partie de votre organisation, par exemple les ingénieurs du support technique Microsoft, à accéder temporairement à vos ressources Azure pour résoudre des problèmes.

## <a name="how-does-external-collaboration-using-b2b-work"></a>Comment fonctionne la collaboration externe à l’aide de B2B ?

Quand vous utilisez B2B, vous pouvez inviter un utilisateur externe à votre annuaire. L’utilisateur externe semble figurer dans votre annuaire, mais il n’est associé à aucune information d’identification. Chaque fois qu’un utilisateur externe doit être authentifié, il est authentifié auprès de son annuaire de base et non auprès de votre annuaire. Cela signifie que l’utilisateur externe perd automatiquement l’accès à votre annuaire s’il n’a plus accès à son annuaire de base. Par exemple, si l’utilisateur externe quitte son organisation, il perd automatiquement l’accès à toutes les ressources que vous avez partagées avec lui dans votre annuaire sans aucune action de votre part. Pour plus d’informations sur B2B, consultez [Qu’est-ce que l’accès utilisateur invité dans Azure Active Directory B2B ?](../b2b/what-is-b2b.md).

![B2B et utilisateur externe](./media/pim-resource-roles-external-users/b2b-external-user.png)

## <a name="check-external-collaboration-settings"></a>Vérifier les paramètres de collaboration externe

Pour pouvoir inviter des utilisateurs externes dans votre annuaire, vous devez passer en revue vos paramètres de collaboration externe.

1. Connectez-vous au [portail Azure](https://portal.azure.com/).

1. Cliquez sur **Azure Active Directory** > **Paramètres utilisateur**.

1. Cliquez sur **Gérer les paramètres de collaboration externe**.

    ![Paramètres de collaboration externe](./media/pim-resource-roles-external-users/external-collaboration-settings.png)

1. Vérifiez que le commutateur **Les administrateurs et utilisateurs ayant le rôle d’inviteur invité peuvent inviter** a la valeur **Oui**.

## <a name="invite-an-external-user-and-assign-a-role"></a>Inviter un utilisateur externe et attribuer un rôle

PIM vous permet d’inviter un utilisateur externe pour qu’il ait droit, au même titre qu’un utilisateur membre, à un rôle de ressource Azure.

1. Connectez-vous au [portail Azure](https://portal.azure.com/) avec un utilisateur qui est membre du rôle [Administrateur de rôle privilégié](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) ou [Administrateur des comptes d’utilisateur](../users-groups-roles/directory-assign-admin-roles.md#user-account-administrator).

1. Ouvrez **Azure AD Privileged Identity Management**.

1. Cliquez sur **Ressources Azure**.

1. Utilisez le **filtre de ressources** pour filtrer la liste des ressources managées.

1. Cliquez sur la ressource que vous souhaitez gérer, comme une ressource, un groupe de ressources, un abonnement ou un groupe d’administration.

    Définissez l’étendue pour qu’elle corresponde uniquement à ce dont l’utilisateur externe a besoin.

1. Sous Gérer, cliquez sur **Rôles** pour afficher la liste des rôles pour les ressources Azure.

    ![Rôles de ressources Azure](./media/pim-resource-roles-external-users/resources-roles.png)

1. Cliquez sur le rôle minimum dont l’utilisateur a besoin.

    ![Rôle sélectionné](./media/pim-resource-roles-external-users/selected-role.png)

1. Dans la page de rôle, cliquez sur **Ajouter un membre** pour ouvrir le volet Nouvelle affectation.

1. Cliquez sur **Sélectionner un membre ou un groupe**.

    ![Sélectionner un membre ou un groupe](./media/pim-resource-roles-external-users/select-member-group.png)

1. Pour inviter un utilisateur externe, cliquez sur **Inviter**.

    ![Convier un invité](./media/pim-resource-roles-external-users/invite-guest.png)

1. Après avoir spécifié un utilisateur externe, cliquez sur **Inviter**.

    L’utilisateur externe doit être ajouté comme membre sélectionné.

1. Dans le volet Sélectionner un membre ou un groupe, cliquez sur **Sélectionner**.

1. Dans le volet Paramètres d’appartenance, sélectionnez le type et la durée de l’attribution.

    ![Paramètres d’appartenance](./media/pim-resource-roles-external-users/membership-settings.png)

1. Pour terminer l’attribution, cliquez sur **Terminé**, puis sur **Ajouter**.

    L’attribution de rôle d’utilisateur externe s’affiche dans votre liste de rôles.

    ![Attribution de rôle pour un utilisateur externe](./media/pim-resource-roles-external-users/role-assignment.png)

## <a name="activate-role-as-an-external-user"></a>Activer le rôle en tant qu’utilisateur externe

En tant qu’utilisateur externe, vous devez d’abord accepter l’invitation à l’annuaire Azure AD, puis éventuellement activer votre rôle.

1. Ouvrez l’e-mail contenant l’invitation à l’annuaire. L’e-mail ressemble au suivant.

    ![Invitation par e-mail](./media/pim-resource-roles-external-users/email-invite.png)

1. Cliquez sur le lien **Bien démarrer** dans l’e-mail.

1. Après avoir passé en revue les autorisations, cliquez sur **Accepter**.

    ![Passer en revue les autorisations](./media/pim-resource-roles-external-users/invite-accept.png)

1. Vous pouvez être invité à accepter les conditions d’utilisation et à spécifier si vous souhaitez rester connecté.

    Le portail Azure s’ouvre. Si vous avez simplement droit à un rôle, vous n’avez pas accès aux ressources.

1. Pour activer votre rôle, ouvrez l’e-mail contenant le lien d’activation du rôle. L’e-mail ressemble au suivant.

    ![Invitation par e-mail](./media/pim-resource-roles-external-users/email-role-assignment.png)

1. Cliquez sur **Activer le rôle** pour ouvrir vos rôles éligibles dans PIM.

    ![Mes rôles - Éligible](./media/pim-resource-roles-external-users/my-roles-eligible.png)

1. Sous Action, cliquez sur le lien **Activer**.

    En fonction des paramètres du rôle, vous devez spécifier certaines informations pour activer le rôle.

1. Après avoir spécifié les paramètres du rôle, cliquez sur **Activer** pour activer le rôle.

    ![Activer le rôle](./media/pim-resource-roles-external-users/activate-role.png)

    À moins que l’administrateur ne soit tenu d’approuver votre demande, vous devez à présent avoir accès aux ressources spécifiées.

## <a name="view-activity-for-an-external-user"></a>Afficher l’activité pour un utilisateur externe

Au même titre que vous pouvez suivre l’activité des utilisateurs membres, vous pouvez afficher des journaux d’audit pour faire le suivi de l’activité des utilisateurs externes.

1. En tant qu’administrateur, ouvrez PIM et sélectionnez la ressource qui a été partagée.

1. Cliquez sur **Audit des ressources** pour afficher l’activité pour cette ressource. Voici un exemple d’activité pour un groupe de ressources.

    ![Audit d’une ressource](./media/pim-resource-roles-external-users/audit-resource.png)

1. Pour afficher l’activité pour l’utilisateur externe, cliquez sur **Azure Active Directory** > **Utilisateurs** > utilisateur externe.

1. Cliquez sur **Journaux d’audit** pour afficher les journaux d’audit pour l’annuaire. Si nécessaire, vous pouvez spécifier des filtres.

    ![Audit de répertoire](./media/pim-resource-roles-external-users/audit-directory.png)

## <a name="next-steps"></a>Étapes suivantes

- [Attribuer des rôles d’annuaire Azure AD dans PIM](pim-how-to-add-role-to-user.md)
- [Qu’est-ce que l’accès utilisateur invité dans Azure Active Directory B2B ?](../b2b/what-is-b2b.md)
