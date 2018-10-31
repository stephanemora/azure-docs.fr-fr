---
title: Exemples de code Azure Active Directory | Microsoft Docs
description: Fournit un index des exemples de code Azure Active Directory (point de terminaison v2) disponibles, organisés par scénario.
services: active-directory
documentationcenter: dev-center-name
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: a242a5ff-7300-40c2-ba83-fb6035707433
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/17/2018
ms.author: celested
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 7323f532787fa767d875e13262cce9f1ceaa79d8
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/24/2018
ms.locfileid: "49955511"
---
# <a name="azure-active-directory-code-samples-v2-endpoint"></a>Exemples de code Azure Active Directory (point de terminaison v2)

[!INCLUDE [active-directory-develop-applies-v2-msal](../../../includes/active-directory-develop-applies-v2-msal.md)]

Vous pouvez utiliser Microsoft Azure Active Directory (Azure AD) pour :

- Ajouter l’authentification et l’autorisation à vos applications web et API web.
- Exiger un jeton d’accès pour accéder à une API web protégée.

Cet article fournit une brève description et des liens vers des exemples pour le point de terminaison V2 Azure AD. Ces exemples vous montrent comment procéder et contiennent des extraits de code que vous pouvez utiliser dans vos applications. Dans la page d’exemples de code, vous trouverez des rubriques Lisez-moi qui offrent une aide relative à la configuration requise, à l’installation et à la configuration. Les commentaires dans le code sont là pour vous aider à comprendre les sections critiques.

> [!NOTE]
> Si vous êtes intéressé par les exemples V1, consultez [Exemples de code Azure AD (point de terminaison V1)](sample-v1-code.md).

Pour comprendre le scénario de base pour chaque type d’exemple, consultez [Types d’application pour le point de terminaison Azure Active Directory v2.0](v2-app-types.md).

Vous pouvez également contribuer aux exemples sur GitHub. Pour en savoir plus, consultez [Documentation et exemples Microsoft Azure Active Directory](https://github.com/Azure-Samples?page=3&query=active-directory).

## <a name="single-page-applications-spa"></a>Applications monopages (SPA)

Cet exemple montre comment écrire une application monopage sécurisée avec Azure AD. Ces exemples utilisent l’une des variantes de MSAL.js : [Bibliothèque d’authentification Microsoft pour JavaScript](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core), [Bibliothèque d’authentification Microsoft pour Angular](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular), [Bibliothèque d’authentification Microsoft pour AngularJS](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angularjs)

 Plateforme |  Appelle Microsoft Graph
 -------- |  ---------------------
![JavaScript](media/sample-v2-code/logo_js.png) JavaScript (msal.js)  | [javascript-graphapi-web-v2](https://github.com/Azure-Samples/active-directory-javascript-graphapi-web-v2)
![Angular JS](media/sample-v2-code/logo_angular.png) JavaScript (MSAL AngularJS) | [MsalAngularjsDemoApp](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-angularjs/samples/MsalAngularjsDemoApp)
![Angular](media/sample-v2-code/logo_angular.png) JavaScript (MSAL Angular) | [MSALAngularDemoApp](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-angular/samples/MSALAngularDemoApp)

## <a name="web-applications"></a>Applications web

Les exemples suivants illustrent des applications web qui connectent les utilisateurs. Certains exemples montrent également l’application qui appelle Microsoft Graph ou votre propre API web avec l’identité de l’utilisateur.

 Plateforme | Connecte uniquement les utilisateurs | Connecte les utilisateurs et appelle Microsoft Graph
 -------- | ------------------- | ---------------------------------
![ASP.NET](media/sample-v2-code/logo_NETframework.png)<p/> ASP.NET | [appmodelv2-webapp-openIDConnect-dotNet](https://GitHub.com/AzureAdQuickstarts/AppModelv2-WebApp-OpenIDConnect-DotNet) <p/> [dotnet-webapp-openidconnect-v2](https://GitHub.com/azure-samples/active-directory-dotnet-webapp-openidconnect-v2)  |              [aspnet-connect-rest-sample](https://github.com/microsoftgraph/aspnet-connect-rest-sample)
![ASP.NET](media/sample-v2-code/logo_NETcore.png)<p/>ASP.NET Core 2.0 | [aspnetcore-webapp-openidconnect-v2](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2) |              [aspnetcore-connect-sample](https://github.com/microsoftgraph/aspnetcore-connect-sample)
![Node.js](media/sample-v2-code/logo_nodejs.png)  |                   | [AppModelv2-WebApp-OpenIDConnect-nodejs](https://github.com/azureadquickstarts/appmodelv2-webapp-openidconnect-nodejs)
![Ruby](media/sample-v2-code/logo_ruby.png) |                   | [ruby-connect-rest-sample](https://github.com/microsoftgraph/ruby-connect-rest-sample)

## <a name="desktop-and-mobile-public-client-apps"></a>Applications clientes publiques mobiles et de bureau

Les exemples suivants illustrent des applications clientes publiques (applications de bureau/mobile) qui accèdent à Microsoft Graph ou à votre API web pour le compte d’un utilisateur. Toutes ces applications clientes utilisent des bibliothèques d’authentification Microsoft (MSAL)

Application cliente | Plateforme | Flux/Grant | Appelle Microsoft Graph | Appelle une API web ASP.NET Core 2.0
------------------ | -------- |  ----------| ---------- | -------------------------
Bureau (WPF)      | ![.NET/C#](media/sample-v2-code/logo_NET.png) | interactive | [dotnet-desktop-msgraph-v2](http://github.com/azure-samples/active-directory-dotnet-desktop-msgraph-v2) <p/> [dotnet-admin-restricted-scopes-v2](https://github.com/azure-samples/active-directory-dotnet-admin-restricted-scopes-v2) | [dotnet-native-aspnetcore-v2](https://GitHub.com/azure-samples/active-directory-dotnet-native-aspnetcore-v2)
Bureau (Console)   | ![.NET /C# (bureau)](media/sample-v2-code/logo_NET.png) | Authentification Windows intégrée |[dotnet-iwa-v2](https://github.com/azure-samples/active-directory-dotnet-iwa-v2)
Bureau (Console)   | ![.NET /C# (bureau)](media/sample-v2-code/logo_NETcore.png) | Nom d’utilisateur/mot de passe |[dotnetcore-up-v2](https://github.com/azure-samples/active-directory-dotnetcore-up-v2)
Mobile (UWP)   | ![.NET/C# (UWP)](media/sample-v2-code/logo_windows.png) | interactive |[dotnet-native-uwp-v2](https://github.com/azure-samples/active-directory-dotnet-native-uwp-v2) |
Mobile (Android, iOS, UWP)   | ![.NET/C# (Xamarin)](media/sample-v2-code/logo_xamarin.png) | interactive |[xamarin-native-v2](https://Github.com/azure-samples/active-directory-xamarin-native-v2) |
Mobile (iOS)       | ![iOS / Objective C ou swift](media/sample-v2-code/logo_iOS.png) | interactive |[ios-swift-native-v2](https://github.com/azure-samples/active-directory-ios-swift-native-v2) <p/> [ios-native-nxoauth2-v2](https://github.com/azure-samples/active-directory-ios-native-nxoauth2-v2) |
Mobile (Android)   | ![Android / Java](media/sample-v2-code/logo_Android.png) | interactive |  [android-native-v2](https://github.com/azure-samples/active-directory-android-native-v2 ) |

## <a name="daemon-applications"></a>Applications de démon

L’exemple suivant présente une application qui accède à Microsoft Graph avec sa propre identité (sans utilisateur).

Application cliente | Plateforme | Flux/Grant | Appelle Microsoft Graph
------------------ | -------- | ---------- | --------------------
Console | ![.NET Core](media/sample-v2-code/logo_NETcore.png)<p/> ASP.NET  | Informations d’identification du client | [dotnetcore-daemon-v2](https://github.com/azure-samples/active-directory-dotnetcore-daemon-v2)
Application web | ![ASP.NET](media/sample-v2-code/logo_NETframework.png)<p/> ASP.NET  | Informations d’identification du client | [dotnet-daemon-v2](https://github.com/azure-samples/active-directory-dotnet-daemon-v2)

## <a name="headless-applications"></a>Applications sans affichage

Les exemples suivants présentent des applications clients qui s’exécutent sur un appareil sans un navigateur web. Ces applications peuvent être des outils de ligne de commande ou des applications qui s’exécutent sur Linux/Mac ou des applications iOT. Les exemples présentent des applications qui accèdent à Microsoft Graph au nom d’un utilisateur qui se connecte de manière interactive sur un autre appareil (par exemple un téléphone mobile). Toutes ces applications clientes utilisent des bibliothèques d’authentification Microsoft (MSAL)

Application cliente | Plateforme | Flux/Grant | Appelle Microsoft Graph
------------------ | -------- |  ----------| ----------
Bureau (Console)   | ![.NET /C# (bureau)](media/sample-v2-code/logo_NETcore.png) | Flux de code d’appareil |[dotnetcore-devicecodeflow-v2](https://github.com/azure-samples/active-directory-dotnetcore-devicecodeflow-v2)

## <a name="web-apis"></a>API Web

L’exemple suivant montre comment protéger une API web avec le point de terminaison V2 Azure AD. Cette API est utilisée par une application WPF (mais peut réellement être appelée par n’importe quelle application)

Plateforme | Exemple
 -------- | -------------------
![.NET/C#](media/sample-v2-code/logo_NET.png) | [dotnet-native-aspnetcore-v2](https://GitHub.com/azure-samples/active-directory-dotnet-native-aspnetcore-v2)

## <a name="other-microsoft-graph-samples"></a>Autres exemples Microsoft Graph

Pour découvrir des [exemples](https://github.com/microsoftgraph/msgraph-community-samples/tree/master/samples#aspnet) et des tutoriels qui illustrent différents modèles d’utilisation de l’API Microsoft Graph, notamment l’authentification auprès d’Azure AD, consultez [Exemples et tutoriels de la communauté Microsoft Graph](https://github.com/microsoftgraph/msgraph-community-samples).

## <a name="see-also"></a>Voir aussi

[Guide du développeur Azure Active Directory](v1-overview.md)

[Concept et référence de l’API Graph Azure AD](https://msdn.microsoft.com/library/azure/hh974476.aspx)

[Bibliothèque d’assistance de l’API Azure AD Graph](https://www.nuget.org/packages/Microsoft.Azure.ActiveDirectory.GraphClient)
