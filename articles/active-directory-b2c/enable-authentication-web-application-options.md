---
title: Activer les options d’application Web à l’aide d’Azure Active Directory B2C
description: Activez l’utilisation des options d’application Web à l’aide de plusieurs méthodes.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 05/25/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: 3335e035a2d36cc7830d8bc93db82a7d318d26b3
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110482079"
---
# <a name="configure-authentication-in-a-sample-web-application-using-azure-active-directory-b2c-options"></a>Configurer l’authentification dans une application web à l’aide des options d’Azure Active Directory B2C

Cet article décrit les méthodes permettant de personnaliser et d’améliorer l’expérience d’authentification Azure Active Directory B2C (Azure AD B2C) pour votre application Web. Avant de commencer, familiarisez-vous avec les articles suivants : [Configurer l’authentification dans un exemple d’application Web](configure-authentication-sample-web-app.md) ou [Activer l’authentification dans votre application Web](enable-authentication-web-application.md).

## <a name="use-a-custom-domain"></a>Utiliser un domaine personnalisé

L’utilisation d’un [domaine personnalisé](custom-domain.md) dans l’URL de redirection de votre application offre une expérience utilisateur plus transparente. Du point de vue de l’utilisateur, il reste dans votre domaine pendant le processus de connexion au lieu de rediriger vers le domaine par défaut Azure AD B2C .b2clogin.com.

Pour utiliser un domaine personnalisé, suivez les instructions dans [Activer les domaines personnalisés](custom-domain.md). Dans le dossier racine du projet, ouvrez le fichier `appsettings.json`. Ce fichier contient des informations sur votre fournisseur d’identité Azure AD B2C. Mettez à jour l'entrée `Instance` avec votre domaine personnalisé.

Le code JSON suivant montre les paramètres de l’application avant la modification : 

```JSon
"AzureAdB2C": {
  "Instance": "https://contoso.b2clogin.com",
  ...
}
```  

Le code JSON suivant montre les paramètres de l’application après la modification : 

```JSon
"AzureAdB2C": {
  "Instance": "https://login.contoso.com",
  ...
}
``` 

## <a name="use-your-tenant-id"></a>Utiliser votre ID de locataire

Vous pouvez remplacer le nom de votre locataire B2C dans l’URL par le GUID d’ID de votre locataire pour supprimer toutes les références à « b2c » dans l’URL.  Par exemple, vous pouvez remplacer `https://account.contosobank.co.uk/contosobank.onmicrosoft.com/` par `https://account.contosobank.co.uk/<tenant ID GUID>/`

Pour utiliser l’ID de locataire, suivez les instructions [Activer les domaines personnalisés](custom-domain.md#optional-use-tenant-id). Dans le dossier racine du projet, ouvrez le fichier `appsettings.json`. Ce fichier contient des informations sur votre fournisseur d’identité Azure AD B2C. Mettez à jour l'entrée `Domain` avec votre domaine personnalisé.

Le code JSON suivant montre les paramètres de l’application avant la modification : 

```JSon
"AzureAdB2C": {
  "Domain": "tenant-name.onmicrosoft.com",
  ...
}
```  

Le code JSON suivant montre les paramètres de l’application après la modification :

```JSon
"AzureAdB2C": {
  "Domain": "00000000-0000-0000-0000-000000000000",
  ...
}
``` 

## <a name="support-advanced-scenarios"></a>Scénarios avancés d’assistance

La méthode `AddMicrosoftIdentityWebAppAuthentication` de l’API Microsoft Identity Platform permet aux développeurs d’ajouter du code pour des scénarios d’authentification avancés ou de s’abonner à des événements OpenIdConnect. Par exemple, vous pouvez vous abonner à OnRedirectToIdentityProvider, ce qui vous permet de personnaliser la requête d’authentification que votre application envoie à Azure AD B2C.

Pour prendre en charge les scénarios avancés, ouvrez le `Startup.cs` et, dans la fonction `ConfigureServices`, remplacez le `AddMicrosoftIdentityWebAppAuthentication` par l’extrait de code suivant : 

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


## <a name="prepopulate-the-sign-in-name"></a>Préremplir le nom de connexion

Pendant le parcours utilisateur pour la connexion, votre application peut cibler un utilisateur spécifique. Quand vous ciblez un utilisateur, une application peut spécifier, dans la demande d’autorisation, le paramètre de requête `login_hint` avec le nom de connexion de l’utilisateur. Azure AD B2C remplit automatiquement le nom de connexion, et l’utilisateur n’a que le mot de passe à fournir. 

Pour préremplir le nom de connexion, procédez comme suit :

1. Complétez la procédure de [prise en charge des scénarios avancés](#support-advanced-scenarios) .
1. Si vous utilisez une stratégie personnalisée, ajoutez la revendication d’entrée requise comme décrit dans [Configurer la connexion directe](direct-signin.md#prepopulate-the-sign-in-name). 
1. Ajoutez la ligne de code suivante à la fonction *OnRedirectToIdentityProvider* :
    
    ```csharp
    private async Task OnRedirectToIdentityProviderFunc(RedirectContext context)
    {
      context.ProtocolMessage.LoginHint = "emily@contoso.com";
      
      // More code
      await Task.CompletedTask.ConfigureAwait(false);
    }
    ```

## <a name="redirect-sign-in-to-an-external-identity-provider"></a>Rediriger la connexion à un fournisseur d’identité externe

Si vous avez configuré la procédure de connexion pour votre application afin d’inclure des comptes de réseaux sociaux comme Facebook, LinkedIn ou Google, vous pouvez spécifier le paramètre `domain_hint`. Ce paramètre de requête fournit un indicateur à Azure AD B2C concernant le fournisseur d’identité sociale qui doit être utilisé pour la connexion. Par exemple, si l’application spécifie `domain_hint=facebook.com`, le flux de connexion accède directement à la page de connexion Facebook. 

Pour rediriger la connexion à un fournisseur d’identité externe, procédez comme suit :

1. Complétez la procédure de [prise en charge des scénarios avancés](#support-advanced-scenarios).
1. Vérifiez le nom de domaine de votre fournisseur d’identité externe. Pour plus d’informations, consultez [Rediriger la connexion vers un fournisseur social](direct-signin.md#redirect-sign-in-to-a-social-provider). 
1. Dans la fonction *OnRedirectToIdentityProviderFunc*, ajoutez la ligne de code suivante à la fonction *OnRedirectToIdentityProvider* :
    
    ```csharp
    private async Task OnRedirectToIdentityProviderFunc(RedirectContext context)
    {
      context.ProtocolMessage.DomainHint = "facebook.com";
      
      // More code
      await Task.CompletedTask.ConfigureAwait(false);
    }
    ```

## <a name="specify-the-ui-language"></a>Spécifier la langue de l’interface utilisateur

La personnalisation de la langue dans Azure AD B2C permet à votre flux d’utilisateur de prendre en charge plusieurs langues pour répondre aux besoins de votre client. Consultez [Personnalisation linguistique](language-customization.md) pour plus d'informations.

Pour établir le langage préféré, suivez ces étapes :

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

## <a name="pass-a-custom-query-string-parameter"></a>Passer un paramètre de chaîne de requête personnalisé

Avec les stratégies personnalisées, vous pouvez passer un paramètre de chaîne de requête personnalisé, par exemple lorsque vous souhaitez [modifier dynamiquement le contenu de la page](customize-ui-with-html.md?pivots=b2c-custom-policy#configure-dynamic-custom-page-content-uri).


Pour passer un paramètre de chaîne de requête personnalisé, procédez comme suit :

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

## <a name="pass-id-token-hint"></a>Transmission d’indicateur de jeton d’ID

Azure AD B2C permet aux applications par partie de confiance d’envoyer un jeton JWT entrant dans le cadre de la demande d’autorisation OAuth2. Le jeton JWT peut être émis par une application de partie de confiance ou un fournisseur d’identité, et il peut transmettre une indication sur l’utilisateur ou la demande d’autorisation. Azure AD B2C valide la signature, le nom de l’émetteur et l’audience du jeton, puis extrait la revendication du jeton entrant.

Pour inclure un indicateur de jeton d’ID dans la requête d’authentification, procédez comme suit : 

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

Si vous souhaitez personnaliser les actions de **connexion**, **d’inscription** ou de **déconnexion**, vous êtes encouragé à créer votre propre contrôleur. Le fait de disposer de votre propre contrôleur vous permet de transmettre des paramètres entre votre contrôleur et la bibliothèque d’authentification. Le `AccountController` fait partie du package NuGet `Microsoft.Identity.Web.UI`, qui gère les actions de connexion et de déconnexion. Vous pouvez trouver son implémentation dans la [bibliothèque Microsoft Identity Web](https://github.com/AzureAD/microsoft-identity-web/blob/master/src/Microsoft.Identity.Web.UI/Areas/MicrosoftIdentity/Controllers/AccountController.cs). 

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

## <a name="role-based-access-control"></a>Contrôle d'accès en fonction d'un rôle

Avec [l’autorisation dans ASP.NET Core](/aspnet/core/security/authorization/introduction) vous pouvez utiliser [l'autorisation basée sur les rôles](/aspnet/core/security/authorization/roles), [l'autorisation basée sur les revendications](/aspnet/core/security/authorization/claims), ou [l'autorisation basée sur la stratégie](/aspnet/core/security/authorization/policies) pour vérifier si l’utilisateur est autorisé à accéder à une ressource protégée.

Dans la méthode *ConfigureServices*, ajoutez la méthode *AddAuthorization*, qui ajoute le modèle d’autorisation. L’exemple qui suit permet de créer une clé nommée `EmployeeOnly`. La stratégie vérifie qu'il existe une revendication `EmployeeNumber`. La valeur de la revendication doit être l’un des ID suivants : 1, 2, 3, 4 ou 5.

```csharp
services.AddAuthorization(options =>
    {
        options.AddPolicy("EmployeeOnly", policy =>
              policy.RequireClaim("EmployeeNumber", "1", "2", "3", "4", "5"));
    });
```

L’autorisation dans ASP.NET Core est contrôlée à l’aide de [AuthorizeAttribute](/aspnet/core/security/authorization/simple) et de ses différents paramètres. Dans sa forme la plus basique, l’application de l’attribut `[Authorize]` à une page de contrôleur, d’action ou Razor, limite l’accès aux utilisateurs authentifiés de ce composant.

Les stratégies sont appliquées aux contrôleurs à l’aide de l'attribut `[Authorize]` avec le nom de la stratégie. Le code suivant limite l’accès à l'action `Claims` aux utilisateurs autorisés par la stratégie `EmployeeOnly` :

```csharp
[Authorize(Policy = "EmployeeOnly")]
public IActionResult Claims()
{
    return View();
}
```

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus : [Présentation de l’autorisation dans ASP.NET Core](/aspnet/core/security/authorization/introduction)