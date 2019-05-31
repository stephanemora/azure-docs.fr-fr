---
title: Suivi des dépendances dans Azure Application Insights | Microsoft Docs
description: Surveiller les appels de dépendance à partir de votre en local ou d’une application web de Microsoft Azure avec Application Insights.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: d15c4ca8-4c1a-47ab-a03d-c322b4bb2a9e
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: mbullwin
ms.openlocfilehash: 479b810c5a66917bde5754d32991fb489ea26c9b
ms.sourcegitcommit: 8c49df11910a8ed8259f377217a9ffcd892ae0ae
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/29/2019
ms.locfileid: "66299291"
---
# <a name="dependency-tracking-in-azure-application-insights"></a>Suivi des dépendances dans Azure Application Insights 

Un *dépendance* est un composant externe qui est appelé par votre application. Il s’agit habituellement d’un service appelé à l’aide de HTTP, d’une base de données ou d’un système de fichiers. [Application Insights](../../azure-monitor/app/app-insights-overview.md) mesure la durée des appels de dépendance, si son échec ou non, ainsi que des informations supplémentaires telles que le nom de la dépendance et ainsi de suite. Vous pouvez examiner les appels de dépendance spécifique et les mettre en corrélation pour les requêtes et les exceptions.

## <a name="automatically-tracked-dependencies"></a>Dépendances automatiquement suivies

Le SDK application Insights pour .NET et .NET Core livré avec `DependencyTrackingTelemetryModule` qui est un Module de télémétrie qui collecte automatiquement les dépendances. Cette collection de dépendances est activée automatiquement pour [ASP.NET](https://docs.microsoft.com/azure/azure-monitor/app/asp-net) et [ASP.NET Core](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) applications, lorsque configuré conformément à la documentation officielle liée. `DependencyTrackingTelemetryModule` est fourni en tant que [cela](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/) package NuGet et est mis automatiquement lors de l’utilisation des packages NuGet `Microsoft.ApplicationInsights.Web` ou `Microsoft.ApplicationInsights.AspNetCore`.

 `DependencyTrackingTelemetryModule` actuellement suit automatiquement les dépendances suivantes :

|Les dépendances |Détails|
|---------------|-------|
|HTTP/Https | Local ou distant appels http/https |
|Appels de WCF| Suivi uniquement automatiquement si les liaisons Http sont utilisées.|
|SQL | Les appels effectués avec `SqlClient`. Consultez [cela](##advanced-sql-tracking-to-get-full-sql-query) pour capturer SQL de requête.  |
|[Azure stockage (Blob, Table, file d’attente)](https://www.nuget.org/packages/WindowsAzure.Storage/) | Appels effectués avec le Client de stockage Azure. |
|[EventHub logiciel (SDK)](https://www.nuget.org/packages/Microsoft.Azure.EventHubs) | Version 1.1.0 et versions ultérieures. |
|[Kit de développement logiciel (SDK) client ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus)| Version 3.0.0 et versions ultérieures. |
|Azure Cosmos DB | Suivi uniquement automatiquement si vous utilisez HTTP/HTTPS. Le mode TCP ne sera pas capturé par Application Insights. |


## <a name="setup-automatic-dependency-tracking-in-console-apps"></a>Le programme d’installation automatique suivi des dépendances dans les applications de Console

Pour suivre automatiquement les dépendances à partir d’applications de console.NET/.NET Core, installez le package Nuget `Microsoft.ApplicationInsights.DependencyCollector`et initialiser `DependencyTrackingTelemetryModule` comme suit :

```csharp
    DependencyTrackingTelemetryModule depModule = new DependencyTrackingTelemetryModule();
    depModule.Initialize(TelemetryConfiguration.Active);
```

### <a name="how-automatic-dependency-monitoring-works"></a>Dépendance automatique surveillance fonctionne ?

Dépendances sont automatiquement collectées en utilisant l’une des techniques suivantes :

* L’instrumentation de code d’octet autour des méthodes de sélection. (InstrumentationEngine à partir de StatusMonitor ou Extension d’application Web Azure)
* Rappels d’EventSource
* Rappels de DiagnosticSource (dans la dernière.NET/.NET Core kits de développement logiciel)

## <a name="manually-tracking-dependencies"></a>Manuellement le suivi des dépendances

Voici quelques exemples de dépendances, qui ne sont pas automatiquement collectées et nécessitent donc suivi manuel.

* La surveillance d’Azure Cosmos DB est effectuée automatiquement uniquement si le protocole [HTTP/HTTPS](../../cosmos-db/performance-tips.md#networking) est utilisé. Le mode TCP ne sera pas capturé par Application Insights.
* Redis

Ces dépendances pas automatiquement collectées par le Kit de développement logiciel, vous pouvez suivre manuellement à l’aide du [API TrackDependency](api-custom-events-metrics.md#trackdependency) qui est utilisé par les modules de collecte de standard automatique.

Par exemple, si vous générez votre code avec un assembly que vous n’avez pas écrit vous-même, vous pouvez minuter tous les appels vers cet assembly afin de déterminer sa contribution dans votre temps de réponse. Pour afficher ces données dans les graphiques de dépendance d’Application Insights, envoyez-les en utilisant `TrackDependency`.

```csharp

    var startTime = DateTime.UtcNow;
    var timer = System.Diagnostics.Stopwatch.StartNew();
    try
    {
        // making dependency call
        success = dependency.Call();
    }
    finally
    {
        timer.Stop();
        telemetryClient.TrackDependency("myDependencyType", "myDependencyCall", "myDependencyData",  startTime, timer.Elapsed, success);
    }
```

Vous pouvez également `TelemetryClient` fournit des méthodes d’extension `StartOperation` et `StopOperation` qui peut servir à suivre manuellement les dépendances, comme illustré [ici](custom-operations-tracking.md#outgoing-dependencies-tracking)

Si vous souhaitez désactiver le module de suivi des dépendances standard, supprimez la référence à DependencyTrackingTelemetryModule dans [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) pour les applications ASP.NET. Pour les applications ASP.NET Core, suivez les instructions [ici](asp-net-core.md#configuring-or-removing-default-telemetrymodules).

## <a name="tracking-ajax-calls-from-web-pages"></a>Suivi des appels AJAX à partir de Pages Web

Pour les pages web, SDK JavaScript Application Insights collecte automatiquement les appels AJAX en tant que dépendances décrites [ici](javascript.md#ajax-performance). Ce document se concentre sur les dépendances des composants de serveur.

## <a name="advanced-sql-tracking-to-get-full-sql-query"></a>Avancé pour obtenir la totalité de la requête SQL de suivi SQL

Pour les appels SQL, le nom du serveur et base de données est toujours collecté et stocké en tant que nom de la collectées `DependencyTelemetry`. Il existe un champ supplémentaire appelé « données », qui peuvent contenir le texte de requête SQL complète.

Pour les applications ASP.NET Core, il n’existe aucune étape supplémentaire nécessaire pour obtenir la requête SQL complète.

Pour les applications ASP.NET, la requête SQL complète est collectée à l’aide de l’instrumentation de code octet, ce qui oblige le moteur d’instrumentation. Des étapes supplémentaires spécifiques à la plateforme, comme décrit ci-dessous, sont nécessaires.

| Plateforme | Étapes nécessaires pour obtenir la requête SQL complète |
| --- | --- |
| Application web Azure |Dans le panneau de configuration de web app, [ouvrir le panneau Application Insights](../../azure-monitor/app/azure-web-apps.md) et activer les commandes SQL dans .NET |
| Serveur IIS (machine virtuelle Azure, en local et ainsi de suite.) | [Installez Status Monitor sur votre serveur dans lequel l’application est en cours d’exécution](../../azure-monitor/app/monitor-performance-live-website-now.md) puis redémarrez IIS.
| Service cloud Azure |[Tâche de démarrage utilisation](../../azure-monitor/app/cloudservices.md) à [installez Status Monitor](monitor-performance-live-website-now.md#download) |
| IIS Express | Non pris en charge

Dans les cas ci-dessus, la façon correcte de la validation de ce moteur instrumentation est correctement installé est en validant que la version SDK de collectées `DependencyTelemetry` est « rddp ». 'rdddsd' ou 'rddf' indique les dépendances sont collectées via des rappels DiagnosticSource ou EventSource, et par conséquent, ne sont pas capturée requête SQL complète.

## <a name="where-to-find-dependency-data"></a>Où trouver des données sur les dépendances

* [Mise en correspondance d’applications](app-map.md) visualise les dépendances entre votre application et les composants voisins.
* [Diagnostics de transaction](transaction-diagnostics.md) montre unifiée, mise en corrélation de données du serveur.
* [Les panneaux de navigateurs](javascript.md#ajax-performance) montrent les appels AJAX provenant des navigateurs de vos utilisateurs.
* Parcourez les requêtes lentes ou ayant échoué pour vérifier leurs appels de dépendance.
* Vous pouvez utiliser [Analytics](#analytics) pour interroger des données de dépendances.

## <a name="diagnosis"></a> Diagnostiquer les demandes lentes

Chaque événement de demande est associé aux appels de dépendances, exceptions et autres événements qui sont suivis pendant que votre application traite la demande. Par conséquent, si certaines demandes faites mal, vous trouverez qu’il s’agit en raison d’une dépendance des réponses lentes.

Prenons un exemple.

### <a name="tracing-from-requests-to-dependencies"></a>Traçage des demandes aux dépendances

Ouvrez le panneau Performances et examinez la grille des demandes :

![Liste de demandes avec moyennes et nombres](./media/asp-net-dependencies/02-reqs.png)

Celui supérieur est longue. Examinons-la pour en savoir plus.

Cliquez sur cette ligne pour afficher les événements de la demande :

![Liste des occurrences de demande](./media/asp-net-dependencies/03-instances.png)

Cliquez sur n’importe quelle instance de longue durée pour l’examiner de plus près, et faites défiler la page jusqu’aux appels de dépendances distantes associés à cette demande :

![Rechercher des appels de dépendances distantes, identifier une durée anormale](./media/asp-net-dependencies/04-dependencies.png)

Il semble que la plupart du temps passé au traitement de cette demande ait été consacré à l’appel d’un service local.

Sélectionnez cette ligne pour obtenir plus d’informations :

![Cliquez sur cette dépendance distante pour identifier la cause](./media/asp-net-dependencies/05-detail.png)

Apparemment, cette dépendance est la cause du problème. Maintenant que nous avons identifié le problème, il nous suffit de découvrir pourquoi cet appel est si lent.

### <a name="request-timeline"></a>Chronologie de demande

Dans un autre cas, aucun appel de dépendance n’est particulièrement long. Mais en basculant vers la vue chronologique, nous pouvons voir où le délai s’est produit dans notre traitement interne :

![Rechercher des appels de dépendances distantes, identifier une durée anormale](./media/asp-net-dependencies/04-1.png)

Il semble y avoir un long délai après le premier appel de dépendance. Nous devons examiner notre code pour savoir pourquoi.

### <a name="profile-your-live-site"></a>Profiler votre site en ligne

Vous voulez savoir à quoi tout ce temps a été consacré ? Le [Application Insights profiler](../../azure-monitor/app/profiler.md) traces HTTP appelle vers votre site dynamique et répertorie les fonctions dans votre code qui a pris le temps le plus long.

## <a name="failed-requests"></a>Demandes ayant échoué

Les échecs de demandes peuvent également être associés à des échecs d’appels de dépendances. Là encore, nous pouvons tout parcourir d’un simple clic pour localiser le problème.

![Cliquez sur le graphique des demandes ayant échoué](./media/asp-net-dependencies/06-fail.png)

Accédez à une occurrence d’une demande ayant échoué et examinez les événements associés.

![Cliquez sur un type de demande, cliquez sur l’instance pour obtenir une vue différente de la même instance, cliquez dessus pour obtenir des informations relatives à l’exception.](./media/asp-net-dependencies/07-faildetail.png)

## <a name="analytics"></a>Analytics

Vous pouvez suivre les dépendances dans le [langage de requête Kusto](/azure/kusto/query/). Voici quelques exemples.

* Rechercher les appels de dépendances ayant échoué :

```

    dependencies | where success != "True" | take 10
```

* Rechercher les appels AJAX :

```

    dependencies | where client_Type == "Browser" | take 10
```

* Rechercher les appels de dépendances associés aux demandes :

```

    dependencies
    | where timestamp > ago(1d) and  client_Type != "Browser"
    | join (requests | where timestamp > ago(1d))
      on operation_Id  
```


* Rechercher les appels AJAX associés à des pages consultées :

```

    dependencies
    | where timestamp > ago(1d) and  client_Type == "Browser"
    | join (browserTimings | where timestamp > ago(1d))
      on operation_Id
```

## <a name="video"></a>Vidéo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="frequently-asked-questions"></a>Questions fréquentes (FAQ)

### <a name="how-does-automatic-dependency-collector-report-failed-calls-to-dependencies"></a>*Comment le rapport de collecteur de dépendance automatique effectue échecs d’appels aux dépendances ?*

* Appels de dépendance ayant échoué contiendront champ de « réussite » défini sur False. `DependencyTrackingTelemetryModule` ne signale pas `ExceptionTelemetry`. Décrit le modèle de données complète pour la dépendance de [ici](data-model-dependency-telemetry.md).

## <a name="open-source-sdk"></a>Kit de développement logiciel (SDK) open source
Comme chaque kit SDK Application Insights, module de collecte de dépendance est également open source. Lire et contribuer au code, ou signaler des problèmes à [le référentiel GitHub officiel](https://github.com/Microsoft/ApplicationInsights-dotnet-server).


## <a name="next-steps"></a>Étapes suivantes

* [Exceptions](../../azure-monitor/app/asp-net-exceptions.md)
* [Données utilisateur et de page](../../azure-monitor/app/javascript.md)
* [Disponibilité](../../azure-monitor/app/monitor-web-app-availability.md)
