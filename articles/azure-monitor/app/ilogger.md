---
title: Explorer les journaux d’activité de traçage .NET dans Azure Application Insights avec ILogger
description: Exemples d’utilisation du fournisseur d’Azure Application Insights ILogger avec les applications ASP.NET Core et de la Console.
services: application-insights
author: cijothomas
manager: carmonm
ms.service: application-insights
ms.topic: conceptual
ms.date: 02/19/2019
ms.reviewer: mbullwin
ms.author: cithomas
ms.openlocfilehash: 615eaa3df7cabad72ac321978eb01d93a7bfa988
ms.sourcegitcommit: 5f348bf7d6cf8e074576c73055e17d7036982ddb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2019
ms.locfileid: "59608283"
---
# <a name="applicationinsightsloggerprovider-for-net-core-ilogger-logs"></a>ApplicationInsightsLoggerProvider pour les journaux de .NET Core ILogger

ASP.NET Core prend en charge une API de journalisation qui fonctionne avec différents types de fournisseurs de journalisation intégrés et tiers. La journalisation est effectuée en appelant Log() ou une variante de celui-ci sur `ILogger` instances. Cet article montre comment utiliser `ApplicationInsightsLoggerProvider` pour capturer `ILogger` enregistre dans la console et les applications ASP.NET Core. Cet article décrit également comment `ApplicationInsightsLoggerProvider` est intégré à d’autres données de télémétrie Application Insights.
Pour en savoir plus la journalisation dans Asp.Net Core, consultez [cet article](https://docs.microsoft.com/aspnet/core/fundamentals/logging).

## <a name="aspnet-core-applications"></a>Applications ASP.NET Core

En commençant par [Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) 2.7.0-beta3 de version et les versions ultérieures, `ApplicationInsightsLoggerProvider` est activé par défaut lorsque vous activez la surveillance d’Application Insights régulières à l’aide des méthodes standards - par appel `UseApplicationInsights` méthode d’extension sur IWebHostBuilder ou `AddApplicationInsightsTelemetry` méthode d’extension sur IServiceCollection. `ILogger` journaux capturés par `ApplicationInsightsLoggerProvider` sont soumises à la même configuration en tant que les autres données de télémétrie collectées. Par exemple, ils ont le même ensemble de `TelemetryInitializer`s, `TelemetryProcessor`s, utilise le même `TelemetryChannel`et seront mis en corrélation et échantillonnées dans la même façon que toutes les autres données de télémétrie.  Si vous utilisez cette version du SDK ou une version ultérieure, aucune action n’est nécessaire pour capturer `ILogger` journaux.

Par défaut, seuls `ILogger` consigne de `Warning` ou au-dessus (à partir de toutes les catégories) sont envoyés à Application Insights. Ce comportement peut être modifié en appliquant des filtres comme indiqué [ici](#control-logging-level). Également les étapes supplémentaires sont nécessaires si `ILogger` se connecte à partir de `Program.cs` ou `Startup.cs` à capturer comme [ici](#capturing-ilogger-logs-from-startupcs-programcs-in-aspnet-core-applications).

Si vous utilisez une version antérieure du Kit de développement logiciel Microsoft.ApplicationInsights.AspNet, ou que vous souhaitez simplement utiliser ApplicationInsightsLoggerProvider, sans n’importe quel autre Application Insights analyse, suivez les étapes ci-dessous.

1. Installez le package nuget.

```xml
    <ItemGroup>
      <PackageReference Include="Microsoft.Extensions.Logging.ApplicationInsights" Version="2.9.1" />  
    </ItemGroup>
```

2. Modifiez `Program.cs` comme suit

```csharp
using Microsoft.AspNetCore;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Logging;

public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureLogging(
            builder =>
            {
                // Providing an instrumentation key here is required if you are using
                // standalone package Microsoft.Extensions.Logging.ApplicationInsights
                // or if you want to capture logs from early in the application startup
                // pipeline from Startup.cs or Program.cs itself.
                builder.AddApplicationInsights("ikey");

                // Optional: Apply filters to control what logs are sent to Application Insights.
                // The following configures LogLevel Information or above to be sent to
                // Application Insights for all categories.
                builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                                 ("", LogLevel.Information);
            }
        );
}
```

Le code ci-dessus configurera `ApplicationInsightsLoggerProvider`. Voici un exemple de classe de contrôleur qui utilise `ILogger` pour envoyer des journaux, qui sont capturées par ApplicationInsights.

```csharp
public class ValuesController : ControllerBase
{
    private readonly `ILogger` _logger;

    public ValuesController(ILogger<ValuesController> logger)
    {
        _logger = logger;
    }

    // GET api/values
    [HttpGet]
    public ActionResult<IEnumerable<string>> Get()
    {
        // All the following logs will be picked up by Application Insights.
        // and all of them will have ("MyKey", "MyValue") in Properties.
        using (_logger.BeginScope(new Dictionary<string, object> { { "MyKey", "MyValue" } }))
            {
                _logger.LogWarning("An example of a Warning trace..");
                _logger.LogError("An example of an Error level message");
            }
        return new string[] { "value1", "value2" };
    }
}
```

### <a name="capturing-ilogger-logs-from-startupcs-programcs-in-aspnet-core-applications"></a>Capture les journaux ILogger à partir de Startup.cs, Program.cs dans les Applications Asp.Net Core

Avec la nouvelle ApplicationInsightsLoggerProvider, il est possible de capturer des journaux de tôt dans le pipeline de démarrage d’application. Même si ApplicationInsightsLoggerProvider est automatiquement activé Application Insights (à partir de 2.7.0-beta3 et versions ultérieures), il est inutile d’installation de clé d’instrumentation que plus tard dans le pipeline, afin que seuls les journaux à partir du contrôleur / autres classes sont capturées. Pour capturer tous les journaux en commençant par `Program.cs` et `Startup.cs` lui-même, il est nécessaire activer explicitement ApplicationInsightsLoggerProvider avec une clé d’instrumentation. Il est également important de noter que `TelemetryConfiguration` n’enregistre pas les entièrement configurer lorsque quelque chose parmi `Program.cs` ou `Startup.cs` elle-même, alors ces journaux utilisera une configuration minimale strict, qui utilise InMemoryChannel, aucun échantillonnage et aucune télémétrie standard initialiseurs ou processeurs.

Voici des exemples de `Program.cs` et `Startup.cs` à l’aide de cette fonctionnalité.

#### <a name="example-programcs"></a>Exemple Program.cs

```csharp
using Microsoft.AspNetCore;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Logging;

public class Program
{
    public static void Main(string[] args)
    {
        var host = CreateWebHostBuilder(args).Build();
        var logger = host.Services.GetRequiredService<ILogger<Program>>();
        // This will be picked up by AI
        logger.LogInformation("From Program. Running the host now..");
        host.Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureLogging(
        builder =>
            {
            // providing an instrumentation key here is required if you are using
            // standalone package Microsoft.Extensions.Logging.ApplicationInsights
            // or if you want to capture logs from early in the application startup 
            // pipeline from Startup.cs or Program.cs itself.
            builder.AddApplicationInsights("ikey");

            // Adding the filter below to ensure logs of all severity from Program.cs
            // is sent to ApplicationInsights.
            // Replace YourAppName with the namespace of your application's Program.cs
            builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                             ("YourAppName.Program", LogLevel.Trace);
            // Adding the filter below to ensure logs of all severity from Startup.cs
            // is sent to ApplicationInsights.
            // Replace YourAppName with the namespace of your application's Startup.cs
            builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                             ("YourAppName.Startup", LogLevel.Trace);
            }
        );
}
```

#### <a name="example-startupcs"></a>Exemple Startup.cs

```csharp
public class Startup
{
    private readonly `ILogger` _logger;

    public Startup(IConfiguration configuration, ILogger<Startup> logger)
    {
        Configuration = configuration;
        _logger = logger;
    }

    public IConfiguration Configuration { get; }

    // This method gets called by the runtime. Use this method to add services to the container.
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetry();

        // The following will be picked up by Application Insights.
        _logger.LogInformation("Logging from ConfigureServices.");
    }

    // This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        if (env.IsDevelopment())
        {
            // The following will be picked up by Application Insights.
            _logger.LogInformation("Configuring for Development environment");
            app.UseDeveloperExceptionPage();
        }
        else
        {
            // The following will be picked up by Application Insights.
            _logger.LogInformation("Configuring for Production environment");
        }

        app.UseMvc();
    }
}
```

## <a name="migrating-from-old-applicationinsightsloggerprovider"></a>Migration à partir de l’ancien ApplicationInsightsLoggerProvider

Versions du Kit de développement logiciel Microsoft.ApplicationInsights.AspNet avant 2.7.0-beta2, prise en charge un fournisseur de journalisation est désormais obsolète. Ce fournisseur a été activé avec `AddApplicationInsights()` méthode d’extension de `ILoggerFactory`. Ce fournisseur est désormais obsolète, et les utilisateurs sont suggérés pour migrer vers le nouveau fournisseur. Migration implique deux étapes.

1. Supprimez appel ILoggerFactory.AddApplicationInsights() de `Startup.Configure()` méthode afin d’éviter de journalisation double.
2. Réappliquer les règles de filtrage dans le code comme ils ne sont pas respectées par le nouveau fournisseur. Surcharges de ILoggerFactory.AddApplicationInsights() a duré minimale fonctions LogLevel ou filtre. Avec le nouveau fournisseur, le filtrage fait partie de l’infrastructure de journalisation elle-même et ne pas effectuée par le fournisseur d’Application Insights. Par conséquent, tous les filtres fournis par le biais de `ILoggerFactory.AddApplicationInsights()` surcharges doivent être supprimés et les règles de filtrage doivent être fournis suivant [ces](#control-logging-level) obtenir des instructions. Si vous utilisez `appsettings.json` pour filtrer la journalisation, il continuera de fonctionner avec le nouveau fournisseur comme utilisent tous deux le même Alias de fournisseur - **ApplicationInsights**.

Alors que l’ancien fournisseur peut toujours être utilisé (il est désormais obsolète et sera supprimée uniquement dans le changement de version majeure à 3.xx), la migration vers le dernier fournisseur est fortement recommandée pour les raisons suivantes.

1. Fournisseur précédent n’offrait pas de prise en charge de [étendues](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.2#log-scopes). Dans le nouveau fournisseur, les propriétés de portée sont automatiquement ajoutées en tant que propriétés personnalisées pour les données de télémétrie collectées.
2. Journaux peuvent maintenant être capturés beaucoup plus tôt dans le pipeline de démarrage d’application. Par exemple, Les journaux à partir de classes de démarrage et le programme peuvent maintenant être capturés.
3. Avec le nouveau fournisseur, le filtrage est effectué au niveau du framework lui-même. Filtrage des journaux au fournisseur d’Application Insights peut être effectuée dans la même façon que pour d’autres fournisseurs, notamment les fournisseurs intégrés, tels que de la Console, débogage et ainsi de suite. Il est également possible d’appliquer les mêmes filtres à plusieurs fournisseurs.
4. Le [recommandé](https://github.com/aspnet/Announcements/issues/255) dans ASP.NET Core (2.0 et versions ultérieures) pour activer les fournisseurs de journalisation consiste à l’aide de méthodes d’extension sur ILoggingBuilder dans `Program.cs` lui-même.

> [!Note]
> Le nouveau fournisseur est disponible pour les applications ciblant `NETSTANDARD2.0` ou une version ultérieure. Si votre application est ciblage des anciennes versions de .NET Core, comme .NET Core 1.1 ou si vous ciblez le .NET Framework, continuer à utiliser l’ancien fournisseur.

## <a name="console-application"></a>Application de console

Le code suivant montre un exemple d’application Console configuré pour envoyer `ILogger` traces vers Application Insights.

Packages installés :

```xml
<ItemGroup>
  <PackageReference Include="Microsoft.Extensions.DependencyInjection" Version="2.1.0" />  
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

        // Channel is explicitly configured to do flush on it later.
        var channel = new InMemoryChannel();
        services.Configure<TelemetryConfiguration>(
            (config) =>
            {
                config.TelemetryChannel = channel;
            }
        );

        // Add the logging pipelines to use. We are using Application Insights only here.
        services.AddLogging(builder =>
        {
            // Optional: Apply filters to configure LogLevel Trace or above is sent to
            // Application Insights for all categories.
            builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                             ("", LogLevel.Trace);
            builder.AddApplicationInsights("--YourAIKeyHere--");
        });

        // Build ServiceProvider.
        IServiceProvider serviceProvider = services.BuildServiceProvider();

        ILogger<Program> logger = serviceProvider.GetRequiredService<ILogger<Program>>();

        // Begin a new scope. This is optional.
        using (logger.BeginScope(new Dictionary<string, object> { { "Method", nameof(Main) } }))
        {
            logger.LogInformation("Logger is working"); // this will be captured by Application Insights.
        }

        // Explicitly call Flush() followed by sleep is required in Console Apps.
        // This is to ensure that even if application terminates, telemetry is sent to the back-end.
        channel.Flush();
        Thread.Sleep(1000);
    }
}
```

Dans l’exemple ci-dessus, le package autonome `Microsoft.Extensions.Logging.ApplicationInsights` est utilisé. Par défaut, cette configuration utilise le strict minimum `TelemetryConfiguration` pour envoyer des données à Application Insights. Le strict minimum signifie que le canal utilisé sera `InMemoryChannel`, aucun échantillonnage et aucun TelemetryInitializers standard. Ce comportement peut être substitué pour une application console comme indiqué dans l’exemple ci-dessous.

Installez ce package supplémentaire :

```xml
<PackageReference Include="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel" Version="2.9.1" />
```

La section suivante montre comment substituer la valeur par défaut `TelemetryConfiguration` à l’aide de `services.Configure<TelemetryConfiguration>()` (méthode). Cet exemple configure `ServerTelemetryChannel`, l’échantillonnage et ajoute un personnalisé `ITelemetryInitializer` à la `TelemetryConfiguration`.

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

    // Add the logging pipelines to use. We are adding Application Insights only.
    services.AddLogging(loggingBuilder =>
    {
        loggingBuilder.AddApplicationInsights();
    });

    ........
    ........

    // Explicitly call Flush() followed by sleep is required in Console Apps.
    // This is to ensure that even if application terminates, telemetry is sent to the back-end.
    serverChannel.Flush();
    Thread.Sleep(1000);
```

## <a name="control-logging-level"></a>Niveau de journalisation de contrôle

Asp.Net Core `ILogger` infra a un mécanisme intégré pour appliquer [filtrage](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.2#log-filtering) des journaux, ce qui permet aux utilisateurs de contrôler les journaux envoyés à chaque fournisseurs enregistrés, y compris le fournisseur d’Application Insights. Ce filtrage peut être effectué dans la configuration (généralement à l’aide `appsettings.json` fichier) ou dans le code. Cette fonctionnalité est fournie par le framework lui-même et n’est pas spécifique au fournisseur d’Application Insights.

Exemples d’appliquer les règles de filtre à ApplicationInsightsLoggerProvider est donné ci-dessous.

### <a name="create-filter-rules-in-configuration-with-appsettingsjson"></a>Créer des règles de filtre dans la Configuration avec appsettings.json

Pour ApplicationInsightsLoggerProvider, l’alias de fournisseur est `ApplicationInsights`. Le sous la section indique dans `appsettings.json` configure les journaux `Warning` et versions ultérieures à partir de toutes les catégories, `Error` et versions ultérieures à partir des catégories commençant par « Microsoft » à envoyer à `ApplicationInsightsLoggerProvider`.

```json
{
  "Logging": {
    "ApplicationInsights": {
      "LogLevel": {
        "Default": "Warning",
        "Microsoft": "Error"
      }
    },
    "LogLevel": {
      "Default": "Warning"
    }
  },
  "AllowedHosts": "*"
}
```

### <a name="create-filter-rules-in-code"></a>Créer des règles de filtre dans le code

Le code ci-dessous extrait de code configure les journaux `Warning` et versions ultérieures à partir de toutes les catégories, `Error` et versions ultérieures à partir des catégories commençant par « Microsoft » à envoyer à `ApplicationInsightsLoggerProvider`. Cette configuration est identique à la configuration ci-dessus effectuée dans `appsettings.json`.

```csharp
    WebHost.CreateDefaultBuilder(args)
    .UseStartup<Startup>()
    .ConfigureLogging(logging =>
      logging.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                        ("", LogLevel.Warning)
             .AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                        ("Microsoft", LogLevel.Error);
```

## <a name="frequently-asked-questions"></a>Forum Aux Questions (FAQ)

*1. Quelle est la ApplicationInsightsLoggerProvider ancienne et nouvel ?*

* [Kit de développement logiciel Microsoft.ApplicationInsights.AspNet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) livré avec un ApplicationInsightsLoggerProvider intégré (Microsoft.ApplicationInsights.AspNetCore.Logging.ApplicationInsightsLoggerProvider), qui a été activé à l’aide de ILoggerFactory méthodes d’extension. Ce fournisseur est marqué comme obsolète à partir de 2.7.0-beta2 et versions ultérieures et est supprimé complètement dans la prochaine modification de version principale. [Cela](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) package lui-même pas obsolète et est nécessaire pour activer la surveillance des demandes, etc. de dépendances.

* L’alternative suggérée est le nouveau package autonome [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights), contenant une (ApplicationInsightsLoggerProvider améliorée Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider) et les méthodes d’extension sur ILoggerBuilder pour l’activer.

* [Kit de développement logiciel Microsoft.ApplicationInsights.AspNet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) version 2.7.0-beta3 et versions ultérieures prendront une dépendance sur le package ci-dessus et permet de `ILogger` capturer automatiquement.

*2. Je ne vois certaines `ILogger` journaux figurent deux fois dans Application Insights ?*

* Cette duplication est possible si vous avez l’ancienne version (obsolète) de `ApplicationInsightsLoggerProvider` activé en appelant `AddApplicationInsights` sur `ILoggerFactory`. Vérifiez si votre `Configure` méthode suivantes et le supprimer.

   ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
    {
        loggerFactory.AddApplicationInsights(app.ApplicationServices, LogLevel.Warning);
        // ..other code.
    }
   ```

* Si vous rencontrez une journalisation double lors du débogage à partir de Visual Studio, puis modifiez le code utilisé pour activer Application Insights comme suit, en définissant `EnableDebugLogger` avoir la valeur false. Ce problème de duplication et d’un correctif est pertinente uniquement lorsque le débogage de l’application.

   ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        ApplicationInsightsServiceOptions options = new ApplicationInsightsServiceOptions();
        options.EnableDebugLogger = false;
        services.AddApplicationInsightsTelemetry(options);
        // ..other code.
    }
   ```

*3. J’ai mis à jour vers [Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) 2.7.0-beta3 de version et je vois maintenant qui se connecte à partir de `ILogger` sont automatiquement capturés. Comment puis-je désactiver cette fonctionnalité complètement ?*

* Consultez [cela](../../azure-monitor/app/ilogger.md#control-logging-level) section pour savoir comment filtrer les journaux en général. Pour désactiver ApplicationInsightsLoggerProvider utiliser `LogLevel.None` pour celui-ci.

  Dans le code

    ```csharp
        builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                          ("", LogLevel.None);
    ```

  Dans la configuration

    ```json
    {
      "Logging": {
        "ApplicationInsights": {
          "LogLevel": {
            "Default": "None"
          }
    }
    ```

*4. Je ne vois certaines `ILogger` journaux ne sont pas avoir les mêmes propriétés que d’autres ?*

* Application des captures des Insights et envoie `ILogger` se connecte à l’aide de la même `TelemetryConfiguration` utilisé pour toutes les autres données de télémétrie. Il existe une exception à cette règle. La valeur par défaut `TelemetryConfiguration` n’enregistre pas les entièrement configurer lorsque quelque chose parmi `Program.cs` ou `Startup.cs` elle-même, alors les journaux à partir de ces emplacements n’aura pas la configuration par défaut et par conséquent, ne seront pas exécuté toutes les `TelemetryInitializer`s et `TelemetryProcessor`s.

*5. J’utilise le package autonome Microsoft.Extensions.Logging.ApplicationInsights, et je souhaite ouvrir une session manuellement des données de télémétrie personnalisées supplémentaires. Comment dois-je procéder ?*

* Lorsque vous utilisez le package autonome, `TelemetryClient` n’est pas injecté au conteneur d’injection de dépendance, donc les utilisateurs sont susceptibles de créer une nouvelle instance de `TelemetryClient` à l’aide de la même configuration que celles utilisées par le fournisseur de l’enregistreur d’événements, comme indiqué ci-dessous. Cela garantit que la même configuration est utilisée pour toutes les données de télémétrie personnalisées, ainsi que celles capturées à partir de ILogger.

```csharp
public class MyController : ApiController
{
   // This telemtryclient can be used to track additional telemetry using TrackXXX() api.
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
> Veuillez noter que, si le package Microsoft.ApplicationInsights.AspNetCore package est utilisé pour activer Application Insights, puis l’exemple ci-dessus doit être modifié pour obtenir `TelemetryClient` directement dans le constructeur. Consultez [cela](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core-no-visualstudio#frequently-asked-questions) pour un exemple complet.


*6. Quel type de données de télémétrie Application Insights est généré à partir de `ILogger` journaux ? ou où puis-je voir `ILogger` journaux dans Application Insights ?*

* Capture ApplicationInsightsLoggerProvider `ILogger` se connecte et crée `TraceTelemetry` à partir de celui-ci. Si un objet d’Exception est passé à la méthode Log() sur ILogger, puis à la place de `TraceTelemetry`, un `ExceptionTelemetry` est créé. Vous trouverez ces éléments de télémétrie dans les mêmes emplacements que n’importe quel autre `TraceTelemetry` ou `ExceptionTelemetry` pour Application Insights, y compris le portail, analytique ou débogueur local de Visual Studio.
Si vous préférez toujours envoyer `TraceTelemetry`, puis utilisez l’extrait de code ```builder.AddApplicationInsights((opt) => opt.TrackExceptionsAsExceptionTelemetry = false);```.

*7. Je n’ai pas SDK installé, et j’utilise Extension d’application Web Azure pour activer Application Insights pour mes applications Asp.Net Core. Comment utiliser le nouveau fournisseur ?*

* Extension application Insights dans Azure Web App utilise l’ancien fournisseur. Règles de filtrage peuvent être modifiés dans `appsettings.json` pour votre application. Si vous souhaitez tirer parti du nouveau fournisseur, utilisez instrumentation du moment de la génération en tirant la dépendance de nuget sur le Kit de développement. Ce document sera mis à jour lors de l’extension change pour utiliser le nouveau fournisseur.

*8. Je suis à l’aide d’un package autonome Microsoft.Extensions.Logging.ApplicationInsights et l’activation de fournisseur d’Application Insights par le Générateur de l’appelant. AddApplicationInsights("ikey"). Existe-t-il une option pour obtenir la clé d’instrumentation à partir de la configuration ?*


* Modifier `Program.cs` et `appsettings.json` comme indiqué ci-dessous.

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .UseStartup<Startup>()
            .ConfigureLogging((hostingContext, logging) =>
            {
                // hostingContext.HostingEnvironment can be used to determine environments as well.
                var appInsightKey = hostingContext.Configuration["myikeyfromconfig"];
                logging.AddApplicationInsights(appInsightKey);
            });
}
```

Section appropriée à partir de `appsettings.json`

```json
{
  "myikeyfromconfig": "putrealikeyhere"
}
```

Le code ci-dessus est requis uniquement lorsque vous utilisez le fournisseur de journalisation autonome. Pour l’analyse régulière des Application Insights, clé d’instrumentation est automatiquement chargée à partir du chemin de configuration `ApplicationInsights:Instrumentationkey` et `appsettings.json` doit se présenter comme ci-dessous.

```json
{
  "ApplicationInsights":
    {
        "Instrumentationkey":"putrealikeyhere"
    }
}
```

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus :

* [Journalisation dans Asp.Net Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging)
* [Journaux de suivi de .NET dans Application Insights](../../azure-monitor/app/asp-net-trace-logs.md)
