---
title: Convier des invités et attribuer des rôles de ressources Azure dans PIM – Azure Active Directory | Microsoft Docs
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
ms.date: 04/09/2019
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 07476c9f5db64a5d107a493022fa3548fe0dae4c
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/29/2019
ms.locfileid: "67476353"
---
# <a name="invite-guest-users-and-assign-azure-resource-roles-in-pim"></a>Convier des invités et attribuer des rôles de ressources Azure dans PIM

Azure Active Directory (Azure AD) B2B regroupe, au sein d’Azure AD, plusieurs fonctionnalités qui permettent aux organisations de collaborer avec des utilisateurs et fournisseurs externes (invités) utilisant tout type de compte. Quand vous combinez B2B et Azure AD Privileged Identity Management (PIM), vous pouvez continuer à appliquer vos critères de conformité et de gouvernance à des invités. Par exemple, vous pouvez utiliser ces fonctionnalités de PIM pour des tâches liées aux identités Azure avec des invités :

- Attribuer l’accès à des ressources Azure spécifiques
- Activer l’accès juste-à-temps
- Spécifier la durée de l’attribution et la date de fin
- Exiger MFA sur une affectation active ou une activation
- Effectuer des révisions d’accès
- Utiliser des alertes et des journaux d’audit

Cet article explique comment convier un invité dans votre organisation et gérer son accès aux ressources Azure à l’aide d’Azure Active Directory Privileged Identity Management.

## <a name="when-would-you-invite-guests"></a>Quand voulez-vous convier des invités ?

Voici quelques exemples de scénarios où vous pourriez convier des invités à votre organisation :

- Autoriser un fournisseur externe indépendant qui dispose uniquement d’un compte e-mail à accéder à vos ressources Azure dans le cadre d’un projet.
- Autoriser un partenaire externe d’une grande organisation qui utilise les services de fédération Active Directory (AD FS) locaux à accéder à votre application de notes de frais.
- Autoriser les ingénieurs du support technique ne faisant pas partie de votre organisation, par exemple les ingénieurs du support technique Microsoft, à accéder temporairement à vos ressources Azure pour résoudre des problèmes.

## <a name="how-does-collaboration-using-b2b-guests-work"></a>Comment fonctionne la collaboration avec des invités B2B ?

Lorsque vous utilisez une collaboration B2B, vous pouvez inviter un utilisateur externe à votre organisation en tant qu’invité. L’invité semble se trouver dans votre organisation, mais n’a pas d’informations d’identification qui lui sont associées. Chaque fois qu’un invité doit être authentifié, il doit l’être dans son organisation d’origine, non dans la vôtre. Cela signifie que si l’invité n’a plus accès à son organisation d’origine, il n’a également plus accès à votre organisation. Par exemple, si l’invité quitte son organisation, il perd automatiquement l’accès à toutes les ressources que vous avez partagées avec lui dans Azure AD sans que vous ayez rien à faire. Pour plus d’informations sur B2B, consultez [Qu’est-ce que l’accès utilisateur invité dans Azure Active Directory B2B ?](../b2b/what-is-b2b.md).

![Diagramme montrant comment un utilisateur invité apparaît dans votre répertoire, alors qu’il est authentifié dans son répertoire d’origine](./media/pim-resource-roles-external-users/b2b-external-user.png)

## <a name="check-guest-collaboration-settings"></a>Vérifier les paramètres de collaboration d’invité

Pour vous assurer que vous pouvez convier des invités dans votre organisation, vous devez vérifier les paramètres de collaboration d’invité.

1. Connectez-vous au [portail Azure](https://portal.azure.com/).

1. Cliquez sur **Azure Active Directory** > **Paramètres utilisateur**.

1. Cliquez sur **Gérer les paramètres de collaboration externe**.

    ![Page des paramètres de collaboration externe montrant les paramètres de restriction d’autorisation, d’invitation et de collaboration](./media/pim-resource-roles-external-users/external-collaboration-settings.png)

1. Vérifiez que le commutateur **Les administrateurs et utilisateurs ayant le rôle d’inviteur invité peuvent inviter** a la valeur **Oui**.

## <a name="invite-a-guest-and-assign-a-role"></a>Convier un invité et attribuer un rôle

PIM vous permet de convier un invité et de le rendre éligible à un rôle de ressource Azure au même titre qu’un utilisateur membre.

1. Connectez-vous au [portail Azure](https://portal.azure.com/) avec un utilisateur membre du rôle [Administrateur de rôle privilégié](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) ou [Administrateur d’utilisateurs](../users-groups-roles/directory-assign-admin-roles.md#user-administrator).

1. Ouvrez **Azure AD Privileged Identity Management**.

1. Cliquez sur **Ressources Azure**.

1. Utilisez le **filtre de ressources** pour filtrer la liste des ressources managées.

1. Cliquez sur la ressource que vous souhaitez gérer, comme une ressource, un groupe de ressources, un abonnement ou un groupe d’administration.

    Définissez l’étendue pour qu’elle corresponde uniquement à ce dont l’invité a besoin.

1. Sous Gérer, cliquez sur **Rôles** pour afficher la liste des rôles pour les ressources Azure.

    ![Liste des rôles de ressources Azure indiquant le nombre d’utilisateurs actifs et éligibles](./media/pim-resource-roles-external-users/resources-roles.png)

1. Cliquez sur le rôle minimum dont l’utilisateur a besoin.

    ![Page de rôle sélectionné répertoriant les membres actuels de ce rôle](./media/pim-resource-roles-external-users/selected-role.png)

1. Dans la page de rôle, cliquez sur **Ajouter un membre** pour ouvrir le volet Nouvelle affectation.

1. Cliquez sur **Sélectionner un membre ou un groupe**.

    ![Nouvelle affectation – Sélectionnez un volet de membre ou de groupes répertoriant des utilisateurs et des groupes avec une option Inviter](./media/pim-resource-roles-external-users/select-member-group.png)

1. Pour convier un invité, cliquez sur **Inviter**.

    ![Page Convier un invité avec des zones pour entrer une adresse e-mail et spécifier un message personnel](./media/pim-resource-roles-external-users/invite-guest.png)

1. Après avoir sélectionné un invité, cliquez sur **inviter**.

    L’invité doit être ajouté en tant que membre sélectionné.

1. Dans le volet **Sélectionner un membre ou un groupe**, cliquez sur **Sélectionner**.

1. Dans le volet **Paramètres d’appartenance**, sélectionnez le type et la durée de l’affectation.

    ![Nouvelle affectation – Page de paramètres d’appartenance contenant des options pour spécifier le type d’affectation, ainsi que les dates de début et de fin de celle-ci](./media/pim-resource-roles-external-users/membership-settings.png)

1. Pour terminer l’attribution, cliquez sur **Terminé**, puis sur **Ajouter**.

    L’attribution de rôle d’invité s’affiche dans votre liste de rôles.

    ![Page de rôles indiquant que l’invité est éligible](./media/pim-resource-roles-external-users/role-assignment.png)

## <a name="activate-role-as-a-guest"></a>Activer un rôle en tant qu’invité

En tant qu’utilisateur externe, vous devez commencer par accepter l’invitation à votre organisation Azure AD, puis éventuellement activer votre rôle.

1. Ouvrez l’e-mail contenant votre invitation. L’e-mail ressemble au suivant.

    ![E-mail d’invitation avec nom d’annuaire, un message personnel et un lien pour commencer](./media/pim-resource-roles-external-users/email-invite.png)

1. Cliquez sur le lien **Bien démarrer** dans l’e-mail.

1. Après avoir passé en revue les autorisations, cliquez sur **Accepter**.

    ![Examinez dans un navigateur la page des autorisations contenant une liste d’autorisations que l’organisation aimerait que vous passiez en revue](./media/pim-resource-roles-external-users/invite-accept.png)

1. Il se peut que vous soyez invité à accepter des conditions d’utilisation et à spécifier si vous souhaitez rester connecté.

    Le portail Azure s’ouvre. Si vous avez simplement droit à un rôle, vous n’avez pas accès aux ressources.

1. Pour activer votre rôle, ouvrez l’e-mail contenant le lien d’activation du rôle. L’e-mail ressemble au suivant.

    ![Message de courrier provenant de PIM indiquant que vous êtes éligible pour un rôle et contenant un lien Activer le rôle](./media/pim-resource-roles-external-users/email-role-assignment.png)

1. Cliquez sur **Activer le rôle** pour ouvrir vos rôles éligibles dans PIM.

    ![Page Mes rôles dans PIM répertoriant vos rôles éligibles](./media/pim-resource-roles-external-users/my-roles-eligible.png)

1. Sous Action, cliquez sur le lien **Activer**.

    En fonction des paramètres du rôle, vous devez spécifier certaines informations pour activer le rôle.

1. Après avoir spécifié les paramètres du rôle, cliquez sur **Activer** pour activer le rôle.

    ![Page Activer affichant l’étendue et des options permettant de spécifier l’heure de début, la durée et le motif](./media/pim-resource-roles-external-users/activate-role.png)

    À moins que l’administrateur ne soit tenu d’approuver votre demande, vous devez à présent avoir accès aux ressources spécifiées.

## <a name="view-activity-for-a-guest"></a>Afficher l’activité d’un invité

Au même titre que vous pouvez suivre l’activité des utilisateurs membres, vous pouvez afficher des journaux d’audit pour faire le suivi de l’activité des invités.

1. En tant qu’administrateur, ouvrez PIM et sélectionnez la ressource qui a été partagée.

1. Cliquez sur **Audit des ressources** pour afficher l’activité pour cette ressource. Voici un exemple d’activité pour un groupe de ressources.

    ![Ressources Azure – Page Audit des ressources indiquant le temps, le demandeur et l’action](./media/pim-resource-roles-external-users/audit-resource.png)

1. Pour afficher l’activité de l’invité, cliquez sur **Azure Active Directory** > **Utilisateurs** > nom de l’invité.

1. Cliquez sur **Journaux d’audit** pour afficher les journaux d’audit pour l’organisation. Si nécessaire, vous pouvez spécifier des filtres.

    ![Journaux d’audit d’annuaire indiquant la date, la cible, la personne ayant initié l’activité et l’activité](./media/pim-resource-roles-external-users/audit-directory.png)

## <a name="next-steps"></a>Étapes suivantes

- [Attribuer des rôles Azure AD dans PIM](pim-how-to-add-role-to-user.md)
- [Qu’est-ce que l’accès utilisateur invité dans Azure Active Directory B2B ?](../b2b/what-is-b2b.md)
