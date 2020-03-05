---
title: Vue d’ensemble des agents de surveillance Azure | Microsoft Docs
description: Cet article fournit une présentation détaillée des agents Azure disponibles prenant en charge la surveillance de machines virtuelles Azure hébergées dans un environnement Azure ou hybride.
services: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/14/2020
ms.openlocfilehash: a7e6a3a299df8112fe4fbcf457516894c1766b8c
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2020
ms.locfileid: "77668517"
---
# <a name="overview-of-azure-monitor-agents"></a>Vue d’ensemble des agents Azure Monitor

Les machines virtuelles et autres ressources de calcul nécessitent qu’un agent collecte des données de supervision pour mesurer les performances et la disponibilité de leur système d’exploitation invité et de leurs charges de travail. Cet article décrit les agents utilisés par Azure Monitor et vous aide à déterminer ceux dont vous avez besoin pour répondre aux exigences de votre environnement.

> [!NOTE]
> Azure Monitor compte plusieurs agents en raison de son regroupement récent avec Log Analytics. Bien qu’il puisse y avoir des fonctionnalités qui se chevauchent, chacun possède des fonctionnalités uniques. Selon vos exigences, vous pouvez avoir besoin d’un ou de plusieurs agents sur vos machines virtuelles. 

Vous pouvez avoir un ensemble spécifique d’exigences qui ne peuvent pas être complètement satisfaites avec un seul agent pour une machine virtuelle particulière. Par exemple, vous pouvez utiliser des alertes de métrique qui nécessitent l’extension Diagnostics Azure, mais souhaiter également tirer parti des fonctionnalités d’Azure Monitor pour machines virtuelles qui nécessitent l’agent Log Analytics et l’agent de dépendances. Dans un cas pareil, vous pouvez utiliser plusieurs agents ; il s’agit d’un scénario courant pour les clients qui nécessitent des fonctionnalités de chacun d’eux.

## <a name="summary-of-agents"></a>Résumé des agents

Les tableaux suivants permettent de comparer rapidement les agents Azure Monitor pour Windows et Linux. Pour plus d’informations sur chacun d’eux, reportez-vous à la section ci-après. 

### <a name="windows-agents"></a>Agents Windows

| | Diagnostics<br>extension (WAD) | Log Analytics<br>agent | Dépendance<br>agent |
|:---|:---|:---|:---|
| Environnements pris en charge | Azure | Azure<br>Autre cloud<br>Local | Azure<br>Autre cloud<br>Local | 
| Exigences liées à l’agent  | None | None | Requiert l’agent Log Analytics |
| Données collectées | Journaux des événements<br>Événements ETW<br>Performances<br>Journaux basés sur des fichiers<br>Journaux d’activité IIS<br>Journaux d’application .NET<br>Vidages sur incident<br>Journaux de diagnostics de l’agent | Journaux des événements<br>Performances<IIS logs><br>Journaux basés sur des fichiers<br>Insights et solutions<br>Autres services | Détails et dépendances du processus<br>Métriques de connexion réseau |
| Données envoyées à | Stockage Azure<br>Métriques Azure Monitor<br>Event Hub | Journaux Azure Monitor | Journaux Azure Monitor |


### <a name="linux-agents"></a>Agents Linux

| | Diagnostics<br>extension (LAD) | Telegraf<br>agent | Log Analytics<br>agent | Dépendance<br>agent |
|:---|:---|:---|:---|:---|
| Environnements pris en charge | Azure | Azure<br>Autre cloud<br>Local | Azure<br>Autre cloud<br>Local | Azure<br>Autre cloud<br>Local |
| Exigences liées à l’agent  | None | None | None | Requiert l’agent Log Analytics |
| Données collectées | syslog<br>Performances | Performances | syslog<br>Performances| Détails et dépendances du processus<br>Métriques de connexion réseau |
| Données envoyées à | Stockage Azure<br>Event Hub | Métriques Azure Monitor | Journaux Azure Monitor | Journaux Azure Monitor |

## <a name="log-analytics-agent"></a>Agent Log Analytics

L’[agent Log Analytics](log-analytics-agent.md) collecte des données de surveillance à partir du système d’exploitation invité et des charges de travail des machines virtuelles dans Azure, d’autres fournisseurs cloud et en local. Il collecte les données dans un espace de travail Log Analytics. L’agent Log Analytics est le même agent que celui utilisé par System Center Operations Manager et les ordinateurs d’agent multirésidents peuvent communiquer avec votre groupe d’administration et Azure Monitor simultanément. Cet agent est également requis par certains insights et solutions dans Azure Monitor.


> [!NOTE]
> L’agent Log Analytics pour Windows est souvent appelé Microsoft Monitoring Agent (MMA). L’agent Log Analytics pour Linux est souvent appelé agent OMS.



Utilisez l’agent Log Analytics si vous avez besoin d’effectuer les opérations suivantes :

* Collecter des journaux et des données de performances à partir de machines physiques ou virtuelles en dehors d’Azure. 
* Envoyer les données à un espace de travail Log Analytics pour tirer parti des fonctionnalités prises en charge par les [journaux Azure Monitor](data-platform-logs.md#what-can-you-do-with-azure-monitor-logs) comme les [requêtes de journal](../log-query/log-query-overview.md).
* Utiliser [Azure Monitor pour machines virtuelles](../insights/vminsights-overview.md), qui vous permet de superviser vos machines virtuelles à grande échelle et qui supervise leurs processus et dépendances vis-à-vis d’autres ressources et processus externes.  
* Gérer la sécurité de vos machines virtuelles à l’aide d’[Azure Security Center](../../security-center/security-center-intro.md) ou d’[Azure Sentinel](../../sentinel/overview.md).
* Utiliser [Azure Automation Update Management](../../automation/automation-update-management.md), [Azure Automation State Configuration](../../automation/automation-dsc-overview.md) ou [Azure Automation Change Tracking and Inventory](../../automation/change-tracking.md) pour offrir une gestion complète de vos machines virtuelles Azure.
* Utiliser des [solutions](../monitor-reference.md#insights-and-core-solutions) différentes pour superviser un service ou une application spécifique.

Les limitations de l’agent Log Analytics sont les suivantes :

- Impossible d’envoyer des données aux métriques Azure Monitor, à Stockage Azure ou à Azure Event Hubs.

## <a name="azure-diagnostics-extension"></a>Extension Diagnostics Azure

L’[extension Diagnostics Azure](diagnostics-extension-overview.md) collecte des données de supervision à partir du système d’exploitation invité et des charges de travail des machines virtuelles Azure et autres ressources de calcul. Elle collecte principalement les données dans le Stockage Azure, mais vous permet également de définir des récepteurs de données pour envoyer les données à d’autres destinations, telles que des métriques Azure Monitor et Azure Event Hubs.

Utilisez l’extension Diagnostics Azure si vous devez effectuer les opérations suivantes :

- Envoyer les données au Stockage Azure à des fins d’archivage ou pour les analyser à l’aide d’outils tels que l’[Explorateur Stockage Azure](../../vs-azure-tools-storage-manage-with-storage-explorer.md).
- Envoyer les données aux [métriques Azure Monitor](data-platform-metrics.md) pour les analyser avec [Metrics Explorer](metrics-getting-started.md) et tirer parti des fonctionnalités telles que les [alertes de métrique](../../azure-monitor/platform/alerts-metric-overview.md) en temps quasi-réel et la [mise à l’échelle automatique](autoscale-overview.md) (Windows uniquement).
- Envoyer les données à des outils tiers à l’aide d’[Azure Event Hubs](diagnostics-extension-stream-event-hubs.md).
- Collecter des [diagnostics de démarrage](../../virtual-machines/troubleshooting/boot-diagnostics.md) afin d’étudier les problèmes de démarrage de machines virtuelles.

Les limitations de l’extension Diagnostics Azure sont les suivantes :

- Peut uniquement être utilisée avec des ressources Azure.
- Capacité limitée à envoyer des données aux journaux Azure Monitor.

## <a name="telegraf-agent"></a>Agent Telegraf

L’[agent InfluxData Telegraf](collect-custom-metrics-linux-telegraf.md) est utilisé pour collecter les données de performances des ordinateurs Linux sur les métriques Azure Monitor.

Utilisez l’agent Telegraf si vous avez besoin d’effectuer l’opération suivante :

* Envoyer les données aux [métriques Azure Monitor](data-platform-metrics.md) pour les analyser avec [Metrics Explorer](metrics-getting-started.md) et tirer parti des fonctionnalités telles que les [alertes de métrique](../../azure-monitor/platform/alerts-metric-overview.md) en temps quasi-réel et la [mise à l’échelle automatique](autoscale-overview.md) (Linux uniquement). 



## <a name="dependency-agent"></a>Agent de dépendances

L’agent de dépendances collecte les données découvertes sur les processus exécutés sur la machine virtuelle et les dépendances de processus externes. 

Utilisez l’agent de dépendances si vous avez besoin d’effectuer l’opération suivante :

* Utiliser la fonctionnalité de carte [Azure Monitor pour machines virtuelles](../insights/vminsights-overview.md) ou la solution [Service Map](../insights/service-map.md).

Tenez compte des éléments suivants lors de l’utilisation de l’agent de dépendances :

- L’agent de dépendances requiert l’installation de l’agent Log Analytics sur la même machine virtuelle.
- Sur les machines virtuelles Linux, l’agent Log Analytics doit être installé avant l’extension Diagnostics Azure.

## <a name="extensions-compared-to-agents"></a>Comparaison des extensions aux agents

L’extension Log Analytics pour [Windows](../../virtual-machines/extensions/oms-windows.md) et [Linux](../../virtual-machines/extensions/oms-linux.md) installe l’agent Log Analytics sur les machines virtuelles Azure. L’extension Azure Monitor Dependency pour [Windows](../../virtual-machines/extensions/agent-dependency-windows.md) et [Linux](../../virtual-machines/extensions/agent-dependency-linux.md) installe l’agent de dépendances sur les machines virtuelles Azure. Il s’agit des mêmes agents que ceux décrits ci-dessus, mais vous pouvez les gérer par le biais d’[extensions de machine virtuelle](../../virtual-machines/extensions/overview.md). Dans la mesure du possible, utilisez des extensions pour installer et gérer les agents.


## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur chacun des agents, consultez les articles suivants :

- [Vue d’ensemble de l’agent Log Analytics](log-analytics-agent.md)
- [Vue d’ensemble de l’extension Diagnostics Azure](diagnostics-extension-overview.md)
- [Collecter des métriques personnalisées pour une machine virtuelle Linux avec l’agent InfluxData Telegraf](collect-custom-metrics-linux-telegraf.md)
