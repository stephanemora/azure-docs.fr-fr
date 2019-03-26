---
title: Configurer l’inscription et la connexion avec un compte Google - Azure Active Directory B2C | Microsoft Docs
description: Proposez l’inscription et la connexion aux clients disposant d’un compte Google dans vos applications avec Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 1d4f7460d53e721ca1cba7aba6c6ef9c45111ee5
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/26/2019
ms.locfileid: "58435438"
---
# <a name="set-up-sign-up-and-sign-in-with-a-google-account-using-azure-active-directory-b2c"></a>Configurer l’inscription et la connexion avec un compte Google à l’aide d’Azure Active Directory B2C

## <a name="create-a-google-application"></a>Créer une application Google

Pour utiliser un compte Google comme [fournisseur d’identité](active-directory-b2c-reference-oauth-code.md) dans Azure Active Directory (Azure AD) B2C, vous devez créer une application qui la représente dans votre locataire (tenant). Si vous ne disposez pas déjà d’un compte Google, vous pouvez en créer un à l’adresse [https://accounts.google.com/SignUp](https://accounts.google.com/SignUp).

1. Connectez-vous à la [Console Google Developers](https://console.developers.google.com/) avec les informations d’identification de votre compte Google.
2. Dans le coin supérieur gauche de la page, sélectionnez la liste des projets, puis **nouveau projet**.
3. Entrez un **Nom du projet**, cliquez sur **Créer**, puis vérifiez que vous utilisez le nouveau projet.
4. Sélectionnez **Informations d’identification** dans le menu de gauche, puis **Créer des informations d’identification** > **ID client OAuth**.
5. Sous **Type d’application**, sélectionnez **Application web**.
6. Entrez un **Nom** pour votre application, saisissez `https://your-tenant-name.b2clogin.com` dans **Origines JavaScript autorisées** et `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` dans **URI de redirection autorisés**. Remplacez `your-tenant-name` par le nom de votre locataire. Vous devez utiliser uniquement des minuscules quand vous entrez le nom de votre locataire, même si le locataire est défini à l’aide de majuscules dans Azure AD B2C.
7. Cliquez sur **Créer**.
8. Copiez les valeurs de **ID client** et **Clé secrète client**. Vous aurez besoin de ces deux valeurs pour configurer Google comme fournisseur d’identité dans votre locataire. **Client secret** est une information d’identification de sécurité importante.

## <a name="configure-a-google-account-as-an-identity-provider"></a>Configurer un compte Google comme fournisseur d’identité

1. Connectez-vous au [portail Azure](https://portal.azure.com/) en tant qu’administrateur général de votre locataire Azure AD B2C.
2. Veillez à utiliser l’annuaire qui contient votre locataire Azure AD B2C en cliquant sur le **filtre Répertoire et abonnement** dans le menu du haut et en choisissant l’annuaire qui contient votre locataire.
3. Choisissez **Tous les services** dans le coin supérieur gauche du Portail Azure, recherchez et sélectionnez **Azure Active Directory B2C**.
4. Cliquez sur **Fournisseurs d’identité**, puis sélectionnez **Ajouter**.
5. Saisissez un **Nom**. Par exemple, entrez *Google*.
6. Sélectionnez **Type de fournisseur d’identité** et **Google**, puis cliquez sur **OK**.
7. Sélectionnez **Configurer ce fournisseur d’identité** et entrez l’ID client enregistré précédemment en tant **qu’ID Client** ainsi que la clé secrète client enregistrée en tant que **Clé secrète client** de l’application Google créée précédemment.
8. Cliquez sur **OK**, puis sur **Créer** pour enregistrer votre configuration Google.

