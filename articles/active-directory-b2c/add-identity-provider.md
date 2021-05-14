---
title: Ajout d’un fournisseur d’identité – Azure Active Directory B2C | Microsoft Docs
description: Découvrez comment ajouter un fournisseur d’identité à votre locataire Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 04/29/2021
ms.custom: mvc
ms.topic: how-to
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 9c7eda8db68d4b9fba532d2f665f31d302a7822e
ms.sourcegitcommit: 43be2ce9bf6d1186795609c99b6b8f6bb4676f47
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/29/2021
ms.locfileid: "108277207"
---
# <a name="add-an-identity-provider-to-your-azure-active-directory-b2c-tenant"></a>Ajout d’un fournisseur d’identité à un locataire Azure Active Directory B2C

Vous pouvez configurer Azure AD B2C pour permettre aux utilisateurs de se connecter à votre application avec des informations d’identification de fournisseurs d’identités de réseau social ou d’entreprise externes (IdP). Azure AD B2C prend en charge les fournisseurs d’identité externe, comme Facebook, compte Microsoft, Google, Twitter et tout fournisseur d’identité prenant en charge les protocoles OAuth 1.0, OAuth 2.0, OpenID Connect et SAML.

Avec la fédération des fournisseurs d’identités externes, vous pouvez donner à vos consommateurs la possibilité de se connecter avec leurs comptes de réseaux sociaux ou d’entreprise existants, sans avoir à créer de compte spécialement pour votre application.

Dans la page d’inscription ou de connexion, Azure AD B2C affiche une liste de fournisseurs d’identités externes à partir de laquelle l’utilisateur peut faire son choix pour se connecter. Dès lors qu’il a sélectionné un des fournisseurs d’identités externes, il est emmené (redirigé) sur le site web du fournisseur sélectionné pour terminer le processus de connexion. Une fois l’utilisateur dûment connecté, il est retourné à Azure AD B2C pour l’authentification du compte dans votre application.

![Exemple de connexion sur un appareil mobile avec un compte de réseau social (Facebook)](media/add-identity-provider/external-idp.png)

Vous pouvez ajouter des fournisseurs d’identité qui sont pris en charge par Azure Active Directory B2C (Azure AD B2C) à votre [flux d’utilisateurs](user-flow-overview.md) à l’aide du portail Azure. Vous pouvez également ajouter des fournisseurs d’identité à vos [stratégies personnalisées](user-flow-overview.md).

## <a name="select-an-identity-provider"></a>Sélectionner un fournisseur d'identité

Vous utilisez généralement un seul fournisseur d’identité dans vos applications, mais vous pouvez en ajouter d’autres. Les guides pratiques ci-dessous vous montrent comment créer l’application de fournisseur d’identité et ajouter le fournisseur d’identité à votre locataire, puis à votre flux utilisateur ou à votre stratégie personnalisée.

* [AD FS](identity-provider-adfs.md)
* [Amazon](identity-provider-amazon.md)
* [Apple](identity-provider-apple-id.md)
* [Azure AD (un seul locataire)](identity-provider-azure-ad-single-tenant.md)
* [Azure AD (plusieurs locataires)](identity-provider-azure-ad-multi-tenant.md)
* [Azure AD B2C](identity-provider-azure-ad-b2c.md)
* [Ebay](identity-provider-ebay.md)
* [Facebook](identity-provider-facebook.md)
* [Fournisseur d’identité générique](identity-provider-generic-openid-connect.md)
* [GitHub](identity-provider-github.md)
* [ID.me](identity-provider-id-me.md)
* [Google](identity-provider-google.md)
* [LinkedIn](identity-provider-linkedin.md)
* [Compte Microsoft](identity-provider-microsoft-account.md)
* [QQ](identity-provider-qq.md)
* [Salesforce](identity-provider-salesforce.md)
* [Salesforce (protocole SAML)](identity-provider-salesforce-saml.md)
* [Twitter](identity-provider-twitter.md)
* [WeChat](identity-provider-wechat.md)
* [Weibo](identity-provider-weibo.md)
