---
title: Migrer vers la bibliothèque d’authentification Microsoft (MSAL)
titleSuffix: Microsoft identity platform
description: Découvrez les différences entre la bibliothèque d’authentification Microsoft (MSAL) et la bibliothèque d’authentification Azure AD (ADAL), et apprenez à effectuer une migration vers MSAL.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 02/27/2020
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 3af18eb09fd9906a0caaebda0b786795400467f3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78165395"
---
# <a name="migrate-applications-to-microsoft-authentication-library-msal"></a>Migrer des applications vers la bibliothèque d’authentification Microsoft (MSAL)

La bibliothèque d’authentification Microsoft (MSAL) et la bibliothèque d’authentification Azure AD (ADAL) sont utilisées pour authentifier des entités Azure AD et demander des jetons auprès d’Azure AD. Jusqu’à présent, la plupart des développeurs utilisent la plateforme Azure AD pour développeurs (v1.0) afin d’authentifier les identités Azure AD (comptes professionnels et scolaires) en demandant des jetons avec la Bibliothèque d’authentification Active Directory (ADAL). Grâce à MSAL :

- Vous pouvez authentifier un ensemble plus large d’identités Microsoft (identités Azure AD et comptes Microsoft, comptes de réseaux sociaux et locaux par le biais d’Azure AD B2C) via le point de terminaison de la plateforme d’identités Microsoft.
- vos utilisateurs bénéficieront de la meilleure expérience d’authentification unique ;
- Votre application peut activer le consentement incrémentiel et l’accès conditionnel est plus facile à prendre en charge.
- Vous bénéficiez de l’innovation.

**MSAL est désormais la bibliothèque d’authentification recommandée à utiliser avec la plateforme d’identité Microsoft**. Aucune nouvelle fonctionnalité ne sera implémentée sur ADAL. Les efforts se focalisent sur l'amélioration de MSAL.

Les articles suivants décrivent les différences entre les bibliothèques MSAL et ADAL, et vous aident à migrer vers MSAL :
- [Migrer vers MSAL.NET](msal-net-migration.md)
- [Migrer vers MSAL.js](msal-compare-msal-js-and-adal-js.md)
- [Migrer vers MSAL.Android](migrate-android-adal-msal.md)
- [Migrer vers MSAL.iOS / macOS](migrate-objc-adal-msal.md)
- [Migrer vers MSAL Python](migrate-python-adal-msal.md)
- [Migrer vers MSAL pour Java](migrate-adal-msal-java.md)
- [Migrer des applications Xamarin avec des répartiteurs vers MSAL.NET](msal-net-migration-ios-broker.md)
