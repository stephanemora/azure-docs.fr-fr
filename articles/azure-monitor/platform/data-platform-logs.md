---
title: Journaux dans Azure Monitor | Microsoft Docs
description: Décrit les journaux qui sont utilisés dans Azure Monitor pour une analyse avancée des données de surveillance.
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2019
ms.author: bwren
ms.openlocfilehash: 319d43b4096c638eee74031e5b506b5cec3ffd5d
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/19/2020
ms.locfileid: "77467281"
---
# <a name="logs-in-azure-monitor"></a>Journaux dans Azure Monitor

> [!NOTE]
> Toutes les données collectées par Azure Monitor font partie d’un des deux types fondamentaux, Métriques et Journaux. Cet article décrit les Journaux. Voir [Métriques dans Azure Monitor](data-platform-metrics.md) pour obtenir une description détaillée des métriques, et [Surveillance des données collectées par Azure Monitor](data-platform.md) pour obtenir une comparaison des deux.

Les journaux dans Azure Monitor sont particulièrement utiles pour effectuer des analyses complexes de données provenant de diverses sources. Cet article décrit comment les journaux sont structurés dans Azure Monitor, ce que vous pouvez faire des données, et identifie les différentes sources de données qui stockent des données dans des Journaux.

> [!NOTE]
> Il est important de faire la distinction entre les journaux Azure Monitor et les sources des données de journal dans Azure. Par exemple, les événements de niveau abonnement dans Azure sont écrits dans un [journal d’activité](platform-logs-overview.md) que vous pouvez voir à partir du menu Azure Monitor. La plupart des ressources écrivent des informations opérationnelles dans un [journal de ressources](platform-logs-overview.md) que vous pouvez transférer à différents emplacements. Les journaux Azure Monitor constituent une plateforme de données de journal qui collecte des journaux d’activité et des journaux de ressources ainsi que d’autres données de supervision pour fournir une analyse approfondie de l’ensemble de vos ressources.

## <a name="what-are-azure-monitor-logs"></a>Présentation des journaux Azure Monitor

Les journaux dans Azure Monitor contiennent différents types de données organisées en enregistrements, avec différents jeux de propriétés pour chaque type. Les journaux peuvent contenir des valeurs numériques, comme des métriques Azure Monitor, mais ils contiennent généralement des données texte avec des descriptions détaillées. Par ailleurs, ils diffèrent des données métriques en ce qu’ils varient dans leur structure et ne sont souvent pas collectés à intervalles réguliers. Les données de télémétrie, comme les événements et les traces, sont stockées sous forme de journaux Azure Monitor en plus des données de performances, afin qu’elles puissent être combinées pour analyse.

Un événement est un type courant d’entrée de journal collectée de façon sporadique. Des événements sont créés par une application ou un service, et incluent généralement suffisamment d’informations pour fournir un contexte complet à eux seuls. Par exemple, un événement peut indiquer la création ou la modification d’une ressource particulière, le démarrage d’un nouvel hôte en réponse à une augmentation du trafic, ou une erreur détectée dans une application.

 Le format des données pouvant varier, les applications peuvent créer des journaux d’activité personnalisés et structurés de façon appropriée. Il est même possible de stocker des données métriques dans des journaux afin de les combiner avec d’autres données de supervision pour observer des tendances ou mener d’autres analyses de données.


## <a name="what-can-you-do-with-azure-monitor-logs"></a>Que pouvez-vous faire avec les journaux Azure Monitor ?
Le tableau suivant répertorie les différentes façons d’utiliser des journaux dans Azure Monitor.


|  |  |
|:---|:---|
| Analyser | Utiliser [Log Analytics](../log-query/get-started-portal.md) sur le portail Azure pour écrire des [requêtes de journal](../log-query/log-query-overview.md) et analyser de manière interactive des données de journal à l’aide du puissant moteur d’analyse Explorateur de données.<br>Utiliser la [console analytique d’Application Insights](../app/analytics.md) sur le portail Azure pour écrire des requêtes de journal et analyser les données de journal d’Application Insights de manière interactive. |
| Visualisation | Épingler les résultats de requête affichés sous forme de tableaux ou de graphiques dans un [tableau de bord Azure](../../azure-portal/azure-portal-dashboards.md).<br>Créer un [classeur](../app/usage-workbooks.md) à combiner avec plusieurs jeux de données dans un rapport interactif. <br>Exporter les résultats d’une requête vers [Power BI](powerbi.md) pour utiliser différentes visualisations et les partager avec les utilisateurs extérieurs à Azure.<br>Exporter les résultats d’une requête vers [Grafana](grafana-plugin.md) afin de profiter de ses tableaux de bord et de les combiner avec d’autres sources de données.|
| Alerte | Configurer une [règle d’alerte de journal](alerts-log.md) qui envoie une notification ou prend une [action de façon automatique](action-groups.md) lorsque les résultats de la requête correspondent à un résultat spécifique.<br>Configurer une [règle d’alerte de métrique](alerts-metric-logs.md) sur certains journaux de données de journal extraits en tant que métriques. |
| Récupération | Accéder aux résultats de requête de journal à partir d’une ligne de commande à l’aide d’[Azure CLI](/cli/azure/ext/log-analytics/monitor/log-analytics).<br>Accéder aux résultats de requête de journal à partir d’une ligne de commande à l’aide de [cmdlets PowerShell](https://docs.microsoft.com/powershell/module/az.operationalinsights).<br>Accéder aux résultats de requête de journal à partir d’une application personnalisée à l’aide d’une [API REST](https://dev.loganalytics.io/). |
| Exporter | Générer un flux de travail pour récupérer des données de journal et les copier vers un emplacement externe à l’aide de [Logic Apps](~/articles/logic-apps/index.yml). |


## <a name="how-is-data-in-azure-monitor-logs-structured"></a>Comment les données sont-elles structurées dans Azure Monitor ?
Les données collectées par les journaux Azure Monitor sont stockées dans un [espace de travail Log Analytics](../platform/design-logs-deployment.md). Chaque espace de travail contient plusieurs tables qui stockent chacune des données à partir d’une source particulière. Si toutes les tables partagent [des propriétés communes](log-standard-properties.md), chacune a un jeu unique de propriétés unique en fonction du type de données qu’elle stocke. Un nouvel espace de travail comprend un ensemble standard de tables, et d’autres tables seront ajoutées par différentes solutions de surveillance et autres services écrivant dans l’espace de travail.

Les données de journal d’Application Insights utilisent le même moteur Log Analytics que les espaces de travail, mais elles sont stockées séparément pour chaque application surveillée. Chaque application possède un ensemble standard de tables pour stocker des données telles que des demandes, des exceptions et des vues de page d’application.

Les requêtes de journal utiliseront les données d’un espace de travail Log Analytics ou d’une application Application Insights. Vous pouvez utiliser une [requête inter-ressources](../log-query/cross-workspace-query.md) pour analyser des données d’application avec d’autres données de journal, ou pour créer des requêtes comprenant plusieurs espaces de travail ou applications.

![Workspaces](media/data-platform-logs/workspaces.png)

## <a name="log-queries"></a>Requêtes dans les journaux
Les données de journaux Azure Monitor sont récupérées en utilisant une [requête de journal](../log-query/log-query-overview.md) écrite dans le [langage de requête Kusto](../log-query/get-started-queries.md), qui vous permet de rapidement récupérer, consolider et analyser les données collectées. Utilisez [Log Analytics](../log-query/portals.md) pour écrire et tester des requêtes de journal dans le portail Azure. Vous pouvez afficher les résultats de manière interactive ou les épingler au tableau de bord pour les voir avec d’autres visualisations.

![Log Analytics](media/data-platform-logs/log-analytics.png)

Ouvrez [Log Analytics à partir d’Application Insights](../app/analytics.md) pour analyser les données d’Application Insights.

![Application Insights Analytics](media/data-platform-logs/app-insights-analytics.png)

Vous pouvez également récupérer des données de journal à l’aide de l’[API Log Analytics](https://dev.loganalytics.io/documentation/overview) et le [l’API REST Application Insights](https://dev.applicationinsights.io/documentation/overview).


## <a name="sources-of-azure-monitor-logs"></a>Sources de journaux Azure Monitor
Azure Monitor peut collecter des données de journal à partir de diverses sources au sein d’Azure et de ressources locales. Les tableaux suivants répertorient les différentes sources de données disponibles à partir de différentes ressources qui écrivent des données dans des journaux Azure Monitor. Chacune contient un lien vers des détails sur toute configuration requise.

### <a name="azure-tenant-and-subscription"></a>Locataire et abonnement Azure

| Données | Description |
|:---|:---|
| Journaux d’audit Azure Active Directory | Configurés à l’aide de paramètres de diagnostic pour chaque annuaire. Voir [Intégrer des journaux Azure AD aux journaux Azure Monitor](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md).  |
| Journaux d’activité | Par défaut, stockés séparément et utilisables pour des alertes en quasi temps réel. Installez la solution Activity Log Analytics pour écrire dans l’espace de travail Log Analytics. Voir [Collecter et analyser les journaux d’activité Azure dans Log Analytics](activity-log-collect.md). |

### <a name="azure-resources"></a>Ressources Azure

| Données | Description |
|:---|:---|
| Diagnostics de ressources | Configurer les paramètres de diagnostic pour écrire des données de diagnostic, dont des métriques, dans un espace de travail Log Analytics. Consultez [Envoyer en streaming les journaux de ressources Azure à Log Analytics](resource-logs-collect-storage.md). |
| Solutions de supervision | Les solutions de supervision écrivent des données qu’elles collectent dans leur espace de travail Log Analytics. Pour la liste des solutions, voir [Détails sur la collecte de données pour les solutions de gestion dans Azure](../insights/solutions-inventory.md). Pour plus de détails sur l’installation et l’utilisation de solutions, voir [Solutions de monitoring dans Azure Monitor](../insights/solutions.md). |
| Mesures | Envoyer des métriques de plateforme pour des ressources Azure Monitor à un espace de travail Log Analytics afin de conserver les données de journal plus longtemps et d’effectuer une analyse complexe avec d’autres types de données à l’aide du [langage de requête Kusto](/azure/kusto/query/). Consultez [Envoyer en streaming les journaux de ressources Azure à Log Analytics](resource-logs-collect-storage.md). |
| Stockage de table Azure | Collecter des données à partir d’un stockage Azure où certaines ressources Azure écrivent des données de surveillance. Voir [Utiliser un Stockage Blob Azure pour IIS et un Stockage Table Azure pour des événements avec Log Analytics](diagnostics-extension-logs.md). |

### <a name="virtual-machines"></a>Virtual Machines

| Données | Description |
|:---|:---|
|  Sources de données des agents | Les sources de données collectées à partir d’agents [Windows](agent-windows.md) et [Linux](../learn/quick-collect-linux-computer.md) incluent des événements, des données de performances et des journaux personnalisés. Pour obtenir la liste des sources de données et des détails sur la configuration, voir [Sources de données d’agent dans Azure Monitor](data-sources.md). |
| Solutions de supervision | Les solutions de supervision écrivent des données collectées à partir d’agents dans leur espace de travail Log Analytics. Pour la liste des solutions, voir [Détails sur la collecte de données pour les solutions de gestion dans Azure](../insights/solutions-inventory.md). Pour plus de détails sur l’installation et l’utilisation de solutions, voir [Solutions de monitoring dans Azure Monitor](../insights/solutions.md). |
| System Center Operations Manager | Connecter le groupe de gestion Operations Manager à Azure Monitor pour collecter des données d’événements et de performances d’agents locaux dans des journaux. Pour plus d’informations sur cette configuration, voir [Connexion d’Operations Manager à Log Analytics](om-agents.md). |


### <a name="applications"></a>Applications

| Données | Description |
|:---|:---|
| Demandes et exceptions | Les données détaillées sur les demandes et exceptions d’application figurent dans les tableaux _requests_, _pageViews_ et _exceptions_. Les appels à des [composants externes](../app/asp-net-dependencies.md) figurent dans le tableau _dependencies_. |
| Utilisation et performances | Les performances de l’application figurent dans les tableaux _requests_, _browserTimings_ et _performanceCounters_. Les données des [métriques personnalisées](../app/api-custom-events-metrics.md#trackevent) figurent dans le tableau _customMetrics_.|
| Données de trace | Les résultats du [suivi distribué](../app/distributed-tracing.md) sont stockés dans le tableau _traces_. |
| Tests de disponibilité | Les données de synthèse des [tests de disponibilité](../app/monitor-web-app-availability.md) sont stockées dans le tableau _availabilityResults_. Les données détaillées de ces tests figurent dans un stockage distinct accessible à partir d’Application Insights sur le portail Azure. |

### <a name="insights"></a>Insights

| Données | Description |
|:---|:---|
| Azure Monitor pour des conteneurs | Données d’inventaire et de performances collectées par [Azure Monitor pour conteneurs](../insights/container-insights-overview.md). Pour obtenir la liste des tableaux, voir [Détails sur la collecte de données des conteneurs](../insights/container-insights-log-search.md#container-records). |
| Azure Monitor pour machines virtuelles | Données de carte et de performances collectées par [Azure Monitor pour machines virtuelles](../insights/vminsights-overview.md). Pour plus d’informations sur l’interrogation de ces données, voir [Comment interroger des journaux d’activité à partir d’Azure Monitor pour machines virtuelles](../insights/vminsights-log-search.md). |

### <a name="custom"></a>Custom 

| Données | Description |
|:---|:---|
| API REST | Écrire des données dans un espace de travail Log Analytics à partir d’un client REST. Pour plus de détails, voir [Transmettre des données à Azure Monitor avec l’API Collecteur de données HTTP](data-collector-api.md).
| Application logique | Écrire des données dans un espace de travail Log Analytics à partir d’un flux de travail d’application logique avec l’action de **collecteur de données Azure Log Analytics**. |

### <a name="security"></a>Sécurité

| Données | Description |
|:---|:---|
| Azure Security Center | [Azure Security Center](/azure/security-center/) stocke les données qu’il collecte dans un espace de travail Log Analytics où elles peuvent être analysées avec d’autres données de journal. Pour plus d’informations sur la configuration de l’espace de travail, voir [Collecte de données dans Azure Security Center](../../security-center/security-center-enable-data-collection.md). |
| Azure Sentinel | [Azure Sentinel](/azure/sentinel/) stocke les données de sources de données dans un espace de travail Log Analytics. Voir [Connecter des sources de données](/azure/sentinel/connect-data-sources).  |


## <a name="next-steps"></a>Étapes suivantes

- Apprenez-en davantage sur la [plateforme de données Azure Monitor](data-platform.md).
- Découvrez les [métriques dans Azure Monitor](data-platform-metrics.md).
- Découvrez les [données de surveillance disponibles](data-sources.md) pour différentes ressources dans Azure.
