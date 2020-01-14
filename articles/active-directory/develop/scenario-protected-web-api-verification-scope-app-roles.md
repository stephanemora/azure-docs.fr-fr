---
title: Vérifier les étendues et les rôles d’application avec l’API web protégée | Azure
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2eb9cdf68bf5103776d50db28e9e6facc89c9278
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75423688"
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

Pour protéger une API web ASP.NET/ASP.NET Core, vous devez ajouter l’attribut `[Authorize]` sur l’un de ces éléments :

- Le contrôleur proprement dit, si vous souhaitez protéger toutes les actions du contrôleur
- L’action de contrôleur individuelle pour votre API

```csharp
    [Authorize]
    public class TodoListController : Controller
    {
     ...
    }
```

Mais cette protection n’est pas suffisante. Elle garantit uniquement que ASP.NET/ASP.NET Core valide le jeton. Votre API doit vérifier que le jeton utilisé pour appeler votre API web a été demandé avec les revendications attendues, notamment :

- Les *étendues*, si l’API est appelée pour le compte d’un utilisateur.
- Les *rôles d’application*, si l’API peut être appelée à partir d’une application de démon.

## <a name="verifying-scopes-in-apis-called-on-behalf-of-users"></a>Vérification des étendues dans les API appelées pour le compte d’utilisateurs

Si votre API est appelée par une application cliente pour le compte d’un utilisateur, elle doit demander un jeton du porteur ayant des étendues spécifiques pour l’API. (Consultez [Configuration de code | Jeton du porteur](scenario-protected-web-api-app-configuration.md#bearer-token).)

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

La méthode `VerifyUserHasAnyAcceptedScope` doit vérifier ce qui suit :

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

Cet [exemple de code](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/02352945c1c4abb895f0b700053506dcde7ed04a/Microsoft.Identity.Web/Resource/ScopesRequiredByWebAPIExtension.cs#L47) est pour ASP.NET Core. Pour ASP.NET, remplacez simplement `HttpContext.User` par `ClaimsPrincipal.Current` et remplacez le type de revendication `"http://schemas.microsoft.com/identity/claims/scope"` par `"scp"`. (Voir aussi l’extrait de code plus loin dans cet article).

## <a name="verifying-app-roles-in-apis-called-by-daemon-apps"></a>Vérification des rôles d’application dans les API appelées par des applications de démon

Si votre API web est appelée par une [application de démon](scenario-daemon-overview.md), cette application doit exiger une autorisation d’application à votre API web. Nous avons vu dans [Exposition des autorisations d’application (rôles d’application)](https://docs.microsoft.com/azure/active-directory/develop/scenario-protected-web-api-app-registration#exposing-application-permissions-app-roles) que votre API expose de telles autorisations (par exemple, le rôle d’application `access_as_application`).
Vous devez maintenant demander à vos API de vérifier que le jeton reçu contient la revendication `roles` et que cette revendication a la valeur attendue. Le code effectuant cette vérification est similaire au code qui vérifie les autorisations déléguées, à ceci près que, au lieu de tester `scopes`, votre action de contrôleur teste `roles` :

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

La méthode `ValidateAppRole()` peut ressembler à ce qui suit :

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

Cette fois-ci, l’extrait de code est destiné à ASP.NET. Pour ASP.NET Core, remplacez simplement `ClaimsPrincipal.Current` par `HttpContext.User` et remplacez le nom de revendication `"roles"` par `"http://schemas.microsoft.com/identity/claims/roles"`. (Voir aussi l’extrait de code plus haut dans cet article).

### <a name="accepting-app-only-tokens-if-the-web-api-should-be-called-only-by-daemon-apps"></a>Acceptation de jetons d’application uniquement si l’API web doit être appelée uniquement par des applications de démon

La revendication `roles` est également utilisée pour les utilisateurs dans les modèles d’attribution d’utilisateurs. (Consultez [Guide pratique : Ajouter des rôles d’application dans votre application et les recevoir dans le jeton](howto-add-app-roles-in-azure-ad-apps.md).) Par conséquent, se contenter uniquement de vérifier les rôles autorise les applications à se connecter en tant qu’utilisateurs et inversement, si les rôles peuvent être attribués aux deux. Nous vous recommandons de déclarer des rôles différents pour les utilisateurs et les applications afin d’éviter cette confusion.

Si vous souhaitez autoriser uniquement des applications de démon à appeler votre API web, ajoutez une condition, lorsque vous validez le rôle d’application, indiquant que le jeton est un jeton d’application uniquement :

```csharp
string oid = ClaimsPrincipal.Current.FindFirst("oid")?.Value;
string sub = ClaimsPrincipal.Current.FindFirst("sub")?.Value;
bool isAppOnlyToken = oid == sub;
```

La vérification de la condition inverse autorise uniquement les applications qui connectent un utilisateur pour appeler votre API.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Passer en production](scenario-protected-web-api-production.md)
