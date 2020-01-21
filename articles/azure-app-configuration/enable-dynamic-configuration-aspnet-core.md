---
title: 'Tutoriel : Utiliser la configuration dynamique App Configuration dans ASP.NET Core'
titleSuffix: Azure App Configuration
description: Dans ce tutoriel, vous allez apprendre à mettre à jour dynamiquement les données de configuration pour les applications ASP.NET Core
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 02/24/2019
ms.author: lcozzens
ms.custom: mvc
ms.openlocfilehash: 1c08b42d8217bf16dfcd8af17fa3c4627b95ffc3
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/15/2020
ms.locfileid: "76028225"
---
# <a name="tutorial-use-dynamic-configuration-in-an-aspnet-core-app"></a>Tutoriel : Utiliser la configuration dynamique dans une application ASP.NET Core

ASP.NET Core offre un système de configuration enfichable qui peut lire des données de configuration à partir de diverses sources. Il peut gérer instantanément les changements sans entraîner le redémarrage d’une application. ASP.NET Core prend en charge la liaison des paramètres de configuration pour les classes .NET fortement typées. Il les injecte dans votre code à l’aide des différents modèles `IOptions<T>`. L’un de ces modèles, en particulier `IOptionsSnapshot<T>`, recharge automatiquement la configuration de l’application quand les données sous-jacentes changent. Vous pouvez injecter `IOptionsSnapshot<T>` dans des contrôleurs de votre application pour accéder à la configuration la plus récente stockée dans Azure App Configuration.

Vous pouvez également configurer la bibliothèque de client ASP.NET Core App Configuration pour actualiser dynamiquement un jeu de paramètres de configuration à l’aide d’un intergiciel. Tant que l’application web continue à recevoir des demandes, les paramètres de configuration continuent à être mis à jour avec le magasin de configuration.

Pour maintenir les paramètres à jour et éviter trop d’appels au magasin de configuration, un cache est utilisé pour chaque paramètre. Tant que la valeur mise en cache d’un paramètre n’a pas expiré, l’opération d’actualisation ne met pas à jour la valeur, même si celle-ci a été modifiée dans le magasin de configuration. Pour chaque requête, le délai d’expiration par défaut est de 30 secondes. Toutefois, vous pouvez le modifier selon vos besoins.

Ce tutoriel montre comment vous pouvez implémenter des mises à jour de la configuration dynamique dans votre code. Il s’appuie sur l’application web introduite dans les guides de démarrage rapide. Avant de continuer, terminez d’abord l’étape [Créer une application ASP.NET Core avec App Configuration](./quickstart-aspnet-core-app.md).

Vous pouvez utiliser l’éditeur de code de votre choix pour exécuter les étapes de ce tutoriel. [Visual Studio Code](https://code.visualstudio.com/) est une excellente option qui est disponible sur les plateformes Windows, macOS et Linux.

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Configurer votre application pour mettre à jour sa configuration en réponse aux changements survenant dans un magasin App Configuration.
> * Injecter la configuration la plus récente dans les contrôleurs de votre application.

## <a name="prerequisites"></a>Conditions préalables requises

Pour effectuer ce tutoriel, installez le [kit SDK .NET Core](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Avant de continuer, terminez d’abord l’étape [Créer une application ASP.NET Core avec App Configuration](./quickstart-aspnet-core-app.md).

## <a name="reload-data-from-app-configuration"></a>Recharger des données à partir d’Azure App Configuration

1. Ajoutez une référence au package NuGet `Microsoft.Azure.AppConfiguration.AspNetCore` en exécutant la commande suivante :

    ```CLI
    dotnet add package Microsoft.Azure.AppConfiguration.AspNetCore --version 3.0.0-preview-010560002-1165
    ```

1. Ouvrez *Program.cs*, puis mettez à jour la méthode `CreateWebHostBuilder` pour ajouter la méthode `config.AddAzureAppConfiguration()`.

    #### <a name="net-core-2xtabcore2x"></a>[.NET Core 2.x](#tab/core2x)

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();

                config.AddAzureAppConfiguration(options =>
                {
                    options.Connect(settings["ConnectionStrings:AppConfig"])
                           .ConfigureRefresh(refresh =>
                           {
                               refresh.Register("TestApp:Settings:BackgroundColor")
                                      .Register("TestApp:Settings:FontColor")
                                      .Register("TestApp:Settings:Message");
                           });
                });
            })
            .UseStartup<Startup>();
    ```

    #### <a name="net-core-3xtabcore3x"></a>[.NET Core 3.x](#tab/core3x)

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
                webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
                {
                    var settings = config.Build();
                    config.AddAzureAppConfiguration(options =>
                    {   
                        options.Connect(settings["ConnectionStrings:AppConfig"])
                            .ConfigureRefresh(refresh =>
                                {
                                    refresh.Register("TestApp:Settings:BackgroundColor")
                                            .Register("TestApp:Settings:FontColor")
                                            .Register("TestApp:Settings:Message");
                                });
                    });
                })
            .UseStartup<Startup>());
    ```
    ---

    La méthode `ConfigureRefresh` permet de spécifier les paramètres utilisés pour mettre à jour les données de configuration à l’aide du magasin App Configuration quand une opération d’actualisation est déclenchée. Pour déclencher une opération d’actualisation, un intergiciel d’actualisation doit être configuré pour que l’application actualise les données de configuration en cas de modification.

2. Ajoutez un fichier *Settings.cs* qui définit et implémente une nouvelle classe `Settings`.

    ```csharp
    namespace TestAppConfig
    {
        public class Settings
        {
            public string BackgroundColor { get; set; }
            public long FontSize { get; set; }
            public string FontColor { get; set; }
            public string Message { get; set; }
        }
    }
    ```

3. Ouvrez *Startup.cs*, puis utilisez `IServiceCollection.Configure<T>` dans la méthode `ConfigureServices` pour lier les données de configuration à la classe `Settings`.

    #### <a name="net-core-2xtabcore2x"></a>[.NET Core 2.x](#tab/core2x)

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Configure<Settings>(Configuration.GetSection("TestApp:Settings"));
        services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
    }
    ```

    #### <a name="net-core-3xtabcore3x"></a>[.NET Core 3.x](#tab/core3x)

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Configure<Settings>(Configuration.GetSection("TestApp:Settings"));
        services.AddControllersWithViews();
    }
    ```
    ---

4. Mettez à jour la méthode `Configure`, en ajoutant l’intergiciel (middleware) `UseAzureAppConfiguration` afin de permettre la mise à jour des paramètres de configuration inscrits pour être actualisés pendant que l’application web ASP.NET Core continue de recevoir des requêtes.


    #### <a name="net-core-2xtabcore2x"></a>[.NET Core 2.x](#tab/core2x)

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        app.UseAzureAppConfiguration();

        services.Configure<CookiePolicyOptions>(options =>
        {
            options.CheckConsentNeeded = context => true;
            options.MinimumSameSitePolicy = SameSiteMode.None;
        });

        app.UseMvc();
    }
    ```

    #### <a name="net-core-3xtabcore3x"></a>[.NET Core 3.x](#tab/core3x)

    ```csharp
    public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
    {
            if (env.IsDevelopment())
            {
                app.UseDeveloperExceptionPage();
            }
            else
            {
                app.UseExceptionHandler("/Home/Error");
                // The default HSTS value is 30 days. You may want to change this for production scenarios, see https://aka.ms/aspnetcore-hsts.
                app.UseHsts();
            }

            // Add the following line:
            app.UseAzureAppConfiguration();

            app.UseHttpsRedirection();
            
            app.UseStaticFiles();

            app.UseRouting();

            app.UseAuthorization();

            app.UseEndpoints(endpoints =>
            {
                endpoints.MapControllerRoute(
                    name: "default",
                    pattern: "{controller=Home}/{action=Index}/{id?}");
            });
    }
    ```
    ---
    
    L’intergiciel utilise la configuration d’actualisation spécifiée dans la méthode `AddAzureAppConfiguration` de `Program.cs` afin de déclencher une actualisation pour chaque demande reçue par l’application web ASP.NET Core. Pour chaque demande, une opération d’actualisation est déclenchée et la bibliothèque de client vérifie si la valeur mise en cache pour les paramètres de configuration inscrits a expiré. Pour les valeurs mises en cache ayant expiré, les valeurs des paramètres sont mises à jour avec celles du magasin App Configuration. Quant aux valeurs restantes, elles ne changent pas.
    
    > [!NOTE]
    > Pour un paramètre de configuration, le délai d’expiration du cache par défaut est de 30 secondes. Toutefois, vous pouvez le modifier en appelant la méthode `SetCacheExpiration` de l’initialiseur d’options qui est passé en tant qu’argument à la méthode `ConfigureRefresh`.

## <a name="use-the-latest-configuration-data"></a>Utiliser les données de configuration les plus récentes

1. Ouvrez *HomeController.cs* dans le répertoire Controllers et ajoutez une référence au package `Microsoft.Extensions.Options`.

    ```csharp
    using Microsoft.Extensions.Options;
    ```

2. Mettez à jour la classe `HomeController` pour recevoir `Settings` par le biais de l’injection de dépendance, puis utilisez ses valeurs.

    #### <a name="net-core-2xtabcore2x"></a>[.NET Core 2.x](#tab/core2x)

    ```csharp
    public class HomeController : Controller
    {
        private readonly Settings _settings;
        public HomeController(IOptionsSnapshot<Settings> settings)
        {
            _settings = settings.Value;
        }

        public IActionResult Index()
        {
            ViewData["BackgroundColor"] = _settings.BackgroundColor;
            ViewData["FontSize"] = _settings.FontSize;
            ViewData["FontColor"] = _settings.FontColor;
            ViewData["Message"] = _settings.Message;

            return View();
        }
    }
    ```

    #### <a name="net-core-3xtabcore3x"></a>[.NET Core 3.x](#tab/core3x)

    ```csharp
    public class HomeController : Controller
    {
        private readonly Settings _settings;
        private readonly ILogger<HomeController> _logger;

        public HomeController(ILogger<HomeController> logger, IOptionsSnapshot<Settings> settings)
        {
            _logger = logger;
            _settings = settings.Value;
        }

        public IActionResult Index()
        {
            ViewData["BackgroundColor"] = _settings.BackgroundColor;
            ViewData["FontSize"] = _settings.FontSize;
            ViewData["FontColor"] = _settings.FontColor;
            ViewData["Message"] = _settings.Message;

            return View();
        }

        // ...
    }
    ```
    ---



3. Ouvrez *Index.cshtml* dans le répertoire Views > Home, puis remplacez son contenu par le script suivant :

    ```html
    <!DOCTYPE html>
    <html lang="en">
    <style>
        body {
            background-color: @ViewData["BackgroundColor"]
        }
        h1 {
            color: @ViewData["FontColor"];
            font-size: @ViewData["FontSize"];
        }
    </style>
    <head>
        <title>Index View</title>
    </head>
    <body>
        <h1>@ViewData["Message"]</h1>
    </body>
    </html>
    ```

## <a name="build-and-run-the-app-locally"></a>Générer et exécuter l’application localement

1. Pour générer l’application à l’aide de l’interface CLI .NET Core, exécutez la commande suivante dans l’interpréteur de commandes :

        dotnet build

2. Une fois la génération correctement terminée, exécutez la commande suivante pour exécuter l’application web localement :

        dotnet run

3. Ouvrez une fenêtre de navigateur, puis accédez à `http://localhost:5000`, qui est l’URL par défaut de l’application web hébergée localement.

    ![Démarrage rapide du lancement d’application local](./media/quickstarts/aspnet-core-app-launch-local-before.png)

4. Connectez-vous au [portail Azure](https://portal.azure.com). Sélectionnez **Toutes les ressources**, puis sélectionnez l’instance du magasin App Configuration que vous avez créée dans le guide de démarrage rapide.

5. Sélectionnez **Explorateur de configuration**, puis mettez à jour les valeurs des clés suivantes :

    | Clé | Valeur |
    |---|---|
    | TestApp:Settings:BackgroundColor | green |
    | TestApp:Settings:FontColor | lightGray |
    | TestApp:Settings:Message | Données provenant d’Azure App Configuration, avec maintenant des mises à jour automatiques ! |

6. Actualisez la page de navigateur pour afficher les nouveaux paramètres de configuration. Vous devrez peut-être actualiser la page du navigateur à plusieurs reprises pour que les modifications apparaissent.

    ![Démarrage rapide de l’actualisation locale de l’application](./media/quickstarts/aspnet-core-app-launch-local-after.png)
    
    > [!NOTE]
    > Étant donné que les paramètres de configuration sont mis en cache avec un délai d’expiration par défaut de 30 secondes, toute modification apportée aux paramètres dans le magasin App Configuration n’apparaît dans l’application web qu’après l’expiration du cache.

## <a name="clean-up-resources"></a>Nettoyer les ressources

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez permis à votre application web ASP.NET Core d’actualiser dynamiquement les paramètres de configuration à partir d’App Configuration. Pour savoir comment utiliser une identité managée Azure afin de simplifier l’accès à App Configuration, passez au tutoriel suivant.

> [!div class="nextstepaction"]
> [Intégration des identités managées](./howto-integrate-azure-managed-service-identity.md)
