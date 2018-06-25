---
title: Configuration de Weibo dans Azure Active Directory B2C | Microsoft Docs
description: Proposez l’inscription et la connexion à des consommateurs disposant de comptes Weibo dans vos applications sécurisées par Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 3/26/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: c11931b0b1c568591a89277844722dceeac59039
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2018
ms.locfileid: "34711465"
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-weibo-accounts"></a>Azure Active Directory B2C : Proposer l’inscription et la connexion à des consommateurs disposant de comptes Weibo

> [!NOTE]
> Cette fonctionnalité est en préversion. N’utilisez pas ce fournisseur d’identité dans votre environnement de production.
> 

## <a name="create-a-weibo-application"></a>Créer une application Weibo

Pour utiliser Weibo en tant que fournisseur d’identité dans Azure Active Directory (Azure AD) B2C, vous devez créer une application Weibo et lui fournir les paramètres appropriés. Pour ce faire, vous avez besoin d’un compte Weibo. Si vous n’en avez pas, vous pouvez en obtenir un à l’adresse [http://weibo.com/signup/signup.php?lang=en-us](http://weibo.com/signup/signup.php?lang=en-us).

### <a name="register-for-the-weibo-developer-program"></a>Inscrivez-vous au programme de développement Weibo

1. Accédez au [centre des développeurs Weibo](http://open.weibo.com/) et connectez-vous avec les informations d’identification de votre compte Weibo.
2. Une fois que vous êtes connecté, cliquez sur votre nom d’affichage dans le coin supérieur droit.
3. Dans la liste déroulante, sélectionnez **编辑开发者信息** (Modifier les informations développeur).
4. Entrez les informations requises dans le formulaire et cliquez sur **提交**(Envoyer).
5. Finalisez le processus de vérification d’e-mail.
6. Accédez à la page de [vérification d’identité](http://open.weibo.com/developers/identity/edit).
7. Entrez les informations requises dans le formulaire et cliquez sur **提交**(Envoyer).

### <a name="register-a-weibo-application"></a>Inscrire une application Weibo

1. Accédez à la [Page d’inscription de l’application Weibo](http://open.weibo.com/apps/new).
2. Entrez les informations nécessaires relatives à l’application.
3. Cliquez sur **创建** (Créer).
4. Copiez les valeurs **Clé d’application** et **Secret d’application**. Vous en aurez besoin ultérieurement.
5. Téléchargez les photos requises et entrez les informations nécessaires.
6. Cliquez sur **保存以上信息** (Enregistrer).
7. Cliquez sur **高级信息** (Informations avancées).
8. Cliquez sur **编辑** (Modifier) en regard du champ pour OAuth2.0 **授权设置**(URL de redirection).
9. Entrez `https://login.microsoftonline.com/te/{tenant_name}/oauth2/authresp` pour OAuth2.0 **授权设置**(URL de redirection). Par exemple, si votre `tenant_name` est contoso.onmicrosoft.com, définissez l’URL sur `https://login.microsoftonline.com/te/contoso.onmicrosoft.com/oauth2/authresp`.
10. Cliquez sur **提交** (Envoyer).  

## <a name="configure-weibo-as-an-identity-provider-in-your-tenant"></a>Configurer Weibo en tant que fournisseur d’identité dans votre locataire
1. Suivez ces étapes pour [accéder au panneau de fonctionnalités B2C](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) sur le portail Azure.
2. Dans le panneau de fonctionnalités B2C, cliquez sur **Fournisseurs d’identité**.
3. Cliquez sur **+Ajouter** en haut du volet.
4. Fournissez un **Nom** convivial pour la configuration de fournisseur d’identité. Par exemple, entrez « Weibo ».
5. Cliquez sur **Type de fournisseur d’identité**, sélectionnez **Weibo**, puis cliquez sur **OK**.
6. Cliquez sur **Configurer ce fournisseur d’identité**.
7. Entrez la **clé d’application** que vous avez copiée précédemment dans **ID client**.
8. Entrez le **secret d’application** que vous avez copié précédemment dans **Secret client**.
9. Cliquez sur **OK**, puis sur **Créer** pour enregistrer votre configuration Weibo.

