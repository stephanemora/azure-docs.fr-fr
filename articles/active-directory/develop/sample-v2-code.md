---
title: Exemples de code pour l’authentification et l’autorisation de la plateforme d’identités Microsoft
description: Index d’exemples de code gérés par Microsoft illustrant l’authentification et l’autorisation dans plusieurs types d’applications, langages de développement et infrastructures.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: sample
ms.workload: identity
ms.date: 08/27/2021
ms.author: marsma
ms.reviewer: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 541c9f29e18f3972bf0f9e2a717457fa4fe07a70
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123102089"
---
# <a name="microsoft-identity-platform-code-samples"></a>Exemples de code pour la plateforme d’identités Microsoft

Ces exemples de code, élaborés et mis à jour par Microsoft, illustrent l’authentification et l’autorisation à l’aide d’Azure AD et de la plateforme d’identités Microsoft dans plusieurs [types d’applications](v2-app-types.md), langages de développement et infrastructures.

- Connectez les utilisateurs aux applications web et fournissez un accès autorisé aux API web protégées.
- Protégez une API web en exigeant un jeton d’accès pour effectuer des opérations d’API.

Chaque exemple de code comprend un fichier _README.md_ qui décrit comment générer le projet (le cas échéant) et exécuter l’exemple d’application. Les commentaires dans le code vous aident à comprendre les sections critiques qui implémentent l’authentification et l’autorisation à l’aide de bibliothèques d’authentification et de la plateforme d’identités.

## <a name="single-page-applications"></a>Applications monopages

Ces exemples montrent comment écrire une application monopage sécurisée avec la plateforme d’identité Microsoft. Ils utilisent l’une des saveurs disponibles de MSAL.js.

> [!div class="mx-tdCol2BreakAll"]
> | Langage/<br/>Plateforme | Exemple(s) de code <br/>sur GitHub | Auth<br/> libraries | Flux d’authentification |  
> | ------- | -------- | ------------- | -------------- |  
> | Angular | &#8226; [Connecter des utilisateurs](https://github.com/Azure-Samples/ms-identity-javascript-angular-tutorial/tree/main/1-Authentication/1-sign-in/README.md)<br/>&#8226; [Connecter des utilisateurs (B2C)](https://github.com/Azure-Samples/ms-identity-javascript-angular-tutorial/tree/main/1-Authentication/2-sign-in-b2c/README.md) <br/> &#8226; [Appeler Microsoft Graph](https://github.com/Azure-Samples/ms-identity-javascript-angular-tutorial/tree/main/2-Authorization-I/1-call-graph/README.md)<br/>&#8226; [Appeler l’API web .NET Core](https://github.com/Azure-Samples/ms-identity-javascript-angular-tutorial/tree/main/3-Authorization-II/1-call-api)<br/>&#8226; [Appeler l’API web .NET Core (B2C)](https://github.com/Azure-Samples/ms-identity-javascript-angular-tutorial/tree/main/3-Authorization-II/2-call-api-b2c)<br/>&#8226; [Appeler Microsoft Graph via OBO](https://github.com/Azure-Samples/ms-identity-javascript-angular-tutorial/tree/main/7-AdvancedScenarios/1-call-api-obo/README.md)<br/>&#8226; [Appeler l’API web .NET Core en utilisant PoP](https://github.com/Azure-Samples/ms-identity-javascript-angular-tutorial/tree/main/7-AdvancedScenarios/2-call-api-pop/README.md)<br/>&#8226; [Utiliser des rôles d’application pour le contrôle d’accès](https://github.com/Azure-Samples/ms-identity-javascript-angular-tutorial/tree/main/5-AccessControl/1-call-api-roles/README.md)<br/> &#8226; [Utiliser des groupes de sécurité pour le contrôle d’accès](https://github.com/Azure-Samples/ms-identity-javascript-angular-tutorial/tree/main/5-AccessControl/2-call-api-groups/README.md)<br/>&#8226; [Déployer sur Stockage Azure et App Service](https://github.com/Azure-Samples/ms-identity-javascript-angular-tutorial/tree/main/4-Deployment/README.md)| MSAL Angular | &#8226; Code d’autorisation avec PKCE<br/>&#8226; On-Behalf-Of (OBO) <br/>&#8226; Preuve de possession (PoP)|
> | WebAssembly Blazor | &#8226; [Connecter des utilisateurs](https://github.com/Azure-Samples/ms-identity-blazor-wasm/blob/main/WebApp-OIDC/MyOrg/README.md)<br/>&#8226; [Connecter des utilisateurs (B2C)](https://github.com/Azure-Samples/ms-identity-blazor-wasm/blob/main/WebApp-OIDC/B2C/README.md)<br/>&#8226; [Appeler Microsoft Graph](https://github.com/Azure-Samples/ms-identity-blazor-wasm/blob/main/WebApp-graph-user/Call-MSGraph/README.md)<br/>&#8226; [Déployer sur Azure App Service](https://github.com/Azure-Samples/ms-identity-blazor-wasm/blob/main/Deploy-to-Azure/README.md) | MSAL.js | Code d’autorisation avec PKCE |
> | JavaScript | &#8226; [Connecter des utilisateurs](https://github.com/Azure-Samples/ms-identity-javascript-tutorial/tree/main/1-Authentication/1-sign-in/README.md)<br/>&#8226; [Connecter des utilisateurs (B2C)](https://github.com/Azure-Samples/ms-identity-javascript-tutorial/tree/main/1-Authentication/2-sign-in-b2c/README.md) <br/> &#8226; [Appeler Microsoft Graph](https://github.com/Azure-Samples/ms-identity-javascript-tutorial/tree/main/2-Authorization-I/1-call-graph/README.md)<br/>&#8226; [Appeler l’API web Node.js](https://github.com/Azure-Samples/ms-identity-javascript-tutorial/tree/main/3-Authorization-II/1-call-api/README.md)<br/>&#8226; [Appeler l’API web Node.js (B2C)](https://github.com/Azure-Samples/ms-identity-javascript-tutorial/tree/main/3-Authorization-II/2-call-api-b2c/README.md)<br/>&#8226; [Appeler Microsoft Graph via OBO](https://github.com/Azure-Samples/ms-identity-javascript-tutorial/tree/main/4-AdvancedGrants/1-call-api-graph/README.md)<br/>&#8226; [Appeler l’API web Node.js via OBO et l’accès conditionnel](https://github.com/Azure-Samples/ms-identity-javascript-tutorial/tree/main/4-AdvancedGrants/2-call-api-api-ca/README.md)<br/>&#8226; [Déployer sur Stockage Azure et App Service](https://github.com/Azure-Samples/ms-identity-javascript-tutorial/tree/main/5-Deployment/README.md)| MSAL.js | &#8226; Code d’autorisation avec PKCE<br/>&#8226; On-Behalf-Of (OBO) <br/>&#8226; Accès conditionnel |
> | React | &#8226; [Connecter des utilisateurs](https://github.com/Azure-Samples/ms-identity-javascript-react-tutorial/tree/main/1-Authentication/1-sign-in/README.md)<br/>&#8226; [Connecter des utilisateurs (B2C)](https://github.com/Azure-Samples/ms-identity-javascript-react-tutorial/tree/main/1-Authentication/2-sign-in-b2c/README.md) <br/> &#8226; [Appeler Microsoft Graph](https://github.com/Azure-Samples/ms-identity-javascript-react-tutorial/tree/main/2-Authorization-I/1-call-graph/README.md)<br/>&#8226; [Appeler l’API web Node.js](https://github.com/Azure-Samples/ms-identity-javascript-react-tutorial/tree/main/3-Authorization-II/1-call-api)<br/>&#8226; [Appeler l’API web Node.js (B2C)](https://github.com/Azure-Samples/ms-identity-javascript-react-tutorial/tree/main/3-Authorization-II/2-call-api-b2c)<br/>&#8226; [Appeler Microsoft Graph via OBO](https://github.com/Azure-Samples/ms-identity-javascript-react-tutorial/tree/main/6-AdvancedScenarios/1-call-api-obo/README.md)<br/>&#8226; [Appeler l’API web Node.js en utilisant PoP](https://github.com/Azure-Samples/ms-identity-javascript-react-tutorial/tree/main/6-AdvancedScenarios/2-call-api-pop/README.md)<br/>&#8226; [Utiliser des rôles d’application pour le contrôle d’accès](https://github.com/Azure-Samples/ms-identity-javascript-react-tutorial/tree/main/5-AccessControl/1-call-api-roles/README.md)<br/>&#8226; [Utiliser des groupes de sécurité pour le contrôle d’accès](https://github.com/Azure-Samples/ms-identity-javascript-react-tutorial/tree/main/5-AccessControl/2-call-api-groups/README.md)<br/>&#8226; [Déployer sur Stockage Azure et App Service](https://github.com/Azure-Samples/ms-identity-javascript-react-tutorial/tree/main/4-Deployment/1-deploy-storage/README.md)<br/>&#8226; [Déployer sur Azure Static Web Apps](https://github.com/Azure-Samples/ms-identity-javascript-react-tutorial/tree/main/4-Deployment/2-deploy-static/README.md)| MSAL React | &#8226; Code d’autorisation avec PKCE<br/>&#8226; On-Behalf-Of (OBO) <br/>&#8226; Accès conditionnel<br/>&#8226; Preuve de possession (PoP) |

## <a name="web-applications"></a>Applications web

Les exemples suivants illustrent des applications web qui connectent les utilisateurs. Certains exemples montrent également l’application qui appelle Microsoft Graph ou votre propre API web avec l’identité de l’utilisateur.

> [!div class="mx-tdCol2BreakAll"]
> | Langage/<br/>Plateforme | Exemple(s) de code<br/> sur GitHub | Auth<br/> libraries | Flux d’authentification |
> | ------- | --------------------------- | ------------- | -------------- |
> | ASP.NET Core| Série ASP.NET Core <br/> &#8226; [Connecter des utilisateurs](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/1-WebApp-OIDC/README.md) <br/> &#8226; [Connecter des utilisateurs (B2C)](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/1-WebApp-OIDC/1-5-B2C/README.md) <br/> &#8226; [Appeler Microsoft Graph](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/2-WebApp-graph-user/2-1-Call-MSGraph/README.md) <br/> &#8226; [Personnaliser le cache de jeton](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/2-WebApp-graph-user/2-2-TokenCache/README.md) <br/> &#8226; [Appeler Graph (multilocataire)](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/2-WebApp-graph-user/2-3-Multi-Tenant/README.md) <br/> &#8226; [Appeler des API REST Azure](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/3-WebApp-multi-APIs/README.md) <br/> &#8226; [Protéger des API web](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/4-WebApp-your-API/4-1-MyOrg/README.md) <br/> &#8226; [Protéger des API web (B2C)](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/4-WebApp-your-API/4-2-B2C/README.md) <br/> &#8226; [Protéger une API web multilocataire](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/4-WebApp-your-API/4-3-AnyOrg/Readme.md) <br/> &#8226; [Utiliser des rôles d’application pour le contrôle d’accès](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/5-WebApp-AuthZ/5-1-Roles/README.md) <br/> &#8226; [Utiliser des groupes de sécurité pour le contrôle d’accès](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/5-WebApp-AuthZ/5-2-Groups/README.md) <br/> &#8226; [Déployer sur Stockage Azure et App Service](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/6-Deploy-to-Azure/README.md) | &#8226; MSAL.NET<br/> &#8226; Microsoft.Identity.Web | &#8226; OpenID Connect <br/> &#8226; Code d’autorisation <br/> &#8226; On-Behalf-Of|
> | Blazor | Série Blazor Server <br/> &#8226; [Connecter des utilisateurs](https://github.com/Azure-Samples/ms-identity-blazor-server/tree/main/WebApp-OIDC/MyOrg) <br/> &#8226; [Connecter des utilisateurs (B2C)](https://github.com/Azure-Samples/ms-identity-blazor-server/tree/main/WebApp-OIDC/B2C) <br/> &#8226; [Appeler Microsoft Graph](https://github.com/Azure-Samples/ms-identity-blazor-server/tree/main/WebApp-graph-user/Call-MSGraph) <br/> &#8226; [Appeler une API web](https://github.com/Azure-Samples/ms-identity-blazor-server/tree/main/WebApp-your-API/MyOrg) <br/> &#8226; [Appeler une API web (B2C)](https://github.com/Azure-Samples/ms-identity-blazor-server/tree/main/WebApp-your-API/B2C) | MSAL.NET | Code d’autorisation avec PKCE|
> | ASP.NET Core|[Scénarios avancés de mise en cache des jetons](https://github.com/Azure-Samples/ms-identity-dotnet-advanced-token-cache) | &#8226; MSAL.NET <br/> &#8226; Microsoft.Identity.Web | On-Behalf-Of (OBO) |
> | ASP.NET Core|[Utiliser le contexte d’authentification de l’accès conditionnel pour effectuer une authentification par étapes](https://github.com/Azure-Samples/ms-identity-dotnetcore-ca-auth-context-app/blob/main/README.md) | &#8226; MSAL.NET <br/> &#8226; Microsoft.Identity.Web | Code d’autorisation. |
> | ASP.NET Core|[Migrer d’Active Directory FS vers Azure AD](https://github.com/Azure-Samples/ms-identity-dotnet-adfs-to-aad) | MSAL.NET | &#8226; SAML <br/> &#8226; OpenID Connect |
> | ASP.NET | &#8226; [Exemple de formation Microsoft Graph](https://github.com/microsoftgraph/msgraph-training-aspnetmvcapp) <br/> &#8226; [Connecter des utilisateurs et appeler Microsoft Graph](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect) <br/> &#8226; [Connecter des utilisateurs et appeler Microsoft Graph avec une étendue limitée par l’administrateur](https://github.com/azure-samples/active-directory-dotnet-admin-restricted-scopes-v2) <br/> &#8226; [Démarrage rapide : Connecter des utilisateurs](https://github.com/AzureAdQuickstarts/AppModelv2-WebApp-OpenIDConnect-DotNet) | MSAL.NET | &#8226; OpenID Connect <br/> &#8226; Code d’autorisation |
> | Java </p> Spring |Série Azure AD Spring Boot Starter <br/> &#8226; [Connecter des utilisateurs](https://github.com/Azure-Samples/ms-identity-java-spring-tutorial/tree/main/1-Authentication/sign-in) <br/> &#8226; [Connecter des utilisateurs (B2C)](https://github.com/Azure-Samples/ms-identity-java-spring-tutorial/tree/main/1-Authentication/sign-in-b2c) <br/> &#8226; [Appeler Microsoft Graph](https://github.com/Azure-Samples/ms-identity-java-spring-tutorial/tree/main/2-Authorization-I/call-graph) <br/> &#8226; [Utiliser des rôles d’application pour le contrôle d’accès](https://github.com/Azure-Samples/ms-identity-java-spring-tutorial/tree/main/3-Authorization-II/roles) <br/> &#8226; [Utiliser des groupes pour le contrôle d’accès](https://github.com/Azure-Samples/ms-identity-java-spring-tutorial/tree/main/3-Authorization-II/groups) <br/> &#8226; [Déployer sur Azure App Service](https://github.com/Azure-Samples/ms-identity-java-spring-tutorial/tree/main/4-Deployment/deploy-to-azure-app-service) | &#8226; MSAL Java <br/> &#8226; Azure AD Boot Starter | Code d’autorisation. |
> | Java </p> Servlets | Série Spring-less Servlet <br/> &#8226; [Connecter des utilisateurs](https://github.com/Azure-Samples/ms-identity-java-servlet-webapp-authentication/tree/main/1-Authentication/sign-in) <br/> &#8226; [Connecter des utilisateurs (B2C)](https://github.com/Azure-Samples/ms-identity-java-servlet-webapp-authentication/tree/main/1-Authentication/sign-in-b2c) <br/> &#8226; [Appeler Microsoft Graph](https://github.com/Azure-Samples/ms-identity-java-servlet-webapp-authentication/tree/main/2-Authorization-I/call-graph) <br/> &#8226; [Utiliser des rôles d’application pour le contrôle d’accès](https://github.com/Azure-Samples/ms-identity-java-servlet-webapp-authentication/tree/main/3-Authorization-II/roles) <br/> &#8226; [Utiliser des groupes de sécurité pour le contrôle d’accès](https://github.com/Azure-Samples/ms-identity-java-servlet-webapp-authentication/tree/main/3-Authorization-II/groups) <br/> &#8226; [Déployer sur Azure App Service](https://github.com/Azure-Samples/ms-identity-java-servlet-webapp-authentication/tree/main/4-Deployment/deploy-to-azure-app-service) | MSAL Java | Code d’autorisation. |
> | Java | [Connecter des utilisateurs et appeler Microsoft Graph](https://github.com/Azure-Samples/ms-identity-java-webapp)| MSAL Java | Code d’autorisation. |
> | Java </p> Spring| Connecter des utilisateurs et appeler Microsoft Graph via OBO </p> &#8226; [API web](https://github.com/Azure-Samples/ms-identity-java-webapi) | MSAL Java | &#8226; Code d’autorisation <br/> &#8226; On-Behalf-Of (OBO) |
> | Node.js </p> Express | Série Application web Express <br/> &#8226; [Connecter des utilisateurs](https://github.com/Azure-Samples/ms-identity-javascript-nodejs-tutorial/blob/main/1-Authentication/1-sign-in/README.md)<br/> &#8226; [Connecter des utilisateurs (B2C)](https://github.com/Azure-Samples/ms-identity-javascript-nodejs-tutorial/blob/main/1-Authentication/2-sign-in-b2c/README.md)<br/> &#8226; [Appeler Microsoft Graph](https://github.com/Azure-Samples/ms-identity-javascript-nodejs-tutorial/blob/main/2-Authorization/1-call-graph/README.md)<br/> &#8226; [Déployer sur Azure App Service](https://github.com/Azure-Samples/ms-identity-javascript-nodejs-tutorial/blob/main/3-Deployment/README.md)<br/> &#8226; [Utiliser des rôles d’application pour le contrôle d’accès](https://github.com/Azure-Samples/ms-identity-javascript-nodejs-tutorial/blob/main/4-AccessControl/1-app-roles/README.md)<br/> &#8226; [Utiliser des groupes de sécurité pour le contrôle d’accès](https://github.com/Azure-Samples/ms-identity-javascript-nodejs-tutorial/blob/main/4-AccessControl/2-security-groups/README.md) <br/> &#8226; [Applications web qui connectent les utilisateurs](https://github.com/Azure-Samples/ms-identity-node) | MSAL Node | Code d’autorisation. |
> | Python </p> Flask | Série Flask <br/> &#8226; [Connecter des utilisateurs](https://github.com/Azure-Samples/ms-identity-python-flask-tutorial) <br/> &#8226; [Connecter des utilisateurs (B2C)](https://github.com/Azure-Samples/ms-identity-python-flask-tutorial) <br/>&#8226; [Connecter des utilisateurs et appeler Microsoft Graph](https://github.com/Azure-Samples/ms-identity-python-webapp) <br/> &#8226; [Appeler Microsoft Graph](https://github.com/Azure-Samples/ms-identity-python-flask-tutorial) <br/> &#8226; [Déployer sur Azure App Service](https://github.com/Azure-Samples/ms-identity-python-flask-tutorial) | MSAL Python | Code d’autorisation. |
> | Python </p> Django | Série Django <br/> &#8226; [Connecter des utilisateurs](https://github.com/Azure-Samples/ms-identity-python-django-tutorial/tree/main/1-Authentication/sign-in) <br/> &#8226; [Connecter des utilisateurs (B2C)](https://github.com/Azure-Samples/ms-identity-python-django-tutorial/tree/main/1-Authentication/sign-in-b2c) <br/> &#8226; [Appeler Microsoft Graph](https://github.com/Azure-Samples/ms-identity-python-django-tutorial/tree/main/2-Authorization-I/call-graph) <br/> &#8226; [Déployer sur Azure App Service](https://github.com/Azure-Samples/ms-identity-python-django-tutorial/tree/main/3-Deployment/deploy-to-azure-app-service)| MSAL Python | Code d’autorisation. |
> | Ruby | Formation Graph <br/> &#8226; [Connecter des utilisateurs et appeler Microsoft Graph](https://github.com/microsoftgraph/msgraph-training-rubyrailsapp) | OmniAuth OAuth2 | Code d’autorisation. |

## <a name="web-api"></a>API Web

Les exemples suivants montrent comment protéger une API web avec la plateforme d’identité Microsoft et comment appeler une API en aval à partir de l’API web.

> [!div class="mx-tdCol2BreakAll"]
> | Langage/<br/>Plateforme | Exemple(s) de code <br/> sur GitHub |Auth<br/> libraries |Flux d’authentification |
> | ----------- | ----------- |----------- |----------- |
> | ASP.NET | [Appeler Microsoft Graph](https://github.com/Azure-Samples/ms-identity-aspnet-webapi-onbehalfof) | MSAL.NET | On-Behalf-Of (OBO) |
> | ASP.NET Core | [Connecter des utilisateurs et appeler Microsoft Graph](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/tree/master/2.%20Web%20API%20now%20calls%20Microsoft%20Graph) | MSAL.NET | On-Behalf-Of (OBO) |
> | Java | [Connexion des utilisateurs](https://github.com/Azure-Samples/ms-identity-java-webapi) | MSAL Java | On-Behalf-Of (OBO) |
> | Node.js | &#8226; [Protéger une API web Node.js](https://github.com/Azure-Samples/active-directory-javascript-nodejs-webapi-v2) <br/> &#8226; [Protéger une API web Node.js avec Azure AD B2C](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi) | MSAL Node | Porteur des autorisations |

## <a name="desktop"></a>Bureau

Les exemples suivants illustrent des applications de bureau clientes publiques qui accèdent à l’API Microsoft Graph ou à votre API web pour le compte d’un utilisateur. En plus de l’exemple _Bureau (console) avec WAM (Workspace Application Manager)_ , toutes ces applications clientes utilisent la bibliothèque d’authentification Microsoft (MSAL).

> [!div class="mx-tdCol2BreakAll"]
> | Langage/<br/>Plateforme | Exemple(s) de code <br/> sur GitHub | Auth<br/> libraries | Flux d’authentification |  
> | ------- | -------- | ------------- | -------------- |
> | .NET Core | &#8226; [Appeler Microsoft Graph](https://github.com/Azure-Samples/ms-identity-dotnet-desktop-tutorial/tree/master/1-Calling-MSGraph/1-1-AzureAD) <br/> &#8226; [Appeler Microsoft Graph avec le cache de jeton](https://github.com/Azure-Samples/ms-identity-dotnet-desktop-tutorial/tree/master/2-TokenCache) <br/> &#8226; [Appeler Microsoft Graph avec l’interface utilisateur web personnalisée HTML](https://github.com/Azure-Samples/ms-identity-dotnet-desktop-tutorial/tree/master/3-CustomWebUI/3-1-CustomHTML) <br/> &#8226; [Appeler Microsoft Graph avec un navigateur web personnalisé](https://github.com/Azure-Samples/ms-identity-dotnet-desktop-tutorial/tree/master/3-CustomWebUI/3-2-CustomBrowser) <br/> &#8226; [Connecter des utilisateurs avec le flux de code d’appareil](https://github.com/Azure-Samples/ms-identity-dotnet-desktop-tutorial/tree/master/4-DeviceCodeFlow) | MSAL.NET |&#8226; Code d’autorisation avec PKCE <br/> &#8226; Code d’appareil |
> | .NET | &#8226; [Appeler Microsoft Graph avec la console démon](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/tree/master/1-Call-MSGraph) <br/> &#8226; [Appeler l’API web avec la console démon](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/master/2-Call-OwnApi/README.md) | MSAL.NET | Code d’autorisation avec PKCE |
> | .NET | [Appeler l’API protégée avec l’authentification Windows intégrée](https://github.com/azure-samples/active-directory-dotnet-iwa-v2) | MSAL.NET | Authentification Windows intégrée |
> | Java | [Appeler Microsoft Graph](https://github.com/Azure-Samples/ms-identity-java-desktop/) | MSAL Java | Authentification Windows intégrée |
> | Node.js | [Connexion des utilisateurs](https://github.com/Azure-Samples/ms-identity-javascript-nodejs-desktop) | MSAL Node | Code d’autorisation avec PKCE |
> | PowerShell | [Appeler Microsoft Graph en connectant les utilisateurs avec un nom d’utilisateur/mot de passe](https://github.com/azure-samples/active-directory-dotnetcore-console-up-v2) | MSAL.NET | Informations d’identification de mot de passe du propriétaire de ressource |
> | Python | [Connexion des utilisateurs](https://github.com/Azure-Samples/ms-identity-python-desktop) | MSAL Python | Code d’autorisation avec PKCE |
> | Plateforme Windows universelle (UWP) | [Appeler Microsoft Graph](https://github.com/azure-samples/active-directory-dotnet-native-uwp-wam) | API du gestionnaire de comptes web | Authentification Windows intégrée |
> | Windows Presentation Foundation (WPF) | [Connecter des utilisateurs et appeler Microsoft Graph](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/tree/master/2.%20Web%20API%20now%20calls%20Microsoft%20Graph) | MSAL.NET | Code d’autorisation avec PKCE |
> | XAML | &#8226; [Connecter des utilisateurs et appeler l’API web ASP.NET Core](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/tree/master/1.%20Desktop%20app%20calls%20Web%20API) <br/> &#8226; [Connecter des utilisateurs et appeler Microsoft Graph](https://github.com/azure-samples/active-directory-dotnet-desktop-msgraph-v2) | MSAL.NET | Code d’autorisation avec PKCE |

## <a name="mobile"></a>Mobile

Les exemples suivants illustrent des applications mobiles clientes publiques qui accèdent à l’API Microsoft Graph ou à votre API web pour le compte d’un utilisateur. Ces applications clientes utilisent la bibliothèque d’authentification Microsoft (MSAL).

> [!div class="mx-tdCol2BreakAll"]
> | Langage/<br/>Plateforme | Exemple(s) de code <br/> sur GitHub |Auth<br/> libraries |Flux d’authentification |
> | ----------- | ----------- |----------- |----------- |
> | iOS | &#8226; [Appeler l’application Microsoft Graph native](https://github.com/Azure-Samples/ms-identity-mobile-apple-swift-objc) <br/> &#8226; [Appeler Microsoft Graph avec Azure AD nxoauth](https://github.com/azure-samples/active-directory-ios-native-nxoauth2-v2) | MSAL iOS | Code d’autorisation avec PKCE |
> | Java | [Connecter des utilisateurs et appeler Microsoft Graph](https://github.com/Azure-Samples/ms-identity-android-java) | MSAL pour Android | Code d’autorisation avec PKCE |
> | Kotlin | [Connecter des utilisateurs et appeler Microsoft Graph](https://github.com/Azure-Samples/ms-identity-android-kotlin) | MSAL pour Android | Code d’autorisation avec PKCE |
> | Xamarin | &#8226; [Connecter des utilisateurs et appeler Microsoft Graph](https://github.com/Azure-Samples/active-directory-xamarin-native-v2/tree/main/1-Basic) <br/>&#8226; [Connecter des utilisateurs avec Broker et appeler Microsoft Graph](https://github.com/Azure-Samples/active-directory-xamarin-native-v2/tree/main/2-With-broker) | MSAL.NET | Code d’autorisation avec PKCE |

## <a name="service--daemon"></a>Service/démon

L’exemple suivant présente une application qui accède à l’API Microsoft Graph avec sa propre identité (sans utilisateur).

> [!div class="mx-tdCol2BreakAll"]
> | Langage/<br/>Plateforme | Exemple(s) de code <br/> sur GitHub |Auth<br/> libraries |Flux d’authentification |
> | ----------- | ----------- |----------- |----------- |
> | ASP.NET| &#8226; [Appeler Microsoft Graph](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/tree/master/1-Call-MSGraph) <br/> &#8226; [Appeler une API web](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/tree/master/2-Call-OwnApi)<br/> &#8226; [Appeler votre API web](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/tree/master/4-Call-OwnApi-Pop) <br/> &#8226; [Utilisation d’une identité managée et d’un coffre de clés Azure](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/tree/master/3-Using-KeyVault) <br/> &#8226; [Application à plusieurs locataires avec point de terminaison de plateforme d’identités Microsoft](https://github.com/Azure-Samples/ms-identity-aspnet-daemon-webapp) | MSAL.NET | Octroi des identifiants du client|
> | Java | [Appeler Microsoft Graph](https://github.com/Azure-Samples/ms-identity-java-daemon)| MSAL Java| Octroi des identifiants du client|
> | Node.js | [Connecter des utilisateurs et appeler l’API web](https://github.com/Azure-Samples/ms-identity-javascript-nodejs-console) | MSAL Node | Octroi des identifiants du client |
> | Python | &#8226; [Appeler Microsoft Graph avec un secret](https://github.com/Azure-Samples/ms-identity-python-daemon/tree/master/1-Call-MsGraph-WithSecret) <br/> &#8226; [Appeler Microsoft Graph avec un certificat](https://github.com/Azure-Samples/ms-identity-python-daemon/tree/master/2-Call-MsGraph-WithCertificate) | MSAL Python| Octroi des identifiants du client|

## <a name="azure-functions-as-web-apis"></a>Fonctions Azure en tant qu’API web

Les exemples suivants montrent comment protéger une fonction Azure en utilisant HttpTrigger et en exposant une API web avec la plateforme d’identité Microsoft et comment appeler une API en aval à partir de l’API web.

> [!div class="mx-tdCol2BreakAll"]
> | Langage/<br/>Plateforme | Exemple(s) de code <br/> sur GitHub |Auth<br/> libraries |Flux d’authentification |
> | ----------- | ----------- |----------- |----------- |
> | .NET | [API web de fonction Azure .NET sécurisée par Azure AD](https://github.com/Azure-Samples/ms-identity-dotnet-webapi-azurefunctions) | MSAL.NET | Code d’autorisation. |
> | Node.js | [API web de fonction Azure Node.js sécurisée par Azure AD](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-azurefunctions) | MSAL Node | Porteur des autorisations |
> | Node.js | [Appeler l’API Microsoft Graph pour le compte d’un utilisateur](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-onbehalfof-azurefunctions) | MSAL Node| On-Behalf-Of (OBO)|
> | Python | [API web de fonction Azure Python sécurisée par Azure AD](https://github.com/Azure-Samples/ms-identity-python-webapi-azurefunctions) | MSAL Python | Code d’autorisation. |

## <a name="headless"></a>Avec administration à distance

L’exemple suivant présente une application cliente publique qui s’exécute sur un appareil sans navigateur web. Cette application peut être un outil en ligne de commande ou une application qui s’exécute sur un système Linux ou Mac, ou encore une application IoT. Cet exemple présente une application accédant à l’API Graph au nom d’un utilisateur qui se connecte de manière interactive sur un autre appareil (par exemple, un téléphone mobile). Cette application cliente utilise la bibliothèque d’authentification Microsoft (MSAL).

> [!div class="mx-tdCol2BreakAll"]
> | Langage/<br/>Plateforme | Exemple(s) de code <br/> sur GitHub |Auth<br/> libraries |Flux d’authentification |
> | ----------- | ----------- |----------- |----------- |
> | .NET Core | [Appeler l’API protégée à partir d’un appareil de texte uniquement](https://github.com/azure-samples/active-directory-dotnetcore-devicecodeflow-v2) | MSAL.NET | Code d’appareil|
> | Java | [Connecter des utilisateurs et appeler l’API protégée](https://github.com/Azure-Samples/ms-identity-java-devicecodeflow) | MSAL Java | Code d’appareil |
> | Python | [Appeler Microsoft Graph](https://github.com/Azure-Samples/ms-identity-python-devicecodeflow) | MSAL Python | Code d’appareil |

## <a name="multi-tenant-saas"></a>SaaS mutualisé

Les exemples suivants montrent comment configurer votre application de façon à lui faire accepter les connexions de n’importe quel locataire Azure Active Directory (Azure AD). Configurer votre application en _multilocataire_ signifie que vous pouvez proposer une application **SaaS** (Software as a Service) à de nombreuses organisations, ce qui permet à leurs utilisateurs de se connecter à votre application après l’octroi du consentement.

> [!div class="mx-tdCol2BreakAll"]
> | Langage/<br/>Plateforme | Exemple(s) de code <br/> sur GitHub |Auth<br/> libraries |Flux d’authentification |
> | ----------- | ----------- |----------- |----------- |
> | ASP.NET Core | [L’application web ASP.NET Core MVC appelle l’API Microsoft Graph](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-3-Multi-Tenant) | MSAL.NET | OpenID Connect |
> | ASP.NET Core | [Application web ASP.NET Core MVC appelant l’API Web ASP.NET Core](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/4-WebApp-your-API/4-3-AnyOrg) | MSAL.NET | Code d’autorisation. |

## <a name="next-steps"></a>Étapes suivantes

Si vous souhaitez explorer plus en détail d’autres exemples de code, consultez :

- [Connecter des utilisateurs et appeler l’API Microsoft Graph à partir d’une application Angular](tutorial-v2-angular-auth-code.md)
- [Connecter des utilisateurs dans une application web Node.js et Express](tutorial-v2-nodejs-webapp-msal.md)
- [Appeler l’API Microsoft Graph à partir d’une plateforme Windows universelle](tutorial-v2-windows-uwp.md)
