---
title: 'Démarrage rapide : application web Azure AD v2.0 ASP.NET Core | Microsoft Docs'
description: Découvrez comment implémenter la connexion Microsoft sur une application web ASP.NET Core à l’aide d’OpenID Connect.
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/25/2018
ms.author: andret
ms.custom: aaddev
ms.openlocfilehash: ba67acec778a48c084897095aa457e5637240a57
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/26/2018
ms.locfileid: "47227423"
---
# <a name="add-sign-in-with-microsoft-to-an-aspnet-core-web-app"></a>Ajouter la connexion avec Microsoft à une application web ASP.NET Core

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

Ce démarrage rapide inclut un exemple de code montrant comment une application web ASP.NET Core peut connecter des comptes personnels (hotmail.com, live.com, etc.) et des comptes professionnels et scolaires à partir de n’importe quelle instance Azure Active Directory.

![Fonctionnement de l’exemple d’application généré par ce démarrage rapide](media/quickstart-v2-aspnet-core-webapp/aspnetcorewebapp-intro.png)


> [!div renderon="docs"]
> ## <a name="register-your-application-and-download-your-quickstart-app"></a>Inscrire votre application et télécharger votre application de démarrage rapide
>
> ### <a name="register-and-configure-your-application-and-code-sample"></a>Inscrire et configurer votre application et votre exemple de code
> #### <a name="step-1-register-your-application"></a>Étape 1 : Inscrire votre application
> 
> 1. Accédez au [portail d’inscription des applications de Microsoft](https://apps.dev.microsoft.com/portal/register-app).
> 1. Entrez le nom de votre application. Vérifiez que la case de l’option **Assistant Installation** est décochée, puis cliquez sur **Créer**.
> 1. Cliquez sur `Add Platform`, puis sélectionnez `Web`.
> 1. Assurez-vous que la case **Autoriser un flux implicite** est *cochée*.
> 1. Dans **URL de redirection**, entrez `https://localhost:3110/`.
> 1. Faites défiler la page jusqu’en bas, puis cliquez sur **Enregistrer**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-azure-portal"></a>Étape 1 : Configurer votre application dans le Portail Azure
> Pour que l’exemple de code de ce démarrage rapide fonctionne, vous devez ajouter une URL de réponse telle que `http://localhost:3110/`.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Apporter cette modification pour moi]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Déjà configuré](media/quickstart-v2-aspnet-core-webapp/green-check.png) Votre application est configurée avec cet attribut.

#### <a name="step-2-download-your-aspnet-core-project"></a>Étape 2 : Télécharger votre projet ASP.NET Core

- [Télécharger le projet ASP.NET Core](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/archive/master.zip)

#### <a name="step-3-configure-your-project"></a>Étape 3 : Configurer votre projet

1. Extrayez le fichier zip dans un dossier local (par exemple, **C:\Azure-Samples**).
1. Si vous utilisez Visual Studio 2017, ouvrez le projet dans Visual Studio (facultatif).
1. Modifiez le fichier **appsettings.json** et remplacez la valeur de `ClientId` par l’ID de l’application que vous venez d’inscrire :

    ```json
    "ClientId": "Enter_the_Application_Id_here"
    "TenantId": "common"
    ```
1. Si votre application est une *application à client unique* (ciblant les comptes du répertoire actif uniquement), dans votre fichier **appsettings.json**, recherchez la valeur de `TenantId` et remplacez `common` par votre **ID de client** ou **nom de client** (par exemple, contoso.microsoft.com). Le nom du client est disponible dans la **page Vue d’ensemble**.

## <a name="more-information"></a>Plus d’informations

Cette section offre une vue d’ensemble du code requis pour connecter les utilisateurs. Elle vous sera utile pour comprendre comment le code fonctionne et les principaux arguments. Elle vous aidera également à ajouter une connexion à une application ASP.NET Core existante.

### <a name="startup-class"></a>Classe de démarrage

L’intergiciel *Microsoft.AspNetCore.Authentication* utilise une classe de démarrage qui s’exécute lors de l’initialisation du processus d’hébergement :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(sharedOptions =>
    {
        sharedOptions.DefaultScheme = CookieAuthenticationDefaults.AuthenticationScheme;
        sharedOptions.DefaultChallengeScheme = OpenIdConnectDefaults.AuthenticationScheme;
    })
    .AddAzureAd(options => Configuration.Bind("AzureAd", options))
    .AddCookie();

    services.AddMvc();
}
```

La méthode `AddAuthentication` configure le service pour ajouter une authentification basée sur les cookies (utilisée dans les scénarios impliquant un navigateur et pour définir la demande sur OpenIdConnect). 

La ligne contenant `.AddAzureAd` ajoute l’authentification Azure Active Directory à votre application.

En outre, le fichier **AzureAdAuthenticationBuilderExtensions.cs** ajoute une méthode d’extension au pipeline Azure AD Authentication. Cette méthode d’extension configure les attributs nécessaires à Azure AD Authentication. La méthode `Configure` dans l’interface `IConfigureNamedOptions` contient les éléments suivants :

```csharp
public void Configure(string name, OpenIdConnectOptions options)
{
    options.ClientId = _azureOptions.ClientId;
    options.Authority = $"{_azureOptions.Instance}common/v2.0";   // V2 specific
    options.UseTokenLifetime = true;
    options.RequireHttpsMetadata = false;
    options.TokenValidationParameters.ValidateIssuer = false;     // accept several tenants (here simplified)
}
```
> |Where  |  |
> |---------|---------|
> |ClientId     |ID de l’application inscrite dans le Portail Azure|
> |Authority | Point de terminaison STS pour l’utilisateur à authentifier. Généralement https://login.microsoftonline.com/{tenant}/v2.0 pour le cloud public, où {tenant} est le nom de votre client, votre ID de client ou *common* pour une référence au point de terminaison commun (utilisé pour les applications mutualisées).|
> |UseTokenLifetime |Indique que le cookie d’authentification doit correspondre à celui du jeton d’authentification.|
> |RequireHttpsMetadata     |Exige le protocole HTTPS pour l’autorité ou l’adresse des métadonnées. Il est recommandé de définir cette valeur sur `True`.|
> |TokenValidationParameters     | Liste de paramètres pour la validation du jeton. Dans ce cas, `ValidateIssuer` a la valeur `false` pour indiquer qu’il peut accepter des connexions à partir de n’importe quel compte personnel, professionnel ou scolaire.|

### <a name="initiate-an-authentication-challenge"></a>Initier une demande d’authentification

Vous pouvez forcer un utilisateur à se connecter en effectuant une demande d’authentification dans votre contrôleur, tout comme dans **AccountController.cs** :

```csharp
public IActionResult SignIn()
{
    var redirectUrl = Url.Action(nameof(HomeController.Index), "Home");
    return Challenge(
        new AuthenticationProperties { RedirectUri = redirectUrl },
        OpenIdConnectDefaults.AuthenticationScheme);
}
```

> [!TIP]
> Il n’est pas obligatoire d’effectuer une demande d’authentification à l’aide de la méthode ci-dessus. Cette méthode est généralement utilisée pour rendre une vue disponible à la fois pour les utilisateurs authentifiés et non authentifiés. Vous pouvez protéger les contrôleurs à l’aide de la méthode décrite dans la section suivante.

### <a name="protect-a-controller-or-a-controllers-method"></a>Protéger un contrôleur ou la méthode d’un contrôleur

Vous pouvez protéger un contrôleur ou les méthodes d’un contrôleur à l’aide de l’attribut `[Authorize]`. Cet attribut limite l’accès au contrôleur ou aux méthodes en accordant une autorisation aux utilisateurs authentifiés uniquement. Ainsi, la demande d’authentification peut être lancée pour accéder au contrôleur si l’utilisateur n’est pas authentifié.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez le référentiel GitHub pour ce démarrage rapide ASP.NET Core. Celui-ci inclut également des instructions sur l’ajout de l’authentification à une toute nouvelle application web ASP.NET Core :

> [!div class="nextstepaction"]
> [Exemple de code pour une application web ASP.NET Core](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]