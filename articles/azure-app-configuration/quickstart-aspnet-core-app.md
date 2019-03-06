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
ms.date: 02/24/2019
ms.author: yegu
ms.openlocfilehash: ce19041b29d567f061dde59fbe041adf61f889a0
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2019
ms.locfileid: "56961480"
---
# <a name="quickstart-create-an-aspnet-core-app-with-azure-app-configuration"></a>Démarrage rapide : Créer une application ASP.NET Core avec Azure App Configuration

Azure App Configuration est un service de configuration managée dans Azure. Il vous permet de stocker et gérer facilement tous les paramètres de votre application à un seul endroit, indépendamment de votre code. Ce guide de démarrage rapide vous montre comment intégrer le service à une application web ASP.NET Core. ASP.NET Core génère un objet de configuration basé sur une paire clé-valeur unique à l’aide des paramètres d’une ou plusieurs sources de données, appelées *fournisseurs de configuration*, spécifiés par une application. Étant donné que le client .NET Core d’App Configuration est implémenté en tant que fournisseur, le service ressemble tout simplement à une autre source de données.

Vous pouvez utiliser l’éditeur de code de votre choix pour exécuter les étapes de ce démarrage rapide. Toutefois, [Visual Studio Code](https://code.visualstudio.com/) est une excellente option disponible sur les plateformes Windows, macOS et Linux.

## <a name="prerequisites"></a>Prérequis

Pour suivre ce guide de démarrage rapide, installez le [SDK .NET Core](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-an-app-configuration-store"></a>Créer un magasin de configuration d’application

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

## <a name="create-an-aspnet-core-web-app"></a>Créez une application web ASP.NET Core

Vous utilisez l’[interface de ligne de commande (CLI) .NET Core](https://docs.microsoft.com/dotnet/core/tools/) pour créer un projet Web App MVC ASP.NET Core. Par rapport à Visual Studio, l’interface CLI .NET Core offre l’avantage d’être disponible sur les plateformes Windows, macOS et Linux.

1. Créez un nouveau dossier pour votre projet. Pour les besoins de ce guide de démarrage rapide, nous allons le nommer *TestAppConfig*.

2. Dans le nouveau dossier, exécutez la commande suivante pour créer un projet d’application Web MVC ASP.NET Core :

        dotnet new mvc

## <a name="add-secret-manager"></a>Ajouter Secret Manager

Vous allez ajouter l’outil [Secret Manager](https://docs.microsoft.com/aspnet/core/security/app-secrets) à votre projet. L’outil Secret Manager stocke les données sensibles pour les travaux de développement à l’extérieur de l’arborescence de votre projet. Cette approche empêche le partage accidentel des secrets d’une application au sein du code source.

- Ouvrez votre fichier *.csproj*. Ajoutez un élément `UserSecretsId` comme indiqué ci-dessous et remplacez sa valeur par la vôtre, qui est généralement un GUID. Enregistrez le fichier .

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

## <a name="connect-to-app-configuration-store"></a>Se connecter au magasin de configuration d’application

1. Ajoutez une référence au package NuGet `Microsoft.Extensions.Configuration.AzureAppConfiguration` en exécutant la commande suivante :

        dotnet add package Microsoft.Extensions.Configuration.AzureAppConfiguration

2. Exécutez la commande suivante pour restaurer les packages de votre projet.

        dotnet restore

3. Ajoutez un secret nommé *ConnectionStrings:AppConfig* à Secret Manager.

    Ce secret contient la chaîne de connexion permettant d’accéder à votre magasin de configuration d’application. Dans la commande ci-dessous, remplacez la valeur par la chaîne de connexion de votre magasin de configuration d’application.

    Cette commande doit être exécutée dans le même répertoire que le fichier *.csproj*.

        dotnet user-secrets set ConnectionStrings:AppConfig "Endpoint=<your_endpoint>;Id=<your_id>;Secret=<your_secret>"

    Secret Manager est uniquement utilisé pour tester l’application web localement. Une fois que l’application est déployée (par exemple, sur [Azure App Service](https://azure.microsoft.com/services/app-service/web)), vous allez utiliser un paramètre d’application (par exemple, des **chaînes de connexion** dans App Service) au lieu de stocker la chaîne de connexion dans Secret Manager.

    L’API de configuration permet d’accéder à ce secret. Un signe deux-points (:) fonctionne dans le nom de configuration avec l’API de configuration sur toutes les plateformes prises en charge, consultez [Configuration par environnement](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/index?tabs=basicconfiguration&view=aspnetcore-2.0#configuration-by-environment).

4. Ouvrez *Program.cs* et mettez à jour la méthode `CreateWebHostBuilder` pour utiliser App Configuration en appelant la méthode `config.AddAzureAppConfiguration()`.

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

5. Ouvrez *Index.cshtml* dans le répertoire *Views* > *Home*, puis remplacez son contenu par le code suivant :

    ```html
    @using Microsoft.Extensions.Configuration
    @inject IConfiguration Configuration

    <!DOCTYPE html>
    <html lang="en">
    <style>
        body {
            background-color: @Configuration["TestApp:Settings:BackgroundColor"]
        }
        h1 {
            color: @Configuration["TestApp:Settings:FontColor"];
            font-size: @Configuration["TestApp:Settings:FontSize"];
        }
    </style>
    <head>
        <title>Index View</title>
    </head>
    <body>
        <h1>@Configuration["TestApp:Settings:Message"]</h1>
    </body>
    </html>
    ```

6. Ouvrez *_Layout.cshtml* dans le répertoire *Views* > *Shared*, puis remplacez son contenu par le code suivant :

    ```html
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

1. Pour générer l’application à l’aide de l’interface CLI .NET Core, exécutez la commande suivante dans l’interpréteur de commandes :

        dotnet build

2. Une fois la version terminée, exécutez la commande suivante afin d’exécuter l’application web en local :

        dotnet run

3. Lancez une fenêtre de navigateur, puis accédez à `http://localhost:5000`, qui est l’URL par défaut de l’application web hébergée localement.

    ![Démarrage rapide du lancement d’application local](./media/quickstarts/aspnet-core-app-launch-local.png)

## <a name="clean-up-resources"></a>Supprimer des ressources

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Étapes suivantes

En suivant ce guide de démarrage rapide, vous avez créé un magasin de configuration d’application et vous l’avez utilisé avec une application web ASP.NET Core. Pour en savoir plus sur l’utilisation d’App Configuration, passez au tutoriel suivant pour découvrir l’authentification.

> [!div class="nextstepaction"]
> [Identités managées pour l’intégration des ressources Azure](./integrate-azure-managed-service-identity.md)
