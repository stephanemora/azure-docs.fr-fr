---
title: Activer les options d’authentification de l’application web à l’aide d’Azure Active Directory B2C
description: Cet article décrit plusieurs façons d’activer les options d’authentification de l’application web.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 08/12/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: 50cdb5f171614c138427b358f2418b8b81751457
ms.sourcegitcommit: ef448159e4a9a95231b75a8203ca6734746cd861
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/30/2021
ms.locfileid: "123220322"
---
# <a name="enable-authentication-options-in-a-web-app-by-using-azure-ad-b2c"></a>Activer les options d’authentification dans une application web avec Azure AD B2C 

Cet article décrit les méthodes permettant d’activer, de personnaliser et d’améliorer l’expérience d’authentification Azure Active Directory B2C (Azure AD B2C) pour votre application web. 

Avant de commencer, lisez les articles suivants pour vous familiariser avec les concepts : 
* [Configurer l’authentification dans un exemple d’application web](configure-authentication-sample-web-app.md)
* [Activez l’authentification dans votre propre application web](enable-authentication-web-application.md).

[!INCLUDE [active-directory-b2c-app-integration-custom-domain](../../includes/active-directory-b2c-app-integration-custom-domain.md)]

Pour utiliser un domaine personnalisé et l’ID de votre locataire dans l’URL d’authentification, suivez les instructions fournies dans [Activer les domaines personnalisés](custom-domain.md). Dans le dossier racine du projet, ouvrez le fichier *appsettings.json*. Ce fichier contient des informations sur votre fournisseur d’identité Azure AD B2C.

Dans le fichier *appsettings.json*, faites ce qui suit :

- Mettez à jour l'entrée `Instance` avec votre domaine personnalisé.
- Mettez à jour l'entrée `Domain` avec votre [ID de locataire](tenant-management.md#get-your-tenant-id). Pour plus d’informations, consultez [Utiliser l’ID de locataire](custom-domain.md#optional-use-tenant-id).

Le code JSON suivant montre les paramètres de l’application avant la modification : 

```JSON
"AzureAdB2C": {
  "Instance": "https://contoso.b2clogin.com",
  "Domain": "tenant-name.onmicrosoft.com",
  ...
}
```  

Le code JSON suivant montre les paramètres de l’application après la modification : 

```JSON
"AzureAdB2C": {
  "Instance": "https://login.contoso.com",
  "Domain": "00000000-0000-0000-0000-000000000000",
  ...
}
``` 

## <a name="support-advanced-scenarios"></a>Scénarios avancés d’assistance

La méthode `AddMicrosoftIdentityWebAppAuthentication` de l’API Microsoft Identity Platform permet aux développeurs d’ajouter du code pour des scénarios d’authentification avancés ou de s’abonner à des événements OpenIdConnect. Par exemple, vous pouvez vous abonner à OnRedirectToIdentityProvider, ce qui vous permet de personnaliser la requête d’authentification que votre application envoie à Azure AD B2C.

Pour prendre en charge les scénarios avancés, ouvrez le fichier *Startup.cs* et, dans la fonction `ConfigureServices`, remplacez le `AddMicrosoftIdentityWebAppAuthentication` par l’extrait de code suivant : 

```csharp
// Configuration to sign-in users with Azure AD B2C

//services.AddMicrosoftIdentityWebAppAuthentication(Configuration, "AzureAdB2C");

services.AddAuthentication(OpenIdConnectDefaults.AuthenticationScheme)
        .AddMicrosoftIdentityWebApp(options =>
{
    Configuration.Bind("AzureAdB2C", options);
    options.Events ??= new OpenIdConnectEvents();
    options.Events.OnRedirectToIdentityProvider += OnRedirectToIdentityProviderFunc;
});
```

Le code précédent ajoute l’événement OnRedirectToIdentityProvider avec une référence à la méthode `OnRedirectToIdentityProviderFunc`. Ajoutez l’extrait de code suivant dans la classe `Startup.cs`.

```csharp
private async Task OnRedirectToIdentityProviderFunc(RedirectContext context)
{
    // Custom code here
    
    // Don't remove this line
    await Task.CompletedTask.ConfigureAwait(false);
}
```

Vous pouvez transmettre des paramètres entre votre contrôleur et la fonction `OnRedirectToIdentityProvider` à l’aide de paramètres de contexte. 

[!INCLUDE [active-directory-b2c-app-integration-login-hint](../../includes/active-directory-b2c-app-integration-login-hint.md)]

1. Si vous utilisez une stratégie personnalisée, ajoutez la revendication d’entrée requise comme décrit dans [Configurer la connexion directe](direct-signin.md#prepopulate-the-sign-in-name). 
1. Complétez la procédure de [prise en charge des scénarios avancés](#support-advanced-scenarios).
1. Ajoutez la ligne de code suivante à la fonction `OnRedirectToIdentityProvider` :
    
    ```csharp
    private async Task OnRedirectToIdentityProviderFunc(RedirectContext context)
    {
      context.ProtocolMessage.LoginHint = "emily@contoso.com";
      
      // More code
      await Task.CompletedTask.ConfigureAwait(false);
    }
    ```

[!INCLUDE [active-directory-b2c-app-integration-domain-hint](../../includes/active-directory-b2c-app-integration-domain-hint.md)]

1. Vérifiez le nom de domaine de votre fournisseur d’identité externe. Pour plus d’informations, consultez [Rediriger la connexion vers un fournisseur social](direct-signin.md#redirect-sign-in-to-a-social-provider). 
1. Complétez la procédure de [prise en charge des scénarios avancés](#support-advanced-scenarios).
1. Dans la fonction `OnRedirectToIdentityProviderFunc`, ajoutez la ligne de code suivante à la fonction `OnRedirectToIdentityProvider` :
    
    ```csharp
    private async Task OnRedirectToIdentityProviderFunc(RedirectContext context)
    {
      context.ProtocolMessage.DomainHint = "facebook.com";
      
      // More code
      await Task.CompletedTask.ConfigureAwait(false);
    }
    ```


[!INCLUDE [active-directory-b2c-app-integration-ui-locales](../../includes/active-directory-b2c-app-integration-ui-locales.md)]

1. [Configurez la personnalisation de la langue](language-customization.md).
1. Complétez la procédure de [prise en charge des scénarios avancés](#support-advanced-scenarios).
1. Ajoutez la ligne de code suivante à la fonction `OnRedirectToIdentityProvider` :

    ```csharp
    private async Task OnRedirectToIdentityProviderFunc(RedirectContext context)
    {
      context.ProtocolMessage.UiLocales = "es";

      // More code
      await Task.CompletedTask.ConfigureAwait(false);
    }
    ```

[!INCLUDE [active-directory-b2c-app-integration-custom-parameters](../../includes/active-directory-b2c-app-integration-custom-parameters.md)]

1. Configurez l’élément [ContentDefinitionParameters](customize-ui-with-html.md#configure-dynamic-custom-page-content-uri).
1. Complétez la procédure de [prise en charge des scénarios avancés](#support-advanced-scenarios).
1. Ajoutez la ligne de code suivante à la fonction `OnRedirectToIdentityProvider` :
    
    ```csharp
    private async Task OnRedirectToIdentityProviderFunc(RedirectContext context)
    {
      context.ProtocolMessage.Parameters.Add("campaignId", "123");

      // More code
      await Task.CompletedTask.ConfigureAwait(false);
    }
    ```


[!INCLUDE [active-directory-b2c-app-integration-id-token-hint](../../includes/active-directory-b2c-app-integration-id-token-hint.md)]

1. Complétez la procédure de [prise en charge des scénarios avancés](#support-advanced-scenarios).
1. Dans votre stratégie personnalisée, définissez un [indicateur de jeton d’ID de profil technique](id-token-hint.md).
1. Ajoutez la ligne de code suivante à la fonction `OnRedirectToIdentityProvider` :
    
    ```csharp
    private async Task OnRedirectToIdentityProviderFunc(RedirectContext context)
    {
      // The idTokenHint variable holds your ID token 
      context.ProtocolMessage.IdTokenHint = idTokenHint

      // More code
      await Task.CompletedTask.ConfigureAwait(false);
    }
    ```
    
## <a name="account-controller"></a>Contrôleur de compte

Si vous souhaitez personnaliser les actions de *connexion*, *d’inscription* ou de *déconnexion*, nous vous encourageons à créer votre propre contrôleur. Le fait de disposer de votre propre contrôleur vous permet de transmettre des paramètres entre votre contrôleur et la bibliothèque d’authentification. Le `AccountController` fait partie du package NuGet `Microsoft.Identity.Web.UI`, qui gère les actions de connexion et de déconnexion. Vous pouvez trouver son implémentation dans la [bibliothèque Microsoft Identity Web](https://github.com/AzureAD/microsoft-identity-web/blob/master/src/Microsoft.Identity.Web.UI/Areas/MicrosoftIdentity/Controllers/AccountController.cs). 

### <a name="add-the-account-controller"></a>Ajouter le contrôleur de compte

Dans le projet Visual Studio, cliquez avec le bouton droit sur le dossier *Contrôleurs* et ajoutez un nouveau **Contrôleur**. Sélectionnez **MVC - Contrôleur vide**, puis fournissez le nom **MyAccountController.cs**.

L’extrait de code suivant montre un `MyAccountController` personnalisé avec l’action de *SignIn*.

```csharp
using System;
using System.Collections.Generic;
using System.Diagnostics;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authentication;
using Microsoft.AspNetCore.Authentication.OpenIdConnect;
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Mvc;


namespace mywebapp.Controllers
{
    [AllowAnonymous]
    [Area("MicrosoftIdentity")]
    [Route("[area]/[controller]/[action]")]
    public class MyAccountController : Controller
    {

        [HttpGet("{scheme?}")]
        public IActionResult SignIn([FromRoute] string scheme)
        {
            scheme ??= OpenIdConnectDefaults.AuthenticationScheme;
            var redirectUrl = Url.Content("~/");
            var properties = new AuthenticationProperties { RedirectUri = redirectUrl };
            return Challenge(properties, scheme);
        }

    }
}
```

Dans l’affichage *_LoginPartial.cshtml*, modifiez le lien de connexion à votre contrôleur.

```html
<form method="get" asp-area="MicrosoftIdentity" asp-controller="MyAccount" asp-action="SignIn">
```

### <a name="pass-the-azure-ad-b2c-policy-id"></a>Transmettre l’ID de stratégie Azure AD B2C

L’extrait de code suivant montre un `MyAccountController` personnalisé avec les actions **SignIn** et **SignUp**. L’action transmet un paramètre nommé `policy` à la bibliothèque d’authentification. Cela vous permet de fournir l’ID de stratégie Azure AD B2C qui convient pour l’action spécifique.

```csharp
public IActionResult SignIn([FromRoute] string scheme)
{
    scheme ??= OpenIdConnectDefaults.AuthenticationScheme;
    var redirectUrl = Url.Content("~/");
    var properties = new AuthenticationProperties { RedirectUri = redirectUrl };
    properties.Items["policy"] = "B2C_1_SignIn";
    return Challenge(properties, scheme);
}

public IActionResult SignUp([FromRoute] string scheme)
{
    scheme ??= OpenIdConnectDefaults.AuthenticationScheme;
    var redirectUrl = Url.Content("~/");
    var properties = new AuthenticationProperties { RedirectUri = redirectUrl };
    properties.Items["policy"] = "B2C_1_SignUp";
    return Challenge(properties, scheme);
}
```

Dans l’affichage *_LoginPartial.cshtml*, remplacez la valeur `asp-controller` par `MyAccountController` pour tous les autres liens d’authentification, tels que l’inscription ou la modification du profil.

### <a name="pass-custom-parameters"></a>Transmettre les paramètres personnalisés

L’extrait de code suivant montre un `MyAccountController` personnalisé avec l’action de **SignIn**. L’action transmet un paramètre nommé `campaign_id` à la bibliothèque d’authentification.

```csharp
public IActionResult SignIn([FromRoute] string scheme)
{
    scheme ??= OpenIdConnectDefaults.AuthenticationScheme;
    var redirectUrl = Url.Content("~/");
    var properties = new AuthenticationProperties { RedirectUri = redirectUrl };
    properties.Items["policy"] = "B2C_1_SignIn";
    properties.Items["campaign_id"] = "1234";
    return Challenge(properties, scheme);
}
```

Complétez la procédure de [prise en charge des scénarios avancés](#support-advanced-scenarios), puis, dans la méthode `OnRedirectToIdentityProvider`, lisez le paramètre personnalisé :

```csharp
private async Task OnRedirectToIdentityProviderFunc(RedirectContext context)
{
    // Read the custom parameter
    var campaign_id = context.Properties.Items.FirstOrDefault(x => x.Key == "campaign_id").Value;

    // Add your custom code here
    if (campaign_id != null)
    {
        // Send parameter to authentication request
        context.ProtocolMessage.SetParameter("campaign_id", campaign_id);
    }
    
    await Task.CompletedTask.ConfigureAwait(false);
}
```

## <a name="secure-your-logout-redirect"></a>Sécuriser la redirection de déconnexion

Après la déconnexion, l’utilisateur est redirigé vers l’URI spécifié dans le paramètre `post_logout_redirect_uri`, quelles que soient les URL de réponse qui ont été spécifiées pour l’application. Cependant, si un `id_token_hint` valide est transmis et que l’option [Exiger un jeton d’ID dans les demandes de déconnexion](session-behavior.md#secure-your-logout-redirect) est activée, Azure AD B2C vérifie que la valeur de `post_logout_redirect_uri` correspond à l’un des URI de redirection configurés de l’application avant d’effectuer la redirection. Si aucune URL de réponse correspondante n’a été configurée pour l’application, un message d’erreur s’affiche et l’utilisateur n’est pas redirigé.

Pour prendre en charge une redirection de déconnexion sécurisée dans votre application, suivez d’abord les étapes décrites dans les sections [Contrôleur de compte](enable-authentication-web-application-options.md#add-the-account-controller) et [Prendre en charge les scénarios avancés](#support-advanced-scenarios). Suivez ensuite les étapes ci-dessous :

1. Dans le contrôleur `MyAccountController.cs`, ajoutez une action **SignOut** à l’aide de l’extrait de code suivant :

    ```csharp
    [HttpGet("{scheme?}")]
    public async Task<IActionResult> SignOutAsync([FromRoute] string scheme)
    {
        scheme ??= OpenIdConnectDefaults.AuthenticationScheme;

        //obtain the id_token
        var idToken = await HttpContext.GetTokenAsync("id_token");
        //send the id_token value to the authentication middleware
        properties.Items["id_token_hint"] = idToken;            

        return SignOut(properties,CookieAuthenticationDefaults.AuthenticationScheme,scheme);
    }
    ```

1. Dans la classe **Startup.cs**, analysez la valeur `id_token_hint` et ajoutez la valeur à la demande d’authentification. L’extrait de code suivant montre comment transmettre la valeur `id_token_hint` à la demande d’authentification :

    ```csharp
    private async Task OnRedirectToIdentityProviderFunc(RedirectContext context)
    {
        var id_token_hint = context.Properties.Items.FirstOrDefault(x => x.Key == "id_token_hint").Value;
        if (id_token_hint != null)
        {
            // Send parameter to authentication request
            context.ProtocolMessage.SetParameter("id_token_hint", id_token_hint);
        }

        await Task.CompletedTask.ConfigureAwait(false);
    }
    ```

1. Dans la fonction `ConfigureServices`, ajoutez l’option `SaveTokens` pour permettre aux **contrôleurs** d’accéder à la valeur `id_token` : 

    ```csharp
    services.AddAuthentication(OpenIdConnectDefaults.AuthenticationScheme)
        .AddMicrosoftIdentityWebApp(options =>
        {
            Configuration.Bind("AzureAdB2C", options);
            options.Events ??= new OpenIdConnectEvents();        
            options.Events.OnRedirectToIdentityProvider += OnRedirectToIdentityProviderFunc;
            options.SaveTokens = true;
        });
    ```

1. Dans le fichier de configuration **appsettings.json**, ajoutez le chemin d’accès de l’URI de redirection de déconnexion à la clé `SignedOutCallbackPath`.

    ```json
    "AzureAdB2C": {
      "Instance": "https://<your-tenant-name>.b2clogin.com",
      "ClientId": "<web-app-application-id>",
      "Domain": "<your-b2c-domain>",
      "SignedOutCallbackPath": "/signout/<your-sign-up-in-policy>",
      "SignUpSignInPolicyId": "<your-sign-up-in-policy>"
    }
    ```

Dans l’exemple ci-dessus, le **post_logout_redirect_uri** qui est passé dans la demande de déconnexion sera au format `https://your-app.com/signout/<your-sign-up-in-policy>`. Cette URL doit être ajoutée à l’URL de réponse de l’inscription d’application.

## <a name="role-based-access-control"></a>Contrôle d'accès en fonction d'un rôle

Avec l'[autorisation dans ASP.NET Core](/aspnet/core/security/authorization/introduction), vous pouvez vérifier si les utilisateurs sont autorisés à accéder à une ressource protégée à l’aide de l’une des méthodes suivantes : 
* [Autorisation basée sur des rôles](/aspnet/core/security/authorization/roles) 
* [Autorisation basée sur des revendications](/aspnet/core/security/authorization/claims) 
* [Autorisation basée sur une stratégie](/aspnet/core/security/authorization/policies)

Dans la méthode `ConfigureServices`, ajoutez la méthode `AddAuthorization`, qui ajoute le modèle d’autorisation. L’exemple qui suit permet de créer une clé nommée `EmployeeOnly`. La stratégie vérifie qu'il existe une revendication `EmployeeNumber`. La valeur de la revendication doit être l’un des ID suivants : 1, 2, 3, 4 ou 5.

```csharp
services.AddAuthorization(options =>
    {
        options.AddPolicy("EmployeeOnly", policy =>
              policy.RequireClaim("EmployeeNumber", "1", "2", "3", "4", "5"));
    });
```

Vous contrôlez l’autorisation dans ASP.NET Core à l’aide de [AuthorizeAttribute](/aspnet/core/security/authorization/simple) et de ses différents paramètres. Dans sa forme la plus basique, l’application de l’attribut `Authorize` à une page de contrôleur, d’action ou Razor, limite l’accès aux utilisateurs authentifiés de ce composant.

Les stratégies sont appliquées aux contrôleurs à l’aide de l'attribut `Authorize` avec le nom de la stratégie. Le code suivant limite l’accès à l'action `Claims` aux utilisateurs autorisés par la stratégie `EmployeeOnly` :

```csharp
[Authorize(Policy = "EmployeeOnly")]
public IActionResult Claims()
{
    return View();
}
```

## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur l’autorisation, consultez [Présentation de l’autorisation dans ASP.NET Core](/aspnet/core/security/authorization/introduction).
