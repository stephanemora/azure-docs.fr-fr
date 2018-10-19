---
title: Rôles d’annuaire Azure AD pouvant être gérés dans PIM | Microsoft Docs
description: Fournit la liste des rôles d’annuaire Azure AD que vous pouvez gérer dans Azure AD Privileged Identity Management (PIM).
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
ms.custom: pim ; H1Hack27Feb2017;oldportal;it-pro;
ms.openlocfilehash: 63f4c4c48dd0fed3130dc99929a0e84c3cba2026
ms.sourcegitcommit: 06724c499837ba342c81f4d349ec0ce4f2dfd6d6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/19/2018
ms.locfileid: "46466045"
---
# <a name="azure-ad-directory-roles-you-can-manage-in-pim"></a>Rôles d’annuaire Azure AD pouvant être gérés dans PIM
<!-- **PLACEHOLDER: Need description of how this works. Azure PIM uses roles from MSODS objects.**-->

Vous pouvez affecter aux utilisateurs de votre organisation différents rôles administratifs dans Azure AD. Ces affectations de rôles contrôlent les tâches (ajouter/supprimer des utilisateurs, modifier des paramètres de service, etc.) que les utilisateurs peuvent effectuer sur Azure AD, Office 365 et d’autres services en ligne Microsoft et applications connectées.  

Un administrateur général peut mettre à jour les utilisateurs **définitivement** affectés aux rôles dans Azure AD par le biais du portail, comme décrit à la rubrique [Attribution de rôles d’administrateur dans Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md), ou à l’aide des [commandes PowerShell](/powershell/module/azuread#directory_roles).

Azure AD Privileged Identity Management (PIM) gère les stratégies pour l’accès privilégié des utilisateurs dans Azure AD. PIM affecte aux utilisateurs un ou plusieurs rôles dans Azure AD, et vous pouvez affecter définitivement le rôle à une personne, ou la rendre éligible au rôle. Lorsqu’un utilisateur est définitivement affecté à un rôle ou qu’il active une affectation de rôle éligible, il peut alors gérer Azure Active Directory, Office 365 et d’autres applications avec les autorisations attribuées aux rôles.

Il n’existe aucune différence entre un accès accordé de façon permanente à un utilisateur et l’affectation d’un rôle éligible. La seule différence réside dans le fait que certaines personnes n’ont pas besoin d’un accès permanent. Ces personnes deviennent éligibles au rôle et peuvent l’activer et le désactiver chaque fois que cela est nécessaire.

## <a name="roles-managed-in-pim"></a>Rôles gérés dans PIM
Privileged Identity Management vous permet d’affecter des utilisateurs à des rôles d’administrateur courants, notamment :

* **Administrateur général** (également connu sous le nom d’administrateur d’entreprise) : a accès à toutes les fonctionnalités d’administration. Votre organisation peut compter plusieurs administrateurs généraux. La personne qui s’inscrit pour acheter Office 365 devient automatiquement administrateur général.
* **Administrateur de rôle privilégié** : gère Azure AD PIM et met à jour les attributions de rôles pour d’autres utilisateurs.  
* **Administrateur de facturation** : effectue les achats, gère les abonnements ainsi que les tickets de support et surveille l’intégrité des services.
* **Administrateur de mots de passe** : réinitialise les mots de passe, gère les demandes de service et surveille l’intégrité des services. La tâche des administrateurs de mots de passe se limite à la réinitialisation des mots de passe des utilisateurs.
* **Administrateur de services fédérés** : gère les demandes de service et surveille l’intégrité des services.
  
  > [!NOTE]
  > Si vous utilisez Office 365, avant d’affecter le rôle d’administrateur de services fédérés à un utilisateur, commencez par affecter les autorisations d’administrateur à un service, comme Exchange Online.
  > 
  > 
* **Administrateur d’utilisateurs** : réinitialise les mots de passe, surveille l’intégrité des services et gère les comptes d’utilisateurs, les groupes d’utilisateurs et les demandes de service. L’administrateur d’utilisateurs ne peut pas supprimer un administrateur général, créer d’autres rôles d’administrateur ni réinitialiser des mots de passe pour les administrateurs de facturation, généraux et de services fédérés.
* **Administrateur Exchange** : dispose d’un accès administratif à Exchange Online par le biais du Centre d’administration Exchange (EAC), et peut effectuer pratiquement toutes les tâches dans Exchange Online.
* **Administrateur de services SharePoint** : dispose d’un accès administratif à SharePoint Online par le biais du Centre d’administration SharePoint Online, et peut effectuer pratiquement toutes les tâches dans SharePoint Online. Les utilisateurs éligibles peuvent rencontrer des problèmes de latence en utilisant ce rôle dans SharePoint après son activation dans Privileged Identity Management (PIM).
* **Administrateur Skype Entreprise** : dispose d’un accès administratif à Skype Entreprise par le biais du Centre d’administration Skype Entreprise, et peut effectuer pratiquement toutes les tâches dans Skype Entreprise Online.

Consultez les articles suivants pour plus d’informations sur l’[attribution de rôles d’administrateur dans Azure AD](../users-groups-roles/directory-assign-admin-roles.md) et sur l’[attribution de rôles d’administrateur dans Office 365](https://support.office.com/article/Assigning-admin-roles-in-Office-365-eac4d046-1afd-4f1a-85fc-8219c79e1504).

<!--**PLACEHOLDER: The above article may not be the one we want since PIM gets roles from places other that Office 365**-->


À partir de PIM, vous pouvez [affecter ces rôles à un utilisateur](pim-how-to-add-role-to-user.md) pour qu’il puisse [activer le rôle si nécessaire](pim-how-to-activate-role.md).

Si vous souhaitez autoriser un autre utilisateur à effectuer la gestion dans PIM, les rôles que requiert PIM pour cet utilisateur sont décrits dans [Comment accorder l’accès à PIM](pim-how-to-give-access-to-pim.md).

<!-- ## The PIM Security Administrator Role **PLACEHOLDER: Need description of the Security Administrator role.**-->

## <a name="roles-not-managed-in-pim"></a>Rôles non gérés dans PIM
Les rôles dans Exchange Online ou SharePoint Online, à l’exception de ceux mentionnés ci-dessus, ne sont pas représentés dans Azure AD et par conséquent, ne sont pas visibles dans PIM. Pour plus d’informations sur la modification des affectations de rôles affinées dans ces services Office 365, consultez [Autorisations dans Office 365](https://support.office.com/article/Permissions-in-Office-365-da585eea-f576-4f55-a1e0-87090b6aaa9d).

<!--**The above links might be replaced by ones that are from within this documentation repository **-->


## <a name="user-roles-and-signing-in"></a>Rôles d’utilisateur et connexion
Pour certains services et applications Microsoft, l’affectation d’un utilisateur à un rôle peut ne pas suffire pour permettre à cet utilisateur d’être un administrateur.

L’accès au portail Azure nécessite que l’utilisateur soit propriétaire d’un abonnement Azure, même s’il n’a pas besoin de gérer les abonnements Azure.  Par exemple, pour gérer les paramètres de configuration pour Azure AD, un utilisateur doit être administrateur général dans Azure AD et propriétaire d’un abonnement Azure.  Pour découvrir comment ajouter des utilisateurs aux abonnements Azure, consultez [Gérer les accès à l’aide du contrôle d’accès en fonction du rôle et du portail Azure](../..//role-based-access-control/role-assignments-portal.md).

L’accès à Microsoft Online Services peut nécessiter l’affectation d’une licence à l’utilisateur pour qu’il puisse ouvrir le portail du service ou effectuer des tâches administratives.

## <a name="assign-a-license-to-a-user-in-azure-ad"></a>Attribution d’une licence à un utilisateur dans Azure AD

1. Connectez-vous au [portail Azure](http://portal.azure.com) avec un rôle d’administrateur général ou de propriétaire.

1. Sélectionnez l’annuaire Azure AD que vous souhaitez utiliser et auquel des licences sont associées.

1. Dans le volet de navigation à gauche, cliquez sur **Azure Active Directory**.

1. Cliquez sur **Licences**. La liste des licences disponibles s’affiche.

    ![Licences Azure Active Directory](./media/pim-roles/licenses-overview.png)

1. Cliquez sur votre **produit**.

1. Cliquez sur le plan de licence contenant les licences que vous souhaitez distribuer.

    ![Produits sous licence](./media/pim-roles/licenses-products.png)

1. Cliquez sur **Affecter** pour ouvrir le volet Affecter une licence.

    ![Utilisateurs sous licence](./media/pim-roles/licenses-licensed-users.png)

1. Sélectionnez l’utilisateur ou le groupe auxquels vous souhaitez affecter une licence.

    ![Affecter une licence](./media/pim-roles/licenses-assign-license.png)

1. Cliquez sur **Options d’affectation** pour configurer vos options d’affectation.

    ![Options d’affectation](./media/pim-roles/licenses-assignment-options.png)

1. Cliquez sur **Affecter** pour affecter la licence. L’utilisateur est désormais titulaire de la licence.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Étapes suivantes

- [Commencer à utiliser PIM](pim-getting-started.md)
- [Attribuer des rôles d’annuaire Azure AD dans PIM](pim-how-to-add-role-to-user.md)

