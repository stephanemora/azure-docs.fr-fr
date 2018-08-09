---
title: Azure Active Directory pour les développeurs | Microsoft Docs
description: Cet article fournit une vue d’ensemble de la connexion des comptes professionnels et scolaires Microsoft à l’aide de Azure Active Directory.
services: active-directory
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 5c872c89-ef04-4f4c-98de-bc0c7460c7c2
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/30/2018
ms.author: celested
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 666a677943811af05cd3403eab4887271c1f87b3
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/07/2018
ms.locfileid: "39591208"
---
# <a name="azure-active-directory-for-developers"></a>Azure Active Directory pour les développeurs

Azure Active Directory (Azure AD) est un service d’identité cloud qui permet aux développeurs de créer des applications qui connectent de façon sécurisée les utilisateurs disposant d’un compte Microsoft professionnel ou scolaire. Azure AD prend en charge les développeurs qui créent des applications à locataire unique et métiers, ainsi que les développeurs qui souhaitent développer des applications multi-locataires. En plus de la connexion basique, Azure AD permet également aux applications d’appeler des API Microsoft comme [Microsoft Graph](https://developer.microsoft.com/en-us/graph/docs/concepts/overview) et des API personnalisées reposant sur la plateforme Azure AD. Cette documentation explique comment ajouter une prise en charge Azure AD à votre application par le biais de protocoles standard du secteur, comme OAuth 2.0 et OpenID Connect.

> [!NOTE]
> Le contenu de cette page traite en grande partie du point de terminaison Azure AD v1.0, qui prend seulement en charge les comptes Microsoft professionnels ou scolaires. Si vous voulez vous connecter à des comptes Microsoft consommateurs ou personnels, consultez les informations sur le [point de terminaison Azure AD v2.0](active-directory-appmodel-v2-overview.md). Le point de terminaison Azure AD v2.0 offre une expérience de développement unifiée pour les applications devant connecter des utilisateurs avec des comptes Azure AD (professionnels et scolaires) et des comptes Microsoft personnels.

| | |
| --- | --- |
|[Principes fondamentaux de l’authentification](authentication-scenarios.md) | Introduction à l’authentification avec Azure AD. |
|[Types d’applications](authentication-scenarios.md#application-types-and-scenarios) | Vue d’ensemble des scénarios d’authentification pris en charge par Azure AD. |      
| | |

## <a name="get-started"></a>Prise en main
Les indications de configuration suivantes vous guident pas à pas dans les différentes étapes de création d’une application sur votre plateforme préférée à l’aide du SDK de la bibliothèque Azure AD Authentication (ADAL). Si vous recherchez des informations à l’aide de la bibliothèque d’authentification Microsoft (MSAL), consultez la documentation sur le [point de terminaison Azure AD v2.0](active-directory-appmodel-v2-overview.md).

|  |  |  |  |
| --- | --- | --- | --- |
| <center>![Applications de bureau et mobiles](./media/azure-ad-developers-guide/NativeApp_Icon.png)<br />Applications de bureau et mobiles</center> | [Vue d’ensemble](authentication-scenarios.md#native-application-to-web-api)<br /><br />[iOS](quickstart-v1-ios.md)<br /><br />[Android](quickstart-v1-android.md) | [.NET (WPF)](quickstart-v1-dotnet.md)<br /><br />[Xamarin](quickstart-v1-xamarin.md) |
| <center>![Applications Web](./media/azure-ad-developers-guide/Web_app.png)<br />Applications Web</center> | [Vue d’ensemble](authentication-scenarios.md#web-browser-to-web-application)<br /><br />[ASP.NET](quickstart-v1-aspnet-webapp.md)<br /><br />[Java](https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect) | [Python](https://github.com/Azure-Samples/active-directory-python-webapp-graphapi)<br/><br/> [Node.JS](quickstart-v1-openid-connect-code.md) |
| <center>![Applications à page unique](./media/azure-ad-developers-guide/SPA.png)<br />Applications à page unique</center> | [Vue d’ensemble](authentication-scenarios.md#single-page-application-spa)<br /><br />[AngularJS](quickstart-v1-angularjs-spa.md)<br /><br />[JavaScript](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) |  |
| <center>![API Web](./media/azure-ad-developers-guide/Web_API.png)<br />API Web</center> | [Vue d’ensemble](authentication-scenarios.md#web-application-to-web-api)<br /><br />[ASP.NET](quickstart-v1-dotnet-webapi.md)<br /><br />[Node.JS](quickstart-v1-nodejs-webapi.md) | &nbsp; |
| <center>![De service à service](./media/azure-ad-developers-guide/Service_App.png)<br />De service à service</center> | [Vue d’ensemble](authentication-scenarios.md#daemon-or-server-application-to-web-api)<br /><br />[.NET](sample-v1-code.md#daemon-applications-accessing-web-apis-with-the-applications-identity)|  |
|  |  |  |  |  |

## <a name="how-to-guides"></a>Procédures
Ces indications vous guident pas à pas dans l’exécution des tâches les plus courantes dans Azure AD.

|                                                                           |  |
|---------------------------------------------------------------------------| --- |
|[Inscription de l’application](quickstart-v1-integrate-apps-with-azure-ad.md)           | Comment inscrire une application dans Azure AD. |
|[Applications multilocataires](howto-convert-app-to-be-multi-tenant.md)    | Comment connecter n’importe quel compte professionnel Microsoft. |
|[Protocoles OAuth et OpenID Connect](v1-protocols-openid-connect-code.md)| Comment connecter des utilisateurs et appeler des API web en utilisant les protocoles d’authentification de Microsoft. |
|  |  |

## <a name="reference-topics"></a>Rubriques de référence
Les articles suivants fournissent des informations détaillées sur les API, les messages de protocole et les termes utilisés dans Azure AD.

|                                                                                   | |
| ----------------------------------------------------------------------------------| --- |
| [Bibliothèques d’authentification](active-directory-authentication-libraries.md)   | Vue d’ensemble des bibliothèques et des kits de développement logiciel fournis par Azure AD. |
| [Exemples de code](sample-v1-code.md)                                  | Une liste de tous les exemples de code Azure AD. |
| [Glossaire](developer-glossary.md)                                      | Terminologie et définitions des termes utilisés dans cette documentation. |
|  |  |


[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
