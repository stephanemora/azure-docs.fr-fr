---
title: Supervision des données collectées par Azure Monitor | Microsoft Docs
description: La supervision des données collectées par Azure Monitor est divisée en plusieurs métriques légères et capables de prendre en charge des scénarios en temps quasi-réel ainsi que les journaux utilisés pour une analyse avancée.
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: monitoring
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/05/2018
ms.author: bwren
ms.openlocfilehash: efc5fb022d117caeaec9da014252b501f2d06769
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/22/2019
ms.locfileid: "54450007"
---
# <a name="monitoring-data-collected-by-azure-monitor"></a>Supervision des données collectées par Azure Monitor
[Azure Monitor](../overview.md) est un service qui vous permet de surveiller vos applications et les ressources dont elles dépendent. Au cœur de cette fonction se trouve le stockage de données de télémétrie et d’autres données tirées des ressources supervisées. Cet article décrit de manière exhaustive la façon dont ces données sont stockées et utilisées par Azure Monitor.

Toutes les données collectées par Azure Monitor font partie d’un des deux types fondamentaux : les [métriques](#metrics) et les [journaux](#logs). Les métriques sont des valeurs numériques décrivant un aspect d’un système à un moment précis dans le temps. Elles sont légères et capables de prendre en charge des scénarios en quasi-temps réel. Les journaux contiennent différents types de données organisées en enregistrements, avec différents jeux de propriétés pour chaque type. Les données de télémétrie, comme les événements et les traces, sont stockées sous forme de journaux en plus des données de performances, afin qu’elles puissent être combinées pour analyse.

![Vue d’ensemble d’Azure Monitor](media/data-collection/overview.png)

## <a name="metrics"></a>Mesures
Les métriques sont des valeurs numériques décrivant certains aspects d’un système à un moment donné. Elles sont légères et capables de prendre en charge des scénarios en quasi-temps réel. Les métriques sont collectées à intervalles réguliers, que la valeur change ou non. Elles sont utiles pour créer des alertes, dans la mesure où elles peuvent être échantillonnées fréquemment, et où une alerte peut être déclenchée rapidement avec un circuit logique assez simple.

Par exemple, vous pouvez collecter l’utilisation du processeur d’une machine virtuelle à chaque minute, ou le nombre d’utilisateurs connectés à votre application toutes les 10 minutes. Vous pouvez déclencher une alerte lorsque l’une des valeurs collectées ou même la différence entre deux valeurs dépasse un seuil défini.

Les attributs spécifiques des métriques dans Azure sont les suivants :

* Collectées toutes les minutes, sauf indication contraire dans la définition de la métrique.
* Identifiées par un nom de métrique et un espace de noms qui sert de catégorie.
* Stockées pendant 93 jours. Vous pouvez copier des métriques vers les journaux pour les tendances à long terme.

Chaque valeur de métrique présente les propriétés suivantes :
* Heure à laquelle la valeur a été collectée.
* Type de mesure que représente la valeur.
* Ressource à laquelle est associée la valeur.
* Valeur elle-même.
* Certaines métriques peuvent avoir plusieurs dimensions, comme indiqué dans la section suivante. Les métriques personnalisées peuvent avoir jusqu’à 10 dimensions.

### <a name="multi-dimensional-metrics"></a>Métriques multidimensionnelles
Les dimensions d’une métrique sont des paires nom-valeur qui transmettent des données supplémentaires pour décrire la valeur de la métrique. Par exemple, une métrique _Espace disque disponible_ peut avoir une dimension nommée _Lecteur_ avec les valeurs _C:_ et _D:_, qui permet d’afficher l’espace disque disponible sur les deux lecteurs ou sur chaque lecteur individuellement.

L’exemple ci-dessous illustre deux jeux de données pour une métrique hypothétique nommée _Débit réseau_. Le premier jeu de données n’a pas de dimensions. Le deuxième jeu de données montre les valeurs avec deux dimensions, _Adresse IP_ et _Direction_ :

### <a name="network-throughput"></a>Débit réseau

| Timestamp     | Valeur de métrique |
| ------------- |:-------------|
| 9/8/2017 8:14 | 1 331,8 Kbits/s |
| 9/8/2017 8:15 | 1 141,4 Kbits/s |
| 9/8/2017 8:16 | 1 110,2 Kbits/s |

Cette métrique sans dimensions peut répondre uniquement à une question de base telle que « quel est mon débit réseau à un moment donné ? ».

### <a name="network-throughput--two-dimensions-ip-and-direction"></a>Débit réseau + deux dimensions (« IP » et « Direction »)

| Timestamp     | Dimension « IP »   | Dimension « Direction » | Valeur de métrique|
| ------------- |:-----------------|:------------------- |:-----------|
| 9/8/2017 8:14 | IP="192.168.5.2" | Direction="Send"    | 646,5 Kbits/s |
| 9/8/2017 8:14 | IP="192.168.5.2" | Direction="Receive" | 420,1 Kbits/s |
| 9/8/2017 8:14 | IP="10.24.2.15"  | Direction="Send"    | 150,0 Kbits/s |
| 9/8/2017 8:14 | IP="10.24.2.15"  | Direction="Receive" | 115,2 Kbits/s |
| 9/8/2017 8:15 | IP="192.168.5.2" | Direction="Send"    | 515,2 Kbits/s |
| 9/8/2017 8:15 | IP="192.168.5.2" | Direction="Receive" | 371,1 Kbits/s |
| 9/8/2017 8:15 | IP="10.24.2.15"  | Direction="Send"    | 155,0 Kbits/s |
| 9/8/2017 8:15 | IP="10.24.2.15"  | Direction="Receive" | 100,1 Kbits/s |

Cette métrique peut répondre à des questions telles que « quel était le débit réseau pour chaque adresse IP ? » et « quelle quantités de données ont été envoyées et reçues ? ». Les métriques multidimensionnelles incluent des valeurs d’analyse et de diagnostic supplémentaires par rapport aux métriques sans dimensions.

### <a name="value-of-metrics"></a>Valeur des métriques
Les métriques individuelles fournissent généralement peu d’informations en elles-mêmes. Elles fournissent une valeur unique sans contexte autre qu’une comparaison à une valeur de seuil. Elles sont cependant utiles quand elles sont combinées avec d’autres métriques pour identifier des modèles et tendances, ou lorsqu’elles sont combinées avec des journaux qui fournissent un contexte autour de valeurs particulières.

Par exemple, le nombre d’utilisateurs de votre application à un moment donné peut vous donner une indication sur l’intégrité de l’application. Toutefois, une chute soudaine du nombre d’utilisateurs, indiquée par plusieurs valeurs de la même métrique, peut traduire un problème. Un nombre excessif d’exceptions levées par l’application, et indiquées par une métrique distincte, peut permettre d’identifier un problème d’application à l’origine de la chute. Les événements créés par l’application pour identifier les défaillances dans ses composants peuvent vous aider à identifier la cause profonde du problème.

### <a name="sources-of-metric-data"></a>Sources des données métriques
Il existe trois sources fondamentales pour les métriques collectées par Azure Monitor. Toutes ces métriques sont disponibles dans le magasin de métriques où elles peuvent être évaluées ensemble, quelle que soit leur source.

Les **métriques de plateforme** sont créées par des ressources Azure et vous donnent une visibilité sur leur intégrité et leurs performances. Chaque type de ressource crée un [ensemble distinct de métriques](../../azure-monitor/platform/metrics-supported.md) sans aucune configuration requise.

Les **métriques d’application** sont créées par Application Insights pour vos applications supervisées et vous aident à détecter les problèmes de performances et à suivre les tendances dans l’utilisation de votre application. Cela inclut des valeurs comme _Temps de réponse du serveur_ et _Exceptions du navigateur_.

Les **métriques personnalisées** sont des métriques que vous définissez en plus de la métrique standard, et qui sont automatiquement disponibles. Les métriques personnalisées doivent être créées pour une ressource unique dans la même région que cette ressource. Vous pouvez créer des métriques personnalisées à l’aide des méthodes suivantes :
- [Définition de métriques personnalisées dans votre application](../../azure-monitor/app/api-custom-events-metrics.md) sous la supervision d’Application Insights. Ces métriques complètent l’ensemble standard de métriques d’application.
- Publication de métriques personnalisées à partir de vos machines virtuelles Windows à l’aide de l’[extension de diagnostics Windows](../../azure-monitor/platform/diagnostics-extension-overview.md).
- Publication de métriques personnalisées à partir de vos machines virtuelles Linux à l’aide de l’[agent InfluxData Telegraf](https://www.influxdata.com/time-series-platform/telegraf/).
- Écriture de métriques personnalisées à partir d’un service Azure à l’aide de l’API de métriques personnalisées.

![Vue d’ensemble des métriques](media/data-collection/metrics-overview.png)

### <a name="what-can-you-do-with-metrics"></a>Que pouvez-vous faire avec les mesures ?
Les tâches que vous pouvez effectuer avec les métriques sont les suivantes :

- Utiliser [Metrics Explorer](../../azure-monitor/platform/metrics-charts.md) pour analyser les métriques collectées et les représenter sur un graphique. Suivre les performances d’une ressource (par exemple, machine virtuelle, site web ou application logique) en épinglant des graphiques sur un [tableau de bord Azure](../../azure-portal/azure-portal-dashboards.md).
- Configurer une [règle d’alerte sur les métriques](alerts-metric.md) qui envoie une notification ou prend [une action de façon automatique](action-groups.md) lorsque la métrique dépasse le seuil défini.
- Utiliser la [mise à l’échelle automatique](../../azure-monitor/platform/autoscale-overview.md) pour augmenter ou diminuer les ressources si une métrique dépasse le seuil défini.
- Acheminer les métriques vers Log Analytics pour analyser les données métriques avec les données de journal et pour stocker les valeurs métriques pendant plus de 93 jours.
- Transmettre en continu les métriques vers un [Event Hub](../../azure-monitor/platform/stream-monitoring-data-event-hubs.md) pour les acheminer vers [Azure Stream Analytics](../../stream-analytics/stream-analytics-introduction.md) ou vers des systèmes externes.
- [Archivez](../../azure-monitor/learn/tutorial-archive-data.md) l’historique des performances ou d’intégrité de votre ressource à des fins de conformité, d’audit ou de création de rapports hors connexion.
- Accéder à des valeurs métriques à partir d’une ligne de commande ou à l’aide d’une application personnalisée avec les [cmdlets PowerShell](https://docs.microsoft.com/powershell/module/azurerm.insights/?view=azurermps-6.7.0) ou l’[API REST](../../azure-monitor/platform/rest-api-walkthrough.md).

### <a name="viewing-metrics"></a>Affichage des métriques
Les métriques Azure Monitor sont stockées dans une base de données de séries chronologiques optimisée pour une récupération rapide et stockant les valeurs métriques pendant 93 jours. Copiez les métriques vers les journaux pour une analyse et des tendances à long terme.

Les données métriques sont utilisées de différentes façons, comme décrit ci-dessus. Utilisez [Metrics Explorer](../../azure-monitor/platform/metrics-charts.md) pour analyser directement les données dans votre magasin de métriques et représenter les valeurs de plusieurs métriques au fil du temps dans un graphique. Vous pouvez afficher les graphiques de manière interactive ou les épingler au tableau de bord pour les voir avec d’autres visualisations. Vous pouvez également extraire des métriques à l’aide de l’[API REST Azure Monitoring](../../azure-monitor/platform/rest-api-walkthrough.md).

![Metrics Explorer](media/data-collection/metrics-explorer.png)

## <a name="logs"></a>Journaux
Les journaux contiennent différents types de données organisées en enregistrements, avec différents jeux de propriétés pour chaque type. Les journaux peuvent contenir des valeurs numériques, comme les métriques, mais ils contiennent généralement des données texte avec des descriptions détaillées. Par ailleurs, ils diffèrent des métriques en ce qu’ils varient dans leur structure et ne sont souvent pas collectés à intervalles réguliers.

Un événement collecté de façon sporadique est un type courant d’entrée de journal. Ils sont créés par une application ou un service, et incluent généralement suffisamment d’informations pour fournir un contexte complet à eux seuls. Par exemple, un événement peut indiquer la création ou la modification d’une ressource particulière, le démarrage d’un nouvel hôte en réponse à une augmentation du trafic, ou une erreur détectée dans une application.

Les journaux sont particulièrement utiles pour combiner des données provenant de sources diverses, afin d’effectuer une analyse complexe et d’observer une tendance au fil du temps. Le format des données pouvant varier, les applications peuvent créer des journaux personnalisés et structurés de façon appropriée. Il est même possible de répliquer des métriques dans des journaux afin de les combiner avec d’autres données de supervision pour observer des tendances ou mener d’autres analyses de données.

### <a name="sources-of-log-data"></a>Sources de données de journal
Azure Monitor peut collecter des données de journal à partir de diverses sources au sein d’Azure et de ressources locales. Les sources des données de journal sont les suivantes :

- [Journaux d’activité](collect-activity-logs.md) provenant de ressources Azure qui incluent des informations sur leur configuration et leur intégrité et [journaux de diagnostic](../../azure-monitor/platform/diagnostic-logs-stream-log-store.md) qui fournissent des informations sur leur fonctionnement.
- Agents sur les machines virtuelles [Windows](agent-windows.md) et [Linux](../learn/quick-collect-linux-computer.md) qui envoient les données de télémétrie du système d’exploitation invité et des applications à Azure Monitor en fonction des [sources de données](data-sources.md) que vous configurez.
- Données d’application collectées par [Application Insights](https://docs.microsoft.com/azure/application-insights/).
- Données fournissant des informations sur une application ou un service spécifique à partir de [solutions de supervision](../insights/solutions.md) ou de fonctionnalités telles que Container Insights, VM Insights ou Resource Group Insights.
- Données de sécurité collectées par [Azure Security Center](https://docs.microsoft.com/azure/security-center/).
- [Métriques](#metrics) provenant de ressources Azure. Cela vous permet de stocker des métriques pendant plus de 93 jours et de les analyser avec d’autres données de journal.
- Données de télémétrie écrites dans [Stockage Azure](azure-storage-iis-table.md).
- Données personnalisées provenant de n’importe quel client API REST à l’aide du client d’[API Collecte de données HTTP](data-collector-api.md) ou d’un flux de travail d’[application logique Azure](https://docs.microsoft.com/azure/logic-apps/).

![Vue d’ensemble des journaux](media/data-collection/logs-overview.png)

### <a name="what-can-you-do-with-logs"></a>Que pouvez-vous faire avec les journaux ?
Les tâches réalisables avec les journaux sont les suivantes :

- Utiliser [Log Analytics](../log-query/get-started-portal.md) dans le Portail Azure pour écrire des requêtes d’analyse des données de journal. Épingler les résultats affichés sous forme de tableaux ou de graphiques dans un [tableau de bord Azure](../../azure-portal/azure-portal-dashboards.md).
- Configurer une [règle d’alerte de journal](alerts-log.md) qui envoie une notification ou prend une [action de façon automatique](action-groups.md) lorsque les résultats de la requête correspondent à un résultat spécifique.
- Générer un flux de travail en fonction des données de journal à l’aide de [Logic Apps](~/articles/logic-apps/index.yml).
- Exporter les résultats d’une requête vers [Power BI](powerbi.md) pour utiliser différentes visualisations et les partager avec les utilisateurs extérieurs à Azure.
- Accéder à des valeurs métriques à partir d’une ligne de commande ou à l’aide d’une application personnalisée avec les [cmdlets PowerShell](https://docs.microsoft.com/powershell/module/azurerm.operationalinsights/?view=azurermps-6.8.1) ou l’[API REST](https://dev.loganalytics.io/).

### <a name="viewing-log-data"></a>Affichage des données de journal
Toutes les données de journal dans Azure Monitor sont récupérées en utilisant une [requête de journal](../log-query/log-query-overview.md) écrite dans le [langage de requête Data Explorer](../log-query/get-started-queries.md), qui vous permet de rapidement récupérer, consolider et analyser les données collectées. Utilisez [Log Analytics](../log-query/portals.md) pour écrire et tester des requêtes dans le Portail Azure. Vous pouvez afficher les résultats de manière interactive ou les épingler au tableau de bord pour les voir avec d’autres visualisations. Vous pouvez également récupérer les journaux à l’aide de l’[API REST Azure Monitoring](../../monitoring-and-diagnostics/monitoring-rest-api-walkthrough.md).

> [!IMPORTANT]
> Les données Application Insights sont stockées dans une partition autre que les données de journal dans Azure Monitor. Les mêmes fonctionnalités que les autres données de journal sont prises en charge, mais vous devez utiliser la [console Application Insights](../app/analytics.md) ou l’[API Application Insights](https://dev.applicationinsights.io/) pour accéder à ces données. Vous pouvez utiliser une [requête interressources](../log-query/cross-workspace-query.md) pour analyser les données d’application ainsi que d’autres données de journal.

![Journaux](media/data-collection/logs.png)

## <a name="convert-monitoring-data"></a>Convertir les données de supervision

### <a name="metrics-to-logs"></a>Des métriques aux journaux
Vous pouvez copier les métriques dans les journaux pour effectuer une analyse complexe avec d’autres types de données au moyen du langage de requête riche d’Azure Monitor. Vous pouvez également conserver des données de journal sur des périodes plus longues que pour les métriques, ce qui vous permet d’analyser une tendance au fil du temps. Utilisez des métriques pour prendre en charge l’analyse et la génération d’alertes en quasi temps réel lorsque vous utilisez des journaux à des fins d’analyse et de suivi de tendances avec d’autres données.

Pour trouver des conseils concernant la collecte de métriques à partir de ressources Azure, voir [Collecte des journaux et des métriques des services Azure à utiliser dans Azure Monitor](collect-azure-metrics-logs.md). Pour obtenir des conseils concernant la collecte de métriques de ressources Azure PaaS, voir [Configurer la collecte de métriques de ressources Azure PaaS avec Azure Monitor](collect-azurepass-posh.md).

### <a name="logs-to-metrics"></a>Des journaux aux métriques
Comme décrit précédemment, les métriques sont plus réactives que les journaux, ce qui vous permet de créer des alertes avec une latence faible et à moindre coût. Une quantité significative de données numériques est stockée sous forme de journaux qui seraient appropriés pour des métriques, mais ces données ne sont pas stockées dans Azure Monitor sous forme de métriques. Un exemple courant est celui des données de performances collectées auprès d’agents et de solutions de gestion. Certaines de ces valeurs peuvent être copiées dans les métriques où elles sont disponibles pour la génération d’alertes et pour l’analyse avec Metrics Explorer.

Pour une explication de cette fonctionnalité, voir [Créer des alertes de métrique de journaux dans Azure Monitor](../../azure-monitor/platform/alerts-metric-logs.md). La liste des valeurs prises en charge est disponible dans le document [Métriques prises en charge avec Azure Monitor](../../azure-monitor/platform/metrics-supported.md#microsoftoperationalinsightsworkspaces).

## <a name="stream-data-to-external-systems"></a>Transmettre des données en continu vers les systèmes externes
En plus d’utiliser les outils dans Azure pour analyser les données de supervision, vous aurez peut-être besoin de transférer ces données vers un outil externe, comme un produit SIEM (Security Information and Event Management). Ce transfert est le plus souvent effectué directement à partir des ressources supervisées au travers d’[Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/).

Pour obtenir des conseils concernant les différents types de données de surveillance, voir [Diffuser des données de surveillance Azure vers un hub d’événements pour les utiliser dans un outil externe](../../azure-monitor/platform/stream-monitoring-data-event-hubs.md).

## <a name="next-steps"></a>Étapes suivantes

- Découvrez les [données de surveillance disponibles](data-sources.md) pour différentes ressources dans Azure.
