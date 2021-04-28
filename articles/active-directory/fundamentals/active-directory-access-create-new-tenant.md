---
title: 'Démarrage rapide : Créer un locataire et y accéder - Azure AD'
description: Instructions pour rechercher Azure Active Directory et créer un locataire pour votre organisation.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: quickstart
ms.date: 09/10/2018
ms.author: ajburnle
ms.custom: it-pro, seodec18, fasttrack-edit
ms.collection: M365-identity-device-management
ms.openlocfilehash: 93ae1982dfaef6e10edf39013474b6663dd688d0
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108130608"
---
# <a name="quickstart-create-a-new-tenant-in-azure-active-directory"></a>Démarrage rapide : Créer un locataire dans Azure Active Directory

Vous pouvez effectuer toutes vos tâches d’administration à l’aide du portail Azure Active Directory (Azure AD), notamment la création d’un locataire pour votre organisation. 

Dans ce guide de démarrage rapide, vous découvrez comment accéder au portail Azure et à Azure Active Directory, et comment créer un locataire de base pour votre organisation.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="create-a-new-tenant-for-your-organization"></a>Créer un locataire pour votre organisation

Une fois connecté au portail Azure, vous pouvez créer un locataire pour votre organisation. Votre nouveau locataire représente votre organisation et vous permet de gérer une instance particulière des services cloud de Microsoft pour vos utilisateurs internes et externes.

### <a name="to-create-a-new-tenant"></a>Pour créer un locataire

1. Connectez-vous au [portail Azure](https://portal.azure.com/) de votre organisation.

1. Dans le menu du portail Azure, sélectionnez **Azure Active Directory**.  

    <kbd>![Azure Active Directory - Page Vue d’ensemble - Créer un locataire](media/active-directory-access-create-new-tenant/azure-ad-portal.png)</kbd>  

1. Sélectionnez **Créer un locataire**.

1. Sous l’onglet Informations de base, sélectionnez le type de locataire que vous souhaitez créer : **Azure Active Directory** ou **Azure Active Directory (B2C)** .

1. Sélectionnez **Suivant : Configuration** pour passer à l’onglet Configuration.

    <kbd>![Azure Active Directory - Page Créer un locataire - Onglet Configuration ](media/active-directory-access-create-new-tenant/azure-ad-create-new-tenant.png)</kbd>

1.  Sous l’onglet Configuration, entrez les informations suivantes :
    
    - Tapez _Contoso Organization_ dans la zone **Nom de l’organisation**.

    - Tapez _Contosoorg_ dans la zone **Nom de domaine initial**.

    - Laissez l’option _États-Unis_ dans la zone **Pays ou région**.

1. Sélectionnez **Suivant : Vérifier + créer**. Vérifiez les informations que vous avez entrées et, si elles sont correctes, sélectionnez **Créer**.

    <kbd>![Azure Active Directory - Page Vérifier et créer un locataire](media/active-directory-access-create-new-tenant/azure-ad-review.png)</kbd>

Votre nouveau locataire est créé avec le domaine contoso.onmicrosoft.com.

## <a name="your-user-account-in-the-new-tenant"></a>Votre compte d’utilisateur dans le nouveau locataire

Quand vous créez un locataire Azure AD, vous devenez le premier utilisateur de ce locataire. En tant que premier utilisateur, vous recevez automatiquement le rôle [Administrateur général](../roles/permissions-reference.md#global-administrator). Consultez votre compte d’utilisateur en accédant à la page [**Utilisateurs**](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/MsGraphUsers).

Par défaut, vous figurez aussi comme [contact technique](/microsoft-365/admin/manage/change-address-contact-and-more?view=o365-worldwide#what-do-these-fields-mean) pour le locataire. Vous pouvez modifier les informations de contact technique dans [**Propriétés**](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Properties).

> [!WARNING]
> Vérifiez que votre annuaire a au moins deux comptes disposant des privilèges d’administrateur général. Ceci vous aidera dans le cas où un administrateur général est verrouillé. Pour plus d’informations, consultez l’article [Gérer les comptes d’accès d’urgence dans Azure AD](../roles/security-emergency-access.md).

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous ne prévoyez pas de continuer à utiliser cette application, vous pouvez supprimer le locataire en effectuant les étapes suivantes :

- Vérifiez que vous êtes connecté à l’annuaire que vous voulez supprimer via le filtre **Répertoire + abonnement** dans le portail Azure. Si nécessaire, passez à l’annuaire cible.
- Sélectionnez **Azure Active Directory** puis, dans la page **Contoso - Vue d’ensemble**, sélectionnez **Supprimer l’annuaire**.

    Le locataire et ses informations associées sont supprimés.

    <kbd>![Page Vue d’ensemble, avec le bouton Supprimer le répertoire mis en surbrillance](media/active-directory-access-create-new-tenant/azure-ad-delete-new-tenant.png)</kbd>

## <a name="next-steps"></a>Étapes suivantes

- Pour modifier ou ajouter des noms de domaine supplémentaires, consultez [Guide pratique pour ajouter un nom de domaine personnalisé à Azure Active Directory](add-custom-domain.md).

- Pour ajouter des utilisateurs, consultez [Ajouter ou supprimer un nouvel utilisateur](add-users-azure-active-directory.md)

- Pour ajouter des groupes et des membres, consultez [Créer un groupe de base et ajouter des membres](active-directory-groups-create-azure-portal.md)

- Découvrez l’[accès en fonction du rôle avec Privileged Identity Management](../../role-based-access-control/best-practices.md) et l’[accès conditionnel](../../role-based-access-control/conditional-access-azure-management.md) permettant de gérer l’accès aux ressources et aux applications de votre organisation.

- En savoir plus sur Azure AD, notamment sur [les informations de base des licences, la terminologie et les fonctionnalités associées](active-directory-whatis.md).