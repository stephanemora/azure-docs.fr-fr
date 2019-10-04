---
title: Application web qui connecte des utilisateurs (vue d’ensemble) - Plateforme d’identités Microsoft
description: Découvrez comment générer une application web qui connecte des utilisateurs (vue d’ensemble)
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
ms.date: 09/17/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1f9b6d4ce4048b31f17b50184f90aed4d2a8ba81
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71086569"
---
# <a name="scenario-web-app-that-signs-in-users"></a>Scénario : Application web qui connecte les utilisateurs

Découvrez comment créer une application web qui connecte les utilisateurs à la plateforme d’identités Microsoft.

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>Prise en main

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

Si vous souhaitez créer vos premières applications web (ASP.NET Core) portables qui connectent les utilisateurs, suivez ce guide de démarrage rapide :

> [!div class="nextstepaction"]
> [Démarrage rapide : application web ASP.NET Core qui connecte les utilisateurs](quickstart-v2-aspnet-core-webapp.md)

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

Si vous souhaitez savoir comment ajouter la connexion à une application web ASP.NET héritée, essayez le tutoriel suivant :

> [!div class="nextstepaction"]
> [Démarrage rapide : application web ASP.NET qui connecte les utilisateurs](quickstart-v2-aspnet-webapp.md)

# <a name="javatabjava"></a>[Java](#tab/java)

Si vous êtes développeur Java, essayez le guide de démarrage rapide suivant :

> [!div class="nextstepaction"]
> [Démarrage rapide : Ajouter la connexion à Microsoft sur une application web Java](quickstart-v2-java-webapp.md)

# <a name="pythontabpython"></a>[Python](#tab/python)

Si vous développez avec Python, essayez :

> [!div class="nextstepaction"]
> [Démarrage rapide : Ajouter la connexion à Microsoft sur une application web Python](quickstart-v2-python-webapp.md)

---

## <a name="overview"></a>Vue d'ensemble

Vous allez ajouter l’authentification à votre application web afin qu’elle puisse connecter des utilisateurs. L’ajout de l’authentification permet à votre application web d’accéder aux informations de profil limitées et, par exemple, de personnaliser l’expérience que vous offrez à ses utilisateurs. Les applications web authentifient un utilisateur dans un navigateur web. Dans ce scénario, l’application web dirige le navigateur de l’utilisateur pour connecter ce dernier à Azure AD. Azure AD renvoie une réponse de connexion via le navigateur de l’utilisateur, celle-ci contenant des revendications relatives à l’utilisateur dans un jeton de sécurité. Connecter des utilisateurs permet de tirer parti du protocole standard [Open ID Connect](./v2-protocols-oidc.md) simplifié par l’utilisation de [bibliothèques](scenario-web-app-sign-user-app-configuration.md#libraries-used-to-protect-web-apps) d’intergiciels.

![Application web qui connecte des utilisateurs](./media/scenario-webapp/scenario-webapp-signs-in-users.svg)

Lors d’une deuxième phase, vous pouvez également permettre à l’application d’appeler des API web de la part de l’utilisateur connecté. Cette phase suivante est un autre scénario, que vous trouverez dans l’article [Application web qui appelle des API web](scenario-web-app-call-api-overview.md).

> [!NOTE]
> L’ajout de connexions à une application web consiste à protéger l’application web et à valider un jeton utilisateur, ce qui correspond à l’action des bibliothèques **d’intergiciels**. Dans le cas de .NET, ce scénario ne demande pas encore les bibliothèques MSAL (Microsoft Authentication Libraries), qui concernent l’acquisition d’un jeton pour appeler des API protégées. Les bibliothèques d’authentification sont uniquement introduites dans le scénario de suivi lorsque l’application web doit appeler les API web.

## <a name="specifics"></a>Spécificités

- Lors de l’inscription de l’application, vous devez fournir un ou plusieurs (si vous déployez l’application à différents emplacements) URI de réponse. Dans certains cas (ASP.NET/ASP.NET Core), vous devez activer le jeton d’ID. Enfin, vous souhaiterez configurer un URI de déconnexion afin que votre application réagisse aux déconnexions des utilisateurs.
- Dans le code de l’application, vous devez indiquer l’autorité à laquelle l’application web délègue la connexion. Vous souhaiterez peut-être personnaliser la validation du jeton (en particulier dans les scénarios d’éditeurs de logiciels indépendants).
- Les applications web prennent en charge tous les types de comptes. Pour plus d’informations, consultez l’article [Types de comptes pris en charge](v2-supported-account-types.md).

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Inscription d’application](scenario-web-app-sign-user-app-registration.md)
