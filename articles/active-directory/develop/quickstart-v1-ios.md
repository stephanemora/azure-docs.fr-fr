---
title: Créer une application iOS intégrée avec Azure AD pour la connexion à l’aide d’OAuth 2.0 | Microsoft Docs
description: Découvrez comment connecter des utilisateurs et appeler l’API Microsoft Graph à partir d’une application iOS.
services: active-directory
documentationcenter: ios
author: rwike77
manager: CelesteDG
editor: ''
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 58b78461c9e585cbba4902799543dadb0f013e85
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73149669"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-an-ios-app-v10"></a>Démarrage rapide : Connecter des utilisateurs et appeler l'API Microsoft Graph à partir d'une application iOS (v1.0)

La [plateforme des identités Microsoft](v2-overview.md) est une évolution de la plateforme de développement Azure Active Directory (Azure AD). Elle permet aux développeurs de générer des applications qui connectent toutes les identités Microsoft et obtiennent des jetons pour appeler des API Microsoft comme Microsoft Graph ou des API que des développeurs ont créées.

[Microsoft Authentication Library (MSAL)](msal-overview.md) permet aux développeurs d’acquérir des jetons à partir du point de terminaison de la plateforme des identités Microsoft afin d’accéder aux API web sécurisées. La Bibliothèque d’authentification Active Directory (ADAL) s’intègre avec le point de terminaison d’Azure AD pour les développeurs (v1.0), tandis que la bibliothèque MSAL s’intègre avec le point de terminaison de la plateforme d’identités Microsoft (v2.0).

Pour les nouvelles applications macOS et iOS, nous vous recommandons d'utiliser la Plateforme d'identités Microsoft (v 2.0) et MSAL afin d'acquérir des jetons et d'accéder aux API web sécurisées : [Démarrage rapide : Connecter des utilisateurs et appeler l'API Microsoft Graph à partir d'une application iOS ou macOS](quickstart-v2-ios.md).
