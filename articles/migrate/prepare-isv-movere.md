---
title: Préparer Azure Migrate à utiliser un outil ISV/Movere
description: Cet article explique comment préparer Azure Migrate à utiliser un outil ISV ou Movere, puis comment commencer à utiliser l’outil.
ms.topic: how-to
ms.date: 05/07/2020
ms.openlocfilehash: 9513e783d4f9d7be83f1434d4dd9011844af8993
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/20/2020
ms.locfileid: "83682651"
---
# <a name="prepare-to-work-with-an-isv-tool-or-movere"></a>Se préparer à utiliser un outil ISV ou Movere

Si vous avez ajouté un [outil ISV](migrate-services-overview.md#isv-integration) ou Movere à un projet Azure Migrate, vous devez suivre quelques étapes avant de lier l’outil et d’envoyer des données à Azure Migrate. 

## <a name="check-azure-ad-permissions"></a>Vérifier les autorisations dans Azure AD

Votre compte d’utilisateur Azure a besoin des autorisations suivantes :

- Autorisation d’inscrire une application Azure Active Directory (Azure AD) auprès de votre locataire Azure
- Autorisation d’allouer un rôle à l’application Azure AD au niveau de l’abonnement


### <a name="set-permissions-to-register-an-azure-ad-app"></a>Définir des autorisations pour inscrire une application Azure AD

1. Dans Azure AD, vérifiez le rôle de votre compte.
2. Si vous avez le rôle d’utilisateur, sélectionnez **Paramètres utilisateur** à gauche et vérifiez si les utilisateurs peuvent inscrire des applications. Si la valeur est **Oui**, tous les utilisateurs dans le locataire Azure AD peuvent inscrire une application. Si la valeur est **Non**, seuls les utilisateurs administrateurs peuvent inscrire des applications.   
3. Si vous n’avez pas d’autorisations, un utilisateur administrateur peut fournir à votre compte d’utilisateur le rôle [d'administrateur de l’application](../active-directory/users-groups-roles/directory-assign-admin-roles.md#application-administrator), afin que vous puissiez inscrire l’application.
4. Une fois l’outil lié à Azure Migrate, l’administrateur peut supprimer le rôle de votre compte.

### <a name="set-permissions-to-assign-a-role-to-an-azure-ad-app"></a>Définir des autorisations pour affecter un rôle à une application Azure AD
 
Dans votre abonnement Azure, votre compte doit disposer de l’accès **Microsoft.Authorization/*/Write** pour attribuer un rôle à une application Azure AD. 

1. Sur le portail Azure, ouvrez **Abonnements**.
2. Sélectionnez l’abonnement approprié. Si vous ne le voyez pas, sélectionnez le **filtre d’abonnements généraux**. 
3. Sélectionner **Mes autorisations**. Ensuite, sélectionnez **Cliquer ici pour afficher les détails d’accès complet pour cet abonnement**.
4. Vérifiez les autorisations dans **Affectations de rôles** > **Affichage**. Si votre compte n’a pas d’autorisations, demandez à l’administrateur de l’abonnement de vous ajouter au rôle [Administrateur de l'accès utilisateur](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#user-access-administrator) ou au rôle [Propriétaire](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner).
 

## <a name="start-using-the-tool"></a>Commencer à utiliser le portail

1. Si vous n’avez pas encore de licence ou d’essai gratuit pour l’outil, accédez à l’accueil de l’outil dans Azure Migrate, sous **Inscrire**, sélectionnez **En savoir plus**.
2. Dans l’outil, suivez les instructions pour lier l’outil au projet Azure Migrate et pour envoyer des données à Azure Migrate.

## <a name="next-steps"></a>Étapes suivantes

Suivez les instructions de votre outil ISV ou Movere pour envoyer des données à Azure Migrate.

   
