---
title: Application Web qui connecte les utilisateurs (se connecter) - plateforme d’identité Microsoft
description: Découvrez comment créer une application web qui se connecte les utilisateurs (connexion)
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
ms.openlocfilehash: 3fb7fbba7ec48da580d2a630ae51aa20b3307848
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65074619"
---
# <a name="web-app-that-signs-in-users---sign-in"></a>Application qui connecte les utilisateurs Web - Connectez-vous

Découvrez comment ajouter une connexion au code de votre application web que les utilisateurs se connecte.

## <a name="sign-in"></a>Connexion

Le code que nous avons dans l’article précédent [code configuration application](scenario-web-app-sign-user-app-configuration.md) est tout ce que vous devez implémenter la déconnexion. Une fois que l’utilisateur a connecté dans votre application, vous souhaiterez probablement leur permettre de se déconnecter. ASP.NET core gère la déconnexion pour vous.

## <a name="what-sign-out-involves"></a>Ce qui se déconnecter implique

Déconnexion à partir d’une application web est sur plus de la suppression des informations sur le compte de connexion à partir de l’état de l’application web.
L’application web doit également rediriger l’utilisateur V2.0 de plateforme d’identité Microsoft `logout` point de terminaison de déconnexion. Lorsque votre application web redirige l’utilisateur vers le `logout` point de terminaison, ce point de terminaison efface la session utilisateur à partir du navigateur. Si votre application n’a pas atteindre le `logout` point de terminaison, l’utilisateur serait s’authentifier de nouveau à votre application sans entrer leurs informations d’identification à nouveau, car ils doivent une session unique valide connectez-vous avec le point de terminaison v2.0 de plateforme Microsoft Identity.

Pour plus d’informations, consultez le [envoyer une demande de déconnexion](v2-protocols-oidc.md#send-a-sign-out-request) section dans le [v2.0 de plateforme Microsoft Identity et le protocole OpenID Connect](v2-protocols-oidc.md) documentation conceptuelle.

## <a name="application-registration"></a>Inscription de l’application

Lors de l’inscription de l’application, vous devez avoir inscrit un **valider l’URI de déconnexion**. Dans notre didacticiel, vous avez inscrit `https://localhost:44321/signout-oidc` dans le **URL de déconnexion** champ la **paramètres avancés** section dans le **authentification** page. Pour plus d’informations, consultez [ inscrire l’application webApp](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-1-MyOrg#register-the-webapp-app-webapp)

## <a name="aspnet-core-code"></a>Code ASP.NET Core

### <a name="signout-button"></a>Bouton de déconnexion

Le bouton de déconnexion est exposée dans `Views\Shared\_LoginPartial.cshtml` et uniquement affiché lorsqu’un compte authentifié (c'est-à-dire lorsque l’utilisateur s’est précédemment connecté).

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

### <a name="signout-action-of-the-accountcontroller"></a>`Signout()` action de la `AccountController`

En appuyant sur la **se déconnecter** bouton sur les déclencheurs d’application web le `SignOut` action sur le `Account` contrôleur. Dans les versions précédentes des modèles ASP.NET core, le `Account` contrôleur a été intégrée dans l’application web, mais cela n’est plus le cas car elle fait désormais partie du framework ASP.NET Core lui-même. 

Le code pour le `AccountController` est disponible à partir du référentiel de core ASP.NET à partir de [AccountController.cs](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Controllers/AccountController.cs). Le contrôle de compte :

- Définit un OpenID redirige URI à `/Account/SignedOut` afin que le contrôleur est rappelé lorsqu’Azure AD a effectué la déconnexion
- Appels `Signout()`, ce qui permet l’intergiciel (middleware) OpenIdConnect contacter la plateforme Microsoft identity `logout` point de terminaison qui :

  - Efface le cookie de session à partir du navigateur, et
  - Appels enfin rappelle le **URL de déconnexion**, qui) par défaut, affiche le signé page de vue [SignedOut.html](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Pages/Account/SignedOut.cshtml) également fourni dans le cadre d’ASP.NET Core.

### <a name="intercepting-the-call-to-the-logout-endpoint"></a>Interception de l’appel à la `logout` point de terminaison

L’intergiciel (middleware) ASP.NET Core OpenIdConnect permet à votre application intercepter l’appel à la plateforme Microsoft identity `logout` point de terminaison en fournissant un événement OpenIdConnect nommé `OnRedirectToIdentityProviderForSignOut`. L’application web utilise pour tenter d’éviter la boîte de dialogue Sélectionnez le compte qui sera présenté à l’utilisateur lors de la déconnexion. L’interception s’effectue le `AddAzureAdV2Authentication` de la `Microsoft.Identity.Web` bibliothèque réutilisable. Consultez [StartupHelpers.cs L58-L66](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/b87a1d859ff9f9a4a98eb7b701e6a1128d802ec5/Microsoft.Identity.Web/StartupHelpers.cs#L58-L66)

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

Dans ASP.NET, la déconnexion est déclenchée à partir de la méthode SignOut() sur un contrôleur (par exemple AccountController). Cette méthode ne fait pas partie de l’infrastructure ASP.NET (contrairement à ce que se passe-t-il dans ASP.NET Core) et n’utilise pas async, et c’est pourquoi il :

- envoie une demande de déconnexion OpenId
- Efface le cache
- effectue une redirection vers la page qu’il veut

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

## <a name="protocol"></a>Protocol

Si vous ne souhaitez pas utiliser ASP.NET Core ou ASP.NET, vous pouvez consulter la documentation du protocole, qui est disponible à partir de [Open ID Connect](./v2-protocols-oidc.md).

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Passer en production](scenario-web-app-sign-user-production.md)
