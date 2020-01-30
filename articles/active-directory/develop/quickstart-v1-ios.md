---
title: Se connecter et appeler Microsoft Graph à partir d’iOS | Azure
description: Découvrez comment connecter des utilisateurs et appeler l’API Microsoft Graph à partir d’une application iOS.
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: 42303177-9566-48ed-8abb-279fcf1e6ddb
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: quickstart
ms.date: 10/25/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: brandwe
ms.openlocfilehash: 0f612a6f59455162d0acdffc8e05201be0a730d9
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/23/2020
ms.locfileid: "76703777"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-an-ios-app-v10"></a>Démarrage rapide : Connecter des utilisateurs et appeler l'API Microsoft Graph à partir d'une application iOS (v1.0)

La [plateforme des identités Microsoft](v2-overview.md) est une évolution de la plateforme de développement Azure Active Directory (Azure AD). Elle permet aux développeurs de générer des applications qui connectent toutes les identités Microsoft et obtiennent des jetons pour appeler des API Microsoft comme Microsoft Graph ou des API que des développeurs ont créées.

[Microsoft Authentication Library (MSAL)](msal-overview.md) permet aux développeurs d’acquérir des jetons à partir du point de terminaison de la plateforme des identités Microsoft afin d’accéder aux API web sécurisées. La Bibliothèque d’authentification Active Directory (ADAL) s’intègre avec le point de terminaison d’Azure AD pour les développeurs (v1.0), tandis que la bibliothèque MSAL s’intègre avec le point de terminaison de la plateforme d’identités Microsoft (v2.0).

Pour les nouvelles applications macOS et iOS, nous vous recommandons d'utiliser la Plateforme d'identités Microsoft (v 2.0) et MSAL afin d'acquérir des jetons et d'accéder aux API web sécurisées : [Démarrage rapide : Connecter des utilisateurs et appeler l'API Microsoft Graph à partir d'une application iOS ou macOS](quickstart-v2-ios.md).
