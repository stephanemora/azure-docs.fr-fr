---
title: Vue d’ensemble des agents de surveillance Azure | Microsoft Docs
description: Cet article fournit une présentation détaillée des agents Azure disponibles prenant en charge la surveillance de machines virtuelles Azure hébergées dans un environnement Azure ou hybride.
services: azure-monitor
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/15/2019
ms.openlocfilehash: a01258799efa81c8d3ddba398facaa90c24c2513
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74150030"
---
# <a name="overview-of-the-azure-monitor-agents"></a>Vue d’ensemble des agents Azure Monitor 
Les ressources de calcul telles que les machines virtuelles génèrent des données qui permettent de superviser leurs performances et leur disponibilité, à l’image des [autres ressources cloud](../insights/monitor-azure-resource.md). Toutefois, les ressources de calcul comportent également un système d’exploitation invité et des charges de travail qui doivent être supervisés. La collecte de ces données de supervision à partir de la ressource nécessite un agent. Cet article décrit les agents utilisés par Azure Monitor et vous aide à déterminer ceux dont vous avez besoin pour répondre aux exigences de votre environnement.

## <a name="summary-of-agents"></a>Résumé des agents

> [!NOTE]
> Azure Monitor compte plusieurs agents en raison de son regroupement récent avec Log Analytics. Les deux agents partagent certaines fonctionnalités, tandis que d’autres sont propres à l’un ou l’autre. Selon vos besoins, vous devrez peut-être avoir l’un des agents ou les deux. 

Azure Monitor a trois agents qui fournissent chacun des fonctionnalités spécifiques. En fonction de vos besoins, vous pouvez installer un seul agent ou plusieurs sur vos machines virtuelles et les autres ressources de calcul.

* [Extension Diagnostics Azure](#azure-diagnostic-extension)
* [Agent Log Analytics](#log-analytics-agent)
* [Agent de dépendances](#dependency-agent)

Le tableau suivant fournit une comparaison rapide des différents agents. Pour plus d’informations sur chacun d’eux, consultez le reste de cet article.

| | Extension Diagnostics Azure | Agent Log Analytics | Agent de dépendances |
|:---|:---|:---|:---|
| Environnements pris en charge | Azure | Azure<br>Autre cloud<br>Local | Azure<br>Autre cloud<br>Local |
| Systèmes d’exploitation | Windows<br>Linux | Windows<br>Linux | Windows<br>Linux
| Dépendances d’agent  | Aucun | Aucun | Requiert l’agent Log Analytics |
| Données collectées | Journaux des événements<br>Événements ETW<br>syslog<br>Performances<br>Journaux d’activité IIS<br>Journaux d’activité de sortie de suivi d’application .NET<br>Vidages sur incident | Journaux des événements<br>syslog<br>Performances<br>Journaux d’activité IIS<br>Journaux d’activité personnalisés<br>Données à partir de solutions | Détails et dépendances du processus<br>Métriques de connexion réseau |
| Données envoyées à | Stockage Azure<br>Métriques Azure Monitor<br>Event Hub | Journaux Azure Monitor | Journaux Azure Monitor |



## <a name="azure-diagnostic-extension"></a>Extension Azure Diagnostics
L’[extension Diagnostics Azure](../../azure-monitor/platform/diagnostics-extension-overview.md) collecte des données de surveillance à partir du système d’exploitation invité et des charges de travail des ressources de calcul Azure. Elle collecte principalement des données dans le Stockage Azure. Vous pouvez configurer Azure Monitor pour qu’il copie les données du stockage dans un espace de travail Log Analytics. Vous pouvez également collecter les données de performances de l’invité dans les métriques Azure Monitor.

L’extension Diagnostics Azure est souvent appelée extension de diagnostic Windows Azure (WAD) ou Linux Azure (LAD).


### <a name="scenarios-supported"></a>Scénarios pris en charge

Les scénarios pris en charge par l’extension Diagnostics Azure sont les suivants :

* Collecter les journaux et données de performances à partir des ressources de calcul Azure.
* Archiver les journaux et données de performances du système d’exploitation invité vers le stockage Azure.
* Afficher les données de supervision dans le stockage à l’aide d’un outil tel que l’[Explorateur Stockage Azure](../../vs-azure-tools-storage-manage-with-storage-explorer.md).
* Collecter les données de performances dans une base de données de métriques pour tirer parti des fonctionnalités prises en charge par les [métriques Azure Monitor](data-platform-metrics.md) telles que les [alertes de métriques](../../azure-monitor/platform/alerts-metric-overview.md) en temps quasi-réel et la [mise à l’échelle automatique](autoscale-overview.md). 
* Collecter les données de supervision du [stockage dans un espace de travail Log Analytics](azure-storage-iis-table.md) pour tirer parti des fonctionnalités prises en charge par les [journaux Azure Monitor](data-platform-logs.md#what-can-you-do-with-azure-monitor-logs) comme les [requêtes de journal](../log-query/log-query-overview.md).
* Envoyer les données de supervision à des outils tiers à l’aide d’[Azure Event Hubs](diagnostics-extension-stream-event-hubs.md).
* Étudier les problèmes de démarrage de machines virtuelles grâce à des [Diagnostics de démarrage](../../virtual-machines/troubleshooting/boot-diagnostics.md).
* Copier les données des applications qui s’exécutent dans votre machine virtuelle [vers Application Insights](diagnostics-extension-to-application-insights.md) pour les intégrer à d’autres supervisions d’applications.

## <a name="log-analytics-agent"></a>Agent Log Analytics
L’[agent Log Analytics](log-analytics-agent.md) collecte des données de surveillance à partir du système d’exploitation invité et des charges de travail des machines virtuelles dans Azure, d’autres fournisseurs cloud et en local. Il collecte les données dans un espace de travail Log Analytics.

L’agent Log Analytics est le même agent que celui utilisé par System Center Operations Manager et les ordinateurs d’agent multirésidents pour communiquer avec votre groupe d’administration et Azure Monitor simultanément. Cet agent est également requis par certaines solutions dans Azure Monitor.

L’agent Log Analytics pour Windows est souvent appelé Microsoft Management Agent (MMA). L’agent Log Analytics pour Linux est souvent appelé agent OMS.


### <a name="scenarios-supported"></a>Scénarios pris en charge

Les scénarios pris en charge par l’agent Log Analytics sont les suivants :

* Collecter les journaux et les données de performances à partir de machines virtuelles dans Azure, d’autres fournisseurs de cloud et localement. 
* Collecter les données de supervision dans un espace de travail Log Analytics pour tirer parti des fonctionnalités prises en charge par les [journaux Azure Monitor](data-platform-logs.md#what-can-you-do-with-azure-monitor-logs) comme les [requêtes de journal](../log-query/log-query-overview.md).
* Utiliser les solutions de supervision Azure Monitor, telles qu’[Azure Monitor pour machines virtuelles](../insights/vminsights-overview.md), [Azure Monitor pour conteneurs](../insights/container-insights-overview.md), etc.  
* Gérer la sécurité de vos machines virtuelles à l’aide d’[Azure Sentinel](../../sentinel/overview.md), ce qui nécessite le recourt à l’agent Log Analytics.
* Gérer la sécurité de vos machines virtuelles à l’aide d’[Azure Security Center](../../security-center/security-center-intro.md), ce qui nécessite le recourt à l’agent Log Analytics.
* Utiliser les fonctionnalités d’[Azure Automation](../../automation/automation-intro.md) pour assurer une gestion complète de vos machines virtuelles Azure tout au long de leur cycle de vie.  Voici quelques exemples de ces fonctionnalités qui nécessitent l’agent Log Analytics :
  * [Gestion de la mise à jour d’Azure Automation](../../automation/automation-update-management.md) des mises à jour du système d’exploitation.
  * [Azure Automation State Configuration](../../automation/automation-dsc-overview.md) pour maintenir un état de configuration cohérent.
  * Le suivi des modifications de configuration avec [Azure Automation Change Tracking and Inventory](../../automation/change-tracking.md).

## <a name="dependency-agent"></a>Agent de dépendances
L’agent de dépendances collecte les données découvertes sur les processus exécutés sur la machine virtuelle et les dépendances de processus externes. Cet agent est requis pour [Service Map](../insights/service-map.md) et la fonctionnalité Cartographie d’[Azure Monitor pour machines virtuelles](../insights/vminsights-overview.md). L’agent de dépendances, qui requiert l’agent Log Analytics, écrit des données dans un espace de travail Log Analytics au sein d’Azure Monitor.


## <a name="using-multiple-agents"></a>Utilisation de plusieurs agents
Des exigences spécifiques peuvent vous amener à utiliser l’extension Diagnostics Azure ou l’agent Log Analytics pour une machine virtuelle particulière. Par exemple, vous souhaiterez peut-être utiliser des alertes de métrique qui nécessitent l’extension Diagnostics Azure. Toutefois, vous pouvez également souhaiter utiliser la fonctionnalité Cartographie d’Azure Monitor pour machines virtuelles, qui requiert l’agent de dépendances et l’agent Log Analytics. Dans ce cas, vous pouvez utiliser plusieurs agents ; il s’agit d’un scénario courant pour les clients qui requièrent des fonctionnalités de chacun d’eux.

### <a name="considerations"></a>Considérations

- L’agent de dépendances requiert l’installation de l’agent Log Analytics sur la même machine virtuelle.
- Sur les machines virtuelles Linux, l’agent Log Analytics doit être installé avant l’extension Diagnostics Azure.


## <a name="next-steps"></a>Étapes suivantes

- Consultez [Vue d’ensemble de l’agent Log Analytics](../../azure-monitor/platform/log-analytics-agent.md) afin de passer en revue les exigences et les méthodes prises en charge pour déployer l’agent sur des machines hébergées dans Azure, dans votre centre de données, ou dans un autre environnement cloud.

