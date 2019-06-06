---
title: AD FS prend en charge dans la bibliothèque d’authentification Microsoft pour .NET | Azure
description: En savoir plus sur Active Directory Federation Services (ADFS) prise en charge dans la bibliothèque d’authentification Microsoft pour .NET (MSAL.NET).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/03/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: e5963c5e83b5af3848edd934328caa1f095bd184
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66676727"
---
# <a name="active-directory-federation-services-support-in-msalnet"></a>Active Directory Federation Services prennent en charge dans MSAL.NET
Active Directory Federation Services (ADFS) dans Windows Server vous permet d’ajouter OpenID Connect et OAuth 2.0 en fonction de l’authentification et autorisation pour les applications que vous développez et ces avez sont authentifier les utilisateurs directement dans AD FS. Pour plus d’informations, consultez [scénarios AD FS pour les développeurs](/windows-server/identity/ad-fs/overview/ad-fs-scenarios-for-developers).

Bibliothèque d’authentification Microsoft pour .NET (MSAL.NET) prend en charge deux scénarios pour l’authentification auprès d’AD FS :

- MSAL.NET communique avec Azure Active Directory, qui elle-même est *fédéré* avec AD FS.
- Discussions MSAL.NET *directement* à une autorité d’AD FS, où la version des services AD FS est OpenID Connect conforme (en commençant dans AD FS 2019). Permet de se connecter directement à AD FS MSAL.NET auprès d’applications qui s’exécutent [Azure Stack](https://azure.microsoft.com/overview/azure-stack/).

## <a name="msal-connects-to-azure-ad-which-is-federated-with-ad-fs"></a>MSAL se connecte à Azure AD, qui est fédéré avec AD FS
MSAL.NET prend en charge la connexion à Azure AD, qui connecte les utilisateurs gérés (utilisateurs gérés dans Azure AD) ou fédérées des utilisateurs (gérés par un autre fournisseur d’identité tel qu’AD FS). MSAL.NET ne connaît pas le fait que les utilisateurs sont fédérés. En ce qui concerne, il communique avec Azure AD.

Le [autorité](msal-client-application-configuration.md#authority) vous utilisez dans ce cas est l’autorité habituelle (nom d’hôte autorité + client, commun ou des organisations).

### <a name="acquiring-a-token-interactively"></a>L’acquisition d’un jeton de manière interactive
Lorsque vous appelez le `AcquireTokenInteractive` , l’expérience utilisateur est généralement :

1. L’utilisateur entre son ID de compte.
2. Azure AD affiche brièvement le message « Vous êtes redirigé vers la page de votre organisation ».
3. L’utilisateur est redirigé vers la page de connexion du fournisseur d’identité. La page de connexion est généralement personnalisée avec le logo de l’organisation.

AD FS versions prises en charge ce scénario fédérée sont v2 AD FS, AD FS v3 (Windows Server 2012 R2) et AD FS v4 (AD FS 2016).

### <a name="acquiring-a-token-using-acquiretokenbyintegratedauthentication-or-acquiretokenbyusernamepassword"></a>L’acquisition d’un jeton à l’aide de AcquireTokenByIntegratedAuthentication ou AcquireTokenByUsernamePassword
Lors de l’acquisition d’un jeton à l’aide du `AcquireTokenByIntegratedAuthentication` ou `AcquireTokenByUsernamePassword` méthodes, MSAL.NET obtient à contacter le fournisseur d’identité basé sur le nom d’utilisateur.  MSAL.NET reçoit un [jeton SAML 1.1](reference-saml-tokens.md) après avoir contacté le fournisseur d’identité.  MSAL.NET fournit ensuite le jeton SAML à Azure AD comme une assertion de l’utilisateur (similaire à la [flux on-behalf-of](msal-authentication-flows.md#on-behalf-of)) pour retourner un jeton JWT.

## <a name="msal-connects-directly-to-ad-fs"></a>MSAL se connecte directement aux services AD FS
MSAL.NET prend en charge la connexion à AD FS 2019, c'est-à-dire Open ID Connect conforme. Lorsque vous vous connectez directement à AD FS, l’autorité que vous souhaitez utiliser pour générer votre application est similaire à `https://mysite.contoso.com/adfs/`.

Actuellement, il n’y a aucun plan pour prendre en charge une connexion directe à AD FS 2016 ou AD FS v2 (qui n’est pas cette conformité). Si vous avez besoin prendre en charge des scénarios qui exigent une connexion directe à AD FS 2016, utilisez la dernière version de [Azure Active Directory Authentication Library](active-directory-authentication-libraries.md#microsoft-supported-client-libraries). Lorsque vous avez mis à niveau votre système de site à AD FS 2019, vous serez en mesure d’utiliser MSAL.NET.
