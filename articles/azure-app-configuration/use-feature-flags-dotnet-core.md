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
ms.openlocfilehash: fc5215f71af45d3273da437fc796bf0d396ba3f9
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66393512"
---
# <a name="tutorial-use-feature-flags-in-a-net-core-app"></a>Didacticiel : Utiliser des indicateurs de fonctionnalité dans une application .NET Core

Les bibliothèques de gestion des fonctionnalités .NET Core fournissent une prise en charge idiomatique de l’implémentation des indicateurs de fonctionnalités dans une application .NET ou ASP.NET Core. Ces bibliothèques vous permettent d’ajouter de manière déclarative des indicateurs de fonctionnalités à votre code afin de vous éviter d’écrire manuellement toutes les instructions `if` correspondantes.

Les bibliothèques de gestion des fonctionnalités gèrent également les cycles de vie des indicateurs de fonctionnalités en arrière-plan. Par exemple, elles actualisent et mettent en cache l’état des indicateurs ou garantissent qu’un état d’indicateur est immuable pendant un appel de demande. De plus, la bibliothèque ASP.NET Core offre des intégrations prêtes à l’emploi, notamment des intergiciels (middleware), des routes, des vues et des actions de contrôleur MVC.

Le [guide de démarrage rapide Ajouter des indicateurs de fonctionnalités dans une application ASP.NET Core](./quickstart-feature-flag-aspnet-core.md) montre plusieurs façons d’ajouter des indicateurs de fonctionnalités à une application ASP.NET Core. Ce tutoriel explique plus en détails ces différentes méthodes. Pour obtenir une référence complète, consultez la [documentation sur la gestion des fonctionnalités ASP.NET Core](https://go.microsoft.com/fwlink/?linkid=2091410).

Ce didacticiel vous apprendra à effectuer les opérations suivantes :

> [!div class="checklist"]
> * Ajouter des indicateurs de fonctionnalités dans des parties clés de votre application afin de contrôler la disponibilité des fonctionnalités
> * Effectuer une intégration à App Configuration quand vous l’utilisez pour gérer les indicateurs de fonctionnalités.

## <a name="set-up-feature-management"></a>Configurer la gestion des fonctionnalités

Le gestionnaire de fonctionnalités de .NET Core `IFeatureManager` obtient les indicateurs de fonctionnalités à partir du système de configuration natif du framework. En conséquence, vous pouvez définir les indicateurs de fonctionnalités de votre application à l’aide de n’importe quelle source de configuration prise en charge par .NET Core, notamment le fichier *appsettings.json* local ou des variables d’environnement. `IFeatureManager` s’appuie sur l’injection de dépendances .NET Core. Vous pouvez inscrire les services de gestion des fonctionnalités à l’aide de conventions standard :

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

Par défaut, le gestionnaire de fonctionnalités récupère les indicateurs de fonctionnalités à partir de la section `"FeatureManagement"` des données de configuration .NET Core. L’exemple suivant fait en sorte qu’il lise à partir d’une autre section nommée `"MyFeatureFlags"` à la place :

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

Si vous utilisez des filtres dans vos indicateurs de fonctionnalités, vous devez inclure une bibliothèque supplémentaire et l’inscrire. L’exemple suivant montre comment utiliser un filtre de fonctionnalité intégré nommé `PercentageFilter` :

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

Nous vous recommandons de conserver les indicateurs de fonctionnalités en dehors de l’application et de les gérer séparément. Cela vous permet de modifier l’état des indicateurs à tout moment, et d’appliquer immédiatement ces changements dans l’application. Le service App Configuration fournit un emplacement centralisé pour organiser et contrôler tous vos indicateurs de fonctionnalités par le biais d’une interface utilisateur de portail dédiée. Il remet également les indicateurs à votre application directement par le biais de ses bibliothèques clientes .NET Core.

Le moyen le plus simple de connecter votre application ASP.NET Core à App Configuration est de passer par l’objet `Microsoft.Extensions.Configuration.AzureAppConfiguration` du fournisseur de configuration. Pour utiliser ce package NuGet, ajoutez le code suivant au fichier *Program.cs* :

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

Les valeurs des indicateurs de fonctionnalités sont supposées changer au fil du temps. Par défaut, le gestionnaire de fonctionnalités actualise les valeurs des indicateurs de fonctionnalités toutes les 30 secondes. Le code suivant montre comment définir l’intervalle d’interrogation sur 5 secondes dans l’appel `options.UseFeatureFlags()` :

```csharp
config.AddAzureAppConfiguration(options => {
    options.Connect(settings["ConnectionStrings:AppConfig"])
           .UseFeatureFlags(featureFlagOptions => {
                featureFlagOptions.PollInterval = TimeSpan.FromSeconds(5);
           });
});
```

## <a name="feature-flag-declaration"></a>Déclaration d’indicateur de fonctionnalité

Chaque indicateur de fonctionnalité comporte deux parties : un nom et une liste d’un ou plusieurs filtres qui servent à évaluer si l’état d’une fonctionnalité est *on* (autrement dit, quand sa valeur est `True`). Un filtre définit un cas d’usage pour le moment où une fonctionnalité doit être activée.

Si un indicateur de fonctionnalité a plusieurs filtres, la liste des filtres est parcourue dans l’ordre jusqu’à ce que l’un d’eux détermine que la fonctionnalité doit être activée. À ce stade, l’indicateur de fonctionnalité est *on* (activée) et les résultats du filtrage restants sont ignorés. Si aucun filtre n’indique que la fonctionnalité doit être activée, l’indicateur de fonctionnalité est *off* (désactivée).

Le gestionnaire de fonctionnalités prend en charge *appsettings.json* comme source de configuration pour les indicateurs de fonctionnalités. L’exemple suivant montre comment configurer des indicateurs de fonctionnalités dans un fichier JSON :

```JSON
"FeatureManagement": {
    "FeatureA": true, // Feature flag set to on
    "FeatureB": false, // Feature flag set to off
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

Par convention, la section `FeatureManagement` de ce document JSON est utilisée pour les paramètres des indicateurs de fonctionnalités. L’exemple précédent montre trois indicateurs de fonctionnalités avec leurs filtres définis dans la propriété `EnabledFor` :

* `FeatureA` est défini sur *on*.
* `FeatureB` est défini sur *off*.
* `FeatureC` spécifie un filtre nommé `Percentage` avec une propriété `Parameters`. `Percentage` est un filtre configurable. Dans cet exemple, `Percentage` spécifie une probabilité de 50 % que l’indicateur `FeatureC` soit défini sur *on*.

## <a name="feature-flag-references"></a>Références des indicateurs de fonctionnalités

Pour pouvoir aisément référencer des indicateurs de fonctionnalités dans du code, vous devez les définir en tant que variables `enum` :

```csharp
public enum MyFeatureFlags
{
    FeatureA,
    FeatureB,
    FeatureC
}
```

## <a name="feature-flag-checks"></a>Vérifications des indicateurs de fonctionnalités

Le modèle de gestion des fonctionnalités de base consiste à d’abord vérifier si un indicateur de fonctionnalité est défini sur *on*. Si c’est le cas, le gestionnaire de fonctionnalités exécute les actions que la fonctionnalité contient. Par exemple :

```csharp
IFeatureManager featureManager;
...
if (featureManager.IsEnabled(nameof(MyFeatureFlags.FeatureA)))
{
    // Run the following code
}
```

## <a name="dependency-injection"></a>Injection de dépendances

Dans ASP.NET Core MVC, vous pouvez accéder au gestionnaire de fonctionnalités `IFeatureManager` par le biais de l’injection de dépendances :

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

## <a name="controller-actions"></a>Actions de contrôleur

Dans les contrôleurs MVC, vous pouvez utiliser un attribut `Feature` pour déterminer si l’ensemble d’une classe de contrôleur ou une action spécifique est activée. Le contrôleur `HomeController` suivant exige que `FeatureA` soit défini sur *on* avant l’exécution de toute action que contient la classe de contrôleur :

```csharp
[Feature(MyFeatureFlags.FeatureA)]
public class HomeController : Controller
{
    ...
}
```

L’action `Index` suivante exige que `FeatureA` soit défini sur *on* pour pouvoir être exécutée :

```csharp
[Feature(MyFeatureFlags.FeatureA)]
public IActionResult Index()
{
    return View();
}
```

Quand une action ou un contrôleur MVC est bloqué car l’indicateur de fonctionnalité de contrôle est défini sur *off*, une interface `IDisabledFeaturesHandler` inscrite est appelée. L’interface `IDisabledFeaturesHandler` par défaut retourne un code d’état 404 au client sans corps de réponse.

## <a name="mvc-views"></a>Vues MVC

Dans les vues MVC, vous pouvez utiliser une balise `<feature>` pour restituer le contenu si un indicateur de fonctionnalité est activé :

```html
<feature name="FeatureA">
    <p>This can only be seen if 'FeatureA' is enabled.</p>
</feature>
```

## <a name="mvc-filters"></a>Filtres MVC

Vous pouvez configurer des filtres MVC de sorte que leur activation soit basée sur l’état d’un indicateur de fonctionnalité. Le code suivant ajoute un filtre MVC nommé `SomeMvcFilter`. Ce filtre est déclenché dans le pipeline MVC seulement si `FeatureA` est activé.

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

## <a name="routes"></a>Itinéraires

Vous pouvez utiliser des indicateurs de fonctionnalités pour exposer dynamiquement des routes. Le code suivant ajoute une route qui définit `Beta` comme contrôleur par défaut uniquement quand `FeatureA` est activé :

```csharp
app.UseMvc(routes => {
    routes.MapRouteForFeature(nameof(MyFeatureFlags.FeatureA), "betaDefault", "{controller=Beta}/{action=Index}/{id?}");
});
```

## <a name="middleware"></a>Middlewares

Vous pouvez également utiliser des indicateurs de fonctionnalités pour ajouter de manière conditionnelle des intergiciels (middlewares) et des branches d’application. Le code suivant insère un composant d’intergiciel dans le pipeline de requête uniquement quand `FeatureA` est activé :

```csharp
app.UseMiddlewareForFeature<ThirdPartyMiddleware>(nameof(MyFeatureFlags.FeatureA));
```

Ce code repose sur la capacité plus générique qui consiste à créer une branche de l’ensemble de l’application entière en fonction d’un indicateur de fonctionnalité :

```csharp
app.UseForFeature(featureName, appBuilder => {
    appBuilder.UseMiddleware<T>();
});
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez découvert comment implémenter des indicateurs de fonctionnalités dans votre application ASP.NET Core en utilisant les bibliothèques `Microsoft.FeatureManagement`. Pour plus d’informations sur la prise en charge de la gestion des fonctionnalités dans ASP.NET Core et App Configuration, consultez les ressources suivantes :

* [Exemple de code d’indicateur de fonctionnalité ASP.NET Core](/azure/azure-app-configuration/quickstart-feature-flag-aspnet-core)
* [Documentation de Microsoft.FeatureManagement](https://docs.microsoft.com/dotnet/api/microsoft.featuremanagement)
* [Gérer les indicateurs de fonctionnalité](./manage-feature-flags.md)
