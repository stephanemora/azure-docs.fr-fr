---
title: Prendre en charge AD FS dans la bibliothèque d’authentification Microsoft pour .NET | Azure
description: Découvrez la prise en charge des services de fédération Active Directory (AD FS) dans la bibliothèque d’authentification Microsoft pour .NET (MSAL.NET).
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
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66676727"
---
# <a name="active-directory-federation-services-support-in-msalnet"></a>Prendre en charge les services de fédération Active Directory (AD FS) dans MSAL.NET
Les services de fédération Active Directory (AD FS) dans Windows Server vous permettent d’ajouter authentification et autorisation OpenID Connect et OAuth 2.0 aux applications que vous développez, applications qui peuvent ensuite authentifier les utilisateurs directement auprès d’AD FS. Pour plus d’informations, consultez [Scénarios AD FS pour développeurs](/windows-server/identity/ad-fs/overview/ad-fs-scenarios-for-developers).

La bibliothèque d’authentification Microsoft pour .NET (MSAL.NET) prend en charge deux scénarios pour l’authentification auprès d’AD FS :

- MSAL.NET communique avec le service Azure Active Directory, qui lui-même est *fédéré* à AD FS.
- MSAL.NET communique *directement* avec une autorité d’AD FS, pour laquelle la version d’AD FS est compatible avec OpenID Connect (à partir d’AD FS 2019). La connexion directe à AD FS permet à MSAL.NET de s’authentifier auprès d’applications qui s’exécutent dans [Azure Stack](https://azure.microsoft.com/overview/azure-stack/).

## <a name="msal-connects-to-azure-ad-which-is-federated-with-ad-fs"></a>MSAL se connecte à Azure AD, qui est fédéré à AD FS
MSAL.NET prend en charge la connexion à Azure AD, qui connecte les utilisateurs managés (utilisateurs managés dans Azure AD) ou fédérés (utilisateurs managés par un autre fournisseur d’identité, tel qu’AD FS). MSAL.NET ignore le fait que les utilisateurs sont fédérés. En ce qui le concerne, il communique avec Azure AD.

L’[autorité](msal-client-application-configuration.md#authority) que vous utilisez dans ce cas est l’autorité habituelle (nom d’hôte d’autorité + locataire, commun ou organisations).

### <a name="acquiring-a-token-interactively"></a>Acquisition d’un jeton de manière interactive
Lorsque vous appelez la méthode `AcquireTokenInteractive`, l’expérience utilisateur est généralement la suivante :

1. L’utilisateur entre son ID de compte.
2. Azure AD affiche brièvement le message « Vous êtes redirigé vers la page de votre organisation ».
3. L’utilisateur est redirigé vers la page de connexion du fournisseur d’identité. La page de connexion est en règle générale personnalisée avec le logo de l’organisation.

Les versions AD FS prises en charge dans ce scénario fédéré sont AD FS v2, AD FS v3 (Windows Server 2012 R2) et AD FS v4 (AD FS 2016).

### <a name="acquiring-a-token-using-acquiretokenbyintegratedauthentication-or-acquiretokenbyusernamepassword"></a>Acquisition d’un jeton à l’aide de AcquireTokenByIntegratedAuthentication ou de AcquireTokenByUsernamePassword
Lors de l’acquisition d’un jeton à l’aide des méthodes `AcquireTokenByIntegratedAuthentication` ou `AcquireTokenByUsernamePassword`, MSAL.NET obtient le fournisseur d’identité à contacter en fonction du nom d’utilisateur.  MSAL.NET reçoit un [jeton SAML 1.1](reference-saml-tokens.md) après avoir contacté le fournisseur d’identité.  MSAL.NET fournit ensuite le jeton SAML à Azure AD, sous la forme d’une assertion d’utilisateur (similaire au [flux on-behalf-of](msal-authentication-flows.md#on-behalf-of)) pour récupérer un jeton JWT.

## <a name="msal-connects-directly-to-ad-fs"></a>MSAL se connecte directement à AD FS
MSAL.NET prend en charge la connexion à AD FS 2019, qui est compatible avec Open ID Connect. Lorsque vous vous connectez directement à AD FS, l’autorité que vous voulez utiliser pour générer votre application est similaire à `https://mysite.contoso.com/adfs/`.

Actuellement, il n’existe aucun plan pour prendre en charge une connexion directe à AD FS 2016 ou à AD FS v2 (qui ne sont pas compatibles avec OpenID Connect). Si vous avez besoin de prendre en charge des scénarios qui exigent une connexion directe à AD FS 2016, utilisez la dernière version en date de [Bibliothèque d’authentification Azure Active Directory](active-directory-authentication-libraries.md#microsoft-supported-client-libraries). Lorsque vous aurez mis à niveau votre système local vers AD FS 2019, vous serez en mesure d’utiliser MSAL.NET.
