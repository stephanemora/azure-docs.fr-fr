---
title: Métriques dans Azure Monitor | Microsoft Docs
description: Découvrez les métriques dans Azure Monitor, qui sont des données de surveillance légères capables de prendre en charge des scénarios en quasi-temps réel.
documentationcenter: ''
author: bwren
manager: carmonm
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/27/2021
ms.author: bwren
ms.openlocfilehash: 3986e59965842166ce675cd778d04984896d671d
ms.sourcegitcommit: 557ed4e74f0629b6d2a543e1228f65a3e01bf3ac
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2021
ms.locfileid: "129458526"
---
# <a name="azure-monitor-metrics-overview"></a>Vue d’ensemble d’Azure Monitor Metrics
Azure Monitor Metrics est une fonctionnalité Azure Monitor qui collecte des données numériques à partir de [ressources surveillées](../monitor-reference.md) dans une base de données de séries chronologiques. Les métriques sont des valeurs numériques collectées à intervalles réguliers et qui décrivent un certain aspect d’un système à un moment donné. 

Dans Azure Monitor, les métriques sont légères et peuvent prendre en charge des scénarios en quasi-temps réel. Elles sont donc utiles pour la génération d’alertes et la détection rapide des problèmes. Vous pouvez les analyser de manière interactive à l’aide de Metrics Explorer, être alerté de manière proactive lorsqu’une valeur dépasse un seuil ou les visualiser dans un classeur ou un tableau de bord.

> [!NOTE]
> Métriques Azure Monitor représente la moitié de la plateforme de données qui prend en charge Azure Monitor. L’autre moitié est [Journaux d’activité Azure Monitor](../logs/data-platform-logs.md), qui collecte et organise les données de journal et de performances et permet leur analyse avec un langage de requête riche. 
>
> La fonctionnalité Métriques ne peut stocker que des données numériques dans une structure particulière, tandis que la fonctionnalité Journaux d’activité peut stocker une variété de types de données (chacune avec sa propre structure). Vous pouvez également effectuer des analyses complexes sur des données de journal à l’aide de requêtes de journal, ce que vous ne pouvez pas faire pour l’analyse des données de métrique.

## <a name="what-can-you-do-with-azure-monitor-metrics"></a>Que pouvez-vous faire avec les métriques Azure Monitor ?
Le tableau suivant répertorie les façons dont vous pouvez utiliser la fonctionnalité Métriques dans Azure Monitor.

|  | Description |
|:---|:---|
| **Analyser** | Utilisez [Metrics Explorer](metrics-charts.md) pour analyser les métriques collectées sur un graphique et comparer des métriques à partir de différentes ressources. |
| **Alert** | Configurez une [règle d’alerte sur les métriques](../alerts/alerts-metric.md) qui envoie une notification ou prend [une action de façon automatique](../alerts/action-groups.md) lorsque la valeur métrique dépasse le seuil défini. |
| **Visualiser** | Épinglez un graphique à partir de Metrics Explorer dans un [tableau de bord Azure](../app/tutorial-app-dashboards.md).<br>Créer un [classeur](../visualize/workbooks-overview.md) à combiner avec plusieurs jeux de données dans un rapport interactif. Exporter les résultats d’une requête vers [Grafana](../visualize/grafana-plugin.md) pour utiliser ses tableaux de bord et les combiner avec d’autres sources de données. |
| **Automatisation** |  Utilisez la [mise à l’échelle automatique](../autoscale/autoscale-overview.md) pour augmenter ou diminuer les ressources si une valeur métrique dépasse le seuil défini. |
| **Récupérer** | Accédez à des valeurs de métriques à partir d’une ligne de commande à l’aide des [cmdlets PowerShell](/powershell/module/az.monitor).<br>Accédez à des valeurs de métriques à partir d’une application personnalisée à l’aide de l’[API REST](./rest-api-walkthrough.md).<br>Accédez à des valeurs de métriques à partir d’une ligne de commande à l’aide d’[Azure CLI](/cli/azure/monitor/metrics). |
| **Export** | [Acheminez les métriques vers les journaux d’activité](./resource-logs.md#send-to-azure-storage) pour analyser les données de Métriques Azure Monitor avec celles de Journaux d’activité Azure Monitor et stocker les valeurs de métriques pendant plus de 93 jours.<br>Transmettez en continu les métriques vers un [Event Hub](./stream-monitoring-data-event-hubs.md) pour les acheminer vers des systèmes externes. |
| **Archive** | [Archivez](./platform-logs-overview.md) l’historique des performances ou d’intégrité de votre ressource à des fins de conformité, d’audit ou de création de rapports hors connexion. |

![Diagramme montrant les sources et les utilisations des métriques.](media/data-platform-metrics/metrics-overview.png)

## <a name="data-collection"></a>Collecte de données
Azure Monitor collecte des métriques à partir des sources suivantes. Une fois ces métriques collectées dans la base de données de métriques d’Azure Monitor, elles peuvent être évaluées ensemble, quelle que soit leur source.

- **Ressources Azure**. Les métriques de plateforme sont créées par des ressources Azure et vous donnent une visibilité sur leur intégrité et leurs performances. Chaque type de ressource crée un [ensemble distinct de métriques](./metrics-supported.md) sans aucune configuration requise. Les métriques de plateforme sont collectées à partir des ressources Azure toutes les minutes, sauf indication contraire dans la définition de la métrique. 

- **Applications**. Application Insights crée des métriques pour vos applications supervisées afin de vous aider à détecter les problèmes de performances et à suivre les tendances dans l’utilisation de votre application. Les valeurs incluent notamment _Temps de réponse du serveur_ et _Exceptions du navigateur_.

- **Agents de machine virtuelle**. Les métriques sont collectées à partir du système d’exploitation invité d’une machine virtuelle. Vous pouvez activer les métriques de SE invité pour les machines virtuelles Windows en utilisant l’[extension de diagnostic Windows](../agents/diagnostics-extension-overview.md) et pour les machines virtuelles Linux en utilisant l’[agent InfluxData Telegraf](https://www.influxdata.com/time-series-platform/telegraf/).

- **Métriques personnalisées**. Vous pouvez définir des métriques en plus des métriques standard qui sont automatiquement disponibles. Vous pouvez [définir des métriques personnalisées dans votre application](../app/api-custom-events-metrics.md) qui est surveillée par Application Insights ou créer des métriques personnalisées pour un service Azure en utilisant l’[API de métriques personnalisées](./metrics-store-custom-rest-api.md).

Pour obtenir la liste complète des sources de données qui peuvent envoyer des données à Métriques Azure Monitor, consultez [Quels sont les éléments supervisés par Azure Monitor ?](../monitor-reference.md)

## <a name="metrics-explorer"></a>Metrics Explorer
Utilisez [Metrics Explorer](metrics-charts.md) pour analyser de façon interactive les données dans votre base de données de métriques et représenter les valeurs de plusieurs métriques au fil du temps dans un graphique. Vous pouvez épingler les graphiques à un tableau de bord pour les afficher avec d’autres visualisations. Vous pouvez également extraire des métriques à l’aide de l’[API REST Azure Monitoring](./rest-api-walkthrough.md).

![Capture d’écran d’un exemple de graphique dans Metrics Explorer qui montre les demandes de serveur, le temps de réponse du serveur et les demandes ayant échoué.](media/data-platform-metrics/metrics-explorer.png)

Pour plus d’informations, consultez [Prise en main d’Azure Monitor Metrics Explorer](./metrics-getting-started.md).

## <a name="data-structure"></a>Structure de données
Les données collectées par Métriques Azure Monitor sont stockées dans une base de données de série chronologique qui est optimisée pour l’analyse des données horodatées. Chaque jeu de valeurs métriques est une série chronologique avec les propriétés suivantes :

* Heure à laquelle la valeur a été collectée.
* La ressource à laquelle est associée la valeur.
* Un espace de noms qui agit comme une catégorie pour la métrique.
* Un nom de métrique.
* Valeur elle-même.
* [Plusieurs dimensions](#multi-dimensional-metrics) lorsqu’elles sont présentes. Notez que les métriques personnalisées sont limitées à dix dimensions.

## <a name="multi-dimensional-metrics"></a>Métriques multidimensionnelles
L’une des problématiques associées aux données métriques est la suivante : elles présentent souvent des informations limitées pour fournir un contexte pour les valeurs collectées. Azure Monitor résout cette problématique grâce aux métriques multidimensionnelles. 

Les dimensions d’une métrique sont des paires nom-valeur qui transmettent des données supplémentaires pour décrire la valeur de la métrique. Par exemple, une métrique appelée _Espace disque disponible_ peut comporter une dimension nommée _Lecteur_ avec les valeurs _C:_ et _D:_ . Cette dimension permet de visualiser l’espace disque disponible pour tous les lecteurs ou pour chaque lecteur individuellement.

L’exemple suivant illustre deux jeux de données pour une métrique hypothétique nommée _Débit réseau_. Le premier jeu de données n’a pas de dimensions. Le deuxième jeu de données montre les valeurs avec deux dimensions, _IP_ et _Direction_.

### <a name="network-throughput"></a>Débit du réseau

| Timestamp     | Valeur de métrique |
| ------------- |:-------------|
| 9/8/2017 8:14 | 1 331,8 Kbits/s |
| 9/8/2017 8:15 | 1 141,4 Kbits/s |
| 9/8/2017 8:16 | 1 110,2 Kbits/s |

Cette métrique non dimensionnelle peut uniquement répondre à une question basique telle que « Quel est mon débit réseau à un moment donné ? »

### <a name="network-throughput-and-two-dimensions-ip-and-direction"></a>Débit réseau et deux dimensions (« IP » et « Direction »)

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

Cette métrique peut répondre à des questions telles que « Quel était le débit réseau pour chaque adresse IP ? » et « Quelle quantité de données a été envoyée et reçue ? » Les métriques multidimensionnelles incluent des valeurs d’analyse et de diagnostic supplémentaires par rapport aux métriques non dimensionnelles.

### <a name="viewing-multi-dimensional-performance-counter-metrics-in-metrics-explorer"></a>Affichage des métriques multidimensionnelles des compteurs de performances dans Metrics Explorer 
Il n’est pas possible d’envoyer des métriques de compteurs de performances contenant un astérisque (\*) à Azure Monitor via l’API Métriques invitées classique. Cette API ne peut pas afficher les métriques qui contiennent un astérisque, car il s’agit d’une métrique multidimensionnelle que les métriques classiques ne prennent pas en charge.

Pour configurer et afficher les métriques multidimensionnelles des compteurs de performances du SE invité à l’aide de l’extension Azure Diagnostic :

1. Accédez à la page **Paramètres de diagnostic** de votre machine virtuelle.
2. Sélectionnez l’onglet **Compteurs de performances**. 
3. Sélectionnez **Personnalisé** pour configurer les compteurs de performances que vous souhaitez collecter.

   ![Capture d’écran de la section Compteurs de performances de la page des paramètres de diagnostic.](media/data-platform-metrics/azure-monitor-perf-counter.png)

4. Sélectionnez **Récepteurs**. Sélectionnez ensuite **Activé** pour envoyer vos données à Azure Monitor.

   ![Capture d’écran de la section Récepteurs de la page des paramètres de diagnostic.](media/data-platform-metrics/azure-monitor-sink.png)

5. Pour afficher votre métrique dans Azure Monitor, sélectionnez **Invité de machine virtuelle** dans la liste déroulante **Espace de noms de la métrique**.

   ![Capture d’écran de l’espace de noms de la métrique.](media/data-platform-metrics/vm-guest-namespace.png)

6. Sélectionnez **Appliquer le fractionnement** et renseignez les détails pour fractionner la métrique par instance. Vous pouvez alors voir la métrique décomposée par chacune des valeurs possibles représentées par l’astérisque dans la configuration. Dans cet exemple, l’astérisque représente les volumes de disque logique plus le total.

   ![Capture d’écran du fractionnement d’une métrique par instance.](media/data-platform-metrics/split-by-instance.png)

## <a name="retention-of-metrics"></a>Rétention des métriques
Pour la plupart des ressources dans Azure, les métriques de la plateforme sont stockées pendant 93 jours. Il existe quelques exceptions :

- **Métriques classiques du SE invité** : Il s’agit des compteurs de performances collectés par l’[extension de diagnostic Windows](../agents/diagnostics-extension-overview.md) ou l’[extension de diagnostic Linux](../../virtual-machines/extensions/diagnostics-linux.md) et acheminés vers un compte de stockage Azure. La rétention de ces métriques est garantie pendant au moins 14 jours, bien qu’aucune date d’expiration ne soit inscrite dans le compte de stockage. 
  
  Pour des raisons de performances, le portail limite la quantité de données affichées en fonction du volume. Par conséquent, le nombre réel de jours que le portail récupère peut être supérieur à 14 jours si le volume de données écrites n’est pas important. 

- **Métriques du SE invité envoyées à Métriques Azure Monitor** : Il s’agit des compteurs de performances collectés par l’[extension de diagnostic Windows](../agents/diagnostics-extension-overview.md) et envoyés au [récepteur de données Azure Monitor](../agents/diagnostics-extension-overview.md#data-destinations), par l’[agent InfluxData Telegraf](https://www.influxdata.com/time-series-platform/telegraf/) sur des machines Linux ou par le nouvel [agent Azure Monitor](../agents/azure-monitor-agent-overview.md) via des règles de collecte de données. La période de conservation de ces métriques s’élève à 93 jours.

- **Métriques du SE invité collectées par l’agent Log Analytics** : Il s’agit des compteurs de performances collectés par l’agent Log Analytics et envoyés à un espace de travail Log Analytics. La rétention de ces métriques s’élève à 31 jours et peut aller jusqu’à 2 ans.

- **Métriques reposant sur un journal d’Application Insights**. En arrière-plan, les [métriques reposant sur un journal](../app/pre-aggregated-metrics-log-metrics.md) se traduisent par des requêtes de journal. Leur rétention est variable et correspond à celle des événements dans les journaux sous-jacents (entre 31 jours et 2 ans). Pour les ressources Application Insights, les journaux sont stockés pendant 90 jours.

> [!NOTE]
> Vous pouvez [envoyer des métriques de plateforme pour les ressources Azure Monitor à un espace de travail Log Analytics](./resource-logs.md#send-to-azure-storage) pour les tendances à long terme.

> [!NOTE]
> Comme mentionné précédemment, pour la plupart des ressources dans Azure, les métriques de plateforme sont stockées pendant 93 jours. Toutefois, vos interrogations (dans la vignette **Métriques**) ne peuvent porter que sur 30 jours de données au maximum pour un même graphique. Cette limitation ne s’applique pas aux métriques reposant sur un journal. 
>
> Si le graphique est vide ou affiche uniquement une partie des données de métriques, vérifiez que l’intervalle entre les dates de début et de fin dans le sélecteur de temps ne dépasse pas 30 jours. Après avoir sélectionné un intervalle de 30 jours, vous pouvez afficher le graphique en mode [panoramique](./metrics-charts.md#pan) pour afficher la fenêtre de rétention complète.

## <a name="next-steps"></a>Étapes suivantes

- Apprenez-en davantage sur la [plateforme de données Azure Monitor](../data-platform.md).
- Apprenez-en davantage sur les [données de journal dans Azure Monitor](../logs/data-platform-logs.md).
- Découvrez les [données de surveillance disponibles](../agents/data-sources.md) pour différentes ressources dans Azure.