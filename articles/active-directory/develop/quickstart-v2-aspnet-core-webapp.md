---
title: Ajouter la connexion avec Microsoft à des applications web ASP.NET Core - Plateforme d’identités Microsoft | Azure
description: Découvrez comment implémenter la connexion Microsoft sur une application web ASP.NET Core à l’aide d’OpenID Connect.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 04/11/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 72c363c34a3e7e01cb32917dd87237e4bbfc9490
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/03/2020
ms.locfileid: "78249143"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-an-aspnet-core-web-app"></a>Démarrage rapide : Ajouter la connexion avec Microsoft à une application web ASP.NET Core
Dans ce guide de démarrage rapide, vous utilisez un exemple de code pour découvrir comment une application web ASP.NET Core peut connecter des comptes personnels (hotmail.com, outlook.com, etc.) et des comptes professionnels et scolaires à partir de n’importe quelle instance Azure Active Directory (Azure AD). (Consultez [Fonctionnement de l’exemple](#how-the-sample-works) pour une illustration.)
> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>Inscrire et télécharger votre application de démarrage rapide
> Vous disposez de deux options pour démarrer votre application de démarrage rapide :
> * [Express] [Option 1 : Inscrire et configurer automatiquement votre application, puis télécharger votre exemple de code](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * [Manuel] [Option 2 : Inscrire et configurer manuellement vos application et exemple de code](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Option 1 : Inscrire et configurer automatiquement votre application, puis télécharger votre exemple de code
>
> 1. Accédez au [portail Azure - Inscriptions d’applications](https://aka.ms/aspnetcore2-1-aad-quickstart-v2).
> 1. Entrez un nom pour votre application, puis sélectionnez **Inscrire**.
> 1. Suivez les instructions pour télécharger et configurer automatiquement votre nouvelle application pour vous en un seul clic.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Option n°2 : Inscrire et configurer manuellement vos application et exemple de code
>
> #### <a name="step-1-register-your-application"></a>Étape 1 : Inscrivez votre application
> Pour inscrire votre application et ajouter manuellement les informations d’inscription de l’application à votre solution, procédez comme suit :
>
> 1. Connectez-vous au [portail Azure](https://portal.azure.com) avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
> 1. Si votre compte vous propose un accès à plusieurs locataires, sélectionnez votre compte en haut à droite et définissez votre session de portail sur le locataire Azure AD souhaité.
> 1. Accédez à la page [Inscriptions des applications](https://go.microsoft.com/fwlink/?linkid=2083908) de la plateforme d’identité Microsoft pour les développeurs.
> 1. Sélectionnez **Nouvelle inscription**.
> 1. Lorsque la page **Inscrire une application** s’affiche, saisissez les informations d’inscription de votre application :
>    - Dans la section **Nom**, saisissez un nom d’application cohérent qui s’affichera pour les utilisateurs de l’application, par exemple `AspNetCore-Quickstart`.
>    - Dans **URI de redirection**, ajoutez `https://localhost:44321/`, puis sélectionnez **Inscrire**.
> 1. Sélectionnez le menu **Authentification** et ajoutez les informations suivantes :
>    - Dans **URI de redirection**, ajoutez `https://localhost:44321/signin-oidc`, puis sélectionnez **Enregistrer**.
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

#### <a name="step-2-download-your-aspnet-core-project"></a>Étape 2 : Télécharger votre projet ASP.NET Core

> [!div renderon="docs"]
> [Télécharger la solution Visual Studio 2019](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/archive/aspnetcore2-2.zip)

> [!div class="sxs-lookup" renderon="portal"]
> Exécutez le projet à l’aide de Visual Studio 2019.
> [!div renderon="portal" id="autoupdate" class="nextstepaction"]
> [Téléchargez l’exemple de code]().

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>Étape 3 : Votre application est configurée et prête à être exécutée
> Nous avons configuré votre projet avec les valeurs des propriétés de votre application et il est prêt à être exécuté. 
> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > Enter_the_Supported_Account_Info_Here
> [!div renderon="docs"]
> #### <a name="step-3-run-your-visual-studio-project"></a>Étape 3 : Exécuter votre projet Visual Studio
> 1. Extrayez le fichier zip dans un dossier local du dossier racine, par exemple, **C:\Azure-Samples**
> 1. Ouvrez la solution dans Visual Studio 
> 1. Modifiez le fichier **appsettings.json**. Recherchez `ClientId` et mettez à jour la valeur de `ClientId` avec la valeur de l’**ID d’application (client)** de l’application que vous avez inscrite. 
>
>    ```json
>    "ClientId": "Enter_the_Application_Id_here"
>    "TenantId": "Enter_the_Tenant_Info_Here"
>    ```



> [!div renderon="docs"]
> Où :
> - `Enter_the_Application_Id_here` est l’**ID d’application (client)** de l’application que vous avez inscrite dans le portail Azure. Vous trouverez l’**ID d’application (client)** dans la page **Vue d’ensemble** de l’application.
> - `Enter_the_Tenant_Info_Here` est l’une des options suivantes :
>   - Si votre application prend en charge **Comptes dans cet annuaire organisationnel uniquement**, remplacez cette valeur par l’**ID de locataire** ou le **Nom du locataire** (par exemple, contoso.microsoft.com)
>   - Si votre application prend en charge **Comptes dans un annuaire organisationnel**, remplacez cette valeur par `organizations`
>   - Si votre application prend en charge **tous les utilisateurs de compte Microsoft**, remplacez cette valeur par `common`
>
> > [!TIP]
> > Pour connaître les valeurs de l’**ID d’Application (client)** , de l’**ID de l’annuaire (locataire)** , et des **Types de comptes pris en charge**, consultez la page **Vue d’ensemble** de l’application dans le Portail Azure.

## <a name="more-information"></a>Informations complémentaires

Cette section offre une vue d’ensemble du code requis pour connecter les utilisateurs. Cette vue d’ensemble peut être utile pour comprendre comment le code fonctionne, les principaux arguments et également si vous souhaitez ajouter une connexion à une application ASP.NET Core existante.

### <a name="how-the-sample-works"></a>Fonctionnement de l’exemple
![Fonctionnement de l’exemple d’application généré par ce guide de démarrage rapide](media/quickstart-v2-aspnet-core-webapp/aspnetcorewebapp-intro.svg)

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
    options.Authority = options.Authority + "/v2.0/";         // Microsoft identity platform

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

La méthode `AddAuthentication` configure le service pour ajouter une authentification basée sur les cookies, qui est utilisée sur les scénarios de navigateur, et pour définir la demande d’authentification sur OpenID Connect. 

La ligne contenant `.AddAzureAd` ajoute à votre application l’authentification auprès de la plateforme d’identités Microsoft. Elle est ensuite configurée pour la connexion à l’aide du point de terminaison de la plateforme d’identités Microsoft.

> |Where  |  |
> |---------|---------|
> | ClientId  | ID d’application (client) de l’application inscrite dans le portail Azure. |
> | Authority | Point de terminaison STS pour l’utilisateur à authentifier. Généralement, il s’agit de <https://login.microsoftonline.com/{tenant}/v2.0> pour le cloud public, où {tenant} est le nom de votre locataire ou l’ID de votre locataire, ou *common* pour une référence au point de terminaison commun (utilisé pour les applications multilocataires). |
> | TokenValidationParameters | Liste de paramètres pour la validation du jeton. Dans ce cas, `ValidateIssuer` a la valeur `false` pour indiquer qu’il peut accepter des connexions à partir de n’importe quel compte personnel, professionnel ou scolaire. |


> [!NOTE]
> Le paramètre `ValidateIssuer = false` est une simplification destinée aux seules fins de ce guide de démarrage rapide. Dans une application réelle, vous auriez à valider l’émetteur.
> Pour savoir comment procéder, consultez les exemples.

### <a name="protect-a-controller-or-a-controllers-method"></a>Protéger un contrôleur ou la méthode d’un contrôleur

Vous pouvez protéger un contrôleur ou les méthodes d’un contrôleur à l’aide de l’attribut `[Authorize]`. Cet attribut limite l’accès au contrôleur ou aux méthodes en autorisant uniquement les utilisateurs authentifiés, ce qui signifie que la demande d’authentification peut être démarrée pour accéder au contrôleur si l’utilisateur n’est pas authentifié.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Étapes suivantes

Consultez le dépôt GitHub pour ce tutoriel ASP.NET Core pour plus d’informations, notamment pour savoir comment ajouter une authentification à une toute nouvelle application web ASP.NET Core, appeler Microsoft Graph et d’autres API Microsoft, appeler vos propres API, ajouter une autorisation, connecter des utilisateurs dans des clouds nationaux ou avec des identités de réseaux sociaux et plus encore :

> [!div class="nextstepaction"]
> [Tutoriel sur les applications web ASP.NET Core](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/)

Aidez-nous à améliorer la plateforme des identités Microsoft. Faites-nous part de votre avis en répondant à une petite enquête de deux questions.

> [!div class="nextstepaction"]
> [Enquête sur la plateforme des identités Microsoft](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)
