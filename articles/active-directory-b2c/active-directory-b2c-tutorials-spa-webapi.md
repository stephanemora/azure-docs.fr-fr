---
title: Tutoriel - Accorder l’accès à une API web ASP.NET Core dans une application monopage - Azure Active Directory B2C | Microsoft Docs
description: Didacticiel sur l’utilisation d’Active Directory B2C pour protéger une API web .NET Core et l’appeler depuis une application monopage.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.author: davidmu
ms.date: 02/04/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 13fedae2798311a59a5cee2805ce9e09b1bd5a0f
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64724677"
---
# <a name="tutorial-grant-access-to-an-aspnet-core-web-api-from-a-single-page-application-using-azure-active-directory-b2c"></a>Didacticiel : Accorder l’accès à une API web ASP.NET Core dans une application monopage à l’aide d’Azure Active Directory B2C

Ce tutoriel vous montre comment appeler une ressource d’API web ASP.NET Core protégée par Azure Active Directory (Azure AD) B2C dans une application monopage.

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Ajouter une application d’API web
> * Configurer des étendues pour une API web
> * Accorder des autorisations à l’API web
> * Configurer l’exemple pour utiliser l’application

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prérequis

Effectuez les étapes et les prérequis du [Tutoriel : Activer l’authentification d’application monopage avec des comptes à l’aide d’Azure Active Directory B2C](active-directory-b2c-tutorials-spa.md).

## <a name="add-a-web-api-application"></a>Ajouter une application d’API web

Les ressources d’API web doivent être inscrites auprès de votre locataire pour pouvoir accepter et répondre aux requêtes de ressources protégées émanant des applications clientes qui présentent un jeton d’accès.

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Veillez à utiliser l’annuaire qui contient votre locataire Azure AD B2C en cliquant sur le **filtre Répertoire et abonnement** dans le menu du haut et en choisissant l’annuaire qui contient votre locataire.
3. Choisissez **Tous les services** dans le coin supérieur gauche du portail Azure, puis recherchez et sélectionnez **Azure AD B2C**.
4. Sélectionnez **Applications**, puis **Ajouter**.
5. Entrez un nom pour l’application. Par exemple, *webapi1*.
6. Pour **inclure l’application web/l’API web** et **autoriser un flux implicite**, sélectionnez **Oui**.
7. Pour l’**URL de réponse**, entrez un point de terminaison où Azure AD B2C doit retourner les jetons demandés par votre application. Dans ce tutoriel, l'exemple s'exécute localement et écoute `https://localhost:5000`.
8. Pour l’**L’URI de l’ID d’application**, entrez l’identificateur utilisé pour votre API web. L’identificateur complet URI, y compris le domaine, est généré pour vous. Par exemple : `https://contosotenant.onmicrosoft.com/api`.
9. Cliquez sur **Créer**.
10. Sur la page des propriétés, enregistrez l'ID d'application que vous utiliserez pour configurer l'application web.

## <a name="configure-scopes"></a>Configurer des étendues

Les étendues permettent de gérer l'accès aux ressources protégées. Elles sont utilisées par l’API web pour implémenter le contrôle d’accès basé sur les étendues. Par exemple, certains utilisateurs peuvent bénéficier d’un accès en lecture et en écriture tandis que d’autres peuvent disposer d’autorisations d’accès en lecture seule. Dans ce didacticiel, vous allez définir des autorisations d’accès en lecture pour l’API web.

1. Sélectionnez **Applications**, puis *webapi1*.
2. Sélectionnez **Étendues publiées**.
3. Pour **Étendue**, entrez `Hello.Read`, puis pour la description, entrez `Read access to hello`.
4. Pour **Étendue**, entrez `Hello.Write`, puis pour la description, entrez `Write access to hello`.
5. Cliquez sur **Enregistrer**.

Les étendues publiées peuvent servir à accorder à une application cliente une autorisation d’accès à l’API web.

## <a name="grant-permissions"></a>Accorder des autorisations

Pour appeler une API web protégée à partir d'une application, vous devez accorder à cette application les autorisations d'accès à l'API. Dans le cadre du tutoriel de prérequis, vous avez créé dans Azure AD B2C une application web nommée *webapp1*. Vous allez utiliser cette application pour appeler l’API web.

1. Sélectionnez **Applications**, puis *webapp1*.
2. Sélectionnez **Accès aux API**, puis sélectionnez **Ajouter**.
3. Dans la liste déroulante **Sélectionner une API**, sélectionnez *webapi1*.
4. Dans la liste déroulante **Sélectionnez des étendues**, sélectionnez les étendues **Hello.Read** et **Hello.Write** que vous avez définies précédemment.
5. Cliquez sur **OK**.

L’application **Mon exemple d’application monopage** est inscrite pour appeler **l’API Core Hello** protégée. Un utilisateur s’authentifie auprès d’Azure AD B2C pour utiliser l’application monopage. L’application monopage obtient un octroi d’autorisation d’Azure AD B2C pour accéder à l’API web protégée.

## <a name="configure-the-sample"></a>Configurer l'exemple

Maintenant que l’API web est inscrite et que les étendues sont définies, vous devez configurer le code de l’API web pour utiliser votre locataire Azure AD B2C. Dans ce tutoriel, vous allez configurer un exemple d’application web .NET Core que vous pouvez télécharger à partir de GitHub. [Téléchargez un fichier zip ](https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapi/archive/master.zip) ou clonez l’exemple d’application web à partir de GitHub.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapi.git
```

### <a name="configure-the-web-api"></a>Configurer l’API web

1. Ouvrez la solution **B2C-WebAPI.sln** dans Visual Studio.
2. Ouvrez le fichier **appsettings.json**. Mettez à jour les valeurs suivantes pour configurer l’API web pour utiliser votre locataire :

    ```javascript
    "AzureAdB2C": 
      {
        "Tenant": "<your tenant name>.onmicrosoft.com", 
        "ClientId": "<application-ID>",
        "Policy": "B2C_1_signupsignin1>",
        "ScopeRead": "Hello.Read"  
      },
    ```

#### <a name="enable-cors"></a>Activez CORS

Pour autoriser votre application monopage à appeler l’API web ASP.NET Core, vous devez activer [CORS](https://docs.microsoft.com/aspnet/core/security/cors).

1. Dans **Startup.cs**, ajoutez CORS à la méthode `ConfigureServices()`.

    ```C#
    public void ConfigureServices(IServiceCollection services) {
      services.AddCors();
    ```

2. Dans **Startup.cs**, configurez CORS dans la méthode `Configure()`.

    ```C#
    public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory) {
      app.UseCors(builder =>
        builder.WithOrigins("http://localhost:6420").AllowAnyHeader().AllowAnyMethod());
    ```

3. Ouvrez le fichier **launchSettings.json** sous **Propriétés**, localisez le paramètre **iisSettings** *applicationURL* et définissez le numéro de port sur celui qui est inscrit pour l’URL de réponse de l’API `http://localhost:5000`.

### <a name="configure-the-single-page-application"></a>Configurer l’application monopage

L’application monopage utilise Azure AD B2C pour l’inscription et la connexion de l’utilisateur, et appelle l’API web ASP.NET Core protégée. Vous devez mettre à jour l’application monopage pour appeler l’API web .NET Core.

Pour modifier les paramètres d’application :

1. Ouvrez le fichier `index.html` .
2. Configurez l’exemple avec les informations d’inscription des locataires Azure AD B2C. Dans le code suivant, ajoutez votre nom de client à **b2cScopes** et remplacez la valeur **webApi** par la valeur *applicationURL* que vous avez enregistrée précédemment :

    ```javascript
    // The current application coordinates were pre-registered in a B2C tenant.
    var applicationConfig = {
        clientID: '<application-ID>',
        authority: "https://<your-tenant-name>.b2clogin.com/tfp/<your-tenant-name>.onmicrosoft.com/B2C_1_signupsignin1",
        b2cScopes: ["https://<Your tenant name>.onmicrosoft.com/api/Hello.Read"],
        webApi: 'http://localhost:5000/api/values',
    };
    ```

## <a name="run-the-spa-application-and-web-api"></a>Exécuter l’application SPA et l’API web

Vous devez exécuter l’application monopage Node.js et l’API web .NET Core.

### <a name="run-the-aspnet-core-web-api"></a>Exécuter l’API web ASP.NET Core 

Appuyez sur **F5** pour déboguer la solution **B2C-WebAPI.sln** dans Visual Studio.

Au lancement du projet, une page web s’affiche dans votre navigateur par défaut pour indiquer que l’API est disponible afin d’envoyer des requêtes.

### <a name="run-the-single-page-app"></a>Exécuter l’application monopage

1. Lancez une invite de commande Node.js.
2. Accédez au répertoire contenant l’exemple Node.js. Par exemple `cd c:\active-directory-b2c-javascript-msal-singlepageapp`
3. Exécutez les commandes suivantes :
    ```
    npm install && npm update
    node server.js
    ```

    La fenêtre de console affiche le numéro de port de l’endroit où est hébergée l’application.
    
    ```
    Listening on port 6420...
    ```

4. Utilisez un navigateur pour accéder à l’adresse `http://localhost:6420` afin d’afficher l’application.
5. Connectez-vous à l’aide de l’adresse e-mail et du mot de passe utilisés dans le [didacticiel Enable single-page app authentication with accounts using Azure Active Directory B2C](active-directory-b2c-tutorials-spa.md) (Activer l’authentification d’application monopage avec des comptes à l’aide d’Azure Active Directory B2C).
6. Cliquez sur **Appeler une API**.

Après vous être connecté ou inscrit avec un compte d’utilisateur, l’exemple appelle l’API web protégée et renvoie un résultat.

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à :

> [!div class="checklist"]
> * Ajouter une application d’API web
> * Configurer des étendues pour une API web
> * Accorder des autorisations à l’API web
> * Configurer l’exemple pour utiliser l’application

> [!div class="nextstepaction"]
> [Tutoriel : Ajouter des fournisseurs d’identité à vos applications dans Azure Active Directory B2C](tutorial-add-identity-providers.md)
