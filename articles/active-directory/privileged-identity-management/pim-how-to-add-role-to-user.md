---
title: Attribuer des rôles Azure AD dans PIM - Azure Active Directory | Microsoft Docs
description: Découvrez comment attribuer des rôles Azure AD dans Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 06/03/2021
ms.author: curtand
ms.collection: M365-identity-device-management
ms.custom: subject-rbac-steps
ms.openlocfilehash: a741ce7fff528fbe1f4120f4138a88d7b6e2e915
ms.sourcegitcommit: f3b930eeacdaebe5a5f25471bc10014a36e52e5e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/16/2021
ms.locfileid: "112233004"
---
# <a name="assign-azure-ad-roles-in-privileged-identity-management"></a>Attribuer des rôles dans Azure AD dans Privileged Identity Management

Avec Azure Active Directory (Azure AD), un administrateur général peut effectuer des attributions de rôles d'administrateur Azure AD **permanentes**. Ces attributions de rôles peuvent être créées via le [portail Azure](../roles/permissions-reference.md) ou à l’aide de [commandes PowerShell](/powershell/module/azuread#directory_roles).

Le service Azure AD Privileged Identity Management (PIM) permet également aux Administrateurs de rôle privilégié d’établir des attributions permanentes du rôle Administrateur. En outre, les Administrateurs de rôle privilégié peuvent rendre les utilisateurs **éligibles** pour les rôles Administrateur d’Azure AD. Un administrateur éligible peut activer le rôle lorsqu’il en a besoin, puis l’autorisation expirera lorsqu’il aura terminé.

Privileged Identity Management prend en charge les rôles Azure AD personnalisés et intégrés. Pour plus d’informations sur les rôles personnalisés Azure AD, consultez [ Contrôle d’accès en fonction du rôle dans Azure Active Directory](../roles/custom-overview.md).

## <a name="assign-a-role"></a>Attribuer un rôle

Suivez ces étapes pour rendre un utilisateur éligible pour un rôle d'administrateur Azure AD.

1. Connectez-vous au [Portail Azure](https://portal.azure.com/) à l’aide d’un nom d’utilisateur qui est membre du rôle [Administrateur de rôle privilégié](../roles/permissions-reference.md#privileged-role-administrator).

1. Ouvrez **Azure AD Privileged Identity Management**.

1. Sélectionnez des **rôles Azure AD**.

1. Sélectionnez **Rôles** pour afficher la liste des rôles pour les autorisations Azure AD.

    ![Capture d’écran de la page « Rôles » avec l’action « Ajouter des affectations » sélectionnée.](./media/pim-how-to-add-role-to-user/roles-list.png)

1. Sélectionnez **Ajouter des affectations** pour ouvrir la page **Ajouter des affectations**.

1. Choisissez **Sélectionner un rôle** pour ouvrir la page **Sélectionner un rôle**.

    ![Volet Nouvelle affectation](./media/pim-how-to-add-role-to-user/select-role.png)

1. Sélectionnez un rôle, puis un membre à affecter à ce rôle, puis cliquez sur **Suivant**.

1. Dans la liste **Type d’affectation** du volet **Paramètres d’appartenance**, sélectionnez **Éligible** ou **Actif**.

    - Les attributions **éligibles** exigent des membres qu’ils effectuent une action pour utiliser ce rôle. Il peut s’agir de procéder à une vérification de l’authentification multifacteur (MFA), de fournir une justification professionnelle ou de demander une approbation aux approbateurs désignés.

    - Les attributions **actives** n’exigent pas des membres qu’ils effectuent une action pour utiliser ce rôle. Les membres attribués comme étant actifs détiennent à tout moment les privilèges affectés au rôle.

1. Pour spécifier une durée d’attribution spécifique, ajoutez des zones de date et heure de début et de fin. Lorsque vous avez terminé, sélectionnez **Affecter** pour créer la nouvelle attribution de rôle.

    ![Paramètres des appartenances - date et heure](./media/pim-how-to-add-role-to-user/start-and-end-dates.png)

1. Une fois le rôle attribué, une notification d’état d’attribution s’affiche.

    ![Nouvelle affectation - Notification](./media/pim-how-to-add-role-to-user/assignment-notification.png)

## <a name="assign-a-role-with-restricted-scope"></a>Affecter un rôle avec une étendue restreinte

Pour certains rôles, l’étendue des autorisations accordées peut être limitée à une unité d’administration, un principal de service ou une application unique. Cette procédure est un exemple si vous attribuez un rôle qui a l’étendue d’une unité administrative. Pour obtenir la liste des rôles prenant en charge l’étendue via une unité administrative, voir [Attribuer des rôles dont l’étendue est délimitée à une unité administrative](../roles/admin-units-assign-roles.md). Cette fonctionnalité est actuellement déployée dans les organisations Azure AD.

1. connectez-vous au [Centre d'administration Azure Active Directory](https://aad.portal.azure.com) avec les autorisations Administrateur de rôle privilégié.

1. Sélectionnez **Azure Active Directory** > **Rôles et administrateurs**.

1. Sélectionnez **Administrateur d’utilisateurs**.

    ![La commande Ajouter une attribution est disponible lorsque vous ouvrez un rôle dans le portail](./media/pim-how-to-add-role-to-user/add-assignment.png)

1. Sélectionnez **Ajouter des affectations**.

    ![Quand un rôle prend en charge l’étendue, vous pouvez sélectionner une étendue](./media/pim-how-to-add-role-to-user/add-scope.png)

1. Sur la page **Ajouter des affectations**, vous pouvez :

   - Sélectionner un utilisateur ou un groupe à affecter au rôle
   - Sélectionner l’étendue du rôle (dans ce cas, unités administratives)
   - Sélectionner une unité administrative pour l’étendue

Pour plus d’informations sur la création d’unités administratives, voir [Ajouter et supprimer des unités administratives](../roles/admin-units-manage.md).

## <a name="update-or-remove-an-existing-role-assignment"></a>Mettre à jour ou supprimer une attribution de rôle existante

Suivez ces étapes pour mettre à jour ou supprimer une attribution de rôle existante. **Clients titulaires d’une licence Azure AD P2 uniquement :** n’affectez pas un groupe actif à un rôle à la fois par le biais d’Azure AD et de Privileged Identity Management (PIM). Pour une explication détaillée, consultez [Problèmes connus](../roles/groups-concept.md#known-issues).

1. Ouvrez **Azure AD Privileged Identity Management**.

1. Sélectionnez des **rôles Azure AD**.

1. Sélectionnez **Rôles** pour afficher la liste des rôles pour Azure AD.

1. Sélectionnez le rôle que vous souhaitez mettre à jour ou supprimer.

1. Recherchez l’attribution de rôle sous les onglets **Rôles éligibles** et **Rôles actifs**.

    ![Mettre à jour ou supprimer une attribution de rôle](./media/pim-how-to-add-role-to-user/remove-update-assignments.png)

1. Sélectionnez **Mettre à jour** ou **Supprimer** pour mettre à jour ou supprimer l’attribution de rôle.

## <a name="next-steps"></a>Étapes suivantes

- [Configurer les paramètres des rôles d’administrateur Azure AD dans Privileged Identity Management](pim-how-to-change-default-settings.md)
- [Attribuer des rôles de ressources Azure dans Privileged Identity Management](pim-resource-roles-assign-roles.md)