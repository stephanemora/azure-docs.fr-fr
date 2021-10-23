---
title: Activer une application web qui appelle des options d’API web à l’aide d’Azure Active Directory B2C
description: Cet article explique comment activer l’utilisation d’une application web qui appelle des options de l’API web de plusieurs façons.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 07/05/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: e6d43846307ddf97983a408c244ff3470bea2641
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130040870"
---
# <a name="configure-authentication-options-in-a-web-app-that-calls-a-web-api-by-using-azure-ad-b2c"></a>Configurer des options d’authentification dans une application web qui appelle une API web à l’aide d’Azure Active Directory B2C 

Cet article décrit les méthodes permettant de personnaliser et d’améliorer l’expérience d’authentification Azure Active Directory B2C (Azure AD B2C) pour votre application web qui appelle une API web. Avant de commencer, lisez les articles suivants pour vous familiariser avec les concepts : 
* [Configurer l’authentification dans un exemple d’application web](configure-authentication-sample-web-app-with-api.md)
* [Activez l’authentification dans votre propre application web](enable-authentication-web-app-with-api.md).

[!INCLUDE [active-directory-b2c-app-integration-custom-domain](../../includes/active-directory-b2c-app-integration-custom-domain.md)] 

Pour utiliser un domaine personnalisé et l’ID de votre locataire dans l’URL d’authentification, suivez les instructions fournies dans [Activer les domaines personnalisés](custom-domain.md). Dans le dossier racine du projet, ouvrez le fichier `appsettings.json`. Ce fichier contient des informations sur votre fournisseur d’identité Azure AD B2C. 

- Mettez à jour l'entrée `Instance` avec votre domaine personnalisé.
- Mettez à jour l’entrée `Domain` avec votre [ID de locataire](tenant-management.md#get-your-tenant-id). Pour plus d’informations, consultez [Utiliser l’ID de locataire](custom-domain.md#optional-use-tenant-id).

Le code JSON suivant montre les paramètres de l’application *avant* la modification : 

```json
"AzureAdB2C": {
  "Instance": "https://contoso.b2clogin.com",
  "Domain": "tenant-name.onmicrosoft.com",
  ...
}
```  

Le code JSON suivant montre les paramètres de l’application *après* la modification : 

```json
"AzureAdB2C": {
  "Instance": "https://login.contoso.com",
  "Domain": "00000000-0000-0000-0000-000000000000",
  ...
}
``` 

## <a name="support-advanced-scenarios"></a>Scénarios avancés d’assistance

La méthode `AddMicrosoftIdentityWebAppAuthentication` de l’API Microsoft Identity Platform permet aux développeurs d’ajouter du code pour des scénarios d’authentification avancés ou de s’abonner à des événements OpenIdConnect. Par exemple, vous pouvez vous abonner à OnRedirectToIdentityProvider afin de personnaliser la requête d’authentification que votre application envoie à Azure AD B2C.

Pour prendre en charge les scénarios avancés, ouvrez le fichier *Startup.cs* et, dans la fonction `ConfigureServices`, remplacez `AddMicrosoftIdentityWebAppAuthentication` par l’extrait de code suivant : 

```csharp
// Configuration to sign in users with Azure AD B2C

//services.AddMicrosoftIdentityWebAppAuthentication(Configuration, "AzureAdB2C");

services.AddAuthentication(OpenIdConnectDefaults.AuthenticationScheme)
        .AddMicrosoftIdentityWebApp(options =>
{
    Configuration.Bind("AzureAdB2C", options);
    options.Events ??= new OpenIdConnectEvents();
    options.Events.OnRedirectToIdentityProvider += OnRedirectToIdentityProviderFunc;
});
```

Le code ci-dessus ajoute l’événement OnRedirectToIdentityProvider avec une référence à la méthode *OnRedirectToIdentityProviderFunc*. Ajoutez l’extrait de code suivant dans la classe `Startup.cs`.

```csharp
private async Task OnRedirectToIdentityProviderFunc(RedirectContext context)
{
    // Custom code here
    
    // Don't remove this line
    await Task.CompletedTask.ConfigureAwait(false);
}
```

Vous pouvez transmettre des paramètres entre votre contrôleur et la fonction *OnRedirectToIdentityProvider* à l’aide de paramètres de contexte. 


[!INCLUDE [active-directory-b2c-app-integration-login-hint](../../includes/active-directory-b2c-app-integration-login-hint.md)]

1. Si vous utilisez une stratégie personnalisée, ajoutez la requête d’entrée requise comme décrit dans [Configurer la connexion directe](direct-signin.md#prepopulate-the-sign-in-name). 

1. Complétez la procédure de [prise en charge des scénarios avancés](#support-advanced-scenarios).

1. Ajoutez la ligne de code suivante à la fonction *OnRedirectToIdentityProvider* :
    
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
1. Dans la fonction *OnRedirectToIdentityProviderFunc*, ajoutez la ligne de code suivante à la fonction *OnRedirectToIdentityProvider* :
    
    ```csharp
    private async Task OnRedirectToIdentityProviderFunc(RedirectContext context)
    {
      context.ProtocolMessage.DomainHint = "facebook.com";
      
      // More code
      await Task.CompletedTask.ConfigureAwait(false);
    }
    ```

[!INCLUDE [active-directory-b2c-app-integration-ui-locales](../../includes/active-directory-b2c-app-integration-ui-locales.md)]

1. [Configurez la personnalisation du langage](language-customization.md).
1. Complétez la procédure de [prise en charge des scénarios avancés](#support-advanced-scenarios).
1. Ajoutez la ligne de code suivante à la fonction *OnRedirectToIdentityProvider* :

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
1. Ajoutez la ligne de code suivante à la fonction *OnRedirectToIdentityProvider* :
    
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
1. Ajoutez la ligne de code suivante à la fonction *OnRedirectToIdentityProvider* :
    
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

Si vous souhaitez personnaliser les actions de *connexion*, d’*inscription* ou de *déconnexion*, nous vous encourageons à créer votre propre contrôleur. Lorsque vous disposez de votre propre contrôleur, vous pouvez transmettre des paramètres entre votre contrôleur et la bibliothèque d’authentification. `AccountController` fait partie du package NuGet `Microsoft.Identity.Web.UI`, qui gère les actions de connexion et de déconnexion. Vous pouvez trouver son implémentation dans la [bibliothèque Microsoft Identity Web](https://github.com/AzureAD/microsoft-identity-web/blob/master/src/Microsoft.Identity.Web.UI/Areas/MicrosoftIdentity/Controllers/AccountController.cs). 

L’extrait de code suivant montre un `MyAccountController` personnalisé avec l’action de **SignIn**. L’action transmet un paramètre nommé `campaign_id` à la bibliothèque d’authentification.

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
            properties.Items["campaign_id"] = "1234";
            return Challenge(properties, scheme);
        }

    }
}

```

Dans l’affichage `_LoginPartial.cshtml`, modifiez le lien de connexion à votre contrôleur

```
<form method="get" asp-area="MicrosoftIdentity" asp-controller="MyAccount" asp-action="SignIn">
```

Dans le `OnRedirectToIdentityProvider` dans les appels `Startup.cs`, vous pouvez lire le paramètre personnalisé :

```csharp
private async Task OnRedirectToIdentityProviderFunc(RedirectContext context)
{
    // Read the custom parameter
    var campaign_id = (context.Properties.Items.ContainsKey("campaign_id"))
    
    // Add your custom code here
    
    await Task.CompletedTask.ConfigureAwait(false);
}
```

## <a name="role-based-access-control"></a>Contrôle d’accès en fonction du rôle

Avec [l’autorisation dans ASP.NET Core](/aspnet/core/security/authorization/introduction) vous pouvez utiliser [l’autorisation basée sur les rôles](/aspnet/core/security/authorization/roles), [l’autorisation basée sur les revendications](/aspnet/core/security/authorization/claims) ou [l’autorisation basée sur la stratégie](/aspnet/core/security/authorization/policies) pour vérifier si l’utilisateur est autorisé à accéder à une ressource protégée.

Dans la méthode *ConfigureServices*, ajoutez la méthode *AddAuthorization*, qui ajoute le modèle d’autorisation. L’exemple qui suit permet de créer une clé nommée `EmployeeOnly`. La stratégie vérifie s’il existe une revendication `EmployeeNumber`. La valeur de la revendication doit être l’un des ID suivants : 1, 2, 3, 4 ou 5.

```csharp
services.AddAuthorization(options =>
    {
        options.AddPolicy("EmployeeOnly", policy =>
              policy.RequireClaim("EmployeeNumber", "1", "2", "3", "4", "5"));
    });
```

Vous contrôlez l’autorisation dans ASP.NET Core à l’aide de [AuthorizeAttribute](/aspnet/core/security/authorization/simple) et de ses différents paramètres. Lorsque vous appliquez la forme la plus basique de l’attribut `[Authorize]` à une page de contrôleur, d’action ou Razor, vous limitez l’accès aux utilisateurs authentifiés de ce composant.

Les stratégies sont appliquées aux contrôleurs à l’aide de l’attribut `[Authorize]` avec le nom de la stratégie. Le code suivant limite l’accès à l’action `Claims` aux utilisateurs autorisés par la stratégie `EmployeeOnly` :

```csharp
[Authorize(Policy = "EmployeeOnly")]
public IActionResult Claims()
{
    return View();
}
```

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus, consultez [Présentation de l’autorisation dans ASP.NET Core](/aspnet/core/security/authorization/introduction).
