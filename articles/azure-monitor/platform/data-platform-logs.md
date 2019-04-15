---
title: Journaux dans Azure Monitor | Microsoft Docs
description: Décrit les journaux dans Azure Monitor qui sont utilisés pour une analyse avancée des données d’analyse.
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: monitoring
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 0203/26/2019
ms.author: bwren
ms.openlocfilehash: 59213c5391b5b652eeead05c4a5af761571fcece
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/09/2019
ms.locfileid: "59360630"
---
# <a name="logs-in-azure-monitor"></a>Journaux dans Azure Monitor

> [!NOTE]
> Toutes les données collectées par Azure Monitor s’adapte à un des deux types fondamentaux, mesures et journaux. Cet article décrit les journaux. Reportez-vous à [métriques dans Azure Monitor](data-platform-metrics.md) pour obtenir une description détaillée des mesures et en [analyse les données collectées par Azure Monitor](data-platform.md) pour obtenir une comparaison des deux.

Les journaux dans Azure Monitor sont particulièrement utiles pour effectuer des analyses complexes les données provenant de diverses sources. Cet article décrit comment les journaux sont structurées dans Azure Monitor, ce que vous pouvez faire avec les données et identifie les différentes sources de données qui stockent les données dans les journaux.

> [!NOTE]
> Il est important de faire la distinction entre les journaux d’Azure Monitor et les sources de données de journal dans Azure. Par exemple, les événements de niveau d’abonnement dans Azure sont écrits dans un [journal d’activité](activity-logs-overview.md) que vous pouvez afficher dans le menu Azure Monitor. La plupart des ressources seront consignent des informations opérationnelles pour un [journal de diagnostic](diagnostic-logs-overview.md) que vous pouvez transférer à différents emplacements. Journaux d’Azure Monitor est une plateforme de données de journal qui collecte des journaux d’activité et les journaux de diagnostic, ainsi que d’autres données d’analyse pour fournir une analyse approfondie sur votre ensemble de ressources.

## <a name="what-are-azure-monitor-logs"></a>Quelles sont les journaux d’Azure Monitor ?

Journaux dans Azure Monitor contiennent différents types de données organisées en enregistrements avec différents jeux de propriétés pour chaque type. Journaux peuvent contenir des valeurs numériques comme mesures Azure Monitor, mais contiennent généralement des données de texte avec une description détaillée. Elles diffèrent davantage de données de mesure car ils varient dans leur structure et ne sont souvent pas collectés à intervalles réguliers. Données de télémétrie comme événements et suivis sont stockés les journaux Azure Monitor en plus des données de performances afin qu’il puisse tous être combinées pour l’analyse.

Un type commun de l’entrée de journal est un événement qui est collecté sporadique. Événements sont créés par une application ou un service et incluent généralement suffisamment d’informations pour fournir un contexte complet sur leurs propres. Par exemple, un événement peut indiquer la création ou la modification d’une ressource particulière, le démarrage d’un nouvel hôte en réponse à une augmentation du trafic, ou une erreur détectée dans une application.

 Le format des données pouvant varier, les applications peuvent créer des journaux d’activité personnalisés et structurés de façon appropriée. Données de mesure peuvent même être stockées dans les journaux à les combiner avec d’autres données d’analyse des tendances et d’autres analyses de données.


## <a name="what-can-you-do-with-azure-monitor-logs"></a>Que pouvez-vous faire avec les journaux d’Azure Monitor ?
Le tableau suivant répertorie les différentes façons, que vous pouvez utiliser les journaux dans Azure Monitor.


|  |  |
|:---|:---|
| Analyser | Utilisez [Analytique de journal](../log-query/get-started-portal.md) dans le portail Azure pour écrire [enregistrer des requêtes](../log-query/log-query-overview.md) et analyser les données de journal avec le puissant moteur d’analyse de l’Explorateur de données de manière interactive.<br>Utilisez le [console d’analytique Application Insights](../app/analytics.md) dans le portail Azure pour écrire des requêtes de journal et analyser les données du journal d’Application Insights de manière interactive. |
| Visualisation | Épingler les résultats de la requête rendus sous forme de tableaux ou graphiques pour une [tableau de bord Azure](../../azure-portal/azure-portal-dashboards.md).<br>Créer un [classeur](../app/usage-workbooks.md) à combiner avec plusieurs jeux de données dans un rapport interactif. <br>Exporter les résultats d’une requête vers [Power BI](powerbi.md) pour utiliser différentes visualisations et les partager avec les utilisateurs extérieurs à Azure.<br>Exporter les résultats d’une requête pour [Grafana](grafana-plugin.md) à exploiter ses tableaux de bord et à combiner avec d’autres sources de données.|
| Alerte | Configurer une [règle d’alerte de journal](alerts-log.md) qui envoie une notification ou prend une [action de façon automatique](action-groups.md) lorsque les résultats de la requête correspondent à un résultat spécifique.<br>Configurer un [règle d’alerte métrique](alerts-metric-logs.md) sur certains journaux de données de fichier journal extraits comme des métriques. |
| Récupération | Accéder aux résultats de requête de journal à partir d’une ligne de commande à l’aide [Azure CLI](/cli/azure/ext/log-analytics/monitor/log-analytics).<br>Accéder aux résultats de requête de journal à partir d’une ligne de commande à l’aide [applets de commande PowerShell](https://docs.microsoft.com/powershell/module/az.operationalinsights).<br>Accéder aux résultats de requête de journal à partir d’une application personnalisée à l’aide [API REST](https://dev.loganalytics.io/). |
| Exportation | Générer un flux de travail pour récupérer des données de journal et le copier à un emplacement externe avec [Logic Apps](~/articles/logic-apps/index.yml). |


## <a name="how-is-data-in-azure-monitor-logs-structured"></a>Comment sont données dans les journaux d’Azure Monitor structurée ?
Les données collectées par les journaux d’Azure Monitor sont stockées dans un [espace de travail Analytique de journal](../platform/manage-access.md). Vous pouvez [créer plusieurs espaces de travail](manage-access.md#determine-the-number-of-workspaces-you-need) dans votre abonnement pour gérer différents ensembles de données de journal. Chaque espace de travail contient plusieurs tables qui contiennent chacune des données à partir d’une source particulière. Tandis que toutes les tables partagent [certaines propriétés communes](log-standard-properties.md), chacun a un jeu unique de propriétés en fonction du type de données qu’il contient. Un espace de travail aura un ensemble standard de tables et les tables supplémentaires seront ajoutés par différentes solutions de surveillance et d’autres services qui écrivent dans l’espace de travail.

Données du journal d’Application Insights utilisent le même moteur Analytique de journal en tant qu’espaces de travail, mais elles sont stockées séparément pour chaque application analysée. Chaque application possède un ensemble standard de tables pour stocker les données telles que les demandes d’application, les exceptions et les vues de page.

Requêtes de journal seront soit utiliser les données à partir d’un espace de travail Analytique de journal ou une application Application Insights. Vous pouvez utiliser un [inter-ressources requête](../log-query/cross-workspace-query.md) pour analyser les données d’application ainsi que d’autres données de journal ou pour créer des requêtes, y compris plusieurs espaces de travail ou applications.

![Workspaces](media/data-platform-logs/workspaces.png)

## <a name="log-queries"></a>Requêtes dans les journaux
Dans les journaux d’Azure Monitor sont extraites à l’aide un [requête de journal](../log-query/log-query-overview.md) écrit avec le [langage de requête Kusto](../log-query/get-started-queries.md), ce qui vous permet rapidement récupérer, consolider et analyser les données collectées. Utilisez [Analytique de journal](../log-query/portals.md) pour écrire et tester des requêtes de journal dans le portail Azure. Il vous permet de travailler avec les résultats de manière interactive ou les épingler au tableau de bord pour les afficher avec les autres visualisations.

![Log Analytics](media/data-platform-logs/log-analytics.png)

Ouvrez [Analytique de journal d’Application Insights](../app/analytics.md) pour analyser les données d’Application Insights.

![Application Insights Analytics](media/data-platform-logs/app-insights-analytics.png)

Vous pouvez également récupérer des données de journal à l’aide de la [journal Analytique API](https://dev.loganalytics.io/documentation/overview) et le [l’API REST Insights Application](https://dev.applicationinsights.io/documentation/overview).


## <a name="sources-of-azure-monitor-logs"></a>Sources de journaux Azure Monitor
Azure Monitor peut collecter des données de journal à partir de diverses sources au sein d’Azure et de ressources locales. Les tableaux suivants répertorient les différentes sources de données disponibles à partir de différentes ressources qui écrivent des données dans les journaux d’Azure Monitor. Chacun a un lien vers plus d’informations sur toute configuration requise.

### <a name="azure-tenant-and-subscription"></a>Abonnement et client azure

| Données | Description |
|:---|:---|
| Journaux d’audit Azure Active Directory | Configuré via les paramètres de Diagnostic pour chaque répertoire. Consultez [journaux d’intégration Azure AD avec Azure Monitor journaux](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md).  |
| Journaux d’activité | Par défaut, stockés séparément et peut être utilisé pour les alertes en temps réel en temps quasi. Installez la solution d’Analytique de journal d’activité pour écrire dans l’espace de travail Analytique de journal. Consultez [collecter et analyser les journaux d’activité Azure dans Log Analytique](collect-activity-logs.md). |

### <a name="azure-resources"></a>Ressources Azure

| Données | Description |
|:---|:---|
| Diagnostic des ressources | Configurer les paramètres de Diagnostic pour écrire des données de diagnostic, y compris les mesures à un espace de travail Analytique de journal. Consultez [Stream des journaux de Diagnostic Azure pour l’Analytique de journal](diagnostic-logs-stream-log-store.md). |
| Solutions de supervision | Solutions de surveillance écrivent des données qu’elles collectent à leur espace de travail Analytique de journal. Consultez [détails pour les solutions de gestion dans Azure de la collecte de données](../insights/solutions-inventory.md) pour obtenir la liste de solutions. Consultez [surveillance des solutions dans Azure Monitor](../insights/solutions.md) pour plus d’informations sur l’installation et l’utilisation de solutions. |
| Mesures | Envoyer des mesures de plate-forme pour les ressources d’Azure Monitor à un espace de travail Analytique de journal pour conserver les données de journal pour des périodes plus longues et pour effectuer une analyse complexe avec d’autres types de données à l’aide de la [langage de requête Kusto](/azure/kusto/query/). Consultez [Stream des journaux de Diagnostic Azure pour l’Analytique de journal](diagnostic-logs-stream-log-store.md). |
| Stockage de table Azure | Collecter des données à partir du stockage Azure où certaines ressources Azure écrire les données de surveillance. Consultez [stockage d’objets blob Azure de l’utilisation d’IIS et table le stockage Azure pour les événements avec Analytique de journal](azure-storage-iis-table.md). |

### <a name="virtual-machines"></a>Virtual Machines

| Données | Description |
|:---|:---|
|  Sources de données des agents | Sources de données collectées à partir de [Windows](agent-windows.md) et [Linux](../learn/quick-collect-linux-computer.md) agents incluent les événements, les données de performances et les journaux personnalisés. Consultez [sources de données de l’Agent dans Azure Monitor](data-sources.md) pour obtenir la liste des sources de données et des détails sur la configuration. |
| Solutions de supervision | Solutions de surveillance écrivent des données collectées à partir des agents à leur espace de travail Analytique de journal. Consultez [détails pour les solutions de gestion dans Azure de la collecte de données](../insights/solutions-inventory.md) pour obtenir la liste de solutions. Consultez [surveillance des solutions dans Azure Monitor](../insights/solutions.md) pour plus d’informations sur l’installation et l’utilisation de solutions. |
| System Center Operations Manager | Connecter le groupe d’administration Operations Manager à Azure Monitor pour collecter des données de performances et d’événements à partir d’agents locaux dans les journaux. Consultez [connecter Operations Manager à Log Analytique](om-agents.md) pour plus d’informations sur cette configuration. |


### <a name="applications"></a>APPLICATIONS

| Données | Description |
|:---|:---|
| Demandes et exceptions | Les données détaillées sur les demandes d’application et les exceptions sont dans le _demandes_, _pageViews_, et _exceptions_ tables. Les appels à [composants externes](../app/asp-net-dependencies.md) se trouvent dans le _dépendances_ table. |
| Performances et utilisation | Performances de l’application sont disponible dans le _demandes_, _browserTimings_ et _performanceCounters_ tables. Les données de [mesures personnalisées](../app/api-custom-events-metrics.md#trackevent) est dans le _customMetrics_ table.|
| Les données de trace | Résulte de [traçage distribué](../app/distributed-tracing.md) sont stockés dans le _traces_ table. |
| Tests de disponibilité | Données de synthèse provenant [tests de disponibilité](../app/monitor-web-app-availability.md) est stocké dans le _availabilityResults_ table. Les données détaillées à partir de ces tests sont dans un stockage distinct et accessible à partir de l’Application Insights dans le portail Azure. |

### <a name="insights"></a>Insights

| Données | Description |
|:---|:---|
| Azure Monitor pour les conteneurs | Données d’inventaire et de performances collectées par [Azure Monitor pour les conteneurs](../insights/container-insights-overview.md). Consultez [détails de collecte de données de conteneur](../insights/container-insights-analyze.md#container-data-collection-details) pour obtenir la liste des tables. |
| Azure Monitor pour machines virtuelles | Données de carte et de performances collectées par [Azure Monitor pour les machines virtuelles](../insights/vminsights-overview.md). Consultez [comment interroger des journaux à partir d’Azure Monitor pour les machines virtuelles](../insights/vminsights-log-search.md) pour plus d’informations sur l’interrogation de ces données. |

### <a name="custom"></a>Personnalisée 

| Données | Description |
|:---|:---|
| API REST | Écrire des données dans un espace de travail Analytique de journal à partir de n’importe quel client REST. Consultez [envoie des données de journal vers Azure Monitor avec l’API de collecte de données HTTP](data-collector-api.md) pour plus d’informations.
| Application logique | Écrire des données à un espace de travail Analytique de journal à partir d’un flux de travail d’application logique avec le **collecteur de données Azure Log Analytique** action. |

### <a name="security"></a>Sécurité

| Données | Description |
|:---|:---|
| Azure Security Center | [Azure Security Center](/azure/security-center/) stocke les données qu’il collecte dans un espace de travail Analytique de journal où elles peuvent être analysées avec d’autres données de journal. Consultez [collecte des données dans Azure Security Center](../../security-center/security-center-enable-data-collection.md) pour plus d’informations sur la configuration de l’espace de travail. |
| Azure Sentinel | [Azure Sentinel](/azure/sentinel/) stocke les données à partir de sources de données dans un espace de travail Analytique de journal. Consultez [connecter des sources de données](/azure/sentinel/connect-data-sources).  |


## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur la [plateforme de données Azure Monitor](data-platform.md).
- En savoir plus sur [métriques dans Azure Monitor](data-platform-metrics.md).
- Découvrez les [données de surveillance disponibles](data-sources.md) pour différentes ressources dans Azure.
