---
title: Collecte de données de surveillance dans Azure | Microsoft Docs
description: Vue d’ensemble des données de surveillance collectées à partir des applications et des services Azure, ainsi que des outils utilisés pour les analyser.
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: monitoring
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/06/2018
ms.author: bwren
ms.openlocfilehash: efbf0907f3ed75957159c38a536bd31e88a0dbb3
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2018
ms.locfileid: "39213297"
---
# <a name="collecting-monitoring-data-in-azure"></a>Collecte de données de surveillance dans Azure
Cet article fournit une vue d’ensemble des données de surveillance collectées à partir des applications et des services Azure, ainsi que des outils utilisés pour les analyser. 

## <a name="types-of-monitoring-data"></a>Types de données de surveillance
Toutes les données de surveillance sont de l’un des deux types fondamentaux, les métriques ou les journaux. Chaque type présente des caractéristiques distinctes, adaptées aux scénarios particuliers décrits ci-dessous.

### <a name="metrics"></a>Mesures
Les métriques sont des valeurs numériques décrivant un aspect d’un système à un moment précis dans le temps. Elles incluent des données distinctes, dont la valeur proprement dite, l’heure de collecte de la valeur, le type de mesure que la valeur représente, et la ressource à laquelle la valeur est associée. Les métriques sont collectées à intervalles réguliers, que la valeur change ou non. Par exemple, vous pouvez collecter la mesure de l’utilisation du processeur d’une machine virtuelle à chaque minute, ou le nombre d’utilisateurs connecté à votre application toutes les 10 minutes.

Les métriques sont légères et capables de prendre en charge des scénarios en quasi temps réel. Elles sont particulièrement utiles pour créer des alertes, dans la mesure où elles peuvent être échantillonnées fréquemment, et où une alerte peut être déclenchée rapidement avec une logique relativement simple. Par exemple, vous pouvez déclencher une alerte quand une métrique dépasse une valeur de seuil, ou quand la différence entre les valeurs de deux métriques atteint une valeur particulière.

Les métriques individuelles fournissent généralement peu d’informations en elles-mêmes. Elles fournissent une valeur unique sans contexte autre qu’une comparaison à une valeur de seuil. Elles sont cependant utiles quand elles sont combinées avec d’autres mesures pour identifier des modèles et tendances, ou combinées avec des journaux qui fournissent un contexte autour de valeurs particulières. Par exemple, le nombre d’utilisateurs de votre application à un moment donné peut vous donner une indication sur l’intégrité de l’application. Une chute soudaine du nombre d’utilisateurs indiquée par plusieurs valeurs de la même métrique peut traduire un problème. Un nombre excessif d’exceptions levées par l’application, indiqué par une métrique distincte, peut permettre d’identifier un problème d’application à l’origine de la chute. Des événements créés par l’application identifiant une défaillance de composants particuliers de l’application peuvent vous aider à identifier la cause racine.

Les alertes basées sur des journaux ne sont pas aussi réactives que les alertes basées sur des métriques, mais elles peuvent inclure une logique plus complexe. Vous pouvez créer une alerte basée sur les résultats de toute requête qui effectue une analyse complexe sur des données provenant de plusieurs sources.

### <a name="logs"></a>Journaux
Les journaux contiennent différents types de données organisées en enregistrements, avec différents jeux de propriétés pour chaque type. Les journaux peuvent contenir des valeurs numériques, comme les métriques, mais contiennent généralement des données de texte avec des descriptions détaillées. Par ailleurs, ils diffèrent des métriques en ce qu’ils varient dans leur structure et ne sont souvent pas collectés à intervalles réguliers.

Un type courant d’entrée de journal est un événement. Les événements sont collectés de façon sporadique, car ils sont créés par une application ou un service, et incluent généralement suffisamment d’informations pour fournir un contexte complet.  Par exemple, un événement peut indiquer la création ou la modification d’une ressource particulière, le démarrage d’un nouvel hôte en réponse à une augmentation du trafic, ou une erreur détectée dans une application.

Les journaux sont particulièrement utiles pour combiner des données de diverses sources afin d’effectuer une analyse complexe et de suivre une tendance au fil du temps. Le format des données pouvant varier, les applications peuvent créer des journaux personnalisés structurés de façon appropriée. Il est même possible de répliquer des métriques dans des journaux afin de les combiner avec d’autres données de surveillance afin d’observer des tendances ou de conduire d’autres analyses de données.


## <a name="monitoring-tools-in-azure"></a>Outils de surveillance dans Azure
Les données de surveillance dans Azure sont collectées et analysées à l’aide de plusieurs sources décrites dans les sections suivantes.

### <a name="azure-metrics"></a>Métriques Azure
Les métriques de ressources et applications Azure sont collectées dans Métriques Azure. Les données de métrique sont intégrées dans les pages du portail Azure pour des ressources Azure particulières, telles que des machines virtuelles, qui incluent des graphiques de métriques telles que l’utilisation du processeur et du réseau pour l’ordinateur sélectionné. Elles peuvent également être analysées avec [Metrics Explorer](../monitoring-and-diagnostics/monitoring-metric-charts.md) qui peut représenter sous forme graphique les valeurs de plusieurs métriques au fil du temps.  Vous pouvez afficher les graphiques de manière interactive ou les épingler au tableau de bord pour les voir avec d’autres visualisations. Vous pouvez également récupérer les métriques à l’aide de l’[API REST Surveillance Azure](../monitoring-and-diagnostics/monitoring-rest-api-walkthrough.md).

Pour plus de détails sur les données de métrique collectées par les différents types de ressources Azure, voir [Sources de données de surveillance dans Azure](monitoring-data-sources.md). 

![Metrics Explorer](media/monitoring-data-collection/metrics-explorer.png)


### <a name="azure-activity-log"></a>Journaux d’activité 
Le [Journal des activités Windows Azure](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) stocke les journaux concernant la configuration et l’intégrité des services Azure. Vous pouvez utiliser l’Explorateur du journal d’activité pour afficher ces journaux dans le portail Azure, mais ils sont généralement [copiés dans Log Analytics](../log-analytics/log-analytics-activity.md) afin d’être analysés avec d’autres données de journal.

Vous pouvez utiliser l’Explorateur de journal d’activité pour afficher le journal d’activité filtré selon certains critères.  La plupart des ressources ont également une option Journal d’activité dans leur menu sur le portail Azure, qui affiche l’Explorateur du journal d’activité filtré pour ces ressources. Vous pouvez également récupérer les journaux d’activité avec l’[API REST Surveillance Azure](../monitoring-and-diagnostics/monitoring-rest-api-walkthrough.md).

![Explorateur du journal d’activité](media/monitoring-data-collection/activity-log-explorer.png)


### <a name="log-analytics"></a>Log Analytics
Log Analytics fournit une plateforme de données commune pour la gestion dans Azure. C’est le service principal utilisé pour le stockage et l’analyse des journaux dans Azure, la collecte de données à partir de diverses sources, dont des agents sur les machines virtuelles, des solutions de gestion et différentes ressources Azure. Les données provenant d’autres sources, dont des métriques et le journal d’activité, peuvent être copiées dans Log Analytics afin de créer un référentiel central complet des données de surveillance.

Log Analytics utilise un langage de requête riche pour analyser les données collectées.  Vous pouvez utiliser les [portails de recherche dans les journaux](../log-analytics/log-analytics-log-search-portals.md) pour écrire et tester les requêtes, ainsi qu’analyser leurs résultats de manière interactive. Vous pouvez également [créer des affichages](../log-analytics/log-analytics-view-designer.md) pour visualiser les résultats de recherche dans les journaux, ou coller les résultats d’une requête directement dans un tableau de bord Azure.  Les solutions de gestion sont les recherches dans les journaux et les affichages dans Log Analytics l’analyse des données collectées. D’autres services tels qu’Application Insights stockent des données dans Log Analytics, et fournissent des outils supplémentaires pour l’analyse.  

![Journaux](media/monitoring-data-collection/logs.png)

### <a name="application-insights"></a>Application Insights
Application Insights collecte la télémétrie d’applications web installées sur une série de plateformes. La solution stocke ses données dans Métriques Azure et Log Analytics, et fournit une panoplie complète d’outils riches, en plus des outils existants destinés à l’analyse de ces données, qui permettent de les analyser et de les visualiser. Cela vous permet de tirer parti d’un ensemble commun de services, tels que des alertes, des recherches dans les journaux et des tableaux de bord que vous pouvez utiliser à d’autres fins de surveillance.


![App Insights](media/monitoring-data-collection/app-insights.png)

### <a name="service-map"></a>Service Map
Service Map fournit une représentation visuelle des machines virtuelles avec leurs processus et dépendances. La solution stocke la plupart de ces données dans Log Analytics pour vous permettre de les analyser avec d’autres données de gestion. La console de Service Map récupère également des données à partir de Log Analytics afin de les présenter dans le contexte de la machine virtuelle en cours d’analyse.

![Service Map](media/monitoring-data-collection/service-map.png)


## <a name="transferring-monitoring-data"></a>Transfert de données de surveillance

### <a name="metrics-to-logs"></a>Des métriques aux journaux
Les métriques peuvent également être répliquées dans Log Analytics pour effectuer une analyse complexe avec d’autres types de données à l’aide de son langage de requête riche. Vous pouvez également conserver des données de journal plus longtemps que les métriques, ce qui vous permet d’analyser une tendance au fil du temps. Lorsque des métriques ou d’autres données de performances sont stockées dans Log Analytics, elles font office de journal. Utilisez des métriques pour prendre en charge l’analyse et la génération d’alertes en quasi temps réel lorsque vous utilisez des journaux à des fins d’analyse et de suivi de tendances avec d’autres données.

Pour trouver des conseils concernant la collecte de métriques à partir de ressources Azure, voir [Collecte des journaux et des métriques des services Azure à utiliser dans Log Analytics](../log-analytics/log-analytics-azure-storage.md). Pour obtenir des conseils concernant la collecte de métriques de ressources Azure PaaS, voir [Configurer la collecte de métriques de ressources Azure PaaS avec Log Analytics](../log-analytics/log-analytics-collect-azurepass-posh.md).

### <a name="logs-to-metrics"></a>Des journaux aux métriques
Comme décrit ci-dessus, les métriques sont plus réactives que les journaux, ce qui vous permet de créer des alertes avec une latence faible et à moindre coût. Log Analytics collecte une quantité significative de données numériques qui seraient appropriées pour des métriques, mais qui ne sont pas stockées dans Métriques Azure. Un exemple courant est celui des données de performances collectées auprès d’agents et de solutions de gestion. Certaines de ces valeurs peuvent être copiées dans Métriques Azure où elles sont disponibles pour la génération d’alertes et pour l’analyse avec Metrics Explorer.

Pour une explication de cette fonctionnalité, voir [alertes Métrique plus rapides pour les journaux désormais en préversion publique limitée](https://azure.microsoft.com/blog/faster-metric-alerts-for-logs-now-in-limited-public-preview/). Pour voir la liste des valeurs prises en charge, voir [Métriques et méthodes de création prises en charge pour les nouvelles alertes Métrique](../monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts.md).

### <a name="event-hub"></a>Event Hub
Outre ces outils dans Azure pour analyser les données de surveillance, il se peut que vous vouliez transférer ces données vers un outil externe tel qu’un produit SIEM (Security Information and Event Management). Cela s’effectue généralement à l’aide d’[Azure Event Hub](https://docs.microsoft.com/azure/event-hubs/). 

Pour obtenir des conseils concernant les différents types de données de surveillance, voir [Diffuser des données de surveillance Azure vers un hub d’événements pour les utiliser dans un outil externe](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md).

## <a name="next-steps"></a>Étapes suivantes

- Découvrez les [données de surveillance disponibles](monitoring-data-sources.md) pour différentes ressources dans Azure. 