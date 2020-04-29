---
title: Obtenir un jeton pour une API web appelant des API web | Azure
titleSuffix: Microsoft identity platform
description: Découvrez comment générer une API web qui appelle des API web nécessitant l’acquisition d’un jeton pour l’application.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 79f8eb9e804502a7c0e61c18e4998fa05db10278
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80885138"
---
# <a name="a-web-api-that-calls-web-apis-acquire-a-token-for-the-app"></a>Une API web qui appelle des API web : Acquérir un jeton pour l’application

Après avoir généré un objet d’application cliente, utilisez-le pour acquérir un jeton qui vous servira à appeler une API web.

## <a name="code-in-the-controller"></a>Code dans le contrôleur

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Voici un exemple de code appelé dans les actions des contrôleurs d’API. Il appelle une API en aval nommée *todolist*.

```csharp
private async Task GetTodoList(bool isAppStarting)
{
 ...
 //
 // Get an access token to call the To Do service.
 //
 AuthenticationResult result = null;
 try
 {
  app = BuildConfidentialClient(HttpContext, HttpContext.User);
  result = await app.AcquireTokenSilent(Scopes, account)
                     .ExecuteAsync()
                     .ConfigureAwait(false);
 }
...
}
```

`BuildConfidentialClient()` est semblable au scénario présenté dans [Une API web qui appelle des API web : Configuration d’application](scenario-web-api-call-api-app-configuration.md). `BuildConfidentialClient()` instancie `IConfidentialClientApplication` avec un cache qui contient les informations d’un seul compte. Le compte est fourni par la méthode `GetAccountIdentifier`.

La méthode `GetAccountIdentifier` utilise les revendications qui sont associées à l’identité de l’utilisateur pour lequel l’API web a reçu le jeton JWT (JSON Web Token) :

```csharp
public static string GetMsalAccountId(this ClaimsPrincipal claimsPrincipal)
{
 string userObjectId = GetObjectId(claimsPrincipal);
 string tenantId = GetTenantId(claimsPrincipal);

 if (    !string.IsNullOrWhiteSpace(userObjectId)
      && !string.IsNullOrWhiteSpace(tenantId))
 {
  return $"{userObjectId}.{tenantId}";
 }

 return null;
}
```

# <a name="java"></a>[Java](#tab/java)
Voici un exemple de code appelé dans les actions des contrôleurs d’API. Il appelle l'API situé en aval, Microsoft Graph.

```java
@RestController
public class ApiController {

    @Autowired
    MsalAuthHelper msalAuthHelper;

    @RequestMapping("/graphMeApi")
    public String graphMeApi() throws MalformedURLException {

        String oboAccessToken = msalAuthHelper.getOboToken("https://graph.microsoft.com/.default");

        return callMicrosoftGraphMeEndpoint(oboAccessToken);
    }

}
```

# <a name="python"></a>[Python](#tab/python)

Une API web Python doit utiliser un intergiciel pour valider le jeton du porteur reçu du client. L'API web peut ensuite obtenir le jeton d'accès de l'API située en aval à l'aide de la bibliothèque MSAL Python en appelant la méthode [`acquire_token_on_behalf_of`](https://msal-python.readthedocs.io/en/latest/?badge=latest#msal.ConfidentialClientApplication.acquire_token_on_behalf_of). Aucun exemple illustrant ce flux avec MSAL Python n'est encore disponible.

---

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Une API web qui appelle des API web : Appeler une API](scenario-web-api-call-api-call-api.md)
