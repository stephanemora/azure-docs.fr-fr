---
title: Bibliothèques d’authentification Azure Active Directory v2.0 | Microsoft Docs
description: Bibliothèques clientes et bibliothèques de middleware de serveur compatibles, et liens vers la bibliothèque, la source et des exemples connexes pour le point de terminaison Azure Active Directory v2.0.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 19cec615-e51f-4141-9f8c-aaf38ff9f746
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/13/2018
ms.author: celested
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 8fe3db09acbdec606f25d0bc81300bc4f5e87411
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/14/2018
---
# <a name="azure-active-directory-v20-authentication-libraries"></a>Bibliothèques d’authentification Azure Active Directory v2.0

Le [point de terminaison Azure Active Directory (Azure AD) v2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-compare) prend en charge les protocoles OAuth 2.0 et OpenID Connect 1.0 standard. La bibliothèque d’authentification Microsoft (MSAL) est conçue pour fonctionner avec le point de terminaison Azure AD v2.0. Il est également possible d’utiliser des bibliothèques open source qui prennent en charge OAuth 2.0 et OpenID Connect 1.0.

Nous vous recommandons d’utiliser des bibliothèques écrites par des experts du domaine du protocole qui respectent une méthodologie Security Development Lifecycle (SDL) comme [celle que suit Microsoft][Microsoft-SDL]. Si vous décidez de coder à la main la prise en charge des protocoles, suivez une méthodologie comme celle de Microsoft SDL et de bien prendre en compte les considérations relatives à la sécurité que vous trouverez dans les spécifications des normes pour chaque protocole.

> [!NOTE]
> Vous recherchez les bibliothèques Azure AD v1.0 (ADAL) ? Consultez le [guide des bibliothèques ADAL](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries).
>
>

## <a name="types-of-libraries"></a>Types de bibliothèques

Le point de terminaison Azure AD v2.0 prend en charge deux types de bibliothèques :

* **Bibliothèques clientes**. Les serveurs et clients natifs utilisent les bibliothèques clientes afin d’obtenir des jetons d’accès pour appeler une ressource, comme Microsoft Graph.
* **Bibliothèques de middleware de serveur**. Les applications web utilisent des bibliothèques de middleware de serveur pour la connexion des utilisateurs. Les API web utilisent des bibliothèques de middleware de serveur pour valider les jetons qui sont envoyés par des clients natifs ou par d’autres serveurs.

## <a name="library-support"></a>Prise en charge de la bibliothèque

Étant donné que vous pouvez choisir n’importe quelle bibliothèque conforme aux normes lors de l’utilisation du point de terminaison v2.0, il est important de savoir où vous adresser pour la prise en charge. Pour tout problème ou demande de fonctionnalité au niveau du code de la bibliothèque, adressez-vous au propriétaire de la bibliothèque. Pour tout problème ou demande de fonctionnalité au niveau de l’implémentation du protocole côté service, adressez-vous à Microsoft. [Envoyez une demande de nouvelle fonctionnalité](https://feedback.azure.com/forums/169401-azure-active-directory) pour les fonctionnalités supplémentaires que vous aimeriez voir dans le protocole. [Créez une demande de support](https://docs.microsoft.com/en-us/azure/azure-supportability/how-to-create-azure-support-request) si vous détectez un problème pour lequel le point de terminaison Azure AD v2.0 n’est pas compatible avec OAuth 2.0 ou OpenID Connect 1.0.

Les bibliothèques sont classées en deux catégories de prise en charge :

* **Pris en charge par Microsoft**. Microsoft fournit des correctifs de ces bibliothèques, auxquelles il a appliqué la méthodologie SDL standard.
* **Compatible**. Microsoft a testé ces bibliothèques dans des scénarios de base et a confirmé que celles-ci fonctionnent avec le point de terminaison v2.0. Microsoft ne fournit pas de correctifs pour ces bibliothèques et n’a pas vérifié ces bibliothèques. Les problèmes et les demandes de fonctionnalités doivent être orientés vers le projet open source de la bibliothèque.

Pour obtenir la liste des bibliothèques qui fonctionnent avec le point de terminaison v2.0, consultez les sections suivantes de cet article.

## <a name="microsoft-supported-client-libraries"></a>Bibliothèques clientes prises en charge par Microsoft

> [!IMPORTANT]
> Les bibliothèques MSAL en préversion peuvent être utilisées dans un environnement de production. Microsoft fournit la même prise en charge au niveau de la production pour ces bibliothèques que pour les bibliothèques de production actuelles (ADAL). Tant que les bibliothèques sont disponibles en préversion, attendez-vous à des modifications à l’API MSAL, au format de cache interne et à d’autres mécanismes de ces bibliothèques, que vous devrez appliquer via des améliorations de fonctionnalités ou des correctifs de bogues. Cela peut avoir un impact sur votre application. Par exemple, une modification apportée au format de cache avoir pour conséquence de demander à vos utilisateurs de se reconnecter. Une modification de l’API peut vous obliger à mettre à jour votre code. Lorsque la version à disponibilité générale (GA) devient disponible, toutes les applications utilisant une version préliminaire de la bibliothèque devront être mises à jour dans les six mois, sans quoi elles pourraient cesser de fonctionner.

| Plateforme | Bibliothèque | Download | Code source | Exemple | Informations de référence
| --- | --- | --- | --- | --- | --- |
| .NET Client, Windows Store, UWP, Xamarin iOS et Android | MSAL .NET (préversion) |[NuGet](https://www.nuget.org/packages/Microsoft.Identity.Client) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) | [Application de bureau](guidedsetups/active-directory-mobileanddesktopapp-windowsdesktop-intro.md) |  |
| JavaScript | MSAL.js (préversion) | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js) | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js) | [Application à page unique](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2) |  |
| iOS, macOS | MSAL (préversion) | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-objc) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-objc) | [Application iOS](https://github.com/Azure-Samples/active-directory-msal-ios-swift) |  |
| Android | MSAL (préversion) | [Référentiel Central](https://repo1.maven.org/maven2/com/microsoft/identity/client/msal/) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-android) | [Application Android](guidedsetups/active-directory-mobileanddesktopapp-android-intro.md) | [JavaDocs](http://javadoc.io/doc/com.microsoft.identity.client/msal) |

## <a name="microsoft-supported-server-middleware-libraries"></a>Bibliothèques de middleware de serveur prises en charge par Microsoft

| Plateforme | Bibliothèque | Download | Code source | Exemple | Informations de référence
| --- | --- | --- | --- | --- | --- |
| .NET 4.x | Intergiciel OWIN OpenID Connect |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect) |[GitHub](https://github.com/aspnet/AspNetKatana/) |[Application MVC](guidedsetups/active-directory-serversidewebapp-aspnetwebappowin-intro.md) | |
| .NET 4.x | Intergiciel OWIN OAuth Bearer pour Azure AD |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.ActiveDirectory/) |[GitHub](https://github.com/aspnet/AspNetKatana/) |  | |
| .NET 4.x | Gestionnaire JWT pour .NET 4.5 | [NuGet](https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt/4.0.4.403061554) | [GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |
| .NET Core | Intergiciel ASP.NET OpenID Connect |[Microsoft.AspNetCore.Authentication.OpenIdConnect (NuGet)][ServerLib-NetCore-Owin-Oidc-Lib] |[Sécurité ASP.NET (GitHub)][ServerLib-NetCore-Owin-Oidc-Repo] |[Application MVC](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore-v2) |
| .NET Core | Intergiciel ASP.NET OAuth Bearer |[Microsoft.AspNetCore.Authentication.OAuth (NuGet)][ServerLib-NetCore-Owin-Oauth-Lib] |[Sécurité ASP.NET (GitHub)][ServerLib-NetCore-Owin-Oauth-Repo] |  |
| .NET Core | Gestionnaire JWT pour .NET Core  |[NuGet](https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt) |[GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |
| Node.js |Azure AD Passport |[npm](https://www.npmjs.com/package/passport-azure-ad) |[GitHub](https://github.com/AzureAD/passport-azure-ad) | [Application web](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs)| |

## <a name="compatible-client-libraries"></a>Bibliothèques clientes compatibles

| Plateforme | Nom de la bibliothèque | Version testée | Code source | Exemple |
|:---:|:---:|:---:|:---:|:---:|
| Android |[OIDCAndroidLib](https://github.com/kalemontes/OIDCAndroidLib/) |0.2.1 |[OIDCAndroidLib](https://github.com/kalemontes/OIDCAndroidLib) |[Exemple d’application native](active-directory-v2-devquickstarts-android.md) |
| iOS |[NXOAuth2Client](https://github.com/nxtbgthng/OAuth2Client) |1.2.8 |[NXOAuth2Client](https://github.com/nxtbgthng/OAuth2Client) |[Exemple d’application native](active-directory-v2-devquickstarts-ios.md) |
| JavaScript |[Hello.js](https://adodson.com/hello.js/) |1.13.5 |[Hello.js](https://github.com/MrSwitch/hello.js) |[SPA](https://github.com/Azure-Samples/active-directory-javascript-graphapi-web-v2) |
| Java | [Scribe Java scribejava](https://github.com/scribejava/scribejava) | [Version 3.2.0](https://github.com/scribejava/scribejava/releases/tag/scribejava-3.2.0) | [Scribe Java](https://github.com/scribejava/scribejava/) | |
| PHP | [The PHP League oauth2-client](https://github.com/thephpleague/oauth2-client) | [Version 1.4.2](https://github.com/thephpleague/oauth2-client/releases/tag/1.4.2) | [oauth2-client](https://github.com/thephpleague/oauth2-client/) | |
| Ruby |[OmniAuth](https://github.com/omniauth/omniauth/wiki) |omniauth:1.3.1</br>omniauth-oauth2:1.4.0 |[OmniAuth](https://github.com/omniauth/omniauth)</br>[OmniAuth OAuth2](https://github.com/intridea/omniauth-oauth2) |  |

## <a name="related-content"></a>Contenu connexe
Pour plus d’informations sur le point de terminaison Azure AD v2.0, consultez [Vue d’ensemble du modèle d’application Azure AD v2.0][AAD-App-Model-V2-Overview].

<!--Image references-->

<!--Reference style links -->
[AAD-App-Model-V2-Overview]: ../active-directory-appmodel-v2-overview.md
[ClientLib-NET-Lib]: http://www.nuget.org/packages/Microsoft.Identity.Client
[ClientLib-NET-Repo]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet
[ClientLib-NET-Sample]: active-directory-v2-devquickstarts-wpf.md
[ClientLib-Node-Lib]: https://www.npmjs.com/package/passport-azure-ad
[ClientLib-Node-Repo]: https://github.com/AzureAD/passport-azure-ad
[ClientLib-Node-Sample]:/
[ClientLib-Iosmac-Lib]:/
[ClientLib-Iosmac-Repo]:/
[ClientLib-Iosmac-Sample]:/
[ClientLib-Android-Lib]:/
[ClientLib-Android-Repo]:/
[ClientLib-Android-Sample]:/
[ClientLib-Js-Lib]:/
[ClientLib-Js-Repo]:/
[ClientLib-Js-Sample]:/

[Microsoft-SDL]: http://www.microsoft.com/sdl/default.aspx
[ServerLib-Net4-Owin-Oidc-Lib]: https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/
[ServerLib-Net4-Owin-Oidc-Repo]: http://katanaproject.codeplex.com/
[ServerLib-Net4-Owin-Oidc-Sample]: active-directory-v2-devquickstarts-dotnet-web.md
[ServerLib-Net4-Owin-Oauth-Lib]: https://www.nuget.org/packages/Microsoft.Owin.Security.OAuth/
[ServerLib-Net4-Owin-Oauth-Repo]: http://katanaproject.codeplex.com/
[ServerLib-Net4-Owin-Oauth-Sample]: https://azure.microsoft.com/documentation/articles/active-directory-v2-devquickstarts-dotnet-api/
[ServerLib-Net-Jwt-Lib]: https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt
[ServerLib-Net-Jwt-Repo]: https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet
[ServerLib-Net-Jwt-Sample]:/
[ServerLib-NetCore-Owin-Oidc-Lib]: https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.OpenIdConnect/
[ServerLib-NetCore-Owin-Oidc-Repo]: https://github.com/aspnet/Security
[ServerLib-NetCore-Owin-Oidc-Sample]: https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore-v2
[ServerLib-NetCore-Owin-Oauth-Lib]: https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.OAuth/
[ServerLib-NetCore-Owin-Oauth-Repo]: https://github.com/aspnet/Security
[ServerLib-NetCore-Owin-Oauth-Sample]:/
[ServerLib-Node-Lib]: https://www.npmjs.com/package/passport-azure-ad
[ServerLib-Node-Repo]: https://github.com/AzureAD/passport-azure-ad/
[ServerLib-Node-Sample]: https://azure.microsoft.com/documentation/articles/active-directory-v2-devquickstarts-node-web/
