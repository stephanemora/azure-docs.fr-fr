---
title: Configurer l’inscription et la connexion avec un compte Weibo à l’aide d’Azure Active Directory B2C | Microsoft Docs
description: Proposez l’inscription et la connexion aux clients disposant de comptes Weibo dans vos applications à l’aide d’Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 9701d1583a19be46c4c72a82d9f376a8db0c625c
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64704242"
---
# <a name="set-up-sign-up-and-sign-in-with-a-weibo-account-using-azure-active-directory-b2c"></a>Configurer l’inscription et la connexion avec un compte Weibo à l’aide d’Azure Active Directory B2C

> [!NOTE]
> Cette fonctionnalité est en préversion.
> 

## <a name="create-a-weibo-application"></a>Créer une application Weibo

Pour utiliser un compte Weibo en tant que fournisseur d’identité dans Azure Active Directory (Azure AD) B2C, vous devez créer une application dans votre locataire qui la représente. Si vous n’avez pas encore de compte Weibo, vous pouvez en obtenir un à l’adresse [https://weibo.com/signup/signup.php?lang=en-us](https://weibo.com/signup/signup.php?lang=en-us).

1. Connectez-vous au [portail des développeurs Weibo](https://open.weibo.com/) avec les informations d’identification de votre compte Weibo.
2. Une fois que vous êtes connecté, sélectionnez votre nom d’affichage dans le coin supérieur droit.
3. Dans la liste déroulante, sélectionnez **编辑开发者信息** (Modifier les informations développeur).
4. Entrez les informations requises et sélectionnez **提交** (Envoyer).
5. Finalisez le processus de vérification d’e-mail.
6. Accédez à la page de [vérification d’identité](https://open.weibo.com/developers/identity/edit).
7. Entrez les informations requises et sélectionnez **提交** (Envoyer).

### <a name="register-a-weibo-application"></a>Inscrire une application Weibo

1. Accédez à la [Page d’inscription de l’application Weibo](https://open.weibo.com/apps/new).
2. Entrez les informations nécessaires relatives à l’application.
3. Sélectionnez **创建** (Créer).
4. Copiez les valeurs **Clé d’application** et **Secret d’application**. Vous avez besoin des deux pour ajouter le fournisseur d’identité à votre locataire.
5. Téléchargez les photos requises et entrez les informations nécessaires.
6. Sélectionnez **保存以上信息** (Enregistrer).
7. Sélectionnez **高级信息** (Informations avancées).
8. Sélectionnez **编辑** (Modifier) en regard du champ pour OAuth2.0 **授权设置**(URL de redirection).
9. Entrez `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` pour OAuth2.0 **授权设置**(URL de redirection). Par exemple, si le nom de votre locataire est contoso, définissez l’URL sur `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`.
10. Sélectionnez **提交** (Envoyer).  

## <a name="configure-a-weibo-account-as-an-identity-provider"></a>Configuration d’un compte Weibo en tant que fournisseur d’identité

1. Connectez-vous au [portail Azure](https://portal.azure.com/) en tant qu’administrateur général de votre locataire Azure AD B2C.
2. Veillez à utiliser l’annuaire qui contient votre locataire Azure AD B2C en cliquant sur le **filtre Répertoire et abonnement** dans le menu du haut et en choisissant l’annuaire qui contient votre locataire.
3. Choisissez **Tous les services** dans le coin supérieur gauche du Portail Azure, recherchez et sélectionnez **Azure Active Directory B2C**.
4. Cliquez sur **Fournisseurs d’identité**, puis sélectionnez **Ajouter**.
5. Fournissez un **nom**. Par exemple, entrez *Weibo*.
6. Sélectionnez **Type de fournisseur d'identité**, **Weibo (préversion)**, puis cliquez sur **OK**.
7. Sélectionnez **Configurer ce fournisseur d’identité**, entrez la clé d’application enregistrée précédemment en tant **qu’ID Client** et entrez le secret d’application enregistré en tant que **Secret client** de l’application Weibo créée précédemment.
8. Cliquez sur **OK**, puis sur **Créer** pour enregistrer votre configuration Weibo.
