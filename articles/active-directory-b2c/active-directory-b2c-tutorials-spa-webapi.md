---
title: Didacticiel - Accorder l’accès à une API web ASP.NET Core depuis une application monopage à l’aide d’Azure Active Directory B2C | Microsoft Docs
description: Didacticiel sur l’utilisation d’Active Directory B2C pour protéger une API web .NET Core et l’appeler depuis une application monopage.
services: active-directory-b2c
author: davidmu1
ms.author: davidmu
ms.date: 3/02/2018
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory-b2c
ms.openlocfilehash: 0e9e3074e2cdd9ec3adc814779811d150cd11010
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2018
---
# <a name="tutorial-grant-access-to-an-aspnet-core-web-api-from-a-single-page-app-using-azure-active-directory-b2c"></a>Didacticiel : Accorder l’accès à une API web ASP.NET Core depuis une application monopage à l’aide d’Azure Active Directory B2C

Ce didacticiel vous montre comment appeler une ressource d’API web ASP.NET Core protégée par Azure Active Directory (Azure AD) B2C à partir d’une application monopage.

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * inscrire une API web dans votre client Azure AD B2C ;
> * définir et configurer les étendues d’une API web ;
> * accorder à une application des autorisations d’accès à l’API web ;
> * mettre à jour l’exemple de code pour utiliser Azure AD B2C afin de protéger une API web.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prérequis


* Suivez le [didacticiel Enable single-page app authentication with accounts using Azure Active Directory B2C](active-directory-b2c-tutorials-spa.md) (Activer l’authentification d’application monopage avec des comptes à l’aide d’Azure Active Directory B2C).
* Installer [Visual Studio 2017](https://www.visualstudio.com/downloads/) avec la charge de travail **Développement ASP.NET et web**.
* [Kit de développement logiciel (SDK) .NET Core 2.0.0](https://www.microsoft.com/net/core) ou version ultérieure
* Installez [Node.js](https://nodejs.org/en/download/)

## <a name="register-web-api"></a>Inscrire une API web

Les ressources d’API web doivent être inscrites dans votre client pour pouvoir accepter les [demandes de ressources protégées](../active-directory/develop/active-directory-dev-glossary.md#resource-server) des [applications clientes](../active-directory/develop/active-directory-dev-glossary.md#client-application) qui présentent un [jeton d’accès](../active-directory/develop/active-directory-dev-glossary.md#access-token) d’Azure Active Directory et y répondre. L’inscription établit [l’objet principal de service et d’application](../active-directory/develop/active-directory-dev-glossary.md#application-object) dans votre client. 

Connectez-vous au [portail Azure](https://portal.azure.com/) en tant qu’administrateur général de votre client Azure AD B2C.

[!INCLUDE [active-directory-b2c-switch-b2c-tenant](../../includes/active-directory-b2c-switch-b2c-tenant.md)]

1. Sélectionnez **Azure AD B2C** dans la liste des services du portail Azure.

2. Dans les paramètres B2C, cliquez sur **Applications**, puis sur **Ajouter**.

    Pour inscrire l’exemple d’API web dans votre client, utilisez les paramètres ci-dessous.
    
    ![Ajouter une nouvelle API](media/active-directory-b2c-tutorials-spa-webapi/web-api-registration.png)
    
    | Paramètre      | Valeur suggérée  | Description                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Name** | API Core Hello | Entrez un **nom** qui décrit votre API web pour les développeurs. |
    | **Inclure une application/API web** | OUI | Sélectionnez **Oui** pour une API web. |
    | **Autoriser le flux implicite** | OUI | Sélectionnez **Oui** puisque l’API utilise la [connexion OpenID Connect](active-directory-b2c-reference-oidc.md). |
    | **URL de réponse** | `http://localhost:44332` | Les URL de réponse sont des points de terminaison auxquels Azure AD B2C retourne les jetons demandés par votre API. Dans ce didacticiel, l’exemple d’API web s’exécute localement (localhost) et écoute sur le port 5000. |
    | **URI ID d’application** | HelloCoreAPI | L’URI identifie de façon unique l’API dans le client. Vous pouvez ainsi inscrire plusieurs API par client. Les [étendues](../active-directory/develop/active-directory-dev-glossary.md#scopes) régissent l’accès à la ressource d’API protégée et sont définies par l’URI ID d’application. |
    | **Client natif** | Non  | Dans la mesure où il s’agit d’une API web et pas d’un client natif, sélectionnez Non. |
    
3. Cliquez sur **Créer** pour inscrire votre API.

Les API inscrites sont indiquées dans la liste des applications du client Azure AD B2C. Sélectionnez votre API web dans la liste. Le volet de propriétés de l’API web s’affiche.

![Propriétés de l’API Web](./media/active-directory-b2c-tutorials-spa-webapi/b2c-web-api-properties.png)

Notez **l’ID du client d’application**. Cet ID identifie l’API de manière unique. Il est nécessaire pour configurer l’API ultérieurement dans le didacticiel.

L’inscription de l’API web avec Azure AD B2C définit une relation d’approbation. Étant donné que l’API est inscrite avec B2C, elle peut désormais approuver les jetons d’accès B2C reçus des autres applications.

## <a name="define-and-configure-scopes"></a>Définir et configurer des étendues

Les [étendues](../active-directory/develop/active-directory-dev-glossary.md#scopes) permettent de gérer l’accès aux ressources protégées. Elles sont utilisées par l’API web pour implémenter le contrôle d’accès basé sur les étendues. Par exemple, certains utilisateurs peuvent bénéficier d’un accès en lecture et en écriture tandis que d’autres peuvent disposer d’autorisations d’accès en lecture seule. Dans ce didacticiel, vous allez définir des autorisations d’accès en lecture pour l’API web.

### <a name="define-scopes-for-the-web-api"></a>Définir les étendues de l’API web

Les API inscrites sont indiquées dans la liste des applications du client Azure AD B2C. Sélectionnez votre API web dans la liste. Le volet de propriétés de l’API web s’affiche.

Cliquez sur **Étendues publiées (préversion)**.

Pour configurer les étendues de l’API, ajoutez les entrées ci-dessous. 

![étendues définies dans l’api web](media/active-directory-b2c-tutorials-spa-webapi/scopes-web-api.png)

| Paramètre      | Valeur suggérée  | Description                                        |
| ------------ | ------- | -------------------------------------------------- |
| **Portée** | demo.read | Accès en lecture à l’API de démonstration |

Cliquez sur **Enregistrer**.

Les étendues publiées peuvent servir à accorder à une application cliente une autorisation d’accès à l’API web.

### <a name="grant-app-permissions-to-web-api"></a>Accorder à une application des autorisations d’accès à l’API web

Pour appeler une API web protégée à partir d’une application, vous devez accorder à cette application des autorisations d’accès à l’API. Dans ce didacticiel, utilisez l’application monopage créée dans le [didacticiel Enable single-page app authentication with accounts using Azure Active Directory B2C](active-directory-b2c-tutorials-spa.md) (Activer l’authentification d’application monopage avec des comptes à l’aide d’Azure Active Directory B2C).

1. Dans le portail Azure, sélectionnez **Azure AD B2C** dans la liste des services, puis cliquez sur **Applications** pour afficher la liste des applications inscrites.

2. Dans la liste des applications, sélectionnez **Mon exemple d’application monopage** et cliquez sur **Accès d’API (préversion)**, puis sur **Ajouter**.

3. Dans la liste déroulante **Sélectionner une API**, sélectionnez votre API web inscrite **API Core Hello**.

4. Dans la liste déroulante **Sélectionnez des étendues**, sélectionnez les étendues que vous avez définies au cours de l’inscription de l’API web.

    ![sélection d’étendues pour l’application](media/active-directory-b2c-tutorials-spa-webapi/selecting-scopes-for-app.png)

5. Cliquez sur **OK**.

L’application **Mon exemple d’application monopage** est inscrite pour appeler **l’API Core Hello** protégée. Un utilisateur [s’authentifie](../active-directory/develop/active-directory-dev-glossary.md#authentication) auprès d’Azure AD B2C pour utiliser l’application de bureau WPF. L’application de bureau obtient un [octroi d’autorisation](../active-directory/develop/active-directory-dev-glossary.md#authorization-grant) d’Azure AD B2C pour accéder à l’API web protégée.

## <a name="update-code"></a>Mettre à jour le code

Maintenant que l’API web est inscrite et que les étendues sont définies, vous devez configurer le code de l’API web pour utiliser votre client Azure AD B2C. Dans ce didacticiel, vous configurez un exemple d’application web .NET Core que vous pouvez télécharger à partir de GitHub. 

[Téléchargez un fichier zip ](https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapi/archive/master.zip) ou clonez l’exemple d’application web à partir de GitHub.

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
        "ClientId": "<The Application ID for your web API obtained from the Azure portal>",
        "Policy": "<Your sign up sign in policy e.g. B2C_1_SiUpIn>",
        "ScopeRead": "demo.read"  
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

### <a name="configure-the-single-page-app"></a>Configurer l’application monopage

L’application monopage utilise Azure AD B2C pour l’inscription et la connexion de l’utilisateur, et appelle l’API web ASP.NET Core protégée. Vous devez mettre à jour l’application monopage pour appeler l’API web .NET Core.
Pour modifier les paramètres d’application :

1. Ouvrez le fichier `index.html` dans l’exemple d’application monopage Node.js.
2. Configurez l’exemple avec les informations d’inscription des locataires Azure AD B2C. Modifiez les valeurs **b2cScopes** et **webApi** dans les lignes de code suivantes :

    ```javascript
    // The current application coordinates were pre-registered in a B2C tenant.
    var applicationConfig = {
        clientID: '<Application ID for your SPA obtained from portal app registration>',
        authority: "https://login.microsoftonline.com/tfp/<your-tenant-name>.onmicrosoft.com/B2C_1_SiUpIn",
        b2cScopes: ["https://<Your tenant name>.onmicrosoft.com/HelloCoreAPI/demo.read"],
        webApi: 'http://localhost:58553/api/values',
    };
    ```

## <a name="run-the-spa-app-and-web-api"></a>Exécuter l’application SPA et l’API web

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

    La fenêtre de console affiche le numéro de port sur lequel l’application est hébergée.
    
    ```
    Listening on port 6420...
    ```

4. Utilisez un navigateur pour accéder à l’adresse `http://localhost:6420` pour afficher l’application.
5. Connectez-vous à l’aide de l’adresse e-mail et du mot de passe utilisés dans le [didacticiel Enable single-page app authentication with accounts using Azure Active Directory B2C](active-directory-b2c-tutorials-spa.md) (Activer l’authentification d’application monopage avec des comptes à l’aide d’Azure Active Directory B2C).
6. Cliquez sur le bouton **Appel d’API**.

Après vous être connecté ou inscrit avec un compte d’utilisateur, l’exemple appelle l’API web protégée et renvoie un résultat.

## <a name="clean-up-resources"></a>Supprimer des ressources

Vous pouvez utiliser votre client Azure AD B2C si vous envisagez d’effectuer d’autres didacticiels Azure AD B2C. Si vous n’en avez plus besoin, vous pouvez [supprimer votre client Azure AD B2C](active-directory-b2c-faqs.md#how-do-i-delete-my-azure-ad-b2c-tenant).

## <a name="next-steps"></a>Étapes suivantes

Cet article vous a expliqué en détail comment protéger une API web en inscrivant et en définissant des étendues dans Azure AD B2C. Apprenez-en davantage en parcourant les exemples de code Azure AD B2C disponibles.

> [!div class="nextstepaction"]
> [Exemples de code Azure AD B2C](https://azure.microsoft.com/resources/samples/?service=active-directory-b2c&sort=0)
