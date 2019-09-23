---
title: Démarrage rapide pour l’ajout d’indicateurs de fonctionnalités à ASP.NET Core | Microsoft Docs
description: Guide de démarrage rapide pour l’ajout d’indicateurs de fonctionnalités à des applications ASP.NET Core et leur gestion dans Azure App Configuration
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: quickstart
ms.tgt_pltfrm: ASP.NET Core
ms.workload: tbd
ms.date: 04/19/2019
ms.author: yegu
ms.openlocfilehash: 6f9094a52ff3558fa8d1f2fee1d80ed8eb09a416
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71076325"
---
# <a name="quickstart-add-feature-flags-to-an-aspnet-core-app"></a>Démarrage rapide : Ajouter des indicateurs de fonctionnalités dans une application ASP.NET Core

Dans ce guide de démarrage rapide, vous incorporez Azure App Configuration à une application web ASP.NET Core pour créer une implémentation de bout en bout de la gestion des fonctionnalités. Vous pouvez utiliser le service App Configuration pour stocker de manière centralisée tous vos indicateurs de fonctionnalités et contrôler leur état. 

Les bibliothèques de gestion des fonctionnalités .NET Core étendent le framework avec une prise en charge complète des indicateurs de fonctionnalités. Ces bibliothèques sont basées sur le système de configuration .NET Core. Elles s’intègrent de manière fluide à App Configuration par le biais de son fournisseur de configuration .NET Core.

## <a name="prerequisites"></a>Prérequis

- Abonnement Azure : [créez-en un gratuitement](https://azure.microsoft.com/free/)
- [SDK .NET Core](https://dotnet.microsoft.com/download).

## <a name="create-an-app-configuration-store"></a>Créer un magasin App Configuration

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Sélectionnez **Gestionnaire de fonctionnalités** >  **+Ajouter** pour ajouter les indicateurs de fonctionnalités suivants :

    | Clé | State |
    |---|---|
    | Bêta | Off |

## <a name="create-an-aspnet-core-web-app"></a>Créez une application web ASP.NET Core

Vous utilisez l’[interface de ligne de commande (CLI) .NET Core](https://docs.microsoft.com/dotnet/core/tools/) pour créer un projet d’application web MVC ASP.NET Core. Par rapport à Visual Studio, l’interface CLI .NET Core offre l’avantage d’être disponible sur les plateformes Windows, macOS et Linux.

1. Créez un nouveau dossier pour votre projet. Pour les besoins de ce guide de démarrage rapide, nommez-le *TestFeatureFlags*.

1. Dans le nouveau dossier, exécutez la commande suivante pour créer un projet d’application web MVC ASP.NET Core :

   ```    
   dotnet new mvc
   ```

## <a name="add-secret-manager"></a>Ajouter Secret Manager

Ajoutez l’outil [Secret Manager](https://docs.microsoft.com/aspnet/core/security/app-secrets) à votre projet. L'outil Secret Manager stocke les données sensibles des travaux de développement à l'extérieur de l'arborescence de votre projet. Cette approche empêche le partage accidentel des secrets d’une application au sein du code source.

1. Ouvrez le fichier  *.csproj*.
1. Ajoutez un élément `UserSecretsId` comme illustré dans l’exemple suivant, et remplacez sa valeur par la vôtre, qui est généralement un GUID :

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

1. Enregistrez le fichier .

## <a name="connect-to-an-app-configuration-store"></a>Se connecter à un magasin App Configuration

1. Ajoutez une référence aux packages NuGet `Microsoft.Azure.AppConfiguration.AspNetCore` et `Microsoft.FeatureManagement.AspNetCore` en exécutant les commandes suivantes :

    ```
    dotnet add package Microsoft.Azure.AppConfiguration.AspNetCore --version 2.0.0-preview-009470001-12
    dotnet add package Microsoft.FeatureManagement.AspNetCore --version 1.0.0-preview-009000001-1251
    ```

1. Exécutez la commande suivante pour restaurer les packages de votre projet :

    ```
    dotnet restore
    ```

1. Ajoutez un secret nommé **ConnectionStrings:AppConfig** à Secret Manager.

    Ce secret contient la chaîne de connexion permettant d’accéder à votre magasin App Configuration. Dans la commande suivante, remplacez la valeur de `<your_connection_string>` par la chaîne de connexion de votre magasin App Configuration.

    Cette commande doit être exécutée dans le même répertoire que le fichier *.csproj*.

    ```
    dotnet user-secrets set ConnectionStrings:AppConfig <your_connection_string>
    ```

    Vous utilisez Secret Manager seulement pour tester l’application web localement. Par exemple, quand vous déployez l’application sur [Azure App Service](https://azure.microsoft.com/services/app-service), vous utilisez un paramètre d’application nommé **Chaînes de connexion** dans App Service au lieu de Secret Manager pour stocker la chaîne de connexion.

    Vous pouvez accéder à ce secret avec l’API App Configuration. Avec l’API App Configuration, un signe deux-points (:) fonctionne dans le nom de configuration sur toutes les plateformes prises en charge. Consultez [Configuration par environnement](https://docs.microsoft.com/aspnet/core/fundamentals/configuration).

1. Ouvrez *Program.cs*, puis ajoutez une référence au fournisseur App Configuration .NET Core :

    ```csharp
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

1. Mettez à jour la méthode `CreateWebHostBuilder` pour utiliser App Configuration en appelant la méthode `config.AddAzureAppConfiguration()`.

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();
                config.AddAzureAppConfiguration(options => {
                    options.Connect(settings["ConnectionStrings:AppConfig"])
                           .UseFeatureFlags();
                });
            })
            .UseStartup<Startup>();
    ```

1. Ouvrez *Startup.cs*, puis ajoutez des références au gestionnaire de fonctionnalités .NET Core :

    ```csharp
    using Microsoft.FeatureManagement;
    ```

1. Mettez à jour la méthode `ConfigureServices` pour ajouter la prise en charge de l’indicateur de fonctionnalité en appelant la méthode `services.AddFeatureManagement()`. Si vous le souhaitez, vous pouvez inclure un filtre à utiliser avec les indicateurs de fonctionnalité en appelant `services.AddFeatureFilter<FilterType>()` :

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddFeatureManagement();
    }
    ```

1. Mettez à jour la méthode `Configure` en y ajoutant un middleware (intergiciel) afin de permettre l’actualisation des valeurs des indicateurs de fonctionnalités à intervalles réguliers, pendant que l’application web ASP.NET continue de recevoir des requêtes.

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        app.UseAzureAppConfiguration();
        app.UseMvc();
    }
    ```

1. Ajoutez un fichier *MyFeatureFlags.cs* :

    ```csharp
    namespace TestFeatureFlags
    {
        public enum MyFeatureFlags
        {
            Beta
        }
    }
    ```

1. Ajoutez *BetaController.cs* au répertoire *Controllers* :

    ```csharp
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.FeatureManagement;
    using Microsoft.FeatureManagement.Mvc;

    namespace TestFeatureFlags.Controllers
    {
        public class BetaController: Controller
        {
            private readonly IFeatureManager _featureManager;

            public BetaController(IFeatureManagerSnapshot featureManager)
            {
                _featureManager = featureManager;
            }

            [FeatureGate(MyFeatureFlags.Beta)]
            public IActionResult Index()
            {
                return View();
            }
        }
    }
    ```

1. Ouvrez *_ViewImports.cshtml* dans le répertoire *Views*, puis ajoutez le tag helper du gestionnaire de fonctionnalités :

    ```html
    @addTagHelper *, Microsoft.FeatureManagement.AspNetCore
    ```

1. Ouvrez *_Layout.cshtml* dans le répertoire *Views*\\*Shared*, puis remplacez le code de la barre `<nav>` sous `<body>` > `<header>` par le code suivant :

    ```html
    <nav class="navbar navbar-expand-sm navbar-toggleable-sm navbar-light bg-white border-bottom box-shadow mb-3">
        <div class="container">
            <a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">TestFeatureFlags</a>
            <button class="navbar-toggler" type="button" data-toggle="collapse" data-target=".navbar-collapse" aria-controls="navbarSupportedContent"
            aria-expanded="false" aria-label="Toggle navigation">
            <span class="navbar-toggler-icon"></span>
            </button>
            <div class="navbar-collapse collapse d-sm-inline-flex flex-sm-row-reverse">
                <ul class="navbar-nav flex-grow-1">
                    <li class="nav-item">
                        <a class="nav-link text-dark" asp-area="" asp-controller="Home" asp-action="Index">Home</a>
                    </li>
                    <feature name="Beta">
                    <li class="nav-item">
                        <a class="nav-link text-dark" asp-area="" asp-controller="Beta" asp-action="Index">Beta</a>
                    </li>
                    </feature>
                    <li class="nav-item">
                        <a class="nav-link text-dark" asp-area="" asp-controller="Home" asp-action="Privacy">Privacy</a>
                    </li>
                </ul>
            </div>
        </div>
    </nav>
    ```

1. Créez un répertoire *Beta* sous *Views*, puis ajoutez-y *Index.cshtml* :

    ```html
    @{
        ViewData["Title"] = "Beta Home Page";
    }

    <h1>
        This is the beta website.
    </h1>
    ```

## <a name="build-and-run-the-app-locally"></a>Générer et exécuter l’application localement

1. Pour générer l’application à l’aide de l’interface CLI .NET Core, exécutez la commande suivante dans l’interpréteur de commandes :

    ```
    dotnet build
    ```

1. Une fois la génération correctement terminée, exécutez la commande suivante pour exécuter l’application web localement :

    ```
    dotnet run
    ```

1. Ouvrez une fenêtre de navigateur, puis accédez à `https://localhost:5001`, qui est l’URL par défaut de l’application web hébergée localement.

    ![Démarrage rapide du lancement d’application local](./media/quickstarts/aspnet-core-feature-flag-local-before.png)

1. Connectez-vous au [Portail Azure](https://portal.azure.com). Sélectionnez **Toutes les ressources**, puis sélectionnez l’instance du magasin App Configuration que vous avez créée dans le guide de démarrage rapide.

1. Sélectionnez **Gestionnaire de fonctionnalités**, puis changez la valeur de la clé **Bêta** en **On** :

    | Clé | State |
    |---|---|
    | Bêta | Il en va |

1. Redémarrez votre application en retournant à votre invite de commandes et en appuyant sur `Ctrl-C` pour annuler le processus `dotnet` en cours d’exécution, puis en réexécutant `dotnet run`.

1. Actualisez la page de navigateur pour afficher les nouveaux paramètres de configuration.

    ![Démarrage rapide du lancement d’application local](./media/quickstarts/aspnet-core-feature-flag-local-after.png)

## <a name="clean-up-resources"></a>Supprimer des ressources

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez créé un magasin App Configuration et vous l’avez utilisé pour gérer les fonctionnalités dans une application web ASP.NET Core via les [bibliothèques de gestion des fonctionnalités](https://go.microsoft.com/fwlink/?linkid=2074664).

- Découvrez plus d’informations sur la [gestion des fonctionnalités](./concept-feature-management.md).
- [Gérer les indicateurs de fonctionnalité](./manage-feature-flags.md).
- [Utiliser des indicateurs de fonctionnalités dans une application ASP.NET Core](./use-feature-flags-dotnet-core.md).
- [Utiliser la configuration dynamique dans une application ASP.NET Core](./enable-dynamic-configuration-aspnet-core.md)
