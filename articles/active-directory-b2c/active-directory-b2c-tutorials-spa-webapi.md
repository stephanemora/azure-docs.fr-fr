---
title: 'Didacticiel : Accorder l’accès à une API web ASP.NET Core dans une application monopage'
titleSuffix: Azure AD B2C
description: Dans ce tutoriel, découvrez comment utiliser Active Directory B2C pour protéger une API web .NET Core et l’appeler depuis une application Node.js monopage.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 07/24/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: e5ced0c3e4ad05c33866548acb09189e03ed22f4
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/09/2019
ms.locfileid: "74949702"
---
# <a name="tutorial-grant-access-to-an-aspnet-core-web-api-from-a-single-page-application-using-azure-active-directory-b2c"></a>Didacticiel : Accorder l’accès à une API web ASP.NET Core dans une application monopage à l’aide d’Azure Active Directory B2C

Ce tutoriel vous montre comment appeler une ressource d’API web ASP.NET Core protégée par Azure Active Directory B2C (Azure AD B2C) dans une application monopage.

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Ajouter une application d’API web
> * Configurer des étendues pour une API web
> * Accorder des autorisations à l’API web
> * Configurer l’exemple pour utiliser l’application

## <a name="prerequisites"></a>Prérequis

* Effectuez les étapes et les prérequis du [Tutoriel : Activer l’authentification dans une application monopage avec Azure Active Directory B2C](active-directory-b2c-tutorials-spa.md).
* Visual Studio 2019 ou ultérieur, ou Visual Studio Code
* .NET Core 2.2 ou ultérieur
* Node.js

## <a name="add-a-web-api-application"></a>Ajouter une application d’API web

[!INCLUDE [active-directory-b2c-appreg-webapi](../../includes/active-directory-b2c-appreg-webapi.md)]

## <a name="configure-scopes"></a>Configurer des étendues

Les étendues permettent de gérer l'accès aux ressources protégées. Elles sont utilisées par l’API web pour implémenter le contrôle d’accès basé sur les étendues. Par exemple, certains utilisateurs peuvent bénéficier d’un accès en lecture et en écriture tandis que d’autres peuvent disposer d’autorisations d’accès en lecture seule. Dans ce tutoriel, vous définissez des autorisations d’accès en lecture et en écriture pour l’API web.

[!INCLUDE [active-directory-b2c-scopes](../../includes/active-directory-b2c-scopes.md)]

Enregistrez la valeur sous **ÉTENDUES** de l’étendue `demo.read` pour l’utiliser plus tard, quand vous configurerez l’application monopage. La valeur d’étendue complète est similaire à `https://contosob2c.onmicrosoft.com/api/demo.read`.

## <a name="grant-permissions"></a>Accorder des autorisations

Pour appeler une API web protégée à partir d’une autre application, vous devez accorder à cette application les autorisations d’accès à l’API web.

Dans le tutoriel sur les prérequis, vous avez créé une application web nommée *webapp1*. Dans ce tutoriel, vous configurez cette application pour appeler l’API web que vous avez créée dans la section précédente, *webapi1*.

[!INCLUDE [active-directory-b2c-permissions-api](../../includes/active-directory-b2c-permissions-api.md)]

Votre application web monopage est inscrite pour appeler l’API web protégée. Un utilisateur s’authentifie auprès d’Azure AD B2C pour utiliser l’application monopage. L’application monopage obtient un octroi d’autorisation d’Azure AD B2C pour accéder à l’API web protégée.

## <a name="configure-the-sample"></a>Configurer l'exemple

Maintenant que l’API web est inscrite et que les étendues sont définies, vous devez configurer le code de l’API web pour utiliser votre locataire Azure AD B2C. Dans ce tutoriel, vous configurez un exemple d’application web .NET Core que vous téléchargez à partir de GitHub.

[Téléchargez une \*archive .zip](https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapi/archive/master.zip) ou clonez l’exemple de projet d’API web à partir de GitHub.

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapi.git
```

### <a name="configure-the-web-api"></a>Configurer l’API web

1. Ouvrez le fichier <em>B2C-WebApi/**appsettings.json**</em> dans Visual Studio ou Visual Studio Code.
1. Modifiez le bloc `AzureAdB2C` de façon à refléter le nom de votre locataire, l’ID d’application de l’application d’API web, le nom de votre stratégie d’inscription/de connexion, et les étendues que vous avez définies précédemment. Le bloc doit se présenter comme dans l’exemple suivant (avec les valeurs appropriées pour `Tenant` et `ClientId`) :

    ```json
    "AzureAdB2C": {
      "Tenant": "<your-tenant-name>.onmicrosoft.com",
      "ClientId": "<webapi-application-ID>",
      "Policy": "B2C_1_signupsignin1",

      "ScopeRead": "demo.read",
      "ScopeWrite": "demo.write"
    },
    ```

#### <a name="enable-cors"></a>Activez CORS

Pour autoriser votre application monopage à appeler l’API web ASP.NET Core, vous devez activer [CORS](https://docs.microsoft.com/aspnet/core/security/cors) dans l’API web.

1. Dans *Startup.cs*, ajoutez CORS à la méthode `ConfigureServices()`.

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddCors();
    ```

1. En outre, dans la méthode `ConfigureServices()`, définissez la valeur de `jwtOptions.Authority` sur l’URI de l’émetteur de jeton suivant.

    Remplacez `<your-tenant-name>` par le nom de votre locataire B2C.

    ```csharp
    jwtOptions.Authority = $"https://<your-tenant-name>.b2clogin.com/{Configuration["AzureAdB2C:Tenant"]}/{Configuration["AzureAdB2C:Policy"]}/v2.0";
    ```

1. Dans la méthode `Configure()`, configurez CORS.

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
    {
        app.UseCors(builder =>
            builder.WithOrigins("http://localhost:6420").AllowAnyHeader().AllowAnyMethod());
    ```

1. (Visual Studio uniquement) Sous **Propriétés** dans l’Explorateur de solutions, ouvrez le fichier *launchSettings.json*, puis recherchez le bloc `iisExpress`.
1. (Visual Studio uniquement) Mettez à jour la valeur de `applicationURL` avec le numéro de port que vous avez spécifié lors de l’inscription de l’application *webapi1* dans une étape précédente. Par exemple :

    ```json
    "iisExpress": {
      "applicationUrl": "http://localhost:5000/",
      "sslPort": 0
    }
    ```

### <a name="configure-the-single-page-application"></a>Configurer l’application monopage

L’application mono du [tutoriel précédent](active-directory-b2c-tutorials-spa.md) de la série utilise Azure AD B2C pour l’inscription et la connexion des utilisateurs, et appelle l’API web ASP.NET Core protégée par le locataire de démonstration *frabrikamb2c*.

Dans cette section, vous mettez à jour l’application monopage pour appeler l’API web ASP.NET Core protégée par *votre* locataire Azure AD B2C, que vous exécutez sur votre machine locale.

Pour modifier les paramètres dans l’application monopage :

1. Ouvrez le fichier *index.html* dans le projet [active-directory-b2c-javascript-msal-singlepageapp][github-js-spa] que vous avez téléchargé ou cloné dans le tutoriel précédent.
1. Configurez l’exemple avec l’URI pour l’étendue *demo.read* que vous avez créée précédemment et l’URL de l’API web.
    1. Dans la définition de `appConfig`, remplacez la valeur de `b2cScopes` par l’URI complet de l’étendue (la valeur **ÉTENDUE** que vous avez enregistrée précédemment).
    1. Remplacez la valeur `webApi` par l’URI de redirection que vous avez ajouté lors de l’inscription de l’application API Web dans une étape antérieure.

    La définition de `appConfig` doit être similaire au bloc de code suivant (avec le nom de votre locataire à la place de `<your-tenant-name>`) :

    ```javascript
    // The current application coordinates were pre-registered in a B2C tenant.
    var appConfig = {
      b2cScopes: ["https://<your-tenant-name>.onmicrosoft.com/api/demo.read"],
      webApi: "http://localhost:5000/"
    };
    ```

## <a name="run-the-spa-and-web-api"></a>Exécuter l’application monopage et l’API web

Enfin, vous exécutez l’API web ASP.NET Core et l’application monopage Node.js sur votre machine locale. Ensuite, vous vous connectez à l’application monopage et vous appuyez sur un bouton pour envoyer une demande à l’API protégée.

Bien que les deux applications s’exécutent localement dans ce tutoriel, elles utilisent Azure AD B2C pour sécuriser l’inscription/la connexion et pour accorder l’accès à l’API web protégée.

### <a name="run-the-aspnet-core-web-api"></a>Exécuter l’API web ASP.NET Core

Dans Visual Studio, appuyez sur **F5** pour générer et déboguer la solution *B2C-WebAPI.sln*. Au lancement du projet, une page web s’affiche dans votre navigateur par défaut pour indiquer que l’API est disponible pour des demandes.

Si vous préférez utiliser l’interface CLI `dotnet` au lieu de Visual Studio :

1. Ouvrez une fenêtre de console et accédez au répertoire contenant le fichier *\*.csproj*. Par exemple :

    `cd active-directory-b2c-dotnetcore-webapi/B2C-WebApi`

1. Générez et exécutez l’API web en exécutant `dotnet run`.

    Quand l’API est opérationnelle, vous devez voir une sortie similaire à ce qui suit (pour le tutoriel, vous pouvez ignorer sans problème les avertissements `NETSDK1059`) :

    ```console
    $ dotnet run
    Hosting environment: Production
    Content root path: /home/user/active-directory-b2c-dotnetcore-webapi/B2C-WebApi
    Now listening on: http://localhost:5000
    Application started. Press Ctrl+C to shut down.
    ```

### <a name="run-the-single-page-app"></a>Exécuter l’application monopage

1. Ouvrez une fenêtre de console et accédez au répertoire contenant l’exemple Node.js. Par exemple :

    `cd active-directory-b2c-javascript-msal-singlepageapp`

1. Exécutez les commandes suivantes :

    ```console
    npm install && npm update
    node server.js
    ```

    La fenêtre de console affiche le numéro de port de l’endroit où est hébergée l’application.

    ```console
    Listening on port 6420...
    ```

1. Accédez à `http://localhost:6420` dans votre navigateur pour voir l’application.
1. Connectez-vous en utilisant l’adresse e-mail et le mot de passe que vous avez utilisés dans le [tutoriel précédent](active-directory-b2c-tutorials-spa.md). Une fois la connexion établie, vous voyez normalement le message `User 'Your Username' logged-in`.
1. Sélectionnez le bouton **Call Web API** (Appeler l’API web). L’application monopage obtient un octroi d’autorisation d’Azure AD B2C, puis accède à l’API web protégée pour afficher le contenu de sa page d’index :

    ```Output
    Web APi returned:
    "<html>\r\n<head>\r\n  <title>Azure AD B2C API Sample</title>\r\n ...
    ```

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à :

> [!div class="checklist"]
> * Ajouter une application d’API web
> * Configurer des étendues pour une API web
> * Accorder des autorisations à l’API web
> * Configurer l’exemple pour utiliser l’application

Maintenant que vous avez vu une application monopage demander une ressource à partir d’une API web protégée, vous pouvez mieux comprendre comment ces types d’application interagissent entre elles et avec Azure AD B2C.

> [!div class="nextstepaction"]
> [Types d’applications pouvant être utilisés dans Azure Active Directory B2C >](active-directory-b2c-apps.md)

<!-- Links - EXTERNAL -->
[github-js-spa]: https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp
