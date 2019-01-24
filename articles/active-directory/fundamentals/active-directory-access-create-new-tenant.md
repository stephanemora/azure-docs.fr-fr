---
title: Démarrage rapide pour accéder à un nouveau locataire et pour en créer un - Azure Active Directory | Microsoft Docs
description: Instructions pour rechercher Azure Active Directory et créer un locataire pour votre organisation.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.component: fundamentals
ms.workload: identity
ms.topic: quickstart
ms.date: 09/10/2018
ms.author: lizross
ms.custom: it-pro, seodec18
ms.openlocfilehash: 5c5116062e4a20168516c8a57c40c5fce6cd9689
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/22/2019
ms.locfileid: "54452358"
---
# <a name="quickstart-create-a-new-tenant-in-azure-active-directory"></a>Démarrage rapide : Créer un locataire dans Azure Active Directory
Vous pouvez effectuer toutes vos tâches d’administration à l’aide du portail Azure Active Directory (Azure AD), notamment la création d’un locataire pour votre organisation. 

Dans ce guide de démarrage rapide, vous découvrez comment accéder au portail Azure et à Azure Active Directory, et comment créer un locataire de base pour votre organisation.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

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

## <a name="clean-up-resources"></a>Supprimer des ressources
Si vous ne pensez pas continuer à utiliser cette application, vous pouvez supprimer le locataire en procédant comme suit :

- Sélectionnez **Azure Active Directory** puis, dans la page **Contoso - Vue d’ensemble**, sélectionnez **Supprimer l’annuaire**.

    Le locataire et ses informations associées sont supprimés.

    ![Page de création d’un annuaire](media/active-directory-access-create-new-tenant/azure-ad-delete-new-tenant.png)

## <a name="next-steps"></a>Étapes suivantes
- Pour modifier ou ajouter des noms de domaine supplémentaires, consultez [Guide pratique pour ajouter un nom de domaine personnalisé à Azure Active Directory](add-custom-domain.md).

- Pour ajouter des utilisateurs, consultez [Ajouter ou supprimer un nouvel utilisateur](add-users-azure-active-directory.md)

- Pour ajouter des groupes et des membres, consultez [Créer un groupe de base et ajouter des membres](active-directory-groups-create-azure-portal.md)

- Découvrez l’[accès en fonction du rôle avec Privileged Identity Management](../../role-based-access-control/pim-azure-resource.md) et l’[accès conditionnel](../../role-based-access-control/conditional-access-azure-management.md) permettant de gérer l’accès aux ressources et aux applications de votre organisation.

- En savoir plus sur Azure AD, notamment sur [les informations de base des licences, la terminologie et les fonctionnalités associées](active-directory-whatis.md).
