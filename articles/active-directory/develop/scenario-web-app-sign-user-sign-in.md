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
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: be7801515355452306cd5e7afa709a0681c7c314
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68562202"
---
# <a name="web-app-that-signs-in-users---sign-in"></a>Application web qui connecte des utilisateurs - connexion

Découvrez comment ajouter une connexion au code de votre application web pour connecter les utilisateurs.

## <a name="sign-in"></a>Connexion

Le code que nous avons vu dans l’article précédent ([Configuration du code de l’application](scenario-web-app-sign-user-app-configuration.md)) est la seule chose que vous devez implémenter pour la déconnexion. Une fois que l’utilisateur est connecté à votre application, vous devez leur donner la possibilité de se déconnecter. ASP.NET Core gère la déconnexion pour vous.

## <a name="what-sign-out-involves"></a>Implications de la déconnexion

La déconnexion d’une application web ne consiste pas seulement à supprimer les informations sur le compte connecté de l’état de l’application web.
L’application web doit également rediriger l’utilisateur vers le point de terminaison `logout` de la plateforme d’identités Microsoft pour suivre la procédure de déconnexion. Lorsque votre application web redirige l’utilisateur vers le point de terminaison `logout`, ce dernier efface la session de l’utilisateur dans le navigateur. Si l’application n’accède pas au point de terminaison `logout`, l’utilisateur peut se réauthentifier auprès de votre application sans saisir de nouveau ses informations d’identification, puisqu’il dispose d’une session de connexion unique valide avec le point de terminaison de la plateforme d’identités Microsoft.

Pour en savoir plus, consultez la section [Envoi d’une demande de déconnexion](v2-protocols-oidc.md#send-a-sign-out-request) dans la documentation conceptuelle de la [plateforme d’identités Microsoft et du protocole OpenID Connect](v2-protocols-oidc.md).

## <a name="application-registration"></a>Inscription de l’application

Lors de l’inscription de l’application, vous devez avoir inscrit un **URI post-déconnexion**. Dans notre didacticiel, vous avez inscrit `https://localhost:44321/signout-oidc` dans le champ **URL de déconnexion** de la section **Paramètres avancés** sur la page **Authentification**. Pour plus d’informations, consultez la [procédure d’inscription de l’application web](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-1-MyOrg#register-the-webapp-app-webapp).

## <a name="aspnet-core-code"></a>Code ASP.NET Core

### <a name="signout-button"></a>Bouton de déconnexion

Le bouton de déconnexion est exposé dans `Views\Shared\_LoginPartial.cshtml` et uniquement affiché dans le cas d’un compte authentifié (c’est-à-dire lorsque l’utilisateur s’est précédemment connecté).

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

### <a name="signout-action-of-the-accountcontroller"></a>Action `Signout()` de `AccountController`

Le bouton de **déconnexion** de l’application web déclenche l’action `SignOut` sur le contrôleur `Account`. Dans les versions précédentes des modèles ASP.NET Core, le contrôleur `Account` était intégré à l’application web, mais ce n’est plus le cas, car il fait désormais partie du framework ASP.NET Core lui-même. 

Le code pour `AccountController` est disponible dans le référentiel ASP.NET Core à partir de [AccountController.cs](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Controllers/AccountController.cs). Le contrôle de compte a plusieurs fonctions :

- Définir un URI de redirection OpenID sur `/Account/SignedOut` afin que le contrôleur soit rappelé lorsque Azure AD a effectué la déconnexion
- Appeler `Signout()`, ce qui permet à l’intergiciel OpenIdConnect de contacter le point de terminaison `logout` de la plateforme d’identités Microsoft qui :

  - efface le cookie de session du navigateur, et
  - rappelle l’**URL de déconnexion** qui, par défaut, affiche la page de vue de déconnexion [SignedOut.html](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Pages/Account/SignedOut.cshtml) également fournie dans le cadre d’ASP.NET Core.

### <a name="intercepting-the-call-to-the-logout-endpoint"></a>Interception de l’appel à au point de terminaison `logout`

L’intergiciel OpenIdConnect ASP.NET Core permet à votre application d’intercepter l’appel au point de terminaison `logout` de la plateforme d’identités Microsoft en fournissant un événement OpenIdConnect nommé `OnRedirectToIdentityProviderForSignOut`. L’application web l’utilise pour tenter d’éviter de montrer la boîte de dialogue de sélection de compte à l’utilisateur lors de la déconnexion. L’interception s’effectue dans l’élément `AddAzureAdV2Authentication` de la `Microsoft.Identity.Web` bibliothèque réutilisable. Voir [StartupHelpers.cs L58-L66](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/b87a1d859ff9f9a4a98eb7b701e6a1128d802ec5/Microsoft.Identity.Web/StartupHelpers.cs#L58-L66)

```CSharp
public static IServiceCollection AddAzureAdV2Authentication(this IServiceCollection services,
                                                            IConfiguration configuration)
{
    ...
    services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
    {
        ...
        options.Authority = options.Authority + "/v2.0/";
        ...
        // Attempt to avoid displaying the select account dialog when signing out
        options.Events.OnRedirectToIdentityProviderForSignOut = async context =>
        {
            var user = context.HttpContext.User;
            context.ProtocolMessage.LoginHint = user.GetLoginHint();
            context.ProtocolMessage.DomainHint = user.GetDomainHint();
            await Task.FromResult(0);
        };
    }
}
```

## <a name="aspnet-code"></a>Code ASP.NET

Dans ASP.NET, la déconnexion est déclenchée par la méthode SignOut() sur un contrôleur (par exemple, AccountController). Cette méthode ne fait pas partie de l’infrastructure ASP.NET (contrairement à ASP.NET Core) et n’utilise pas async. Par conséquent :

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
 MsalAppBuilder.ClearUserTokenCache();
 Response.Redirect("/");
}
```

## <a name="protocol"></a>Protocole

Si vous ne souhaitez pas utiliser ASP.NET Core ou ASP.NET, vous pouvez consulter la documentation du protocole, qui est disponible auprès d’[Open ID Connect](./v2-protocols-oidc.md).

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Passer en production](scenario-web-app-sign-user-production.md)
