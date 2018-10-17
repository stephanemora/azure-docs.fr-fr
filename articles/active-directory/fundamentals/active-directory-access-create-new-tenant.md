---
title: Démarrage rapide pour accéder à Azure Active Directory et créer un locataire | Microsoft Docs
description: Guide de démarrage rapide décrivant les étapes pour rechercher Azure Active Directory et créer un locataire pour votre organisation.
services: active-directory
author: eross-msft
manager: mtillman
ms.service: active-directory
ms.component: fundamentals
ms.workload: identity
ms.topic: quickstart
ms.date: 09/10/2018
ms.author: lizross
custom: it-pro
ms.openlocfilehash: 8ef68c8afcf61a1a11c341a679443071aece9812
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/19/2018
ms.locfileid: "46363001"
---
# <a name="quickstart-access-azure-active-directory-to-create-a-new-tenant"></a>Démarrage rapide : Accéder à Azure Active Directory pour créer un locataire
Vous pouvez effectuer toutes vos tâches d’administration à l’aide du portail Azure Active Directory (Azure AD), notamment la création d’un locataire pour votre organisation. 

Dans ce guide de démarrage rapide, vous découvrez comment accéder au portail Azure et à Azure Active Directory, et comment créer un locataire de base pour votre organisation.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Prérequis
Avant de commencer, vous devez :

- Vous assurer que votre organisation dispose d’une licence Azure AD valide

- Vérifier que vous êtes administrateur général

## <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.
Connectez-vous au [portail Azure](https://portal.azure.com/) de votre organisation avec un compte d’administrateur général.

![Écran du Portail Azure](media/active-directory-access-create-new-tenant/azure-ad-portal.png)

## <a name="create-a-new-tenant-for-your-organization"></a>Créer un locataire pour votre organisation
Une fois connecté au portail Azure, vous pouvez créer un locataire pour votre organisation. Votre nouveau locataire représente votre organisation et vous permet de gérer une instance particulière des services cloud de Microsoft pour vos utilisateurs internes et externes.

### <a name="to-create-a-new-tenant"></a>Pour créer un locataire
1. Sélectionnez **Azure Active Directory**, puis **Créer des ressources**, **Identité** et **Azure Active Directory**.

    La page **Créer un annuaire** s’affiche.

    ![Page de création d’un annuaire Azure Active Directory](media/active-directory-access-create-new-tenant/azure-ad-create-new-tenant.png)

2.  Dans la page **Créer un annuaire**, indiquez les informations suivantes :
    
    - Tapez _Contoso_ dans la zone **Nom de l’organisation**.

    - Tapez _Contoso_ dans la zone **Nom de domaine initial**.

    - Laissez l’option _États-Unis_ dans la zone **Pays ou région**.

3. Sélectionnez **Créer**.

Votre nouveau locataire est créé avec le domaine contoso.onmicrosoft.com.

## <a name="clean-up-resources"></a>Supprimer les ressources
Si vous ne pensez pas continuer à utiliser cette application, vous pouvez supprimer le locataire en procédant comme suit :

- Sélectionnez **Azure Active Directory** puis, dans la page **Contoso - Vue d’ensemble**, sélectionnez **Supprimer l’annuaire**.

    Le locataire et ses informations associées sont supprimés.

    ![Page de création d’un annuaire](media/active-directory-access-create-new-tenant/azure-ad-delete-new-tenant.png)

## <a name="next-steps"></a>Étapes suivantes
- Pour modifier ou ajouter des noms de domaine supplémentaires, consultez [Guide pratique pour ajouter un nom de domaine personnalisé à Azure Active Directory](add-custom-domain.md).

- Pour ajouter des utilisateurs, consultez [Ajouter ou supprimer un nouvel utilisateur](add-users-azure-active-directory.md)

- Pour ajouter des groupes et des membres, consultez [Créer un groupe de base et ajouter des membres](active-directory-groups-create-azure-portal.md)

- Découvrez l’[accès en fonction du rôle avec Privileged Identity Management](../../role-based-access-control/pim-azure-resource.md) et l’[accès conditionnel](../../role-based-access-control/conditional-access-azure-management.md) permettant de gérer l’accès aux ressources et aux applications de votre organisation.
