---
title: Application mobile qui appelle des API - web vue d’ensemble | Plateforme d’identité Microsoft
description: Découvrez comment créer une application mobile par appels web API (vue d’ensemble)
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: dadobali
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: eb0acd1534bab11eb57a7aa0e695f192b5999ed2
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65076494"
---
# <a name="scenario-mobile-application-that-calls-web-apis"></a>Scénario : Application mobile appelle des API web

En savoir plus il que vous suffit de créer une application mobile qui appelle l’API web.

## <a name="prerequisites"></a>Conditions préalables

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>Prise en main

Créer votre première application mobile et essayez un démarrage rapide !

> [!div class="nextstepaction"]
> [Démarrage rapide : Acquérir un jeton et appeler des API Microsoft Graph à partir d’une application Android](./quickstart-v2-android.md)
>
> [Démarrage rapide : Acquérir un jeton et appeler des API Microsoft Graph à partir d’une application iOS](./quickstart-v2-ios.md)
>
> [Démarrage rapide : Acquérir un jeton et appeler des API Microsoft Graph à partir d’une application Android et le Xamarin iOS](https://github.com/Azure-Samples/active-directory-xamarin-native-v2)

## <a name="overview"></a>Présentation

Lorsque vous générez une application mobile, une expérience utilisateur personnalisée et transparente est essentielle.  Plateforme d’identité Microsoft permet aux développeurs mobiles faire exactement pour les utilisateurs iOS et Android. Votre application peut se connecter dans Azure AD, compte Microsoft personnel et les utilisateurs Azure AD B2C et acquérir des jetons pour appeler une API web en leur nom. Pour implémenter ces flux, nous allons utiliser Microsoft Authentication Library (MSAL) qui implémente la norme industrielle [flux de code d’autorisation OAuth2.0](v2-oauth2-auth-code-flow.md).

![Applications démon](./media/scenarios/mobile-app.svg)

Considérations sur les applications mobiles :

- ***L’expérience utilisateur est la clé***: Permettre aux utilisateurs d’afficher la valeur de votre application avant de demander pour la connexion et demander uniquement les autorisations nécessaires.
- ***Prise en charge toutes les configurations utilisateur***: Nombre d’utilisateurs métier mobiles situés sous l’accès conditionnel et les stratégies de conformité. Veillez à prendre en charge ces scénarios clés.
- ***Implémenter l’authentification unique (SSO)***: MSAL et la plateforme d’identité Microsoft simplifient l’activation de l’authentification unique via le navigateur du périphérique ou le Microsoft Authenticator (et le portail d’entreprise Intune sur Android).

## <a name="specifics"></a>Spécificités

Lorsque vous générez une application mobile sur la plateforme d’identité Microsoft, l’expérience de bout en bout présente quelques considérations :

- Selon la plateforme, sans aucune interaction de signature peut-être pas possible sur la première connexion. iOS, par exemple, exige que les applications pour afficher l’intervention de l’utilisateur lors de l’obtention des temps de l’authentification unique de la première via Microsoft Authenticator (et portail d’entreprise Intune sur Android).
- Sur iOS et Android, MSAL peut utiliser un navigateurs externes (qui peuvent être présentes sur votre application) pour connecter les utilisateurs. Cela peut être personnalisé pour utiliser des vues Web dans l’application à la place.
- N’utilisez jamais une clé secrète dans une application mobile, elle est accessible à tous les utilisateurs.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Inscription d’application](scenario-mobile-app-registration.md)
