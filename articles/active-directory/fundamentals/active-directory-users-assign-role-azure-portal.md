---
title: Affectation de rôles d’administrateur à un utilisateur dans Azure Active Directory | Microsoft Docs
description: Comment changer les informations administratives d’un utilisateur dans Azure Active Directory
services: active-directory
documentationcenter: ''
author: eross-msft
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
ms.topic: quickstart
ms.date: 06/25/2018
ms.author: lizross
ms.reviewer: jeffsta
ms.openlocfilehash: ec30f1507bfa45c29709a7f4b7dc1e91aa25ca57
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/03/2018
ms.locfileid: "37440746"
---
# <a name="assign-a-user-to-administrator-roles-in-azure-active-directory"></a>Affecter des rôles d’administrateur à un utilisateur dans Azure Active Directory
Cet article explique comment affecter un rôle d’administration à un utilisateur dans Azure Active Directory (Azure AD). Pour en savoir plus sur l’ajout d’utilisateurs dans votre organisation, consultez [Ajout de nouveaux utilisateurs à Azure Active Directory](../add-users-azure-active-directory.md). Par défaut, les utilisateurs ajoutés ne reçoivent pas d’autorisations d’administrateur, mais vous pouvez leur attribuer des rôles à tout moment.

## <a name="assign-a-role-to-a-user"></a>Affecter un rôle à un utilisateur
1. Connectez-vous au [portail Azure](https://portal.azure.com) avec un compte d’administrateur général ou d’administrateur avec un rôle privilégié pour le répertoire.

2. Sélectionnez **Azure Active Directory**, **Utilisateurs**, puis un utilisateur spécifique dans la liste.

    ![Ouvrir la gestion des utilisateurs](./media/active-directory-users-assign-role-azure-portal/create-users-user-management.png)

3. Pour l’utilisateur sélectionné, sélectionnez **Rôle d’annuaire**, **Ajouter un rôle**, puis les rôles d’administrateur appropriés dans la liste **Rôles d’annuaire** (par exemple **Administrateur d’accès conditionnel**). Pour plus d’informations sur les rôles d’administrateur, consultez [Attribution de rôles d’administrateur dans Azure AD](../users-groups-roles/directory-assign-admin-roles.md). 

    ![Affectation d’un utilisateur à un rôle](./media/active-directory-users-assign-role-azure-portal/create-users-assign-role.png)

1. Appuyez sur **Sélectionner** pour enregistrer.

## <a name="next-steps"></a>Étapes suivantes
* [Guide de démarrage rapide : Ajouter ou supprimer des utilisateurs dans Azure Active Directory](add-users-azure-active-directory.md)
* [Gérer les profils utilisateur](active-directory-users-profile-azure-portal.md)
* [Ajouter des utilisateurs invités à partir d’un autre répertoire](../b2b/what-is-b2b.md) 
* [Affecter un utilisateur à un rôle dans Azure AD](active-directory-users-assign-role-azure-portal.md)
* [Restaurer un utilisateur supprimé](active-directory-users-restore.md)
