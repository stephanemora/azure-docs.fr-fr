---
title: Prise en main d’Azure AD v2.0 avec les applications de serveur web ASP.NET | Microsoft Docs
description: Implémentation de la connexion Microsoft dans une solution ASP.NET avec une application basée sur un navigateur web traditionnel utilisant le standard OpenID Connect
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/11/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: fce6ab2c5068d1628860356a9df0dd33c73948b3
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/15/2019
ms.locfileid: "69511922"
---
# <a name="add-sign-in-with-microsoft-to-an-aspnet-web-app"></a>Ajouter la connexion avec Microsoft à une application ASP.NET

Ce guide explique comment implémenter la connexion avec Microsoft à l’aide d’une solution ASP.NET MVC avec une application basée sur un navigateur web traditionnel avec une norme OpenID Connect.

À la fin de ce guide, votre application sera en mesure d’accepter les connexions à l’aide de comptes personnels, comme les comptes outlook.com, live.com et autres. Cela comprend également les comptes professionnels et scolaires de toute entreprise ou organisation ayant intégré Azure Active Directory.

> Pour suivre ce guide, vous avez besoin de Visual Studio 2019.  Ni l’un, ni l’autre ne sont installés sur votre ordinateur ?  [Télécharger Visual Studio 2019 gratuitement](https://www.visualstudio.com/downloads/)

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Fonctionnement de l’exemple d’application de ce guide

![Fonctionnement de l’exemple d’application généré par ce tutoriel](media/active-directory-develop-guidedsetup-aspnetwebapp-intro/aspnetbrowsergeneral.svg)

L’exemple d’application que vous créez est basé sur un scénario dans lequel vous utilisez le navigateur pour accéder à un site web ASP.NET qui vous invite à vous authentifier via un bouton de connexion. Dans ce scénario, la majorité du travail pour afficher la page web se passe côté serveur.

## <a name="libraries"></a>Bibliothèques

Ce guide utilise les bibliothèques suivantes :

|Bibliothèque|Description|
|---|---|
|[Microsoft.Owin.Security.OpenIdConnect](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/)|Intergiciel qui permet à une application d’utiliser OpenIDConnect pour l’authentification.|
|[Microsoft.Owin.Security.Cookies](https://www.nuget.org/packages/Microsoft.Owin.Security.Cookies)|Intergiciel qui permet à une application de maintenir la session utilisateur à l’aide de cookies.|
|[Microsoft.Owin.Host.SystemWeb](https://www.nuget.org/packages/Microsoft.Owin.Host.SystemWeb)|Permet aux applications basées sur OWIN de s’exécuter sur IIS à l’aide du pipeline de requête ASP.NET.|

## <a name="set-up-your-project"></a>Configuration de votre projet

Cette section explique comment installer et configurer le pipeline d’authentification via l’intergiciel OWIN sur un projet ASP.NET à l’aide d’OpenID Connect.

> Vous préférez télécharger le projet Visual Studio de cet exemple ? [Téléchargez un projet](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-DotNet/archive/master.zip) et passez à l’étape [Configuration](#register-your-application) pour configurer l’exemple de code avant l’exécution.

### <a name="create-your-aspnet-project"></a>Créer votre projet ASP.NET

1. Dans Visual Studio : `File` > `New` > `Project`
2. Sous *Visual C# \Web*, sélectionnez `ASP.NET Web Application (.NET Framework)`.
3. Donnez un nom à votre application et cliquez sur *OK*.
4. Sélectionnez `Empty` et cochez la case pour ajouter des références `MVC`.

## <a name="add-authentication-components"></a>Ajouter les composants d’authentification

1. Dans Visual Studio : `Tools` > `Nuget Package Manager` > `Package Manager Console`
2. Ajoutez *les packages NuGet de l’intergiciel OWIN* en saisissant la commande suivante dans la fenêtre Console du gestionnaire de package :

    ```powershell
    Install-Package Microsoft.Owin.Security.OpenIdConnect
    Install-Package Microsoft.Owin.Security.Cookies
    Install-Package Microsoft.Owin.Host.SystemWeb
    ```

<!--start-collapse-->
> ### <a name="about-these-libraries"></a>À propos de ces bibliothèques
> Les bibliothèques ci-dessus activent l’authentification unique à l’aide d’OpenID Connect via l’authentification basée sur les cookies. Une fois que l’authentification est terminée et que le jeton qui représente l’utilisateur est envoyé à votre application, l’intergiciel OWIN crée un cookie de session. Le navigateur utilise ensuite ce cookie pour les requêtes ultérieures afin que l’utilisateur n’ait pas besoin de saisir à nouveau le mot de passe, et aucune vérification complémentaire n’est nécessaire.
<!--end-collapse-->

## <a name="configure-the-authentication-pipeline"></a>Configurer le pipeline d’authentification

Les étapes suivantes permettent de créer une classe de démarrage d’intergiciel OWIN pour configurer l’authentification OpenID Connect. Cette classe sera exécutée automatiquement au démarrage de votre processus IIS.

> [!TIP]
> Si votre projet n’a pas de fichier `Startup.cs` dans le dossier racine :
> 1. Cliquez avec le bouton droit sur le dossier racine du projet : > `Add` > `New Item...` > `OWIN Startup class`<br/>
> 2. Nommez-le `Startup.cs`.
>
>> Assurez-vous que la classe sélectionnée est une classe de démarrage OWIN et non une classe C# standard. Pour le savoir, vérifiez que `[assembly: OwinStartup(typeof({NameSpace}.Startup))]` s’affiche au-dessus de l’espace de noms.

1. Ajoutez les références *OWIN* et *Microsoft.IdentityModel* à `Startup.cs` :

    ```csharp
    using Microsoft.Owin;
    using Owin;
    using Microsoft.IdentityModel.Protocols.OpenIdConnect;
    using Microsoft.IdentityModel.Tokens;
    using Microsoft.Owin.Security;
    using Microsoft.Owin.Security.Cookies;
    using Microsoft.Owin.Security.OpenIdConnect;
    using Microsoft.Owin.Security.Notifications;
    ```

2. Remplacez la classe de démarrage par le code ci-dessous :

    ```csharp
    public class Startup
    {
        // The Client ID is used by the application to uniquely identify itself to Azure AD.
        string clientId = System.Configuration.ConfigurationManager.AppSettings["ClientId"];

        // RedirectUri is the URL where the user will be redirected to after they sign in.
        string redirectUri = System.Configuration.ConfigurationManager.AppSettings["RedirectUri"];

        // Tenant is the tenant ID (e.g. contoso.onmicrosoft.com, or 'common' for multi-tenant)
        static string tenant = System.Configuration.ConfigurationManager.AppSettings["Tenant"];

        // Authority is the URL for authority, composed by Azure Active Directory v2.0 endpoint and the tenant name (e.g. https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0)
        string authority = String.Format(System.Globalization.CultureInfo.InvariantCulture, System.Configuration.ConfigurationManager.AppSettings["Authority"], tenant);

        /// <summary>
        /// Configure OWIN to use OpenIdConnect 
        /// </summary>
        /// <param name="app"></param>
        public void Configuration(IAppBuilder app)
        {
            app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

            app.UseCookieAuthentication(new CookieAuthenticationOptions());
            app.UseOpenIdConnectAuthentication(
                new OpenIdConnectAuthenticationOptions
                {
                    // Sets the ClientId, authority, RedirectUri as obtained from web.config
                    ClientId = clientId,
                    Authority = authority,
                    RedirectUri = redirectUri,
                    // PostLogoutRedirectUri is the page that users will be redirected to after sign-out. In this case, it is using the home page
                    PostLogoutRedirectUri = redirectUri,
                    Scope = OpenIdConnectScope.OpenIdProfile,
                    // ResponseType is set to request the id_token - which contains basic information about the signed-in user
                    ResponseType = OpenIdConnectResponseType.IdToken,
                    // ValidateIssuer set to false to allow personal and work accounts from any organization to sign in to your application
                    // To only allow users from a single organizations, set ValidateIssuer to true and 'tenant' setting in web.config to the tenant name
                    // To allow users from only a list of specific organizations, set ValidateIssuer to true and use ValidIssuers parameter
                    TokenValidationParameters = new TokenValidationParameters()
                    {
                        ValidateIssuer = false // This is a simplification
                    },
                    // OpenIdConnectAuthenticationNotifications configures OWIN to send notification of failed authentications to OnAuthenticationFailed method
                    Notifications = new OpenIdConnectAuthenticationNotifications
                    {
                        AuthenticationFailed = OnAuthenticationFailed
                    }
                }
            );
        }

        /// <summary>
        /// Handle failed authentication requests by redirecting the user to the home page with an error in the query string
        /// </summary>
        /// <param name="context"></param>
        /// <returns></returns>
        private Task OnAuthenticationFailed(AuthenticationFailedNotification<OpenIdConnectMessage, OpenIdConnectAuthenticationOptions> context)
        {
            context.HandleResponse();
            context.Response.Redirect("/?errormessage=" + context.Exception.Message);
            return Task.FromResult(0);
        }
    }
    ```

> [!NOTE]
> Le paramètre `ValidateIssuer = false` est une simplification destinée aux seules fins de ce guide de démarrage rapide. Dans les applications réelles, vous devez valider l’émetteur. Consultez les exemples pour savoir comment procéder.

<!--start-collapse-->
> ### <a name="more-information"></a>Informations complémentaires
> Les paramètres que vous fournissez dans *OpenIDConnectAuthenticationOptions* serviront de coordonnées pour que l’application puisse communiquer avec Azure AD. Vous devez également configurer l’authentification des cookies comme indiqué dans le code ci-dessus, car l’intergiciel OpenID Connect utilise des cookies en arrière-plan. La valeur *ValidateIssuer* indique à OpenID Connect de ne pas restreindre l’accès à une organisation spécifique.
<!--end-collapse-->

## <a name="add-a-controller-to-handle-sign-in-and-sign-out-requests"></a>Ajouter un contrôleur pour gérer les demandes de connexion et de déconnexion

Cette étape explique comment créer un contrôleur pour exposer les méthodes de connexion et de déconnexion.

1.  Cliquez avec le bouton droit sur le dossier `Controllers` et sélectionnez `Add` > `Controller`.
2.  Sélectionnez `MVC (.NET version) Controller – Empty`.
3.  Cliquez sur *Ajouter*.
4.  Nommez-le `HomeController`, puis cliquez sur *Ajouter*.
5.  Ajoutez les références *OWIN* à la classe :

    ```csharp
    using Microsoft.Owin.Security;
    using Microsoft.Owin.Security.Cookies;
    using Microsoft.Owin.Security.OpenIdConnect;
    ```
    
6. Ajoutez les deux méthodes ci-dessous pour gérer la connexion à votre contrôleur et la déconnexion de celui-ci en lançant une demande d’authentification via du code :
    
    ```csharp
    /// <summary>
    /// Send an OpenID Connect sign-in request.
    /// Alternatively, you can just decorate the SignIn method with the [Authorize] attribute
    /// </summary>
    public void SignIn()
    {
        if (!Request.IsAuthenticated)
        {
            HttpContext.GetOwinContext().Authentication.Challenge(
                new AuthenticationProperties{ RedirectUri = "/" },
                OpenIdConnectAuthenticationDefaults.AuthenticationType);
        }
    }
    
    /// <summary>
    /// Send an OpenID Connect sign-out request.
    /// </summary>
    public void SignOut()
    {
        HttpContext.GetOwinContext().Authentication.SignOut(
                OpenIdConnectAuthenticationDefaults.AuthenticationType,
                CookieAuthenticationDefaults.AuthenticationType);
    }
    ```

## <a name="create-the-apps-home-page-to-sign-in-users-via-a-sign-in-button"></a>Créer la page d’accueil de l’application pour connecter des utilisateurs via un bouton de connexion

Dans Visual Studio, créez une vue pour ajouter le bouton de connexion et afficher les informations de l’utilisateur après l’authentification :

1.  Cliquez avec le bouton droit sur le dossier `Views\Home` et sélectionnez `Add View`.
2.  Nommez-le `Index`.
3.  Ajoutez le code HTML suivant, qui inclut le bouton de connexion, au fichier :

    ```html
    <html>
    <head>
        <meta name="viewport" content="width=device-width" />
        <title>Sign in with Microsoft Guide</title>
    </head>
    <body>
    @if (!Request.IsAuthenticated)
    {
        <!-- If the user is not authenticated, display the sign-in button -->
        <a href="@Url.Action("SignIn", "Home")" style="text-decoration: none;">
            <svg xmlns="http://www.w3.org/2000/svg" xml:space="preserve" width="300px" height="50px" viewBox="0 0 3278 522" class="SignInButton">
            <style type="text/css">.fil0:hover {fill: #4B4B4B;} .fnt0 {font-size: 260px;font-family: 'Segoe UI Semibold', 'Segoe UI'; text-decoration: none;}</style>
            <rect class="fil0" x="2" y="2" width="3174" height="517" fill="black" />
            <rect x="150" y="129" width="122" height="122" fill="#F35325" />
            <rect x="284" y="129" width="122" height="122" fill="#81BC06" />
            <rect x="150" y="263" width="122" height="122" fill="#05A6F0" />
            <rect x="284" y="263" width="122" height="122" fill="#FFBA08" />
            <text x="470" y="357" fill="white" class="fnt0">Sign in with Microsoft</text>
            </svg>
        </a>
    }
    else
    {
        <span><br/>Hello @System.Security.Claims.ClaimsPrincipal.Current.FindFirst("name").Value;</span>
        <br /><br />
        @Html.ActionLink("See Your Claims", "Index", "Claims")
        <br /><br />
        @Html.ActionLink("Sign out", "SignOut", "Home")
    }
    @if (!string.IsNullOrWhiteSpace(Request.QueryString["errormessage"]))
    {
        <div style="background-color:red;color:white;font-weight: bold;">Error: @Request.QueryString["errormessage"]</div>
    }
    </body>
    </html>
    ```

<!--start-collapse-->
> ### <a name="more-information"></a>Informations complémentaires
> Cette page ajoute un bouton de connexion au format SVG avec un arrière-plan noir :<br/>![Se connecter avec Microsoft](media/active-directory-develop-guidedsetup-aspnetwebapp-use/aspnetsigninbuttonsample.png)<br/> Pour d’autres boutons de connexion, accédez à [cette page](https://docs.microsoft.com/azure/active-directory/develop/active-directory-branding-guidelines "Conseils pour la personnalisation").
<!--end-collapse-->

## <a name="add-a-controller-to-display-users-claims"></a>Ajouter un contrôleur pour afficher les revendications de l’utilisateur
Ce contrôleur démontre les utilisations de l’attribut `[Authorize]` pour protéger un contrôleur. Cet attribut limite l’accès au contrôleur en autorisant uniquement les utilisateurs authentifiés. Le code ci-dessous tire parti de l’attribut pour afficher les revendications de l’utilisateur qui ont été récupérées dans le cadre de la connexion.

1.  Cliquez avec le bouton droit sur le dossier `Controllers` et sélectionnez `Add` > `Controller`
2.  Sélectionnez `MVC {version} Controller – Empty`.
3.  Cliquez sur *Ajouter*.
4.  Nommez-le `ClaimsController`.
5.  Remplacez le code de votre classe de contrôleur par le code ci-dessous afin d’ajouter l’attribut `[Authorize]` à la classe :

    ```csharp
    [Authorize]
    public class ClaimsController : Controller
    {
        /// <summary>
        /// Add user's claims to viewbag
        /// </summary>
        /// <returns></returns>
        public ActionResult Index()
        {
            var userClaims = User.Identity as System.Security.Claims.ClaimsIdentity;
    
            //You get the user’s first and last name below:
            ViewBag.Name = userClaims?.FindFirst("name")?.Value;
    
            // The 'preferred_username' claim can be used for showing the username
            ViewBag.Username = userClaims?.FindFirst("preferred_username")?.Value;
    
            // The subject/ NameIdentifier claim can be used to uniquely identify the user across the web
            ViewBag.Subject = userClaims?.FindFirst(System.Security.Claims.ClaimTypes.NameIdentifier)?.Value;
    
            // TenantId is the unique Tenant Id - which represents an organization in Azure AD
            ViewBag.TenantId = userClaims?.FindFirst("http://schemas.microsoft.com/identity/claims/tenantid")?.Value;
    
            return View();
        }
    }
    ```

<!--start-collapse-->
> ### <a name="more-information"></a>Informations complémentaires
> En raison de l’utilisation de l’attribut `[Authorize]`, toutes les méthodes de ce contrôleur peuvent uniquement être exécutées si l’utilisateur est authentifié. Si l’utilisateur n’est pas authentifié et tente d’accéder au contrôleur, OWIN initialise une demande d’authentification et oblige l’utilisateur à s’authentifier. Le code ci-dessus examine la collection de revendications pour les attributs utilisateur spécifiques inclus dans le jeton d’ID de l’utilisateur. Ces attributs incluent le nom complet et le nom d’utilisateur de l’utilisateur, ainsi que le sujet d’identificateur d’utilisateur global. Il contient également le *ID client*, qui représente l’identifiant de l’organisation de l’utilisateur. 
<!--end-collapse-->

## <a name="create-a-view-to-display-the-users-claims"></a>Créer une vue pour afficher les revendications de l’utilisateur

Dans Visual Studio, créez une vue pour afficher les revendications de l’utilisateur dans une page web :

1.  Cliquez avec le bouton droit sur le dossier `Views\Claims` et sélectionnez `Add View`.
2.  Nommez-le `Index`.
3.  Ajoutez le code HTML suivant au fichier :

    ```html
    <html>
    <head>
        <meta name="viewport" content="width=device-width" />
        <title>Sign in with Microsoft Sample</title>
        <link href="@Url.Content("~/Content/bootstrap.min.css")" rel="stylesheet" type="text/css" />
    </head>
    <body style="padding:50px">
        <h3>Main Claims:</h3>
        <table class="table table-striped table-bordered table-hover">
            <tr><td>Name</td><td>@ViewBag.Name</td></tr>
            <tr><td>Username</td><td>@ViewBag.Username</td></tr>
            <tr><td>Subject</td><td>@ViewBag.Subject</td></tr>
            <tr><td>TenantId</td><td>@ViewBag.TenantId</td></tr>
        </table>
        <br />
        <h3>All Claims:</h3>
        <table class="table table-striped table-bordered table-hover table-condensed">
        @foreach (var claim in System.Security.Claims.ClaimsPrincipal.Current.Claims)
        {
            <tr><td>@claim.Type</td><td>@claim.Value</td></tr>
        }
        </table>
        <br />
        <br />
        @Html.ActionLink("Sign out", "SignOut", "Home", null, new { @class = "btn btn-primary" })
    </body>
    </html>
    ```

## <a name="register-your-application"></a>Inscrivez votre application

Pour inscrire votre application et ajouter les informations d’inscription de l’application à votre solution, deux options s’offrent à vous :

### <a name="option-1-express-mode"></a>Option 1 : Mode Express

Vous pouvez inscrire rapidement votre application en procédant comme suit :

1. Accédez au nouveau volet [Portail Azure - Inscriptions d’applications](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AspNetWebAppQuickstartPage/sourceType/docs).
1. Saisissez un nom pour votre application et cliquez sur **Inscrire**.
1. Suivez les instructions pour télécharger et configurer automatiquement votre nouvelle application pour vous en un seul clic.

### <a name="option-2-advanced-mode"></a>Option 2 : Mode Avancé

Pour inscrire votre application et ajouter manuellement les informations d’inscription de l’application à votre solution, procédez comme suit :

1. Accédez à Visual Studio et :
   1. Dans l’Explorateur de solutions, sélectionnez le projet et examinez la fenêtre Propriétés (si vous ne voyez pas une fenêtre Propriétés, appuyez sur F4).
   1. Définissez l’option SSL activé sur `True`.
   1. Cliquez avec le bouton droit sur le projet dans Visual Studio, puis choisissez **Propriétés** et cliquez sur l’onglet **Web**. Dans la section *Serveurs*, modifiez *l’URL du projet* de manière à indiquer l’URL SSL.
   1. Copiez l’URL SSL. Vous allez ajouter cette URL à la liste d’URL de redirection dans la liste du portail d’inscription d’URL de redirection à l’étape suivante :<br/><br/>![Propriétés du projet](media/active-directory-develop-guidedsetup-aspnetwebapp-configure/vsprojectproperties.png)<br />
1. Connectez-vous au [portail Azure](https://portal.azure.com) avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Si votre compte vous propose un accès à plusieurs locataires, sélectionnez votre compte en haut à droite et définissez votre session de portail sur le locataire Azure AD souhaité.
1. Accédez à la page [Inscriptions des applications](https://go.microsoft.com/fwlink/?linkid=2083908) de la plateforme d’identité Microsoft pour les développeurs.
1. Sélectionnez **Nouvelle inscription**.
1. Lorsque la page **Inscrire une application** s’affiche, saisissez les informations d’inscription de votre application :
   1. Dans la section **Nom**, saisissez un nom d’application cohérent qui s’affichera pour les utilisateurs de l’application, par exemple `ASPNET-Tutorial`.
   1. Ajoutez l’URL SSL que vous avez copiée à partir de Visual Studio à l’étape 1 (par exemple `https://localhost:44368/`) dans **URL de réponse**, puis cliquez sur **Inscrire**.
1. Sélectionnez le menu **Authentification**, définissez les **Jetons d’ID** sous **Octroi implicite**, puis sélectionnez **Enregistrer**.
1. Ajoutez le code suivant dans le fichier `web.config` situé dans le dossier racine, sous la section `configuration\appSettings` :

    ```xml
    <add key="ClientId" value="Enter_the_Application_Id_here" />
    <add key="redirectUri" value="Enter_the_Redirect_URL_here" />
    <add key="Tenant" value="common" />
    <add key="Authority" value="https://login.microsoftonline.com/{0}/v2.0" />
    ```

1. Remplacez `ClientId` par l’ID de l’application que vous venez d’inscrire.
1. Remplacez `redirectUri` par l’URL SSL de votre projet.

## <a name="test-your-code"></a>Test de votre code

Pour tester votre application dans Visual Studio, appuyez sur **F5** afin d’exécuter votre projet. Le navigateur s’ouvre sur l’emplacement http://<span></span>localhost:{port} et vous voyez le bouton **Se connecter avec Microsoft**. Sélectionnez le bouton pour démarrer le processus de connexion.

Lorsque vous êtes prêt pour le test, utilisez un compte Microsoft Azure Active Directory (compte professionnel ou scolaire) ou un compte Microsoft personnel (<span>live.</span>com ou <span>outlook.</span>com) pour vous connecter.

![Se connecter avec Microsoft](media/active-directory-develop-guidedsetup-aspnetwebapp-test/aspnetbrowsersignin.png)
<br/><br/>
![Connexion à votre compte Microsoft](media/active-directory-develop-guidedsetup-aspnetwebapp-test/aspnetbrowsersignin2.png)

#### <a name="view-application-results"></a>Afficher les résultats de l’application

Une fois connecté, l’utilisateur est redirigé vers la page d’accueil de votre site web. La page d’accueil correspond à l’URL HTTPS qui est spécifiée dans les informations d’inscription de votre application, dans le portail d’inscription des applications de Microsoft. La page d’accueil comprend le message de bienvenue *« Bonjour \<Utilisateur>, »* un lien pour se déconnecter et un lien pour afficher les revendications de l’utilisateur. Le lien des revendications de l’utilisateur redirige vers le contrôleur *Claims* que vous avez créé précédemment.

### <a name="browse-to-see-the-users-claims"></a>Accéder aux revendications de l’utilisateur

Pour afficher les revendications de l’utilisateur, sélectionnez le lien pour accéder à la vue de contrôleur qui est disponible uniquement pour les utilisateurs authentifiés.

#### <a name="view-the-claims-results"></a>Afficher les résultats des revendications

Une fois que vous avez accédé à la vue de contrôleur, vous devez voir un tableau qui contient les propriétés de base de l’utilisateur :

|Propriété |Valeur |Description |
|---|---|---|
|**Name** |Nom complet de l’utilisateur | Prénom et nom de l’utilisateur
|**Nom d’utilisateur** |utilisateur<span>@domain.com</span> | Nom d’utilisateur employé pour identifier l’utilisateur.
|**Subject** |Objet |Chaîne qui identifie de manière unique l’utilisateur sur le web.|
|**Tenant ID** |Guid | **GUID** qui représente de manière unique l’organisation Azure AD de l’utilisateur.|

En outre, vous devez voir un tableau avec toutes les revendications qui se trouvent dans la requête d’authentification. Pour plus d’informations, consultez la [liste des revendications qui se trouvent dans un jeton d’ID AD Azure](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims).

### <a name="test-access-to-a-method-that-has-an-authorize-attribute-optional"></a>Tester l’accès à une méthode disposant d’un attribut Authorize (facultatif)

Pour tester l’accès en tant qu’utilisateur anonyme à un contrôleur protégé avec l’attribut `Authorize`, procédez comme suit :

1. Sélectionnez le lien de déconnexion de l’utilisateur et terminez le processus de déconnexion.
2. Dans le navigateur, tapez http://<span></span>localhost:{port}/claims pour accéder à votre contrôleur qui est protégé par l’attribut `Authorize`.

#### <a name="expected-results-after-access-to-a-protected-controller"></a>Résultats attendus après l’accès à un contrôleur protégé

Vous êtes invité à vous authentifier pour utiliser la vue de contrôleur protégé.

## <a name="advanced-options"></a>Options avancées

<!--start-collapse-->
### <a name="protect-your-entire-website"></a>Protéger l’ensemble de votre site web
Pour protéger tout votre site web, dans le fichier **Global.asax**, ajoutez l’attribut `AuthorizeAttribute` au filtre `GlobalFilters` dans la méthode `Application_Start` :

```csharp
GlobalFilters.Filters.Add(new AuthorizeAttribute());
```
<!--end-collapse-->

### <a name="restrict-who-can-sign-in-to-your-application"></a>Restreindre les utilisateurs autorisés à se connecter à votre application

Par défaut, lorsque vous générez l’application créée par ce guide, votre application accepte des connexions de comptes personnels (y compris outlook.com, live.com et autres) et de comptes professionnels et scolaires de n’importe quelle société ou organisation intégrées à Azure Active Directory. Il s’agit d’une option recommandée pour les applications SaaS.

Pour restreindre les utilisateurs qui peuvent se connecter à votre application, plusieurs options sont disponibles :

#### <a name="option-1-restrict-users-from-only-one-organizations-active-directory-instance-to-sign-in-to-your-application-single-tenant"></a>Option 1 : Limiter la connexion à votre application aux utilisateurs de l’instance Active Directory d’une seule organisation (un seul locataire)

Cette option est un scénario courant pour les *applications métier* : Si vous souhaitez que votre application accepte les connexions uniquement à partir des comptes qui appartiennent à une instance Azure Active Directory spécifique (notamment les *comptes invités* de cette instance), effectuez les étapes suivantes :

1. Dans le fichier **web.config**, remplacez la valeur du paramètre `Tenant` de `Common` par le nom du locataire de l’organisation, par exemple `contoso.onmicrosoft.com`.
2. Dans la classe [OWIN Startup](#configure-the-authentication-pipeline), définissez l’argument `ValidateIssuer` sur `true`.

#### <a name="option-2-restrict-access-to-your-application-to-users-in-a-specific-list-of-organizations"></a>Option 2 : Restreindre l’accès à votre application aux utilisateurs figurant dans une liste spécifique d’organisations

Vous pouvez limiter les connexions aux comptes d’utilisateurs d’une organisation Azure AD figurant dans la liste des organisations autorisées :
1. Dans la classe [OWIN Startup](#configure-the-authentication-pipeline), définissez l’argument `ValidateIssuer` sur `true`.
2. Définissez la valeur du paramètre `ValidIssuers` sur la liste des organisations autorisées.

#### <a name="option-3-use-a-custom-method-to-validate-issuers"></a>Option 3 : Utiliser une méthode personnalisée pour valider les émetteurs

Vous pouvez implémenter une méthode personnalisée pour valider les émetteurs à l’aide du paramètre **IssuerValidator**. Pour plus d’informations sur l’utilisation de ce paramètre, lisez cette rubrique concernant la [classe TokenValidationParameters](/previous-versions/visualstudio/dn464192(v=vs.114)).

## <a name="next-steps"></a>Étapes suivantes

Découvrez le fonctionnement des applications web qui appellent des API web.

### <a name="learn-the-steps-to-create-the-application-used-in-this-quickstart"></a>Découvrez les étapes permettant de créer l’application utilisée dans ce démarrage rapide

> [!div class="nextstepaction"]
> [Applications web appelant des API web]( https://aka.ms/msal-net-authorization-code)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

Aidez-nous à améliorer la plateforme d’identité Microsoft. Faites-nous part de votre avis en répondant à une petite enquête de deux questions.

> [!div class="nextstepaction"]
> [Enquête sur la plateforme des identités Microsoft](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)