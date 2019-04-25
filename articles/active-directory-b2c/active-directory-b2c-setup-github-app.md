---
title: Configurer l’inscription et la connexion avec un compte GitHub - Azure Active Directory B2C | Microsoft Docs
description: Proposez l’inscription et la connexion aux clients disposant de comptes GitHub dans vos applications à l’aide d’Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: f1c1ac91c08fe27445f4b9631500543d1d0287bd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60316423"
---
# <a name="set-up-sign-up-and-sign-in-with-a-github-account-using-azure-active-directory-b2c"></a>Configurer l’inscription et la connexion avec un compte GitHub à l’aide d’Azure Active Directory B2C

> [!NOTE]
> Cette fonctionnalité est en préversion.
> 

Pour utiliser un compte GitHub en tant que [fournisseur d’identité](active-directory-b2c-reference-oauth-code.md) dans Azure Active Directory (Azure AD) B2C, vous devez créer une application dans votre locataire qui la représente. Si vous ne disposez pas encore de compte GitHub, vous pouvez en créer un à l’adresse [https://www.github.com/](https://www.github.com/).

## <a name="create-a-github-oauth-application"></a>Créer une application GitHub OAuth

1. Connectez-vous au site web [Développeur GitHub](https://github.com/settings/developers) avec vos informations d’identification GitHub.
2. Sélectionnez **Applications OAuth**, puis **Nouvelle application OAuth**.
3. Entrez le **Nom de l’application** et l’**URL de la page d’accueil**.
4. Entrez `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` dans l’**URL de rappel d’autorisation**. Remplacez `your-tenant-name` par le nom de votre locataire Azure AD B2C. Utilisez uniquement des minuscules quand vous entrez le nom de votre locataire, même si le locataire est défini à l’aide de majuscules dans Azure AD B2C.
5. Cliquez sur **Inscrire l’application**.
6. Copiez les valeurs de **ID client** et **Clé secrète client**. Vous avez besoin des deux pour ajouter le fournisseur d’identité à votre locataire.

## <a name="configure-a-github-account-as-an-identity-provider"></a>Configuration d’un compte GitHub en tant que fournisseur d’identité

1. Connectez-vous au [portail Azure](https://portal.azure.com/) en tant qu’administrateur général de votre locataire Azure AD B2C.
2. Veillez à utiliser l’annuaire qui contient votre locataire Azure AD B2C en cliquant sur le **filtre Répertoire et abonnement** dans le menu du haut et en choisissant l’annuaire qui contient votre locataire.
3. Choisissez **Tous les services** dans le coin supérieur gauche du Portail Azure, recherchez et sélectionnez **Azure Active Directory B2C**.
4. Cliquez sur **Fournisseurs d’identité**, puis sélectionnez **Ajouter**.
5. Entrez un **nom**. Par exemple, entrez *GitHub*.
6. Pour le **type de fournisseur d'identité**, sélectionnez **GitHub (préversion)**, puis cliquez sur **OK**.
7. Sélectionnez **Configurer ce fournisseur d’identité**, entrez l’ID client enregistré précédemment en tant **qu’ID Client** et entrez le secret client enregistré en tant que **Secret client** de l’application de compte GitHub créée précédemment.
8. Cliquez sur **OK**, puis sur **Créer** pour enregistrer la configuration de votre compte GitHub.
