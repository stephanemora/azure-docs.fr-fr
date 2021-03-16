---
title: Créer et supprimer des comptes d’utilisateurs consommateurs Azure AD B2C dans le portail Azure
description: Découvrez comment utiliser le portail Azure pour créer et supprimer des utilisateurs consommateurs dans votre répertoire Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/03/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ace0ccb8372ff21a2d3e8721baf09bab539846c2
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102033660"
---
# <a name="use-the-azure-portal-to-create-and-delete-consumer-users-in-azure-ad-b2c"></a>Utiliser le portail Azure pour créer et supprimer des utilisateurs consommateurs dans Azure AD B2C

Dans certains scénarios, vous souhaiterez créer manuellement des comptes consommateurs dans votre répertoire Azure Active Directory B2C (Azure AD B2C). Bien que les comptes consommateurs dans un répertoire Azure AD B2C soient le plus souvent créés lorsque les utilisateurs s'inscrivent pour utiliser une de vos applications, vous pouvez les créer par programmation et en utilisant le portail Azure. Cet article se concentre sur la création et la suppression d'utilisateurs à l’aide du portail Azure.

Pour ajouter ou supprimer des utilisateurs, votre compte doit se voir attribuer le rôle *Administrateur utilisateur* ou *Administrateur global*.

## <a name="types-of-user-accounts"></a>Types de comptes d’utilisateur

Comme décrit dans [Vue d’ensemble des comptes utilisateurs dans Azure AD B2C](user-overview.md), trois types de comptes utilisateurs peuvent être créés dans un répertoire Azure AD B2C :

* Travail
* Invité
* Consommateur

Cet article se concentre l’utilisation de **comptes consommateurs** sur le portail Azure. Pour plus d'informations sur la création et la suppression de comptes professionnels et invités, voir [Ajouter ou supprimer des utilisateurs avec Azure Active Directory](../active-directory/fundamentals/add-users-azure-active-directory.md).

## <a name="create-a-consumer-user"></a>Créer un utilisateur consommateur

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Sélectionnez le filtre **Annuaire et abonnement** dans le menu supérieur, puis l’annuaire qui contient votre locataire Azure AD B2C.
1. Dans le menu de gauche, sélectionnez **Azure AD B2C**. Ou sélectionnez **Tous les services**, puis recherchez et sélectionnez **Azure AD B2C**.
1. Sous **Gérer**, sélectionnez **Utilisateurs**.
1. Sélectionnez **Nouvel utilisateur**.
1. Sélectionnez **Créer un utilisateur Azure AD B2C**.
1. Choisissez une **méthode de connexion** et entrez une adresse **e-mail** ou un **nom d'utilisateur** pour le nouvel utilisateur. La méthode de connexion que vous sélectionnez ici doit correspondre au paramètre que vous avez spécifié pour le fournisseur d'identité du *compte local* de votre locataire Azure AD B2C (voir **Gérer** > **Fournisseur d'identité** dans votre locataire Azure AD B2C).
1. Entrez un **nom** pour l’utilisateur. Il s'agit généralement du nom complet (prénom et nom) de l'utilisateur.
1. (Facultatif) Vous pouvez **bloquer la connexion** si vous souhaitez retarder la possibilité de l'utilisateur à se connecter. Vous pouvez activer la connexion ultérieurement en éditant le **profil** de l’utilisateur dans le portail Azure.
1. Choisissez **Générer automatiquement le mot de passe** ou **Me permettre de créer le mot de passe**.
1. Spécifiez le **prénom** et le **nom** de l’utilisateur.
1. Sélectionnez **Create** (Créer).

À moins d’avoir sélectionné **Bloquer la connexion**, l'utilisateur peut maintenant se connecter en utilisant la méthode de connexion (e-mail ou nom d'utilisateur) que vous avez indiquée.

## <a name="reset-a-users-password"></a>Réinitialiser le mot de passe de l’utilisateur

En tant qu’administrateur, vous pouvez réinitialiser le mot de passe d’un utilisateur qui viendrait à l’oublier. Lorsque vous réinitialisez le mot de passe de l’utilisateur, un mot de passe temporaire est généré automatiquement pour celui-ci. Le mot de passe temporaire n’expire jamais. La prochaine fois que l’utilisateur se connectera, le mot de passe fonctionnera toujours, quel que soit le temps écoulé dans depuis sa création. Ensuite, l’utilisateur doit réinitialiser le mot de passe en définissant un mot de passe permanent. 

> [!IMPORTANT]
> Avant de réinitialiser le mot de passe d’un utilisateur, [configurez un flux pour forcer la réinitialisation du mot de passe dans Azure Active Directory B2C](force-password-reset.md). Autrement, l’utilisateur ne pourra pas se connecter.

Pour réinitialiser le mot de passe d’un utilisateur :

1. Dans annuaire Azure AD B2C, sélectionnez **Utilisateurs**, puis sélectionnez l’utilisateur dont vous voulez réinitialiser le mot de passe.
1. Recherchez et choisissez l’utilisateur qui a besoin de la réinitialisation, puis sélectionnez **Réinitialiser le mot de passe**.

    La page **Alain Charon - profil** s’affiche avec l’option **Réinitialiser le mot de passe**.

    ![Page du profil de l’utilisateur, avec l’option Réinitialiser le mot de passe mis en surbrillance](media/manage-users-portal/user-profile-reset-password-link.png)

1. Sur la page **Réinitialiser le mot de passe**, sélectionnez **Réinitialiser le mot de passe**.
1. Copiez le mot de passe et donnez-le à l’utilisateur. L’utilisateur devra le modifier lors de sa prochaine connexion.


## <a name="delete-a-consumer-user"></a>Supprimer un utilisateur consommateur

1. Dans votre répertoire Azure AD B2C, sélectionnez **Utilisateurs**, puis sélectionnez l'utilisateur que vous souhaitez supprimer.
1. Sélectionnez **Supprimer**, puis **Oui** pour confirmer la suppression.

Pour plus de détails sur la restauration d'un utilisateur dans les 30 premiers jours après la suppression, ou pour supprimer définitivement un utilisateur, voir [Restaurer ou supprimer un utilisateur supprimé à l’aide d’Azure Active Directory](../active-directory/fundamentals/active-directory-users-restore.md).

## <a name="next-steps"></a>Étapes suivantes

Pour consulter des scénarios de gestion automatisée des utilisateurs, par exemple la migration d’utilisateurs d’un autre fournisseur d’identité vers votre annuaire Azure AD B2C, voir [Azure AD B2C : Migration d’utilisateur](user-migration.md).
