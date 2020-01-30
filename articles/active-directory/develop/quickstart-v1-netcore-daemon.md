---
title: Acquérir un jeton et appeler Microsoft Graph (console .NET Core) (v1.0) | Azure
description: Créez une application démon .NET qui s’intègre à Azure AD et appelle des API protégées par Azure AD en utilisant OAuth 2.0.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 07/17/2019
ms.author: jmprieur
ms.reviewer: ryanwi
ms.custom: aaddev
ms.openlocfilehash: bdcf0632110df23cbe91040f41535f51fb06ba44
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/23/2020
ms.locfileid: "76703794"
---
# <a name="quickstart-acquire-token-and-call-microsoft-graph-using-console-apps-identity-v10"></a>Démarrage rapide : Acquérir un jeton et appeler Microsoft Graph en utilisant l’identité de l’application console (v1.0)

La [plateforme des identités Microsoft](v2-overview.md) est une évolution de la plateforme de développement Azure Active Directory (Azure AD). Elle permet aux développeurs de générer des applications qui connectent toutes les identités Microsoft et obtiennent des jetons pour appeler des API Microsoft comme Microsoft Graph ou des API que des développeurs ont créées.

[Microsoft Authentication Library (MSAL)](msal-overview.md) permet aux développeurs d’acquérir des jetons à partir du point de terminaison de la plateforme des identités Microsoft afin d’accéder aux API web sécurisées. La Bibliothèque d’authentification Active Directory (ADAL) s’intègre avec le point de terminaison d’Azure AD pour les développeurs (v1.0), tandis que la bibliothèque MSAL s’intègre avec le point de terminaison de la plateforme d’identités Microsoft (v2.0).

## <a name="next-steps"></a>Étapes suivantes

Pour les nouvelles applications démons .NET, nous vous recommandons d’utiliser la plateforme des identités Microsoft (v 2.0) et MSAL pour acquérir des jetons et accéder aux API web sécurisées : [Démarrage rapide : Acquérir un jeton et appeler l’API Microsoft Graph à partir d’une application console à l’aide de l’identité de l’application](quickstart-v2-netcore-daemon.md)
