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
ms.date: 07/15/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 56bcc4e4936371b58d78f6de5ce4c2d25fbf614d
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94442801"
---
# <a name="a-web-api-that-calls-web-apis-acquire-a-token-for-the-app"></a>Une API web qui appelle des API web : Acquérir un jeton pour l’application

Après avoir généré un objet d’application cliente, utilisez-le pour acquérir un jeton qui vous servira à appeler une API web.

## <a name="code-in-the-controller"></a>Code dans le contrôleur

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

*Microsoft.Identity.Web* ajoute des méthodes d’extension qui fournissent des services pratiques pour appeler Microsoft Graph ou une API web en aval. Ces méthodes sont expliquées en détail dans [Une API web qui appelle des API web : Appeler une API](scenario-web-api-call-api-call-api.md). Avec ces méthodes d’assistance, vous n’avez pas besoin d’acquérir manuellement un jeton.

Toutefois, si vous souhaitez acquérir manuellement un jeton, le code suivant montre un exemple d’utilisation de *Microsoft.Identity.Web* pour le faire dans un contrôleur d’API. Il appelle une API en aval nommée *todolist*.
Pour obtenir un jeton permettant d’appeler l’API en aval, injectez le service `ITokenAcquisition` par injection de dépendance dans le constructeur de votre contrôleur (ou votre constructeur de page, si vous utilisez Blazor), puis utilisez-le dans vos actions de contrôleur, en obtenant un jeton pour l’utilisateur (`GetAccessTokenForUserAsync`) ou pour l’application elle-même (`GetAccessTokenForAppAsync`) dans le cas d’un scénario basé sur un démon.

```csharp
[Authorize]
public class MyApiController : Controller
{
    /// <summary>
    /// The web API will accept only tokens 1) for users, 2) that have the `access_as_user` scope for
    /// this API.
    /// </summary>
    static readonly string[] scopeRequiredByApi = new string[] { "access_as_user" };

     static readonly string[] scopesToAccessDownstreamApi = new string[] { "api://MyTodolistService/access_as_user" };

    private readonly ITokenAcquisition _tokenAcquisition;

    public MyApiController(ITokenAcquisition tokenAcquisition)
    {
        _tokenAcquisition = tokenAcquisition;
    }

    public IActionResult Index()
    {
        HttpContext.VerifyUserHasAnyAcceptedScope(scopeRequiredByApi);

        string accessToken = _tokenAcquisition.GetAccessTokenForUserAsync(scopesToAccessDownstreamApi);
        return await callTodoListService(accessToken);
    }
}
```

Pour plus d’informations sur la méthode `callTodoListService`, consultez [Une API web qui appelle des API web : Appeler une API](scenario-web-api-call-api-call-api.md).

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

Une API web Python doit utiliser un middleware pour valider le jeton du porteur reçu du client. L’API web peut alors obtenir le jeton d’accès de l’API située en aval à l’aide de la bibliothèque MSAL Python en appelant la méthode [`acquire_token_on_behalf_of`](https://msal-python.readthedocs.io/en/latest/?badge=latest#msal.ConfidentialClientApplication.acquire_token_on_behalf_of). Aucun exemple illustrant ce flux avec MSAL Python n’est encore disponible.

---

## <a name="next-steps"></a>Étapes suivantes

Passez à l’article suivant de ce scénario, [Appeler une API](scenario-web-api-call-api-call-api.md).
