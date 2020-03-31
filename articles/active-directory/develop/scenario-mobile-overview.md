---
title: Générer une application mobile qui appelle des API web | Azure
titleSuffix: Microsoft identity platform
description: Apprenez à générer une application mobile qui appelle des API web (vue d’ensemble)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviwer: brandwe
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 6675d67299091325fcc3e12572a906716bf5b88d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77132414"
---
# <a name="scenario-mobile-application-that-calls-web-apis"></a>Scénario : Application mobile appelant des API web

Découvrez comment générer une application mobile qui appelle des API web.

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>Prise en main

Créez votre première application mobile et essayez un démarrage rapide.

> [!div class="nextstepaction"]
> [Démarrage rapide : Acquérir un jeton et appeler l’API Microsoft Graph à partir d’une application Android](./quickstart-v2-android.md)
>
> [Démarrage rapide : Acquérir un jeton et appeler l’API Microsoft Graph à partir d’une application iOS](./quickstart-v2-ios.md)
>
> [Démarrage rapide : Acquérir un jeton et appeler l’API Microsoft Graph à partir d’une application Xamarin iOS et Android](https://github.com/Azure-Samples/active-directory-xamarin-native-v2)

## <a name="overview"></a>Vue d’ensemble

Une expérience utilisateur transparente et personnalisée est essentielle pour les applications mobiles.  La Plateforme d’identité Microsoft permet aux développeurs mobiles de créer cette expérience pour les utilisateurs iOS et Android. Votre application peut connecter des utilisateurs Azure Active Directory utilisateurs (Azure AD), des utilisateurs de compte Microsoft personnels et des utilisateurs Azure AD B2C. Elle peut également acquérir des jetons pour appeler une API web en leur nom. Pour implémenter ces flux, nous allons utiliser la Bibliothèque d’authentification Microsoft (MSAL). MSAL implémente la norme [Flux du code d’autorisation OAuth2.0 ](v2-oauth2-auth-code-flow.md).

![Applications démon](./media/scenarios/mobile-app.svg)

Éléments à prendre en considération pour les applications mobiles :

- **L’expérience utilisateur est essentielle** : Autorisez les utilisateurs à voir la valeur de votre application avant de demander la connexion. Demandez uniquement les autorisations requises.
- **Prise en charge de toutes les configurations utilisateur** : De nombreux utilisateurs professionnels mobiles doivent adhérer à des stratégies d’accès conditionnel et à des stratégies de conformité des appareils. Veillez à prendre en charge ces scénarios clés.
- **Implémenter l’authentification unique (SSO)** : La Bibliothèque d’authentification Microsoft (MSAL) et la Plateforme d’identités Microsoft vous permettent d’activer l’authentification unique via le navigateur de l’appareil ou Microsoft Authenticator (et le Portail d’entreprise Intune sur Android).

## <a name="specifics"></a>Spécificités

Gardez les considérations suivantes à l’esprit lorsque vous générez une application mobile sur la Plateforme d’identités Microsoft :

- Selon la plateforme, une interaction de l’utilisateur peut être nécessaire la première fois que celui-ci se connecte. Par exemple, iOS exige des applications qu’elles affichent l’interaction de l’utilisateur lorsque ce dernier utilise l’authentification unique pour la première fois via Microsoft Authenticator (et le Portail d’entreprise Intune sur Android).
- Sur iOS et Android, la bibliothèque MSAL peut utiliser un navigateur externe pour connecter les utilisateurs. Le navigateur externe peut apparaître en haut de votre application. Vous pouvez personnaliser la configuration afin d’utiliser plutôt les vues Web dans l’application.
- N’utilisez jamais de clé secrète dans une application mobile. Dans ces applications, les secrets sont accessibles à tous les utilisateurs.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Inscription d’application](scenario-mobile-app-registration.md)
