---
title: Configurer l’inscription et la connexion avec un compte Amazon - Azure Active Directory B2C | Microsoft Docs
description: Proposez l’inscription et la connexion aux clients disposant d’un compte Amazon dans vos applications avec Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/21/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 9098e92c594a5a6043888042f143cd875b02833d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60316457"
---
# <a name="set-up-sign-up-and-sign-in-with-an-amazon-account-using-azure-active-directory-b2c"></a>Configurer l’inscription et la connexion avec un compte Amazon à l’aide d’Azure Active Directory B2C

## <a name="create-an-amazon-application"></a>Création d’une application Amazon

Pour utiliser un compte Amazon comme [fournisseur d’identité](active-directory-b2c-reference-oauth-code.md) dans Azure Active Directory (Azure AD) B2C, vous devez créer une application qui la représente dans votre locataire. Si vous n’avez pas déjà un compte Amazon, vous pouvez obtenir à l’adresse [ https://www.amazon.com/ ](https://www.amazon.com/).

1. Connectez-vous au [Centre de développement Amazon](https://login.amazon.com/) avec les informations d’identification de votre compte Amazon.
2. Si ce n’est déjà fait, cliquez sur **Sign Up**, suivez la procédure d’inscription pour développeur et acceptez la politique d’utilisation.
3. Cliquez sur **Enregistrer une nouvelle application**.
4. Entrez un **Nom**, une **Description** et une **URL d’avis de confidentialité**, puis cliquez sur **Enregistrer**. La déclaration de confidentialité est une page que vous gérez et qui fournit des informations de confidentialité aux utilisateurs.
5. Dans la section **Paramètres web**, copiez les valeurs **ID client**. Sélectionnez **Montrer le secret** pour obtenir la clé secrète client et copiez-la. Vous aurez besoin de ces deux valeurs pour configurer un compte Amazon en tant que fournisseur d’identité dans votre locataire. **Client Secret** est une information d’identification de sécurité importante.
6. Dans la section **Paramètres web**, sélectionnez **Modifier**, puis entrez `https://your-tenant-name.b2clogin.com` dans **Origines JavaScript autorisées** et `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` dans **URL de retour autorisées**. Remplacez `your-tenant-name` par le nom de votre locataire. Vous devez utiliser uniquement des minuscules quand vous entrez le nom de votre locataire, même si le locataire est défini à l’aide de majuscules dans Azure AD B2C.
7. Cliquez sur **Enregistrer**.

## <a name="configure-an-amazon-account-as-an-identity-provider"></a>Configurer un compte Amazon comme fournisseur d’identité

1. Connectez-vous au [portail Azure](https://portal.azure.com/) en tant qu’administrateur général de votre locataire Azure AD B2C.
2. Veillez à utiliser l’annuaire qui contient votre locataire Azure AD B2C en cliquant sur le **filtre Répertoire et abonnement** dans le menu du haut et en choisissant l’annuaire qui contient votre locataire.
3. Choisissez **Tous les services** dans le coin supérieur gauche du Portail Azure, recherchez et sélectionnez **Azure Active Directory B2C**.
4. Cliquez sur **Fournisseurs d’identité**, puis sélectionnez **Ajouter**.
5. Saisissez un **Nom**. Par exemple, entrez *Amazon*.
6. Sélectionnez **Type de fournisseur d’identité** et **Amazon**, puis cliquez sur **OK**.
7. Sélectionnez **Configurer ce fournisseur d’identité** et entrez l’ID client enregistré précédemment en tant **qu’ID Client** ainsi que la clé secrète client enregistrée en tant que **Clé secrète client** de l’application Amazon créée précédemment.
8. Cliquez sur **OK**, puis sur **Créer** pour enregistrer votre configuration Amazon.

