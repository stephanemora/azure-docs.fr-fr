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
ms.openlocfilehash: 316ab055a077b251e88421ab26997f8556a6e31f
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/25/2019
ms.locfileid: "74482473"
---
# <a name="scenario-web-app-that-signs-in-users"></a>Scénario : Application web qui connecte les utilisateurs

Découvrez comment créer une application web qui utilise la plateforme d’identités Microsoft pour connecter les utilisateurs.

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>Prise en main

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

Si vous souhaitez créer votre première application web (ASP.NET Core) portable qui connecte les utilisateurs, suivez ce guide de démarrage rapide :

> [!div class="nextstepaction"]
> [Démarrage rapide : application web ASP.NET Core qui connecte les utilisateurs](quickstart-v2-aspnet-core-webapp.md)

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

Si vous souhaitez savoir comment ajouter la connexion à une application web ASP.NET existante, essayez le démarrage rapide suivant :

> [!div class="nextstepaction"]
> [Démarrage rapide : Application web ASP.NET qui connecte les utilisateurs](quickstart-v2-aspnet-webapp.md)

# <a name="javatabjava"></a>[Java](#tab/java)

Si vous êtes développeur Java, essayez le démarrage rapide suivant :

> [!div class="nextstepaction"]
> [Démarrage rapide : Ajouter la connexion à Microsoft sur une application web Java](quickstart-v2-java-webapp.md)

# <a name="pythontabpython"></a>[Python](#tab/python)

Si vous développez avec Python, essayez le démarrage rapide suivant :

> [!div class="nextstepaction"]
> [Démarrage rapide : Ajouter la connexion à Microsoft sur une application web Python](quickstart-v2-python-webapp.md)

---

## <a name="overview"></a>Vue d'ensemble

Vous allez ajouter l’authentification à votre application web afin qu’elle puisse connecter des utilisateurs. L’ajout de l’authentification permet à votre application web d’accéder aux informations de profil limitées afin de personnaliser l’expérience des utilisateurs. 

Les applications web authentifient un utilisateur dans un navigateur web. Dans ce scénario, l’application web dirige le navigateur de l’utilisateur pour connecter ce dernier à Azure Active Directory (Azure AD). Azure AD renvoie une réponse de connexion via le navigateur de l’utilisateur, celle-ci contenant des revendications relatives à l’utilisateur dans un jeton de sécurité. Connecter des utilisateurs permet de tirer parti du protocole standard [Open ID Connect](./v2-protocols-oidc.md) simplifié par l’utilisation de [bibliothèques](scenario-web-app-sign-user-app-configuration.md#libraries-for-protecting-web-apps) d’intergiciels.

![Une application web connecte des utilisateurs](./media/scenario-webapp/scenario-webapp-signs-in-users.svg)

Lors d’une deuxième phase, vous pouvez permettre à l’application d’appeler des API web de la part de l’utilisateur connecté. Cette phase suivante est un autre scénario, que vous trouverez dans l’article [Application web qui appelle des API web](scenario-web-app-call-api-overview.md).

> [!NOTE]
> L’ajout de connexions à une application web consiste à protéger l’application web et à valider un jeton utilisateur, ce qui correspond à l’action des bibliothèques **d’intergiciels**. Dans le cas de .NET, ce scénario ne demande pas encore la bibliothèque MSAL (Microsoft Authentication Library), qui concerne l’acquisition d’un jeton pour appeler des API protégées. Les bibliothèques d’authentification sont introduites dans le scénario de suivi lorsque l’application web doit appeler les API web.

## <a name="specifics"></a>Spécificités

- Lors de l’inscription de l’application, vous devez fournir un ou plusieurs (si vous déployez l’application à différents emplacements) URI de réponse. Dans certains cas (ASP.NET et ASP.NET Core), vous devez activer le jeton d’ID. Enfin, vous souhaiterez configurer un URI de déconnexion afin que votre application réagisse aux déconnexions des utilisateurs.
- Dans le code de l’application, vous devez indiquer l’autorité à laquelle votre application web délègue la connexion. Vous souhaiterez peut-être personnaliser la validation du jeton (en particulier dans les scénarios partenaires).
- Les applications web prennent en charge tous les types de comptes. Pour plus d’informations, consultez l’article [Types de comptes pris en charge](v2-supported-account-types.md).

## <a name="next-steps"></a>Étapes suivantes

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

> [!div class="nextstepaction"]
> [Inscription d’application](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-app-registration?tabs=aspnetcore)

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

> [!div class="nextstepaction"]
> [Inscription d’application](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-app-registration?tabs=aspnet)

# <a name="javatabjava"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [Inscription d’application](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-app-registration?tabs=java)

# <a name="pythontabpython"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [Inscription d’application](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-app-registration?tabs=python)

---
