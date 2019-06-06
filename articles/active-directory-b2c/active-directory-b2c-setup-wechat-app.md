---
title: Configurer l’inscription et la connexion avec un compte WeChat à l’aide d’Azure Active Directory B2C | Microsoft Docs
description: Proposez l’inscription et la connexion aux clients disposant de comptes WeChat dans vos applications à l’aide d’Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: aa44edcf009d381894a581172ea5edffefe946a0
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66508134"
---
# <a name="set-up-sign-up-and-sign-in-with-a-wechat-account-using-azure-active-directory-b2c"></a>Configurer l’inscription et la connexion avec un compte WeChat à l’aide d’Azure Active Directory B2C

> [!NOTE]
> Cette fonctionnalité est en préversion.
> 

## <a name="create-a-wechat-application"></a>Créer une application WeChat

Pour utiliser un compte WeChat en tant que fournisseur d’identité dans Azure Active Directory (Azure AD) B2C, vous devez créer une application dans votre locataire qui la représente. Si vous n’avez pas encore de compte WeChat, vous pouvez en obtenir un à l’adresse [https://kf.qq.com/faq/161220Brem2Q161220uUjERB.html](https://kf.qq.com/faq/161220Brem2Q161220uUjERB.html).

### <a name="register-a-wechat-application"></a>Inscrire une application WeChat

1. Connectez-vous à [ https://open.weixin.qq.com/ ](https://open.weixin.qq.com/) avec vos informations d’identification WeChat.
2. Cliquez sur **管理中心** (centre de gestion).
3. Suivez les étapes d’inscription d’une nouvelle application.
4. Dans le champ **授权回调域** (URL de rappel), entrez `https://your-tenant_name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`. Par exemple, si le nom de votre locataire est contoso, définissez l’URL sur `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`.
5. Copiez l’**ID de l’application** et la **clé de l’application**. Vous aurez besoin des deux pour ajouter le fournisseur d’identité à votre locataire.

## <a name="configure-wechat-as-an-identity-provider-in-your-tenant"></a>Configurer WeChat en tant que fournisseur d’identité dans votre locataire

1. Connectez-vous au [portail Azure](https://portal.azure.com/) en tant qu’administrateur général de votre locataire Azure AD B2C.
2. Veillez à utiliser l’annuaire qui contient votre locataire Azure AD B2C en cliquant sur le **filtre Répertoire et abonnement** dans le menu du haut et en choisissant l’annuaire qui contient votre locataire.
3. Choisissez **Tous les services** dans le coin supérieur gauche du Portail Azure, recherchez et sélectionnez **Azure Active Directory B2C**.
4. Cliquez sur **Fournisseurs d’identité**, puis sélectionnez **Ajouter**.
5. Entrez un **nom**. Par exemple, entrez *WeChat*.
6. Sélectionnez **Type de fournisseur d'identité**, **WeChat (préversion)** , puis cliquez sur **OK**.
7. Sélectionnez **Configurer ce fournisseur d’identité**, entrez l’ID de l’application enregistrée précédemment en tant **qu’ID Client** et entrez la clé de l’application enregistrée en tant que **Secret client** de l’application WeChat créée précédemment.
8. Cliquez sur **OK**, puis sur **Créer** pour enregistrer votre configuration WeChat.

