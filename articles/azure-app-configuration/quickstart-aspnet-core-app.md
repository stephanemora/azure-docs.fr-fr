---
title: Démarrage rapide pour Azure App Configuration avec ASP.NET Core | Microsoft Docs
description: Guide de démarrage rapide pour utiliser Azure App Configuration avec des applications ASP.NET Core
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: quickstart
ms.tgt_pltfrm: ASP.NET Core
ms.workload: tbd
ms.date: 10/11/2019
ms.author: yegu
ms.openlocfilehash: 91712b3f730317e65cda7b48c8f5636b2fb9ab2c
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74185083"
---
# <a name="quickstart-create-an-aspnet-core-app-with-azure-app-configuration"></a>Démarrage rapide : Créer une application ASP.NET Core avec Azure App Configuration

Dans ce guide de démarrage rapide, vous intégrez Azure App Configuration dans une application ASP.NET Core pour centraliser le stockage et la gestion des paramètres d’application en dehors de votre code. ASP.NET Core génère un objet de configuration basé sur une paire clé-valeur unique, à l’aide des paramètres d’une ou de plusieurs sources de données qui sont spécifiées par une application. Ces sources de données sont appelées *fournisseurs de configuration*. Étant donné que le client .NET Core d’App Configuration est implémenté en tant que fournisseur, le service ressemble à une autre source de données.

## <a name="prerequisites"></a>Prérequis

- Abonnement Azure : [créez-en un gratuitement](https://azure.microsoft.com/free/)
- [Kit de développement logiciel (SDK) .NET Core](https://dotnet.microsoft.com/download)

## <a name="create-an-app-configuration-store"></a>Créer un magasin App Configuration

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Sélectionnez **Explorateur de configurations** >  **+ Créer** pour ajouter les paires clé-valeur suivantes :

    | Clé | Valeur |
    |---|---|
    | TestApp:Settings:BackgroundColor | Blanc |
    | TestApp:Settings:FontSize | 24 |
    | TestApp:Settings:FontColor | Noir |
    | TestApp:Settings:Message | Data from Azure App Configuration |

    Laissez **Étiquette** et **Type de contenu** vides pour l’instant.

## <a name="create-an-aspnet-core-web-app"></a>Créez une application web ASP.NET Core

Vous utilisez l’[interface de ligne de commande (CLI) .NET Core](https://docs.microsoft.com/dotnet/core/tools/) pour créer un projet d’application web MVC ASP.NET Core. Par rapport à Visual Studio, l’interface CLI .NET Core offre l’avantage d’être disponible sur les plateformes Windows, macOS et Linux.

1. Créez un nouveau dossier pour votre projet. Pour les besoins de ce guide de démarrage rapide, nommez-le *TestAppConfig*.

2. Dans le nouveau dossier, exécutez la commande suivante pour créer un projet d’application web MVC ASP.NET Core :

    ```CLI
        dotnet new mvc --no-https
    ```

## <a name="add-secret-manager"></a>Ajouter Secret Manager

Pour utiliser Secret Manager, ajoutez un élément `UserSecretsId` à votre fichier  *.csproj*.

- Ouvrez le fichier  *.csproj*. Ajoutez un élément `UserSecretsId` comme indiqué ici. Vous pouvez utiliser le même GUID ou remplacer cette valeur par votre propre valeur. Enregistrez le fichier .

    ```xml
    <Project Sdk="Microsoft.NET.Sdk.Web">

    <PropertyGroup>
        <TargetFramework>netcoreapp2.1</TargetFramework>
        <UserSecretsId>79a3edd0-2092-40a2-a04d-dcb46d5ca9ed</UserSecretsId>
    </PropertyGroup>

    <ItemGroup>
        <PackageReference Include="Microsoft.AspNetCore.App" />
        <PackageReference Include="Microsoft.AspNetCore.Razor.Design" Version="2.1.2" PrivateAssets="All" />
    </ItemGroup>

    </Project>
    ```

L’outil Secret Manager stocke les données sensibles pour les travaux de développement à l’extérieur de l’arborescence de votre projet. Cette approche empêche le partage accidentel des secrets d’une application au sein du code source. Pour plus d’informations sur Secret Manager, consultez [Stockage sécurisé des secrets d’application en développement dans ASP.NET Core](https://docs.microsoft.com/aspnet/core/security/app-secrets).

## <a name="connect-to-an-app-configuration-store"></a>Se connecter à un magasin App Configuration

1. Ajoutez une référence au package NuGet `Microsoft.Azure.AppConfiguration.AspNetCore` en exécutant la commande suivante :

    ```CLI
        dotnet add package Microsoft.Azure.AppConfiguration.AspNetCore --version 2.0.0-preview-010060003-1250
    ```
2. Exécutez la commande suivante pour restaurer les packages de votre projet :

    ```CLI
        dotnet restore
    ```
3. Ajoutez un secret nommé *ConnectionStrings:AppConfig* à Secret Manager.

    Ce secret contient la chaîne de connexion permettant d’accéder à votre magasin App Configuration. Dans la commande suivante, remplacez la valeur par la chaîne de connexion de votre magasin App Configuration.

    Cette commande doit être exécutée dans le même répertoire que le fichier *.csproj*.

    ```CLI
        dotnet user-secrets set ConnectionStrings:AppConfig <your_connection_string>
    ```

    > [!IMPORTANT]
    > Certains interpréteurs de commandes tronquent la chaîne de connexion, à moins qu’elle soit placée entre guillemets. Vérifiez que la sortie de la commande `dotnet user-secrets` affiche la chaîne de connexion complète. Si ce n’est pas le cas, réexécutez la commande en plaçant la chaîne de connexion entre guillemets.

    Secret Manager est utilisé uniquement pour tester l’application web localement. Une fois l’application déployée sur [Azure App Service](https://azure.microsoft.com/services/app-service/web), par exemple, vous utilisez un paramètre d’application **Chaînes de connexion** dans App Service au lieu de Secret Manager pour stocker la chaîne de connexion.

    L’API de configuration permet d’accéder à ce secret. Un signe deux-points (:) fonctionne dans le nom de configuration avec l’API de configuration sur toutes les plateformes prises en charge. Consultez [Configuration par environnement](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/index?tabs=basicconfiguration&view=aspnetcore-2.0).

4. Ouvrez *Program.cs*, puis ajoutez une référence au fournisseur App Configuration .NET Core.

    ```csharp
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

5. Mettez à jour la méthode `CreateWebHostBuilder` pour utiliser App Configuration en appelant la méthode `config.AddAzureAppConfiguration()`.
    
    > [!IMPORTANT]
    > `CreateHostBuilder` remplace `CreateWebHostBuilder` dans .NET Core 3.0.  Sélectionnez la syntaxe appropriée en fonction de votre environnement.

    ### <a name="update-createwebhostbuilder-for-net-core-2x"></a>Mettre à jour `CreateWebHostBuilder` pour .NET Core 2.x

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();
                config.AddAzureAppConfiguration(settings["ConnectionStrings:AppConfig"]);
            })
            .UseStartup<Startup>();
    ```

    ### <a name="update-createhostbuilder-for-net-core-3x"></a>Mettre à jour `CreateHostBuilder` pour .NET Core 3.x

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
        {
            var settings = config.Build();
            config.AddAzureAppConfiguration(settings["ConnectionStrings:AppConfig"]);
        })
        .UseStartup<Startup>());
    ```

6. Ouvrez *Index.cshtml* dans le répertoire Views > Home, puis remplacez son contenu par le code suivant :

    ```HTML
    @using Microsoft.Extensions.Configuration
    @inject IConfiguration Configuration

    <style>
        body {
            background-color: @Configuration["TestApp:Settings:BackgroundColor"]
        }
        h1 {
            color: @Configuration["TestApp:Settings:FontColor"];
            font-size: @Configuration["TestApp:Settings:FontSize"];
        }
    </style>

    <h1>@Configuration["TestApp:Settings:Message"]</h1>
    ```

7. Ouvrez *_Layout.cshtml* dans le répertoire Views > Shared, puis remplacez son contenu par le code suivant :

    ```HTML
    <!DOCTYPE html>
    <html>
    <head>
        <meta charset="utf-8" />
        <meta name="viewport" content="width=device-width, initial-scale=1.0" />
        <title>@ViewData["Title"] - hello_world</title>

        <link rel="stylesheet" href="~/lib/bootstrap/dist/css/bootstrap.css" />
        <link rel="stylesheet" href="~/css/site.css" />
    </head>
    <body>
        <div class="container body-content">
            @RenderBody()
        </div>

        <script src="~/lib/jquery/dist/jquery.js"></script>
        <script src="~/lib/bootstrap/dist/js/bootstrap.js"></script>
        <script src="~/js/site.js" asp-append-version="true"></script>

        @RenderSection("Scripts", required: false)
    </body>
    </html>
    ```

## <a name="build-and-run-the-app-locally"></a>Générer et exécuter l’application localement

1. Pour générer l’application à l’aide de l’interface CLI .NET Core, exécutez la commande suivante dans l’interpréteur de commandes :

    ```CLI
       dotnet build
    ```

2. Une fois la génération correctement terminée, exécutez la commande suivante pour exécuter l’application web localement :

    ```CLI
        dotnet run
    ```

3. Ouvrez une fenêtre de navigateur, puis accédez à `http://localhost:5000`, qui est l’URL par défaut de l’application web hébergée localement.

## <a name="clean-up-resources"></a>Supprimer des ressources

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Étapes suivantes

En suivant ce guide de démarrage rapide, vous avez créé un magasin App Configuration et l’avez utilisé avec une application web ASP.NET Core par l’intermédiaire du [fournisseur App Configuration](https://go.microsoft.com/fwlink/?linkid=2074664). Pour savoir comment configurer votre application ASP.NET Core afin d’actualiser dynamiquement les paramètres de configuration, passez au tutoriel suivant.

> [!div class="nextstepaction"]
> [Activer la configuration dynamique](./enable-dynamic-configuration-aspnet-core.md)
