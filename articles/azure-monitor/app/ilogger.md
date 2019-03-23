---
title: Explorer les journaux de traçage .NET dans Azure Application Insights avec ILogger
description: Exemples d’utilisation de l’implémentation Azure Application Insights ILogger avec les applications console et ASP.NET Core.
services: application-insights
author: cijothomas
manager: carmonm
ms.service: application-insights
ms.topic: conceptual
ms.date: 02/19/2019
ms.reviewer: mbullwin
ms.author: cithomas
ms.openlocfilehash: deaddfbd27c4ffe6738988c6368ce4f9c3a7fa78
ms.sourcegitcommit: 223604d8b6ef20a8c115ff877981ce22ada6155a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/22/2019
ms.locfileid: "58359141"
---
# <a name="ilogger"></a>ILogger

ASP.NET Core prend en charge une API de journalisation qui fonctionne avec un large éventail de fournisseurs de journalisation intégrés et tiers. Cet article explique comment gérer la journalisation avec l’implémentation Application Insights ILogger dans les applications console et ASP.NET Core. Pour en savoir plus sur la journalisation basée sur ILogger, consultez [cet article](https://docs.microsoft.com/aspnet/core/fundamentals/logging).

## <a name="console-application"></a>Application de console

Voici un exemple d’application console configurée pour envoyer des traces ILogger à Application Insights.

Packages installés :

```xml
<ItemGroup>
  <PackageReference Include="Microsoft.Extensions.DependencyInjection" Version="2.1.0" />
  <PackageReference Include="Microsoft.Extensions.Logging" Version="2.1.0" />
  <PackageReference Include="Microsoft.Extensions.Logging.ApplicationInsights" Version="2.9.1" />
  <PackageReference Include="Microsoft.Extensions.Logging.Console" Version="2.1.0" />
</ItemGroup>
```

```csharp
class Program
{
    static void Main(string[] args)
    {
        // Create DI container.
        IServiceCollection services = new ServiceCollection();
            
        // Add the logging pipelines to use. We are using Application Insights only here.
        services.AddLogging(loggingBuilder =>
        {
        // Optional: Apply filters to configure LogLevel Trace or above is sent to ApplicationInsights for all
        // categories.
        loggingBuilder.AddFilter<ApplicationInsightsLoggerProvider>("", LogLevel.Trace);
            loggingBuilder.AddApplicationInsights("--YourAIKeyHere--");                
        });

        // Build ServiceProvider.
        IServiceProvider serviceProvider = services.BuildServiceProvider();

        ILogger<Program> logger = serviceProvider.GetRequiredService<ILogger<Program>>();

        // Begin a new scope. This is optional. Epecially in case of AspNetCore request info is already
        // present in scope.
        using (logger.BeginScope(new Dictionary<string, object> { { "Method", nameof(Main) } }))
        {
            logger.LogInformation("Logger is working"); // this will be captured by Application Insights.
        }
    }
}
```

## <a name="aspnet-core-application"></a>Application ASP.NET Core

Voici un exemple d’application ASP.NET Core configurée pour envoyer des traces ILogger à Application Insights. Cet exemple peut être suivi pour envoyer des traces ILogger à partir de Program.cs, Startup.cs ou toute autre logique d’application/contrôleur.

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        var host = BuildWebHost(args);
        var logger = host.Services.GetRequiredService<ILogger<Program>>();
        logger.LogInformation("From Program. Running the host now.."); // This will be picked up by AI
        host.Run();
    }

    public static IWebHost BuildWebHost(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()                
        .ConfigureLogging(logging =>
        {                
        logging.AddApplicationInsights("ikeyhere");
                
        // Optional: Apply filters to configure LogLevel Trace or above is sent to
        // ApplicationInsights for all categories.
            logging.AddFilter<ApplicationInsightsLoggerProvider>("", LogLevel.Trace);
                
            // Additional filtering For category starting in "Microsoft",
        // only Warning or above will be sent to Application Insights.
        logging.AddFilter<ApplicationInsightsLoggerProvider>("Microsoft", LogLevel.Warning);
        })
        .Build();
}
```

```csharp
public class Startup
{
    private readonly ILogger _logger;

    public Startup(IConfiguration configuration, ILogger<Startup> logger)
    {
        Configuration = configuration;
        _logger = logger;
    }

    public IConfiguration Configuration { get; }

    // This method gets called by the runtime. Use this method to add services to the container.
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
    
    // The following be picked up by Application Insights.
        _logger.LogInformation("From ConfigureServices. Services.AddMVC invoked"); 
    }

    // This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        if (env.IsDevelopment())
        {
        // The following be picked up by Application Insights.  
            _logger.LogInformation("Configuring for Development environment");
            app.UseDeveloperExceptionPage();
        }
        else
        {
            // The following be picked up by Application Insights.
            _logger.LogInformation("Configuring for Production environment");
        }

        app.UseMvc();
    }
}
```

```csharp
public class ValuesController : ControllerBase
{
    private readonly ILogger _logger;

    public ValuesController(ILogger<ValuesController> logger)
    {
        _logger = logger;
    }

    // GET api/values
    [HttpGet]
    public ActionResult<IEnumerable<string>> Get()
    {
        // All the following logs will be picked up by Application Insights.
    // and all have ("MyKey", "MyValue") in Properties.
    using (_logger.BeginScope(new Dictionary<string, object> { { "MyKey", "MyValue" } }))
        {           
        _logger.LogInformation("An example of a Information trace..");
        _logger.LogWarning("An example of a Warning trace..");
        _logger.LogTrace("An example of a Trace level message");
        }

        return new string[] { "value1", "value2" };
    }
}
```

Dans les deux exemples ci-dessus, le package autonome Microsoft.Extensions.Logging.ApplicationInsights est utilisé. Par défaut, cette configuration utilise le strict minimum `TelemetryConfiguration` pour envoyer des données à Application Insights. Le strict minimum signifie que le canal utilisé sera `InMemoryChannel`, aucun échantillonnage et aucun TelemetryInitializers standard. Ce comportement peut être substitué pour une application console comme indiqué dans l’exemple ci-dessous.

Installez ce package supplémentaire :

```xml
<PackageReference Include="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel" Version="2.9.1" />
```

La section suivante montre comment substituer la valeur par défaut `TelemetryConfiguration`. Cet exemple configure `ServerTelemetryChannel`, l’échantillonnage, ainsi qu’une valeur `ITelemetryInitializer` personnalisée.

```csharp
    // Create DI container.
    IServiceCollection services = new ServiceCollection();
    var serverChannel = new ServerTelemetryChannel();
    services.Configure<TelemetryConfiguration>(
        (config) =>
        {                            
            config.TelemetryChannel = serverChannel;
            config.TelemetryInitializers.Add(new MyTelemetryInitalizer());
            config.DefaultTelemetrySink.TelemetryProcessorChainBuilder.UseSampling(5);
            serverChannel.Initialize(config);
        }
    );

    // Add the logging pipelines to use. We are adding ApplicationInsights only.
    services.AddLogging(loggingBuilder =>
    {
        loggingBuilder.AddApplicationInsights();
    });
```

Bien que l’approche ci-dessus peut être utilisée dans une application ASP.NET Core, une approche plus courante consiste à combiner l’analyse d’application régulière (requêtes, dépendances, etc.) avec la capture ILogger, comme indiqué ci-dessous.

Installez ce package supplémentaire :

```xml
<PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.6.1" />
```

Ajoutez ce qui suit à la méthode `ConfigureServices`. Ce code permet l’analyse d’application régulière avec la configuration par défaut (ServerTelemetryChannel, LiveMetrics, requêtes/dépendances, corrélation, etc.)

```csharp
services.AddApplicationInsightsTelemetry("ikeyhere");
```

Dans cet exemple, la configuration utilisée par `ApplicationInsightsLoggerProvider` est identique à celle utilisée par l’analyse d’application régulière. Par conséquent, les deux traces `ILogger` et les autres données de télémétrie (requêtes, dépendances, etc.) exécutent le même ensemble de `TelemetryInitializers`, `TelemetryProcessors` et `TelemetryChannel`. Ils seront mis en corrélation et échantillonnés/non échantillonnés de la même façon.

Toutefois, il existe une exception à ce comportement. La valeur par défaut `TelemetryConfiguration` n’est pas entièrement configurée lors de la journalisation d’un élément à partir de `Program.cs` ou `Startup.cs`, alors ces journaux n’auront pas la configuration par défaut. Toutefois, les autres journaux (par exemple, les journaux à partir des contrôleurs, des modèles, etc.) partagent la configuration.

## <a name="control-logging-level"></a>Niveau de journalisation de contrôle

Outre le filtrage des journaux sur le code comme dans les exemples ci-dessus, il est également possible de contrôler le niveau de journalisation Application Insights capture, en modifiant le `appsettings.json`. Le [ASP.NET journalisation documentation des notions de base](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.2#log-filtering) montre comment y parvenir. Spécifiquement pour Application Insights, le nom de l’alias de fournisseur est `ApplicationInsights`, comme illustré dans l’exemple qui configure ci-dessous `ApplicationInsights` pour capturer uniquement les journaux de `Warning` et versions ultérieures de toutes les catégories.

```json
{
  "Logging": {
    "ApplicationInsights": {
      "LogLevel": {
        "Default": "Warning"
      }
    },
    "LogLevel": {
      "Default": "Warning"
    }
  },
  "AllowedHosts": "*"
}
```

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus :

- [Journalisation basée sur ILogger](https://docs.microsoft.com/aspnet/core/fundamentals/logging)
- [Journaux des traces .NET](../../azure-monitor/app/asp-net-trace-logs.md)
