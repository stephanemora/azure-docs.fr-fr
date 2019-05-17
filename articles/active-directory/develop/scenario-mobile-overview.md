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
ms.openlocfilehash: d9978905f8ae0719f6a77ca2351f2c43bbf94ee0
ms.sourcegitcommit: f013c433b18de2788bf09b98926c7136b15d36f1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/13/2019
ms.locfileid: "65550367"
---
# <a name="scenario-mobile-application-that-calls-web-apis"></a>Scénario : Application mobile appelle des API web

Apprendre tout ce que vous devez savoir pour créer une application mobile qui appelle l’API web.

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

Une expérience utilisateur transparente personnalisé est essentielle pour les applications mobiles.  Plateforme d’identité Microsoft permet aux développeurs mobiles créer cette expérience pour iOS et Android. Votre application peut se connecter à des utilisateurs Azure Active Directory (Azure AD), les utilisateurs de compte Microsoft personnels et les utilisateurs Azure AD B2C et acquérir des jetons pour appeler une API web en leur nom. Pour implémenter ces flux, nous allons utiliser Microsoft Authentication Library (MSAL), qui implémente la norme industrielle [flux de code d’autorisation OAuth2.0](v2-oauth2-auth-code-flow.md).

![Applications démon](./media/scenarios/mobile-app.svg)

Considérations pour les applications mobiles :

- **L’expérience utilisateur est la clé**: Permettre aux utilisateurs d’afficher la valeur de votre application avant de demander pour la connexion et demander uniquement les autorisations requises.
- **Prise en charge toutes les configurations utilisateur**: Nombre d’utilisateurs métier mobiles situés sous l’accès conditionnel et les stratégies de conformité. Veillez à prendre en charge ces scénarios clés.
- **Implémenter l’authentification unique (SSO)**: MSAL et la plateforme d’identité Microsoft simplifient l’activation de l’authentification unique via le navigateur du périphérique ou le Microsoft Authenticator (et le portail d’entreprise Intune sur Android).

## <a name="specifics"></a>Spécificités

N’oubliez pas ces considérations lorsque vous générez une application mobile sur la plateforme d’identité Microsoft :

- Selon la plateforme, une intervention de l’utilisateur peut être nécessaire la première fois que les utilisateurs se connectent. Par exemple, iOS exige que les applications pour afficher l’intervention de l’utilisateur lorsque vous utilisez l’authentification unique de la première fois à Microsoft Authenticator (et portail d’entreprise Intune sur Android).
- Sur iOS et Android, MSAL peut utiliser un navigateur externe (qui peut apparaître en haut de votre application) pour connecter les utilisateurs. Vous pouvez personnaliser la configuration pour utiliser les vues Web dans l’application à la place.
- N’utilisez jamais une clé secrète dans une application mobile. Il sera accessible à tous les utilisateurs.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Inscription d’application](scenario-mobile-app-registration.md)
