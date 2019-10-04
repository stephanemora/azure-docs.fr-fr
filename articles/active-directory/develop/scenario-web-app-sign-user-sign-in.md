---
title: Application web qui connecte des utilisateurs (connexion) - Plateforme d’identités Microsoft
description: Apprenez à générer une application web qui connecte des utilisateurs (connexion).
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
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4ed61a09ffc76b4813dcb97330d3a1a436aa16eb
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71086456"
---
# <a name="web-app-that-signs-in-users---sign-in"></a>Application web qui connecte des utilisateurs - connexion

Découvrez comment ajouter une connexion au code de votre application web pour connecter les utilisateurs.

## <a name="sign-in"></a>Connexion

Le code que nous avons passé en revue dans l’article précédent [Configuration du code de l’application](scenario-web-app-sign-user-app-configuration.md) est tout ce dont vous avez besoin pour implémenter la connexion.
Une fois que l’utilisateur est connecté à votre application, vous devez leur donner la possibilité de se déconnecter. ASP.NET Core gère la déconnexion pour vous.

## <a name="what-sign-out-involves"></a>Implications de la déconnexion

La déconnexion d’une application web ne consiste pas seulement à supprimer les informations sur le compte connecté de l’état de l’application web.
L’application web doit également rediriger l’utilisateur vers le point de terminaison `logout` de la plateforme d’identités Microsoft pour suivre la procédure de déconnexion. Lorsque votre application web redirige l’utilisateur vers le point de terminaison `logout`, ce dernier efface la session de l’utilisateur dans le navigateur. Si l’application n’accède pas au point de terminaison `logout`, l’utilisateur peut se réauthentifier auprès de votre application sans saisir de nouveau ses informations d’identification, puisqu’il dispose d’une session de connexion unique valide avec le point de terminaison de la plateforme d’identités Microsoft.

Pour en savoir plus, consultez la section [Envoi d’une demande de déconnexion](v2-protocols-oidc.md#send-a-sign-out-request) dans la documentation conceptuelle de la [plateforme d’identités Microsoft et du protocole OpenID Connect](v2-protocols-oidc.md).

### <a name="application-registration"></a>Inscription de l’application

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

Lors de l’inscription de l’application, vous devez avoir inscrit un **URI post-déconnexion**. Dans notre didacticiel, vous avez inscrit `https://localhost:44321/signout-oidc` dans le champ **URL de déconnexion** de la section **Paramètres avancés** sur la page **Authentification**. Pour plus d’informations, consultez la [procédure d’inscription de l’application web](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-1-MyOrg#register-the-webapp-app-webapp).

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

Lors de l’inscription de l’application, vous devez avoir inscrit un **URI post-déconnexion**. Dans notre didacticiel, vous avez inscrit `https://localhost:44308/Account/EndSession` dans le champ **URL de déconnexion** de la section **Paramètres avancés** sur la page **Authentification**. Pour plus d’informations, voir [Inscrire l’application web](https://github.com/Azure-Samples/active-directory-dotnet-web-single-sign-out#register-the-service-app-webapp-distributedsignout-dotnet).

# <a name="javatabjava"></a>[Java](#tab/java)

Lors de l’inscription de l’application, vous inscrivez un **URI Post-déconnexion**. Dans notre didacticiel, vous avez inscrit `http://localhost:8080/msal4jsample/` dans le champ **URL de déconnexion** de la section **Paramètres avancés** sur la page **Authentification**.

# <a name="pythontabpython"></a>[Python](#tab/python)

Au cours de l’inscription de l’application, vous n’avez pas besoin d’inscrire d’URL de déconnexion. L’exemple n’implémente pas de déconnexion globale

---

### <a name="sign-out-button"></a>Bouton Déconnexion

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

Dans ASP.NET Core, le bouton Déconnexion est exposé dans `Views\Shared\_LoginPartial.cshtml` et ne s’affiche que quand il y a un compte authentifié (c’est-à-dire lorsque l’utilisateur s’est précédemment connecté).

```html
@using Microsoft.Identity.Web
@if (User.Identity.IsAuthenticated)
{
    <ul class="nav navbar-nav navbar-right">
        <li class="navbar-text">Hello @User.GetDisplayName()!</li>
        <li><a asp-area="AzureAD" asp-controller="Account" asp-action="SignOut">Sign out</a></li>
    </ul>
}
else
{
    <ul class="nav navbar-nav navbar-right">
        <li><a asp-area="AzureAD" asp-controller="Account" asp-action="SignIn">Sign in</a></li>
    </ul>
}
```

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

Dans ASP.NET MVC, le bouton Déconnexion est exposé dans `Views\Shared\_LoginPartial.cshtml` et ne s’affiche que quand il y a un compte authentifié (c’est-à-dire lorsque l’utilisateur s’est précédemment connecté).

```html
@if (Request.IsAuthenticated)
{
    <text>
        <ul class="nav navbar-nav navbar-right">
            <li class="navbar-text">
                Hello, @User.Identity.Name!
            </li>
            <li>
                @Html.ActionLink("Sign out", "SignOut", "Account")
            </li>
        </ul>
    </text>
}
else
{
    <ul class="nav navbar-nav navbar-right">
        <li>@Html.ActionLink("Sign in", "SignIn", "Account", routeValues: null, htmlAttributes: new { id = "loginLink" })</li>
    </ul>
}
```

# <a name="javatabjava"></a>[Java](#tab/java)

Dans notre guide de démarrage rapide Java, le bouton Déconnexion se trouve dans le fichier main/resources/templates/auth_page.html.

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<body>

<form action="/msal4jsample/sign_out">
    <input type="submit" value="Sign out">
</form>
...
```

# <a name="pythontabpython"></a>[Python](#tab/python)

Dans le guide de démarrage rapide Python, le bouton Déconnexion se trouve dans le fichier [templates/display.html](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e1199b4c3cdcb637cf0d8306832efbd85492e123/templates/display.html#L18-L20).

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Acquire Token Result </title>
</head>
<body>
    {% if cond  %}
        <p1><b>Your information</b> </p1>
        <table>
        {% for key, value in auth_result.items() %}
           <tr>
                <th> {{ key }} </th>
                <td> {{ value }} </td>
           </tr>
        {% endfor %}
        </table>
        <form action="/logout" >
            <input type="submit" value=" Logout"/>
        </form>
    {% else %}
        <p1><b> {{auth_result}} </b> </p1>
        <form action="/authenticate" >
            <input type="submit" value=" Sign-in"/>
        </form>
    {% endif %}
</body>
</html>
```

---

### <a name="signout-action-of-the-controller"></a>Action `Signout()` du contrôleur

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

Dans ASP.NET, le bouton **Déconnexion** de l’application web déclenche l’action `SignOut` sur le contrôleur `AccountController`. Dans les versions précédentes des modèles ASP.NET Core, le contrôleur `Account` était intégré avec l’application web, mais ce n’est plus le cas, car il fait désormais partie du framework ASP.NET Core.

Le code pour `AccountController` est disponible dans le référentiel ASP.NET Core à partir de [AccountController.cs](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Controllers/AccountController.cs). Le contrôle de compte a plusieurs fonctions :

- Définir un URI de redirection OpenID sur `/Account/SignedOut` afin que le contrôleur soit rappelé quand Azure AD a effectué la déconnexion
- Appeler `Signout()`, ce qui permet à l’intergiciel OpenIdConnect de contacter le point de terminaison `logout` de la plateforme d’identités Microsoft qui :

  - efface le cookie de session du navigateur, et
  - appelle finalement l’**URL de déconnexion** qui, par défaut, affiche la page de vue de déconnexion [SignedOut.html](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Pages/Account/SignedOut.cshtml) également fournie dans le cadre d’ASP.NET Core.

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

Dans ASP.NET, la déconnexion est déclenchée par la méthode `SignOut()` sur un contrôleur (par exemple, AccountController). Cette méthode ne fait pas partie de l’infrastructure ASP.NET (contrairement à ASP.NET Core). Elle n’utilise pas `async`. C’est pourquoi :

- Elle envoie une demande de déconnexion OpenId.
- Elle efface le cache.
- Elle effectue une redirection vers la page souhaitée.

```CSharp
/// <summary>
/// Send an OpenID Connect sign-out request.
/// </summary>
public void SignOut()
{
 HttpContext.GetOwinContext()
            .Authentication
            .SignOut(CookieAuthenticationDefaults.AuthenticationType);
 Response.Redirect("/");
}
```

# <a name="javatabjava"></a>[Java](#tab/java)

Dans Java, la déconnexion est gérée en appelant directement le point de terminaison de déconnexion de la plateforme d’identités Microsoft et en fournissant la valeur post_logout_redirect_uri.

```Java
@RequestMapping("/msal4jsample/sign_out")
    public void signOut(HttpServletRequest httpRequest, HttpServletResponse response) throws IOException {

        httpRequest.getSession().invalidate();

        String endSessionEndpoint = "https://login.microsoftonline.com/common/oauth2/v2.0/logout";

        String redirectUrl = "http://localhost:8080/msal4jsample/";
        response.sendRedirect(endSessionEndpoint + "?post_logout_redirect_uri=" +
                URLEncoder.encode(redirectUrl, "UTF-8"));
    }
```

# <a name="pythontabpython"></a>[Python](#tab/python)

```Python
@app.route("/logout")
def logout():
    return flask.redirect(flask.url_for('index'))
```

---

### <a name="intercepting-the-call-to-the-logout-endpoint"></a>Interception de l’appel à au point de terminaison `logout`

L’URI Post-déconnexion permet aux applications de participer à la déconnexion globale.

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

L’intergiciel OpenIdConnect ASP.NET Core permet à votre application d’intercepter l’appel au point de terminaison `logout` de la plateforme d’identités Microsoft en fournissant un événement OpenIdConnect nommé `OnRedirectToIdentityProviderForSignOut`. Pour obtenir un exemple illustrant comment s’abonner à cet événement (pour effacer le cache de jeton), voir [Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L151-L156](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/faa94fd49c2da46b22d6694c4f5c5895795af26d/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L151-L156).

```CSharp
               // Handling the global sign-out
                options.Events.OnRedirectToIdentityProviderForSignOut = async context =>
                {
                    // Forget about the signed-in user
                };
```

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

Dans ASP.NET, vous déléguez à l’intergiciel (middleware) l’exécution de la déconnexion, en effaçant le cookie de session :

```CSharp
public class AccountController : Controller
{
 ...
 public void EndSession()
 {
  Request.GetOwinContext().Authentication.SignOut();
  Request.GetOwinContext().Authentication.SignOut(Microsoft.AspNet.Identity.DefaultAuthenticationTypes.ApplicationCookie);
  this.HttpContext.GetOwinContext().Authentication.SignOut(CookieAuthenticationDefaults.AuthenticationType);
 }
}
```

# <a name="javatabjava"></a>[Java](#tab/java)

Dans notre guide de démarrage rapide Java, l’URI de redirection post-déconnexion affiche uniquement la page index.html. 

# <a name="pythontabpython"></a>[Python](#tab/python)

Dans notre guide de démarrage rapide Python, l’URI de redirection post-déconnexion affiche uniquement la page index.html.

---

## <a name="protocol"></a>Protocol

Pour en savoir plus sur la déconnexion, voir la documentation du protocole, disponible dans [Open ID Connect](./v2-protocols-oidc.md).

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Passer en production](scenario-web-app-sign-user-production.md)
