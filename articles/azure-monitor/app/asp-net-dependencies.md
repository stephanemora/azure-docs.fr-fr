---
title: Suivi des dépendances dans Azure Application Insights | Microsoft Docs
description: Analysez l'utilisation, la disponibilité et les performances de votre application web locale ou Microsoft Azure avec Application Insights.
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
ms.openlocfilehash: 4aa18ae791e5fa573eae76d5bdb9c45b9311e6b5
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2019
ms.locfileid: "56888081"
---
# <a name="set-up-application-insights-dependency-tracking"></a>Configurer Application Insights : Suivi des dépendances
Un *dépendance* est un composant externe qui est appelé par votre application. Il s’agit habituellement d’un service appelé à l’aide de HTTP, d’une base de données ou d’un système de fichiers. [Application Insights](../../azure-monitor/app/app-insights-overview.md) mesure combien de temps votre application attend les dépendances et la fréquence à laquelle un appel de dépendance échoue. Vous pouvez examiner des appels spécifiques et les associer à des demandes et des exceptions.

Le moniteur de dépendance prêt à l’emploi signale les appels aux types de dépendances suivants :

* Serveur
  * Bases de données SQL
  * Services web et WCF d’ASP.NET qui utilisent des liaisons HTTP
  * Appels HTTP locaux ou distants
  * Azure Cosmos DB, table, stockage d’objets blob et file d’attente 
* Pages web
  * Appels AJAX

La surveillance fonctionne à l’aide de [l’instrumentation de code octet](https://msdn.microsoft.com/library/z9z62c29.aspx) autour des méthodes de sélection ou en fonction des rappels DiagnosticSource (dans les derniers SDK .NET) à partir de .NET Framework. La surcharge de performances est minime.

Vous pouvez écrire vos propres appels de SDK pour surveiller d’autres dépendances, à la fois dans le code client et serveur, à l’aide de l’[API TrackDependency](../../azure-monitor/app/api-custom-events-metrics.md#trackdependency).

> [!NOTE]
> La surveillance d’Azure Cosmos DB est effectuée automatiquement uniquement si le protocole [HTTP/HTTPS](../../cosmos-db/performance-tips.md#networking) est utilisé. Le mode TCP ne sera pas capturé par Application Insights.

## <a name="set-up-dependency-monitoring"></a>Configurer la surveillance des dépendances
Les informations sur les dépendances partielles sont collectées automatiquement par le [SDK Application Insights](asp-net.md). Pour obtenir des données complètes, installez l’agent approprié pour le serveur hôte.

| Plateforme | Installer |
| --- | --- |
| Serveur IIS |[Installez Status Monitor sur votre serveur](../../azure-monitor/app/monitor-performance-live-website-now.md) ou [mettez à niveau votre application avec .NET Framework version 4.6 ou ultérieure](https://go.microsoft.com/fwlink/?LinkId=528259) et installez le [SDK Application Insights](asp-net.md) dans votre application. |
| Application web Azure |Dans le panneau de configuration de votre application web, [ouvrez le panneau Application Insights](../../azure-monitor/app/azure-web-apps.md) et choisissez l’installation si vous y êtes invité. |
| Service cloud Azure |[Utilisez une tâche de démarrage](../../azure-monitor/app/cloudservices.md) ou [installez le .NET Framework version 4.6 ou ultérieure](../../cloud-services/cloud-services-dotnet-install-dotnet.md). |

## <a name="where-to-find-dependency-data"></a>Où trouver des données sur les dépendances
* [Mise en correspondance d’applications](#application-map) visualise les dépendances entre votre application et les composants voisins.
* [Les panneaux de performances, de navigateurs et d’échecs](#performance-and-failure-blades) affichent les données sur les dépendances de serveur.
* [Les panneaux de navigateurs](#ajax-calls) montrent les appels AJAX provenant des navigateurs de vos utilisateurs.
* Parcourez les requêtes lentes ou ayant échoué pour vérifier leurs appels de dépendance.
* Vous pouvez utiliser [Analytics](#analytics) pour interroger des données de dépendances.

## <a name="application-map"></a>Mise en correspondance d'applications
Mise en correspondance d'applications fonctionne comme une aide visuelle pour découvrir les dépendances entre les composants de votre application. Il est généré automatiquement à partir de la télémétrie de votre application. Cet exemple montre les appels AJAX provenant des scripts de navigateur et les appels REST de l’application serveur à deux services externes.

![Mise en correspondance d'applications](./media/asp-net-dependencies/08.png)

* **Accédez à partir des zones** aux graphiques de dépendances pertinents et d’autres graphiques.
* **Épinglez la mise en correspondance** sur le [tableau de bord](../../azure-monitor/app/app-insights-dashboards.md), où elle sera entièrement opérationnelle.

[En savoir plus](../../azure-monitor/app/app-map.md).

## <a name="performance-and-failure-blades"></a>Panneaux de performances et d’échecs
Le panneau de performances indique la durée des appels de dépendances effectués par l’application serveur. Il existe un graphique de synthèse et un tableau segmenté par appel.

![Graphiques de dépendances du panneau de performances](./media/asp-net-dependencies/dependencies-in-performance-blade.png)

Parcourez les graphiques de synthèses ou les éléments du tableau pour rechercher les occurrences brutes de ces appels.

![Instances d’appels de dépendances](./media/asp-net-dependencies/dependency-call-instance.png)

Le **Nombre d’échecs** est affiché dans le panneau **Échecs**. Un échec est tout code de retour non compris dans la plage 200-399 ou inconnu.

> [!NOTE]
> **100 % d’échecs ?** Cela signifie probablement que vous obtenez uniquement des données de dépendances partielles. Vous devez [configurer une surveillance des dépendances adaptée à votre plateforme](#set-up-dependency-monitoring).
>
>

## <a name="ajax-calls"></a>Appels AJAX
Le panneau Navigateurs affiche la durée et le taux d’échec des appels AJAX à partir de [JavaScript dans vos pages web](../../azure-monitor/app/javascript.md). Ils sont affichés en tant que dépendances.

## <a name="diagnosis"></a> Diagnostiquer les demandes lentes
Chaque événement de demande est associé aux appels de dépendances, exceptions et autres événements qui sont suivis pendant que votre application traite la demande. Ainsi, si certaines demandes présentent de médiocres performances, vous pouvez savoir si cela est dû à la lenteur des réponses d’une dépendance.

Prenons un exemple.

### <a name="tracing-from-requests-to-dependencies"></a>Traçage des demandes aux dépendances
Ouvrez le panneau Performances et examinez la grille des demandes :

![Liste de demandes avec moyennes et nombres](./media/asp-net-dependencies/02-reqs.png)

La durée de la première est très longue. Examinons-la pour en savoir plus.

Cliquez sur cette ligne pour afficher les événements de la demande :

![Liste des occurrences de demande](./media/asp-net-dependencies/03-instances.png)

Cliquez sur n’importe quelle instance de longue durée pour l’examiner de plus près, et faites défiler la page jusqu’aux appels de dépendances distantes associés à cette demande :

![Rechercher des appels de dépendances distantes, identifier une durée anormale](./media/asp-net-dependencies/04-dependencies.png)

Il semble que la plupart du temps passé au traitement de cette demande ait été consacré à l’appel d’un service local.

Sélectionnez cette ligne pour obtenir plus d’informations :

![Cliquez sur cette dépendance distante pour identifier la cause](./media/asp-net-dependencies/05-detail.png)

Il semblerait que ce soit la cause du problème. Maintenant que nous avons identifié le problème, il nous suffit de découvrir pourquoi cet appel est si lent.

### <a name="request-timeline"></a>Chronologie de demande
Dans un autre cas, aucun appel de dépendance n’est particulièrement long. Mais en basculant vers la vue chronologique, nous pouvons voir où le délai s’est produit dans notre traitement interne :

![Rechercher des appels de dépendances distantes, identifier une durée anormale](./media/asp-net-dependencies/04-1.png)

Il semble y avoir un long délai après le premier appel de dépendance. Nous devons examiner notre code pour savoir pourquoi.

### <a name="profile-your-live-site"></a>Profiler votre site en ligne

Vous voulez savoir à quoi tout ce temps a été consacré ? Le [profileur d’Application Insights](../../azure-monitor/app/profiler.md) effectue le suivi des appels HTTP vers votre site dynamique et vous indique les fonctions de votre code qui ont pris le plus de temps.

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



## <a name="custom-dependency-tracking"></a>Suivi personnalisé des dépendances
Le module de suivi des dépendances standard découvre automatiquement les dépendances externes, telles que des bases de données et des API REST. Mais vous souhaiterez peut-être traiter d’autres composants de la même façon.

Vous pouvez écrire du code qui envoie des informations de dépendance, en utilisant la même [API TrackDependency](../../azure-monitor/app/api-custom-events-metrics.md#trackdependency) que celle utilisée par les modules standard.

Par exemple, si vous générez votre code avec un assembly que vous n’avez pas écrit vous-même, vous pouvez minuter tous les appels vers cet assembly afin de déterminer sa contribution dans votre temps de réponse. Pour afficher ces données dans les graphiques de dépendance d’Application Insights, envoyez-les en utilisant `TrackDependency`.

```csharp

            var startTime = DateTime.UtcNow;
            var timer = System.Diagnostics.Stopwatch.StartNew();
            try
            {
                success = dependency.Call();
            }
            finally
            {
                timer.Stop();
                telemetry.TrackDependency("myDependency", "myCall", startTime, timer.Elapsed, success);
                // The call above has been made obsolete in the latest SDK. The updated call follows this format:
                // TrackDependency (string dependencyTypeName, string dependencyName, string data, DateTimeOffset startTime, TimeSpan duration, bool success);
            }
```

Si vous souhaitez désactiver le module de suivi des dépendances standard, supprimez la référence à DependencyTrackingTelemetryModule dans [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md).

## <a name="troubleshooting"></a>Résolution de problèmes
*L’indicateur de réussite de la dépendance affiche toujours True ou False.*

*La requête SQL n’est pas affichée en entier.*

Consultez le tableau ci-dessous et vérifiez que vous avez choisi la configuration appropriée afin d’activer la surveillance des dépendances pour votre application.

| Plateforme | Installer |
| --- | --- |
| Serveur IIS |[Installez Status Monitor sur votre serveur](../../azure-monitor/app/monitor-performance-live-website-now.md). Ou [mettez à niveau votre application avec .NET Framework version 4.6 ou ultérieure](https://go.microsoft.com/fwlink/?LinkId=528259) et installez le [SDK Application Insights](asp-net.md) dans votre application. |
| IIS Express |Utilisez plutôt le serveur IIS. |
| Application web Azure |Dans le panneau de configuration de votre application web, [ouvrez le panneau Application Insights](../../azure-monitor/app/azure-web-apps.md) et choisissez l’installation si vous y êtes invité. |
| Service cloud Azure |[Utilisez une tâche de démarrage](../../azure-monitor/app/cloudservices.md) ou [installez .NET Framework version 4.6 ou ultérieure](../../cloud-services/cloud-services-dotnet-install-dotnet.md). |

## <a name="video"></a>Vidéo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>Étapes suivantes
* [Exceptions](../../azure-monitor/app/asp-net-exceptions.md)
* [Données utilisateur et de page](../../azure-monitor/app/javascript.md)
* [Disponibilité](../../azure-monitor/app/monitor-web-app-availability.md)
