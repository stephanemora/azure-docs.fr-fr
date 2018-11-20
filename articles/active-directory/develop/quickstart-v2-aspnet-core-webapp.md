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
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/09/2018
ms.author: andret
ms.custom: aaddev
ms.openlocfilehash: 4849ffcc6fd71a0b88b270f2e6cbdb23b18ecc76
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/13/2018
ms.locfileid: "51611639"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-an-aspnet-web-app"></a>Démarrage rapide : Ajouter la connexion avec Microsoft à une application web ASP.NET

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

Dans ce guide de démarrage rapide, vous allez découvrir comment une application web ASP.NET Core peut connecter des comptes personnels (hotmail.com, outlook.com, etc.) et des comptes professionnels et scolaires à partir de n’importe quelle instance Azure Active Directory (Azure AD).

![Fonctionnement de l’exemple d’application généré par ce démarrage rapide](media/quickstart-v2-aspnet-core-webapp/aspnetcorewebapp-intro.png)


> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>Inscrire et télécharger votre application de démarrage rapide
> Vous disposez de deux options pour démarrer votre application de démarrage rapide :
> * [Express] [Option 1 : Inscrire et configurer automatiquement votre application, puis télécharger votre exemple de code](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * [Manuel] [Option 2 : Inscrire et configurer manuellement votre application et exemple de code](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Option 1 : Inscrire et configurer automatiquement votre application, puis télécharger votre exemple de code
>
> 1. Accédez au [portail Azure - Inscriptions d’applications (préversion)](https://aka.ms/aspnetcore2-1-aad-quickstart-v2).
> 1. Entrez un nom pour votre application, puis sélectionnez **Inscrire**.
> 1. Suivez les instructions pour télécharger et configurer automatiquement votre nouvelle application pour vous en un seul clic.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Option 2 : Inscrire et configurer manuellement votre application et exemple de code
>
> #### <a name="step-1-register-your-application"></a>Étape 1 : Inscrire votre application
> Pour inscrire votre application et ajouter manuellement les informations d’inscription de l’application à votre solution, procédez comme suit :
>
> 1. Connectez-vous au [portail Azure](https://portal.azure.com) avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
> 1. Si votre compte vous propose un accès à plusieurs locataires, sélectionnez votre compte en haut à droite et définissez votre session de portail sur le locataire Azure AD souhaité.
> 1. Dans le volet de navigation gauche, sélectionnez le service **Azure Active Directory**, puis sélectionnez **Inscriptions d’applications (préversion)** > **Nouvelle inscription**.
> 1. Lorsque la page **Inscrire une application** s’affiche, saisissez les informations d’inscription de votre application :
>    - Dans la section **Nom**, saisissez un nom d’application cohérent qui s’affichera pour les utilisateurs de l’application, par exemple `AspNetCore-Quickstart`.
>    - Dans **URL de réponse**, ajoutez `https://localhost:44321/` et sélectionnez **Inscrire**.
> 1. Sélectionnez le menu **Authentification** et ajoutez les informations suivantes :
>    - Dans **URL de réponse**, ajoutez `https://localhost:44321/signin-oidc` et sélectionnez **Inscrire**.
>    - Dans la section **Paramètres avancés**, définissez **URL de déconnexion** sur `https://localhost:44321/signout-oidc`.
>    - Sous **Octroi implicite**, cochez **Jetons d’ID**.
>    - Sélectionnez **Enregistrer**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Étape 1 : Configurer votre application dans le portail Azure
> Pour que l’exemple de code de ce guide de démarrage rapide fonctionne, vous devez ajouter les URL de réponse `https://localhost:44321/` et `https://localhost:44321/signin-oidc`, ajouter l’URL de déconnexion `https://localhost:44321/signout-oidc` et demander l’émission des jetons d’ID par le point de terminaison d’autorisation.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Apporter cette modification pour moi]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Déjà configuré](media/quickstart-v2-aspnet-webapp/green-check.png) Votre application est configurée avec ces attributs.

#### <a name="step-2-download-your-aspnet-core-project"></a>Étape 2 : Télécharger votre projet ASP.NET Core

- [Télécharger la solution Visual Studio 2017](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/archive/aspnetcore2-2.zip)

#### <a name="step-3-configure-your-visual-studio-project"></a>Étape 3 : configurer votre projet Visual Studio

1. Extrayez le fichier zip dans un dossier local du dossier racine, par exemple, **C:\Azure-Samples**
1. Si vous utilisez Visual Studio 2017, ouvrez la solution dans Visual Studio (facultatif).
1. Modifiez le fichier **appsettings.json**. Recherchez `ClientId` et remplacez `Enter_the_Application_Id_here` par la valeur **ID d’application (client)** que vous venez d’inscrire. 

    ```json
    "ClientId": "Enter_the_Application_Id_here"
    "TenantId": "Enter_the_Tenant_Info_Here"
    ```

> [!div renderon="docs"]
> Où :
> - `Enter_the_Application_Id_here` est l’**ID d’application (client)** de l’application que vous avez inscrite dans le portail Azure. Vous trouverez l’**ID d’application (client)** dans la page **Vue d’ensemble** de l’application.
> - `Enter_the_Tenant_Info_Here` est l’une des options suivantes :
>   - Si votre application prend en charge **Comptes dans cet annuaire organisationnel uniquement**, remplacez cette valeur par l’**ID de locataire** ou le **Nom du locataire** (par exemple, contoso.microsoft.com)
>   - Si votre application prend en charge **Comptes dans un annuaire organisationnel**, remplacez cette valeur par `organizations`
>   - Si votre application prend en charge **tous les utilisateurs de compte Microsoft**, remplacez cette valeur par `common`
>
> > [!TIP]
> > Pour connaître les valeurs de l’**ID d’Application (client)**, de l’**ID de l’annuaire (locataire)**, et des **Types de comptes pris en charge**, consultez la page **Vue d’ensemble** de l’application dans le Portail Azure.

## <a name="more-information"></a>Plus d’informations

Cette section offre une vue d’ensemble du code requis pour connecter les utilisateurs. Elle vous sera utile pour comprendre comment le code fonctionne et les principaux arguments. Elle vous aidera également à ajouter une connexion à une application ASP.NET Core existante.

### <a name="startup-class"></a>Classe de démarrage

L’intergiciel *Microsoft.AspNetCore.Authentication* utilise une classe de démarrage qui s’exécute lors de l’initialisation du processus d’hébergement :

```csharp
public void ConfigureServices(IServiceCollection services)
{
  services.Configure<CookiePolicyOptions>(options =>
  {
    // This lambda determines whether user consent for non-essential cookies is needed for a given request.
    options.CheckConsentNeeded = context => true;
    options.MinimumSameSitePolicy = SameSiteMode.None;
  });

  services.AddAuthentication(AzureADDefaults.AuthenticationScheme)
          .AddAzureAD(options => Configuration.Bind("AzureAd", options));

  services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
  {
    options.Authority = options.Authority + "/v2.0/";         // Azure AD v2.0

    options.TokenValidationParameters.ValidateIssuer = false; // accept several tenants (here simplified)
  });

  services.AddMvc(options =>
  {
     var policy = new AuthorizationPolicyBuilder()
                     .RequireAuthenticatedUser()
                     .Build();
     options.Filters.Add(new AuthorizeFilter(policy));
  })
  .SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
}
```

La méthode `AddAuthentication` configure le service pour ajouter une authentification basée sur les cookies, qui est utilisée dans les scénarios de navigateur, et pour définir la demande sur OpenID Connect. 

La ligne contenant `.AddAzureAd` ajoute l’authentification Azure AD à votre application. Elle est ensuite configurée pour se connecter en utilisant le point de terminaison Azure AD v2.0.

> |Where  |  |
> |---------|---------|
> | ClientId  | ID d’application (client) de l’application inscrite dans le portail Azure. |
> | Authority | Point de terminaison STS pour l’utilisateur à authentifier. Généralement, il s’agit de https://login.microsoftonline.com/{tenant}/v2.0 pour le cloud public, où {tenant} est le nom de votre locataire ou l’ID de votre locataire, ou *common* pour une référence au point de terminaison commun (utilisé pour les applications multilocataires). |
> | TokenValidationParameters | Liste de paramètres pour la validation du jeton. Dans ce cas, `ValidateIssuer` a la valeur `false` pour indiquer qu’il peut accepter des connexions à partir de n’importe quel compte personnel, professionnel ou scolaire. |

### <a name="protect-a-controller-or-a-controllers-method"></a>Protéger un contrôleur ou la méthode d’un contrôleur

Vous pouvez protéger un contrôleur ou les méthodes d’un contrôleur à l’aide de l’attribut `[Authorize]`. Cet attribut limite l’accès au contrôleur ou aux méthodes en accordant une autorisation aux utilisateurs authentifiés uniquement, ce qui signifie que la demande d’authentification peut être lancée pour accéder au contrôleur si l’utilisateur n’est pas authentifié.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez le dépôt GitHub pour ce guide de démarrage rapide ASP.NET Core, car il contient notamment des instructions sur l’ajout d’une authentification à une toute nouvelle application web ASP.NET Core :

> [!div class="nextstepaction"]
> [Exemple de code pour une application web ASP.NET Core](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/)

