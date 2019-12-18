---
title: Générer une application de bureau appelant des API web - Plateforme d’identités Microsoft | Azure
description: Apprenez à générer une application de bureau qui appelle des API web (vue d’ensemble)
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
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: 545012629686e1fe3ece8a48ed852542e09e54fe
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74965515"
---
# <a name="scenario-desktop-app-that-calls-web-apis"></a>Scénario : Application de bureau qui appelle des API web

Découvrez tout ce que vous devez savoir pour générer une application de bureau qui appelle des API web

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>Prise en main

Si ce n’est pas déjà fait, créez votre première application en suivant le Guide de démarrage rapide du bureau .NET, le guide de démarrage rapide d’UWP ou le guide de démarrage rapide de l’application native macOS :

> [!div class="nextstepaction"]
> [Démarrage rapide : Acquérir un jeton et appeler l’API Microsoft Graph à partir d’une application de bureau Windows](./quickstart-v2-windows-desktop.md)


> [!div class="nextstepaction"]
> [Démarrage rapide : Acquérir un jeton et appeler l’API Microsoft Graph à partir d’une application UWP](./quickstart-v2-uwp.md)

> [!div class="nextstepaction"]
> [Démarrage rapide : Acquérir un jeton et appeler l’API Microsoft Graph à partir d’une application native macOS](./quickstart-v2-ios.md)

## <a name="overview"></a>Vue d'ensemble

Vous écrivez une application de bureau et vous voulez connecter des utilisateurs à votre application et appeler des API Web telles que Microsoft Graph, d’autres API Microsoft ou votre propre API Web. Vous avez plusieurs possibilités :

- Vous pouvez utiliser l’acquisition de jetons interactive :

  - Si votre application de bureau prend en charge les contrôles graphiques, par exemple s’il s’agit d’une application Windows.Form, d’une application WPF ou d’une application native macOS.
  - S’il s’agit d’une application .NET Core et que vous acceptez que l’interaction d’authentification avec Azure AD se produise dans le navigateur système.

- Pour les applications hébergées sous Windows, il est également possible pour les applications s’exécutant sur des ordinateurs reliés à un domaine Windows ou AAD relié à un domaine Windows d’acquérir un jeton en silence en utilisant l’authentification Windows intégrée.
- Enfin, et bien que ce ne soit pas recommandé, vous pouvez utiliser Nom d’utilisateur/Mot de passe dans les applications clientes publiques. Il est toujours nécessaire dans certains scénarios (comme DevOps), mais prenez garde, car son utilisation peut imposer des contraintes à votre application. Par exemple, il ne peut pas se connecter à un utilisateur qui a besoin d’effectuer une authentification multifacteur (accès conditionnel). De plus, votre application ne bénéficiera pas de l’authentification unique (SSO).

  Elle est également contraire aux principes de l’authentification moderne et n’est fournie que pour des raisons patrimoniales.

  ![Application de bureau](media/scenarios/desktop-app.svg)

- Si vous écrivez un outil en ligne de commande portable, probablement une application .NET Core fonctionnant sous Linux ou Mac, et si vous acceptez que l’authentification soit déléguée au navigateur système, vous serez en mesure d’utiliser l’authentification interactive. (.Net Core ne fournit pas encore de [navigateur web](https://aka.ms/msal-net-uses-web-browser) et, par conséquent, l’authentification se produit dans le navigateur système.) Sinon, la meilleure option dans ce cas consiste à utiliser le flux de code d’appareil. Ce flux est également utilisé pour les applications sans navigateur, telles que les applications IoT

  ![Application sans navigateur](media/scenarios/device-code-flow-app.svg)

## <a name="specifics"></a>Spécificités

Les applications de bureau ont un certain nombre de spécificités, qui dépendent principalement du fait que votre application utilise ou non l’authentification interactive.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Application de bureau – Inscription de l’application](scenario-desktop-app-registration.md)
