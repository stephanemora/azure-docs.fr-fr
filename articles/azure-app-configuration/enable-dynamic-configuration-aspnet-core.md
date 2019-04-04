---
title: Tutoriel relatif à l’utilisation de la configuration dynamique d’Azure App Configuration dans une application ASP.NET Core | Microsoft Docs
description: Dans ce tutoriel, vous allez apprendre à mettre à jour dynamiquement les données de configuration pour les applications ASP.NET Core
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: na
ms.topic: tutorial
ms.date: 02/24/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: cf872766a18c5691f6c094d71a0c29f6bcf736da
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2019
ms.locfileid: "58579034"
---
# <a name="tutorial-use-dynamic-configuration-in-an-aspnet-core-app"></a>Didacticiel : Utiliser la configuration dynamique dans une application ASP.NET Core

ASP.NET Core offre un système de configuration enfichable qui peut lire des données de configuration à partir de diverses sources. Il peut gérer instantanément les changements sans entraîner le redémarrage d’une application. ASP.NET Core prend en charge la liaison des paramètres de configuration pour les classes .NET fortement typées. Il les injecte dans votre code à l’aide des différents modèles `IOptions<T>`. L’un de ces modèles, en particulier `IOptionsSnapshot<T>`, recharge automatiquement la configuration de l’application quand les données sous-jacentes changent. 

Vous pouvez injecter `IOptionsSnapshot<T>` dans des contrôleurs de votre application pour accéder à la configuration la plus récente stockée dans Azure App Configuration. Vous pouvez également configurer la bibliothèque cliente ASP.NET Core d’App Configuration pour superviser et récupérer en continu tout changement dans un magasin de configuration d’application. Vous définissez un intervalle d’interrogation régulier.

Ce tutoriel montre comment vous pouvez implémenter des mises à jour de la configuration dynamique dans votre code. Il s’appuie sur l’application web introduite dans les guides de démarrage rapide. Avant de continuer, terminez d’abord l’étape [Créer une application ASP.NET Core avec App Configuration](./quickstart-aspnet-core-app.md).

Vous pouvez utiliser l’éditeur de code de votre choix pour exécuter les étapes de ce guide de démarrage rapide. [Visual Studio Code](https://code.visualstudio.com/) est une excellente option qui est disponible sur les plateformes Windows, macOS et Linux.

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Configurer votre application pour mettre à jour sa configuration en réponse aux changements survenant dans un magasin de configuration d’application.
> * Injecter la configuration la plus récente dans les contrôleurs de votre application.

## <a name="prerequisites"></a>Prérequis

Pour suivre ce guide de démarrage rapide, installez le [SDK .NET Core](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="reload-data-from-app-configuration"></a>Recharger des données à partir d’Azure App Configuration

1. Ouvrez Program.cs, puis mettez à jour la méthode `CreateWebHostBuilder` en ajoutant la méthode `config.AddAzureAppConfiguration()`.

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();
                config.AddAzureAppConfiguration(options =>
                    options.Connect(settings["ConnectionStrings:AppConfig"])
                           .Watch("TestApp:Settings:BackgroundColor")
                           .Watch("TestApp:Settings:FontColor")
                           .Watch("TestApp:Settings:Message"));
            })
            .UseStartup<Startup>();
    ```

    Le deuxième paramètre de la méthode `.Watch` est la fréquence à laquelle la bibliothèque cliente ASP.NET interroge un magasin de configuration d’application. La bibliothèque cliente vérifie si le paramètre de configuration spécifique a fait l’objet d’un changement.

2. Ajoutez un fichier Settings.cs qui définit et implémente une nouvelle classe `Settings`.

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

3. Ouvrez Startup.cs, puis mettez à jour la méthode `ConfigureServices` pour lier des données de configuration à la classe `Settings`.

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Configure<Settings>(Configuration.GetSection("TestApp:Settings"));

        services.Configure<CookiePolicyOptions>(options =>
        {
            options.CheckConsentNeeded = context => true;
            options.MinimumSameSitePolicy = SameSiteMode.None;
        });

        services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
    }
    ```

## <a name="use-the-latest-configuration-data"></a>Utiliser les données de configuration les plus récentes

1. Ouvrez HomeController.cs dans le répertoire Controllers. Mettez à jour la classe `HomeController` pour recevoir `Settings` par le biais de l’injection de dépendance, puis utilisez ses valeurs.

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

2. Ouvrez Index.cshtml dans le répertoire Views > Home, puis remplacez son contenu par le script suivant :

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

4. Connectez-vous au [Portail Azure](https://aka.ms/azconfig/portal). Sélectionnez **Toutes les ressources**, puis sélectionnez l’instance de magasin de configuration d’application que vous avez créée dans le démarrage rapide.

5. Sélectionnez **Explorateur de paires clé/valeur**, puis mettez à jour les valeurs des clés suivantes :

    | Clé | Valeur |
    |---|---|
    | TestAppSettings:BackgroundColor | blue |
    | TestAppSettings:FontColor | lightGray |
    | TestAppSettings:Message | Données provenant d’Azure App Configuration, avec maintenant des mises à jour automatiques ! |

6. Actualisez la page de navigateur pour afficher les nouveaux paramètres de configuration.

    ![Démarrage rapide de l’actualisation locale de l’application](./media/quickstarts/aspnet-core-app-launch-local-after.png)

## <a name="clean-up-resources"></a>Supprimer des ressources

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez ajouté une identité de service managée Azure pour simplifier l’accès à App Configuration et améliorer la gestion des informations d’identification pour votre application. Pour en savoir plus sur l’utilisation d’App Configuration, passez aux exemples Azure CLI.

> [!div class="nextstepaction"]
> [Exemples d’interface de ligne de commande](./cli-samples.md)
