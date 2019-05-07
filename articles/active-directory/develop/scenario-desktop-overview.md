---
title: Application de bureau que les appels de web API (vue d’ensemble) - plateforme d’identité Microsoft
description: Découvrez comment créer une application de bureau que les appels de web API (vue d’ensemble)
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
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 44d31011ca70bbebaf994b5fb80a45eee8dbde40
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65076944"
---
# <a name="scenario-desktop-app-that-calls-web-apis"></a>Scénario : Application de bureau que des appels d’API web

En savoir plus il que vous suffit de générer une application de bureau qui appelle des API web

## <a name="prerequisites"></a>Conditions préalables

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>Prise en main

Si vous n’avez pas déjà, créez votre première application en suivant le démarrage rapide de bureau .NET ou le démarrage rapide UWP :

> [!div class="nextstepaction"]
> [Démarrage rapide : Acquérir un jeton et appeler des API Microsoft Graph à partir d’une application de bureau Windows](./quickstart-v2-windows-desktop.md)


> [!div class="nextstepaction"]
> [Démarrage rapide : Acquérir un jeton et appeler des API Microsoft Graph à partir d’une application UWP](./quickstart-v2-uwp.md)

## <a name="overview"></a>Présentation

Vous écrivez une application de bureau, et que vous souhaitez connecter des utilisateurs à votre application et appeler des API comme Microsoft Graph, autres APIs Microsoft ou votre propre API web web. Vous avez plusieurs possibilités :

- Si votre application de bureau prend en charge des contrôles graphiques, par exemple s’il s’agit d’une application Windows.Form ou une application WPF, vous pouvez utiliser l’acquisition des jetons interactive.
- Pour les applications Windows hébergées, il est également possible pour les applications exécutées sur des ordinateurs joints à un domaine Windows ou AAD joint pour acquérir un jeton en mode silencieux à l’aide de l’authentification Windows intégrée.
- Enfin, et bien qu’il n’est pas recommandé, vous pouvez utiliser le nom d’utilisateur/mot de passe dans les applications de client public. Il est toujours nécessaire dans certains scénarios (par exemple, les opérations de développement), mais prenez garde que l’utiliser seront imposent des contraintes sur votre application. Par exemple, il ne peut pas se connecter utilisateur ayant besoin d’effectuer une authentification multifacteur (accès conditionnel). Votre application ne bénéficient également d’authentification unique (SSO).

  Il est également sur les principes de l’authentification moderne et est fourni uniquement pour des raisons d’héritage.

  ![Application de bureau](media/scenarios/desktop-app.svg)

- Si vous écrivez un outil de ligne de commande portable - probablement une application .NET Core s’exécutant sur Linux ou Mac, vous ne pourrez pas utiliser ni l’authentification interactive (comme .NET Core ne fournit pas un [navigateur Web](https://aka.ms/msal-net-uses-web-browser)), ni intégré Authentification Windows. Dans ce cas, la meilleure option est d’utiliser des flux de code d’appareil. Ce flux est également utilisé pour les applications sans un navigateur, telles que les applications iOT

  ![Application browserless](media/scenarios/device-code-flow-app.svg)

## <a name="specifics"></a>Spécificités

Applications de bureau ont un nombre de spécificités, qui dépend principalement de si votre application utilise l’authentification interactive ou non.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Application de bureau - inscription de l’application](scenario-desktop-app-registration.md)
