---
title: Configurer l’inscription et la connexion avec un compte QQ à l’aide d’Azure Active Directory B2C | Microsoft Docs
description: Proposez l’inscription et la connexion aux clients disposant de comptes QQ dans vos applications à l’aide d’Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 1681fd0639c176d9a6a00f656c515c089973b554
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2019
ms.locfileid: "54845858"
---
# <a name="set-up-sign-up-and-sign-in-with-a-qq-account-using-azure-active-directory-b2c"></a>Configurer l’inscription et la connexion avec un compte QQ à l’aide d’Azure Active Directory B2C

> [!NOTE]
> Cette fonctionnalité est en préversion.
> 

## <a name="create-a-qq-application"></a>Créer une application QQ

Pour utiliser un compte QQ en tant que fournisseur d’identité dans Azure Active Directory (Azure AD) B2C, vous devez créer une application dans votre locataire qui la représente. Si vous n’avez pas encore de compte QQ, vous pouvez en obtenir un à l’adresse [https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033](https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033).

### <a name="register-for-the-qq-developer-program"></a>Inscrivez-vous au programme de développement QQ

1. Connectez-vous au [portail des développeurs QQ](http://open.qq.com) avec les informations d’identification de votre compte QQ.
2. Une fois connecté, accédez à [http://open.qq.com/reg](http://open.qq.com/reg) vous inscrire en tant que développeur.
3. Sélectionnez **个人** (développeur individuel).
4. Entrez les informations requises et sélectionnez **下一步** (étape suivante).
5. Finalisez le processus de vérification d’e-mail. Une fois que vous êtes inscrit en tant que développeur, vous devez attendre votre approbation pendant quelques jours. 

### <a name="register-a-qq-application"></a>Inscrire une application QQ

1. Accédez à [https://connect.qq.com/index.html](https://connect.qq.com/index.html).
2. Sélectionnez **应用管理** (gestion des applications).
5. Sélectionnez**创建应用**(créer une application) et entrez les informations requises.
7. Dans le champ **授权回调域** (URL de rappel), entrez `https://your-tenant-name.b2clogin.com/your-tenant-name}.onmicrosoft.com/oauth2/authresp`. Par exemple, si votre `tenant_name` est contoso, définissez l’URL sur `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`.
8. Sélectionnez **创建应用** (créer une application).
9. Dans la page de confirmation, sélectionnez **应用管理** (gestion des applications) pour revenir à la page de gestion des applications.
10. Sélectionnez **查看** (afficher) en regard de l’application que vous avez créée.
11. Sélectionnez**修改**(modifier).
12. Copiez l’**ID de l’application** et la **clé de l’application**. Vous avez besoin de ces deux valeurs pour ajouter le fournisseur d’identité à votre locataire.

## <a name="configure-qq-as-an-identity-provider"></a>Configuration de QQ en tant que fournisseur d'identité

1. Connectez-vous au [portail Azure](https://portal.azure.com/) en tant qu’administrateur général de votre locataire Azure AD B2C.
2. Veillez à utiliser l’annuaire qui contient votre locataire Azure AD B2C en cliquant sur le **filtre Répertoire et abonnement** dans le menu du haut et en choisissant l’annuaire qui contient votre locataire.
3. Choisissez **Tous les services** dans le coin supérieur gauche du Portail Azure, recherchez et sélectionnez **Azure Active Directory B2C**.
4. Cliquez sur **Fournisseurs d’identité**, puis sélectionnez **Ajouter**.
5. Entrez un **nom**. Par exemple, entrez *QQ*.
6. Sélectionnez **Type de fournisseur d'identité**, **QQ (préversion)**, puis cliquez sur **OK**.
7. Sélectionnez **Configurer ce fournisseur d’identité**, entrez l’ID de l’application enregistrée précédemment en tant **qu’ID Client** et entrez la clé de l’application enregistré en tant que **Secret client** de l’application QQ créée précédemment.
8. Cliquez sur **OK**, puis sur **Créer** pour enregistrer votre configuration QQ.

