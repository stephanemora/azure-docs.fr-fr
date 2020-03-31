---
title: Prise en charge AD FS (MSAL pour Java)
titleSuffix: Microsoft identity platform
description: Découvrez la prise en charge des services de fédération Active Directory (AD FS) dans la bibliothèque d’authentification Microsoft pour Java (MSAL4j).
services: active-directory
author: sangonzal
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/21/2019
ms.author: sagonzal
ms.reviewer: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 3d834a8d1524595304c22fed9897094622dfd93f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76696212"
---
# <a name="active-directory-federation-services-support-in-msal-for-java"></a>Prendre en charge les services de fédération Active Directory (AD FS) dans MSAL pour Java

Les services de fédération Active Directory (AD FS) de Windows Server vous permettent d'ajouter l'authentification et l'autorisation OpenID Connect et OAuth 2.0 à votre application de bibliothèque d’authentification Microsoft pour Java (MSAL pour Java). Une fois intégrée, votre application peut authentifier les utilisateurs dans AD FS, fédérés via Azure AD. Pour plus d’informations sur les scénarios, consultez [Scénarios AD FS pour développeurs](/windows-server/identity/ad-fs/ad-fs-development).

Une application utilisant MSAL pour Java communique avec Azure Active Directory (Azure AD), qui fédère ensuite sur AD FS.

MSAL pour Java se connecte à Azure AD, qui connecte les utilisateurs managés dans Azure AD (utilisateurs managés) ou les utilisateurs managés par un autre fournisseur d’identité comme AD FS (utilisateurs fédérés). MSAL pour Java ne sait pas qu’un utilisateur est fédéré. Elle communique juste avec Azure AD.

L’[autorité](msal-client-application-configuration.md#authority) que vous utilisez dans ce cas est l’autorité habituelle (nom d’hôte d’autorité + locataire, commun ou organisations).

## <a name="acquire-a-token-interactively-for-a-federated-user"></a>Obtenir un jeton de manière interactive pour un utilisateur fédéré

Lorsque vous appelez la méthode `ConfidentialClientApplication.AcquireToken()` ou `PublicClientApplication.AcquireToken()` avec `AuthorizationCodeParameters` ou `DeviceCodeParameters`, l’expérience utilisateur est généralement la suivante :

1. L’utilisateur entre son ID de compte.
2. Azure AD affiche brièvement le message « Vous êtes redirigé vers la page de votre organisation » et l'utilisateur est redirigé vers la page de connexion du fournisseur d’identité. La page de connexion est en règle générale personnalisée avec le logo de l’organisation.

Les versions AD FS prises en charge dans ce scénario fédéré sont :
- Services de fédération Active Directory (AD FS) v2
- Services de fédération Active Directory (AD FS) v3 (Windows Server 2012 R2)
- Services de fédération Active Directory (AD FS) v4 (AD FS 2016)

## <a name="acquire-a-token-via-username-and-password"></a>Obtenir un jeton via un nom d'utilisateur et un mot de passe

Lorsque vous obtenez un jeton via `ConfidentialClientApplication.AcquireToken()` ou `PublicClientApplication.AcquireToken()` avec `IntegratedWindowsAuthenticationParameters` ou `UsernamePasswordParameters`, MSAL pour Java obtient le fournisseur d’identité à contacter en fonction du nom d’utilisateur. MSAL pour Java obtient un jeton [SAML 1.1](reference-saml-tokens.md) auprès du fournisseur d’identité, jeton ensuite fourni à Azure AD qui renvoie le JSON Web Token (JWT).

## <a name="next-steps"></a>Étapes suivantes

Pour l'authentification fédérée, consultez [Configurer le comportement de la connexion Azure Active Directory pour une application à l'aide d'une stratégie de découverte du domaine d'accueil](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-authentication-for-federated-users-portal).