---
title: Comment ajouter ou supprimer des utilisateurs dans Azure Active Directory | Microsoft Docs
description: Découvrez comment ajouter de nouveaux utilisateurs ou supprimer des utilisateurs existants dans Azure Active Directory.
services: active-directory
author: eross-msft
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
ms.topic: conceptual
ms.date: 09/04/2018
ms.author: lizross
ms.reviewer: jeffsta
ms.custom: it-pro
ms.openlocfilehash: 782363144a6b1dd87aff515c38588b6ce70b61bd
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/19/2018
ms.locfileid: "46295102"
---
# <a name="how-to-add-or-delete-users-using-azure-active-directory"></a>Procédure : Ajouter ou supprimer des utilisateurs à l’aide d’Azure Active Directory
Ajouter de nouveaux utilisateurs ou supprimer des utilisateurs existants dans votre client Azure Active Directory (Azure AD), à l’aide d’Azure AD.

## <a name="add-a-new-user"></a>Ajouter un nouvel utilisateur
Vous pouvez créer un nouvel utilisateur à l’aide d’Azure Active Directory.

### <a name="to-add-a-new-user"></a>Ajouter un nouvel utilisateur
1. Connectez-vous au [portail Azure](https://portal.azure.com/) en tant qu’administrateur général ou administrateur d’utilisateurs du répertoire.

2. Sélectionnez **Azure Active Directory**, **Utilisateurs**, puis **Nouvel utilisateur**.

    ![Page Utilisateurs - Tous avec l’option Nouvel utilisateur mis en surbrillance](media/add-users-azure-active-directory/new-user-all-users-blade.png)

3. Dans la page **Utilisateur**, fournissez les informations requises.

    ![Ajouter un nouvel utilisateur, page Utilisateur avec les informations sur l’utilisateur](media/add-users-azure-active-directory/new-user-user-blade.png)

    - **Nom (obligatoire).** Prénom et nom du nouvel utilisateur. Par exemple, Mary Parker.

    - **Nom d’utilisateur (obligatoire).** Nom d’utilisateur du nouvel utilisateur. Par exemple : mary@contoso.com. 
    
        Le domaine dans le nom d’utilisateur doit correspondre au nom de domaine par défaut initial, <_votrenomdedomaine_>.onmicrosoft.com, ou à un nom de domaine personnalisé, comme contoso.com. Pour plus d’informations sur la création d’un nom de domaine personnalisé, consultez [Ajouter un nom de domaine personnalisé dans Azure Active Directory](add-custom-domain.md).

    - **Profil.** Si vous le souhaitez, ajoutez des informations supplémentaires sur l’utilisateur. Vous pouvez également ajouter ces informations ultérieurement. Pour plus d’informations sur l’ajout d’informations sur l’utilisateur, consultez [Ajouter ou modifier des informations de profil utilisateur](active-directory-users-profile-azure-portal.md).

    - **Groupes.** Si vous le souhaitez, vous pouvez ajouter l’utilisateur à un ou plusieurs groupes existants. Vous pouvez également ajouter l’utilisateur à des groupes ultérieurement. Pour plus d’informations sur l’ajout d’utilisateurs à des groupes, consultez [Créer un groupe de base et ajouter des membres](active-directory-groups-create-azure-portal.md).

    - **Rôle de répertoire.** Si vous le souhaitez, vous pouvez ajouter l’utilisateur à un rôle d’annuaire. Vous pouvez affecter l’utilisateur au rôle d’administrateur global, ou à un ou plusieurs des autres rôles d’administrateur dans Azure AD. Pour plus d’informations sur l’affectation de rôles, consultez [Attribuer des rôles aux utilisateurs](active-directory-users-assign-role-azure-portal.md).

4. Copiez le mot de passe généré automatiquement fourni dans le champ **Mot de passe**. Vous devrez donner ce mot de passe à l’utilisateur pour qu’il se connecte la première fois.

5. Sélectionnez **Créer**.

    L’utilisateur est créé et ajouté à votre locataire Azure AD.

## <a name="add-a-new-user-within-a-hybrid-environment"></a>Ajouter un nouvel utilisateur au sein d’un environnement hybride
Si vous avez un environnement avec Azure Active Directory (cloud) et Windows Server Active Directory (local), vous pouvez ajouter de nouveaux utilisateurs en synchronisant les données du compte d’utilisateur existant. Pour obtenir plus d’informations sur les utilisateurs et environnements hybrides, consultez [Intégrer vos répertoires locaux avec Azure Active Directory](../hybrid/whatis-hybrid-identity.md).

## <a name="delete-a-user"></a>Supprimer un utilisateur
Vous pouvez supprimer un utilisateur existant à l’aide d’Azure Active Directory.

### <a name="to-delete-a-user"></a>Pour supprimer un utilisateur
1. Connectez-vous au [Portail Azure](https://portal.azure.com/) à l’aide d’un compte d’administrateur général pour le répertoire.

2. Sélectionnez **Azure Active Directory**, **Utilisateurs**, puis recherchez et sélectionnez l’utilisateur que vous souhaitez supprimer de votre client Azure AD. Par exemple, _Mary Parker_.

3. Sélectionnez **Supprimer l’utilisateur**.

    ![Page Utilisateurs - Tous avec l’option Supprimer l’utilisateur mis en surbrillance](media/add-users-azure-active-directory/delete-user-all-users-blade.png)

    L’utilisateur est supprimé et n’apparaît plus sur la page **Utilisateurs : Tous les utilisateurs**. L’utilisateur est affiché sur la page **Utilisateurs supprimés** pendant 30 jours et peut être restauré durant cette période. Pour plus d’informations sur la restauration d’un utilisateur, consultez [Restaurer ou supprimer de façon définitive un utilisateur supprimé](active-directory-users-restore.md).

    >[!Note]
    >Vous devez utiliser Windows Server Active Directory pour mettre à jour l’identité, les informations de contact ou sur l’emploi des utilisateurs dont la source d’autorité est Windows Server Active Directory. Une fois la mise à jour terminée, vous devez attendre la fin du prochain cycle de synchronisation pour constater les modifications.

## <a name="next-steps"></a>Étapes suivantes
Une fois que vous avez ajouté vos utilisateurs, vous pouvez exécuter les procédures de base suivantes :

- [Ajouter ou modifier les informations de profil](active-directory-users-profile-azure-portal.md)

- [Attribuer des rôles aux utilisateurs](active-directory-users-assign-role-azure-portal.md)

- [Créer un groupe de base et ajouter des membres](active-directory-groups-create-azure-portal.md)

- [Travailler avec des utilisateurs et groupes dynamiques](../users-groups-roles/groups-create-rule.md)

Ou vous pouvez effectuer d’autres tâches de gestion des utilisateurs, comme [ajouter des utilisateurs invités depuis un autre répertoire](../b2b/what-is-b2b.md) ou [restaurer un utilisateur supprimé](active-directory-users-restore.md). Pour en savoir plus sur les autres actions disponibles, consultez la [documentation Gestion des utilisateurs Azure Active Directory](../users-groups-roles/index.yml).