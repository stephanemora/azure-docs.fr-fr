---
title: 'Démarrage rapide : Ajouter la connexion Microsoft à une application web ASP.NET Core | Azure'
titleSuffix: Microsoft identity platform
description: Dans ce guide de démarrage rapide, vous découvrez comment une application implémente la connexion Microsoft dans une application web ASP.NET Core en utilisant OpenID Connect
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 09/11/2020
ms.author: jmprieur
ms.custom: devx-track-csharp, aaddev, identityplatformtop40, scenarios:getting-started, languages:aspnet-core
ms.openlocfilehash: e7296b04e3e912e96ac8c2ed77b44288324c262f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104578700"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-an-aspnet-core-web-app"></a>Démarrage rapide : Ajouter la connexion avec Microsoft à une application web ASP.NET Core

Dans ce guide de démarrage rapide, vous téléchargez et exécutez un exemple de code qui montre comment une application web ASP.NET Core peut connecter des utilisateurs à partir de n’importe quelle organisation Azure Active Directory (Azure AD).  

> [!div renderon="docs"]
> Le diagramme suivant illustre le fonctionnement de l’exemple d’application :
>
> ![Diagramme de l’interaction entre le navigateur web, l’application web et la plateforme d’identités Microsoft dans l’exemple d’application.](media/quickstart-v2-aspnet-core-webapp/aspnetcorewebapp-intro.svg)
>
> ## <a name="prerequisites"></a>Prérequis
>
> * [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) ou [Visual Studio Code](https://code.visualstudio.com/)
> * [SDK .NET Core 3.1+](https://dotnet.microsoft.com/download)
>
> ## <a name="register-and-download-the-app"></a>Inscrire et télécharger l’application
> Vous avez deux options pour commencer à créer votre application : configuration automatique ou configuration manuelle.
>
> ### <a name="automatic-configuration"></a>Configuration automatique
> Si vous souhaitez configurer automatiquement votre application, puis télécharger l’exemple de code, procédez comme suit :
>
> 1. Accédez à la <a href="https://aka.ms/aspnetcore2-1-aad-quickstart-v2/" target="_blank">page du portail Azure permettant d’inscrire des applications</a>.
> 1. Entrez un nom pour votre application, puis sélectionnez **Inscrire**.
> 1. Suivez les instructions pour télécharger et configurer automatiquement votre nouvelle application en un seul clic.
>
> ### <a name="manual-configuration"></a>Configuration manuelle
> Si vous souhaitez configurer manuellement votre application et votre exemple de code, utilisez les procédures suivantes.
> #### <a name="step-1-register-your-application"></a>Étape 1 : Inscrivez votre application
> 1. Connectez-vous au <a href="https://portal.azure.com/" target="_blank">portail Azure</a>.
> 1. Si vous avez accès à plusieurs locataires, utilisez le filtre **Annuaire + abonnement** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: dans le menu du haut pour sélectionner le locataire dans lequel vous voulez inscrire l’application.
> 1. Recherchez et sélectionnez **Azure Active Directory**.
> 1. Sous **Gérer**, sélectionnez **Inscriptions d’applications** > **Nouvelle inscription**.
> 1. Pour **Nom**, entrez un nom pour votre application. Par exemple, entrez **AspNetCore-Quickstart**. Les utilisateurs de votre application verront ce nom, que vous pourrez changer ultérieurement.
> 1. Pour **URI de redirection**, entrez **https://localhost:44321/signin-oidc** .
> 1. Sélectionnez **Inscription**.
> 1. Sous **Gérer**, sélectionnez **Authentification**.
> 1. Pour **URL de déconnexion du canal frontal**, entrez **https://localhost:44321/signout-oidc** .
> 1. Sous **Octroi implicite et flux hybrides**, sélectionnez **Jetons d’ID**.
> 1. Sélectionnez **Enregistrer**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Étape 1 : Configurer votre application dans le portail Azure
> Pour que l’exemple de code de ce guide de démarrage rapide fonctionne :
> - Pour **URI de redirection**, entrez **https://localhost:44321/** et **https://localhost:44321/signin-oidc** .
> - Pour **URL de déconnexion du canal frontal**, entrez **https://localhost:44321/signout-oidc** . 
>
> Le point de terminaison d’autorisation émettra des jetons d’ID de demande.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Apporter cette modification pour moi]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Déjà configuré](media/quickstart-v2-aspnet-webapp/green-check.png) Votre application est configurée avec ces attributs.

#### <a name="step-2-download-the-aspnet-core-project"></a>Étape 2 : Télécharger le projet ASP.NET Core

> [!div renderon="docs"]
> [Télécharger la solution ASP.NET Core](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/archive/aspnetcore3-1.zip)

> [!div renderon="portal" class="sxs-lookup"]
> Exécutez le projet.

> [!div renderon="portal" class="sxs-lookup" id="autoupdate" class="nextstepaction"]
> [Téléchargez l’exemple de code](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/archive/aspnetcore3-1.zip).

[!INCLUDE [active-directory-develop-path-length-tip](../../../includes/active-directory-develop-path-length-tip.md)]

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>Étape 3 : Votre application est configurée et prête à être exécutée
> Nous avons configuré votre projet avec les valeurs des propriétés de votre application, il est prêt à être exécuté.
> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`
> [!div renderon="docs"]
> #### <a name="step-3-configure-your-aspnet-core-project"></a>Étape 3 : Configurer votre projet ASP.NET Core
> 1. Extrayez l’archive .zip dans un dossier local proche de la racine de votre lecteur. Par exemple, extrayez-le dans *C:\Azure-Samples*.
> 
>    Nous vous recommandons d’extraire l’archive dans un répertoire près de la racine de votre lecteur pour éviter les erreurs dues à des limitations de longueur de chemin sur Windows.
> 1. Ouvrez la solution dans Visual Studio 2019.
> 1. Ouvrez le fichier *appsettings.json* pour modifier le code suivant :
>
>    ```json
>    "Domain": "Enter the domain of your tenant, e.g. contoso.onmicrosoft.com",
>    "ClientId": "Enter_the_Application_Id_here",
>    "TenantId": "common",
>    ```
>
>    - Remplacez `Enter_the_Application_Id_here` par l’ID d’application (client) de l’application que vous avez inscrite dans le portail Azure. Vous trouverez la valeur de l’**ID d’application (client)** dans la page **Vue d’ensemble** de l’application.
>    - Remplacez `common` par l’un des éléments suivants :
>       - Si votre application prend en charge les **Comptes dans cet annuaire organisationnel uniquement**, remplacez cette valeur par l’ID de l’annuaire (locataire) (un GUID) ou par le nom du locataire (par exemple, `contoso.onmicrosoft.com`). La valeur de l’**ID de l’annuaire (locataire)** se trouve dans la page **Vue d’ensemble** de l’application.
>       - Si votre application prend en charge **Comptes dans un annuaire organisationnel**, remplacez cette valeur par `organizations`.
>       - Si votre application prend en charge **Tous les utilisateurs de compte Microsoft**, conservez la valeur `common`.
>
> Pour ce guide de démarrage rapide, ne modifiez pas les autres valeurs du fichier *appsettings.json*.
>
> #### <a name="step-4-build-and-run-the-application"></a>Étape 4 : Générer et exécuter l’application
>
> Générez puis exécutez l’application dans Visual Studio en sélectionnant le menu **Débogage** > **Démarrer le débogage**, ou en appuyant sur la touche F5.
>
> Vous êtes invité à entrer vos informations d’identification, puis à donner votre consentement pour les autorisations dont a besoin votre application. Sélectionnez **Accepter** dans l’invite de consentement.
>
> :::image type="content" source="media/quickstart-v2-aspnet-core-webapp/webapp-01-consent.png" alt-text="Capture d’écran de la boîte de dialogue de consentement, indiquant les autorisations demandées par l’application à l’utilisateur":::
>
> Une fois que vous avez donné votre consentement aux autorisations demandées, l’application indique que vous avez réussi à vous connecter avec vos informations d’identification Azure Active Directory.
>
> :::image type="content" source="media/quickstart-v2-aspnet-core-webapp/webapp-02-signed-in.png" alt-text="Capture d’écran d’un navigateur web qui affiche l’application web en cours d’exécution et l’utilisateur connecté":::

## <a name="more-information"></a>Informations complémentaires

Cette section offre une vue d’ensemble du code requis pour connecter les utilisateurs. Cette vue d’ensemble peut être utile pour comprendre comment le code fonctionne, quels sont les principaux arguments et comment ajouter une connexion à une application ASP.NET Core existante.

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="how-the-sample-works"></a>Fonctionnement de l’exemple
>
> ![Diagramme de l’interaction entre le navigateur web, l’application web et la plateforme d’identités Microsoft dans l’exemple d’application.](media/quickstart-v2-aspnet-core-webapp/aspnetcorewebapp-intro.svg)

### <a name="startup-class"></a>Classe de démarrage

Le middleware (intergiciel) *Microsoft.AspNetCore.Authentication* utilise une classe `Startup` qui s’exécute lors du démarrage du processus d’hébergement :

```csharp
  public void ConfigureServices(IServiceCollection services)
  {
      services.AddAuthentication(OpenIdConnectDefaults.AuthenticationScheme)
          .AddMicrosoftIdentityWebApp(Configuration.GetSection("AzureAd"));

      services.AddControllersWithViews(options =>
      {
          var policy = new AuthorizationPolicyBuilder()
              .RequireAuthenticatedUser()
              .Build();
          options.Filters.Add(new AuthorizeFilter(policy));
      });
      services.AddRazorPages()
          .AddMicrosoftIdentityUI();
  }
```

La méthode `AddAuthentication()` configure le service pour ajouter l’authentification basée sur les cookies. Cette authentification est utilisée dans les scénarios de navigateur et pour définir la demande d’authentification sur OpenID Connect.

La ligne qui contient `.AddMicrosoftIdentityWebApp` ajoute à votre application l’authentification auprès de la plateforme d’identités Microsoft. L’application est ensuite configurée pour connecter les utilisateurs en fonction des informations suivantes contenues dans la section `AzureAD` du fichier de configuration *appsettings.json* :

| Clé *appsettings.json* | Description                                                                                                                                                          |
|------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `ClientId`             | ID d’application (client) de l’application inscrite dans le portail Azure.                                                                                       |
| `Instance`             | Point de terminaison du service d’émission de jeton de sécurité (STS) pour l’utilisateur à authentifier. Cette valeur est généralement `https://login.microsoftonline.com/`, ce qui indique le cloud public Azure. |
| `TenantId`             | Nom de votre locataire ou de l’ID de locataire (GUID), ou `common` pour connecter les utilisateurs avec des comptes professionnels ou scolaires, ou des comptes personnels Microsoft.                             |

La méthode `Configure()` contient deux méthodes importantes, `app.UseAuthentication()` et `app.UseAuthorization()`, qui activent leurs fonctionnalités nommées : Également dans la méthode `Configure()`, vous devez inscrire les routes Microsoft Identity Web avec au moins un appel à `endpoints.MapControllerRoute()` ou un appel à `endpoints.MapControllers()` :

```csharp
app.UseAuthentication();
app.UseAuthorization();

app.UseEndpoints(endpoints =>
{

    endpoints.MapControllerRoute(
        name: "default",
        pattern: "{controller=Home}/{action=Index}/{id?}");
    endpoints.MapRazorPages();
});

// endpoints.MapControllers(); // REQUIRED if MapControllerRoute() isn't called.
```

### <a name="attribute-for-protecting-a-controller-or-methods"></a>Attribut pour la protection d’un contrôleur ou des méthodes

Vous pouvez protéger un contrôleur ou les méthodes d’un contrôleur en utilisant l’attribut `[Authorize]`. Cet attribut limite l’accès au contrôleur ou aux méthodes en autorisant uniquement les utilisateurs authentifiés. Une demande d’authentification peut être démarrée pour accéder au contrôleur si l’utilisateur n’est pas authentifié.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Étapes suivantes

Le dépôt GitHub qui contient ce tutoriel ASP.NET Core inclut des instructions et d’autres exemples de code qui vous montrent comment :

- Ajouter l’authentification à une nouvelle application web ASP.NET Core
- Appeler Microsoft Graph, d’autres API Microsoft ou vos propres API web
- Ajouter une autorisation
- Connecter des utilisateurs dans des clouds nationaux ou avec des identités sociales

> [!div class="nextstepaction"]
> [Tutoriels concernant les applications web ASP.NET Core sur GitHub](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/)
