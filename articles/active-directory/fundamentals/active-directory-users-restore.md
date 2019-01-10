---
title: Restaurer ou supprimer de façon définitive un utilisateur supprimé - Azure Active Directory | Microsoft Docs
description: Comment afficher les utilisateurs pouvant être restaurés, restaurer un utilisateur supprimé et supprimer de façon définitive un utilisateur avec Azure Active Directory.
services: active-directory
author: eross-msft
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
ms.topic: conceptual
ms.date: 12/17/2018
ms.author: lizross
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.openlocfilehash: 066c9189e0c3cea409a99217e0ab4b46002df1f9
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/18/2018
ms.locfileid: "53556779"
---
# <a name="restore-or-remove-a-recently-deleted-user-using-azure-active-directory"></a>Restaurer ou supprimer un utilisateur supprimé à l’aide d’Azure Active Directory
Lorsque vous supprimez un utilisateur, son compte reste à l’état suspendu pendant 30 jours. Pendant ces 30 jours, le compte de l’utilisateur peut être restauré, avec l’ensemble de ses propriétés. À l’issue de ce délai de 30 jours, l’utilisateur est automatiquement et définitivement supprimé.

Vous pouvez afficher les utilisateurs pouvant être restaurés, restaurer un utilisateur supprimé ou supprimer de manière définitive un utilisateur à l’aide d’Azure Active Directory (Azure AD) dans le portail Azure.

>[!Important]
>Un utilisateur supprimé de manière définitive ne peut pas être restauré, ni par vous, ni par le support technique de Microsoft.

## <a name="required-permissions"></a>Autorisations requises
Vous devez disposer d’un des rôles suivants pour restaurer et supprimer définitivement des utilisateurs.

- Administrateur d’entreprise

- Prise en charge de niveau 1 de partenaire

- Prise en charge de niveau 2 de partenaire

- Administrateur de compte utilisateur

## <a name="view-your-restorable-users"></a>Afficher les utilisateurs pouvant être restaurés
Vous pouvez afficher tous les utilisateurs qui ont été supprimés il y a moins de 30 jours. Ces utilisateurs peuvent être restaurés.

### <a name="to-view-your-restorable-users"></a>Pour afficher les utilisateurs pouvant être restaurés
1. Connectez-vous au [Portail Azure](https://portal.azure.com/) à l’aide d’un compte d’administrateur général pour le répertoire.

2. Sélectionnez **Azure Active Directory**, **Utilisateurs**, puis **Utilisateurs supprimés**.

    Passez en revue la liste des utilisateurs pouvant être restaurés.

    ![Page Utilisateurs - Utilisateurs supprimés, avec les utilisateurs pouvant encore être restaurés](media/active-directory-users-restore/users-deleted-users-view-restorable.png)

## <a name="restore-a-recently-deleted-user"></a>Restaurer un utilisateur supprimé récemment
Lorsque le compte d’un utilisateur est suspendu, toutes les informations de répertoire associées sont conservées. Lorsque vous restaurez un utilisateur, ces informations de répertoire sont également restaurées.

### <a name="to-restore-a-user"></a>Pour restaurer un utilisateur
1. Sur la page **Utilisateurs - Utilisateurs supprimés**, recherchez et sélectionnez l’un des utilisateurs disponibles. Par exemple, _Mary Parker_.

2. Sélectionnez **Restaurer utilisateur**.

    ![Page Utilisateurs - Utilisateurs supprimés, avec l’option Restaurer utilisateur mise en surbrillance](media/active-directory-users-restore/users-deleted-users-restore-user.png)

>[!NOTE]
>Précédemment quand un utilisateur était retiré de l’étendue de synchronisation locale et supprimé dans le cloud, l’état DirSyncEnabled du compte était erronément défini sur « False ». Si, par la suite, cet utilisateur était restauré manuellement à partir de la Corbeille Azure AD, il présentait un état incorrect de compte « Cloud uniquement ». Ce problème est désormais résolu et la valeur de l’état DirSyncEnabled est toujours « True » quand un utilisateur est retiré de l’étendue de synchronisation, puis supprimé de manière réversible et restauré manuellement à partir de la Corbeille Azure AD.

## <a name="permanently-delete-a-user"></a>Supprimer définitivement un utilisateur.
Vous pouvez supprimer définitivement un utilisateur de votre répertoire sans attendre la fin du délai de 30 jours avant la suppression automatique. Un utilisateur supprimé définitivement ne peut pas être restauré, ni par vous, ni par un autre administrateur, ni par le support technique de Microsoft.

>[!Note]
>Si vous supprimez définitivement un utilisateur par erreur, vous devrez créer un nouvel utilisateur et saisir manuellement toutes les informations associées. Pour plus d’informations sur la création d’un utilisateur, consultez [Ajouter ou supprimer des utilisateurs](add-users-azure-active-directory.md).

### <a name="to-permanently-delete-a-user"></a>Pour supprimer définitivement un utilisateur

1. Sur la page **Utilisateurs - Utilisateurs supprimés**, recherchez et sélectionnez l’un des utilisateurs disponibles. Par exemple, _Rae Huff_.

2. Sélectionnez **Supprimer définitivement**.

    ![Page Utilisateurs - Utilisateurs supprimés, avec l’option Restaurer utilisateur mise en surbrillance](media/active-directory-users-restore/users-deleted-users-permanent-delete-user.png)

## <a name="next-steps"></a>Étapes suivantes
Une fois que vous avez restauré ou supprimé vos utilisateurs, vous pouvez exécuter les procédures de base suivantes :

- [Ajouter ou supprimer des utilisateurs](add-users-azure-active-directory.md)

- [Attribuer des rôles aux utilisateurs](active-directory-users-assign-role-azure-portal.md)

- [Ajouter ou modifier les informations de profil](active-directory-users-profile-azure-portal.md)

- [Ajouter des utilisateurs invités à partir d’un autre répertoire](../b2b/what-is-b2b.md) 

Pour en savoir plus sur les autres tâches de gestion des utilisateurs disponibles, consultez la [documentation Gestion des utilisateurs Azure Active Directory](../users-groups-roles/index.yml).
