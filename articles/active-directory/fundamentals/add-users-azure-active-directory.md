---
title: Ajouter ou supprimer des utilisateurs - Azure Active Directory | Microsoft Docs
description: Instructions pour ajouter de nouveaux utilisateurs ou supprimer des utilisateurs existants à l’aide d’Azure Active Directory.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: lizross
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: c1bac4d2c0f236b8fca611c7391846abdb782796
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59009595"
---
# <a name="add-or-delete-users-using-azure-active-directory"></a>Ajouter ou supprimer des utilisateurs à l’aide d’Azure Active Directory
Ajouter de nouveaux utilisateurs ou supprimer des utilisateurs existants à partir de votre organisation Azure Active Directory (Azure AD).

## <a name="add-a-new-user"></a>Ajouter un nouvel utilisateur
Vous pouvez créer un utilisateur à l’aide du portail Azure Active Directory.

### <a name="to-add-a-new-user"></a>Ajouter un nouvel utilisateur
1. Se connecter à la [portail](https://portal.azure.com/) en tant qu’utilisateur administrateur pour l’organisation.

2. Sélectionnez **Azure Active Directory**, **Utilisateurs**, puis **Nouvel utilisateur**.

    ![Page Utilisateurs - Tous avec l’option Nouvel utilisateur mis en surbrillance](media/add-users-azure-active-directory/new-user-all-users-blade.png)

3. Dans la page **Utilisateur**, fournissez les informations requises.

    ![Ajouter un nouvel utilisateur, page Utilisateur avec les informations sur l’utilisateur](media/add-users-azure-active-directory/new-user-user-blade.png)

   - **Nom (obligatoire).** Prénom et nom du nouvel utilisateur. Par exemple, Mary Parker.

   - **Nom d’utilisateur (obligatoire).** Nom d’utilisateur du nouvel utilisateur. Par exemple : mary@contoso.com.
    
       Le domaine dans le nom d’utilisateur doit correspondre au nom de domaine par défaut initial, <_votrenomdedomaine_>.onmicrosoft.com, ou à un nom de domaine personnalisé, comme contoso.com. Pour plus d’informations sur la création d’un nom de domaine personnalisé, consultez [Ajouter un nom de domaine personnalisé dans Azure Active Directory](add-custom-domain.md).

   - **Profil.** Si vous le souhaitez, ajoutez des informations supplémentaires sur l’utilisateur. Vous pouvez également ajouter ces informations ultérieurement. Pour plus d’informations sur l’ajout d’informations sur l’utilisateur, consultez [Ajouter ou modifier des informations de profil utilisateur](active-directory-users-profile-azure-portal.md).

   - **Groupes.** Si vous le souhaitez, vous pouvez ajouter l’utilisateur à un ou plusieurs groupes existants. Vous pouvez également ajouter l’utilisateur à des groupes ultérieurement. Pour plus d’informations sur l’ajout d’utilisateurs à des groupes, consultez [Créer un groupe de base et ajouter des membres](active-directory-groups-create-azure-portal.md).

   - **Rôle de répertoire.** Si vous le souhaitez, vous pouvez ajouter l’utilisateur à un rôle d’administrateur Azure AD. Vous pouvez affecter l’utilisateur soit un administrateur général ou un ou plusieurs des rôles administrateur limité dans Azure AD. Pour plus d’informations sur l’affectation de rôles, consultez [Attribuer des rôles aux utilisateurs](active-directory-users-assign-role-azure-portal.md).

4. Copiez le mot de passe généré automatiquement fourni dans le champ **Mot de passe**. Vous devrez donner ce mot de passe à l’utilisateur pour qu’il se connecte la première fois.

5. Sélectionnez **Créer**.

    L’utilisateur est créé et ajouté à votre locataire Azure AD.

## <a name="add-a-new-user-within-a-hybrid-environment"></a>Ajouter un nouvel utilisateur au sein d’un environnement hybride
Si vous avez un environnement avec Azure Active Directory (cloud) et Windows Server Active Directory (local), vous pouvez ajouter de nouveaux utilisateurs en synchronisant les données du compte d’utilisateur existant. Pour obtenir plus d’informations sur les utilisateurs et environnements hybrides, consultez [Intégrer vos répertoires locaux avec Azure Active Directory](../hybrid/whatis-hybrid-identity.md).

## <a name="delete-a-user"></a>Supprimer un utilisateur
Vous pouvez supprimer un utilisateur existant à l’aide du portail Azure Active Directory.

### <a name="to-delete-a-user"></a>Pour supprimer un utilisateur
1. Se connecter à la [Azure portal](https://portal.azure.com/) à l’aide d’un compte d’utilisateur administrateur pour l’organisation.

2. Sélectionnez **Azure Active Directory**, **Utilisateurs**, puis recherchez et sélectionnez l’utilisateur que vous souhaitez supprimer de votre client Azure AD. Par exemple, _Mary Parker_.

3. Sélectionnez **Supprimer l’utilisateur**.

    ![Page Utilisateurs - Tous avec l’option Supprimer l’utilisateur mis en surbrillance](media/add-users-azure-active-directory/delete-user-all-users-blade.png)

    L’utilisateur est supprimé et n’apparaît plus sur la page **Utilisateurs : Tous les utilisateurs**. L’utilisateur est affiché sur la page **Utilisateurs supprimés** pendant 30 jours et peut être restauré durant cette période. Pour plus d’informations sur la restauration d’un utilisateur, consultez [Restaurer ou supprimer de façon définitive un utilisateur supprimé](active-directory-users-restore.md). Lorsqu’un utilisateur est supprimé, les licences consommées par l’utilisateur sont rendus disponibles pour d’autres utilisateurs à être consommés.

    >[!Note]
    >Vous devez utiliser Windows Server Active Directory pour mettre à jour l’identité, les informations de contact ou sur l’emploi des utilisateurs dont la source d’autorité est Windows Server Active Directory. Une fois la mise à jour terminée, vous devez attendre la fin du prochain cycle de synchronisation pour constater les modifications.

## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez ajouté vos utilisateurs, vous pouvez exécuter les procédures de base suivantes :

- [Ajouter ou modifier les informations de profil](active-directory-users-profile-azure-portal.md)

- [Attribuer des rôles aux utilisateurs](active-directory-users-assign-role-azure-portal.md)

- [Créer un groupe de base et ajouter des membres](active-directory-groups-create-azure-portal.md)

- [Travailler avec des utilisateurs et groupes dynamiques](../users-groups-roles/groups-create-rule.md)

Ou vous pouvez effectuer d’autres tâches de gestion des utilisateurs, comme [ajouter des utilisateurs invités depuis un autre répertoire](../b2b/what-is-b2b.md) ou [restaurer un utilisateur supprimé](active-directory-users-restore.md). Pour en savoir plus sur les autres actions disponibles, consultez la [documentation Gestion des utilisateurs Azure Active Directory](../users-groups-roles/index.yml).
