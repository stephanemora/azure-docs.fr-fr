---
title: Flux d’authentification (Microsoft Authentication Library) | Azure
description: En savoir plus sur le flux/accorde d’authentification utilisé par la bibliothèque d’authentification Microsoft (MSAL).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: celested
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/25/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 39f323c2ac86e8d42319b3d99221f6c20beff3e4
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/08/2019
ms.locfileid: "65406684"
---
# <a name="authentication-flows"></a>Flux d’authentification

Cet article décrit les flux d’authentification fournis par Microsoft Authentication Library (MSAL).  Ces flux peut être utilisé dans un large éventail de différents scénarios d’applications.

| Flux | Description  | Utilisé dans|  
| ---- | ----------- | ------- | 
| [Interactive](#interactive) | Obtient le jeton via un processus interactif qui invite l’utilisateur pour les informations d’identification via un navigateur ou une fenêtre indépendante. | [Applications de bureau](scenario-desktop-overview.md), [des applications mobiles](scenario-mobile-overview.md) |
| [Octroi implicite](#implicit-grant) | Autorise l’application obtenir des jetons sans effectuer un échange d’informations d’identification du serveur principal. L’application peut alors connecter l’utilisateur, maintenir la session et obtenir des jetons pour d’autres API web, le tout dans le code JavaScript client.| [Applications à page unique (SPA)](scenario-spa-overview.md) |
| [Code d’autorisation](#authorization-code) | Utilisé dans les applications qui sont installées sur un appareil pour accéder aux ressources protégées, telles que les API web. Cela vous permet de vous permet d’ajouter la connexion et d’accès d’API à vos applications mobiles et de bureau. | [Applications de bureau](scenario-desktop-overview.md), [des applications mobiles](scenario-mobile-overview.md), [applications Web](scenario-web-app-call-api-overview.md) | 
| [On-behalf-of](#on-behalf-of) | Une application appelle une service/API web, qui à son tour doit appeler un autre service/web API. L’idée est de propager l’identité et les autorisations de l’utilisateur délégué via la chaîne de la demande. | [Les API web](scenario-web-api-call-api-overview.md) |
| [Informations d’identification du client](#client-credentials) | Permet d’accéder aux ressources hébergées sur le web à l’aide de l’identité d’une application. Couramment utilisé pour les interactions de serveur à serveur qui doivent s’exécuter en arrière-plan, sans interaction immédiate d’un utilisateur. | [Applications de démon](scenario-daemon-overview.md) |
| [Code de l’appareil](#device-code) | Permet aux utilisateurs de se connecter à des appareils avec contraintes d’entrée comme une télévision intelligente, appareils IoT ou imprimante. | [Applications de bureau/Mobile](scenario-desktop-acquire-token.md#command-line-tool-without-web-browser) |
| [Authentification Windows intégrée](scenario-desktop-acquire-token.md#integrated-windows-authentication) | Permet à des applications sur le domaine ou Azure AD joint les ordinateurs pour acquérir un jeton en mode silencieux (sans interaction de l’interface utilisateur à partir de l’utilisateur).| [Applications de bureau/Mobile](scenario-desktop-acquire-token.md#integrated-windows-authentication) |
| [Nom d’utilisateur/mot de passe](scenario-desktop-acquire-token.md#username--password) | Permet à une application connecter l’utilisateur en gérant directement leur mot de passe. Ce flux n’est pas recommandé. | [Applications de bureau/mobile](scenario-desktop-acquire-token.md#username--password) | 

## <a name="interactive"></a>Interactif
MSAL prend en charge la possibilité d’inviter l’utilisateur ses informations d’identification pour se connecter et obtenir un jeton à l’aide de ces informations d’identification.

![Flux interactif](media/msal-authentication-flows/interactive.png)

Pour plus d’informations sur l’utilisation de MSAL.NET pour acquérir des jetons sur des plateformes spécifiques de manière interactive, consultez les rubriques suivantes :
- [Xamarin Android](msal-net-xamarin-android-considerations.md)
- [Xamarin iOS](msal-net-xamarin-ios-considerations.md)
- [Plateforme Windows universelle](msal-net-uwp-considerations.md)

Pour plus d’informations sur les appels interactifs dans MSAL.js lire [comportement dans des requêtes interactives MSAL.js d’invite](msal-js-prompt-behavior.md)

## <a name="implicit-grant"></a>Octroi implicite

MSAL prend en charge la [flux d’octroi OAuth 2 implicite](v2-oauth2-implicit-grant-flow.md), ce qui permet à l’application obtenir des jetons à partir de la plateforme d’identité Microsoft sans effectuer d’un serveur principal exchange des informations d’identification. L’application peut alors connecter l’utilisateur, maintenir la session et obtenir des jetons pour d’autres API web, le tout dans le code JavaScript client.

![Flux d’octroi implicite](media/msal-authentication-flows/implicit-grant.svg)

De nombreuses applications web modernes sont générées en tant qu’applications à page unique du côté client écrites à l’aide de JavaScript ou un framework SPA comme Angular, Vue.js et React.js. Ces applications s’exécutent dans un navigateur web et présentent les caractéristiques d’authentification que les applications web côté serveur traditionnelles. La plateforme Microsoft identity permet aux applications de page unique connecter des utilisateurs et obtenir des jetons pour accéder aux services principaux ou web API à l’aide du flux d’octroi implicite. Le flux implicite permet à l’application obtenir des jetons d’ID pour représenter l’utilisateur authentifié et également accéder aux jetons nécessaires pour appeler une API protégée.

Ce flux d’authentification n’inclut pas de scénarios d’application à l’aide des infrastructures de JavaScript inter-plateformes telles que d’électrons et React-Native, puisqu’elles nécessitent davantage fonctionnalités pour l’interaction avec les plateformes natives.

## <a name="authorization-code"></a>Code d'autorisation
MSAL prend en charge la [octroi de code d’autorisation OAuth 2](v2-oauth2-auth-code-flow.md), qui peut être utilisé dans les applications qui sont installées sur un appareil pour accéder aux ressources protégées, telles que les API web. Cela vous permet de vous permet d’ajouter la connexion et d’accès d’API à vos applications mobiles et de bureau. 

Lorsque les utilisateurs se connectent aux applications web (sites web), l’application web reçoit un code d’autorisation.  Le code d’autorisation est utilisé pour acquérir un jeton pour appeler l’API web. Dans ASP.NET / ASP.NET cœur des applications web, le seul objectif de `AcquireTokenByAuthorizationCode` consiste à ajouter un jeton au cache du jeton, afin qu’il peut ensuite être utilisé par l’application (généralement dans les contrôleurs) que simplement obtenir un jeton pour une API à l’aide `AcquireTokenSilent`.

![Flux de code d’autorisation](media/msal-authentication-flows/authorization-code.png)

1. Demande un code d’autorisation, qui est utilisé pour un jeton d’accès.
2. Utilise le jeton d’accès pour appeler une API web.

### <a name="considerations"></a>Considérations
- Le code d’autorisation est utilisable qu’une seule fois pour obtenir un jeton. N’essayez pas d’acquisition de jeton plusieurs fois avec le même code d’autorisation (il est explicitement interdite par la spécification du protocole standard). Si vous échangez contre le code plusieurs fois intentionnellement, ou parce que vous n’êtes pas conscient qu’une infrastructure également le fait pour vous, vous obtiendrez une erreur : `AADSTS70002: Error validating credentials. AADSTS54005: OAuth2 Authorization code was already redeemed, please retry with a new valid code or use an existing refresh token.`

- Si vous écrivez une application ASP.NET/ASP.NET Core, cela peut se produire que si vous n’indiquez à l’infrastructure ASP.NET/Core que vous avez déjà utilisé le code d’autorisation. Pour ce faire, vous devez appeler `context.HandleCodeRedemption()` méthode de la `AuthorizationCodeReceived` Gestionnaire d’événements.

- Évitez de partager le jeton d’accès avec ASP.NET, qui peut empêcher la consentement incrémentiel qui passe correctement.  Pour plus d’informations, consultez [émettre #693](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/693).

## <a name="on-behalf-of"></a>On-behalf-of

MSAL prend en charge la [OAuth 2 on-behalf-of OAuth](v2-oauth2-on-behalf-of-flow.md).  Ce flux est utilisé lorsqu’une application appelle une service/API web, qui à son tour doit appeler un autre service/web API. L’idée est de propager l’identité et les autorisations de l’utilisateur délégué via la chaîne de la demande. Pour le service de couche intermédiaire à faire des demandes authentifiées au service en aval, il doit sécuriser un jeton d’accès à partir de la plateforme Microsoft identity, pour le compte de l’utilisateur.

![Flux On-Behalf-Of](media/msal-authentication-flows/on-behalf-of.png)

1. Acquiert un jeton d’accès pour l’API Web
2. Un client (Web, application monopage mobile, de bureau,) appelle une API Web protégée, en ajoutant le jeton d’accès comme un jeton du porteur dans l’en-tête d’authentification de la requête HTTP. L’API Web authentifie l’utilisateur.
3. Lorsque le client appelle l’API Web, l’API Web demande un autre jeton sur la part de l’utilisateur.  
4. L’API Web protégée utilise ce jeton pour appeler une API Web en aval sur la part de l’utilisateur.  L’API Web peut demander également ultérieurement des jetons pour les autres API en aval (mais toujours au nom de l’utilisateur même).

## <a name="client-credentials"></a>Informations d'identification client

MSAL prend en charge la [flux des informations d’identification client OAuth 2](v2-oauth2-client-creds-grant-flow.md). Ce flux permet d’accéder aux ressources hébergées sur le web à l’aide de l’identité d’une application. Ce type d’octroi est couramment utilisé pour les interactions de serveur à serveur qui doivent s’exécuter en arrière-plan sans l’interaction immédiate d’un utilisateur. Ces types d’applications sont souvent appelés démons ou comptes de service. 

Les informations d’identification client accorder flux autorise un service web (client confidentiel) pour utiliser ses propres informations d’identification, au lieu d’emprunter l’identité d’un utilisateur, pour s’authentifier lors de l’appel d’un autre service web. Dans ce scénario, le client est généralement un service web de niveau intermédiaire, un service démon ou un site web. Pour augmenter le niveau d’assurance, la plateforme d’identités Microsoft autorise également le service d’appel à utiliser un certificat (au lieu d’un secret partagé) comme une information d’identification.

> [!NOTE]
> Le flux client confidentiel n’est pas disponible sur les plateformes mobiles (UWP, Xamarin.iOS et Xamarin.Android), étant donné que ceux-ci prennent en charge uniquement les applications de client public.  Les applications de client public ne savent pas prouver l’identité de l’application pour le fournisseur d’identité. Une connexion sécurisée peut être obtenue sur l’application web ou API web des serveurs principaux en déployant un certificat.

MSAL.NET prend en charge deux types d’informations d’identification du client. Ces informations d’identification du client doivent être inscrits auprès d’Azure AD. Les informations d’identification sont transmises à des constructeurs de l’application de client confidentiel dans votre code.

### <a name="application-secrets"></a>Secrets de l’application 

![Client confidentiel avec mot de passe](media/msal-authentication-flows/confidential-client-password.png)

1. Acquiert un jeton à l’aide des informations d’identification d’application/mot de passe secret.
2. Utilise le jeton pour effectuer des demandes de la ressource.

### <a name="certificates"></a>Certificats 

![Client confidentiel avec un certificat](media/msal-authentication-flows/confidential-client-certificate.png)

1. Acquiert un jeton à l’aide des informations d’identification de certificat.
2. Utilise le jeton pour effectuer des demandes de la ressource.

Ces informations d’identification du client doivent être :
- Inscrit auprès d’Azure AD.
- Passé à la construction de l’application de client confidentiel dans votre code.


## <a name="device-code"></a>Code de l’appareil
MSAL prend en charge la [le flux de code d’appareil OAuth 2](v2-oauth2-device-code.md), ce qui permet aux utilisateurs de se connecter à des appareils avec contraintes d’entrée comme une télévision intelligente, appareils IoT ou imprimante. Authentification interactive avec Azure AD nécessite un navigateur web. Le flux de code d’appareil permet à l’utilisateur un autre appareil (par exemple, un autre ordinateur ou un téléphone mobile) permet de vous connecter interactivement où l’appareil ou le système d’exploitation ne fournit pas un navigateur Web.

En utilisant le flux de code d’appareil, l’application obtient des jetons via un processus en deux étapes spécialement conçues pour ces appareils/système d’exploitation. Exemples de ces applications sont des applications qui s’exécutent sur des appareils iOT ou des outils de ligne de commande (CLI). 

![Flux de code d'appareil](media/msal-authentication-flows/device-code.png)

1. Chaque fois que l’authentification de l’utilisateur est requise, l’application fournit un code et invite l’utilisateur à utiliser un autre appareil (par exemple, un smartphone connecté à internet) pour accéder à une URL (par exemple, https://microsoft.com/devicelogin), où l’utilisateur est invité à entrer le code. Que fait, la page web entraîne l’utilisateur via une expérience d’authentification normal, y compris les invites de consentement et l’authentification multifacteur si nécessaire.

2. Après une authentification réussie, l’application de ligne de commande reçoit les jetons requis via un canal arrière et utilisera pour effectuer les appels d’API web dont il a besoin.

### <a name="constraints"></a>Contraintes

- Flux de code d’appareil est disponible uniquement sur les applications de client public.
- L’autorité passé lors de la construction de l’application cliente publique doit être :
  - tenanted (sous la forme `https://login.microsoftonline.com/{tenant}/` où `{tenant}` est le GUID représentant l’ID de locataire ou un domaine associé au locataire.
  - ou, les comptes professionnels et scolaires (`https://login.microsoftonline.com/organizations/`).
- Comptes personnels Microsoft ne sont pas encore pris en charge par le point de terminaison v2.0 Azure AD (vous ne pouvez pas utiliser le `/common` ou `/consumers` locataires).

## <a name="integrated-windows-authentication"></a>Authentification Windows intégrée
MSAL prend en charge l’authentification de Windows intégrée (IWA) pour le bureau ou des applications mobiles qui s’exécutent sur un joint à un domaine ou d’un Azure AD joint à un ordinateur de Windows. À l’aide d’authentification Windows intégrée, ces applications peuvent acquérir un jeton en mode silencieux (sans interaction de l’interface utilisateur à partir de l’utilisateur). 

![Authentification Windows intégrée](media/msal-authentication-flows/integrated-windows-authentication.png)

1. Acquiert un jeton à l’aide de l’authentification Windows intégrée.
2. Utilise le jeton pour effectuer des demandes de la ressource.

### <a name="constraints"></a>Contraintes

Prend en charge l’authentification Windows intégrée **fédérés** utilisateurs uniquement.  Les utilisateurs créés dans Active Directory et associé à Azure Active Directory. Les utilisateurs créés directement dans Azure AD, sans les sauvegarde AD (**gérés** utilisateurs) ne peut pas utiliser ce flux d’authentification. Cette limitation n’affecte pas la [flux de nom d’utilisateur/mot de passe](#usernamepassword).

Authentification Windows intégrée est pour les applications écrites pour les plateformes .NET Framework, .NET Core et plateforme Windows universelle.

Authentification Windows intégrée n’ignore pas MFA (multi-factor authentication). Si l’authentification Multifacteur est configurée, IWA peut échouer si une authentification MFA est nécessaire, car l’authentification Multifacteur nécessite une interaction utilisateur. Celle-ci est délicat. Authentification Windows intégrée est non interactif, mais l’autorisation à deux facteurs (2FA) nécessite l’interactivité utilisateur. Vous ne contrôlez pas lorsque le fournisseur d’identité demande 2FA à effectuer, fait de l’administrateur client. À partir de nos observations, à 2 facteurs est requis lorsque vous vous connectez à partir d’un autre pays, lorsque non connecté via un VPN à un réseau d’entreprise et parfois même lorsque connectés via VPN. Ne pensez pas un ensemble de règles déterministe, Azure Active Directory utilise IA en permanence apprendre si 2FA est requis. Vous devez revenir à une invite d’utilisateur (https://aka.ms/msal-net-interactive) si authentification intégrée Windows échoue.

L’autorité passé lors de la construction de l’application cliente publique doit être :
- tenanted (sous la forme `https://login.microsoftonline.com/{tenant}/` où `tenant` est le guid représentant l’ID de locataire ou un domaine associé au locataire.
- pour les comptes professionnels et scolaires (`https://login.microsoftonline.com/organizations/`). Comptes personnels Microsoft ne sont pas pris en charge (vous ne pouvez pas utiliser `/common` ou `/consumers` locataires).

Étant donné que l’authentification Windows intégrée est un flux en mode silencieux :
- l’utilisateur de votre application doit avoir précédemment a donné son consentement pour utiliser l’application. 
- ou bien, l’administrateur client doit avoir précédemment a donné son consentement à tous les utilisateurs dans le client à utiliser l’application.
- Cela signifie que :
    - soit en tant que développeur avoir cliqué sur le **Grant** sur le portail Azure pour vous-même, bouton 
    - ou un administrateur de locataires a appuyé sur le **Grant/revoke consentement de l’administrateur de domaine du locataire {}** situé dans le **autorisations d’API** onglet de l’inscription de l’application (consultez [ajouter des autorisations pour web Access API](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis))
    - ou, vous avez fourni un moyen pour les utilisateurs à donner son consentement à l’application (consultez [solliciter le consentement de l’utilisateur individuel](v2-permissions-and-consent.md#requesting-individual-user-consent))
    - ou, vous avez fourni un moyen de l’administrateur de client à donner son consentement pour l’application (consultez [consentement de l’administrateur](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant))

Le flux d’authentification Windows intégrée est activé pour les applications de bureau .NET, .NET Core et applications de plateforme universelle Windows. Sur .NET Core uniquement la surcharge qui prend le nom d’utilisateur est disponible comme la plateforme .NET Core ne peut pas demander le nom d’utilisateur pour le système d’exploitation.
  
Pour plus d’informations sur le consentement, consultez [v2.0 autorisations et consentement](v2-permissions-and-consent.md).

## <a name="usernamepassword"></a>Nom d’utilisateur/mot de passe 
MSAL prend en charge la [accorder des informations d’identification du mot de passe propriétaire de la ressource OAuth 2](v2-oauth-ropc.md), ce qui permet à une application connecter l’utilisateur en gérant directement leur mot de passe. Dans votre application de bureau, vous pouvez utiliser le flux de nom d’utilisateur/mot de passe pour acquérir un jeton en mode silencieux. Aucune interface utilisateur n’est nécessaire lors de l’utilisation de l’application.

![Flux de nom d’utilisateur/mot de passe](media/msal-authentication-flows/username-password.png)

1. Acquiert un jeton en envoyant le nom d’utilisateur et le mot de passe pour le fournisseur d’identité.
2. Appelle une API en utilisant le jeton web.

> [!WARNING]
> Ce flux est **déconseillé** , car elle requiert un niveau élevé d’exposition de confiance et l’utilisateur.  Vous devez uniquement utiliser ce flux lorsque les autres, plus sécurisées, les flux ne peut pas être utilisés. Pour plus d’informations sur ce problème, consultez [cet article](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/). 

Le flux par défaut pour acquérir un jeton en mode silencieux sur les ordinateurs joints au domaine de Windows est [l’authentification Windows intégrée](#integrated-windows-authentication). Sinon, vous pouvez également utiliser [flux de code d’appareil](#device-code)

Bien que cela est utile dans certains cas (scénarios DevOps), si vous souhaitez utiliser le nom d’utilisateur/mot de passe dans des scénarios interactifs où vous fournissez votre propre interface utilisateur, vous devez considérer vraiment sur le déplacement depuis celle-ci. En utilisant le nom d’utilisateur/mot de passe, vous sont en donnant à distance un certain nombre de choses :
- principaux clients d’identité moderne : mot de passe obtient capturé, relus. Étant donné que nous avons ce concept d’une clé secrète de partage qui peut être intercepté.
Cela est incompatible avec sans mot de passe.
- les utilisateurs qui doivent effectuer l’authentification Multifacteur ne pourront se connecter (comme il n’existe aucune interaction)
- Les utilisateurs ne pourront pas l’authentification unique

### <a name="constraints"></a>Contraintes

En dehors de la [les contraintes de l’authentification Windows intégrée](#integrated-windows-authentication), les contraintes suivantes s’appliquent également :

- Le flux de nom d’utilisateur/mot de passe n’est pas compatible avec l’accès conditionnel et l’authentification multifacteur : Par conséquent, si votre application s’exécute dans un locataire Azure AD où l’administrateur client requiert l’authentification multifacteur, vous ne pouvez pas utiliser ce flux. De nombreuses organisations le faire.
- Il fonctionne uniquement pour le travail et les comptes scolaires (pas MSA)
- Le flux est disponible sur les applications de bureau .NET et .NET core, mais pas sur plateforme Windows universelle.

### <a name="azure-ad-b2c-specifics"></a>Azure AD B2C spécificités

Pour plus d’informations sur l’utilisation de MSAL.NET et Azure AD B2C, consultez [ROPC à l’aide avec le B2C Azure AD (MSAL.NET)](msal-net-aad-b2c-considerations.md#resource-owner-password-credentials-ropc-with-azure-ad-b2c).
