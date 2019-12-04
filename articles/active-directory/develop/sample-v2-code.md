---
title: Exemples de code pour la plateforme d’identité Microsoft | Microsoft Docs
description: Fournit un index des exemples de code de la plateforme d’identité Microsoft (point de terminaison v2.0) disponibles, organisés par scénario.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: a242a5ff-7300-40c2-ba83-fb6035707433
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/15/2019
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8f4d081d9a52188ee4c4b6b2a854cb9e48c02f16
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/26/2019
ms.locfileid: "74533023"
---
# <a name="microsoft-identity-platform-code-samples-v20-endpoint"></a>Exemples de code de la plateforme d’identité Microsoft (point de terminaison v2.0)

Vous pouvez utiliser la plateforme d’identités Microsoft pour effectuer les opérations suivantes :

- Ajouter l’authentification et l’autorisation à vos applications web et API web.
- Exiger un jeton d’accès pour accéder à une API web protégée.

Cet article fournit une brève description et des liens vers des exemples pour le point de terminaison V2 de la plateforme d’identité Microsoft. Ces exemples vous montrent comment procéder, et fournissent des extraits de code que vous pouvez utiliser dans vos applications. Dans la page d’exemples de code, vous trouverez des rubriques Lisez-moi détaillées, qui offrent une aide relative à la configuration requise, à l’installation et à la configuration. Les commentaires figurant dans le code sont là pour vous aider à comprendre les sections critiques.

> [!NOTE]
> Si vous êtes intéressé par les exemples associés à la version 1.0, voir [Exemples de code Azure Active Directory (point de terminaison v1.0)](sample-v1-code.md).

Pour comprendre le scénario de base pour chaque type d’exemple, voir [Types d’applications pour la plateforme d’identité Microsoft](v2-app-types.md).

Vous pouvez également contribuer aux exemples sur GitHub. Pour en savoir plus, consultez [Documentation et exemples Microsoft Azure Active Directory](https://github.com/Azure-Samples?page=3&query=active-directory).

## <a name="single-page-applications"></a>Applications monopages

Ces exemples montrent comment écrire une application monopage sécurisée avec la plateforme d’identité Microsoft. Ils utilisent l’une des saveurs disponibles de MSAL.js.

| Plateforme | Description | Lien |
| -------- | --------------------- | -------- |
| ![Cette illustration montre le logo JavaScript](media/sample-v2-code/logo_js.png) [JavaScript (msal.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) | Appelle Microsoft Graph |[javascript-graphapi-web-v2](https://github.com/Azure-Samples/active-directory-javascript-graphapi-web-v2) |
| ![Cette illustration montre le logo JavaScript](media/sample-v2-code/logo_js.png) [JavaScript (msal.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) | Appelle B2C |[b2c-javascript-msal-singlepageapp](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp) |
| ![Cette illustration montre le logo JavaScript](media/sample-v2-code/logo_js.png) [JavaScript (msal.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) | Appelle votre propre API web |[javascript-singlepageapp-dotnet-webapi-v2](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2) |
| ![Cette illustration montre le logo Angular JS](media/sample-v2-code/logo_angular.png) [JavaScript (AngularJS MSAL)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angularjs)| Appelle Microsoft Graph  | [MsalAngularjsDemoApp](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/samples/MsalAngularjsDemoApp)
| ![Cette illustration montre le logo Angular JS](media/sample-v2-code/logo_angular.png) [JavaScript (AngularJS MSAL)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular)| Appelle Microsoft Graph  | [MSALAngularDemoApp](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/samples/MSALAngularDemoApp) |

## <a name="web-applications"></a>Applications web

Les exemples suivants illustrent des applications web qui connectent les utilisateurs. Certains exemples montrent également l’application qui appelle Microsoft Graph ou votre propre API web avec l’identité de l’utilisateur.

| Plateforme | Connecte uniquement les utilisateurs | Connecte les utilisateurs et appelle Microsoft Graph |
| -------- | ------------------- | --------------------------------- |
| ![Cette illustration montre le logo ASP.NET Core](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core 2.2 | [Didacticiel de connexion des utilisateurs par la WebApp ASP.NET Core](https://aka.ms/aspnetcore-webapp-sign-in) | Même exemple dans la phase dans laquelle [la WebApp ASP.NET Core appelle Microsoft Graph](https://aka.ms/aspnetcore-webapp-call-msgraph) |
| ![Cette image affiche le logo ASP.NET](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET | [Démarrage rapide avec ASP.NET](https://github.com/AzureAdQuickstarts/AppModelv2-WebApp-OpenIDConnect-DotNet) </p> [dotnet-webapp-openidconnect-v2](https://github.com/azure-samples/active-directory-dotnet-webapp-openidconnect-v2)  |  [dotnet-admin-restricted-scopes-v2](https://github.com/azure-samples/active-directory-dotnet-admin-restricted-scopes-v2) </p> |[msgraph-training-aspnetmvcapp](https://github.com/microsoftgraph/msgraph-training-aspnetmvcapp)
| ![Cette image affiche le logo Java](media/sample-v2-code/logo_java.png)  |                   | [ms-identity-java-webapp](https://github.com/Azure-Samples/ms-identity-java-webapp) : application web MSAL4J appelant Microsoft Graph |
| ![Cette image affiche le logo Node.js](media/sample-v2-code/logo_nodejs.png)  |                   | [Démarrage rapide avec Node.js](https://github.com/azureadquickstarts/appmodelv2-webapp-openidconnect-nodejs) |
| ![Cette image affiche le logo Ruby](media/sample-v2-code/logo_ruby.png) |                   | [msgraph-training-rubyrailsapp](https://github.com/microsoftgraph/msgraph-training-rubyrailsapp) |

## <a name="desktop-and-mobile-public-client-apps"></a>Applications clientes publiques mobiles et de bureau

Les exemples suivants illustrent des applications clientes publiques (applications de bureau ou mobiles) qui accèdent à l’API Graph ou à votre API web pour le compte d’un utilisateur. Toutes ces applications clientes utilisent une bibliothèque d’authentification Microsoft (MSAL).

| Application cliente | Plateforme | Flux/Grant | Appelle Microsoft Graph | Appelle une API web ASP.NET Core 2.0 |
| ------------------ | -------- |  ----------| ---------- | ------------------------- |
| Bureau (WPF)      | ![Cette image affiche le logo .NET/C#](media/sample-v2-code/logo_NET.png) | [interactive](msal-authentication-flows.md#interactive)| [dotnet-desktop-msgraph-v2](https://github.com/azure-samples/active-directory-dotnet-desktop-msgraph-v2) | [dotnet-native-aspnetcore-v2](https://aka.ms/msidentity-aspnetcore-webapi) |
| Bureau (Console)   | ![Cette image affiche le logo .NET/C# (bureau)](media/sample-v2-code/logo_NET.png) | [Authentification Windows intégrée](msal-authentication-flows.md#integrated-windows-authentication) | [dotnet-iwa-v2](https://github.com/azure-samples/active-directory-dotnet-iwa-v2) |  |
| Bureau (Console)   | ![Cette image affiche le logo .NET/C# (bureau)](media/sample-v2-code/logo_NETcore.png) | [Nom d’utilisateur/mot de passe](msal-authentication-flows.md#usernamepassword) |[dotnetcore-up-v2](https://github.com/azure-samples/active-directory-dotnetcore-console-up-v2) |  |
| Mobile (Android, iOS, UWP)   | ![Cette image affiche le logo .NET/C# (Xamarin)](media/sample-v2-code/logo_xamarin.png) | [interactive](msal-authentication-flows.md#interactive) |[xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) |  |
| Mobile (iOS)       | ![Cette image affiche iOS/Objective-C ou Swift](media/sample-v2-code/logo_iOS.png) | [interactive](msal-authentication-flows.md#interactive) |[ios-swift-objc-native-v2](https://github.com/azure-samples/active-directory-ios-swift-native-v2) </p> [ios-native-nxoauth2-v2](https://github.com/azure-samples/active-directory-ios-native-nxoauth2-v2) |  |
| Bureau (macOS)       | macOS | [interactive](msal-authentication-flows.md#interactive) |[macOS-swift-objc-native-v2](https://github.com/Azure-Samples/ms-identity-macOS-swift-objc) |  |
| Mobile (Android-Java)   | ![Cette image affiche le logo Android](media/sample-v2-code/logo_Android.png) | [interactive](msal-authentication-flows.md#interactive) |  [Android-Java](https://github.com/Azure-Samples/ms-identity-android-java) |  |
| Mobile (Android-Kotlin)   | ![Cette image affiche le logo Android](media/sample-v2-code/logo_Android.png) | [interactive](msal-authentication-flows.md#interactive) |  [Android-Kotlin](https://github.com/Azure-Samples/ms-identity-android-kotlin) |  |

## <a name="daemon-applications"></a>Applications de démon

L’exemple suivant présente une application qui accède à l’API Microsoft Graph avec sa propre identité (sans utilisateur).

| Application cliente | Plateforme | Flux/Grant | Appelle Microsoft Graph |
| ------------------ | -------- | ---------- | -------------------- |
| Console | ![Cette image affiche le logo .NET Core](media/sample-v2-code/logo_NETcore.png)</p> ASP.NET  | [Informations d’identification du client](msal-authentication-flows.md#client-credentials) | [dotnetcore-daemon-v2](https://github.com/azure-samples/active-directory-dotnetcore-daemon-v2) |
| Application web | ![Cette image affiche le logo ASP.NET](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET  | [Informations d’identification du client](msal-authentication-flows.md#client-credentials) | [dotnet-daemon-v2](https://github.com/azure-samples/active-directory-dotnet-daemon-v2) |

## <a name="headless-applications"></a>Applications sans affichage

L’exemple suivant présente une application cliente publique qui s’exécute sur un appareil sans navigateur web. Cette application peut être un outil en ligne de commande ou une application qui s’exécute sur un système Linux ou Mac, ou encore une application IoT. Cet exemple présente une application accédant à l’API Graph au nom d’un utilisateur qui se connecte de manière interactive sur un autre appareil (par exemple, un téléphone mobile). Cette application cliente utilise une bibliothèque d’authentification Microsoft (MSAL).

| Application cliente | Plateforme | Flux/Grant | Appelle Microsoft Graph |
| ------------------ | -------- |  ----------| ---------- |
| Bureau (Console)   | ![Cette image affiche le logo .NET/C# (bureau)](media/sample-v2-code/logo_NETcore.png) | [Flux de code d’appareil](msal-authentication-flows.md#device-code) |[dotnetcore-devicecodeflow-v2](https://github.com/azure-samples/active-directory-dotnetcore-devicecodeflow-v2) |

## <a name="web-apis"></a>API Web

Les exemples suivants montrent comment protéger une API web avec le point de terminaison de la plateforme d’identité Microsoft, et comment appeler une API en aval à partir de l’API web.

| Plateforme | Exemple |
| -------- | ------------------- |
| ![Cette illustration montre le logo ASP.NET Core](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core 2.2 | API web ASP.NET Core (service) associée à [dotnet-native-aspnetcore-v2](https://aka.ms/msidentity-aspnetcore-webapi-calls-msgraph)  |
| ![Cette image affiche le logo ASP.NET](media/sample-v2-code/logo_NET.png)</p>ASP.NET MVC | API web (service) associée à [ms-identity-aspnet-webapi-onbehalfof](https://github.com/Azure-Samples/ms-identity-aspnet-webapi-onbehalfof) |

## <a name="azure-functions-as-web-apis"></a>Fonctions Azure en tant qu’API web

Les exemples suivants montrent comment protéger une fonction Azure en utilisant HttpTrigger et en exposant une API web avec le point de terminaison de la plateforme d’identité Microsoft, et comment appeler une API en aval à partir de l’API web.

| Plateforme | Exemple |
| -------- | ------------------- |
| ![Cette illustration montre le logo ASP.NET Core](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core 2.2 | Fonction Azure avec API web ASP.NET Core (service) de [dotnet-native-aspnetcore-v2](https://github.com/Azure-Samples/ms-identity-dotnet-webapi-azurefunctions)  |
| ![Cette image affiche le logo Node.js](media/sample-v2-code/logo_nodejs.png)</p>NodeJS | API web (service) de [NodeJS and passport-azure-ad](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-azurefunctions) |
| ![Cette image affiche le logo Python](media/sample-v2-code/logo_python.png)</p>Python | API web (service) de [Python](https://github.com/Azure-Samples/ms-identity-python-webapi-azurefunctions) |
| ![Cette image affiche le logo Node.js](media/sample-v2-code/logo_nodejs.png)</p>NodeJS | API web (service) de [NodeJS and passport-azure-ad using on behalf of](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-onbehalfof-azurefunctions) |

## <a name="other-microsoft-graph-samples"></a>Autres exemples Microsoft Graph

Pour découvrir des [exemples](https://github.com/microsoftgraph/msgraph-community-samples/tree/master/samples#aspnet) et des tutoriels qui illustrent différents modèles d’utilisation de l’API Microsoft Graph, notamment l’authentification auprès d’Azure AD, consultez [Exemples et tutoriels de la communauté Microsoft Graph](https://github.com/microsoftgraph/msgraph-community-samples).

## <a name="see-also"></a>Voir aussi

- [Guide du développeur Azure Active Directory (v1.0)](v1-overview.md)
- [Concept et référence de l’API Graph Azure AD](https://msdn.microsoft.com/library/azure/hh974476.aspx)
- [Bibliothèque d’assistance de l’API Azure AD Graph](https://www.nuget.org/packages/Microsoft.Azure.ActiveDirectory.GraphClient)
