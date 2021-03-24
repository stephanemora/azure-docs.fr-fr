---
title: Tutoriel sur l’utilisation des indicateurs de fonctionnalités dans une application .NET Core | Microsoft Docs
description: Dans ce tutoriel, vous allez découvrir comment implémenter des indicateurs de fonctionnalités dans des applications .NET Core.
services: azure-app-configuration
documentationcenter: ''
author: AlexandraKemperMS
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 09/17/2020
ms.author: alkemper
ms.custom: devx-track-csharp, mvc
ms.openlocfilehash: 327bc687c466a30d4f92810e48dc08f822f752ec
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101726425"
---
# <a name="tutorial-use-feature-flags-in-an-aspnet-core-app"></a>Tutoriel : Utiliser des indicateurs de fonctionnalités dans une application ASP.NET Core

Les bibliothèques de gestion des fonctionnalités .NET Core fournissent une prise en charge idiomatique de l’implémentation des indicateurs de fonctionnalités dans une application .NET ou ASP.NET Core. Ces bibliothèques vous permettent d’ajouter de façon déclarative des indicateurs de fonctionnalités à votre code afin de vous éviter d’écrire manuellement le code nécessaire pour activer ou désactiver des fonctionnalités avec des instructions `if`.

Les bibliothèques de gestion des fonctionnalités gèrent également les cycles de vie des indicateurs de fonctionnalités en arrière-plan. Par exemple, elles actualisent et mettent en cache l’état des indicateurs ou garantissent qu’un état d’indicateur est immuable pendant un appel de demande. De plus, la bibliothèque ASP.NET Core offre des intégrations prêtes à l’emploi, notamment des intergiciels (middleware), des routes, des vues et des actions de contrôleur MVC.

Le guide de démarrage rapide [Ajouter des indicateurs de fonctionnalités à une application ASP.NET Core](./quickstart-feature-flag-aspnet-core.md) montre un exemple simple d’utilisation d’indicateurs de fonctionnalités dans une application ASP.NET Core. Ce tutoriel présente d’autres fonctionnalités et options de configuration des bibliothèques de gestion des fonctionnalités. Vous pouvez utiliser l’exemple d’application créé dans le guide de démarrage rapide pour essayer l’exemple de code présenté dans ce tutoriel. 

Pour obtenir la documentation de référence de l’API de gestion des fonctionnalités ASP.NET Core, consultez [Espace de noms Microsoft.FeatureManagement](/dotnet/api/microsoft.featuremanagement).

Dans ce didacticiel, vous apprendrez à :

> [!div class="checklist"]
> * Ajouter des indicateurs de fonctionnalités dans des parties clés de votre application afin de contrôler la disponibilité des fonctionnalités
> * Effectuer une intégration à App Configuration quand vous l’utilisez pour gérer les indicateurs de fonctionnalités.

## <a name="set-up-feature-management"></a>Configurer la gestion des fonctionnalités

Pour accéder au gestionnaire de fonctionnalités .NET Core, votre application doit avoir des références au package NuGet `Microsoft.FeatureManagement.AspNetCore`.

Le gestionnaire de fonctionnalités .NET Core est configuré à partir du système de configuration natif du framework. Vous pouvez donc définir les paramètres des indicateurs de fonctionnalités de votre application à l’aide de n’importe quelle source de configuration prise en charge par .NET Core, notamment le fichier *appsettings.json* local ou des variables d’environnement.

Par défaut, le gestionnaire de fonctionnalités récupère la configuration des indicateurs de fonctionnalités à partir de la section `"FeatureManagement"` des données de configuration .NET Core. Pour utiliser l’emplacement de configuration par défaut, appelez la méthode [AddFeatureManagement](/dotnet/api/microsoft.featuremanagement.servicecollectionextensions.addfeaturemanagement) de l’interface **IServiceCollection** passée dans la méthode **ConfigureServices** de la classe **Startup**.


```csharp
using Microsoft.FeatureManagement;

public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        ...
        services.AddFeatureManagement();
    }
}
```

Vous pouvez spécifier que la configuration de la gestion des fonctionnalités doit être récupérée à partir d’une section de configuration différente en appelant [Configuration.GetSection](/dotnet/api/microsoft.web.administration.configuration.getsection) et en passant le nom de la section souhaitée. L’exemple suivant fait en sorte qu’il lise à partir d’une autre section nommée `"MyFeatureFlags"` à la place :

```csharp
using Microsoft.FeatureManagement;

public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        ...
        services.AddFeatureManagement(Configuration.GetSection("MyFeatureFlags"));
    }
}
```


Si vous utilisez des filtres dans vos indicateurs de fonctionnalités, vous devez inclure l’espace de noms [Microsoft.FeatureManagement.FeatureFilters](/dotnet/api/microsoft.featuremanagement.featurefilters) et ajouter un appel à [AddFeatureFilters](/dotnet/api/microsoft.featuremanagement.ifeaturemanagementbuilder.addfeaturefilter) spécifiant le nom de type du filtre que vous souhaitez utiliser comme type générique de la méthode. Pour plus d’informations sur l’utilisation de filtres de fonctionnalités pour activer et désactiver des fonctionnalités de manière dynamique, consultez [Activer le déploiement échelonné des fonctionnalités pour des audiences ciblées](./howto-targetingfilter-aspnet-core.md).

L’exemple suivant montre comment utiliser un filtre de fonctionnalité intégré nommé `PercentageFilter` :



```csharp
using Microsoft.FeatureManagement;
using Microsoft.FeatureManagement.FeatureFilters;

public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        ...
        services.AddFeatureManagement()
                .AddFeatureFilter<PercentageFilter>();
    }
}
```

Plutôt que de coder en dur vos indicateurs de fonctionnalités dans votre application, nous vous recommandons de conserver les indicateurs de fonctionnalités en dehors de l’application et de les gérer séparément. Cela vous permet de modifier l’état des indicateurs à tout moment, et d’appliquer immédiatement ces changements dans l’application. Le service Azure App Configuration fournit une interface utilisateur de portail dédiée pour la gestion de tous les indicateurs de fonctionnalités. Le service Azure App Configuration remet également les indicateurs de fonctionnalités à votre application directement par le biais de ses bibliothèques de client .NET Core.

Le moyen le plus simple de connecter votre application ASP.NET Core à App Configuration est de passer par le fournisseur de configuration inclus dans le package NuGet `Microsoft.Azure.AppConfiguration.AspNetCore`. Après avoir inclus une référence au package, effectuez les étapes suivantes pour utiliser ce package NuGet.

1. Ouvrez le fichier *Program.cs* et ajoutez le code suivant.
    > [!IMPORTANT]
    > `CreateHostBuilder` remplace `CreateWebHostBuilder` dans .NET Core 3.x. Sélectionnez la syntaxe appropriée en fonction de votre environnement.

    ### <a name="net-5x"></a>[.NET 5.x](#tab/core5x)
    
    ```csharp
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
                webBuilder.ConfigureAppConfiguration(config =>
                {
                    var settings = config.Build();
                    config.AddAzureAppConfiguration(options =>
                        options.Connect(settings["ConnectionStrings:AppConfig"]).UseFeatureFlags());
                }).UseStartup<Startup>());
    ```

    ### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)
    
    ```csharp
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;

    public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
            webBuilder.ConfigureAppConfiguration(config =>
            {
                var settings = config.Build();
                config.AddAzureAppConfiguration(options =>
                    options.Connect(settings["ConnectionStrings:AppConfig"]).UseFeatureFlags());
            }).UseStartup<Startup>());
    ```
        
    ### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)
    
    ```csharp
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
            WebHost.CreateDefaultBuilder(args)
                    .ConfigureAppConfiguration(config =>
                    {
                        var settings = config.Build();
                        config.AddAzureAppConfiguration(options =>
                            options.Connect(settings["ConnectionStrings:AppConfig"]).UseFeatureFlags());
                    }).UseStartup<Startup>();
    ```
    ---

2. Ouvrez *Startup.cs* et mettez à jour la méthode `Configure` et `ConfigureServices` pour ajouter l’intergiciel (middleware) intégré appelé `UseAzureAppConfiguration`. Ce middleware permet l’actualisation des valeurs d’indicateurs de fonctionnalités à intervalles réguliers pendant que l’application web ASP.NET continue de recevoir des requêtes.



    ```csharp
    public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
    {
        app.UseAzureAppConfiguration();
    }
    ```

   ```csharp
   public void ConfigureServices(IServiceCollection services)
   {
       services.AddAzureAppConfiguration();
   }
   ```
   
Dans un scénario classique, vous mettez périodiquement à jour les valeurs de vos indicateurs de fonctionnalités à mesure que vous déployez et activez les différentes fonctionnalités de votre application. Par défaut, les valeurs d’indicateur de fonctionnalité sont mises en cache pendant une période de 30 secondes. Une opération d’actualisation déclenchée quand le middleware reçoit une demande ne met donc à jour la valeur qu’après l’expiration de la valeur mise en cache. Le code suivant montre comment faire passer l’heure d’expiration du cache ou l’intervalle d’interrogation à 5 minutes en affectant **UseFeatureFlags** à [CacheExpirationInterval](/dotnet/api/microsoft.extensions.configuration.azureappconfiguration.featuremanagement.featureflagoptions.cacheexpirationinterval) dans l’appel.


    
```csharp
config.AddAzureAppConfiguration(options =>
    options.Connect(settings["ConnectionStrings:AppConfig"]).UseFeatureFlags(featureFlagOptions => {
        featureFlagOptions.CacheExpirationInterval = TimeSpan.FromMinutes(5);
    }));
});
```


## <a name="feature-flag-declaration"></a>Déclaration d’indicateur de fonctionnalité

Chaque déclaration d’indicateur de fonctionnalité comporte deux parties : un nom et une liste d’un ou plusieurs filtres qui servent à évaluer si l’état d’une fonctionnalité est *on* (autrement dit, quand sa valeur est `True`). Un filtre définit un critère pour le moment où une fonctionnalité doit être activée.

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
* `FeatureC` spécifie un filtre nommé `Percentage` avec une propriété `Parameters`. `Percentage` est un filtre configurable. Dans cet exemple, `Percentage` spécifie une probabilité de 50 % que l’indicateur `FeatureC` soit défini sur *on*. Pour obtenir un guide pratique sur l’utilisation des filtres de fonctionnalités, consultez [Utiliser des filtres de fonctionnalités pour activer les indicateurs de fonctionnalités conditionnels](./howto-feature-filters-aspnet-core.md).




## <a name="use-dependency-injection-to-access-ifeaturemanager"></a>Utiliser l’injection de dépendances pour accéder à IFeatureManager 

Pour certaines opérations, telles que la vérification manuelle des valeurs d’indicateurs de fonctionnalités, vous devez obtenir une instance de [IFeatureManager](/dotnet/api/microsoft.featuremanagement.ifeaturemanager?view=azure-dotnet-preview). Dans ASP.NET Core MVC, vous pouvez accéder au gestionnaire de fonctionnalités `IFeatureManager` par le biais de l’injection de dépendances. Dans l’exemple suivant, un argument de type `IFeatureManager` est ajouté à la signature du constructeur d’un contrôleur. Le runtime résout automatiquement la référence et fournit une interface lors de l’appel du constructeur. Si vous utilisez un modèle d’application dans lequel le contrôleur a déjà un ou plusieurs arguments d’injection de dépendances dans le constructeur, par exemple `ILogger`, vous pouvez simplement ajouter `IFeatureManager` comme argument supplémentaire :

### <a name="net-5x"></a>[.NET 5.x](#tab/core5x)
    
```csharp
using Microsoft.FeatureManagement;

public class HomeController : Controller
{
    private readonly IFeatureManager _featureManager;

    public HomeController(ILogger<HomeController> logger, IFeatureManager featureManager)
    {
        _featureManager = featureManager;
    }
}
```

### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

```csharp
using Microsoft.FeatureManagement;

public class HomeController : Controller
{
    private readonly IFeatureManager _featureManager;

    public HomeController(ILogger<HomeController> logger, IFeatureManager featureManager)
    {
        _featureManager = featureManager;
    }
}
```
    
### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

```csharp
using Microsoft.FeatureManagement;

public class HomeController : Controller
{
    private readonly IFeatureManager _featureManager;

    public HomeController(IFeatureManager featureManager)
    {
        _featureManager = featureManager;
    }
}
```

---

## <a name="feature-flag-references"></a>Références des indicateurs de fonctionnalités

Définissez les indicateurs de fonctionnalités en tant que variables de chaîne pour les référencer à partir du code :

```csharp
public static class MyFeatureFlags
{
    public const string FeatureA = "FeatureA";
    public const string FeatureB = "FeatureB";
    public const string FeatureC = "FeatureC";
}
```

## <a name="feature-flag-checks"></a>Vérifications des indicateurs de fonctionnalités

Le modèle de gestion des fonctionnalités courant consiste à vérifier si un indicateur de fonctionnalité est défini sur *on* et, le cas échéant, à exécuter une section de code. Par exemple :

```csharp
IFeatureManager featureManager;
...
if (await featureManager.IsEnabledAsync(MyFeatureFlags.FeatureA))
{
    // Run the following code
}
```

## <a name="controller-actions"></a>Actions de contrôleur

Avec les contrôleurs MVC, vous pouvez utiliser l’attribut `FeatureGate` pour déterminer si l’ensemble d’une classe de contrôleur ou une action spécifique est activée. Le contrôleur `HomeController` suivant exige que `FeatureA` soit défini sur *on* avant l’exécution de toute action que contient la classe de contrôleur :

```csharp
using Microsoft.FeatureManagement.Mvc;

[FeatureGate(MyFeatureFlags.FeatureA)]
public class HomeController : Controller
{
    ...
}
```

L’action `Index` suivante exige que `FeatureA` soit défini sur *on* pour pouvoir être exécutée :

```csharp
using Microsoft.FeatureManagement.Mvc;

[FeatureGate(MyFeatureFlags.FeatureA)]
public IActionResult Index()
{
    return View();
}
```

Quand une action ou un contrôleur MVC est bloqué car l’indicateur de fonctionnalité de contrôle est défini sur *off*, une interface [IDisabledFeaturesHandler](/dotnet/api/microsoft.featuremanagement.mvc.idisabledfeatureshandler?view=azure-dotnet-preview) inscrite est appelée. L’interface `IDisabledFeaturesHandler` par défaut retourne un code d’état 404 au client sans corps de réponse.

## <a name="mvc-views"></a>Vues MVC

Ouvrez *_ViewImports.cshtml* dans le répertoire *Views*, puis ajoutez le tag helper du gestionnaire de fonctionnalités :

```html
@addTagHelper *, Microsoft.FeatureManagement.AspNetCore
```

Dans les vues MVC, vous pouvez utiliser une balise `<feature>` pour restituer le contenu si un indicateur de fonctionnalité est activé :

```html
<feature name="FeatureA">
    <p>This can only be seen if 'FeatureA' is enabled.</p>
</feature>
```

Pour afficher un contenu alternatif quand les conditions requises ne sont pas remplies, l’attribut `negate` peut être utilisé.

```html
<feature name="FeatureA" negate="true">
    <p>This will be shown if 'FeatureA' is disabled.</p>
</feature>
```

La balise de fonctionnalité `<feature>` peut également être utilisée pour afficher le contenu si certaines ou l’ensemble des fonctionnalités dans une liste sont activées.

```html
<feature name="FeatureA, FeatureB" requirement="All">
    <p>This can only be seen if 'FeatureA' and 'FeatureB' are enabled.</p>
</feature>
<feature name="FeatureA, FeatureB" requirement="Any">
    <p>This can be seen if 'FeatureA', 'FeatureB', or both are enabled.</p>
</feature>
```

## <a name="mvc-filters"></a>Filtres MVC

Vous pouvez configurer des filtres MVC de sorte que leur activation soit basée sur l’état d’un indicateur de fonctionnalité. Cette fonctionnalité est limitée aux filtres qui implémentent [IAsyncActionFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncactionfilter). Le code suivant ajoute un filtre MVC nommé `ThirdPartyActionFilter`. Ce filtre est déclenché dans le pipeline MVC seulement si `FeatureA` est activé.  

```csharp
using Microsoft.FeatureManagement.FeatureFilters;

IConfiguration Configuration { get; set;}

public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc(options => {
        options.Filters.AddForFeature<ThirdPartyActionFilter>(MyFeatureFlags.FeatureA);
    });
}
```

## <a name="middleware"></a>Middlewares

Vous pouvez également utiliser des indicateurs de fonctionnalités pour ajouter de manière conditionnelle des intergiciels (middlewares) et des branches d’application. Le code suivant insère un composant d’intergiciel dans le pipeline de requête uniquement quand `FeatureA` est activé :

```csharp
app.UseMiddlewareForFeature<ThirdPartyMiddleware>(MyFeatureFlags.FeatureA);
```

Ce code repose sur la capacité plus générique qui consiste à créer une branche de l’ensemble de l’application entière en fonction d’un indicateur de fonctionnalité :

```csharp
app.UseForFeature(featureName, appBuilder => {
    appBuilder.UseMiddleware<T>();
});
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez découvert comment implémenter des indicateurs de fonctionnalités dans votre application ASP.NET Core en utilisant les bibliothèques `Microsoft.FeatureManagement`. Pour plus d’informations sur la prise en charge de la gestion des fonctionnalités dans ASP.NET Core et App Configuration, consultez les ressources suivantes :

* [Exemple de code d’indicateur de fonctionnalité ASP.NET Core](./quickstart-feature-flag-aspnet-core.md)
* [Documentation de Microsoft.FeatureManagement](/dotnet/api/microsoft.featuremanagement)
* [Gérer les indicateurs de fonctionnalité](./manage-feature-flags.md)