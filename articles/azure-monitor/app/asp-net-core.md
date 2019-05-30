---
title: Azure Application Insights pour ASP.NET Core | Microsoft Docs
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
ms.date: 05/22/2019
ms.author: mbullwin
ms.openlocfilehash: cb7ace20fd0a59dafff3d7f8240f54c3c8e12492
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66226396"
---
# <a name="application-insights-for-aspnet-core-applications"></a>Application Insights pour les applications ASP.NET Core

Cet article décrit comment activer Application Insights pour un [ASP.NET Core](https://docs.microsoft.com/aspnet/core) application. Lorsque vous suivez les instructions dans cet article, Application Insights va commencer à collecter les demandes, dépendances, exceptions, les compteurs de performances, pulsations et journaux à partir de votre application ASP.NET Core. L’exemple d’application est un [Application MVC](https://docs.microsoft.com/aspnet/core/tutorials/first-mvc-app) ciblant `netcoreapp2.2`, mais ces instructions sont applique à toutes les applications ASP.NET Core.

## <a name="supported-scenarios"></a>Scénarios pris en charge

Le [Application Insights SDK (Kit de développement logiciel) pour ASP.NET Core](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) peut surveiller vos applications, quel que soit l’emplacement ou le mode d’exécution de l’application. Si votre application est en cours d’exécution et dispose d’une connectivité réseau vers Azure, les données de télémétrie peuvent être collectées. Cela signifie que surveillance Application Insights est pris en charge partout où que .NET Core est pris en charge. Cette prise en charge inclut, n’importe quel système d’exploitation (Windows, Linux, Mac), la méthode d’hébergement (Visual Studio dans le processus out-of-process), la méthode de déploiement dépendant du framework vs (autonomes), serveur Web (IIS, Kestrel), plateforme d’hébergement (Azure Web Apps, machine virtuelle Azure, Docker, Azure Kubernetes Service (AKS) et ainsi de suite.) ou IDE (Visual Studio, Visual Studio Code, de ligne de commande).

## <a name="prerequisites"></a>Conditions préalables

- Une Application ASP.NET Core fonctionne. Suivez le [guide de démarrage rapide ASP.NET Core bien](https://docs.microsoft.com/aspnet/core/getting-started/) pour créer une application ASP.NET Core, si nécessaire.
- Une Application Insights instrumentation clé valide, ce qui est nécessaire pour envoyer les données de télémétrie au service Application Insights. Suivez le [créer une ressource instructions](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource) pour créer une nouvelle ressource Application Insights, si nécessaire et obtenir une clé d’instrumentation.

## <a name="enable-application-insights-server-side-telemetry-visual-studio"></a>Activer les données de télémétrie Application Insights côté serveur (Visual Studio)

1. Ouvrez votre projet dans Visual Studio.

    > [!TIP]
    > Lors de la pas une étape obligatoire, il peut être utile de définir le contrôle de source pour votre projet pour que vous puissiez suivre toutes les modifications apportées par Application Insights. Pour activer la sélection de contrôle de source **fichier** > **ajouter au contrôle de code Source**.

2. Sélectionnez **Projet** > **Ajouter Application Insights Telemetry**.

3. Sélectionnez **Prise en main**. (Selon votre version de Visual Studio, le texte peut varier légèrement. Certaines versions antérieures affichent un bouton **Démarrer gratuitement** à la place.)

4. Sélectionnez votre abonnement, puis sélectionnez **Ressource** > **Inscrire**.

5. Après avoir ajouté Application Insights à votre projet, vérifiez pour confirmer que vous utilisez la dernière version stable du SDK. Accédez à **projet** > **gérer les Packages NuGet** > **Microsoft.ApplicationInsights.AspNetCore** > Si nécessaire choisissez **Mise à jour**.

     ![Capture d’écran de gérer l’écran de package NuGet avec le package d’Application Insights sélectionné pour la mise à jour](./media/asp-net-core/update-nuget-package.png)

6. Si vous avez suivi l’info-bulle facultative et que vous ajouté votre projet au contrôle de code source vous pouvez ensuite accéder à **vue** > **Team Explorer** > **modifications** et Sélectionnez chaque fichier pour une vue de comparaison des modifications apportées à partir de l’ajout de données de télémétrie Application Insights.

## <a name="enable-application-insights-server-side-telemetry-without-visual-studio"></a>Activer les données de télémétrie Application Insights côté serveur (sans Visual Studio)

1. Installer le [package NuGet du Kit de développement logiciel Application Insights pour ASP.NET Core](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore). Nous vous recommandons de toujours utiliser la dernière version stable. Vous trouverez les notes de version complet pour le Kit de développement sur le [ouvrir le référentiel de code source GitHub](https://github.com/Microsoft/ApplicationInsights-aspnetcore/releases).

    L’extrait suivant montre les modifications à ajouter à votre projet `.csproj` fichier.

    ```xml
        <ItemGroup>
          <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.6.1" />
        </ItemGroup>
    ```

2. Ajouter `services.AddApplicationInsightsTelemetry();` à la `ConfigureServices()` méthode dans votre `Startup` classe. Exemple complet ci-dessous.

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

3. Configurer la clé d’instrumentation.

    Bien qu’il soit possible de fournir la clé d’instrumentation en tant qu’argument à `AddApplicationInsightsTelemetry`, nous vous recommandons de spécifier la clé d’instrumentation dans la configuration. L’exemple suivant montre comment spécifier une clé d’instrumentation dans `appsettings.json`. Assurez-vous que `appsettings.json` est copié dans le dossier racine d’application lors de la publication.

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

    Ou bien, la clé d’instrumentation peut également être spécifiée dans une des variables d’environnement suivantes.

    APPINSIGHTS_INSTRUMENTATIONKEY

    ApplicationInsights:InstrumentationKey

    Exemple :

    `SET ApplicationInsights:InstrumentationKey=putinstrumentationkeyhere`

    `SET APPINSIGHTS_INSTRUMENTATIONKEY=putinstrumentationkeyhere`

    `APPINSIGHTS_INSTRUMENTATIONKEY` est généralement utilisé pour spécifier la clé d’instrumentation pour les applications déployées dans Azure Web Apps.

    > [!NOTE]
    > Une clé d’instrumentation spécifiée dans wins de code sur la variable d’environnement `APPINSIGHTS_INSTRUMENTATIONKEY`, qui prendra le pas sur les autres options.

## <a name="run-your-application"></a>Exécuter votre application

 Exécutez votre application et effectuer des demandes à ce dernier. Données de télémétrie devraient maintenant commencer à circuler à Application Insights. Les données de télémétrie suivantes sont automatiquement collectées par le SDK Application Insights.

|Demandes/dépendances |Détails|
|---------------|-------|
|Requests | Demandes web entrantes à votre application. |
|HTTP/Https | Les appels effectués avec `HttpClient`. |
|SQL | Les appels effectués avec `SqlClient`. |
|[Azure Storage](https://www.nuget.org/packages/WindowsAzure.Storage/) | Appels effectués avec le Client de stockage Azure. |
|[EventHub logiciel (SDK)](https://www.nuget.org/packages/Microsoft.Azure.EventHubs) | Version 1.1.0 et versions ultérieures. |
|[Kit de développement logiciel (SDK) client ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus)| Version 3.0.0 et versions ultérieures. |
|Azure Cosmos DB | Suivi uniquement automatiquement si vous utilisez HTTP/HTTPS. Le mode TCP ne sera pas capturé par Application Insights. |

### <a name="performance-counters"></a>Compteurs de performance

Prise en charge de [les compteurs de performances](https://azure.microsoft.com/documentation/articles/app-insights-web-monitor-performance/) dans ASP.NET Core est limitée à ce qui suit

   * Kit de développement logiciel version 2.4.1 et au-dessus de collecte des compteurs de performances si l’application s’exécute dans Azure Web App (Windows)
   * Kit de développement logiciel version 2.7.0-beta3 et au-dessus de collecte des compteurs de performances si l’application est en cours d’exécution dans Windows et de ciblage `NETSTANDARD2.0` ou une version ultérieure.
   * Pour les applications ciblant le .NET Framework, les compteurs de performance sont prises en charge dans toutes les versions du Kit de développement logiciel.
   * Cet article sera mis à jour lors de l’ajout de la prise en charge du compteur de performance dans Linux.

### <a name="ilogger-logs"></a>Journaux ILogger

[Les journaux ILogger](https://docs.microsoft.com/azure/azure-monitor/app/ilogger) de gravité `Warning` ou version ultérieure sont automatiquement capturées à partir du Kit de développement logiciel version 2.7.0-beta3 ou une version ultérieure.

### <a name="live-metrics"></a>Métriques en temps réel

Il peut prendre quelques minutes pour la télémétrie commencer à apparaître dans le portail. Pour vérifier rapidement si tout fonctionne, il est préférable d’utiliser [métriques temps réel](https://docs.microsoft.com/azure/application-insights/app-insights-live-stream), tandis que la fabrication des demandes à l’application en cours d’exécution.

## <a name="enable-client-side-telemetry-for-web-applications"></a>Activer la télémétrie côté Client pour les Applications Web

Les étapes ci-dessus sont suffisantes pour commencer à collecter des données de télémétrie côté serveur. Si votre application comporte des composants côté client, puis suivez les étapes ci-dessous pour commencer à collecter [télémétrie d’utilisation](https://docs.microsoft.com/azure/azure-monitor/app/usage-overview) à partir de là.

1. Dans `_ViewImports.cshtml`, ajoutez l’injection :

    ```cshtml
        @inject Microsoft.ApplicationInsights.AspNetCore.JavaScriptSnippet JavaScriptSnippet
    ```

2. Dans `_Layout.cshtml`, insérer HtmlHelper à la fin de `<head>` section mais avant tout autre script. Les données de télémétrie JavaScript personnalisée, que vous souhaitez signaler à partir de la page doivent être injectée après cet extrait de code :

    ```cshtml
        @Html.Raw(JavaScriptSnippet.FullScript)
        </head>
    ```

Le `.cshtml` sont des noms de fichiers mentionnés ci-dessus à partir d’un modèle d’application MVC par défaut. Finalement, pour activer correctement l’analyse côté client pour votre application, vous devez l’extrait de code JavaScript pour être présents dans la `<head>` section de chaque page de votre application que vous souhaitez surveiller. Pour ce modèle d’application ajoutant l’extrait de code Javascript à `_Layout.cshtml` exécutera efficacement cet objectif. Si votre projet n’a pas de ce fichier spécifique, vous pouvez toujours ajouter [analyse côté client](https://docs.microsoft.com/azure/azure-monitor/app/website-monitoring). Vous devez soit ajouterions seulement le code JavaScript dans un fichier équivalent qui contrôle le `<head>` de toutes les pages au sein de votre application, ou vous pouvez également vous pouvez ajouter l’extrait de code à personne plusieurs pages même si cela est difficile à gérer et n’est généralement pas recommandé.

## <a name="configuring-application-insights-sdk"></a>Configuration d’Application Insights SDK

Le SDK application Insights pour ASP.NET Core peut être personnalisé pour modifier la configuration par défaut. Les utilisateurs de SDK Application Insights ASP.NET peuvent vous être familiers avec la configuration à l’aide `ApplicationInsights.config`, ou en modifiant `TelemetryConfiguration.Active`. Pour ASP.NET Core, la configuration s’effectue différemment. Le Kit de développement ASP.NET Core est ajouté à l’application et configuré à l’aide intégrée d’ASP.NET Core [l’injection de dépendances](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection). Presque toutes les modifications de configuration sont effectuées dans le `ConfigureServices()` méthode de votre `Startup.cs` classe, sauf indication contraire. Suivez les sections ci-dessous pour en savoir plus.

> [!NOTE]
>  Changement de la configuration en modifiant `TelemetryConfiguration.Active` n’est pas recommandé dans les applications ASP.NET Core.

### <a name="configuring-using-applicationinsightsserviceoptions"></a>Configuration à l’aide de ApplicationInsightsServiceOptions

Il est possible de modifier quelques paramètres communs en passant `ApplicationInsightsServiceOptions` à `AddApplicationInsightsTelemetry`. comme dans l’exemple suivant :

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

La liste exacte des paramètres configurables dans `ApplicationInsightsServiceOptions` trouverez [ici](https://github.com/microsoft/ApplicationInsights-aspnetcore/blob/develop/src/Microsoft.ApplicationInsights.AspNetCore/Extensions/ApplicationInsightsServiceOptions.cs).

### <a name="sampling"></a>échantillonnage

Le SDK application Insights pour ASP.NET Core prend en charge FixedRate et l’échantillonnage adaptatif. Échantillonnage adaptatif est activé par défaut. Suivez notre [obtenir des conseils sur l’échantillonnage ADAPTATIF](../../azure-monitor/app/sampling.md#configuring-adaptive-sampling-for-aspnet-core-applications), pour savoir comment configurer l’échantillonnage pour les applications ASP.NET Core.

### <a name="adding-telemetryinitializers"></a>Ajout de TelemetryInitializers

[Initialiseurs de télémétrie](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#add-properties-itelemetryinitializer) sont utilisés lorsque vous souhaitez définir des propriétés globales qui sont envoyées avec toutes les données de télémétrie.

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

Processeurs de télémétrie personnalisées peuvent être ajoutées à la `TelemetryConfiguration` à l’aide de la méthode d’extension `AddApplicationInsightsTelemetryProcessor` sur `IServiceCollection`. Processeurs de télémétrie sont utilisés dans [scénarios de filtrage avancé](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#filtering-itelemetryprocessor) pour permettre un contrôle plus direct sur ce qui est inclus ou exclu de la télémétrie que vous envoyez au service Application Insights. Utilisez l’exemple suivant.

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

Application Insights utilise des modules de télémétrie de [-la collecte automatique des informations utiles](https://docs.microsoft.com/azure/azure-monitor/app/auto-collect-dependencies) sur les charges de travail spécifiques sans nécessiter de configuration supplémentaire.

Les modules de collection automatique suivants sont activés par défaut et utilisez chargées de collecter automatiquement les données de télémétrie. Ils peuvent être désactivés et configurés pour modifier le comportement par défaut.

* `RequestTrackingTelemetryModule`
* `DependencyTrackingTelemetryModule`
* `PerformanceCollectorModule`
* `QuickPulseTelemetryModule`
* `AppServicesHeartbeatTelemetryModule`
* `AzureInstanceMetadataTelemetryModule`

Pour configurer tout défaut `TelemetryModule`, utilisez la méthode d’extension `ConfigureTelemetryModule<T>` sur `IServiceCollection` comme indiqué dans l’exemple ci-dessous.

```csharp
using Microsoft.ApplicationInsights.DependencyCollector;
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector;

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
using Microsoft.ApplicationInsights.Channel;

    public void ConfigureServices(IServiceCollection services)
    {
        // use the following to replace the default channel with InMemoryChannel.
        // this can also be applied to ServerTelemetryChannel as well.
        services.AddSingleton(typeof(ITelemetryChannel), new InMemoryChannel() {MaxTelemetryBufferCapacity = 19898 });

        services.AddApplicationInsightsTelemetry();
    }
```

## <a name="frequently-asked-questions"></a>Questions fréquentes (FAQ)

### <a name="i-want-to-track-additional-telemetry-other-than-the-auto-collected-telemetry-how-do-i-do-it"></a>Je veux effectuer le suivi des données de télémétrie supplémentaires autres que les données de télémétrie automatiquement collecté. Comment procéder ?

Pour obtenir une instance de `TelemetryClient` à l’aide de l’injection de constructeur et appeler requis `TrackXXX()` méthode dessus. Il n’est pas recommandé pour créer de nouveaux `TelemetryClient` instances dans une application ASP.NET Core, comme une instance singleton de `TelemetryClient` est déjà inscrit dans le conteneur d’injection de dépendance qui partage `TelemetryConfiguration` avec le reste de la télémétrie. Création d’un nouveau `TelemetryClient` instance est recommandée uniquement si elle doit avoir une configuration distincte du reste des données de télémétrie. L’exemple suivant montre comment effectuer le suivi des données de télémétrie supplémentaires à partir d’un contrôleur.

```csharp
using Microsoft.ApplicationInsights;

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

 Reportez-vous à [mesures personnalisées Application Insights référence de l’API](https://docs.microsoft.com/azure/azure-monitor/app/api-custom-events-metrics/) pour obtenir une description des données personnalisées, création de rapports dans Application Insights.

### <a name="some-visual-studio-templates-used-useapplicationinsights-extension-method-on-iwebhostbuilder-to-enable-application-insights-is-this-usage-still-valid"></a>Certains modèles Visual Studio permet de méthode d’extension UseApplicationInsights() sur IWebHostBuilder pour activer Application Insights. Cette utilisation est toujours valide ?

L’activation d’Application Insights avec cette méthode est valide et est utilisé dans l’intégration de Visual Studio et dans les extensions d’application Web Azure également. Toutefois, il est recommandé d’utiliser `services.AddApplicationInsightsTelemetry()` car elle fournit des surcharges pour contrôler une configuration. Les deux méthodes en interne la même chose, c’est le cas s’il n’existe aucune configuration personnalisée à appliquer, l’appel soit convient.

### <a name="i-am-deploying-my-aspnet-core-application-to-azure-web-apps-should-i-still-enable-the-application-insights-extension-from-web-apps"></a>Je déploie mon application ASP.NET Core sur Azure Web Apps. Dois-je toujours activer l’extension Application Insights à partir d’applications Web ?

Si le SDK est installé au moment de la génération, comme indiqué dans cet article, il n’est pas nécessaire pour activer l’extension Application Insights à partir du portail App Service. Même si l’extension est installée, il enregistrera désactivé quand il détecte que le Kit de développement logiciel est déjà ajouté à l’application. Activer Application Insights à partir de l’extension vous évite l’installation et la mise à jour le Kit de développement. Toutefois, l’activation d’Application Insights conformément à cet article est plus flexible pour des raisons ci-dessous.
   * Données de télémétrie application Insights continueront à fonctionner :
       * Tous les systèmes d’exploitation : Windows, Linux, Mac
       * Tous les modes - de publication autonome ou dépendant du Framework.
       * Tous les frameworks cibles, y compris le .NET Framework complet.
       * Toutes les options d’hébergement - application Web Azure, machines virtuelles, Linux, conteneurs, AKS, non Azure.
   * Données de télémétrie sont consultables localement, lors du débogage à partir de Visual Studio.
   * Permet à l’aide des données de télémétrie personnalisées supplémentaires suivi `TrackXXX()` API.
   * Vous avez un contrôle total sur la configuration.

### <a name="can-i-enable-application-insights-monitoring-using-tools-like-status-monitor"></a>Puis-je activer la surveillance d’Application Insights à l’aide des outils tels que Status Monitor ?

Non. [Moniteur d’état](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now) et son remplacement à venir [Status Monitor v2](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-overview) prend actuellement en charge ASP.NET 4.x uniquement.

### <a name="i-have-an-aspnet-core-20-application-isnt-application-insights-automatically-enabled-without-me-doing-anything"></a>J’ai une Application ASP.NET Core 2.0. Application Insights n’est pas automatiquement activé sans mon intervention ?

`Microsoft.AspNetCore.All` métapackage 2.0 SDK Application Insights (version 2.1.0), et si vous exécutez l’application sous le débogueur Visual Studio, Visual Studio permet à Application Insights affiche la télémétrie localement dans l’IDE lui-même. Données de télémétrie n’a pas été envoyée au service Application Insights, sauf si une clé d’instrumentation est spécifiée explicitement. Nous vous recommandons de suivre les instructions de cet article pour activer Application Insights, même pour 2.0 applications.

### <a name="i-run-my-application-in-linux-are-all-features-supported-in-linux-as-well"></a>J’ai exécuté mon application dans Linux. Sont toutes les fonctionnalités prises en charge dans Linux ainsi ?

* Oui. Prise en charge de fonctionnalités pour le Kit de développement est identique dans toutes les plateformes, avec les exceptions suivantes :

    * Compteurs de performances ne sont pas encore pris en charge dans Windows-Non.
    * Même si `ServerTelemetryChannel` est activé par défaut, si l’application est en cours d’exécution dans Linux ou MacOS, le canal ne crée pas automatiquement un dossier de stockage local pour conserver temporairement des données de télémétrie s’il existe des problèmes de réseau. Cette limitation provoque la télémétrie être perdues si les problèmes de serveur ou de réseau temporaires. La solution de contournement pour ce problème est l’utilisateur peut configurer un dossier local pour le canal, comme indiqué ci-dessous.

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;

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

## <a name="open-source-sdk"></a>Kit de développement logiciel (SDK) open source
[Lisez et contribuez au code](https://github.com/Microsoft/ApplicationInsights-aspnetcore#recent-updates).

## <a name="video"></a>Vidéo

- Vidéo détaillée externe sur la [configuration d’Application Insights en partant de zéro avec .NET Core et Visual Studio](https://www.youtube.com/watch?v=NoS9UhcR4gA&t).
- Externe vidéo pas à pas sur [configuration d’Application Insights avec .NET Core et Visual Studio Code](https://youtu.be/ygGt84GDync) à partir de zéro.

## <a name="next-steps"></a>Étapes suivantes
* [Explorez les flux d’utilisateurs](../../azure-monitor/app/usage-flows.md) pour comprendre comment les utilisateurs naviguent dans votre application.
* [Configurez la collecte de captures instantanées](https://docs.microsoft.com/azure/application-insights/app-insights-snapshot-debugger) pour voir l’état du code source et des variables au moment où une exception est levée.
* [Utilisez l’API](../../azure-monitor/app/api-custom-events-metrics.md) pour envoyer vos propres événements et mesures pour obtenir une vue plus détaillée des performances et de l’utilisation de votre application.
* Utilisez des [tests de disponibilité](../../azure-monitor/app/monitor-web-app-availability.md) pour vérifier votre application en permanence dans le monde entier.
