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
ms.date: 04/26/2018
ms.author: celested
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 51a50693a97a011d00d055eff8d3a0846512bce5
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39441406"
---
# <a name="azure-active-directory-code-samples-v2-endpoint"></a>Exemples de code Azure Active Directory (point de terminaison v2)

Vous pouvez utiliser Microsoft Azure Active Directory (Azure AD) pour :

- Ajouter l’authentification et l’autorisation à vos applications web et API web.
- Exiger un jeton d’accès pour accéder à une API web protégée.

Cet article fournit une brève description et des liens vers des exemples pour le point de terminaison V2 Azure AD. Ces exemples vous montrent comment procéder et contiennent des extraits de code que vous pouvez utiliser dans vos applications. Dans la page d’exemples de code, vous trouverez des rubriques Lisez-moi qui offrent une aide relative à la configuration requise, à l’installation et à la configuration. Les commentaires dans le code sont là pour vous aider à comprendre les sections critiques.

> [!NOTE]
> Si vous êtes intéressé par les exemples V1, consultez [Exemples de code Azure AD (point de terminaison V1)](active-directory-code-samples.md).

Pour comprendre le scénario de base pour chaque type d’exemple, consultez [Types d’application pour le point de terminaison Azure Active Directory v2.0](active-directory-v2-flows.md).

Vous pouvez également contribuer aux exemples sur GitHub. Pour en savoir plus, consultez [Documentation et exemples Microsoft Azure Active Directory](https://github.com/Azure-Samples?page=3&query=active-directory).

## <a name="desktop-and-mobile-public-client-apps"></a>Applications clientes publiques mobiles et de bureau

Les exemples suivants illustrent des applications clientes publiques (applications de bureau/mobile) qui accèdent à l’API web ou Microsoft Graph pour le compte d’un utilisateur.

Application cliente | Plateforme | Flux/Grant | Appelle Microsoft Graph | Appelle une API web ASP.NET Core 2.0
------------------ | -------- | ---------- | -------------------- | -------------------------
Bureau (WPF)      | .NET/C#  | Interactive | [dotnet-desktop-msgraph-v2](http://github.com/azure-samples/active-directory-dotnet-desktop-msgraph-v2) <p/> [dotnet-admin-restricted-scopes-v2](https://github.com/azure-samples/active-directory-dotnet-admin-restricted-scopes-v2) | [dotnet-native-aspnetcore-v2](https://GitHub.com/azure-samples/active-directory-dotnet-native-aspnetcore-v2)
Mobile (UWP)   | .NET/C# (UWP) | Interactive | [dotnet-native-uwp-v2](https://github.com/azure-samples/active-directory-dotnet-native-uwp-v2) |
Mobile (Android, iOS, UWP)   | .NET/C# (Xamarin) | Interactive | [xamarin-native-v2](https://Github.com/azure-samples/active-directory-xamarin-native-v2) |
Mobile (iOS)       | iOS / Objective C ou swift | Interactive | [ios-swift-native-v2](https://github.com/azure-samples/active-directory-ios-swift-native-v2) <p/> [ios-native-nxoauth2-v2](https://github.com/azure-samples/active-directory-ios-native-nxoauth2-v2) |
Mobile (Android)   | Android / Java | Interactive |   [android-native-v2](https://github.com/azure-samples/active-directory-android-native-v2 ) |

## <a name="web-applications"></a>Applications web

Les exemples suivants illustrent des applications web qui connectent des utilisateurs, appellent Microsoft Graph, ou appellent une API web avec l’identité de l’utilisateur.

 Plateforme | Connecte uniquement les utilisateurs | Connecte les utilisateurs et appelle Microsoft Graph 
 -------- | ------------------- | --------------------------------- 
ASP.NET 4.x | [appmodelv2-webapp-openIDConnect-dotNet](https://GitHub.com/AzureAdQuickstarts/AppModelv2-WebApp-OpenIDConnect-DotNet) <p/> [dotnet-webapp-openidconnect-v2](https://GitHub.com/azure-samples/active-directory-dotnet-webapp-openidconnect-v2)  |              [aspnet-connect-rest-sample](https://github.com/microsoftgraph/aspnet-connect-rest-sample)   
ASP.NET Core 2.0 | [aspnetcore-webapp-openidconnect-v2](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2) |              [aspnetcore-connect-sample](https://github.com/microsoftgraph/aspnetcore-connect-sample)   
Node.js      |                   | [AppModelv2-WebApp-OpenIDConnect-nodejs](https://github.com/azureadquickstarts/appmodelv2-webapp-openidconnect-nodejs)     
Ruby      |                   | [ruby-connect-rest-sample](https://github.com/microsoftgraph/ruby-connect-rest-sample)     

## <a name="daemon-applications"></a>Applications de démon

Les exemples suivants illustrent des applications web ou de bureau qui accèdent à l’API web ou Microsoft Graph avec l’identité de l’application (aucun utilisateur).

Application cliente | Plateforme | Flux/Grant | Appelle Microsoft Graph 
------------------ | -------- | ---------- | -------------------- 
Application web | .NET/C#  | Informations d’identification du client | [dotnet-daemon-v2](https://github.com/azure-samples/active-directory-dotnet-daemon-v2) 

## <a name="single-page-applications-spa"></a>Applications monopages (SPA)

Cet exemple montre comment écrire une application à page unique sécurisée avec Azure AD.

 Plateforme |  Appelle Microsoft Graph 
 -------- |  --------------------- 
JavaScript (msal.js)  | [javascript-graphapi-v2](https://github.com/azure-samples/active-directory-javascript-graphapi-v2) 
JavaScript (msal.js + AngularJS) | [angular-connect-rest-sample](https://github.com/microsoftgraph/angular-connect-rest-sample) 
JavaScript (Hello.JS)  | [javascript-graphapi-web-v2](https://github.com/azure-samples/active-directory-javascript-graphapi-web-v2) 
JavaScript (hello.js + Angular 4) | [angular4-connect-sample](https://github.com/microsoftgraph/angular4-connect-sample) 

## <a name="web-apis"></a>API Web

### <a name="web-api-protected-by-azure-ad"></a>API web protégée par Azure AD

L’exemple suivant montre comment protéger une API web avec le point de terminaison V2 Azure AD.

Plateforme | Exemple | Description
 -------- | ------------------- | ---------------------
Node.js | [dotnet-native-aspnetcore-v2](https://GitHub.com/azure-samples/active-directory-dotnet-native-aspnetcore-v2) | Appelle l’API web ASP.NET Core à partir d’une application WPF à l’aide d’Azure AD V2.

### <a name="web-api-calling-microsoft-graph-or-another-web-api"></a>API web appelant Microsoft Graph ou une autre API web

Cet exemple n’est pas encore disponible.

## <a name="other-microsoft-graph-samples"></a>Autres exemples Microsoft Graph

Pour découvrir des [exemples](https://github.com/microsoftgraph/msgraph-community-samples/tree/master/samples#aspnet) et des tutoriels qui illustrent différents modèles d’utilisation de l’API Microsoft Graph, notamment l’authentification auprès d’Azure AD, consultez [Exemples et tutoriels de la communauté Microsoft Graph](https://github.com/microsoftgraph/msgraph-community-samples).

## <a name="see-also"></a>Voir aussi

[Guide du développeur Azure Active Directory](azure-ad-developers-guide.md)

[Concept et référence de l’API Graph Azure AD](https://msdn.microsoft.com/library/azure/hh974476.aspx)

[Bibliothèque d’assistance de l’API Azure AD Graph](https://www.nuget.org/packages/Microsoft.Azure.ActiveDirectory.GraphClient)
