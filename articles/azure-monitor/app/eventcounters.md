---
title: Compteurs d’événements dans Application Insights | Microsoft Docs
description: Supervisez les compteurs d’événements .NET/.NET Core système et personnalisés dans Application Insights.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 09/20/2019
ms.openlocfilehash: 5a47f5c2f9c9d4e22e8205853d85214997a2bea7
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75406923"
---
# <a name="eventcounters-introduction"></a>Présentation des compteurs d’événements

`EventCounter` est le mécanisme .NET/.NET Core permettant de publier et consommer des compteurs ou des statistiques. [Ce](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.Tracing/documentation/EventCounterTutorial.md) document donne une vue d’ensemble des `EventCounters` et des exemples sur la façon de les publier et de les consommer. Les compteurs d’événements sont pris en charge sur toutes les plateformes de système d’exploitation : Windows, Linux et macOS. Vous pouvez les considérer comme l’équivalent multiplateforme des [compteurs de performances](https://docs.microsoft.com/dotnet/api/system.diagnostics.performancecounter), uniquement pris en charge dans les systèmes Windows.

Alors que les utilisateurs peuvent publier n’importe quel `EventCounters` personnalisé pour répondre à leurs besoins, le runtime .NET Core 3.0 publie un ensemble de ces compteurs par défaut. Le présent document décrit les étapes nécessaires à la collecte et à l’affichage des `EventCounters` (définis par le système ou par l’utilisateur) dans Azure Application Insights.

## <a name="using-application-insights-to-collect-eventcounters"></a>Utilisation d’Application Insights pour collecter des compteurs d’événements

Application Insights prend en charge la collecte de `EventCounters` avec son `EventCounterCollectionModule`, qui fait partie du package NuGet récemment publié [Microsoft.ApplicationInsights.EventCounterCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventCounterCollector). `EventCounterCollectionModule` est automatiquement activé dès que vous utilisez [AspNetCore](asp-net-core.md) ou [WorkerService](worker-service.md). `EventCounterCollectionModule` collecte les compteurs selon une fréquence non configurable de 60 secondes. Aucune autorisation spéciale n’est nécessaire pour collecter des compteurs d’événements.

## <a name="default-counters-collected"></a>Compteurs par défaut collectés

Pour les applications qui s’exécutent dans .NET Core 3.0, les compteurs suivants sont collectés automatiquement par le SDK. Le nom des compteurs sera au format « Category|Counter ».

|Category | Compteur|
|---------------|-------|
|`System.Runtime` | `cpu-usage` |
|`System.Runtime` | `working-set` |
|`System.Runtime` | `gc-heap-size` |
|`System.Runtime` | `gen-0-gc-count` |
|`System.Runtime` | `gen-1-gc-count` |
|`System.Runtime` | `gen-2-gc-count` |
|`System.Runtime` | `time-in-gc` |
|`System.Runtime` | `gen-0-size` |
|`System.Runtime` | `gen-1-size` |
|`System.Runtime` | `gen-2-size` |
|`System.Runtime` | `loh-size` |
|`System.Runtime` | `alloc-rate` |
|`System.Runtime` | `assembly-count` |
|`System.Runtime` | `exception-count` |
|`System.Runtime` | `threadpool-thread-count` |
|`System.Runtime` | `monitor-lock-contention-count` |
|`System.Runtime` | `threadpool-queue-length` |
|`System.Runtime` | `threadpool-completed-items-count` |
|`System.Runtime` | `active-timer-count` |
|`Microsoft.AspNetCore.Hosting` | `requests-per-second` |
|`Microsoft.AspNetCore.Hosting` | `total-requests` |
|`Microsoft.AspNetCore.Hosting` | `current-requests` |
|`Microsoft.AspNetCore.Hosting` | `failed-requests` |

> [!NOTE]
> Les compteurs de la catégorie Microsoft.AspNetCore.Hosting sont uniquement ajoutés aux applications ASP.NET Core.

## <a name="customizing-counters-to-be-collected"></a>Personnalisation des compteurs à collecter

L’exemple suivant montre comment ajouter/supprimer des compteurs. Cette personnalisation est effectuée dans la méthode `ConfigureServices` de votre application une fois que la collecte de données de télémétrie Application Insights est activée en utilisant `AddApplicationInsightsTelemetry()` ou `AddApplicationInsightsWorkerService()`. Voici un exemple de code d’une application ASP.NET Core. Pour les autres types d’applications, reportez-vous à [ce](worker-service.md#configuring-or-removing-default-telemetrymodules) document.

```csharp
    using Microsoft.ApplicationInsights.Extensibility.EventCounterCollector;

    public void ConfigureServices(IServiceCollection services)
    {
        //... other code...

        // The following code shows several customizations done to EventCounterCollectionModule.
        services.ConfigureTelemetryModule<EventCounterCollectionModule>(
            (module, o) =>
            {
                // This removes all default counters.
                module.Counters.Clear();

                // This adds a user defined counter "MyCounter" from EventSource named "MyEventSource"
                module.Counters.Add(new EventCounterCollectionRequest("MyEventSource", "MyCounter"));

                // This adds the system counter "gen-0-size" from "System.Runtime"
                module.Counters.Add(new EventCounterCollectionRequest("System.Runtime", "gen-0-size"));
            }
        );

        // The following code removes EventCounterCollectionModule to disable the module completely.
        var eventCounterModule = services.FirstOrDefault<ServiceDescriptor>
                    (t => t.ImplementationType == typeof(EventCounterCollectionModule));
        if (eventCounterModule != null)
        {
            services.Remove(eventCounterModule);
        }
    }
```

## <a name="event-counters-in-metric-explorer"></a>Compteurs d’événements dans Metric Explorer

Pour voir les métriques de compteur d’événements dans [Metric Explorer](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-charts), sélectionnez une ressource Application Insights, puis choisissez des métriques basées sur un journal comme espace de noms de métrique. Ainsi, les métriques de compteur d’événements s’affichent sous Catégorie personnalisée.

> [!div class="mx-imgBorder"]
> ![Compteurs d’événements signalés dans Application Insights](./media/event-counters/metrics-explorer-counter-list.png)

## <a name="event-counters-in-analytics"></a>Compteurs d’événements dans Analytics

Vous pouvez également rechercher et afficher des rapports de compteurs d’événements dans [Analytics](../../azure-monitor/app/analytics.md), dans la table **customMetrics**.

Par exemple, exécutez la requête suivante pour voir quels compteurs sont collectés et disponibles pour la requête :

```Kusto
customMetrics | summarize avg(value) by name
```

> [!div class="mx-imgBorder"]
> ![Compteurs d’événements signalés dans Application Insights](./media/event-counters/analytics-event-counters.png)

Pour obtenir un graphique d’un compteur spécifique (par exemple, `ThreadPool Completed Work Item Count`) sur la période récente, exécutez la requête suivante.

```Kusto
customMetrics 
| where name contains "System.Runtime|ThreadPool Completed Work Item Count"
| where timestamp >= ago(1h)
| summarize  avg(value) by cloud_RoleInstance, bin(timestamp, 1m)
| render timechart
```
> [!div class="mx-imgBorder"]
> ![Conversation d’un compteur unique dans Application Insights](./media/event-counters/analytics-completeditems-counters.png)

Comme les autres données de télémétrie, **customMetrics** possède également une colonne `cloud_RoleInstance` qui indique l’identité de l’instance de serveur hôte sur lequel votre application est en cours d’exécution. La requête ci-dessus montre la valeur de compteur par instance et peut servir à comparer les performances des différentes instances de serveur.

## <a name="alerts"></a>Alertes
À l’instar d’autres métriques, vous pouvez [définir une alerte](../../azure-monitor/app/alerts.md) pour vous avertir si un compteur d’événements dépasse une limite que vous spécifiez. Ouvrez le volet Alertes et cliquez sur Ajouter une alerte.

## <a name="frequently-asked-questions"></a>Forum aux questions

### <a name="can-i-see-eventcounters-in-live-metrics"></a>Puis-je voir les compteurs d’événements dans les métriques temps réel ?

Les métriques temps réel ne présentent pas les compteurs d’événements à partir du jour actuel. Utilisez Metrics Explorer ou Analytics pour voir les données de télémétrie.

### <a name="which-platforms-can-i-see-the-default-list-of-net-core-30-counters"></a>Sur quelles plateformes puis-je voir la liste par défaut des compteurs .NET Core 3.0 ?

Un compteur d’événements n’a pas besoin d’autorisations spéciales. Il pris en charge sur toutes les plateformes .NET Core 3.0 prises en charge. notamment :

* **Système d’exploitation** : Windows, Linux ou macOS.
* **Méthode d’hébergement** : dans le processus ou en dehors du processus.
* **Méthode de déploiement** : dépendante de l’infrastructure ou autonome.
* **Serveur web** : IIS (Internet Information Server) ou Kestrel.
* **Plateforme d’hébergement** : fonctionnalité Web Apps d’Azure App Service, machine virtuelle Azure, Docker, Azure Kubernetes Service (AKS), etc.

### <a name="i-have-enabled-application-insights-from-azure-web-app-portal-but-i-cant-see-eventcounters"></a>J’ai activé Application Insights à partir du portail d’application web Azure. Mais je ne vois pas de compteurs d’événements.

 L’[extension Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps) pour ASP.NET Core ne prend pas encore en charge cette fonctionnalité. Ce document sera mis à jour quand cette fonctionnalité sera prise en charge.

## <a name="next"></a>Étapes suivantes

* [Suivi des dépendances](../../azure-monitor/app/asp-net-dependencies.md)
