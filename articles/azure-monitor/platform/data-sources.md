---
title: Sources de données dans Azure Monitor | Microsoft Docs
description: Décrit les données disponibles pour surveiller l’intégrité et les performances de vos ressources Azure, et des applications s’exécutant sur celles-ci.
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: monitoring
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/13/2018
ms.author: bwren
ms.openlocfilehash: 1612c2d47f88b6d065e5307be5ff3c99d8f6d405
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/15/2018
ms.locfileid: "53433438"
---
# <a name="sources-of-data-in-azure-monitor"></a>Sources de données dans Azure Monitor
Cet article décrit les sources de données collectées par Azure Monitor pour surveiller l’intégrité et les performances de vos ressources et des applications s’exécutant sur celles-ci. Ces ressources peuvent être dans Azure, dans un autre cloud ou disponibles en local.  Consultez [Monitoring data collected by Azure Monitor](data-collection.md) (Données de supervision collectées par Azure Monitor) pour plus d’informations sur le stockage et l’affichage des données.

Les données de supervision dans Azure proviennent de diverses sources qui peuvent être organisées en niveaux, les niveaux les plus élevés étant votre application et les systèmes d’exploitation, et les niveaux les plus bas étant les composants de la plateforme Azure. Cela est illustré dans le diagramme suivant, et décrit en détail dans les sections suivantes.

![Niveaux de données de surveillance](media/data-sources/monitoring-tiers.png)

## <a name="azure-tenant"></a>Locataire Azure
Les données de télémétrie liées à votre locataire Azure sont collectées à partir des services à l’échelle du locataire, tels qu’Azure Active Directory.

![Collecte de locataire Azure](media/data-sources/tenant-collection.png)

### <a name="azure-active-directory-audit-logs"></a>Journaux d’audit d’Azure Active Directory
Les [rapports Azure Active Directory](../../active-directory/reports-monitoring/overview-reports.md) contiennent l’historique des connexions et la piste d’audit des modifications apportées à un client particulier. Ces journaux d’audit peuvent être écrits dans les journaux Azure Monitor pour être analysés avec d’autres données de journal.


## <a name="azure-platform"></a>Plateforme Azure
Les données de télémétrie liées à l’intégrité et au fonctionnement d’Azure incluent des données relatives à l’exploitation et à la gestion de votre abonnement Azure. Elle inclut des données sur l’intégrité du service stockées dans le journal d’activité Azure et les journaux d’audit d’Azure Active Directory.

![Collecte d’abonnement Azure](media/data-sources/azure-collection.png)

### <a name="azure-service-health"></a>Azure Service Health
[Azure Service Health](../../monitoring-and-diagnostics/monitoring-service-notifications.md) fournit des informations sur l’intégrité des services Azure compris dans votre abonnement, dont dépendent votre application et vos ressources. Vous pouvez créer des alertes pour être informé des problèmes critiques actuels et prévus susceptibles d’affecter votre application. Les enregistrements de Service Health étant stockés dans le [journal d’activité Azure](../../monitoring-and-diagnostics/monitoring-overview-activity-logs.md), vous pouvez les afficher dans l’Explorateur du journal d’activité et les copier dans les journaux Azure Monitor.

### <a name="azure-activity-log"></a>Journaux d’activité
Le [journal d’activité Azure](../../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) comprend des enregistrements relatifs à l’intégrité du service ainsi qu’aux changements de configuration apportés à vos ressources Azure. Le journal d’activité est disponible pour toutes les ressources Azure et représente leur affichage _externe_. Les types d’enregistrements spécifiques dans le journal d’activité sont décrits dans [Schéma d’événement du journal d’activité Azure](../../azure-monitor/platform/activity-log-schema.md).

Vous pouvez afficher le journal d'activité d’une ressource particulière dans sa page sur le portail Azure, ou les journaux de plusieurs ressources dans [l’Explorateur du journal d’activité](../../monitoring-and-diagnostics/monitoring-overview-activity-logs.md). Il est particulièrement utile de copier les entrées de journal sur Azure Monitor pour les combiner avec d’autres données de supervision. Vous pouvez également les envoyer à d’autres emplacements à l’aide d’[Event Hubs](../../monitoring-and-diagnostics/monitoring-stream-activity-logs-event-hubs.md).



## <a name="azure-services"></a>Services Azure
Les journaux de diagnostic au niveau métriques et ressources fournissent des informations sur le fonctionnement _interne_ des ressources Azure. Ils sont disponibles pour la plupart des services Azure, et les solutions de gestion fournissent des informations supplémentaires sur des services particuliers.

![Collection de ressources Azure](media/data-sources/azure-resource-collection.png)


### <a name="metrics"></a>Mesures
La plupart des services Azure génèrent des [métriques de plateforme](data-collection.md#metrics) reflétant leurs performances et leur fonctionnement. Les [métriques spécifiques varient pour chaque type de ressource](../../azure-monitor/platform/metrics-supported.md).  Elles sont accessibles à partir de Metrics Explorer, et peuvent être copiées vers Log Analytics à des fins d’observation de tendances et d’autres analyses.


### <a name="resource-diagnostic-logs"></a>Journaux de diagnostic des ressources
Tandis que le journal d’activité fournit des informations sur les opérations effectuées sur une ressource Azure, les [journaux de diagnostic](../../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) au niveau ressource fournissent des informations sur le fonctionnement de la ressource proprement dite.   La configuration requise et le contenu de ces journaux [varie selon le type de ressource](../../azure-monitor/platform/tutorial-dashboards.md).

Vous ne pouvez pas afficher directement les journaux de diagnostic dans le portail Azure, mais vous pouvez les [envoyer au Stockage Azure à des fins d’archivage](../../azure-monitor/platform/archive-diagnostic-logs.md), et les exporter vers [Event Hub](../../event-hubs/event-hubs-about.md) en vue de leur redirection vers d’autres services, ou vers [Log Analytics](../../monitoring-and-diagnostics/monitor-stream-diagnostic-logs-log-analytics.md) à des fins d’analyse. Certaines ressources peuvent écrire directement dans Log Analytics, tandis que d’autres écrivent dans un compte de stockage avant d’être [importées dans Log Analytics](../../azure-monitor/platform/azure-storage-iis-table.md#use-the-azure-portal-to-collect-logs-from-azure-storage).

### <a name="monitoring-solutions"></a>Solutions de supervision
 Les [solutions de supervision](../insights/solutions.md) collectent des données pour fournir des insights supplémentaires sur le fonctionnement d’un service ou d’une application particuliers. Elles recueillent des données dans les journaux Azure Monitor, qui peuvent les analyser à l’aide du [langage de requête](../log-query/log-query-overview.md), ou dans des [affichages](view-designer.md) qui sont généralement inclus dans la solution.

## <a name="guest-operating-system"></a>Système d’exploitation invité
Les ressources de calcul dans Azure, dans d’autres clouds et en local ont un système d’exploitation invité à surveiller. Avec l’installation d’un ou plusieurs agents, vous pouvez collecter la télémétrie de l’invité dans les outils de surveillance qu’utilisent les services Azure eux-mêmes.

![Collection de ressources de calcul Azure](media/data-sources/compute-resource-collection.png)

### <a name="azure-diagnostic-extension"></a>Extension Azure Diagnostics
L’extension Azure Diagnostics fournit un niveau de surveillance de base en collectant les journaux et les données de performance du système d’exploitation client des ressources de calcul Azure.   

### <a name="log-analytics-agent"></a>Agent Log Analytics
L’agent Log Analytics permet de superviser et de gérer en détail vos machines virtuelles ou votre ordinateur physique Windows ou Linux. La machine virtuelle peut s’exécuter dans Azure, dans un autre cloud ou en local tandis que l’agent se connecte à Azure Monitor, directement ou par l’intermédiaire de System Center Operations Manager, pour vous permettre de collecter des données depuis les [sources de données](agent-data-sources.md) que vous configurez, ou depuis des [solutions de supervision](../insights/solutions.md) qui fournissent des informations supplémentaires sur les applications exécutées sur la machine virtuelle.

### <a name="dependency-agent"></a>Agent de dépendances
[Service Map](../insights/service-map.md) et [Azure Monitor pour machines virtuelles](../insights/vminsights-overview.md) nécessitent une instance de Dependency Agent sur les machines virtuelles Windows et Linux. Celle-ci opère avec l’agent Log Analytics pour collecter les données découvertes sur les processus exécutés sur la machine virtuelle et les dépendances de processus externes. Ces données sont stockées dans Azure Monitor et les composants interconnectés découverts sont visualisés.  

Pour mieux comprendre les différences entre les agents et savoir lequel utiliser en fonction de vos besoins de surveillance, consultez [Vue d’ensemble des agents de surveillance](agents-overview.md).

## <a name="applications"></a>APPLICATIONS
En plus de la télémétrie que votre application peut écrire dans le système d’exploitation invité, la surveillance détaillée de l’application est effectuée avec [Application Insights](https://docs.microsoft.com/azure/application-insights/). Application Insights peut collecter des données à partir d’applications s’exécutant sur diverses plateformes. L’application peut s’exécuter dans Azure, dans un autre cloud ou localement.

![Collecte de données d’application](media/data-sources/application-collection.png)


### <a name="application-data"></a>Données d'application
Lorsque vous activez Application Insights pour une application en installant un package d’instrumentation, la solution collecte des métriques et journaux liés aux performances et au fonctionnement de l’application. Cela inclut des informations détaillées sur les vues de page, les demandes d’application et les exceptions. Application Insights stocke les données collectées dans Azure Monitor. La solution inclut des outils complets pour analyser ces données, mais vous pouvez également les analyser avec des données provenant d’autres sources à l’aide d’outils tels que Metric Analytics et Log Analytics.

Vous pouvez également utiliser Application Insights pour [créer une métrique personnalisée](../../application-insights/app-insights-api-custom-events-metrics.md).  Cela vous permet de définir votre propre logique pour calculer une valeur numérique, puis stocker celle-ci avec d’autres métriques accessibles à partir de Metrics Explorer et utilisables pour la [Mise à l'échelle automatique](../../azure-monitor/platform/autoscale-custom-metric.md) et les alertes Métrique.

### <a name="dependencies"></a>Les dépendances
Pour surveiller les différentes opérations logiques d’une application, vous devez [collecter la télémétrie de plusieurs composants](../../application-insights/app-insights-transaction-diagnostics.md). Application Insights prend en charge une [corrélation de télémétrie distribuée](../../application-insights/application-insights-correlation.md) qui identifie les dépendances entre les composants, ce qui vous permet de les analyser ensemble.

### <a name="availability-tests"></a>Tests de disponibilité
Les [tests de disponibilité](../../application-insights/app-insights-monitor-web-app-availability.md) dans Application Insights vous permettent de tester la disponibilité et la réactivité de votre application à partir de différents emplacements sur l’Internet public. Vous pouvez effectuer un simple test ping pour vérifier que l’application est active, ou utiliser Visual Studio pour créer un test web simulant un scénario utilisateur.  Les tests de disponibilité ne nécessitent aucune instrumentation dans l’application.

## <a name="custom-sources"></a>Sources personnalisées
Outre les niveaux standard d’une application, vous devrez peut-être surveiller d’autres ressources qui ont des données de télémétrie qui ne peuvent pas être collectées avec les autres sources de données. Pour ces ressources, vous devez écrire ces données à l’aide d’une API Azure Monitor.

![Collecte de données personnalisée](media/data-sources/custom-collection.png)

### <a name="data-collector-api"></a>API du collecteur de données
Azure Monitor peut collecter des données de journal à partir de n’importe quel client REST à l’aide de [l’API de collecteur de données](data-collector-api.md). Cela vous permet de créer des scénarios de supervision personnalisés et d’étendre la supervision à des ressources qui n’exposent pas de données de télémétrie via d’autres sources.

## <a name="next-steps"></a>Étapes suivantes

- Apprenez-en davantage sur les [types de données de supervision collectés par Azure Monitor](data-collection.md) et sur l’affichage et l’analyse de ces données.
