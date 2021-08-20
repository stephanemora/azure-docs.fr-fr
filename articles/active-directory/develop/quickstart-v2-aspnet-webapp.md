---
title: 'Démarrage rapide : Application web ASP.NET qui connecte les utilisateurs'
titleSuffix: Microsoft identity platform
description: Téléchargez et exécutez un exemple de code qui montre comment une application web ASP.NET peut connecter des utilisateurs Azure AD.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 09/25/2020
ms.author: jmprieur
ms.custom: devx-track-csharp, aaddev, identityplatformtop40, scenarios:getting-started, languages:ASP.NET, contperf-fy21q1
ms.openlocfilehash: 08c53f816b151026c03068451aa440f12a2cbc15
ms.sourcegitcommit: 16580bb4fbd8f68d14db0387a3eee1de85144367
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/24/2021
ms.locfileid: "112677801"
---
# <a name="quickstart-aspnet-web-app-that-signs-in-azure-ad-users"></a>Démarrage rapide : Application web ASP.NET qui connecte des utilisateurs Azure AD

Dans ce guide de démarrage rapide, vous allez télécharger et exécuter un exemple de code qui montre comment une application web ASP.NET peut connecter des utilisateurs avec des comptes Azure AD (Azure Active Directory).

> [!div renderon="docs"]
> Le diagramme suivant illustre le fonctionnement de l’exemple d’application :
>
> ![Diagramme de l’interaction entre le navigateur web, l’application web et la plateforme d’identités Microsoft dans l’exemple d’application.](media/quickstart-v2-aspnet-webapp/aspnetwebapp-intro.svg)
>
> ## <a name="prerequisites"></a>Prérequis
>
> * Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
> * [Visual Studio 2019](https://visualstudio.microsoft.com/vs/)
> * [.NET Framework 4.7.2+](https://dotnet.microsoft.com/download/visual-studio-sdks)
>
> ## <a name="register-and-download-the-app"></a>Inscrire et télécharger l’application
> Vous avez deux options pour commencer à créer votre application : configuration automatique ou configuration manuelle.
>
> ### <a name="automatic-configuration"></a>Configuration automatique
> Si vous souhaitez configurer automatiquement votre application, puis télécharger l’exemple de code, procédez comme suit :
>
> 1. Accédez à la <a href="https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AspNetWebAppQuickstartPage/sourceType/docs" target="_blank">page du portail Azure permettant d’inscrire des applications</a>.
> 1. Entrez un nom pour votre application, puis sélectionnez **Inscrire**.
> 1. Suivez les instructions pour télécharger et configurer automatiquement votre nouvelle application en un seul clic.
>
> ### <a name="manual-configuration"></a>Configuration manuelle
> Si vous souhaitez configurer manuellement votre application et votre exemple de code, utilisez les procédures suivantes.
>
> #### <a name="step-1-register-your-application"></a>Étape 1 : Inscrivez votre application
>
> 1. Connectez-vous au <a href="https://portal.azure.com/" target="_blank">portail Azure</a>.
> 1. Si vous avez accès à plusieurs locataires, utilisez le filtre **Annuaire + abonnement** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: dans le menu du haut pour sélectionner le locataire dans lequel vous voulez inscrire l’application.
> 1. Recherchez et sélectionnez **Azure Active Directory**.
> 1. Sous **Gérer**, sélectionnez **Inscriptions d’applications** > **Nouvelle inscription**.
> 1. Pour **Nom**, entrez un nom pour votre application. Par exemple, entrez **ASPNET-Quickstart**. Les utilisateurs de votre application verront ce nom, que vous pourrez changer ultérieurement.
> 1. Ajoutez **https://localhost:44368/** dans **URI de redirection**, puis sélectionnez **Inscrire**.
> 1. Sous **Gérer**, sélectionnez **Authentification**.
> 1. Dans la section **Implicit grant and hybrid flows** (Flux d’octroi implicite et flux hybride), sélectionnez **Jetons d’ID**.
> 1. Sélectionnez **Enregistrer**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Étape 1 : Configurer votre application dans le portail Azure
> Pour que l’exemple de code de ce guide de démarrage rapide fonctionne, entrez **https://localhost:44368/** pour **URI de redirection**.
>
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Apporter cette modification pour moi]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Déjà configuré](media/quickstart-v2-aspnet-webapp/green-check.png) Votre application est configurée avec cet attribut.

#### <a name="step-2-download-the-project"></a>Étape 2 : Téléchargez le projet

> [!div renderon="docs"]
> [Télécharger la solution Visual Studio 2019](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-DotNet/archive/master.zip)

> [!div renderon="portal" class="sxs-lookup"]
> Exécutez le projet avec Visual Studio 2019.
> [!div renderon="portal" id="autoupdate" class="sxs-lookup nextstepaction"]
> [Téléchargez l’exemple de code](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-DotNet/archive/master.zip).

[!INCLUDE [active-directory-develop-path-length-tip](../../../includes/active-directory-develop-path-length-tip.md)]

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>Étape 3 : Votre application est configurée et prête à être exécutée
> Nous avons configuré votre projet avec les valeurs des propriétés de votre application.

> [!div renderon="docs"]
> #### <a name="step-3-run-your-visual-studio-project"></a>Étape 3 : Exécuter votre projet Visual Studio

1. Extrayez le fichier .zip dans un dossier local proche du dossier racine. Par exemple, extrayez-le dans *C:\Azure-Samples*.
   
   Nous vous recommandons d’extraire l’archive dans un répertoire près de la racine de votre lecteur pour éviter les erreurs dues à des limitations de longueur de chemin sur Windows.
2. Ouvrez la solution dans Visual Studio (*AppModelv2-WebApp-OpenIDConnect-DotNet.sln*).
3. Selon la version de Visual Studio, vous devrez peut-être cliquer avec le bouton droit sur le projet **AppModelv2-WebApp-OpenIDConnect-DotNet**, puis sélectionner **Restaurer les packages NuGet**.
4. Ouvrez la console du Gestionnaire de package en sélectionnant **Afficher** > **Autres fenêtres** > **Console du Gestionnaire de package**. Exécutez ensuite `Update-Package Microsoft.CodeDom.Providers.DotNetCompilerPlatform -r`.

> [!div renderon="docs"]
> 5. Modifiez *Web.config* et remplacez les paramètres `ClientId`, `Tenant` et `redirectUri` par :
>    ```xml
>    <add key="ClientId" value="Enter_the_Application_Id_here" />
>    <add key="Tenant" value="Enter_the_Tenant_Info_Here" />
>    <add key="redirectUri" value="https://localhost:44368/" />
>    ```
>    Dans ce code :
>
>    - `Enter_the_Application_Id_here` est l’ID d’application (client) de l’inscription d’application que vous avez créée. Recherchez l’ID d’application (client) dans la page **Vue d’ensemble** de l’application dans **Inscriptions d’applications** au sein du portail Azure.
>    - `Enter_the_Tenant_Info_Here` est l’une des options suivantes :
>      - Si votre application prend en charge **Mon organisation uniquement**, remplacez cette valeur par l’ID d’annuaire (locataire) ou le nom du locataire (par exemple, `contoso.onmicrosoft.com`). Recherchez l’ID d’annuaire (locataire) dans la page **Vue d’ensemble** de l’application dans **Inscriptions d’applications** au sein du portail Azure.
>      - Si votre application prend en charge **Comptes dans un annuaire organisationnel**, remplacez cette valeur par `organizations`.
>      - Si votre application prend en charge **tous les utilisateurs de compte Microsoft**, remplacez cette valeur par `common`.
>    - `redirectUri` est l’**URI de redirection** que vous avez entré dans **Inscriptions d’applications** dans le portail Azure.
>

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

## <a name="more-information"></a>Informations complémentaires

Cette section offre une vue d’ensemble du code requis pour connecter les utilisateurs. Cette vue d’ensemble peut être utile pour comprendre comment le code fonctionne, quels sont les principaux arguments et comment ajouter une connexion à une application ASP.NET existante.

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="how-the-sample-works"></a>Fonctionnement de l’exemple
>
> ![Diagramme de l’interaction entre le navigateur web, l’application web et la plateforme d’identités Microsoft dans l’exemple d’application.](media/quickstart-v2-aspnet-webapp/aspnetwebapp-intro.svg)

### <a name="owin-middleware-nuget-packages"></a>Packages NuGet de l’intergiciel (middleware) OWIN

Vous pouvez configurer le pipeline d’authentification avec une authentification basée sur les cookies en utilisant OpenID Connect dans ASP.NET avec des packages du middleware OWIN. Vous pouvez installer ces packages en exécutant les commandes suivantes dans la console du Gestionnaire de package au sein de Visual Studio :

```powershell
Install-Package Microsoft.Owin.Security.OpenIdConnect
Install-Package Microsoft.Owin.Security.Cookies
Install-Package Microsoft.Owin.Host.SystemWeb
```

### <a name="owin-startup-class"></a>Classe de démarrage OWIN

Le middleware OWIN utilise une *classe de démarrage* qui s’exécute lors du démarrage du processus d’hébergement. Dans ce guide de démarrage rapide, le fichier *startup.cs* se trouve dans le dossier racine. Le code suivant montre les paramètres qu’utilise ce guide de démarrage rapide :

```csharp
public void Configuration(IAppBuilder app)
{
    app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

    app.UseCookieAuthentication(new CookieAuthenticationOptions());
    app.UseOpenIdConnectAuthentication(
        new OpenIdConnectAuthenticationOptions
        {
            // Sets the client ID, authority, and redirect URI as obtained from Web.config
            ClientId = clientId,
            Authority = authority,
            RedirectUri = redirectUri,
            // PostLogoutRedirectUri is the page that users will be redirected to after sign-out. In this case, it's using the home page
            PostLogoutRedirectUri = redirectUri,
            Scope = OpenIdConnectScope.OpenIdProfile,
            // ResponseType is set to request the code id_token, which contains basic information about the signed-in user
            ResponseType = OpenIdConnectResponseType.CodeIdToken,
            // ValidateIssuer set to false to allow personal and work accounts from any organization to sign in to your application
            // To only allow users from a single organization, set ValidateIssuer to true and the 'tenant' setting in Web.config to the tenant name
            // To allow users from only a list of specific organizations, set ValidateIssuer to true and use the ValidIssuers parameter
            TokenValidationParameters = new TokenValidationParameters()
            {
                ValidateIssuer = false // Simplification (see note below)
            },
            // OpenIdConnectAuthenticationNotifications configures OWIN to send notification of failed authentications to the OnAuthenticationFailed method
            Notifications = new OpenIdConnectAuthenticationNotifications
            {
                AuthenticationFailed = OnAuthenticationFailed
            }
        }
    );
}
```

> |Where  | Description |
> |---------|---------|
> | `ClientId`     | ID de l’application inscrite dans le portail Azure. |
> | `Authority`    | Point de terminaison du service d’émission de jeton de sécurité (STS) pour l’utilisateur à authentifier. Il s’agit généralement de `https://login.microsoftonline.com/{tenant}/v2.0` pour le cloud public. Dans cette URL, *{tenant}* est le nom de votre locataire, votre ID de locataire ou `common` pour une référence au point de terminaison commun. (Le point de terminaison commun est utilisé pour les applications multilocataires.) |
> | `RedirectUri`  | URL vers laquelle les utilisateurs sont dirigés après authentification auprès de la plateforme d’identités Microsoft. |
> | `PostLogoutRedirectUri`     | URL vers laquelle les utilisateurs sont envoyés après validation. |
> | `Scope`     | Liste des étendues demandées, séparées par des espaces. |
> | `ResponseType`     | Demande que la réponse de l’authentification contienne un code d’autorisation et un jeton d’ID. |
> | `TokenValidationParameters`     | Liste de paramètres pour la validation du jeton. Dans ce cas, `ValidateIssuer` a la valeur `false` pour indiquer qu’il peut accepter des connexions à partir de tout type de compte : personnel, professionnel ou scolaire. |
> | `Notifications`     | Liste de délégués qui peuvent être exécutés sur des messages `OpenIdConnect`. |


> [!NOTE]
> Le paramètre `ValidateIssuer = false` est une simplification destinée aux seules fins de ce guide de démarrage rapide. Dans les applications réelles, validez l’émetteur. Pour savoir comment procéder, consultez les exemples.

### <a name="authentication-challenge"></a>Demande d’authentification

Vous pouvez forcer un utilisateur à se connecter en effectuant une demande d’authentification dans votre contrôleur :

```csharp
public void SignIn()
{
    if (!Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge(
            new AuthenticationProperties{ RedirectUri = "/" },
            OpenIdConnectAuthenticationDefaults.AuthenticationType);
    }
}
```

> [!TIP]
> La demande d’une demande d’authentification avec cette méthode est facultative. Normalement, vous l’utilisez quand vous souhaitez qu’une vue soit accessible par des utilisateurs à la fois authentifiés et non authentifiés. Vous pouvez également protéger les contrôleurs à l’aide de la méthode décrite dans la section suivante.

### <a name="attribute-for-protecting-a-controller-or-a-controller-actions"></a>Attribut pour protéger un contrôleur ou les actions d’un contrôleur

Vous pouvez protéger un contrôleur ou les actions d’un contrôleur en utilisant l’attribut `[Authorize]`. Cet attribut limite l’accès au contrôleur ou aux actions en autorisant uniquement les utilisateurs authentifiés à accéder aux actions dans le contrôleur. Une demande d’authentification se produit alors automatiquement quand un utilisateur non authentifié tente d’accéder à l’une des actions ou à l’un des contrôleurs décorés par l’attribut `[Authorize]`.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Étapes suivantes

Pour apprendre à créer, étape par étape, des applications et des fonctionnalités, et pour obtenir une explication complète de ce démarrage rapide, essayez le tutoriel ASP.NET.

> [!div class="nextstepaction"]
> [Ajouter une connexion à une application web ASP.NET](tutorial-v2-asp-webapp.md)
