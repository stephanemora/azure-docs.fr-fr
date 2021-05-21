---
title: Exemples de code pour la plateforme d’identité Microsoft
description: Fournit un index des exemples de code disponibles pour la plateforme d’identité Microsoft, organisés par scénario.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: sample
ms.workload: identity
ms.date: 11/04/2020
ms.author: marsma
ms.reviewer: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: bbc068db59bb0a5cd4ef334d3e8ce0a63f74079d
ms.sourcegitcommit: 1b19b8d303b3abe4d4d08bfde0fee441159771e1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/11/2021
ms.locfileid: "109752658"
---
# <a name="microsoft-identity-platform-code-samples-v20-endpoint"></a>Exemples de code de la plateforme d’identité Microsoft (point de terminaison v2.0)

Vous pouvez utiliser la plateforme d’identité Microsoft pour :

- Ajouter l’authentification et l’autorisation à vos applications web et API web.
- Exiger un jeton d’accès pour accéder à une API web protégée.

Cet article fournit une brève description et des liens vers des exemples pour la plateforme d’identité Microsoft. Ces exemples vous montrent comment procéder, et fournissent des extraits de code que vous pouvez utiliser dans vos applications. Dans la page d’exemples de code, vous trouverez des rubriques Lisez-moi détaillées, qui offrent une aide relative à la configuration requise, à l’installation et à la configuration. Les commentaires figurant dans le code sont là pour vous aider à comprendre les sections critiques.

Pour comprendre le scénario de base pour chaque type d’exemple, consultez [Types d’applications pour la plateforme d’identité Microsoft](v2-app-types.md).

Vous pouvez également contribuer aux exemples sur GitHub. Pour en savoir plus, consultez [Documentation et exemples Microsoft Azure Active Directory](https://github.com/Azure-Samples?page=3&query=active-directory).

## <a name="single-page-applications"></a>Applications monopages

Ces exemples montrent comment écrire une application monopage sécurisée avec la plateforme d’identité Microsoft. Ils utilisent l’une des saveurs disponibles de MSAL.js.

> [!div class="mx-tdCol2BreakAll"]
> | Langage/<br/>Plateforme | Exemple de code | Description | Bibliothèques d’authentification | Flux d’authentification |  
> | ------- | -------- | --------------------------- | ------------- | -------------- |  
> |Angular|[Dépôt GitHub](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa)| &#8226; Connecte les utilisateurs avec AAD <br/>&#8226; Appelle Microsoft Graph | MSAL Angular | Flux de code d’authentification (avec PKCE) |
> | Angular | [Dépôt GitHub](https://github.com/Azure-Samples/ms-identity-javascript-angular-tutorial) | &#8226; [Connecte les utilisateurs](https://github.com/Azure-Samples/ms-identity-javascript-angular-tutorial/blob/main/1-Authentication/1-sign-in/README.md)<br/>&#8226; [Connecte les utilisateurs (B2C)](https://github.com/Azure-Samples/ms-identity-javascript-angular-tutorial/blob/main/1-Authentication/2-sign-in-b2c/README.md) <br/> &#8226; [Appelle Microsoft Graph](https://github.com/Azure-Samples/ms-identity-javascript-angular-tutorial/blob/main/2-Authorization-I/1-call-graph/README.md)<br/>&#8226; [Appelle l’API web .NET Core](https://github.com/Azure-Samples/ms-identity-javascript-angular-tutorial/tree/main/3-Authorization-II/1-call-api)<br/>&#8226; [Appelle l’API web .NET Core (B2C)](https://github.com/Azure-Samples/ms-identity-javascript-angular-tutorial/tree/main/3-Authorization-II/2-call-api-b2c)<br/>&#8226; [Appelle Microsoft Graph via OBO](https://github.com/Azure-Samples/ms-identity-javascript-tutorial/blob/main/4-AdvancedGrants/1-call-api-graph/README.md)<br/>&#8226; [Appelle l’API web .NET Core en utilisant PoP](https://github.com/Azure-Samples/ms-identity-javascript-tutorial/blob/main/4-AdvancedGrants/2-call-api-api-ca/README.md)<br/>&#8226; [Utilise des rôles d’application pour le contrôle d’accès](https://github.com/Azure-Samples/ms-identity-javascript-angular-tutorial/blob/main/5-AccessControl/1-call-api-roles/README.md)<br/>&#8226; [Utilise des groupes de sécurité pour le contrôle d’accès](https://github.com/Azure-Samples/ms-identity-javascript-angular-tutorial/blob/main/5-AccessControl/2-call-api-groups/README.md)<br/>&#8226; [Se déploie sur Stockage Azure et App Service](https://github.com/Azure-Samples/ms-identity-javascript-angular-tutorial/blob/main/4-Deployment/README.md)| MSAL Angular | &#8226; Flux de code d’authentification (avec PKCE)<br/>&#8226; Flux On-Behalf-Of (OBO)<br/>&#8226; Preuve de possession (PoP)|
> | WebAssembly Blazor | [Dépôt GitHub](https://github.com/Azure-Samples/ms-identity-blazor-wasm) | &#8226; Connecte les utilisateurs<br/>&#8226; Appelle Microsoft Graph | MSAL.js | Flux de code d’authentification (avec PKCE) |
> | JavaScript | [Dépôt GitHub](https://github.com/Azure-Samples/ms-identity-javascript-v2) | &#8226; Connecte les utilisateurs<br/>&#8226; Appelle Microsoft Graph | MSAL.js | Flux de code d’authentification (avec PKCE) |
> | JavaScript | [Dépôt GitHub](https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa) | &#8226; Connecte les utilisateurs (B2C)<br/>&#8226; Appelle l’API web Node.js |  MSAL.js | Flux de code d’authentification (avec PKCE) |
> | JavaScript | [Dépôt GitHub](https://github.com/Azure-Samples/ms-identity-javascript-tutorial) | &#8226; [Connecte les utilisateurs](https://github.com/Azure-Samples/ms-identity-javascript-tutorial/blob/main/1-Authentication/1-sign-in/README.md)<br/>&#8226; [Connecte les utilisateurs (B2C)](https://github.com/Azure-Samples/ms-identity-javascript-tutorial/blob/main/1-Authentication/2-sign-in-b2c/README.md) <br/> &#8226; [Appelle Microsoft Graph](https://github.com/Azure-Samples/ms-identity-javascript-tutorial/blob/main/2-Authorization-I/1-call-graph/README.md)<br/>&#8226; [Appelle l’API web Node.js](https://github.com/Azure-Samples/ms-identity-javascript-tutorial/blob/main/3-Authorization-II/1-call-api/README.md)<br/>&#8226; [Appelle l’API web Node.js (B2C)](https://github.com/Azure-Samples/ms-identity-javascript-tutorial/blob/main/3-Authorization-II/2-call-api-b2c/README.md)<br/>&#8226; [Appelle Microsoft Graph via OBO](https://github.com/Azure-Samples/ms-identity-javascript-tutorial/blob/main/4-AdvancedGrants/1-call-api-graph/README.md)<br/>&#8226; [Appelle l’API web Node.js via OBO et accès conditionnel](https://github.com/Azure-Samples/ms-identity-javascript-tutorial/blob/main/4-AdvancedGrants/2-call-api-api-ca/README.md)<br/>&#8226; [Se déploie sur Stockage Azure et App Service](https://github.com/Azure-Samples/ms-identity-javascript-tutorial/blob/main/5-Deployment/README.md)| MSAL.js | &#8226; Flux de code d’authentification (avec PKCE)<br/>&#8226; Flux On-Behalf-Of (OBO)<br/>&#8226; Accès conditionnel |
> | React | [Dépôt GitHub](https://github.com/Azure-Samples/ms-identity-javascript-react-spa) | &#8226; Connecte les utilisateurs<br/>&#8226; Appelle Microsoft Graph | MSAL React | Flux de code d’authentification (avec PKCE) |
> | React | [Dépôt GitHub](https://github.com/Azure-Samples/ms-identity-javascript-react-tutorial) | &#8226; [Connecte les utilisateurs](https://github.com/Azure-Samples/ms-identity-javascript-react-tutorial/blob/main/1-Authentication/1-sign-in/README.md)<br/>&#8226; [Connecte les utilisateurs (B2C)](https://github.com/Azure-Samples/ms-identity-javascript-react-tutorial/blob/main/1-Authentication/2-sign-in-b2c/README.md) <br/> &#8226; [Appelle Microsoft Graph](https://github.com/Azure-Samples/ms-identity-javascript-react-tutorial/blob/main/2-Authorization-I/1-call-graph/README.md)<br/>&#8226; [Appelle l’API web Node.js](https://github.com/Azure-Samples/ms-identity-javascript-react-tutorial/tree/main/3-Authorization-II/1-call-api)<br/>&#8226; [Appelle l’API web Node.js (B2C)](https://github.com/Azure-Samples/ms-identity-javascript-react-tutorial/tree/main/3-Authorization-II/2-call-api-b2c)<br/>&#8226; [Appelle Microsoft Graph via OBO](https://github.com/Azure-Samples/ms-identity-javascript-react-tutorial/blob/main/6-AdvancedScenarios/1-call-api-obo/README.md)<br/>&#8226; [Utilise des rôles d’application pour le contrôle d’accès](https://github.com/Azure-Samples/ms-identity-javascript-react-tutorial/blob/main/5-AccessControl/1-call-api-roles/README.md)<br/>&#8226; [Utilise des groupes de sécurité pour le contrôle d’accès](https://github.com/Azure-Samples/ms-identity-javascript-react-tutorial/blob/main/5-AccessControl/2-call-api-groups/README.md)<br/>&#8226; [Se déploie sur Stockage Azure et App Service](https://github.com/Azure-Samples/ms-identity-javascript-react-tutorial/blob/main/4-Deployment/1-deploy-storage/README.md)<br/>&#8226; [Se déploie sur Azure Static Web Apps](https://github.com/Azure-Samples/ms-identity-javascript-react-tutorial/blob/main/4-Deployment/2-deploy-static/README.md)| MSAL React | &#8226; Flux de code d’authentification (avec PKCE)<br/>&#8226; Flux On-Behalf-Of (OBO)<br/>&#8226; Accès conditionnel |

## <a name="web-applications"></a>Applications web

Les exemples suivants illustrent des applications web qui connectent les utilisateurs. Certains exemples montrent également l’application qui appelle Microsoft Graph ou votre propre API web avec l’identité de l’utilisateur.

| Plateforme | Connecte uniquement les utilisateurs | Connecte les utilisateurs et appelle Microsoft Graph |
| -------- | ------------------- | --------------------------------- |
| ![Cette illustration montre le logo ASP.NET Core](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core | [Didacticiel de connexion des utilisateurs par la WebApp ASP.NET Core](https://aka.ms/aspnetcore-webapp-sign-in) | Même exemple dans la phase où [l’application web ASP.NET Core appelle Microsoft Graph](https://aka.ms/aspnetcore-webapp-call-msgraph)</p>Exemple avancé [Accès au cache de jeton de l’utilisateur connecté à partir d’applications en arrière-plan, d’API et de services](https://github.com/Azure-Samples/ms-identity-dotnet-advanced-token-cache) |
| ![Cette image affiche le logo ASP.NET Framework](media/sample-v2-code/logo_NETframework.png)</p>ASP.NET Core | [Playbook de migration d’application AD FS vers Azure AD pour les développeurs](https://github.com/Azure-Samples/ms-identity-dotnet-adfs-to-aad) pour découvrir comment migrer de manière sécurisée vos applications intégrées à Active Directory Federation Services (AD FS) vers Azure Active Directory (Azure AD) | |
| ![Cette image affiche le logo ASP.NET Framework](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET | [Démarrage rapide avec ASP.NET](https://github.com/AzureAdQuickstarts/AppModelv2-WebApp-OpenIDConnect-DotNet) </p> [dotnet-webapp-openidconnect-v2](https://github.com/azure-samples/active-directory-dotnet-webapp-openidconnect-v2)  |  [dotnet-admin-restricted-scopes-v2](https://github.com/azure-samples/active-directory-dotnet-admin-restricted-scopes-v2) </p> [msgraph-training-aspnetmvcapp](https://github.com/microsoftgraph/msgraph-training-aspnetmvcapp) |
| ![Cette image affiche le logo Java](media/sample-v2-code/logo_java.png)  |[Tutoriel sur les servlets Java - Chapitre 1.1](https://github.com/Azure-Samples/ms-identity-java-servlet-webapp-authentication/blob/main/README.md) Se connecter avec AAD| |
| ![Cette image affiche le logo Java](media/sample-v2-code/logo_java.png)  |[Tutoriel sur les servlets Java - Chapitre 1.2](https://github.com/Azure-Samples/ms-identity-java-servlet-webapp-authentication/blob/main/README.md) Se connecter avec B2C |
| ![Cette image affiche le logo Java](media/sample-v2-code/logo_java.png)  | | [Tutoriel sur les servlets Java - Chapitre 2.1](https://github.com/Azure-Samples/ms-identity-java-servlet-webapp-authentication/blob/main/README.md) Se connecter avec AAD et appeler Graph|
| ![Cette image affiche le logo Java](media/sample-v2-code/logo_java.png)  |[Tutoriel sur les servlets Java - Chapitre 3.1](https://github.com/Azure-Samples/ms-identity-java-servlet-webapp-authentication/blob/main/README.md) Se connecter avec AAD et contrôler l’accès avec la revendication basée sur les rôles| |
| ![Cette image affiche le logo Java](media/sample-v2-code/logo_java.png)  | | [Tutoriel sur les servlets Java - Chapitre 3.2](https://github.com/Azure-Samples/ms-identity-java-servlet-webapp-authentication/blob/main/README.md) Se connecter avec AAD et contrôler l’accès avec la revendication basée sur les groupes|
| ![Cette image affiche le logo Java](media/sample-v2-code/logo_java.png) | |[Tutoriel sur les servlets Java - Chapitre 4.1](https://github.com/Azure-Samples/ms-identity-java-servlet-webapp-authentication/blob/main/README.md) Déployer sur Azure App Service|
| ![Cette image affiche le logo Java](media/sample-v2-code/logo_java.png)  | | [ms-identity-java-webapp](https://github.com/Azure-Samples/ms-identity-java-webapp) |
| ![Cette image affiche le logo Java](media/sample-v2-code/logo_java.png)  | [ms-identity-b2c-java-servlet-webapp-authentication](https://github.com/Azure-Samples/ms-identity-b2c-java-servlet-webapp-authentication)|  |
| ![Cette image affiche le logo Node.js](media/sample-v2-code/logo_nodejs.png)</p>Node.js (MSAL Node) | [Tutoriel sur la connexion des utilisateurs avec l’application web Express](https://github.com/Azure-Samples/ms-identity-node) | |
| ![Cette image affiche le logo Python](media/sample-v2-code/logo_python.png)  | [Tutoriel sur Python Flask - Chapitre 1.1](https://github.com/Azure-Samples/ms-identity-python-flask-tutorial/blob/main/README.md) Se connecter avec AAD  |  |
| ![Cette image affiche le logo Python](media/sample-v2-code/logo_python.png)  | [Tutoriel sur Python Flask - Chapitre 1.2](https://github.com/Azure-Samples/ms-identity-python-flask-tutorial/blob/main/README.md) Se connecter avec B2C                    |  |
| ![Cette image affiche le logo Python](media/sample-v2-code/logo_python.png)  | | [Tutoriel sur Python Flask - Chapitre 2.1](https://github.com/Azure-Samples/ms-identity-python-flask-tutorial/blob/main/README.md) Se connecter avec AAD et appeler Graph |
| ![Cette image affiche le logo Python](media/sample-v2-code/logo_python.png)  | |[Tutoriel sur Python Flask - Chapitre 3.1](https://github.com/Azure-Samples/ms-identity-python-flask-tutorial/blob/main/README.md) Déployer sur Azure App Service  |
| ![Cette image affiche le logo Python](media/sample-v2-code/logo_python.png)  | [Tutoriel sur Python Django - Chapitre 1.1](https://github.com/Azure-Samples/ms-identity-python-django-tutorial/blob/main/README.md) Se connecter avec AAD  | |
| ![Cette image affiche le logo Python](media/sample-v2-code/logo_python.png)  | [Tutoriel sur Python Django - Chapitre 1.2](https://github.com/Azure-Samples/ms-identity-python-django-tutorial/blob/main/README.md) Se connecter avec B2C                    |  |
| ![Cette image affiche le logo Python](media/sample-v2-code/logo_python.png)  | | [Tutoriel sur Python Django - Chapitre 2.1](https://github.com/Azure-Samples/ms-identity-python-django-tutorial/blob/main/README.md) Se connecter avec AAD et appeler Graph|
| ![Cette image affiche le logo Python](media/sample-v2-code/logo_python.png)  | | [Tutoriel sur Python Django - Chapitre 3.1](https://github.com/Azure-Samples/ms-identity-python-django-tutorial/blob/main/README.md) Déployer sur Azure App Service                    |
| ![Cette image affiche le logo Python](media/sample-v2-code/logo_python.png)  | | [Application web Python Flask](https://github.com/Azure-Samples/ms-identity-python-webapp) |
| ![Cette image affiche le logo Ruby](media/sample-v2-code/logo_ruby.png) |                   | [msgraph-training-rubyrailsapp](https://github.com/microsoftgraph/msgraph-training-rubyrailsapp) |
| ![Cette image montre le logo Blazor](media/sample-v2-code/logo-blazor.png)</p>Serveur Blazor | [Tutoriel sur la connexion des utilisateurs avec l’application Blazor Server](https://github.com/Azure-Samples/ms-identity-blazor-server/tree/main/WebApp-OIDC) | [L’application Blazor Server appelle Microsoft Graph](https://github.com/Azure-Samples/ms-identity-blazor-server/tree/main/WebApp-graph-user/Call-MSGraph)</p>Tutoriel par chapitre : [Application Blazor Server pour connecter les utilisateurs et appeler les API avec Azure Active Directory](https://github.com/Azure-Samples/ms-identity-blazor-server) |

## <a name="desktop-and-mobile-public-client-apps"></a>Applications clientes publiques mobiles et de bureau

Les exemples suivants illustrent des applications clientes publiques (applications de bureau ou mobiles) qui accèdent à l’API Graph ou à votre API web pour le compte d’un utilisateur. Outre l’exemple *Desktop (console) avec WAM*, toutes ces applications clientes utilisent la bibliothèque d’authentification Microsoft (MSAL).

| Application cliente | Plateforme | Flux/octroi | Appelle Microsoft Graph | Appelle une API web ASP.NET Core |
| ------------------ | -------- |  ----------| ---------- | ------------------------- |
| Tutoriel de bureau (.NET Core) - Utiliser éventuellement :</p>- le cache de jeton Implémente multiplateforme</p>- une interface web personnalisée | ![Cette image affiche le logo .NET/C#](media/sample-v2-code/logo_NETcore.png) | [Code d’autorisation](msal-authentication-flows.md#authorization-code)| [ms-identity-dotnet-desktop-tutorial](https://github.com/azure-samples/ms-identity-dotnet-desktop-tutorial) | |
| Bureau (WPF)      | ![Cette image affiche le logo de bureau .NET/C#](media/sample-v2-code/logo_NET.png) | [Code d’autorisation](msal-authentication-flows.md#authorization-code)| [dotnet-desktop-msgraph-v2](https://github.com/azure-samples/active-directory-dotnet-desktop-msgraph-v2) | [dotnet-native-aspnetcore-v2](https://aka.ms/msidentity-aspnetcore-webapi) |
| Bureau (Console)   | ![Image montrant le logo .NET/C# (bureau)](media/sample-v2-code/logo_NET.png) | [Authentification Windows intégrée](msal-authentication-flows.md#integrated-windows-authentication) | [dotnet-iwa-v2](https://github.com/azure-samples/active-directory-dotnet-iwa-v2) |  |
| Bureau (Console) | ![Image montrant le logo .NET/C# (bureau)](media/sample-v2-code/logo_NET.png) |  [Code d’autorisation](msal-authentication-flows.md#authorization-code) | [active-directory-dotnetcore-daemon-v2](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/tree/master/1-Call-MSGraph) |[active-directory-dotnetcore-daemon-v2](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/master/2-Call-OwnApi/README.md)  |
| Bureau (Console) <br> Utiliser des certificats à la place des secrets | ![Image montrant le logo .NET/C# (bureau)](media/sample-v2-code/logo_NET.png) |  [Code d’autorisation](msal-authentication-flows.md#authorization-code) | [active-directory-dotnetcore-daemon-v2](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/tree/master/1-Call-MSGraph#variation-daemon-application-using-client-credentials-with-certificates) |[active-directory-dotnetcore-daemon-v2](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/tree/master/2-Call-OwnApi#variation-daemon-application-using-client-credentials-with-certificates)  |
| Bureau (Console)   | ![Cette image affiche le logo Java](media/sample-v2-code/logo_java.png) | [Authentification Windows intégrée](msal-authentication-flows.md#integrated-windows-authentication) |[ms-identity-java-desktop](https://github.com/Azure-Samples/ms-identity-java-desktop/) |  |
| Bureau (Console)   | ![Voici le logo .NET/C# (bureau)](media/sample-v2-code/logo_NETcore.png) | [Nom d’utilisateur/mot de passe](msal-authentication-flows.md#usernamepassword) |[dotnetcore-up-v2](https://github.com/azure-samples/active-directory-dotnetcore-console-up-v2) |  |
| Bureau (console) avec WAM  | ![Voici le logo pour .NET/C# (bureau)](media/sample-v2-code/logo_NETcore.png) | Interactif avec [Gestionnaire de comptes web](/windows/uwp/security/web-account-manager) (WAM) |[dotnet-native-uwp-wam](https://github.com/azure-samples/active-directory-dotnet-native-uwp-wam) |  |
| Bureau (Console)   | ![Cette image affiche le logo Java](media/sample-v2-code/logo_java.png) | [Nom d’utilisateur/mot de passe](msal-authentication-flows.md#usernamepassword) |[ms-identity-java-desktop](https://github.com/Azure-Samples/ms-identity-java-desktop/) |  |
| Bureau (Console)   | ![Cette image affiche le logo Python](media/sample-v2-code/logo_python.png) | [Nom d’utilisateur/mot de passe](msal-authentication-flows.md#usernamepassword) |[ms-identity-python-desktop](https://github.com/Azure-Samples/ms-identity-python-desktop) |  |
| Bureau (Electron)   | ![Cette image affiche le logo Node.js](media/sample-v2-code/logo_nodejs.png)</p>Node.js (MSAL Node) | [Code d’autorisation (PKCE)](msal-authentication-flows.md#authorization-code) |[ms-identity-javascript-nodejs-desktop](https://github.com/Azure-Samples/ms-identity-javascript-nodejs-desktop) |  |
| Mobile (Android, iOS, UWP)   | ![Cette image affiche le logo .NET/C# (Xamarin)](media/sample-v2-code/logo_xamarin.png) | [Code d’autorisation](msal-authentication-flows.md#authorization-code) |[xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) |  |
| Mobile (iOS)       | ![Cette image affiche iOS/Objective-C ou Swift](media/sample-v2-code/logo_iOS.png) | [Code d’autorisation](msal-authentication-flows.md#authorization-code) |[ios-swift-objc-native-v2](https://github.com/azure-samples/active-directory-ios-swift-native-v2) </p> [ios-native-nxoauth2-v2](https://github.com/azure-samples/active-directory-ios-native-nxoauth2-v2) |  |
| Bureau (macOS)       | macOS | [Code d’autorisation](msal-authentication-flows.md#authorization-code) |[macOS-swift-objc-native-v2](https://github.com/Azure-Samples/ms-identity-macOS-swift-objc) |  |
| Mobile (Android-Java)   | ![Cette image affiche le logo Android](media/sample-v2-code/logo_Android.png) | [Code d’autorisation](msal-authentication-flows.md#authorization-code) |  [Android-Java](https://github.com/Azure-Samples/ms-identity-android-java) |  |
| Mobile (Android-Kotlin)   | ![Cette image affiche le logo Android](media/sample-v2-code/logo_Android.png) | [Code d’autorisation](msal-authentication-flows.md#authorization-code) |  [Android-Kotlin](https://github.com/Azure-Samples/ms-identity-android-kotlin) |  |

## <a name="daemon-applications"></a>Applications de démon

L’exemple suivant présente une application qui accède à l’API Microsoft Graph avec sa propre identité (sans utilisateur).

| Application cliente | Plateforme | Flux/Grant | Appelle Microsoft Graph |
| ------------------ | -------- | ---------- | -------------------- |
| Console | ![Cette image affiche le logo .NET Core](media/sample-v2-code/logo_NETcore.png)</p> ASP.NET  | [Informations d’identification du client](msal-authentication-flows.md#client-credentials) | [dotnetcore-daemon-v2](https://github.com/azure-samples/active-directory-dotnetcore-daemon-v2) |
| Application web | ![Capture d’écran montrant le logo ASP.NET.](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET  | [Informations d’identification du client](msal-authentication-flows.md#client-credentials) | [dotnet-daemon-v2](https://github.com/azure-samples/active-directory-dotnet-daemon-v2) |
| Console | ![Cette image affiche le logo Java](media/sample-v2-code/logo_java.png) | [Informations d’identification du client](msal-authentication-flows.md#client-credentials) | [ms-identity-java-daemon](https://github.com/Azure-Samples/ms-identity-java-daemon) |
| Console | ![Cette image affiche le logo Node.js](media/sample-v2-code/logo_nodejs.png)</p>Node.js (MSAL Node)| [Informations d’identification du client](msal-authentication-flows.md#client-credentials) | [ms-identity-javascript-nodejs-console](https://github.com/Azure-Samples/ms-identity-javascript-nodejs-console) |
| Console | ![Cette image affiche le logo Python](media/sample-v2-code/logo_python.png) | [Informations d’identification du client](msal-authentication-flows.md#client-credentials) | [ms-identity-python-daemon](https://github.com/Azure-Samples/ms-identity-python-daemon) |

## <a name="headless-applications"></a>Applications sans affichage

L’exemple suivant présente une application cliente publique qui s’exécute sur un appareil sans navigateur web. Cette application peut être un outil en ligne de commande ou une application qui s’exécute sur un système Linux ou Mac, ou encore une application IoT. Cet exemple présente une application accédant à l’API Graph au nom d’un utilisateur qui se connecte de manière interactive sur un autre appareil (par exemple, un téléphone mobile). Cette application cliente utilise la bibliothèque d’authentification Microsoft (MSAL).

| Application cliente | Plateforme | Flux/Grant | Appelle Microsoft Graph |
| ------------------ | -------- |  ----------| ---------- |
| Bureau (Console)   | ![Cette image affiche le logo .NET/C# (bureau)](media/sample-v2-code/logo_NETcore.png) | [Flux de code d’appareil](msal-authentication-flows.md#device-code) |[dotnetcore-devicecodeflow-v2](https://github.com/azure-samples/active-directory-dotnetcore-devicecodeflow-v2) |
| Bureau (Console)   | ![Cette image affiche le logo Java](media/sample-v2-code/logo_java.png) | [Flux de code d’appareil](msal-authentication-flows.md#device-code) |[ms-identity-java-devicecodeflow](https://github.com/Azure-Samples/ms-identity-java-devicecodeflow) |
| Bureau (Console)   | ![Cette image affiche le logo Python](media/sample-v2-code/logo_python.png) | [Flux de code d’appareil](msal-authentication-flows.md#device-code) |[ms-identity-python-devicecodeflow](https://github.com/Azure-Samples/ms-identity-python-devicecodeflow) |

## <a name="multi-tenant-saas-applications"></a>Applications SaaS multilocataires

Les exemples suivants montrent comment configurer votre application de façon à lui faire accepter les connexions de n’importe quel locataire Azure Active Directory (Azure AD). Configurer votre application en *multilocataire* signifie que vous pouvez proposer une application **SaaS** (Software as a Service) à de nombreuses organisations, ce qui permet à leurs utilisateurs de se connecter à votre application après l’octroi du consentement.

| Plateforme | Description | Lien |
| -------- | --------------------- | -------- |
| ![Cette image montre le logo Angular](media/sample-v2-code/logo_angular.png) [Angular (MSAL Angular 2.0)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular) | L’application monopage (SPA) multilocataire appelle une API web personnalisée multilocataire |[ms-identity-javascript-angular-spa-aspnet-webapi-multitenant](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-aspnet-webapi-multitenant/tree/master/Chapter2) |
| ![Cette image montre le logo ASP.NET Core](media/sample-v2-code/logo_NETcore.png) [.NET Core (MSAL.NET)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) | Application web ASP.NET Core MVC appelant l’API Graph |[active-directory-aspnetcore-webapp-openidconnect-v2](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-3-Multi-Tenant) |
| ![Cette image montre le logo ASP.NET Core](media/sample-v2-code/logo_NETcore.png) [.NET Core (MSAL.NET)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) | Application web ASP.NET Core MVC appelant l’API Web ASP.NET Core |[active-directory-aspnetcore-webapp-openidconnect-v2](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/4-WebApp-your-API/4-3-AnyOrg) |

## <a name="web-apis"></a>API Web

Les exemples suivants montrent comment protéger une API web avec la plateforme d’identité Microsoft et comment appeler une API en aval à partir de l’API web.

| Plateforme | Exemple |
| -------- | ------------------- |
| ![Cette illustration montre le logo ASP.NET Core](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core | API web ASP.NET Core (service) associée à [dotnet-native-aspnetcore-v2](https://aka.ms/msidentity-aspnetcore-webapi-calls-msgraph)  |
| ![Cette image affiche le logo ASP.NET](media/sample-v2-code/logo_NET.png)</p>ASP.NET MVC | API web (service) associée à [ms-identity-aspnet-webapi-onbehalfof](https://github.com/Azure-Samples/ms-identity-aspnet-webapi-onbehalfof) |
| ![Cette image affiche le logo Java](media/sample-v2-code/logo_java.png) | (service) API web de [ms-identity-java-webapi](https://github.com/Azure-Samples/ms-identity-java-webapi) |
| ![Cette image affiche le logo Node.js](media/sample-v2-code/logo_nodejs.png)</p>Node.js (Passport.js)| API web (service) de [active-directory-javascript-nodejs-webapi-v2](https://github.com/Azure-Samples/active-directory-javascript-nodejs-webapi-v2) |
| ![Cette image affiche le logo Node.js](media/sample-v2-code/logo_nodejs.png)</p>Node.js (Passport.js)| API web B2C (service) de [active-directory-b2c-javascript-nodejs-webapi](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi) |

## <a name="azure-functions-as-web-apis"></a>Fonctions Azure en tant qu’API web

Les exemples suivants montrent comment protéger une fonction Azure en utilisant HttpTrigger et en exposant une API web avec la plateforme d’identité Microsoft et comment appeler une API en aval à partir de l’API web.

| Plateforme | Exemple |
| -------- | ------------------- |
| ![Cette illustration montre le logo ASP.NET Core](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core | Fonction Azure avec API web ASP.NET Core (service) de [dotnet-native-aspnetcore-v2](https://github.com/Azure-Samples/ms-identity-dotnet-webapi-azurefunctions)  |
| ![Cette image affiche le logo Python](media/sample-v2-code/logo_python.png)</p>Python | API web (service) de [Python](https://github.com/Azure-Samples/ms-identity-python-webapi-azurefunctions) |
| ![Cette image affiche le logo Node.js](media/sample-v2-code/logo_nodejs.png)</p>Node.js (Passport.js)| API web (service) de [Node.js and passport-azure-ad](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-azurefunctions) |
| ![Cette image affiche le logo Node.js](media/sample-v2-code/logo_nodejs.png)</p>Node.js (Passport.js)| API web (service) de [Node.js and passport-azure-ad using on behalf of](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-onbehalfof-azurefunctions) |

## <a name="other-microsoft-graph-samples"></a>Autres exemples Microsoft Graph

Pour découvrir des [exemples](https://github.com/microsoftgraph/msgraph-community-samples/tree/master/samples#aspnet) et des tutoriels qui illustrent différents modèles d’utilisation de l’API Microsoft Graph, notamment l’authentification auprès d’Azure AD, consultez [Exemples et tutoriels de la communauté Microsoft Graph](https://github.com/microsoftgraph/msgraph-community-samples).

## <a name="see-also"></a>Voir aussi

[Concept et référence de l’API Microsoft Graph](/graph/use-the-api?context=graph%2fapi%2fbeta&view=graph-rest-beta&preserve-view=true)
