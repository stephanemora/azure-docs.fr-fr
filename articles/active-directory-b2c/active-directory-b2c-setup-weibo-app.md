---
title: Configurer l’inscription et la connexion avec un compte Weibo à l’aide d’Azure Active Directory B2C | Microsoft Docs
description: Proposez l’inscription et la connexion aux clients disposant de comptes Weibo dans vos applications à l’aide d’Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/09/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 2534e3e39d4a6dd1659dced5a1b0342798c0049e
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38969127"
---
# <a name="set-up-sign-up-and-sign-in-with-a-weibo-account-using-azure-active-directory-b2c"></a>Configurer l’inscription et la connexion avec un compte Weibo à l’aide d’Azure Active Directory B2C

> [!NOTE]
> Cette fonctionnalité est en préversion.
> 

## <a name="create-a-weibo-application"></a>Créer une application Weibo

Pour utiliser un compte Weibo en tant que fournisseur d’identité dans Azure Active Directory (Azure AD) B2C, vous devez créer une application dans votre locataire qui la représente. Si vous n’avez pas encore de compte Weibo, vous pouvez en obtenir un à l’adresse [http://weibo.com/signup/signup.php?lang=en-us](http://weibo.com/signup/signup.php?lang=en-us).

1. Connectez-vous au [portail des développeurs Weibo](http://open.weibo.com/) avec les informations d’identification de votre compte Weibo.
2. Une fois que vous êtes connecté, sélectionnez votre nom d’affichage dans le coin supérieur droit.
3. Dans la liste déroulante, sélectionnez **编辑开发者信息** (Modifier les informations développeur).
4. Entrez les informations requises et sélectionnez **提交** (Envoyer).
5. Finalisez le processus de vérification d’e-mail.
6. Accédez à la page de [vérification d’identité](http://open.weibo.com/developers/identity/edit).
7. Entrez les informations requises et sélectionnez **提交** (Envoyer).

### <a name="register-a-weibo-application"></a>Inscrire une application Weibo

1. Accédez à la [Page d’inscription de l’application Weibo](http://open.weibo.com/apps/new).
2. Entrez les informations nécessaires relatives à l’application.
3. Sélectionnez **创建** (Créer).
4. Copiez les valeurs **Clé d’application** et **Secret d’application**. Vous avez besoin des deux pour ajouter le fournisseur d’identité à votre locataire.
5. Téléchargez les photos requises et entrez les informations nécessaires.
6. Sélectionnez **保存以上信息** (Enregistrer).
7. Sélectionnez **高级信息** (Informations avancées).
8. Sélectionnez **编辑** (Modifier) en regard du champ pour OAuth2.0 **授权设置**(URL de redirection).
9. Entrez `https://login.microsoftonline.com/te/{tenant_name}/oauth2/authresp` pour OAuth2.0 **授权设置**(URL de redirection). Par exemple, si votre `tenant_name` est contoso.onmicrosoft.com, définissez l’URL sur `https://login.microsoftonline.com/te/contoso.onmicrosoft.com/oauth2/authresp`.
10. Sélectionnez **提交** (Envoyer).  

## <a name="configure-a-weibo-account-as-an-identity-provider"></a>Configuration d’un compte Weibo en tant que fournisseur d’identité

1. Connectez-vous au [portail Azure](https://portal.azure.com/) en tant qu’administrateur général de votre locataire Azure AD B2C.
2. Assurez-vous que vous utilisez le répertoire qui contient votre locataire Azure AD B2C en l’activant dans l’angle supérieur droit du portail Azure. Sélectionnez les informations sur votre abonnement, puis cliquez sur **Changer de répertoire**. 

    ![Basculez vers votre client Azure AD B2C.](./media/active-directory-b2c-setup-weibo-app/switch-directories.png)

    Choisissez le répertoire qui contient votre locataire.

    ![Sélectionner le répertoire](./media/active-directory-b2c-setup-weibo-app/select-directory.png)

3. Choisissez **Tous les services** dans le coin supérieur gauche du Portail Azure, recherchez et sélectionnez **Azure Active Directory B2C**.
4. Cliquez sur **Fournisseurs d’identité**, puis sélectionnez **Ajouter**.
5. Fournissez un **nom**. Par exemple, entrez *Weibo*.
6. Sélectionnez **Type de fournisseur d'identité**, **Weibo (préversion)**, puis cliquez sur **OK**.
7. Sélectionnez **Configurer ce fournisseur d’identité**, entrez la clé d’application enregistrée précédemment en tant **qu’ID Client** et entrez le secret d’application enregistré en tant que **Secret client** de l’application Weibo créée précédemment.
8. Cliquez sur **OK**, puis sur **Créer** pour enregistrer votre configuration Weibo.
