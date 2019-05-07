---
title: Application Web qui appelle web API (acquérir un jeton pour l’application) - plateforme d’identité Microsoft
description: Découvrez comment créer une application Web qui appelle des API (l’acquisition d’un jeton pour l’application) web
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
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65074799"
---
# <a name="web-app-that-calls-web-apis---acquire-a-token-for-the-app"></a>Application Web qui appelle des API - web acquérir un jeton pour l’application

Maintenant vous avez généré objet d’application client, vous l’utiliserez pour acquérir un jeton que vous utiliserez ensuite pour appeler une API web. Dans ASP.NET ou ASP.NET Core, appeler une web QU'API est ensuite établie dans le contrôleur. Il concerne la :

- Obtention d’un jeton pour le web API en utilisant le cache de jetons. Pour ce faire, vous appelez `AcquireTokenSilent`
- Appeler l’API protégée avec le jeton d’accès

## <a name="aspnet-core"></a>ASP.NET Core

Les méthodes de contrôleur sont protégés par un `[Authorize]` attribut qui force les utilisateurs soient authentifiés pour utiliser l’application Web. Voici le code qui appelle Microsoft Graph

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

Si vous souhaitez comprendre en détails le total du code requis pour ce scénario, consultez la phase 2 [2-1-Web application appelle Microsoft Graph](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-1-Call-MSGraph) étape de la [ms-identity-aspnetcore-webapp-tutorial](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial) didacticiel

Il existe des nombreuses complexités supplémentaires telles que :

- implémentation d’un cache de jeton pour l’application Web (le didacticiel présente plusieurs implémentations)
- Suppression du compte à partir du cache lorsque l’utilisateur se connecte à la sortie
- Appeler plusieurs API, offrant notamment consentement incrémentiel

## <a name="aspnet"></a>ASP.NET

Choses sont similaires dans ASP.NET :

- Une action de contrôleur protégée par un attribut [Authorize], extrait l’ID client et ID utilisateur de la `ClaimsPrincipal` membre du contrôleur (ASP.NET utilise `HttpContext.User`)
- à partir de là, il génère un MSAL.NET `IConfidentialClientApplication`
- Appel de l’informatique le `AcquireTokenSilent` de l’application cliente confidentielle (méthode) 

le code est similaire au code affiché pour ASP.NET Core

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Appeler une API web](scenario-web-app-call-api-call-api.md)
