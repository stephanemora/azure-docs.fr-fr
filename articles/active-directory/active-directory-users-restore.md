---
title: Restaurer ou supprimer de façon définitive un utilisateur supprimé dans Azure Active Directory | Microsoft Docs
description: Comment restaurer un utilisateur supprimé, afficher les utilisateurs pouvant être restaurés et supprimer de façon définitve un utilisateur dans Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 03/28/2018
ms.author: curtand
ms.reviewer: jeffsta
ms.custom: it-pro
ms.openlocfilehash: 9eea61cc1b735c6505df87526cd526a9a4a6eed8
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2018
---
# <a name="restore-a-deleted-user-in-azure-active-directory"></a>Restaurer un utilisateur supprimé dans Azure Active Directory

Cet article contient des instructions pour restaurer ou supprimer définitivement un utilisateur supprimé précédemment. Lorsque vous supprimez un utilisateur dans Azure Active Directory (Azure AD), celui-ci est conservé pendant 30 jours à partir de la date de suppression. Durant ce laps de temps, l’utilisateur et ses propriétés peuvent être restaurés. 

> [!wARNING]
> Après sa suppression définitive, il ne sera plus possible de le restaurer.


## <a name="how-to-restore-a-recently-deleted-user"></a>Comment restaurer un utilisateur supprimé récemment
Lorsqu’un utilisateur est supprimé récemment, toutes les informations d’annuaire sont conservées. Si l’utilisateur est restauré, ces informations sont également restaurées.

1. Dans le [centre d’administration Azure AD](https://aad.portal.azure.com), sélectionnez **Utilisateurs et groupes** &gt; **Tous les utilisateurs**. 
2. Sous **Afficher**, filtrez la page pour afficher les **Utilisateurs supprimés récemment**. 
3. Sélectionnez un ou plusieurs utilisateurs récemment supprimés.
4. Sélectionnez **Restaurer utilisateur**.

## <a name="how-to-permanently-delete-a-recently-deleted-user"></a>Comment supprimer définitivement un utilisateur récemment supprimé

1. Dans le [centre d’administration Azure AD](https://aad.portal.azure.com), sélectionnez **Utilisateurs et groupes** &gt; **Tous les utilisateurs**. 
2. Sous **Afficher**, filtrez la page pour afficher les **Utilisateurs supprimés récemment**. 
3. Sélectionnez un ou plusieurs utilisateurs récemment supprimés.
4. Sélectionnez **Supprimer définitivement**.

## <a name="required-permissions"></a>Autorisations requises
Les autorisations suivantes sont suffisantes pour restaurer un utilisateur.

Rôle  | Autorisations 
--------- | ---------
Administrateur d’entreprise<p>Prise en charge de niveau 1 de partenaire<p>Prise en charge de niveau 2 de partenaire<p>Administrateur de compte utilisateur | Peut restaurer les utilisateurs supprimés 
Administrateur d’entreprise<p>Prise en charge de niveau 1 de partenaire<p>Prise en charge de niveau 2 de partenaire<p>Administrateur de compte utilisateur | Peut supprimer définitivement les utilisateurs

## <a name="next-steps"></a>Étapes suivantes
Ces articles fournissent des informations supplémentaires sur la gestion des utilisateurs Azure Active Directory.

* [Démarrage rapide : Ajouter ou supprimer des utilisateurs dans Azure Active Directory](add-users-azure-active-directory.md)
* [Gérer les profils utilisateur](active-directory-users-profile-azure-portal.md)
* [Ajouter des utilisateurs invités à partir d’un autre répertoire](active-directory-b2b-what-is-azure-ad-b2b.md) 
* [Affecter un utilisateur à un rôle dans Azure AD](active-directory-users-assign-role-azure-portal.md)
