---
title: Mesures dans Azure Monitor | Microsoft Docs
description: Décrit les mesures dans Azure Monitor qui sont légers peut prendre en charge près de scénarios en temps réel les données de surveillance.
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: monitoring
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2019
ms.author: bwren
ms.openlocfilehash: 2646941e2384acf6d303615f564b65d616931180
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/09/2019
ms.locfileid: "59358878"
---
# <a name="metrics-in-azure-monitor"></a>Mesures dans Azure Monitor

> [!NOTE]
> La plateforme de données Azure Monitor est basée sur les deux types de données fondamentaux : Métriques et les journaux. Cet article décrit les métriques. Reportez-vous à [journaux dans Azure Monitor](data-platform-logs.md) pour obtenir une description détaillée des journaux et en [platforn de données Azure Monitor](data-platform.md) pour obtenir une comparaison des deux.


Mesures dans Azure Monitor sont légères et prendre en charge les scénarios en temps réel, ce qui les rend particulièrement utile pour quasiment la détection d’alerte et rapide des problèmes. Cet article décrit comment les mesures sont structurés, ce que vous pouvez faire avec eux et identifie les différentes sources de données qui stockent les données dans les métriques.

## <a name="what-are-metrics"></a>Quelles sont les mesures ?
Les métriques sont des valeurs numériques décrivant certains aspects d’un système à un moment donné. Mesures sont collectées à intervalles réguliers et sont utiles pour créer des alertes, car ils peuvent être échantillonnées fréquemment, et une alerte peut être déclenchée rapidement avec une logique relativement simple.

## <a name="what-can-you-do-with-azure-monitor-metrics"></a>Que pouvez-vous faire avec les mesures Azure Monitor ?
Le tableau suivant répertorie les différentes façons, que vous pouvez utiliser des données métriques dans Azure Monitor.

|  |  |
|:---|:---|
| Analyser | Utilisez [Explorateur de mesures](metrics-charts.md) pour analyser les métriques collectées sur un graphique et comparer des métriques à partir de différentes ressources. |
| Visualisation | Épingler un graphique à partir de l’Explorateur de mesures pour un [tableau de bord Azure](../learn/tutorial-app-dashboards.md).<br>Créer un [classeur](../app/usage-workbooks.md) à combiner avec plusieurs jeux de données dans un rapport interactif. Exporter les résultats d’une requête pour [Grafana](grafana-plugin.md) à exploiter ses tableaux de bord et à combiner avec d’autres sources de données. |
| Alerte | Configurer un [règle d’alerte métrique](alerts-metric.md) qui envoie une notification ou prend [action automatisée](action-groups.md) lorsque la valeur de métrique dépasse un seuil. |
| Automatisation |  Utilisez [mise à l’échelle](autoscale-overview.md) pour augmenter ou diminuer les ressources en se basant sur une valeur de métrique qui dépasse un seuil. |
| Exportation | [Acheminer des mesures dans les journaux](diagnostic-logs-stream-log-store.md) pour analyser les données dans les mesures Azure Monitor avec les données dans les journaux d’Azure Monitor et pour stocker les valeurs de mesure pendant plus de 93 jours.<br>Stream métriques pour un [Event Hub](stream-monitoring-data-event-hubs.md) les acheminer vers des systèmes externes. |
| Récupération | Accéder aux valeurs de mesure à partir d’un à l’aide de la ligne de commande [applets de commande PowerShell](https://docs.microsoft.com/powershell/module/az.applicationinsights)<br>Accéder à des valeurs de mesure à partir de l’application personnalisée à l’aide [API REST](rest-api-walkthrough.md).<br>Accéder aux valeurs de mesure à partir d’un à l’aide de la ligne de commande [CLI](/cli/azure/monitor/metrics). |
| Archivage | [Archivez](..//learn/tutorial-archive-data.md) l’historique des performances ou d’intégrité de votre ressource à des fins de conformité, d’audit ou de création de rapports hors connexion. |


## <a name="how-is-data-in-azure-monitor-metrics-structured"></a>Comment sont des données structurées de mesures Azure Monitor ?
Données collectées par les métriques Azure Monitor sont stockées dans une base de données de série chronologique qui est optimisé pour l’analyse des données horodatées. Chaque jeu de valeurs de mesure est une série chronologique avec les propriétés suivantes :

* L’heure de que la valeur a été collectée.
* La ressource de la valeur est associée
* Un espace de noms qui agit comme une catégorie pour la métrique
* Un nom de métrique
* La valeur elle-même
* Certaines mesures peuvent avoir plusieurs dimensions comme décrit dans [métriques multidimensionnelles](#multi-dimensional-metrics). Les métriques personnalisées peuvent avoir jusqu’à 10 dimensions.

Métriques dans Azure sont stockées pour 93 jours. Vous pouvez [envoyer des mesures de plate-forme pour les ressources d’Azure Monitor à un espace de travail Analytique de journal](diagnostic-logs-stream-log-store.md) pour l’analyse de tendances à long terme.

## <a name="multi-dimensional-metrics"></a>Métriques multidimensionnelles
Un des défis en matière de données de mesure est qu’il a souvent limitée d’informations pour fournir un contexte pour les valeurs collectées. Azure Monitor face à ce défi avec les métriques multidimensionnelles. Les dimensions d’une métrique sont des paires nom-valeur qui transmettent des données supplémentaires pour décrire la valeur de la métrique. Par exemple, une mesure _espace disque disponible_ peut avoir une dimension nommée _lecteur_ avec des valeurs _C:_, _D:_, ce qui permettrait d’affichage l’espace disque disponible sur tous les lecteurs ou pour chaque lecteur individuellement.

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

## <a name="interacting-with-azure-monitor-metrics"></a>Interaction avec les métriques Azure Monitor
Utilisez [Metrics Explorer](metrics-charts.md) pour analyser les données dans votre base de données de métrique et les valeurs de plusieurs métriques du graphique au fil du temps de manière interactive. Vous pouvez épingler les graphiques au tableau de bord pour les afficher avec les autres visualisations. Vous pouvez également extraire des métriques à l’aide de l’[API REST Azure Monitoring](rest-api-walkthrough.md).

![Metrics Explorer](media/data-platform/metrics-explorer.png)

## <a name="sources-of-azure-monitor-metrics"></a>Sources de métriques Azure Monitor
Il existe trois sources fondamentales pour les métriques collectées par Azure Monitor. Une fois ces mesures sont collectées dans la base de données de métrique Azure Monitor, ils peuvent être évaluées ensemble, quel que soit leur source.

Les **métriques de plateforme** sont créées par des ressources Azure et vous donnent une visibilité sur leur intégrité et leurs performances. Chaque type de ressource crée un [ensemble distinct de métriques](metrics-supported.md) sans aucune configuration requise. Métriques de la plateforme sont collectées à partir de ressources Azure à la fréquence d’une minute, sauf indication contraire dans la définition. 

**Métriques du système d’exploitation invité** sont collectés à partir du système d’exploitation invité d’une machine virtuelle. Activer les métriques du système d’exploitation invité pour les machines virtuelles Windows avec [Extension de Diagnostic Windows (WAD)](../platform/diagnostics-extension-overview.md) et pour les machines virtuelles Linux avec [InfluxData Telegraf Agent](https://www.influxdata.com/time-series-platform/telegraf/).

Les **métriques d’application** sont créées par Application Insights pour vos applications supervisées et vous aident à détecter les problèmes de performances et à suivre les tendances dans l’utilisation de votre application. Cela inclut des valeurs comme _Temps de réponse du serveur_ et _Exceptions du navigateur_.

**Mesures personnalisées** sont des mesures que vous définissez en plus des mesures standards qui sont automatiquement disponibles. Vous pouvez [définissez des mesures personnalisées dans votre application](../app/api-custom-events-metrics.md) qui est surveillé par Application Insights ou créer des mesures personnalisées pour un service Azure en utilisant le [mesures personnalisées API](metrics-store-custom-rest-api.md).


## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur la [plateforme de données Azure Monitor](data-platform.md).
- En savoir plus sur [données du journal dans Azure Monitor](data-platform-logs.md).
- Découvrez les [données de surveillance disponibles](data-sources.md) pour différentes ressources dans Azure.
