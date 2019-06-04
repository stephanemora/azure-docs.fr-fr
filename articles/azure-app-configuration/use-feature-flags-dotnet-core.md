---
title: Tutoriel sur l’utilisation des indicateurs de fonctionnalités dans une application .NET Core | Microsoft Docs
description: Dans ce tutoriel, vous allez découvrir comment implémenter des indicateurs de fonctionnalités dans des applications .NET Core.
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 04/19/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: b0e48a0db63eded9e9c4921d33b03af39656ce0d
ms.sourcegitcommit: 8c49df11910a8ed8259f377217a9ffcd892ae0ae
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/29/2019
ms.locfileid: "66299261"
---
# <a name="tutorial-use-feature-flags-in-a-net-core-app"></a>Didacticiel : Utiliser des indicateurs de fonctionnalité dans une application .NET Core

Les bibliothèques de gestion des fonctionnalités .NET Core fournissent une prise en charge idiomatique de l’implémentation des indicateurs de fonctionnalités dans une application .NET ou ASP.NET Core. Elles vous permettent d’ajouter des indicateurs de fonctionnalités à votre code de manière plus déclarative afin de vous éviter d’écrire manuellement toutes les instructions `if`. Elles gèrent les cycles de vie des indicateurs de fonctionnalités (par exemple les états des caches d’indicateurs et d’actualisations, garantie qu’un état d’indicateur soit immuable pendant un appel de requête) en arrière-plan. De plus, la bibliothèque ASP.NET Core offre des intégrations prêtes à l’emploi, notamment des intergiciels (middleware), des vues, des routes et des actions de contrôleur MVC.

Le guide de démarrage rapide [Ajouter des indicateurs de fonctionnalités à une application ASP.NET Core](./quickstart-feature-flag-aspnet-core.md) montre plusieurs manières d’ajouter des indicateurs de fonctionnalités à une application ASP.NET Core. Ce tutoriel explique ces différentes manières avec davantage de détails. Pour obtenir une référence complète, consultez la [documentation sur la gestion des fonctionnalités ASP.NET Core](https://go.microsoft.com/fwlink/?linkid=2091410).

Ce didacticiel vous apprendra à effectuer les opérations suivantes :

> [!div class="checklist"]
> * Ajouter des indicateurs de fonctionnalités dans des parties clés de votre application afin de contrôler la disponibilité des fonctionnalités
> * Intégrer à App Configuration lors de son utilisation pour gérer les indicateurs de fonctionnalités

## <a name="setup"></a>Paramétrage

Le gestionnaire de fonctionnalités de .NET Core `IFeatureManager` obtient les indicateurs de fonctionnalités à partir du système de configuration natif du framework. Ainsi, vous pouvez définir les indicateurs de fonctionnalités de votre application à l’aide de n’importe quelle source de configuration prise en charge par .NET Core, notamment le fichier *appsettings.json* local ou des variables d’environnement. Le gestionnaire de fonctionnalités s’appuie sur l’injection de dépendances .NET Core. Vous pouvez inscrire les services de gestion de fonctionnalités à l’aide de conventions standard.

```csharp
using Microsoft.FeatureManagement;

public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddFeatureManagement();
    }
}
```

Par défaut, le gestionnaire de fonctionnalités récupère les indicateurs de fonctionnalités à partir de la section « FeatureManagement » des données de configuration .NET Core. L’exemple suivant fait en sorte qu’il lise à partir d’une autre section nommée « MyFeatureFlags » à la place.

```csharp
using Microsoft.FeatureManagement;

public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddFeatureManagement(options =>
        {
                options.UseConfiguration(Configuration.GetSection("MyFeatureFlags"));
        });
    }
}
```

Si vous utilisez des filtres dans vos indicateurs de fonctionnalités, vous devez inclure une bibliothèque supplémentaire et l’inscrire. L’exemple suivant montre comment utiliser un filtre de fonctionnalité intégré nommé **PercentageFilter**.

```csharp
using Microsoft.FeatureManagement;
using Microsoft.FeatureManagement.FeatureFilters;

public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddFeatureManagement()
                .AddFeatureFilter<PercentageFilter>();
    }
}
```

Pour un fonctionnement optimal, vous devez conserver les indicateurs de fonctionnalités en dehors de l’application et les gérer séparément. Cela vous permet de modifier les états des indicateurs à tout moment, et d’appliquer immédiatement ces modifications dans l’application. App Configuration fournit un emplacement centralisé pour organiser et contrôler tous vos indicateurs de fonctionnalités via une interface utilisateur de portail dédiée, et remet les indicateurs à votre application directement par le biais de ses bibliothèques de client .NET Core. Le moyen le plus simple de connecter votre application ASP.NET Core à App Configuration est de passer par l’objet `Microsoft.Extensions.Configuration.AzureAppConfiguration` du fournisseur de configuration. Vous pouvez utiliser ce package NuGet dans votre code en ajoutant le code suivant au fichier *Program.cs* :

```csharp
using Microsoft.Extensions.Configuration.AzureAppConfiguration;

public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
           .ConfigureAppConfiguration((hostingContext, config) => {
               var settings = config.Build();
               config.AddAzureAppConfiguration(options => {
                   options.Connect(settings["ConnectionStrings:AppConfig"])
                          .UseFeatureFlags();
                });
           })
           .UseStartup<Startup>();
```

Les valeurs des indicateurs de fonctionnalités sont supposées changer au fil du temps. Par défaut, le gestionnaire de fonctionnalités actualise les valeurs des indicateurs de fonctionnalités toutes les 30 secondes. Vous pouvez utiliser un autre intervalle d’interrogation dans l’appel `options.UseFeatureFlags()` ci-dessus.

```csharp
config.AddAzureAppConfiguration(options => {
    options.Connect(settings["ConnectionStrings:AppConfig"])
           .UseFeatureFlags(featureFlagOptions => {
                featureFlagOptions.PollInterval = TimeSpan.FromSeconds(5);
           });
});
```

## <a name="feature-flag-declaration"></a>Déclaration d’indicateur de fonctionnalité

Chaque indicateur de fonctionnalité comporte deux parties : un nom et une liste d’un ou plusieurs filtres qui servent à évaluer si l’état d’une fonctionnalité est *on* (autrement dit, quand sa valeur est `True`). Un filtre définit un cas d’usage pour lequel une fonctionnalité doit être activée. Si un indicateur de fonctionnalité a plusieurs filtres, la liste des filtres est parcourue dans l’ordre jusqu’à ce que l’un d’eux détermine que la fonctionnalité doit être activée. À ce stade, l’indicateur de fonctionnalité est considéré comme *on* et les résultats du filtrage restants sont ignorés. Si aucun filtre n’indique que la fonctionnalité doit être activée, l’indicateur de fonctionnalité est *off*.

Le gestionnaire de fonctionnalités prend en charge *appsettings.json* comme source de configuration pour les indicateurs de fonctionnalités. L’exemple suivant montre comment définir des indicateurs de fonctionnalités dans un fichier json.

```JSON
"FeatureManagement": {
    "FeatureX": true, // Feature flag set to on
    "FeatureY": false, // Feature flag set to off
    "FeatureC": {
        "EnabledFor": [
            {
                "Name": "Percentage",
                "Parameters": {
                    "Value": 50
                }
            }
        ]
    }
}
```

Par convention, la section `FeatureManagement` de ce document json est utilisée pour les paramètres des indicateurs de fonctionnalités. L’exemple ci-dessus montre trois indicateurs de fonctionnalités avec leurs filtres définis dans la propriété *EnabledFor* :

* **FeatureA** est *on*.
* **FeatureB** est *off*.
* **FeatureC** spécifie un filtre nommé *Percentage* avec une propriété *Parameters*. *Percentage* est un exemple de filtre configurable ; il spécifie une probabilité de 50 % que l’indicateur **FeatureC** soit *on*.

## <a name="referencing"></a>Référencement

Bien que cela ne soit pas obligatoire, les indicateurs de fonctionnalités doivent être définis comme variables `enum` afin de pouvoir être référencés facilement dans le code.

```csharp
public enum MyFeatureFlags
{
    FeatureA,
    FeatureB,
    FeatureC
}
```

## <a name="feature-flag-check"></a>Vérification d’indicateur de fonctionnalité

Le modèle de gestion des fonctionnalités de base consiste à vérifier d’abord si un indicateur de fonctionnalité a la valeur *on*, puis à effectuer les actions incluses si tel est le cas.

```csharp
IFeatureManager featureManager;
...
if (featureManager.IsEnabled(nameof(MyFeatureFlags.FeatureA)))
{
    // Run the following code
}
```

## <a name="dependency-injection"></a>Injection de dépendances

Dans ASP.NET Core MVC, le gestionnaire de fonctionnalités `IFeatureManager` est accessible par le biais de l’injection de dépendances.

```csharp
public class HomeController : Controller
{
    private readonly IFeatureManager _featureManager;

    public HomeController(IFeatureManager featureManager)
    {
        _featureManager = featureManager;
    }
}
```

## <a name="controller-action"></a>Action du contrôleur

Dans les contrôleurs MVC, vous pouvez utiliser un attribut `Feature` pour contrôler si une classe de contrôleur entière ou une action spécifique est activée. Le contrôleur `HomeController` suivant exige que *FeatureA* soit *on* avant l’exécution de toute action qu’il contient.

```csharp
[Feature(MyFeatureFlags.FeatureA)]
public class HomeController : Controller
{
    ...
}
```

L’action `Index` suivante exige que *FeatureA* soit *on* pour pouvoir s’exécuter.

```csharp
[Feature(MyFeatureFlags.FeatureA)]
public IActionResult Index()
{
    return View();
}
```

Quand une action ou un contrôleur MVC est bloqué car l’indicateur de fonctionnalité de contrôle est *off*, un `IDisabledFeatureHandler` inscrit est appelé. Le `IDisabledFeatureHandler` par défaut retourne un code d’état 404 au client sans corps de réponse.

## <a name="view"></a>Affichage

Dans les vues MVC, vous pouvez utiliser une balise `<feature>` pour restituer le contenu selon qu’un indicateur de fonctionnalité est activé ou non.

```html
<feature name="FeatureA">
    <p>This can only be seen if 'FeatureA' is enabled.</p>
</feature>
```

## <a name="mvc-filter"></a>Filtre MVC

Vous pouvez configurer les filtres MVC de telle sorte que leur activation soit basée sur l’état d’un indicateur de fonctionnalité. Le code suivant ajoute un filtre MVC nommé `SomeMvcFilter`. Ce filtre est déclenché dans le pipeline MVC seulement si *FeatureA* est activée.

```csharp
using Microsoft.FeatureManagement.FeatureFilters;

IConfiguration Configuration { get; set;}

public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc(options => {
        options.Filters.AddForFeature<SomeMvcFilter>(nameof(MyFeatureFlags.FeatureA));
    });
}
```

## <a name="route"></a>Routage

Les routes peuvent être exposées de manière dynamique en fonction d’indicateurs de fonctionnalités. Le code suivant ajoute une route, qui définit `Beta` en tant que contrôleur par défaut, uniquement quand *FeatureA* est activée.

```csharp
app.UseMvc(routes => {
    routes.MapRouteForFeature(nameof(MyFeatureFlags.FeatureA), "betaDefault", "{controller=Beta}/{action=Index}/{id?}");
});
```

## <a name="middleware"></a>Middlewares

Vous pouvez utiliser des indicateurs de fonctionnalités pour ajouter des middlewares et des branches d’application de manière conditionnelle. Le code suivant insère un composant de middleware dans le pipeline de requête uniquement quand *FeatureA* est activée.

```csharp
app.UseMiddlewareForFeature<ThirdPartyMiddleware>(nameof(MyFeatureFlags.FeatureA));
```

Cette opération repose sur la capacité plus générique qui consiste à créer une branche d’application entière en fonction d’un indicateur de fonctionnalité.

```csharp
app.UseForFeature(featureName, appBuilder => {
    appBuilder.UseMiddleware<T>();
});
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez découvert comment implémenter des indicateurs de fonctionnalités dans votre application ASP.NET Core en utilisant les bibliothèques `Microsoft.FeatureManagement`. Pour plus d’informations sur la prise en charge de la gestion des fonctionnalités dans ASP.NET Core et App Configuration, consultez les ressources suivantes.

* [Exemple de code d’indicateur de fonctionnalité ASP.NET Core](/azure/azure-app-configuration/quickstart-feature-flag-aspnet-core)
* [Documentation de Microsoft.FeatureManagement](https://docs.microsoft.com/dotnet/api/microsoft.featuremanagement)
* [Gérer les indicateurs de fonctionnalité](./manage-feature-flags.md)
