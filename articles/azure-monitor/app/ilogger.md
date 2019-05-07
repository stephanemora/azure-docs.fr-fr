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
ms.openlocfilehash: bd010193bf8f001d027ae80be9272ae30a0171c9
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65149986"
---
# <a name="applicationinsightsloggerprovider-for-net-core-ilogger-logs"></a>ApplicationInsightsLoggerProvider pour les journaux de .NET Core ILogger

ASP.NET Core prend en charge une API de journalisation qui fonctionne avec différents types de fournisseurs de journalisation intégrés et tiers. La journalisation est effectuée en appelant **Log()** ou une variante de celui-ci sur *ILogger* instances. Cet article montre comment utiliser *ApplicationInsightsLoggerProvider* pour capturer les journaux ILogger dans les applications console et ASP.NET Core. Cet article décrit également comment ApplicationInsightsLoggerProvider s’intègre avec d’autres données de télémétrie Application Insights.
Pour plus d’informations, consultez [Journalisation dans ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging).

## <a name="aspnet-core-applications"></a>Applications ASP.NET Core

ApplicationInsightsLoggerProvider est activé par défaut dans [Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) version 2.7.0-beta3 (et versions ultérieures) lorsque vous activez un contrôle régulier Application Insights par le biais de la norme méthodes :
- En appelant le **UseApplicationInsights** méthode d’extension sur IWebHostBuilder 
- En appelant le **AddApplicationInsightsTelemetry** méthode d’extension sur IServiceCollection

Les journaux ILogger qui capture ApplicationInsightsLoggerProvider sont soumis à la même configuration que les autres données de télémétrie collectées. Ils ont le même ensemble de TelemetryInitializers et de TelemetryProcessors, utilisent le même TelemetryChannel, sont corrélées et échantillonnées dans la même façon que d’autres données de télémétrie. Si vous utilisez 2.7.0-beta3 de version ou une version ultérieure, aucune action n’est nécessaire pour capturer les journaux ILogger.

Uniquement *avertissement* ou les journaux ILogger plus élevées (à partir de toutes les catégories) sont envoyées à Application Insights par défaut. Toutefois, vous pouvez [appliquer des filtres pour modifier ce comportement](#control-logging-level). Étapes supplémentaires sont nécessaires pour capturer les journaux ILogger à partir de **Program.cs** ou **Startup.cs**. (Consultez [ILogger capture des journaux à partir de Startup.cs et Program.cs dans les applications ASP.NET Core](#capture-ilogger-logs-from-startupcs-and-programcs-in-aspnet-core-apps).)

Si vous utilisez une version antérieure du Kit de développement logiciel Microsoft.ApplicationInsights.AspNet ou vous souhaitez simplement utiliser ApplicationInsightsLoggerProvider sans aucune autre Application Insights fonctionnalité d’analyse, procédez comme suit :

1. Installez le package NuGet :

   ```xml
       <ItemGroup>
         <PackageReference Include="Microsoft.Extensions.Logging.ApplicationInsights" Version="2.9.1" />  
       </ItemGroup>
   ```

1. Modifier **Program.cs** comme indiqué ici :

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
                   // Providing an instrumentation key here is required if you're using
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

Le code à l’étape 2 configure `ApplicationInsightsLoggerProvider`. Le code suivant montre un exemple de classe de contrôleur qui utilise `ILogger` pour envoyer des journaux. Les journaux sont capturés par Application Insights.

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

### <a name="capture-ilogger-logs-from-startupcs-and-programcs-in-aspnet-core-apps"></a>Capturer les journaux ILogger à partir de Startup.cs et Program.cs dans les applications ASP.NET Core

La nouvelle ApplicationInsightsLoggerProvider pouvez capturer des journaux à partir de tôt dans le pipeline de démarrage de l’application. Bien que ApplicationInsightsLoggerProvider est automatiquement activée dans Application Insights (en commençant par la version 2.7.0-beta3), il n’a pas une clé d’instrumentation définie tant ultérieurement dans le pipeline. Par conséquent, les journaux depuis **contrôleur**/ autres classes sont capturées. Pour chaque journal, en commençant par capture **Program.cs** et **Startup.cs** lui-même, vous devez activer explicitement une clé d’instrumentation pour ApplicationInsightsLoggerProvider. En outre, *TelemetryConfiguration* n’est pas entièrement défini lorsque vous vous connectez à partir de **Program.cs** ou **Startup.cs** lui-même. Par conséquent, ces journaux aura une configuration minimale qui n’utilise InMemoryChannel, aucun échantillonnage et aucune des initialiseurs de télémétrie standard ou les processeurs.

Les exemples suivants illustrent cette fonctionnalité avec **Program.cs** et **Startup.cs**.

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
            // Providing an instrumentation key here is required if you're using
            // standalone package Microsoft.Extensions.Logging.ApplicationInsights
            // or if you want to capture logs from early in the application startup 
            // pipeline from Startup.cs or Program.cs itself.
            builder.AddApplicationInsights("ikey");

            // Adding the filter below to ensure logs of all severity from Program.cs
            // is sent to ApplicationInsights.
            // Replace YourAppName with the namespace of your application's Program.cs.
            builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                             ("YourAppName.Program", LogLevel.Trace);
            // Adding the filter below to ensure logs of all severity from Startup.cs
            // is sent to ApplicationInsights.
            // Replace YourAppName with the namespace of your application's Startup.cs.
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

## <a name="migrate-from-the-old-applicationinsightsloggerprovider"></a>Migrer à partir de l’ancien ApplicationInsightsLoggerProvider

Versions de kit de développement logiciel Microsoft.ApplicationInsights.AspNet avant 2.7.0-beta2 pris en charge un fournisseur de journalisation est maintenant obsolète. Ce fournisseur a été activé par le biais du **AddApplicationInsights()** méthode d’extension de ILoggerFactory. Nous vous recommandons de migrer vers le nouveau fournisseur, ce qui implique deux étapes :

1. Supprimer le *ILoggerFactory.AddApplicationInsights()* appeler à partir de la **Startup.Configure()** méthode afin d’éviter de journalisation double.
2. Réappliquer les règles de filtrage dans le code, car ils ne sont pas respectées par le nouveau fournisseur. Les surcharges de *ILoggerFactory.AddApplicationInsights()* a duré minimale fonctions LogLevel ou filtre. Avec le nouveau fournisseur, le filtrage fait partie de l’infrastructure de journalisation proprement dit. Il n’est pas effectuée par le fournisseur d’Application Insights. Par conséquent, tous les filtres qui sont fournies via *ILoggerFactory.AddApplicationInsights()* surcharges doivent être supprimés. Et les règles de filtrage doivent être fournie en suivant le [contrôler le niveau de journalisation](#control-logging-level) obtenir des instructions. Si vous utilisez *appsettings.json* pour filtrer la journalisation, il continue de fonctionner avec le nouveau fournisseur, étant donné que les deux utilisent le même alias de fournisseur, *ApplicationInsights*.

Vous pouvez toujours utiliser l’ancien fournisseur. (Elle sera supprimée uniquement dans un changement de version principale à 3. *xx*.) Mais nous vous recommandons de migrer vers le nouveau fournisseur pour les raisons suivantes :

- Le fournisseur précédent ne dispose pas de prise en charge de [connecter étendues](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.2#log-scopes). Dans le nouveau fournisseur, les propriétés de portée sont automatiquement ajoutées en tant que propriétés personnalisées pour les données de télémétrie collectées.
- Journaux peuvent maintenant être capturés beaucoup plus tôt dans le pipeline de démarrage d’application. Se connecte à partir de la **programme** et **démarrage** classes peuvent maintenant être capturés.
- Avec le nouveau fournisseur, le filtrage est effectué au niveau du framework lui-même. Vous pouvez filtrer les journaux pour le fournisseur d’Application Insights dans la même manière que pour d’autres fournisseurs, notamment les fournisseurs intégrés, tels que de la Console, débogage et ainsi de suite. Vous pouvez également appliquer les mêmes filtres à plusieurs fournisseurs.
- Dans ASP.NET Core (2.0 et versions ultérieures), la méthode recommandée pour [activer les fournisseurs de journalisation](https://github.com/aspnet/Announcements/issues/255) est à l’aide de méthodes d’extension sur ILoggingBuilder dans **Program.cs** lui-même.

> [!Note]
> Le nouveau fournisseur est disponible pour les applications qui ciblent NETSTANDARD2.0 ou version ultérieure. Si votre application cible des versions antérieures de .NET Core, telles que .NET Core 1.1, ou si elle cible le .NET Framework, continuer à utiliser l’ancien fournisseur.

## <a name="console-application"></a>Application de console

Le code suivant montre un exemple d’application console qui est configuré pour envoyer des traces de ILogger à Application Insights.

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
        // Create the DI container.
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

Cet exemple utilise le package autonome `Microsoft.Extensions.Logging.ApplicationInsights`. Par défaut, cette configuration utilise le « minimum » TelemetryConfiguration pour envoyer des données à Application Insights. Strict minimum signifie que InMemoryChannel est le canal est utilisé. Il n’existe aucune TelemetryInitializers d’échantillonnage et non standard. Ce comportement peut être substitué pour une application de console, comme le montre l’exemple suivant.

Installez ce package supplémentaire :

```xml
<PackageReference Include="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel" Version="2.9.1" />
```

La section suivante montre comment remplacer la valeur par défaut TelemetryConfiguration en utilisant le **services. Configurer<TelemetryConfiguration>()** (méthode). Cet exemple configure `ServerTelemetryChannel` et d’échantillonnage. Il ajoute un ITelemetryInitializer personnalisé à la TelemetryConfiguration.

```csharp
    // Create the DI container.
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

    // Explicitly calling Flush() followed by sleep is required in Console Apps.
    // This is to ensure that even if the application terminates, telemetry is sent to the back end.
    serverChannel.Flush();
    Thread.Sleep(1000);
```

## <a name="control-logging-level"></a>Niveau de journalisation de contrôle

ASP.NET Core *ILogger* infra a un mécanisme intégré pour appliquer [filtrage de journal](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.2#log-filtering). Cela vous permet de contrôler les journaux qui sont envoyés à chaque fournisseur inscrit, y compris le fournisseur d’Application Insights. Le filtrage peut être effectué dans la configuration (généralement en utilisant un *appsettings.json* fichier) ou dans le code. Cette fonctionnalité est fournie par le framework lui-même. Il n’est pas spécifique au fournisseur d’Application Insights.

Les exemples suivants s’appliquent les règles de filtre à ApplicationInsightsLoggerProvider.

### <a name="create-filter-rules-in-configuration-with-appsettingsjson"></a>Créer des règles de filtre dans la configuration avec appsettings.json

Pour ApplicationInsightsLoggerProvider, l’alias de fournisseur est `ApplicationInsights`. La section suivante de *appsettings.json* configure les journaux pour *avertissement* et versions ultérieures de toutes les catégories et *erreur* et versions ultérieures à partir des catégories qui commencent par » Microsoft » à envoyer à `ApplicationInsightsLoggerProvider`.

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

L’extrait de code suivant configure les journaux pour *avertissement* et versions ultérieures de toutes les catégories et pour *erreur* et versions ultérieures à partir des catégories qui commencent par « Microsoft » à envoyer à `ApplicationInsightsLoggerProvider`. Cette configuration est le même que dans la section précédente dans *appsettings.json*.

```csharp
    WebHost.CreateDefaultBuilder(args)
    .UseStartup<Startup>()
    .ConfigureLogging(logging =>
      logging.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                        ("", LogLevel.Warning)
             .AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                        ("Microsoft", LogLevel.Error);
```

## <a name="frequently-asked-questions"></a>Questions fréquentes (FAQ)

### <a name="what-are-the-old-and-new-versions-of-applicationinsightsloggerprovider"></a>Quelles sont les versions anciennes et nouvelles de ApplicationInsightsLoggerProvider ?

[Kit de développement logiciel Microsoft.ApplicationInsights.AspNet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) inclus un ApplicationInsightsLoggerProvider intégré (Microsoft.ApplicationInsights.AspNetCore.Logging.ApplicationInsightsLoggerProvider), qui a été activée par le biais  **ILoggerFactory** méthodes d’extension. Ce fournisseur est marqué comme obsolète à partir de la version 2.7.0-beta2. Il sera complètement supprimée dans la prochaine modification de version principale. Le [Microsoft.ApplicationInsights.AspNetCore 2.6.1](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) package lui-même n’est pas obsolète. Il est obligatoire pour activer la surveillance des demandes, dépendances et ainsi de suite.

L’alternative suggérée est le nouveau package autonome [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights), qui contient une (ApplicationInsightsLoggerProvider améliorée Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider) et les méthodes d’extension sur ILoggerBuilder pour l’activer.

[Kit de développement logiciel Microsoft.ApplicationInsights.AspNet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) version 2.7.0-beta3 prennent une dépendance sur le nouveau package et permet la capture de ILogger automatiquement.

### <a name="why-are-some-ilogger-logs-shown-twice-in-application-insights"></a>Pourquoi certains journaux ILogger figurent deux fois dans Application Insights ?

Duplication peut se produire si vous avez l’ancienne version (obsolète) de ApplicationInsightsLoggerProvider activé en appelant `AddApplicationInsights` sur `ILoggerFactory`. Vérifiez si votre **configurer** méthode suivantes et le supprimer :

```csharp
 public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
 {
     loggerFactory.AddApplicationInsights(app.ApplicationServices, LogLevel.Warning);
     // ..other code.
 }
```

Si vous rencontrez double journalisation lorsque vous déboguez à partir de Visual Studio, définissez `EnableDebugLogger` à *false* dans le code qui permet à Application Insights, comme suit. Cette duplication et le correctif est pertinente uniquement lorsque vous déboguez l’application.

```csharp
 public void ConfigureServices(IServiceCollection services)
 {
     ApplicationInsightsServiceOptions options = new ApplicationInsightsServiceOptions();
     options.EnableDebugLogger = false;
     services.AddApplicationInsightsTelemetry(options);
     // ..other code.
 }
```

### <a name="i-updated-to-microsoftapplicationinsightsaspnet-sdkhttpswwwnugetorgpackagesmicrosoftapplicationinsightsaspnetcore-version-270-beta3-and-logs-from-ilogger-are-captured-automatically-how-do-i-turn-off-this-feature-completely"></a>J’ai mis à jour vers [Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) version 2.7.0-beta3 et journaux à partir de ILogger sont automatiquement capturés. Comment activer cette fonctionnalité complètement ?

Consultez le [contrôler le niveau de journalisation](../../azure-monitor/app/ilogger.md#control-logging-level) section pour apprendre à filtrer les journaux en général. Pour désactiver ApplicationInsightsLoggerProvider, utilisez `LogLevel.None`:

**Dans le code :**

```csharp
    builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                      ("", LogLevel.None);
```

**Dans la configuration :**

```json
{
  "Logging": {
    "ApplicationInsights": {
      "LogLevel": {
        "Default": "None"
      }
}
```

### <a name="why-do-some-ilogger-logs-not-have-the-same-properties-as-others"></a>Pourquoi certains journaux ILogger inutile les mêmes propriétés que d’autres ?

Application Insights capture et envoie les journaux ILogger à l’aide de la même TelemetryConfiguration qui est utilisée pour toutes les autres données de télémétrie. Mais il existe une exception. Par défaut, TelemetryConfiguration n'est pas entièrement configurée lorsque vous vous connectez à partir de **Program.cs** ou **Startup.cs**. Les journaux à partir de ces emplacements n’auront pas la configuration par défaut, afin qu’ils ne sont pas être en cours d’exécution TelemetryInitializers et TelemetryProcessors.

### <a name="im-using-the-standalone-package-microsoftextensionsloggingapplicationinsights-and-i-want-to-log-some-additional-custom-telemetry-manually-how-should-i-do-that"></a>J’utilise le package autonome Microsoft.Extensions.Logging.ApplicationInsights, et je souhaite ouvrir une session manuellement des données de télémétrie personnalisées supplémentaires. Comment dois-je procéder ?

Lorsque vous utilisez le package autonome, `TelemetryClient` n’est pas injectés dans le conteneur d’injection de dépendance, donc vous devez créer une nouvelle instance de `TelemetryClient` et utilisent la même configuration de l’enregistreur d’événements fournisseur qui utilise, comme le montre le code suivant. Cela garantit que la même configuration est utilisée pour toutes les données de télémétrie personnalisées ainsi que les données de télémétrie de ILogger.

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
> Si vous utilisez le package Microsoft.ApplicationInsights.AspNetCore pour activer Application Insights, modifier ce code pour obtenir `TelemetryClient` directement dans le constructeur. Pour obtenir un exemple, consultez [ce forum aux questions](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core-no-visualstudio#frequently-asked-questions).


### <a name="what-application-insights-telemetry-type-is-produced-from-ilogger-logs-or-where-can-i-see-ilogger-logs-in-application-insights"></a>Quel type de données de télémétrie Application Insights est généré à partir des journaux de ILogger ? Ou, où puis-je voir Qu'ilogger journaux dans Application Insights ?

ApplicationInsightsLoggerProvider capture les journaux ILogger et crée TraceTelemetry à partir de celles-ci. Si un objet d’Exception est passé à la **Log()** méthode sur ILogger, *ExceptionTelemetry* est créé au lieu de TraceTelemetry. Vous trouverez des ces éléments de télémétrie dans les mêmes emplacements que tout autre TraceTelemetry ou ExceptionTelemetry pour Application Insights, y compris le portail, analytique ou débogueur local de Visual Studio.

Si vous préférez toujours envoyer TraceTelemetry, utilisez cet extrait de code : ```builder.AddApplicationInsights((opt) => opt.TrackExceptionsAsExceptionTelemetry = false);```

### <a name="i-dont-have-the-sdk-installed-and-i-use-the-azure-web-apps-extension-to-enable-application-insights-for-my-aspnet-core-applications-how-do-i-use-the-new-provider"></a>Je n’ai pas le Kit de développement logiciel installé, et j’utilise l’extension Azure Web Apps pour activer Application Insights pour mes applications ASP.NET Core. Comment utiliser le nouveau fournisseur ? 

L’extension Application Insights dans Azure Web Apps utilise l’ancien fournisseur. Vous pouvez modifier les règles de filtrage dans le *appsettings.json* fichier de votre application. Pour tirer parti du nouveau fournisseur, utilisez l’instrumentation de moment de la génération en prenant une dépendance NuGet dans le Kit de développement. Cet article sera mis à jour lors de l’extension change pour utiliser le nouveau fournisseur.

### <a name="im-using-the-standalone-package-microsoftextensionsloggingapplicationinsights-and-enabling-application-insights-provider-by-calling-builderaddapplicationinsightsikey-is-there-an-option-to-get-an-instrumentation-key-from-configuration"></a>Je suis à l’aide d’un package autonome Microsoft.Extensions.Logging.ApplicationInsights et l’activation du fournisseur d’Application Insights en appelant **générateur. AddApplicationInsights("ikey")**. Existe-t-il une option pour obtenir une clé d’instrumentation à partir de la configuration ?


Modifiez Program.cs et appsettings.json comme suit :

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

   La section appropriée à partir de `appsettings.json`:

   ```json
   {
     "myikeyfromconfig": "putrealikeyhere"
   }
   ```

Ce code est requis uniquement lorsque vous utilisez un fournisseur de journalisation autonome. Pour l’analyse régulière des Application Insights, la clé d’instrumentation est automatiquement chargée à partir du chemin de configuration *ApplicationInsights : Clé d’instrumentation*. AppSettings.JSON doit ressembler à ceci :

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

* [Journalisation dans ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging)
* [Journaux de suivi de .NET dans Application Insights](../../azure-monitor/app/asp-net-trace-logs.md)
