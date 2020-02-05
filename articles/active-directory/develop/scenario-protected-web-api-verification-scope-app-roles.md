---
title: Vérifier les étendues et les rôles d’application avec une API web protégée | Azure
titleSuffix: Microsoft identity platform
description: Découvrez comment créer une API web protégée et configurer le code de votre application.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 816a9620a3486b534f9293084b7c4f5b4f748033
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/28/2020
ms.locfileid: "76768118"
---
# <a name="protected-web-api-verify-scopes-and-app-roles"></a>API web protégée : Vérifier les étendues et les rôles d’application

Cet article décrit comment ajouter une autorisation à votre API web. Cette protection garantit que l’API est appelée uniquement par :

- Des applications pour le compte d’utilisateurs qui ont des étendues adaptées.
- Des applications de démon qui ont les rôles d’application appropriés.

> [!NOTE]
> Les extraits de code de cet article proviennent des exemples suivants, qui sont entièrement fonctionnels :
>
> - [Didacticiel incrémentiel sur les API ASP.NET Core](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/02352945c1c4abb895f0b700053506dcde7ed04a/1.%20Desktop%20app%20calls%20Web%20API/TodoListService/Controllers/TodoListController.cs#L37) sur GitHub
> - [Exemple d’API web ASP.NET](https://github.com/Azure-Samples/ms-identity-aspnet-webapi-onbehalfof/blob/dfd0115533d5a230baff6a3259c76cf117568bd9/TodoListService/Controllers/TodoListController.cs#L48)

Pour protéger une API web ASP.NET ou ASP.NET Core, vous devez ajouter l’attribut `[Authorize]` à l’un des éléments suivants :

- Le contrôleur proprement dit, si vous voulez que toutes les actions de contrôleur soient protégées
- L’action de contrôleur individuelle pour votre API

```csharp
    [Authorize]
    public class TodoListController : Controller
    {
     ...
    }
```

Mais cette protection n’est pas suffisante. Elle garantit uniquement qu’ASP.NET et ASP.NET Core valident le jeton. Votre API doit vérifier que le jeton utilisé pour appeler l’API est demandée avec les revendications attendues. Ces revendications doivent notamment être vérifiées :

- Les *étendues* si l’API est appelée pour le compte d’un utilisateur.
- Les *rôles d’application* si l’API peut être appelée à partir d’une application démon.

## <a name="verify-scopes-in-apis-called-on-behalf-of-users"></a>Vérifier les étendues dans les API appelées pour le compte d’utilisateurs

Si une application cliente appelle votre API pour le compte d’un utilisateur, l’API doit demander un jeton du porteur ayant des étendues spécifiques pour elle. Pour plus d’informations, consultez [Configuration de code | Jeton du porteur](scenario-protected-web-api-app-configuration.md#bearer-token).

```csharp
[Authorize]
public class TodoListController : Controller
{
    /// <summary>
    /// The web API will accept only tokens 1) for users, 2) that have the `access_as_user` scope for
    /// this API.
    /// </summary>
    const string scopeRequiredByAPI = "access_as_user";

    // GET: api/values
    [HttpGet]
    public IEnumerable<TodoItem> Get()
    {
        VerifyUserHasAnyAcceptedScope(scopeRequiredByAPI);
        // Do the work and return the result.
        ...
    }
...
}
```

La méthode `VerifyUserHasAnyAcceptedScope` effectue des étapes semblables aux suivantes :

- Vérifier qu’il existe une revendication nommée `http://schemas.microsoft.com/identity/claims/scope` ou `scp`.
- Vérifier que la revendication a une valeur qui contient l’étendue attendue par l’API.

```csharp
    /// <summary>
    /// When applied to a <see cref="HttpContext"/>, verifies that the user authenticated in the
    /// web API has any of the accepted scopes.
    /// If the authenticated user doesn't have any of these <paramref name="acceptedScopes"/>, the
    /// method throws an HTTP Unauthorized error with a message noting which scopes are expected in the token.
    /// </summary>
    /// <param name="acceptedScopes">Scopes accepted by this API</param>
    /// <exception cref="HttpRequestException"/> with a <see cref="HttpResponse.StatusCode"/> set to 
    /// <see cref="HttpStatusCode.Unauthorized"/>
    public static void VerifyUserHasAnyAcceptedScope(this HttpContext context,
                                                     params string[] acceptedScopes)
    {
        if (acceptedScopes == null)
        {
            throw new ArgumentNullException(nameof(acceptedScopes));
        }
        Claim scopeClaim = HttpContext?.User
                                      ?.FindFirst("http://schemas.microsoft.com/identity/claims/scope");
        if (scopeClaim == null || !scopeClaim.Value.Split(' ').Intersect(acceptedScopes).Any())
        {
            context.Response.StatusCode = (int)HttpStatusCode.Unauthorized;
            string message = $"The 'scope' claim does not contain scopes '{string.Join(",", acceptedScopes)}' or was not found";
            throw new HttpRequestException(message);
        }
    }
```

L’[exemple de code](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/02352945c1c4abb895f0b700053506dcde7ed04a/Microsoft.Identity.Web/Resource/ScopesRequiredByWebAPIExtension.cs#L47) précédent s’applique à ASP.NET Core. Pour ASP.NET, remplacez simplement `HttpContext.User` par `ClaimsPrincipal.Current` et remplacez le type de revendication `"http://schemas.microsoft.com/identity/claims/scope"` par `"scp"`. Consultez également l’extrait de code présenté plus loin dans cet article.

## <a name="verify-app-roles-in-apis-called-by-daemon-apps"></a>Vérifier les rôles d’application dans les API appelées par des applications démon

Si votre API web est appelée par une [application de démon](scenario-daemon-overview.md), cette application doit exiger une autorisation d’application à votre API web. Comme indiqué dans [Exposition des autorisations d’application (rôles d’application)](https://docs.microsoft.com/azure/active-directory/develop/scenario-protected-web-api-app-registration#exposing-application-permissions-app-roles), votre API expose de telles autorisations. Le rôle d’application `access_as_application` en est un exemple.

Vous devez maintenant demander à votre API de vérifier que le jeton qu’elle reçoit contient la revendication `roles` et que cette revendication a la valeur attendue. Le code de vérification est similaire au code qui vérifie les autorisations déléguées, excepté que votre action de contrôleur teste les rôles au lieu des étendues :

```csharp
[Authorize]
public class TodoListController : ApiController
{
    public IEnumerable<TodoItem> Get()
    {
        ValidateAppRole("access_as_application");
        ...
    }
```

La méthode `ValidateAppRole` peut ressembler à ce qui suit :

```csharp
private void ValidateAppRole(string appRole)
{
    //
    // The `role` claim tells you what permissions the client application has in the service.
    // In this case, we look for a `role` value of `access_as_application`.
    //
    Claim roleClaim = ClaimsPrincipal.Current.FindFirst("roles");
    if (roleClaim == null || !roleClaim.Value.Split(' ').Contains(appRole))
    {
        throw new HttpResponseException(new HttpResponseMessage
        { StatusCode = HttpStatusCode.Unauthorized,
            ReasonPhrase = $"The 'roles' claim does not contain '{appRole}' or was not found"
        });
    }
}
}
```

Cette fois-ci, l’extrait de code est destiné à ASP.NET. Pour ASP.NET Core, remplacez simplement `ClaimsPrincipal.Current` par `HttpContext.User` et remplacez le nom de revendication `"roles"` par `"http://schemas.microsoft.com/identity/claims/roles"`. Consultez aussi l’extrait de code présenté précédemment dans cet article.

### <a name="accepting-app-only-tokens-if-the-web-api-should-be-called-only-by-daemon-apps"></a>Acceptation de jetons d’application uniquement si l’API web doit être appelée uniquement par des applications de démon

Les utilisateurs peuvent également utiliser des revendications de rôles dans des modèles d’affectation d’utilisateurs, comme indiqué dans [Guide pratique pour ajouter des rôles d’application dans votre application et les recevoir dans le jeton](howto-add-app-roles-in-azure-ad-apps.md). Si les rôles peuvent être attribués aux deux, vérifier les rôles permet aux applications de se connecter en tant qu’utilisateurs et aux utilisateurs de se connecter en tant qu’applications. Nous vous recommandons de déclarer des rôles différents pour les utilisateurs et les applications afin d’éviter cette confusion.

Si vous voulez que seules les applications démon appellent votre API web, ajoutez la condition indiquant que le jeton est un jeton d’application uniquement quand vous validez le rôle d’application.

```csharp
string oid = ClaimsPrincipal.Current.FindFirst("oid")?.Value;
string sub = ClaimsPrincipal.Current.FindFirst("sub")?.Value;
bool isAppOnlyToken = oid == sub;
```

La vérification de la condition inverse autorise uniquement les applications qui connectent un utilisateur à appeler votre API.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Passer en production](scenario-protected-web-api-production.md)
