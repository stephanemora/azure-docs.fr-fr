---
title: Configurer l’inscription et la connexion avec un compte GitHub
titleSuffix: Azure AD B2C
description: Proposez l’inscription et la connexion aux clients disposant de comptes GitHub dans vos applications à l’aide d’Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: ae431ecd76f0ae5d51880832a5de415d3271ba54
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/09/2019
ms.locfileid: "74949353"
---
# <a name="set-up-sign-up-and-sign-in-with-a-github-account-using-azure-active-directory-b2c"></a>Configurer l’inscription et la connexion avec un compte GitHub à l’aide d’Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-github-oauth-application"></a>Créer une application GitHub OAuth

Pour utiliser un compte GitHub en tant que [fournisseur d’identité](active-directory-b2c-reference-oauth-code.md) dans Azure Active Directory B2C (Azure AD B2C), vous devez créer dans votre locataire une application qui le représente. Si vous ne disposez pas déjà d’un compte GitHub, vous pouvez vous inscrire sur [https://www.github.com/](https://www.github.com/).

1. Connectez-vous au site web [Développeur GitHub](https://github.com/settings/developers) avec vos informations d’identification GitHub.
1. Sélectionnez **Applications OAuth**, puis **Nouvelle application OAuth**.
1. Entrez le **Nom de l’application** et l’**URL de la page d’accueil**.
1. Entrez `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` dans l’**URL de rappel d’autorisation**. Remplacez `your-tenant-name` par le nom de votre locataire Azure AD B2C. Utilisez uniquement des minuscules quand vous entrez le nom de votre locataire, même si le locataire est défini à l’aide de majuscules dans Azure AD B2C.
1. Cliquez sur **Inscrire l’application**.
1. Copiez les valeurs de **ID client** et **Clé secrète client**. Vous avez besoin des deux pour ajouter le fournisseur d’identité à votre locataire.

## <a name="configure-a-github-account-as-an-identity-provider"></a>Configuration d’un compte GitHub en tant que fournisseur d’identité

1. Connectez-vous au [portail Azure](https://portal.azure.com/) en tant qu’administrateur général de votre locataire Azure AD B2C.
1. Veillez à utiliser l’annuaire qui contient votre locataire Azure AD B2C en sélectionnant le filtre **Annuaire + abonnement** dans le menu du haut et en choisissant l’annuaire qui contient votre locataire.
1. Choisissez **Tous les services** dans le coin supérieur gauche du Portail Azure, recherchez et sélectionnez **Azure Active Directory B2C**.
1. Cliquez sur **Fournisseurs d’identité**, puis sélectionnez **GitHub (Préversion)** .
1. Saisissez un **Nom**. Par exemple, *GitHub*.
1. Dans **ID client**, entrez l’ID client de l’application GitHub que vous avez créée précédemment.
1. Dans **Clé secrète client**, entrez la clé secrète client que vous avez enregistrée.
1. Sélectionnez **Enregistrer**.
