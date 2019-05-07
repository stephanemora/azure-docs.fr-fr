---
title: Application Web qui connecte les utilisateurs (vue d’ensemble) - plateforme d’identité Microsoft
description: Découvrez comment créer une application web qui connecte les utilisateurs (vue d’ensemble)
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
ms.openlocfilehash: b46df5e4ec8352b47f744a507fad702c37aa5fba
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65075099"
---
# <a name="scenario-web-app-that-signs-in-users"></a>Scénario : Application Web qui connecte les utilisateurs

En savoir plus il que vous suffit de créer une application web que les utilisateurs se connecte avec la plateforme Microsoft identity.

## <a name="prerequisites"></a>Conditions préalables

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>Prise en main

Si vous souhaitez créer vos premières applications de web (ASP.NET Core) portables qui se connectent les utilisateurs, suivez ce guide de démarrage rapide :

> [!div class="nextstepaction"]
> [Démarrage rapide : Application web ASP.NET Core que les utilisateurs se connecte](quickstart-v2-aspnet-core-webapp.md)

Si vous préférez rester avec ASP.NET, essayez le didacticiel suivant :

> [!div class="nextstepaction"]
> [Démarrage rapide : Application web ASP.NET Core que les utilisateurs se connecte](quickstart-v2-aspnet-webapp.md)

## <a name="overview"></a>Présentation

Vous ajoutez l’authentification à votre application web, afin qu’il peut connecter des utilisateurs. Ajout d’authentification permet à votre application web accéder aux informations de profil limité et, par exemple personnaliser l’expérience de que vous offrez à ses utilisateurs. Applications Web authentifient un utilisateur dans un navigateur web. Dans ce scénario, l’application web dirige le navigateur de l’utilisateur pour connecter ce dernier à Azure AD. Azure AD renvoie une réponse de connexion via le navigateur de l’utilisateur, celle-ci contenant des revendications relatives à l’utilisateur dans un jeton de sécurité. Tirer parti des utilisateurs de signature dans le [Open ID Connect](./v2-protocols-oidc.md) protocole standard lui-même simplifiée par l’utilisation de l’intergiciel (middleware) [bibliothèques](scenario-web-app-sign-user-app-configuration.md#libraries-used-to-protect-web-apps).

![Web application se connecte les utilisateurs](./media/scenario-webapp/scenario-webapp-signs-in-users.svg)

Comme une deuxième phase, vous pouvez également activer votre application pour appeler des API Web part de l’utilisateur connecté. Cette phase suivante est un autre scénario, vous trouverez dans [application Web appelle des API Web](scenario-web-app-call-api-overview.md)

> [!NOTE]
> Ajout de connexion à un site web application est sur la protection de l’application web et validation d’un jeton d’utilisateur, qui est ce que **intergiciel (middleware)** faire des bibliothèques. Ce scénario ne nécessite pas encore les bibliothèques d’authentification Microsoft (MSAL), qui sont sur l’acquisition d’un jeton pour appeler des API protégées par. Les bibliothèques d’authentification seront uniquement introduites dans le scénario de suivi lorsque l’application web doit appeler les API web.

## <a name="specifics"></a>Spécificités

- Lors de l’inscription de l’application, vous devez fournir un ou plusieurs (si vous déployez votre application à plusieurs emplacements) URI de réponse. Dans certains cas (Core ASP.NET/ASP.NET), vous devez activer le IDToken. Enfin, vous souhaiterez configurer un URI de déconnexion afin que votre application réagit aux utilisateurs hors connexion.
- Dans le code de votre application, vous devez fournir à l’autorité à laquelle vous application délégués connexion web. Vous souhaiterez peut-être personnaliser la validation du jeton (en particulier dans les scénarios ISV).
- Applications Web prennent en charge les types de comptes. Pour plus d’informations, consultez [pris en charge les types de comptes](v2-supported-account-types.md).

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Inscription d’application](scenario-web-app-sign-user-app-registration.md)
