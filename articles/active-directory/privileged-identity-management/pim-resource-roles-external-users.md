---
title: Inviter des invités externes et affecter des rôles d’administrateur Azure AD - Privileged Identity Management | Microsoft Docs
description: Découvrez comment inviter des utilisateurs et affecter des rôles d’administrateur Azure Active Directory dans Azure AD Privileged Identity Management.
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
ms.date: 03/13/2019
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 68e76a4513d94cceb8e856c94ad6eae2bdab9c46
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/18/2019
ms.locfileid: "57853254"
---
# <a name="invite-guest-users-and-assign-azure-resource-access-in-pim"></a>Inviter des utilisateurs et affecter l’accès aux ressources Azure dans PIM

Azure Active Directory (Azure AD) business-to-business (B2B) est un ensemble de fonctionnalités au sein d’Azure AD qui permet aux organisations de collaborer avec des utilisateurs invités externes (invités) et les fournisseurs à l’aide de n’importe quel compte. Lorsque vous combinez B2B avec Azure AD Privileged Identity Management (PIM), vous pouvez continuer à appliquer vos exigences de conformité et de gouvernance aux invités. Par exemple, vous pouvez utiliser ces fonctionnalités PIM pour les tâches de gestion des identités Azure avec invités :

- Attribuer l’accès à des ressources Azure spécifiques
- Activer l’accès juste-à-temps
- Spécifier la durée de l’attribution et la date de fin
- Exiger MFA sur une affectation active ou une activation
- Effectuer des révisions d’accès
- Utiliser des alertes et des journaux d’audit

Cet article décrit comment convier un invité à votre organisation et de gérer leur accès aux ressources Azure à l’aide de Privileged Identity Management.

## <a name="when-would-you-invite-guests"></a>Lorsque vous invitez invités ?

Voici quelques exemples de scénarios vous pouvez inviter des invités à votre organisation :

- Autoriser un fournisseur externe indépendant qui dispose uniquement d’un compte e-mail à accéder à vos ressources Azure dans le cadre d’un projet.
- Autoriser un partenaire externe d’une grande organisation qui utilise les services de fédération Active Directory (AD FS) locaux à accéder à votre application de notes de frais.
- Autoriser les ingénieurs du support technique ne faisant pas partie de votre organisation, par exemple les ingénieurs du support technique Microsoft, à accéder temporairement à vos ressources Azure pour résoudre des problèmes.

## <a name="how-does-collaboration-using-b2b-guests-work"></a>Fonctionnement de collaboration à l’aide de B2B invités travail ?

Lorsque vous utilisez B2B collaboration, vous pouvez inviter un utilisateur externe à votre organisation en tant qu’invité. L’invité apparaît dans votre organisation, mais l’invité n’a pas d’informations d’identification associées. Chaque fois qu’un invité doit être authentifié, ils doivent s’authentifier dans leur organisation d’origine et non dans votre organisation. Cela signifie que si l’invité n’a plus accès à leur organisation d’origine, ils perdent également l’accès à votre organisation. Par exemple, si l’invité quitte son organisation, ils perdent automatiquement accès à toutes les ressources partagées avec eux dans Azure AD sans avoir rien à faire. Pour plus d’informations sur B2B, consultez [Qu’est-ce que l’accès utilisateur invité dans Azure Active Directory B2B ?](../b2b/what-is-b2b.md).

![B2B et invité](./media/pim-resource-roles-external-users/b2b-external-user.png)

## <a name="check-guest-collaboration-settings"></a>Vérifiez les paramètres de collaboration d’invité

Pour vous assurer que vous pouvez inviter des invités dans votre organisation, vous devez vérifier vos paramètres de collaboration invité.

1. Connectez-vous au [portail Azure](https://portal.azure.com/).

1. Cliquez sur **Azure Active Directory** > **Paramètres utilisateur**.

1. Cliquez sur **Gérer les paramètres de collaboration externe**.

    ![Paramètres de collaboration externe](./media/pim-resource-roles-external-users/external-collaboration-settings.png)

1. Vérifiez que le commutateur **Les administrateurs et utilisateurs ayant le rôle d’inviteur invité peuvent inviter** a la valeur **Oui**.

## <a name="invite-a-guest-and-assign-a-role"></a>Convier un invité et attribuer un rôle

À l’aide de PIM, vous pouvez convier un invité et les rendre éligibles pour un rôle de ressource Azure comme un utilisateur membre.

1. Se connecter à [Azure portal](https://portal.azure.com/) avec un utilisateur qui est membre de la [administrateur de rôle privilégié](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) ou [utilisateur administrateur](../users-groups-roles/directory-assign-admin-roles.md#user-administrator) rôle.

1. Ouvrez **Azure AD Privileged Identity Management**.

1. Cliquez sur **Ressources Azure**.

1. Utilisez le **filtre de ressources** pour filtrer la liste des ressources managées.

1. Cliquez sur la ressource que vous souhaitez gérer, comme une ressource, un groupe de ressources, un abonnement ou un groupe d’administration.

    Vous devez définir l’étendue sur uniquement quel l’invité a besoin.

1. Sous Gérer, cliquez sur **Rôles** pour afficher la liste des rôles pour les ressources Azure.

    ![Rôles de ressources Azure](./media/pim-resource-roles-external-users/resources-roles.png)

1. Cliquez sur le rôle minimum dont l’utilisateur a besoin.

    ![Rôle sélectionné](./media/pim-resource-roles-external-users/selected-role.png)

1. Dans la page de rôle, cliquez sur **Ajouter un membre** pour ouvrir le volet Nouvelle affectation.

1. Cliquez sur **Sélectionner un membre ou un groupe**.

    ![Sélectionner un membre ou un groupe](./media/pim-resource-roles-external-users/select-member-group.png)

1. Pour inviter un invité, cliquez sur **inviter**.

    ![Convier un invité](./media/pim-resource-roles-external-users/invite-guest.png)

1. Une fois que vous avez sélectionné un invité, cliquez sur **inviter**.

    L’invité doit être ajouté comme membre sélectionné.

1. Dans le **sélectionner un membre ou un groupe** volet, cliquez sur **sélectionnez**.

1. Dans le **paramètres d’appartenance** volet, sélectionnez le type d’affectation et la durée.

    ![Paramètres d’appartenance](./media/pim-resource-roles-external-users/membership-settings.png)

1. Pour terminer l’attribution, cliquez sur **Terminé**, puis sur **Ajouter**.

    L’attribution de rôle d’invité s’affiche dans votre liste des rôles.

    ![Attribution de rôle pour l’invité](./media/pim-resource-roles-external-users/role-assignment.png)

## <a name="activate-role-as-a-guest"></a>Activer le rôle en tant qu’invité

En tant qu’un utilisateur externe, vous devez d’abord accepter l’invitation pour votre organisation Azure AD et éventuellement activer votre rôle.

1. Ouvrez l’e-mail d’invitation. L’e-mail ressemble au suivant.

    ![Invitation par e-mail](./media/pim-resource-roles-external-users/email-invite.png)

1. Cliquez sur le lien **Bien démarrer** dans l’e-mail.

1. Après avoir passé en revue les autorisations, cliquez sur **Accepter**.

    ![Passer en revue les autorisations](./media/pim-resource-roles-external-users/invite-accept.png)

1. Vous pouvez être invité à accepter les conditions d’utilisation et à spécifier si vous souhaitez rester connecté.

    Le portail Azure s’ouvre. Si vous avez simplement droit à un rôle, vous n’avez pas accès aux ressources.

1. Pour activer votre rôle, ouvrez l’e-mail contenant le lien d’activation du rôle. L’e-mail ressemble au suivant.

    ![Invitation par e-mail](./media/pim-resource-roles-external-users/email-role-assignment.png)

1. Cliquez sur **Activer le rôle** pour ouvrir vos rôles éligibles dans PIM.

    ![Mes rôles - éligibles](./media/pim-resource-roles-external-users/my-roles-eligible.png)

1. Sous Action, cliquez sur le lien **Activer**.

    En fonction des paramètres du rôle, vous devez spécifier certaines informations pour activer le rôle.

1. Après avoir spécifié les paramètres du rôle, cliquez sur **Activer** pour activer le rôle.

    ![Activer le rôle](./media/pim-resource-roles-external-users/activate-role.png)

    À moins que l’administrateur ne soit tenu d’approuver votre demande, vous devez à présent avoir accès aux ressources spécifiées.

## <a name="view-activity-for-a-guest"></a>Afficher l’activité d’un invité

Tout comme un utilisateur membre, vous pouvez afficher les journaux d’audit pour effectuer le suivi de ce que font les invités.

1. En tant qu’administrateur, ouvrez PIM et sélectionnez la ressource qui a été partagée.

1. Cliquez sur **Audit des ressources** pour afficher l’activité pour cette ressource. Voici un exemple d’activité pour un groupe de ressources.

    ![Audit d’une ressource](./media/pim-resource-roles-external-users/audit-resource.png)

1. Pour afficher l’activité de l’invité, cliquez sur **Azure Active Directory** > **utilisateurs** > nom de l’invité.

1. Cliquez sur **journaux d’Audit** pour afficher les journaux d’audit pour l’organisation. Si nécessaire, vous pouvez spécifier des filtres.

    ![audit de l’organisation](./media/pim-resource-roles-external-users/audit-directory.png)

## <a name="next-steps"></a>Étapes suivantes

- [Affecter des rôles d’administrateur Azure AD dans PIM](pim-how-to-add-role-to-user.md)
- [Qu’est-ce que l’accès utilisateur invité dans Azure Active Directory B2B ?](../b2b/what-is-b2b.md)
