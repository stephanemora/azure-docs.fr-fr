---
title: Configurer l’inscription et la connexion avec un compte Google à l’aide d’Azure Active Directory B2C | Microsoft Docs
description: Proposez l’inscription et la connexion aux clients disposant d’un compte Google dans vos applications avec Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: c53c948df295365df4c9643c06a6d309d9a88d2b
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2019
ms.locfileid: "54843938"
---
# <a name="set-up-sign-up-and-sign-in-with-a-google-account-using-azure-active-directory-b2c"></a>Configurer l’inscription et la connexion avec un compte Google à l’aide d’Azure Active Directory B2C

## <a name="create-a-google-application"></a>Créer une application Google

Pour utiliser un compte Google comme fournisseur d’identité dans Azure Active Directory (Azure AD) B2C, vous devez créer une application qui la représente dans votre locataire (tenant). Si vous ne disposez pas déjà d’un compte Google, vous pouvez en créer un à l’adresse [https://accounts.google.com/SignUp](https://accounts.google.com/SignUp).

1. Connectez-vous à la [Console Google Developers](https://console.developers.google.com/) avec les informations d’identification de votre compte Google.
2. Sélectionnez **Créer un projet**, puis cliquez sur **Créer**. Si vous avez déjà créé des projets, sélectionnez la liste des projets, puis **Nouveau projet**.
3. Entrez un **Nom du projet**, cliquez sur **Créer**, puis vérifiez que vous utilisez le nouveau projet.
3. Sélectionnez **Informations d’identification** dans le menu de gauche, puis **Créer des informations d’identification** > **ID client OAuth**.
4. Sélectionnez **Configurer l’écran de consentement**.
5. Sélectionnez ou spécifiez une **adresse e-mail** valide, indiquez un **nom de produit présenté aux utilisateurs**, ajoutez `b2clogin.com` à **Domaines autorisés**, puis cliquez sur **Enregistrer**.
6. Sous **Type d’application**, sélectionnez **Application web**.
7. Entrez un **Nom** pour votre application, saisissez `https://your-tenant-name.b2clogin.com` dans **Origines JavaScript autorisées** et `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` dans **URI de redirection autorisés**. Remplacez `your-tenant-name` par le nom de votre locataire. Vous devez utiliser uniquement des minuscules quand vous entrez le nom de votre locataire, même si le locataire est défini à l’aide de majuscules dans Azure AD B2C.
8. Cliquez sur **Créer**.
9. Copiez les valeurs de **ID client** et **Clé secrète client**. Vous aurez besoin de ces deux valeurs pour configurer Google comme fournisseur d’identité dans votre locataire. **Client secret** est une information d’identification de sécurité importante.

## <a name="configure-a-google-account-as-an-identity-provider"></a>Configurer un compte Google comme fournisseur d’identité

1. Connectez-vous au [portail Azure](https://portal.azure.com/) en tant qu’administrateur général de votre locataire Azure AD B2C.
2. Veillez à utiliser l’annuaire qui contient votre locataire Azure AD B2C en cliquant sur le **filtre Répertoire et abonnement** dans le menu du haut et en choisissant l’annuaire qui contient votre locataire.
3. Choisissez **Tous les services** dans le coin supérieur gauche du Portail Azure, recherchez et sélectionnez **Azure Active Directory B2C**.
4. Cliquez sur **Fournisseurs d’identité**, puis sélectionnez **Ajouter**.
5. Saisissez un **Nom**. Par exemple, entrez *Google*.
6. Sélectionnez **Type de fournisseur d’identité** et **Google**, puis cliquez sur **OK**.
7. Sélectionnez **Configurer ce fournisseur d’identité** et entrez l’ID client enregistré précédemment en tant **qu’ID Client** ainsi que la clé secrète client enregistrée en tant que **Clé secrète client** de l’application Google créée précédemment.
8. Cliquez sur **OK**, puis sur **Créer** pour enregistrer votre configuration Google.

