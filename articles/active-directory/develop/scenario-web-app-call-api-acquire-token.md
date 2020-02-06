---
title: Obtenir un jeton dans une application web qui appelle des API web – Plateforme d’identités Microsoft | Azure
description: Découvrez comment acquérir un jeton pour une application web qui appelle des API web
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
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: abf7d800eda376c21dfdd672032ddb65e27355be
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/26/2020
ms.locfileid: "76759072"
---
# <a name="a-web-app-that-calls-web-apis-acquire-a-token-for-the-app"></a>Application web qui appelle des API web : Acquérir un jeton pour l’application

Vous avez généré l’objet de votre application cliente. À présent, vous allez l’utiliser pour acquérir un jeton afin d’appeler une API web. Dans ASP.NET ou ASP.NET Core, l’appel d’une API web s’effectue dans le contrôleur :

- Obtenir un jeton pour l’API web en utilisant le cache de jetons. Pour obtenir ce jeton, vous appelez la méthode `AcquireTokenSilent`.
- Appelez l’API protégée, en lui transmettant le jeton d’accès en tant que paramètre.

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

Les méthodes de contrôleur sont protégées par un attribut `[Authorize]` qui force les utilisateurs à s’authentifier pour utiliser l’application web. Voici le code qui appelle Microsoft Graph :

```csharp
[Authorize]
public class HomeController : Controller
{
 readonly ITokenAcquisition tokenAcquisition;

 public HomeController(ITokenAcquisition tokenAcquisition)
 {
  this.tokenAcquisition = tokenAcquisition;
 }

 // Code for the controller actions (see code below)

}
```

Le service `ITokenAcquisition` est injecté par ASP.NET à l’aide de l’injection de dépendances.

Voici le code simplifié pour l’action du `HomeController`, qui obtient un jeton pour appeler Microsoft Graph :

```csharp
public async Task<IActionResult> Profile()
{
 // Acquire the access token.
 string[] scopes = new string[]{"user.read"};
 string accessToken = await tokenAcquisition.GetAccessTokenOnBehalfOfUserAsync(scopes);

 // Use the access token to call a protected web API.
 HttpClient client = new HttpClient();
 client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);
 string json = await client.GetStringAsync(url);
}
```

Pour mieux comprendre le code nécessaire dans ce scénario, consultez la phase 2 ([2-1-Web App Calls Microsoft Graph](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-1-Call-MSGraph)) [Application web appelant Microsoft Graph] du tutoriel [ms-identity-aspnetcore-webapp-tutorial](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial).

Il existe d’autres variantes complexes, telles que :

- Appels de plusieurs API.
- Traitement du consentement incrémentiel et de l’accès conditionnel.

Ces étapes avancées sont abordées dans le chapitre 3 du tutoriel [3-WebApp-multi-APIs](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/3-WebApp-multi-APIs).

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

Le code pour ASP.NET est similaire au code présenté pour ASP.NET Core :

- Une action de contrôleur, protégée par un attribut [Authorize], extrait l’ID de locataire et l’identifiant utilisateur du membre `ClaimsPrincipal` du contrôleur. (ASP.NET utilise `HttpContext.User`.)
- Il génère alors un objet `IConfidentialClientApplication` MSAL.NET.
- Enfin, il appelle la méthode `AcquireTokenSilent` de l’application cliente confidentielle.

# <a name="javatabjava"></a>[Java](#tab/java)

Dans l’exemple Java, le code qui appelle une API se trouve dans la méthode getUsersFromGraph dans [AuthPageController.java#L62](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthPageController.java#L62).

La méthode tente d’appeler `getAuthResultBySilentFlow`. Si l’utilisateur a besoin de consentir à d’autres étendues, le code traite l’objet `MsalInteractionRequiredException` pour interroger l’utilisateur.

```java
@RequestMapping("/msal4jsample/graph/me")
public ModelAndView getUserFromGraph(HttpServletRequest httpRequest, HttpServletResponse response)
        throws Throwable {

    IAuthenticationResult result;
    ModelAndView mav;
    try {
        result = authHelper.getAuthResultBySilentFlow(httpRequest, response);
    } catch (ExecutionException e) {
        if (e.getCause() instanceof MsalInteractionRequiredException) {

            // If the silent call returns MsalInteractionRequired, redirect to authorization endpoint
            // so user can consent to new scopes.
            String state = UUID.randomUUID().toString();
            String nonce = UUID.randomUUID().toString();

            SessionManagementHelper.storeStateAndNonceInSession(httpRequest.getSession(), state, nonce);

            String authorizationCodeUrl = authHelper.getAuthorizationCodeUrl(
                    httpRequest.getParameter("claims"),
                    "User.Read",
                    authHelper.getRedirectUriGraph(),
                    state,
                    nonce);

            return new ModelAndView("redirect:" + authorizationCodeUrl);
        } else {

            mav = new ModelAndView("error");
            mav.addObject("error", e);
            return mav;
        }
    }

    if (result == null) {
        mav = new ModelAndView("error");
        mav.addObject("error", new Exception("AuthenticationResult not found in session."));
    } else {
        mav = new ModelAndView("auth_page");
        setAccountInfo(mav, httpRequest);

        try {
            mav.addObject("userInfo", getUserInfoFromGraph(result.accessToken()));

            return mav;
        } catch (Exception e) {
            mav = new ModelAndView("error");
            mav.addObject("error", e);
        }
    }
    return mav;
}
// Code omitted here
```

# <a name="pythontabpython"></a>[Python](#tab/python)

Dans l’exemple Python, le code qui appelle Microsoft Graph se trouve dans [app.py#L53-L62](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/48637475ed7d7733795ebeac55c5d58663714c60/app.py#L53-L62).

Le code tente d’obtenir un jeton à partir du cache de jetons. Ensuite, après avoir défini l’en-tête d’autorisation, il appelle l’API web. S’il ne peut pas obtenir de jeton, il reconnecte l’utilisateur.

```python
@app.route("/graphcall")
def graphcall():
    token = _get_token_from_cache(app_config.SCOPE)
    if not token:
        return redirect(url_for("login"))
    graph_data = requests.get(  # Use token to call downstream service.
        app_config.ENDPOINT,
        headers={'Authorization': 'Bearer ' + token['access_token']},
        ).json()
    return render_template('display.html', result=graph_data)
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Appeler une API web](scenario-web-app-call-api-call-api.md)
