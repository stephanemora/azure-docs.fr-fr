---
title: Passer en production une application web connectant des utilisateurs - Plateforme d’identités Microsoft | Azure
description: Apprenez à générer une application web qui connecte des utilisateurs (passage en production)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/17/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 47a45b52ac10a44b6efd54c41b3fec1e61a47a35
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "82181628"
---
# <a name="web-app-that-signs-in-users-move-to-production"></a>Application web qui connecte les utilisateurs : Passer en production

Maintenant que vous savez comment obtenir un jeton pour appeler des API web, découvrez comment passer en production.

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>Étapes suivantes

### <a name="troubleshooting"></a>Dépannage

> [!NOTE]
> Lorsque les utilisateurs se connectent à l’application web pour la première fois, ils doivent donner leur consentement. Toutefois, dans certaines organisations, les utilisateurs peuvent voir un message similaire à ce qui suit :
>
> Pour accéder aux ressources de votre organisation, *AppName a besoin d’autorisations que seul un administrateur peut accorder. Veuillez demander à un administrateur d’accorder une autorisation pour cette application avant de pouvoir l’utiliser.*
>
> Cela est dû au fait que votre administrateur client a **désactivé** la possibilité pour les utilisateurs de donner leur consentement. Dans ce cas, vous devez contacter vos administrateurs clients pour qu’ils effectuent un consentement d’administrateur pour les étendues requises par l’application.

### <a name="same-site"></a>Même site

Vérifier que vous comprenez les problèmes susceptibles d’affecter les nouvelles versions du navigateur Chrome

> [!div class="nextstepaction"]
> [Guide pratique pour gérer les changements de cookie SameSite dans le navigateur Chrome](howto-handle-samesite-cookie-changes-chrome-browser.md)

Le package NuGet Microsoft.Identity.Web gère les problèmes SameSite les plus courants.

### <a name="scenario-for-calling-web-apis"></a>Scénario d’appel d’API web

Lorsque votre application web connecte des utilisateurs, elle peut appeler des API web pour le compte des utilisateurs connectés. L’appel d’API web à partir de l’application web est l’objet du scénario suivant :

> [!div class="nextstepaction"]
> [Application web qui appelle des API web](scenario-web-app-call-api-overview.md)

## <a name="deep-dive-aspnet-core-web-app-tutorial"></a>Présentation approfondie : Didacticiel sur les applications web ASP.NET Core

En savoir plus sur les autres façons de connecter les utilisateurs avec ce didacticiel ASP.NET Core : 

> [!div class="nextstepaction"]
> [Permettez à vos applications web de connecter des utilisateurs et d’appeler des API avec la plateforme d’identité Microsoft pour les développeurs](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial) 

Ce didacticiel progressif propose un code prêt pour la production d’une application web, avec la procédure d’ajout de connexion aux comptes dans :

- votre organisation
- plusieurs organisations
- des comptes professionnels et scolaires ou des comptes Microsoft personnels
- [Azure AD B2C](https://aka.ms/aadb2c)
- Clouds nationaux

## <a name="sample-code-java-web-app"></a>Exemple de code : Applications web Java

En savoir plus sur l’application web Java à partir de cet exemple sur GitHub : 

> [!div class="nextstepaction"]
> [Une application web Java qui connecte les utilisateurs à la plateforme d’identités Microsoft et appelle Microsoft Graph](https://github.com/Azure-Samples/ms-identity-java-webapp)
