---
title: Exemples de code Azure Active Directory | Microsoft Docs
description: Fournit un index des exemples de code Azure Active Directory (point de terminaison v1), organisés par scénario.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: mtillman
editor: ''
ms.assetid: a242a5ff-7300-40c2-ba83-fb6035707433
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/24/2018
ms.author: celested
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 299e0d4fa53f9b8a2aef2fc881b136aa41aacfe4
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/14/2018
ms.locfileid: "34157773"
---
# <a name="azure-active-directory-code-samples-v1-endpoint"></a>Exemples de code Azure Active Directory (point de terminaison v1)

[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Vous pouvez utiliser Microsoft Azure Active Directory (Azure AD) pour ajouter l'authentification et l'autorisation à vos applications web et API web.

Cette section fournit des liens vers des exemples que vous pouvez utiliser pour en savoir plus sur le point de terminaison Azure AD V1. Ces exemples vous montrent comment procéder et contiennent des extraits de code que vous pouvez utiliser dans vos applications. Dans la page d’exemples de code, vous trouverez des rubriques Lisez-moi qui offrent une aide relative à la configuration requise, à l’installation et à la configuration. Le code est également accompagné de commentaires pour vous aider à comprendre les sections critiques.

> [!NOTE]
> Si vous êtes intéressé par des exemples de code Azure AD V2, consultez [Exemples de code v2.0 par scénario](active-directory-v2-code-samples.md).

Pour comprendre le scénario de base de chaque type d’exemple, consultez [Scénarios d’authentification pour Azure AD](active-directory-authentication-scenarios.md).

Vous pouvez également contribuer à nos exemples sur GitHub. Pour en savoir plus, consultez [Documentation et exemples Microsoft Azure Active Directory](https://github.com/Azure-Samples?page=3&query=active-directory).

## <a name="desktop-and-mobile-public-client-applications-calling-microsoft-graph-or-a-web-api"></a>Applications mobiles clientes et de bureau appelant Microsoft Graph ou une API web

Les exemples suivants illustrent des applications clientes publiques (applications de bureau/mobile) qui accèdent à l’API web ou Microsoft Graph pour le compte d’un utilisateur.

Application cliente | Plateforme | Flux/Grant | Appelle Microsoft Graph | Appelle une API web ASP.NET ou ASP.NET Core 2.0
------------------ | -------- | ---------- | -------------------- | -------------------------
Bureau (WPF)           | .NET/C# | Interactive | [dotnet-native-multitarget](https://github.com/azure-samples/active-directory-dotnet-native-multitarget) | [Dotnet-native-desktop](https://github.com/Azure-Samples/active-directory-dotnet-native-desktop) </p> [dotnet-native-aspnetcore](https://azure.microsoft.com/resources/samples/active-directory-dotnet-native-aspnetcore/)</p> [dotnet-webapi-manual-jwt-validation](https://github.com/azure-samples/active-directory-dotnet-webapi-manual-jwt-validation)
Mobile (UWP)            | .NET/C#  | Interactive | [dotnet-native-uwp-wam](https://github.com/azure-samples/active-directory-dotnet-native-uwp-wam) |  [dotnet-windows-store](https://github.com/Azure-Samples/active-directory-dotnet-windows-store) (single tenant Web API) </p> [dotnet-webapi-multitenant-windows-store](https://github.com/Azure-Samples/active-directory-dotnet-webapi-multitenant-windows-store) (API web multi-locataire)|
Mobile (Android, iOS, UWP)   | .NET/C# (Xamarin) | Interactive | [dotnet-native-multitarget](https://github.com/azure-samples/active-directory-dotnet-native-multitarget) |
Mobile (Android)           | Android/Java | Interactive |   [android](https://github.com/Azure-Samples/active-directory-android) |
Mobile (iOS)           | iOS/Objective C | Interactive |   [nativeClient-iOS](https://github.com/azureadquickstarts/nativeclient-ios) |
Bureau (Console)          | .NET/C# | Nom d’utilisateur / mot de passe </p> Authentification Windows intégrée | | [dotnet-native-headless](https://github.com/azure-samples/active-directory-dotnet-native-headless)
Bureau (Console)           | .NET Core/C# | Profil d’appareil | | [dotnet-deviceprofile](https://github.com/Azure-Samples/active-directory-dotnet-deviceprofile)

## <a name="web-applications"></a>Applications web

### <a name="web-applications-signing-in-users-calling-microsoft-graph-or-a-web-api-with-the-users-identity"></a>Applications web connectant les utilisateurs, appelant Microsoft Graph ou une API web avec l’identité de l’utilisateur

 Plateforme | Connecte uniquement les utilisateurs | Appelle Microsoft Graph ou AAD Graph| Appelle une autre API web ASP.NET ou ASP.NET Core 2.0
 -------- | ------------------- | --------------------- | -------------------------
ASP.NET 4.5 | [webApp-openidconnect-dotnet](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-aspnetwebapp-v1) </p> [webapp-WSFederation-dotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-wsfederation) | [dotnet-webapp-multitenant-openidconnect](https://github.com/Azure-Samples/active-directory-dotnet-webapp-multitenant-openidconnect) (AAD Graph) |
ASP.NET Core 2.0 | [dotnet-webapp-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore) | [webapp-webapi-multitenant-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-webapp-webapi-multitenant-openidconnect-aspnetcore/) (AAD Graph) | [dotnet-webapp-webapi-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-openidconnect-aspnetcore)
ASP.NET 4.5 | [dotnet-webapp-webapi-oauth2-useridentity](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-useridentity) | |
Python | | [python-webapp-graphapi](https://github.com/Azure-Samples/active-directory-python-webapp-graphapi)  |
Java | | [java-webapp-openidconnect](https://github.com/azure-samples/active-directory-java-webapp-openidconnect)  |
PHP | | [php-graphapi-web](https://github.com/Azure-Samples/active-directory-php-graphapi-web)  |

### <a name="web-applications-demonstrating-role-based-access-control-authorization"></a>Applications web illustrant le contrôle d’accès en fonction du rôle (autorisation)

Les exemples suivants montrent comment implémenter le contrôle d’accès en fonction du rôle, qui permet de restreindre les autorisations de certaines fonctionnalités d’une application web à certains utilisateurs. Les utilisateurs sont autorisés selon qu’ils appartiennent à un rôle ou à un groupe Azure Active Directory.

Plateforme | Exemple | Description
 -------- | ------------------- | ---------------------
ASP.NET 4.5 | [dotnet-webapp-groupclaims](https://github.com/Azure-Samples/active-directory-dotnet-webapp-groupclaims) | Application web MVC .NET 4.5 qui utilise des **groupes** Azure AD pour l’autorisation
ASP.NET 4.5 | [dotnet-webapp-roleclaims](https://github.com/Azure-Samples/active-directory-dotnet-webapp-roleclaims) | Application web MVC .NET 4.5 qui utilise des **rôles** Azure AD pour l’autorisation

## <a name="daemon-applications-accessing-web-apis-with-the-applications-identity"></a>Applications de démon (accédant à des API web avec l’identité de l’application)

Les exemples suivants illustrent des applications web ou de bureau qui accèdent à l’API web ou Microsoft Graph sans utilisateur (avec l’identité de l’application).

Application cliente | Plateforme | Flux/Grant | Appelle Microsoft Graph | Appelle une API web ASP.NET ou ASP.NET Core 2.0
------------------ | -------- | ---------- | -------------------- | -------------------------
Application de démon (Console)          | .NET/C#  | Informations d’identification du client avec certificat ou secret d’application | | [dotnet-daemon](https://github.com/azure-samples/active-directory-dotnet-daemon)</p> [dotnet-daemon-certificate-credential](https://github.com/azure-samples/active-directory-dotnet-daemon-certificate-credential)
Application de démon (Console)         | .NET Core/C# | Informations d’identification du client avec certificat| | [dotnetcore-daemon-certificate-credential](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-certificate-credential)
Bureau            | Java | Informations d'identification du client |   [java-native-headless](https://github.com/azure-samples/active-directory-java-native-headless) |
Application web ASP.NET  | .NET/C# | Informations d'identification du client |    | [dotnet-webapp-webapi-oauth2-appidentity](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-appidentity)

## <a name="web-apis"></a>API Web

### <a name="web-api-protected-by-azure-active-directory"></a>API web protégée par Azure Active Directory

L’exemple suivant montre comment protéger une API web node.js avec Azure AD.

Plateforme | Exemple | Description
 -------- | ------------------- | ---------------------
Node.js | [node-webapi](https://github.com/Azure-Samples/active-directory-node-webapi) |  API web Node.js sécurisée à l’aide d’Azure AD et de jetons d’accès OAuth 2.0.

### <a name="web-api-calling-microsoft-graph-or-another-web-api"></a>API web appelant Microsoft Graph ou une autre API web

Les exemples suivants présentent une API web qui appelle une autre API web. Le deuxième exemple montre comment gérer l’accès conditionnel.

 Plateforme |  Appelle Microsoft Graph | Appelle une autre API web ASP.NET ou ASP.NET Core 2.0
 -------- |  --------------------- | -------------------------
ASP.NET 4.5 | [dotnet-webapi-onbehalfof](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof) |[dotnet-webapi-onbehalfof](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof)
ASP.NET 4.5 | [dotnet-webapi-onbehalfof-ca](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof-ca) |[dotnet-webapi-onbehalfof-ca](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof-ca)

## <a name="single-page-applications"></a>Applications monopages

Cet exemple montre comment écrire une application monopage sécurisée avec Azure AD.

 Plateforme |  Appelle Microsoft Graph | Appelle sa propre API
 -------- |  --------------------- | -------------------------
JavaScript (Angular) / ASP.NET 4.x |  | [angularjs-singlepageapp](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp)

## <a name="other-microsoft-graph-samples"></a>Autres exemples Microsoft Graph

Pour obtenir des exemples et des tutoriels qui illustrent différents modèles d’utilisation de l’API Microsoft Graph, notamment l’authentification auprès d’Azure AD, consultez [Exemples et tutoriels de la communauté Microsoft Graph](https://github.com/microsoftgraph/msgraph-community-samples).

## <a name="see-also"></a>Voir aussi

[Guide du développeur Azure Active Directory](active-directory-developers-guide.md)

[Concept et référence de l’API Graph Azure AD](https://msdn.microsoft.com/library/azure/hh974476.aspx)

[Bibliothèque d’assistance de l’API Azure AD Graph](https://www.nuget.org/packages/Microsoft.Azure.ActiveDirectory.GraphClient)
