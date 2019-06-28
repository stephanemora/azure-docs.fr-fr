---
title: Application web qui appelle des API web (acquisition d’un jeton pour l’application) - Plateforme d’identités Microsoft
description: Découvrez comment générer une application web qui appelle des API web (acquisition d’un jeton pour l’application)
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
ms.openlocfilehash: 653db995000308bb3ef78a9183696cd9d8ed1056
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65074799"
---
# <a name="web-app-that-calls-web-apis---acquire-a-token-for-the-app"></a>Application web qui appelle des API web - acquisition d’un jeton pour l’application

Maintenant que vous avez généré l’objet d’application cliente, vous allez l’utiliser pour acquérir un jeton que vous utiliserez ensuite pour appeler des API web. Dans ASP.NET ou ASP.NET Core, l’appel d’une API web s’effectue dans le contrôleur. Voici en quoi cela consiste :

- Obtenir un jeton pour l’API web en utilisant le cache de jeton. Pour ce faire, vous appelez `AcquireTokenSilent`.
- Appeler l’API protégée avec le jeton d’accès.

## <a name="aspnet-core"></a>ASP.NET Core

Les méthodes de contrôleur sont protégées par un attribut `[Authorize]` qui force les utilisateurs à s’authentifier pour utiliser l’application web. Voici le code qui appelle Microsoft Graph

```CSharp
[Authorize]
public class HomeController : Controller
{
 ...
}
```

Voici un code simplifié de l’action du HomeController, qui obtient un jeton pour appeler Microsoft Graph.

```CSharp
public async Task<IActionResult> Profile()
{
 var application = BuildConfidentialClientApplication(HttpContext, HttpContext.User);
 string accountIdentifier = claimsPrincipal.GetMsalAccountId();
 string loginHint = claimsPrincipal.GetLoginHint();

 // Get the account
 IAccount account = await application.GetAccountAsync(accountIdentifier);

 // Special case for guest users as the Guest iod / tenant id are not surfaced.
 if (account == null)
 {
  var accounts = await application.GetAccountsAsync();
  account = accounts.FirstOrDefault(a => a.Username == loginHint);
 }

 AuthenticationResult result;
 result = await application.AcquireTokenSilent(new []{"user.read"}, account)
                            .ExecuteAsync();
 var accessToken = result.AccessToken;
 ...
 // use the access token to call a web API
}
```

Si vous souhaitez comprendre en détail l’ensemble du code requis pour ce scénario, consultez la phase 2 [2-1-Web App Calls Microsoft Graph](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-1-Call-MSGraph) (Application web appelant Microsoft Graph) du didacticiel [ms-identity-aspnetcore-webapp-tutorial](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial).

De nombreuses complexités viennent s’ajouter, par exemple :

- L’implémentation d’un cache de jeton pour l’application web (le didacticiel présente plusieurs implémentations)
- La suppression du compte du cache lorsque l’utilisateur se déconnecte
- L’appel de plusieurs API, notamment l’obtention du consentement incrémentiel

## <a name="aspnet"></a>ASP.NET

Le processus ASP.NET est similaire :

- Une action de contrôleur protégée par un attribut [Authorize] extrait l’identifiant de locataire et l’identifiant utilisateur du membre `ClaimsPrincipal` du contrôleur (ASP.NET utilise `HttpContext.User`).
- Il génère alors un MSAL.NET `IConfidentialClientApplication`.
- Il appelle la méthode `AcquireTokenSilent` de l’application cliente confidentielle. 

Le code est similaire au code présenté pour ASP.NET Core.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Appeler une API web](scenario-web-app-call-api-call-api.md)
