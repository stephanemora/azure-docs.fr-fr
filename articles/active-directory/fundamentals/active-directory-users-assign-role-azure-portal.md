---
title: Assigner des rôles d’annuaire à des utilisateurs - Azure Active Directory | Microsoft Docs
description: Instructions sur l’attribution de rôles administrateur et non-administrateur aux utilisateurs avec Azure Active Directory.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 09/06/2018
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2df52969ea79e5d1af132aa82c2ec1ceedb92b82
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75422901"
---
# <a name="assign-administrator-and-non-administrator-roles-to-users-with-azure-active-directory"></a>Attribuer des rôles administrateur et non-administrateur aux utilisateurs avec Azure Active Directory
Si un utilisateur de votre organisation a besoin d’une autorisation pour gérer les ressources Azure Active Directory (Azure AD), vous devez assigner un rôle approprié à l’utilisateur dans Azure AD en fonction des actions qu’il souhaite effectuer et pour lesquelles il a besoin d’une permission.

Pour plus d’informations sur les rôles disponibles, consultez [Assigner des rôles d’administrateur dans Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md). Pour plus d’informations sur l’ajout d’utilisateurs, consultez [Ajouter de nouveaux utilisateurs à Azure Active Directory](add-users-azure-active-directory.md).

## <a name="assign-roles"></a>Attribuer des rôles
Une méthode courante pour assigner des rôles Azure AD à un utilisateur se trouve sur la page **Rôle d’annuaire** d’un utilisateur.

Vous pouvez également assigner des rôles à l’aide de Privileged Identity Management (PIM). Pour plus d’informations sur l’utilisation de PIM, consultez [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management).

### <a name="to-assign-a-role-to-a-user"></a>Assigner un rôle à un utilisateur
1. Accédez au [portail Azure](https://portal.azure.com/) et connectez-vous à l’aide d’un compte d’administrateur général pour le répertoire. 

2. Recherchez et sélectionnez **Azure Active Directory**.

      ![Recherche dans le portail Azure d'Azure Active Directory](media/active-directory-users-assign-role-azure-portal/search-azure-active-directory.png)


3. Sélectionnez **Utilisateurs**.

4. Recherchez et sélectionnez l’utilisateur qui obtient l’attribution de rôle. Par exemple, _Alain Charon_.

      ![Page Tous les utilisateurs - Sélectionner l’utilisateur](media/active-directory-users-assign-role-azure-portal/directory-role-select-user.png)

5. Sur la page **Alain Charon - Profil**, sélectionnez **Rôles affectés**.

    La page **Alain Charon - Rôle d’annuaire** s’affiche.

6. Sélectionnez **Ajouter une affectation**. Ensuite, sélectionnez le rôle à assigner à Alain (par exemple, _Administrateur d’application_), puis choisissez **Sélectionner**.

    ![Page Rôles affectés, affichant le rôle sélectionné](media/active-directory-users-assign-role-azure-portal/directory-role-select-role.png)

    Le rôle Administrateur d’application est assigné à Alain Charon et il apparaît sur la page **Alain Charon - Rôle d’annuaire**.

## <a name="remove-a-role-assignment"></a>Supprimer une attribution de rôle
Si vous devez supprimer l’attribution de rôle d’un utilisateur, vous pouvez également le faire à partir de la page **Alain Charon - Rôle d’annuaire**.

### <a name="to-remove-a-role-assignment-from-a-user"></a>Pour supprimer une attribution de rôle d’un utilisateur

1. Sélectionnez **Azure Active Directory**, **Utilisateurs**, puis recherchez et sélectionnez l’utilisateur auquel vous souhaitez supprimer un rôle assigné. Par exemple, _Alain Charon_.

2. Sélectionnez **Rôles affectés**, **Administrateur d’application**, puis sélectionnez **Supprimer l’affectation**.

    ![Page Rôles affectés, affichant le rôle sélectionné et l’option Supprimer](media/active-directory-users-assign-role-azure-portal/directory-role-remove-role.png)

    Le rôle Administrateur d’application d’Alain Charon est supprimé et il n’apparaît plus sur la page **Alain Charon - Rôle d’annuaire**.

## <a name="next-steps"></a>Étapes suivantes
- [Ajouter ou supprimer des utilisateurs](add-users-azure-active-directory.md)

- [Ajouter ou modifier les informations de profil](active-directory-users-profile-azure-portal.md)

- [Ajouter des utilisateurs invités à partir d’un autre répertoire](../b2b/what-is-b2b.md)

Vous pouvez également réaliser d’autres tâches de gestion d’utilisateur, comme l’affectation de délégués, l’utilisation de stratégies et le partage de comptes d’utilisateur. Pour en savoir plus sur les autres actions disponibles, consultez la [documentation Gestion des utilisateurs Azure Active Directory](../users-groups-roles/index.yml).


