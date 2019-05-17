---
title: Exemples de code de plateforme Microsoft identity | Microsoft Docs
description: Fournit des exemples de code de plate-forme (point de terminaison V2), organisées par scénario un index de l’identité de Microsoft disponible.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: a242a5ff-7300-40c2-ba83-fb6035707433
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/26/2018
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: f430d712626c5c5f4667d709a27171592f84a150
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/17/2019
ms.locfileid: "65823604"
---
# <a name="microsoft-identity-platform-code-samples-v20-endpoint"></a>Exemples de code de plateforme Microsoft identity (point de terminaison v2.0)

[!INCLUDE [active-directory-develop-applies-v2-msal](../../../includes/active-directory-develop-applies-v2-msal.md)]

Vous pouvez utiliser la plateforme d’identités Microsoft pour effectuer les opérations suivantes :

- Ajouter l’authentification et l’autorisation à vos applications web et API web.
- Exiger un jeton d’accès pour accéder à une API web protégée.

Cet article décrit brièvement et vous fournit des liens vers des exemples de point de terminaison de plateforme d’identité Microsoft. Ces exemples vous montrent comment procéder et contiennent des extraits de code que vous pouvez utiliser dans vos applications. Dans la page d’exemples de code, vous trouverez des rubriques Lisez-moi qui offrent une aide relative à la configuration requise, à l’installation et à la configuration. Les commentaires dans le code sont là pour vous aider à comprendre les sections critiques.

> [!NOTE]
> Si vous êtes intéressé par les exemples associés à la version 1.0, voir [Exemples de code Azure Active Directory (point de terminaison v1.0)](sample-v1-code.md).

Pour comprendre le scénario de base pour chaque type d’exemple, consultez [types d’application pour le point de terminaison Microsoft identity plateforme](v2-app-types.md).

Vous pouvez également contribuer aux exemples sur GitHub. Pour en savoir plus, consultez [Documentation et exemples Microsoft Azure Active Directory](https://github.com/Azure-Samples?page=3&query=active-directory).

## <a name="single-page-applications-spa"></a>Applications monopages (SPA)

Ces exemples montrent comment écrire une application à page unique sécurisée avec la plateforme d’identité Microsoft. Ils utilisent l’une des versions suivantes de MSAL.js :

| Plateforme | Description  | Liaison |
| -------- | --------------------- | -------- |
| ![JavaScript](media/sample-v2-code/logo_js.png) [JavaScript (msal.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) | Appelle Microsoft Graph |[javascript-graphapi-web-v2](https://github.com/Azure-Samples/active-directory-javascript-graphapi-web-v2) |
| ![JavaScript](media/sample-v2-code/logo_js.png) [JavaScript (msal.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) | Appels appels B2C |[b2c-javascript-msal-singlepageapp](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp) |
| ![JavaScript](media/sample-v2-code/logo_js.png) [JavaScript (msal.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) | Web propre d’appels API |[javascript-singlepageapp-dotnet-webapi-v2](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2) |
| ![Angular JS](media/sample-v2-code/logo_angular.png) [JavaScript (AngularJS MSAL)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angularjs)| Appelle Microsoft Graph  | [MsalAngularjsDemoApp](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-angularjs/samples/MsalAngularjsDemoApp)
| ![Angular](media/sample-v2-code/logo_angular.png) [JavaScript (MSAL Angular)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular)| Appelle Microsoft Graph  | [MSALAngularDemoApp](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-angular/samples/MSALAngularDemoApp) |

## <a name="web-applications"></a>Applications web

Les exemples suivants illustrent des applications web qui connectent les utilisateurs. Certains exemples montrent également l’application qui appelle Microsoft Graph ou votre propre API web avec l’identité de l’utilisateur.

| Plateforme | Connecte uniquement les utilisateurs | Connecte les utilisateurs et appelle Microsoft Graph |
| -------- | ------------------- | --------------------------------- |
| ![ASP.NET Core](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core 2.1 | [Didacticiel d’utilisateurs se connecte en ASP.NET Core WebApp](https://aka.ms/aspnetcore-webapp-sign-in) | Même exemple dans le [application Web ASP.NET Core appelle Microsoft Graph](https://aka.ms/aspnetcore-webapp-call-msgraph) phase |
| ![ASP.NET](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET | [Démarrage rapide avec ASP.NET](https://github.com/AzureAdQuickstarts/AppModelv2-WebApp-OpenIDConnect-DotNet) </p> [dotnet-webapp-openidconnect-v2](https://github.com/azure-samples/active-directory-dotnet-webapp-openidconnect-v2)  |  [dotnet-admin-restricted-scopes-v2](https://github.com/azure-samples/active-directory-dotnet-admin-restricted-scopes-v2) </p> |[msgraph-training-aspnetmvcapp](https://github.com/microsoftgraph/msgraph-training-aspnetmvcapp)
| ![Node.js](media/sample-v2-code/logo_nodejs.png)  |                   | [Démarrage rapide avec Node.js](https://github.com/azureadquickstarts/appmodelv2-webapp-openidconnect-nodejs) |
| ![Ruby](media/sample-v2-code/logo_ruby.png) |                   | [msgraph-training-rubyrailsapp](https://github.com/microsoftgraph/msgraph-training-rubyrailsapp) |

## <a name="desktop-and-mobile-public-client-apps"></a>Applications clientes publiques mobiles et de bureau

Les exemples suivants illustrent des applications clientes publiques (applications de bureau/mobiles) qui accèdent à l’API Microsoft Graph ou à votre API web pour le compte d’un utilisateur. Toutes ces applications clientes utilisent des bibliothèques d’authentification Microsoft (MSAL).

| Application cliente | Plateforme | Flux/Grant | Appelle Microsoft Graph | Appelle une API web ASP.NET Core 2.0 |
| ------------------ | -------- |  ----------| ---------- | ------------------------- |
| Bureau (WPF)      | ![.NET/C#](media/sample-v2-code/logo_NET.png) | [interactive](msal-authentication-flows.md#interactive)| [dotnet-desktop-msgraph-v2](https://github.com/azure-samples/active-directory-dotnet-desktop-msgraph-v2) | [dotnet-native-aspnetcore-v2](https://aka.ms/msidentity-aspnetcore-webapi) |
| Bureau (Console)   | ![.NET /C# (bureau)](media/sample-v2-code/logo_NET.png) | [Authentification Windows intégrée](msal-authentication-flows.md#integrated-windows-authentication) | [dotnet-iwa-v2](https://github.com/azure-samples/active-directory-dotnet-iwa-v2) |  |
| Bureau (Console)   | ![.NET /C# (bureau)](media/sample-v2-code/logo_NETcore.png) | [Nom d’utilisateur/mot de passe](msal-authentication-flows.md#usernamepassword) |[dotnetcore-up-v2](https://github.com/azure-samples/active-directory-dotnetcore-console-up-v2) |  |
| Mobile (Android, iOS, UWP)   | ![.NET/C# (Xamarin)](media/sample-v2-code/logo_xamarin.png) | [interactive](msal-authentication-flows.md#interactive) |[xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) |  |
| Mobile (iOS)       | ![iOS / Objective C ou swift](media/sample-v2-code/logo_iOS.png) | [interactive](msal-authentication-flows.md#interactive) |[ios-swift-native-v2](https://github.com/azure-samples/active-directory-ios-swift-native-v2) </p> [ios-native-nxoauth2-v2](https://github.com/azure-samples/active-directory-ios-native-nxoauth2-v2) |  |
| Mobile (Android)   | ![Android / Java](media/sample-v2-code/logo_Android.png) | [interactive](msal-authentication-flows.md#interactive) |  [android-native-v2](https://github.com/azure-samples/active-directory-android-native-v2 ) |  |

## <a name="daemon-applications"></a>Applications de démon

L’exemple suivant présente une application qui accède à l’API Microsoft Graph avec sa propre identité (sans utilisateur).

| Application cliente | Plateforme | Flux/Grant | Appelle Microsoft Graph |
| ------------------ | -------- | ---------- | -------------------- |
| Console | ![.NET Core](media/sample-v2-code/logo_NETcore.png)</p> ASP.NET  | [Informations d’identification du client](msal-authentication-flows.md#client-credentials) | [dotnetcore-daemon-v2](https://github.com/azure-samples/active-directory-dotnetcore-daemon-v2) |
| Application web | ![ASP.NET](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET  | [Informations d’identification du client](msal-authentication-flows.md#client-credentials) | [dotnet-daemon-v2](https://github.com/azure-samples/active-directory-dotnet-daemon-v2) |

## <a name="headless-applications"></a>Applications sans affichage

L’exemple suivant présente une application cliente publique qui s’exécute sur un appareil sans navigateur web. Cette application peut être un outil en ligne de commande ou une application IoT, et s’exécuter sur un système Linux ou Mac. Cet exemple présente une application qui accède à l’API Microsoft Graph au nom d’un utilisateur qui se connecte de manière interactive sur un autre appareil (par exemple un téléphone mobile). Cette application cliente utilise des bibliothèques d’authentification Microsoft (MSAL).

| Application cliente | Plateforme | Flux/Grant | Appelle Microsoft Graph |
| ------------------ | -------- |  ----------| ---------- |
| Bureau (Console)   | ![.NET /C# (bureau)](media/sample-v2-code/logo_NETcore.png) | [Flux de code d’appareil](msal-authentication-flows.md#device-code) |[dotnetcore-devicecodeflow-v2](https://github.com/azure-samples/active-directory-dotnetcore-devicecodeflow-v2) |

## <a name="web-apis"></a>API Web

L’exemple suivant montre comment protéger une API web avec le point de terminaison Microsoft identity platform. Cette API est utilisée par une application WPF, mais peut être appelée par n’importe quelle application. L’API web appelle également Microsoft Graph.

| Plateforme | Exemple |
| -------- | ------------------- |
| ![.NET/C#](media/sample-v2-code/logo_NET.png) | WebAPI (service) associé à [dotnet-native-aspnetcore-v2](https://aka.ms/msidentity-aspnetcore-webapi-calls-msgraph) |

## <a name="other-microsoft-graph-samples"></a>Autres exemples Microsoft Graph

Pour découvrir des [exemples](https://github.com/microsoftgraph/msgraph-community-samples/tree/master/samples#aspnet) et des tutoriels qui illustrent différents modèles d’utilisation de l’API Microsoft Graph, notamment l’authentification auprès d’Azure AD, consultez [Exemples et tutoriels de la communauté Microsoft Graph](https://github.com/microsoftgraph/msgraph-community-samples).

## <a name="see-also"></a>Voir aussi

- [Guide du développeur Azure Active Directory (v1.0)](v1-overview.md)
- [Concept et référence de l’API Graph Azure AD](https://msdn.microsoft.com/library/azure/hh974476.aspx)
- [Bibliothèque d’assistance de l’API Azure AD Graph](https://www.nuget.org/packages/Microsoft.Azure.ActiveDirectory.GraphClient)
