---
title: Configurer l’inscription et la connexion avec un compte WeChat à l’aide d’Azure Active Directory B2C
description: Proposez l’inscription et la connexion aux clients disposant de comptes WeChat dans vos applications à l’aide d’Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 377fd5c2be9e49e077303aac2a48fa2a0b8288ef
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/19/2019
ms.locfileid: "69622134"
---
# <a name="set-up-sign-up-and-sign-in-with-a-wechat-account-using-azure-active-directory-b2c"></a>Configurer l’inscription et la connexion avec un compte WeChat à l’aide d’Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-wechat-application"></a>Créer une application WeChat

Pour utiliser un compte WeChat en tant que fournisseur d’identité dans Azure Active Directory (Azure AD) B2C, vous devez créer une application dans votre locataire qui la représente. Si vous n’avez pas encore de compte WeChat, vous pouvez obtenir des informations sur [https://kf.qq.com/faq/161220Brem2Q161220uUjERB.html](https://kf.qq.com/faq/161220Brem2Q161220uUjERB.html).

### <a name="register-a-wechat-application"></a>Inscrire une application WeChat

1. Connectez-vous à [ https://open.weixin.qq.com/ ](https://open.weixin.qq.com/) avec vos informations d’identification WeChat.
1. Cliquez sur **管理中心** (centre de gestion).
1. Suivez les étapes d’inscription d’une nouvelle application.
1. Dans le champ **授权回调域** (URL de rappel), entrez `https://your-tenant_name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`. Par exemple, si le nom de votre locataire est contoso, définissez l’URL sur `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`.
1. Copiez l’**ID de l’application** et la **clé de l’application**. Vous aurez besoin des deux pour ajouter le fournisseur d’identité à votre locataire.

## <a name="configure-wechat-as-an-identity-provider-in-your-tenant"></a>Configurer WeChat en tant que fournisseur d’identité dans votre locataire

1. Connectez-vous au [portail Azure](https://portal.azure.com/) en tant qu’administrateur général de votre locataire Azure AD B2C.
1. Veillez à utiliser l’annuaire qui contient votre locataire Azure AD B2C en sélectionnant le filtre **Annuaire + abonnement** dans le menu du haut et en choisissant l’annuaire qui contient votre locataire.
1. Choisissez **Tous les services** dans le coin supérieur gauche du Portail Azure, recherchez et sélectionnez **Azure Active Directory B2C**.
1. Sélectionnez **Fournisseurs d’identité**, puis **Wechat (Préversion)** .
1. Saisissez un **Nom**. Par exemple, *WeChat*.
1. Dans **ID client**, entrez l’ID d’application de l’application Wechat que vous avez créée précédemment.
1. Dans **Clé secrète client**, entrez la clé d’application que vous avez enregistrée.
1. Sélectionnez **Enregistrer**.
