---
title: Journalisation d’Application Insights avec .NET
description: Découvrez comment utiliser Application Insights avec l’interface ILogger dans .NET.
ms.topic: conceptual
ms.date: 05/20/2021
ms.reviewer: mbullwin
ms.openlocfilehash: 6b0c746ffda97c22dc42a4c2016413a80552d28d
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/26/2021
ms.locfileid: "110461514"
---
# <a name="application-insights-logging-with-net"></a>Journalisation d’Application Insights avec .NET

Dans cet article, vous allez apprendre à capturer les journaux avec Application Insights dans les applications .NET à l’aide de plusieurs packages NuGet :

- **Package principal :**
  - [`Microsoft.Extensions.Logging.ApplicationInsights`][nuget-ai]
- **Packages de charge de travail :**
  - [`Microsoft.ApplicationInsights.AspNetCore`][nuget-ai-anc]
  - [`Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel`][nuget-ai-ws-tc]

[nuget-ai]: https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights
[nuget-ai-anc]: https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore
[nuget-ai-ws]: https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService
[nuget-ai-ws-tc]: https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel

> [!TIP]
> Le package NuGet [`Microsoft.ApplicationInsights.WorkerService`][nuget-ai-ws] dépasse le cadre de cet article. Il peut être utilisé pour activer Application Insights pour les services d’arrière-plan. Pour plus d’informations, consultez [Application Insights pour les applications Worker Service](./worker-service.md).

Selon le package de journalisation Application Insights que vous utilisez, il existe différentes façons d’inscrire le `ApplicationInsightsLoggerProvider`. Le `ApplicationInsightsLoggerProvider` est une implémentation de <xref:Microsoft.Extensions.Logging.ILoggerProvider>, qui est chargé de fournir des implémentations <xref:Microsoft.Extensions.Logging.ILogger> et <xref:Microsoft.Extensions.Logging.ILogger%601>.

## <a name="aspnet-core-applications"></a>applications ASP.NET Core ;

Pour ajouter la télémétrie Application Insights à des applications ASP.NET Core, utilisez le package `Microsoft.ApplicationInsights.AspNetCore` NuGet. Vous pouvez le configurer à partir de [Visual Studio en tant que service connecté](/visualstudio/azure/azure-app-insights-add-connected-service) ou manuellement.

Par défaut, les applications ASP.NET Core ont le fournisseur de journalisation Application Insights enregistré lorsqu’elles sont configurées à l’aide de l’approche [avec code](./asp-net-core.md) ou [sans code](./azure-web-apps.md?tabs=netcore#enable-agent-based-monitoring) . Le fournisseur inscrit est configuré pour capturer automatiquement les événements de journal dont la gravité est <xref:Microsoft.Extensions.Logging.LogLevel.Warning?displayProperty=nameWithType> ou plus. La gravité et les catégories peuvent être personnalisées. Pour plus d’informations, consultez [Contrôle du niveau de journalisation](#control-logging-level).

1. Vérifiez que le package NuGet est installé :

   ```xml
    <ItemGroup>
        <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.17.0" />
    </ItemGroup>
   ```

1. Assurez-vous que la méthode `Startup.ConfigureServices` appelle `services.AddApplicationInsightsTelemetry` :

    ```csharp
    using Microsoft.AspNetCore.Builder;
    using Microsoft.AspNetCore.Hosting;
    using Microsoft.AspNetCore.Http;
    using Microsoft.Extensions.DependencyInjection;
    using Microsoft.Extensions.Hosting;
    using Microsoft.Extensions.Configuration;
    
    namespace WebApplication
    {
        public class Startup
        {
            public Startup(IConfiguration configuration)
            {
                Configuration = configuration;
            }

            public IConfiguration Configuration { get; }

            public void ConfigureServices(IServiceCollection services)
            {
                services.AddApplicationInsightsTelemetry(
                    Configuration["APPINSIGHTS_CONNECTIONSTRING"]);

                // An alternative overload, when not using appsettings.json or user secrets.
                // services.AddApplicationInsightsTelemetry();
            }

            public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
            {
                // omitted for brevity
            }
        }
    }
    ```

Lorsque le package NuGet est installé et que le fournisseur est inscrit avec l’injection de dépendances, l’application est prête à se connecter. Avec l’injection de constructeur, exigez <xref:Microsoft.Extensions.Logging.ILogger> ou l’alternative de type générique <xref:Microsoft.Extensions.Logging.ILogger%601>. Lorsque ces implémentations sont résolues, le `ApplicationInsightsLoggerProvider` les fournira. Les messages ou les exceptions journalisés sont envoyés à Application Insights. Considérez l’exemple de contrôleur suivant :

```csharp
public class ValuesController : ControllerBase
{
    private readonly ILogger _logger;

    public ValuesController(ILogger<ValuesController> logger)
    {
        _logger = logger;
    }

    [HttpGet]
    public ActionResult<IEnumerable<string>> Get()
    {
        _logger.LogWarning("An example of a Warning trace..");
        _logger.LogError("An example of an Error level message");

        return new string[] { "value1", "value2" };
    }
}
```

Pour plus d’informations, consultez l’article [Logging in ASP.NET Core](/aspnet/core/fundamentals/logging) (Journalisation dans ASP.NET Core).

### <a name="capture-logs-within-aspnet-core-startup-code"></a>Capturer des journaux dans le code de démarrage d’ASP.NET Core

Certains scénarios nécessitent la capture des journaux dans le cadre de la routine de démarrage de l’application, avant que le pipeline de requête-réponse soit prêt à accepter les demandes. Toutefois, les implémentations `ILogger` ne sont pas facilement disponibles à partir de l’injection de dépendances dans *Program.cs* et *Startup.cs*. Pour plus d’informations, consultez [Journalisation dans .NET : créer des journaux dans `Main`](/dotnet/core/extensions/logging?tabs=command-line#create-logs-in-main).

Il y a plusieurs limitations applicables à la journalisation à partir de *Program.cs* et *Startup.cs* :

* La télémétrie est envoyée à l’aide du canal de télémétrie [InMemoryChannel](./telemetry-channels.md).
* Aucun [échantillonnage](./sampling.md) n’est appliqué aux données de télémétrie.
* Les [initialiseurs de télémétrie standard ou les processeurs](./api-filtering-sampling.md) ne sont pas disponibles.

Les exemples suivants illustrent cela en instanciant et en configurant explicitement *Program.cs* et *Startup.cs* :

#### <a name="example-programcs"></a>Exemple avec Program.cs

```csharp
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;
using Microsoft.Extensions.Logging;
using Microsoft.Extensions.Logging.ApplicationInsights;

namespace WebApplication
{
    public class Program
    {
        public static void Main(string[] args)
        {
            var host = CreateHostBuilder(args).Build();

            var logger = host.Services.GetRequiredService<ILogger<Program>>();
            logger.LogInformation("From Program, running the host now.");

            host.Run();
        }

        public static IHostBuilder CreateHostBuilder(string[] args) =>
            Host.CreateDefaultBuilder(args)
                .ConfigureWebHostDefaults(webBuilder =>
                {
                    webBuilder.UseStartup<Startup>();
                })
                .ConfigureLogging((context, builder) =>
                {
                    // Providing an instrumentation key is required if you're using the
                    // standalone Microsoft.Extensions.Logging.ApplicationInsights package,
                    // or when you need to capture logs during application startup, for example
                    // in the Program.cs or Startup.cs itself.
                    builder.AddApplicationInsights(
                        context.Configuration["APPINSIGHTS_CONNECTIONSTRING"]);

                    // Capture all log-level entries from Program
                    builder.AddFilter<ApplicationInsightsLoggerProvider>(
                        typeof(Program).FullName, LogLevel.Trace);

                    // Capture all log-level entries from Startup
                    builder.AddFilter<ApplicationInsightsLoggerProvider>(
                        typeof(Startup).FullName, LogLevel.Trace);
                });
    }
}
```

Dans le code précédent, le `ApplicationInsightsLoggerProvider` est configuré avec votre chaîne de connexion `"APPINSIGHTS_CONNECTIONSTRING"` et des filtres sont appliqués en affectant au niveau du journal la valeur <xref:Microsoft.Extensions.Logging.LogLevel.Trace?displayProperty=nameWithType>.

> [!IMPORTANT]
> Les [chaînes de connexion](./sdk-connection-string.md?tabs=net) sont recommandées par rapport aux clés d’instrumentation. Les nouvelles régions Azure **exigent** l’utilisation de chaînes de connexion au lieu de clés d’instrumentation. Une chaîne de connexion identifie la ressource à laquelle vous souhaitez associer vos données de télémétrie. Elle vous permet également de modifier les points de terminaison que votre ressource utilisera comme destination pour votre télémétrie. Vous devrez copier la chaîne de connexion et l’ajouter au code de votre application ou à une variable d’environnement.

#### <a name="example-startupcs"></a>Exemple avec Startup.cs

```csharp
using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.Hosting;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.Logging;

namespace WebApplication
{
    public class Startup
    {
        public Startup(IConfiguration configuration)
        {
            Configuration = configuration;
        }

        public IConfiguration Configuration { get; }

        public void ConfigureServices(IServiceCollection services)
        {
            services.AddApplicationInsightsTelemetry(
                Configuration["APPINSIGHTS_CONNECTIONSTRING"]);
        }

        // The ILogger<Startup> is resolved by dependency injection
        // and available in Startup.Configure.
        public void Configure(
            IApplicationBuilder app, IWebHostEnvironment env, ILogger<Startup> logger)
        {
            logger.LogInformation(
                "Configuring for {Environment} environment",
                env.EnvironmentName);

            if (env.IsDevelopment())
            {
                app.UseDeveloperExceptionPage();
            }

            app.UseRouting();
            app.UseEndpoints(endpoints =>
            {
                endpoints.MapGet("/", async context =>
                {
                    await context.Response.WriteAsync("Hello World!");
                });
            });
        }
    }
}
```

## <a name="console-application"></a>Application de console

Packages installés :

```xml
<ItemGroup>
  <PackageReference Include="Microsoft.Extensions.DependencyInjection" Version="5.0.0" />
  <PackageReference Include="Microsoft.Extensions.Logging.ApplicationInsights" Version="2.17.0"/>
</ItemGroup>
```

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Logging;
using Microsoft.Extensions.Logging.ApplicationInsights;
using System;
using System.Threading.Tasks;

namespace ConsoleApp
{
    class Program
    {
        static async Task Main(string[] args)
        {
            using var channel = new InMemoryChannel();

            try
            {
                IServiceCollection services = new ServiceCollection();
                services.Configure<TelemetryConfiguration>(config => config.TelemetryChannel = channel);
                services.AddLogging(builder =>
                {
                    // Only Application Insights is registered as a logger provider
                    builder.AddApplicationInsights("<YourInstrumentationKey>");
                });

                IServiceProvider serviceProvider = services.BuildServiceProvider();
                ILogger<Program> logger = serviceProvider.GetRequiredService<ILogger<Program>>();

                logger.LogInformation("Logger is working...");
            }
            finally
            {
                // Explicitly call Flush() followed by delay is required in Console Apps.
                // This is to ensure that even if application terminates, telemetry is sent to the back-end.
                channel.Flush();

                await Task.Delay(TimeSpan.FromMilliseconds(1000));
            }
        }
    }
}

```

Dans l’exemple précédent, le package `Microsoft.Extensions.Logging.ApplicationInsights` est utilisé. Par défaut, cette configuration utilise le `TelemetryConfiguration` strict minimum pour envoyer des données à Application Insights. Le strict minimum signifie que `InMemoryChannel` est le canal utilisé. Il n’y a aucun échantillonnage et aucune norme `TelemetryInitializer`. Ce comportement peut être substitué pour une application console comme indiqué dans l’exemple suivant.

Installez ce package supplémentaire :

```xml
<PackageReference Include="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel" Version="2.17.0" />
```

La section suivante montre comment substituer le `TelemetryConfiguration` par défaut en utilisant la méthode <xref:Microsoft.Extensions.Options.ConfigureOptions%601.Configure(%600)>. Cet exemple montre comment configurer `ServerTelemetryChannel` et l’échantillonnage. Il ajoute un ITelemetryInitializer personnalisé à TelemetryConfiguration.

```csharp
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Logging;
using Microsoft.Extensions.Logging.ApplicationInsights;
using System;
using System.Threading.Tasks;

namespace ConsoleApp
{
    class Program
    {
        static async Task Main(string[] args)
        {
            using var channel = new ServerTelemetryChannel();

            try
            {
                IServiceCollection services = new ServiceCollection();
                services.Configure<TelemetryConfiguration>(
                    config =>
                    {
                        config.TelemetryChannel = channel;

                        // Optional: implement your own TelemetryInitializer and configure it here
                        // config.TelemetryInitializers.Add(new MyTelemetryInitializer());

                        config.DefaultTelemetrySink.TelemetryProcessorChainBuilder.UseSampling(5);
                        channel.Initialize(config);
                    });

                services.AddLogging(builder =>
                {
                    // Only Application Insights is registered as a logger provider
                    builder.AddApplicationInsights("<YourInstrumentationKey>");
                });

                IServiceProvider serviceProvider = services.BuildServiceProvider();
                ILogger<Program> logger = serviceProvider.GetRequiredService<ILogger<Program>>();

                logger.LogInformation("Logger is working...");
            }
            finally
            {
                // Explicitly call Flush() followed by delay is required in Console Apps.
                // This is to ensure that even if application terminates, telemetry is sent to the back-end.
                channel.Flush();

                await Task.Delay(TimeSpan.FromMilliseconds(1000));
            }
        }
    }
}
```

## <a name="control-logging-level"></a>Contrôler le niveau de journalisation

Les implémentations `ILogger` disposent d’un mécanisme intégré pour appliquer le [filtrage des journaux](/dotnet/core/extensions/logging#how-filtering-rules-are-applied). Cela vous permet de contrôler les journaux qui sont envoyés à chaque fournisseur inscrit, y compris le fournisseur Application Insights. Le filtrage peut s’effectuer soit dans la configuration, par exemple en utilisant un fichier *appsettings.json*, ou dans le code.

Les exemples suivants montrent comment appliquer des règles de filtre à `ApplicationInsightsLoggerProvider`.

### <a name="create-filter-rules-in-configuration-with-appsettingsjson"></a>Créer des règles de filtrage dans la configuration avec appsettings.json

Le `ApplicationInsightsLoggerProvider` est aliasé sous « ApplicationInsights ». La section suivante de *appsettings.json* remplace le niveau de journalisation <xref:Microsoft.Extensions.Logging.LogLevel.Warning?displayProperty=nameWithType> par défaut d’Application Insights à des catégories de journaux qui commencent par « Microsoft » au niveau <xref:Microsoft.Extensions.Logging.LogLevel.Error?displayProperty=nameWithType> et au-dessus.

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Warning"
    },
    "ApplicationInsights": {
      "LogLevel": {
        "Microsoft": "Error"
      }
    }
  }
}
```

### <a name="create-filter-rules-in-code"></a>Créer des règles de filtre dans le code

L’extrait de code suivant configure les journaux de niveau <xref:Microsoft.Extensions.Logging.LogLevel.Warning?displayProperty=nameWithType> et plus de toutes les catégories, et de niveau <xref:Microsoft.Extensions.Logging.LogLevel.Error?displayProperty=nameWithType> et plus, les catégories qui commencent par « Microsoft » à envoyer à `ApplicationInsightsLoggerProvider`.

```csharp
Host.CreateDefaultBuilder(args)
    .UseStartup<Startup>()
    .ConfigureLogging(builder =>
    {
        builder.AddFilter<ApplicationInsightsLoggerProvider>("", LogLevel.Warning);
        builder.AddFilter<ApplicationInsightsLoggerProvider>("Microsoft", LogLevel.Error);
    });
```

Ce code précédent est fonctionnellement équivalent à la section précédente dans *appsettings.json*. Pour plus d’informations, consultez [Configuration dans .NET](/dotnet/core/extensions/configuration).

## <a name="logging-scopes"></a>Étendues de journalisation

Le `ApplicationInsightsLoggingProvider` prend en charge les [Étendues de journal](/dotnet/core/extensions/logging#log-scopes). et les étendues sont activées par défaut. Si l’étendue est de type `IReadOnlyCollection<KeyValuePair<string,object>>`, chaque paire clé-valeur de la collection est ajoutée à la télémétrie Application Insights sous forme de propriétés personnalisées. Dans l’exemple ci-dessous, les journaux sont capturés comme `TraceTelemetry` et comportent `("MyKey", "MyValue")` dans leurs propriétés.

```csharp
using (_logger.BeginScope(new Dictionary<string, object> { ["MyKey"] = "MyValue" }))
{
    _logger.LogError("An example of an Error level message");
}
```

Si un autre type est utilisé comme étendue, il est stocké sous la propriété « Scope » dans la télémétrie Application Insights. Dans l’exemple ci-dessous, `TraceTelemetry` comporte une propriété appelée « Scope » contenant l’étendue.

```csharp
    using (_logger.BeginScope("hello scope"))
    {
        _logger.LogError("An example of an Error level message");
    }
```

## <a name="frequently-asked-questions"></a>Forum aux questions

### <a name="what-are-the-old-and-new-versions-of-applicationinsightsloggerprovider"></a>Quelles sont les versions anciennes et nouvelles de ApplicationInsightsLoggerProvider ?

Le [SDK Microsoft.ApplicationInsights.AspNet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) inclut un fournisseur ApplicationInsightsLoggerProvider intégré (Microsoft.ApplicationInsights.AspNetCore.Logging.Logging.ApplicationInsightsLoggerProvider), qui était activé par les méthodes d’extension **ILoggerFactory**. Ce fournisseur est marqué obsolète à partir de la version 2.7.1. Il sera complètement supprimé lors de la prochaine mise à jour majeure de version. Le package [Microsoft.ApplicationInsights.AspNetCore 2.6.1](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) lui-même n’est pas obsolète. Il est nécessaire pour permettre la surveillance des requêtes, des dépendances, etc.

Il est également possible de préférer le nouveau package autonome [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights), qui contient un fournisseur ApplicationInsightsLoggerProvider amélioré (Microsoft.Extensions.Logging.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider) et des méthodes d’extension sur ILoggerBuilder pour son activation.

Le [Kit de développement logiciel (SDK) Microsoft.ApplicationInsights.AspNet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) version 2.7.1 établit une dépendance avec le nouveau package et active automatiquement la fonctionnalité de capture ILogger.

### <a name="why-are-some-ilogger-logs-shown-twice-in-application-insights"></a>Pourquoi certains journaux ILogger figurent-ils deux fois dans Application Insights ?

Si l’ancienne version (maintenant obsolète) de ApplicationInsightsLoggerProvider est activée dans votre environnement, la duplication est possible en appelant `AddApplicationInsights` sur `ILoggerFactory`. Vérifiez si votre méthode **Configure** possède les éléments suivants et supprimez-la :

```csharp
 public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
 {
     loggerFactory.AddApplicationInsights(app.ApplicationServices, LogLevel.Warning);
     // ..other code.
 }
```

Si vous rencontrez une double journalisation lorsque vous déboguez depuis Visual Studio, définissez `EnableDebugLogger` sur `false` dans le code qui active Application Insights, comme suit. Cette duplication et ce correctif ne sont pertinents que lorsque vous déboguez l’application.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    var options = new ApplicationInsightsServiceOptions
    {
        EnableDebugLogger = false
    }
    services.AddApplicationInsightsTelemetry(options);
    // ...
}
```

### <a name="i-updated-to-microsoftapplicationinsightsaspnet-sdk-version-271-and-logs-from-ilogger-are-captured-automatically-how-do-i-turn-off-this-feature-completely"></a>J’ai effectué la mise à jour vers le [Kit de développement logiciel (SDK) Microsoft.ApplicationInsights.AspNet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) version 2.7.1, et les journaux d’activité d’ILogger sont capturés automatiquement. Comment désactiver complètement cette fonctionnalité ?

Consultez la section [Contrôler le niveau de journalisation](#control-logging-level) pour découvrir comment filtrer les journaux en général. Pour désactiver ApplicationInsightsLoggerProvider, utilisez `LogLevel.None` :

Dans l’appel de configuration de la journalisation, où `builder` est <xref:Microsoft.Extensions.Logging.ILoggingBuilder> :

```csharp
builder.AddFilter<ApplicationInsightsLoggerProvider>("", LogLevel.None);
```

Depuis le fichier *appsettings.json* :

```json
{
    "Logging": {
        "ApplicationInsights": {
            "LogLevel": {
                "Default": "None"
            }
        }
    }
}
```

### <a name="why-do-some-ilogger-logs-not-have-the-same-properties-as-others"></a>Pourquoi certains journaux ILogger n’ont-ils pas les mêmes propriétés que d’autres ?

Application Insights capture et envoie les journaux ILogger à l’aide de la même TelemetryConfiguration qui est utilisée pour toutes les autres données de télémétrie. Il existe cependant une exception. Par défaut, TelemetryConfiguration n’est pas entièrement configuré lorsque vous vous connectez à partir de *Program.cs* ou de *Startup.cs*. Les journaux figurant à ces emplacements ne sont pas soumis à la configuration par défaut et n’exécuteront donc pas tous TelemetryInitializers et TelemetryProcessors.

### <a name="im-using-the-standalone-package-microsoftextensionsloggingapplicationinsights-and-i-want-to-log-some-additional-custom-telemetry-manually-how-should-i-do-that"></a>J’utilise le package autonome Microsoft.Extensions.Logging.Logging.ApplicationInsights, et je veux enregistrer manuellement certaines télémesures personnalisées supplémentaires. Comment dois-je procéder ?

Lorsque vous utilisez le package autonome, `TelemetryClient` n’est pas injecté dans le conteneur DI, vous devez donc créer une nouvelle instance de `TelemetryClient` et utiliser la même configuration que celle du fournisseur d’enregistreur, comme le montre le code suivant. Cela garantit que la même configuration est utilisée pour toutes les données de télémétrie personnalisées ainsi que pour les données de télémétrie de la fonctionnalité ILogger.

```csharp
public class MyController : ApiController
{
   // This TelemetryClient can be used to track additional telemetry using TrackXXX() api.
   private readonly TelemetryClient _telemetryClient;
   private readonly ILogger _logger;

   public MyController(IOptions<TelemetryConfiguration> options, ILogger<MyController> logger)
   {
        _telemetryClient = new TelemetryClient(options.Value);
        _logger = logger;
   }  
}
```

> [!NOTE]
> Si vous utilisez le package `Microsoft.ApplicationInsights.AspNetCore` pour activer Application Insights, modifiez ce code pour obtenir `TelemetryClient` directement dans le constructeur. Pour obtenir un exemple, consultez [ce forum aux questions](./asp-net-core.md#frequently-asked-questions).

### <a name="what-application-insights-telemetry-type-is-produced-from-ilogger-logs-or-where-can-i-see-ilogger-logs-in-application-insights"></a>Quel type de télémétrie Application Insights est produit à partir des journaux d’activité ILogger ? Où les journaux d’activité ILogger figurent-ils dans Application Insights ?

ApplicationInsightsLoggerProvider capture les journaux ILogger et crée TraceTelemetry à partir de ceux-ci. Si un objet Exception est transmis à la méthode `Log` sur `ILogger`, *ExceptionTelemetry* est créé à la place de TraceTelemetry. Ces éléments de télémétrie figurent aux mêmes emplacements que n’importe quel autre élément TraceTelemetry ou ExceptionTelemetry pour Application Insights, y compris le portail, les analyses ou le débogueur local Visual Studio.

Si vous préférez envoyer `TraceTelemetry` systématiquement, utilisez cet extrait de code :

```csharp
builder.AddApplicationInsights(
    options => options.TrackExceptionsAsExceptionTelemetry = false);
```

### <a name="i-dont-have-the-sdk-installed-and-i-use-the-azure-web-apps-extension-to-enable-application-insights-for-my-aspnet-core-applications-how-do-i-use-the-new-provider"></a>Le SDK n’est pas installé mais j’utilise l’extension Azure Web Apps pour activer Application Insights pour mes applications ASP.NET Core. Comment utiliser Personalizer ? 

L’extension Application Insights d’Azure Web Apps utilise le nouveau fournisseur. Vous pouvez modifier les règles de filtrage dans le fichier *appsettings.json* de votre application.

## <a name="next-steps"></a>Étapes suivantes

* [Journalisation dans .NET](/dotnet/core/extensions/logging)
* [Journalisation dans ASP.NET Core](/aspnet/core/fundamentals/logging)
* [Journaux d’activité .NET dans Application Insights](./asp-net-trace-logs.md)
