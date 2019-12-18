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
ms.collection: M365-identity-device-management
ms.openlocfilehash: c6ebeed4a7806c013205d01621107ef65655e753
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74965464"
---
# <a name="scenario-mobile-application-that-calls-web-apis"></a>Scénario : Application mobile appelant des API web

Découvrez comment générer une application mobile qui appelle des API web.

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>Prise en main

Créez votre première application mobile et essayez un démarrage rapide.

> [!div class="nextstepaction"]
> [Démarrage rapide : Acquérir un jeton et appeler l’API Microsoft Graph à partir d’une application Android](./quickstart-v2-android.md)
>
> [Démarrage rapide : Acquérir un jeton et appeler l’API Microsoft Graph à partir d’une application iOS](./quickstart-v2-ios.md)
>
> [Démarrage rapide : Acquérir un jeton et appeler l’API Microsoft Graph à partir d’une application Xamarin iOS et Android](https://github.com/Azure-Samples/active-directory-xamarin-native-v2)

## <a name="overview"></a>Vue d'ensemble

Une expérience utilisateur transparente et personnalisée est essentielle pour les applications mobiles.  La Plateforme d’identité Microsoft permet aux développeurs mobiles de créer cette expérience pour les utilisateurs iOS et Android. Votre application peut se connecter à des utilisateurs Azure Active Directory (Azure AD), des utilisateurs de compte Microsoft personnels et des utilisateurs Azure AD B2C et acquérir des jetons pour appeler une API web en leur nom. Pour implémenter ces flux, nous allons utiliser la Bibliothèque d’authentification Microsoft (MSAL), qui met en œuvre le [flux de code d’autorisation OAuth2.0](v2-oauth2-auth-code-flow.md) qui est la norme du secteur.

![Applications démon](./media/scenarios/mobile-app.svg)

Éléments à prendre en considération pour les applications mobiles :

- **L’expérience utilisateur est essentielle** : Elle permet d’apprécier la valeur de votre application avant de s’y connecter et de demander uniquement les autorisations requises.
- **Prise en charge de toutes les configurations utilisateur** : Nombre d’utilisateurs professionnels mobiles sont soumis à des stratégies de conformité d’appareil et à l’accès conditionnel. Veillez à prendre en charge ces scénarios clés.
- **Implémenter l’authentification unique (SSO)** : La Bibliothèque d’authentification Microsoft (MSAL) et la Plateforme d’identités Microsoft simplifient l’activation de l’authentification unique via le navigateur de l’appareil ou Microsoft Authenticator (et le Portail d’entreprise Intune sur Android).

## <a name="specifics"></a>Spécificités

Gardez ces considérations à l’esprit lorsque vous générez une application mobile sur la Plateforme d’identités Microsoft :

- Selon la plateforme, une intervention de l’utilisateur peut être nécessaire la première fois que les utilisateurs se connectent. Par exemple, iOS exige des applications qu’elles affichent l’interaction de l’utilisateur lorsque ce dernier utilise l’authentification unique pour la première fois via Microsoft Authenticator (et le Portail d’entreprise Intune sur Android).
- Sur iOS et Android, la Bibliothèque d’authentification Microsoft (MSAL) peut utiliser un navigateur externe (qui peut apparaître en haut de votre application) pour connecter les utilisateurs. Vous pouvez personnaliser la configuration afin d’utiliser plutôt les vues Web dans l’application.
- N’utilisez jamais de clé secrète dans une application mobile. Elle est accessible à tous les utilisateurs.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Inscription d’application](scenario-mobile-app-registration.md)
