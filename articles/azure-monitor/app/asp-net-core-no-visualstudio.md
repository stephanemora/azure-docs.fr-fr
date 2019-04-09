---
title: Azure Application Insights pour ASP.NET Core sans Visual Studio | Microsoft Docs
description: Superviser la disponibilité, les performances et l’utilisation des applications web ASP.NET Core.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 3b722e47-38bd-4667-9ba4-65b7006c074c
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: cithomas
ms.openlocfilehash: 8243523887ec9861459b2d196126237cf89bad97
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/08/2019
ms.locfileid: "59288365"
---
# <a name="application-insights-for-aspnet-core-applications"></a>Application Insights pour les applications ASP.NET Core

Cet article décrit les étapes de l’activation d’Application Insights pour un [ASP.NET Core](https://docs.microsoft.com/aspnet/core/?view=aspnetcore-2.2) application sans utiliser l’IDE Visual Studio. Si vous avez l’IDE de Visual Studio installé, puis [cela](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) document contient des instructions spécifiques à Visual Studio. En suivant les instructions dans cet article, Application Insights va commencer à collecter les demandes, dépendances, exceptions, les compteurs de performances, pulsations et journaux à partir de votre application ASP.NET Core. L’exemple d’application utilisé est un [Application MVC](https://docs.microsoft.com/aspnet/core/tutorials/first-mvc-app/?view=aspnetcore-2.2) ciblant `netcoreapp2.2`, mais les instructions fournies ici sont applicables à toutes les Applications ASP.NET Core. Toutes les exceptions sont indiquées le cas échéant.

## <a name="supported-scenarios"></a>Scénarios pris en charge

Application Insights SDK (Kit de développement logiciel) pour ASP.NET Core peut surveiller vos applications, quel que soit l’emplacement ou le mode d’exécution de l’application. Si votre application est en cours d’exécution et dispose d’une connectivité réseau au service Application Insights, données de télémétrie sont censée être collectées. Cette prise en charge inclut, mais n’est pas limité à n’importe quel système d’exploitation (Windows, Linux, Mac), la méthode d’hébergement (Visual Studio dans le processus out-of-process), la méthode de déploiement dépendant du framework vs (autonomes), serveur Web (IIS, Kestrel), plateforme (Azure Web Apps, machine virtuelle Azure, Docker, AKS et ainsi de suite.) ou IDE (Visual Studio, Visual Studio Code, de ligne de commande).

## <a name="prerequisites"></a>Conditions préalables

- Une Application ASP.NET Core fonctionne. Suivez [cela](https://docs.microsoft.com/aspnet/core/getting-started/) guide pour créer une Application ASP.NET Core, si nécessaire.
- Une Application Insights instrumentation clé valide, ce qui est nécessaire pour envoyer les données de télémétrie au service Application Insights. Suivez [ces](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource) instructions pour créer une nouvelle ressource Application Insights, si nécessaire et obtenir une clé d’instrumentation.

## <a name="enabling-application-insights-server-side-telemetry"></a>L’activation de télémétrie Application Insights côté serveur

1. Installez le SDK Application Insights pour ASP.NET Core, qui est un package NuGet standard. Il est recommandé de toujours utiliser la dernière version stable. Certaines des fonctionnalités décrites dans cet article ne peuvent pas être disponible dans les versions antérieures. Notes de publication de version complète pour le SDK [ici](https://github.com/Microsoft/ApplicationInsights-aspnetcore/releases). 

Voici les modifications à ajouter au fichier de votre projet .csproj.

```xml
    <ItemGroup>
      <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.6.1" />
    </ItemGroup>
```

2. Ajouter `services.AddApplicationInsightsTelemetry();` à `ConfigureServices()` méthode dans votre `Startup` classe. Exemple complet ci-dessous.

```csharp
    // This method gets called by the runtime. Use this method to add services to the container.
    public void ConfigureServices(IServiceCollection services)
    {
        // The following line enables Application Insights telemetry collection.
        services.AddApplicationInsightsTelemetry();

        // code adding other services for your application
        services.AddMvc();
    }
```

3. Clé d’instrumentation le programme d’installation.

   Bien qu’il soit possible de fournir la clé d’instrumentation en tant qu’argument à `services.AddApplicationInsightsTelemetry("putinstrumentationkeyhere");`, il est recommandé de spécifier la clé d’instrumentation dans la configuration. L’exemple suivant montre comment spécifier une clé d’instrumentation dans `appsettings.json`. Assurez-vous que `appsettings.json` est copié dans le dossier racine d’application lors de la publication.

```json
    {
      "ApplicationInsights": {
        "InstrumentationKey": "putinstrumentationkeyhere"
      },
      "Logging": {
        "LogLevel": {
          "Default": "Warning"
        }
      }
    }
```

    Alternately, the instrumentation key can also be specified in either of the following environment variables.
    APPINSIGHTS_INSTRUMENTATIONKEY
    ApplicationInsights:InstrumentationKey

    Example:
    `SET ApplicationInsights:InstrumentationKey=putinstrumentationkeyhere`

`APPINSIGHTS_INSTRUMENTATIONKEY` est généralement utilisé pour spécifier la clé d’instrumentation pour les applications déployées dans Azure Web Apps.

> [!NOTE]
> Une clé d’instrumentation spécifiée dans wins de code sur la variable d’environnement `APPINSIGHTS_INSTRUMENTATIONKEY`, qui prendra le pas sur les autres options.

4. Exécutez votre application et effectuer des demandes à ce dernier. Données de télémétrie devraient maintenant commencer à circuler à Application Insights. Les données de télémétrie suivantes sont automatiquement collectées par le SDK Application Insights.

    1. Demandes - web d’entrant demande à votre application.
    1. Les dépendances
        1. Appels HTTP/Https effectués avec `HttpClient`
        1. Appels SQL effectués avec `SqlClient`
        1. Les appels de stockage Azure effectués avec [Client de stockage Azure](https://www.nuget.org/packages/WindowsAzure.Storage/)*
        1. [SDK de Client EventHub](https://www.nuget.org/packages/Microsoft.Azure.EventHubs) version 1.1.0 et versions ultérieures
        1. [SDK de Client ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus) version 3.0.0 et versions ultérieures

             * Azure Cosmos DB est suivi automatiquement uniquement si HTTP/HTTPS est utilisé. Le mode TCP ne sera pas capturé par Application Insights.


    1. [Compteurs de performance](https://azure.microsoft.com/documentation/articles/app-insights-web-monitor-performance/)
        1. Prise en charge des compteurs de performances dans ASP.NET Core est limitée à ce qui suit
            1. Kit de développement logiciel version 2.4.1 et au-dessus de collecte des compteurs de performances si l’application s’exécute dans Azure Web App (Windows)
            1. Kit de développement logiciel version 2.7.0-beta3 et au-dessus de collecte des compteurs de performances si l’application est en cours d’exécution dans Windows et de ciblage `NETSTANDARD2.0` ou une version ultérieure.
            1. Pour les applications ciblant le .NET Framework complet, les compteurs de performance sont prises en charge dans toutes les versions du Kit de développement logiciel.

            Ce document sera mis à jour lors de l’ajout de la prise en charge du compteur de performance dans Linux.
    1. [Métriques temps réel](https://docs.microsoft.com/azure/application-insights/app-insights-live-stream)
    1. `ILogger` journaux de gravité `Warning` ou version ultérieure sont automatiquement capturées à partir du Kit de développement logiciel version 2.7.0-beta3 ou une version ultérieure. En savoir plus [ici](https://docs.microsoft.com/azure/azure-monitor/app/ilogger).

Il peut prendre quelques minutes pour la télémétrie commencer à apparaître dans le portail. Pour vérifier rapidement si tout fonctionne, il est préférable d’utiliser [métriques temps réel](https://docs.microsoft.com/azure/application-insights/app-insights-live-stream), tandis que la fabrication des demandes à l’application en cours d’exécution.

## <a name="send-ilogger-logs-to-application-insights"></a>Envoyer les journaux ILogger à Application Insights

Application Insights prend en charge la capture de journaux envoyés via ILogger. Lisez la documentation complète [ici](https://docs.microsoft.com/azure/azure-monitor/app/ilogger).

## <a name="enable-client-side-telemetry-for-web-applications"></a>Activer la télémétrie côté Client pour les Applications Web

Les étapes ci-dessus sont suffisantes pour commencer à collecter des données de télémétrie côté serveur. Si votre application comporte des composants côté client, puis suivez les étapes ci-dessous pour commencer à collecter [télémétrie d’utilisation](https://docs.microsoft.com/azure/azure-monitor/app/usage-overview) à partir de là.

1. Dans _ViewImports.cshtml, ajoutez l’injection :

```cshtml
    @inject Microsoft.ApplicationInsights.AspNetCore.JavaScriptSnippet JavaScriptSnippet
```

2. Dans _Layout.cshtml, insérez HtmlHelper à la fin de `<head>` section mais avant tout autre script. Les données de télémétrie JavaScript personnalisée, que vous souhaitez signaler à partir de la page doivent être injectée après cet extrait de code :

```cshtml
    @Html.Raw(JavaScriptSnippet.FullScript)
    </head>
```

Modifiez les noms de fichiers en fonction de votre application réelle. Les noms ci-dessus sont à partir d’un modèle d’Application MVC par défaut.

## <a name="configuring-application-insights-sdk"></a>Configuration d’Application Insights SDK

Le SDK application Insights pour ASP.NET Core peut être personnalisé pour modifier la configuration par défaut. Les utilisateurs de SDK Application Insights ASP.NET peuvent vous être familiers avec la configuration à l’aide `ApplicationInsights.config`, ou en modifiant `TelemetryConfiguration.Active`. Pour ASP.NET Core, la configuration s’effectue différemment. Kit de développement ASP.NET Core est ajouté à l’application à l’aide intégrée d’ASP.NET Core [DependencyInjection](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection) mécanisme et la configuration de la même également utilisaient DependencyInjection. Presque toutes les modifications de configuration sont effectuées dans le `ConfigureServices()` méthode de votre `Startup.cs` classe, sauf indication contraire. Suivez les sections ci-dessous pour en savoir plus.

> [!NOTE]
> Il est important de noter que la modification de la configuration en modifiant `TelemetryConfiguration.Active` n’est pas recommandé dans les applications ASP.NET Core.

### <a name="configuring-using-applicationinsightsserviceoptions"></a>Configuration à l’aide de ApplicationInsightsServiceOptions

Il est possible de modifier quelques paramètres communs en passant `ApplicationInsightsServiceOptions` à `services.AddApplicationInsightsTelemetry();`. Voici un exemple.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        Microsoft.ApplicationInsights.AspNetCore.Extensions.ApplicationInsightsServiceOptions aiOptions
                    = new Microsoft.ApplicationInsights.AspNetCore.Extensions.ApplicationInsightsServiceOptions();
        // Disables adaptive sampling.
        aiOptions.EnableAdaptiveSampling = false;

        // Disables QuickPulse (Live Metrics stream).
        aiOptions.EnableQuickPulseMetricStream = false;
        services.AddApplicationInsightsTelemetry(aiOptions);
    }
```

La liste exacte des paramètres configurables dans `ApplicationInsightsServiceOptions` trouverez [ici](https://github.com/Microsoft/ApplicationInsights-aspnetcore/blob/f0b8631e482d25982eb52092103b34e3ff6e5fef/src/Microsoft.ApplicationInsights.AspNetCore/Extensions/ApplicationInsightsServiceOptions.cs).

### <a name="sampling"></a>échantillonnage

Le SDK application Insights pour ASP.NET Core prend en charge FixedRate et l’échantillonnage adaptatif. Échantillonnage adaptatif est activé par défaut. Suivez [cela](../../azure-monitor/app/sampling.md#configuring-adaptive-sampling-for-aspnet-core-applications) document pour savoir comment configurer l’échantillonnage pour les applications ASP.NET Core.

### <a name="adding-telemetryinitializers"></a>Ajout de TelemetryInitializers

Pour ajouter un nouveau `TelemetryInitializer`, ajoutez-le dans le conteneur DependencyInjection, comme indiqué ci-dessous. `TelemetryInitializer`s ajouté au conteneur de DependencyInjection sera récupéré par le Kit de développement logiciel automatiquement.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSingleton<ITelemetryInitializer, MyCustomTelemetryInitializer>();
    }
```

### <a name="removing-telemetryinitializers"></a>Suppression de TelemetryInitializers

Pour supprimer tous les ou TelemetryInitializers spécifiques, qui sont à présents par défaut, utilisez l’exemple de code suivant **après** appelant `AddApplicationInsightsTelemetry()`.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetry();

        // Remove a specific built-in TelemetryInitializer
        var tiToRemove = services.FirstOrDefault<ServiceDescriptor>
                         (t => t.ImplementationType == typeof(AspNetCoreEnvironmentTelemetryInitializer));
        if (tiToRemove != null)
        {
            services.Remove(tiToRemove);
        }

        // Remove all initializers
        // This requires importing namespace using Microsoft.Extensions.DependencyInjection.Extensions;
        services.RemoveAll(typeof(ITelemetryInitializer));
    }
```

### <a name="adding-telemetryprocessors"></a>TelemetryProcessors Ajout

Processeurs de télémétrie personnalisées peuvent être ajoutées à la `TelemetryConfiguration` à l’aide de la méthode d’extension `AddApplicationInsightsTelemetryProcessor` sur `IServiceCollection`. Utilisez l’exemple suivant.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        // ...
        services.AddApplicationInsightsTelemetry();
        services.AddApplicationInsightsTelemetryProcessor<MyFirstCustomTelemetryProcessor>();

        // If you have more processors:
        services.AddApplicationInsightsTelemetryProcessor<MySecondCustomTelemetryProcessor>();
    }
```

### <a name="configuring-or-removing-default-telemetrymodules"></a>Configuration ou la suppression par défaut TelemetryModules

Les modules de collection automatique suivants sont activés par défaut et utilisez chargées de collecter automatiquement les données de télémétrie. Ils peuvent être désactivés et configurés pour modifier le comportement par défaut.

1. `RequestTrackingTelemetryModule`
1. `DependencyTrackingTelemetryModule`
1. `PerformanceCollectorModule`
1. `QuickPulseTelemetryModule`
1. `AppServicesHeartbeatTelemetryModule`
1. `AzureInstanceMetadataTelemetryModule`

Pour configurer tout défaut `TelemetryModule`, utilisez la méthode d’extension `ConfigureTelemetryModule<T>` sur `IServiceCollection` comme indiqué dans l’exemple ci-dessous.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetry();

        // The following configures DependencyTrackingTelemetryModule.
        // Similarly, any other default modules can be configured.
        services.ConfigureTelemetryModule<DependencyTrackingTelemetryModule>((module, o) =>
                        {
                            module.EnableW3CHeadersInjection = true;
                        });

        // The following removes PerformanceCollectorModule to disable perf-counter collection.
        // Similarly, any other default modules can be removed.
        var performanceCounterService = services.FirstOrDefault<ServiceDescriptor>(t => t.ImplementationType == typeof(PerformanceCollectorModule));
        if (performanceCounterService != null)
        {
         services.Remove(performanceCounterService);
        }
    }
```

### <a name="configuring-telemetry-channel"></a>Configuration du canal de télémétrie

Le canal par défaut utilisé est le `ServerTelemetryChannel`. Il peut être remplacé par l’exemple suivant ci-dessous.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        // use the following to replace the default channel with InMemoryChannel.
        // this can also be applied to ServerTelemetryChannel as well.
        services.AddSingleton(typeof(ITelemetryChannel), new InMemoryChannel() {MaxTelemetryBufferCapacity = 19898 });

        services.AddApplicationInsightsTelemetry();
    }
```

## <a name="frequently-asked-questions"></a>Questions fréquentes (FAQ)

*1. Je veux effectuer le suivi des données de télémétrie supplémentaires en dehors de la télémétrie automatiquement collecté. Comment procéder ?*

* Pour obtenir une instance de `TelemetryClient` à l’aide de l’injection de constructeur et appeler requis `TrackXXX()` méthode dessus. Il n’est pas recommandé pour créer de nouveaux `TelemetryClient` instances dans une application ASP.NET Core, comme une instance singleton de `TelemetryClient` est déjà inscrit dans le conteneur d’injection de dépendance qui partage `TelemetryConfiguration` avec le reste de la télémétrie. Création d’un nouveau `TelemetryClient` instance est recommandée uniquement si elle doit avoir une configuration distincte du reste des données de télémétrie. L’exemple suivant montre comment effectuer le suivi des données de télémétrie supplémentaires à partir d’un contrôleur.

```csharp
public class HomeController : Controller
{
    private TelemetryClient telemetry;

    // use constructor injection to get TelemetryClient instance
    public HomeController(TelemetryClient telemetry)
    {
        this.telemetry = telemetry;
    }

    public IActionResult Index()
    {
        // call required TrackXXX method.
        this.telemetry.TrackEvent("HomePageRequested");
        return View();
    }
```

 Reportez-vous à [mesures personnalisées Application Insights référence de l’API](https://docs.microsoft.com/azure/azure-monitor/app/api-custom-events-metrics/) pour la description des données personnalisées, création de rapports dans Application Insights.

*2. Certains modèles Visual Studio permet de méthode d’extension UseApplicationInsights() sur IWebHostBuilder pour activer Application Insights. Cette utilisation est toujours valide ?*

* L’activation d’Application Insights avec cette méthode est valide et est utilisé dans l’intégration de Visual Studio et dans les extensions d’application Web Azure également. Toutefois, il est recommandé d’utiliser `services.AddApplicationInsightsTelemery()` car elle fournit des surcharges pour contrôler une configuration. Les deux méthode fait en interne la même chose, c’est le cas s’il n’existe aucune configuration personnalisée à appliquer, l’appel soit convient.

*3. Je déploie mon application ASP.NET Core sur Azure Web Apps. Dois-je toujours activer l’extension Application Insights à partir d’applications Web ?*

* Si le Kit de développement logiciel est installé au moment de la génération, comme indiqué dans cet article, il n’est pas nécessaire pour activer l’extension Application Insights à partir du portail Web Apps. Même si l’extension est installée, elle sera temporisation, lorsqu’il détecte que le Kit de développement logiciel est déjà ajouté à l’application. Activer Application Insights à partir de l’extension vous évite l’installation et la mise à jour le SDK à votre application. Toutefois, l’activation d’Application Insights conformément à cet article est plus flexible pour des raisons ci-dessous.
    1. Données de télémétrie application Insights continueront à fonctionner
        1. Tous les systèmes d’exploitation : Windows, Linux, Mac
        1. Tous les publication mode - autonome ou dépendant du Framework.
        1. Tous les frameworks cibles, y compris le .NET Framework complet.
        1. Toutes les options d’hébergement - application Web Azure, machines virtuelles, Linux, conteneurs, AKS, non Azure.
    1. Données de télémétrie sont consultables localement, lors du débogage à partir de Visual Studio.
    1. Permet à l’aide des données de télémétrie personnalisées supplémentaires suivi `TrackXXX()` API.
    1. A un contrôle total sur la configuration.

*4. Puis-je activer la surveillance d’Application Insights à l’aide des outils tels que Status Monitor ?*

* Non. [Moniteur d’état](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now) et son remplacement à venir [IISConfigurator](https://github.com/Microsoft/ApplicationInsights-Announcements/issues/21) prend actuellement en charge ASP.NET uniquement. Le document sera actualisé lorsque prend en charge pour les applications ASP.NET Core est disponible.

*5. J’ai une Application ASP.NET Core 2.0 ? N’est pas Application Insights automatiquement activé pour eux sans mon intervention ?*

* `Microsoft.AspNetCore.All` métapackage 2.0 SDK Application Insights (version 2.1.0), et si vous exécutez l’application sous le débogueur Visual Studio, Visual Studio permet à application insights affiche la télémétrie localement dans l’IDE lui-même. Données de télémétrie n’a pas été envoyée au service Application Insights, sauf si une clé d’instrumentation est spécifiée explicitement. Nous recommandons de suivre les instructions dans cet article pour activer Application Insights, même pour 2.0 applications.

*6. J’ai exécuté mon application dans Linux. Sont toutes les fonctionnalités prises en charge dans Linux ainsi ?*

* Oui. Prise en charge de fonctionnalités pour le Kit de développement est identique dans toutes les plateformes, avec les exceptions suivantes :
    1. Compteurs de performances ne sont pas encore pris en charge dans Windows-Non. Ce document sera mis à jour lors de l’ajout de la prise en charge de Linux.
    1. Même si `ServerTelemetryChannel` est activé par défaut, si l’application est en cours d’exécution dans les autres que windows, le canal ne crée pas automatiquement un dossier de stockage local pour conserver temporairement des données de télémétrie s’il existe des problèmes de réseau. Cette limitation provoque la télémétrie être perdues si les problèmes de serveur ou de réseau temporaires. La solution de contournement pour ce problème est l’utilisateur peut configurer un dossier local pour le canal, comme indiqué ci-dessous.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        // The following will configure channel to use the given folder to temporarily
        // store telemetry items during network or application insights server issues.
        // User should ensure that the given folder already exists,
        // and that application has read/write permissions.
        services.AddSingleton(typeof(ITelemetryChannel),
                                new ServerTelemetryChannel () {StorageFolder = "/tmp/myfolder"});
        services.AddApplicationInsightsTelemetry();
    }
```
