---
title: Vue d’ensemble des agents de surveillance Azure | Microsoft Docs
description: Cet article fournit une présentation détaillée des agents Azure disponibles prenant en charge la surveillance de machines virtuelles Azure hébergées dans un environnement Azure ou hybride.
services: azure-monitor
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/12/2021
ms.openlocfilehash: a2f6023b86b96266be8e625fd5b0d6625500e3fc
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102551468"
---
# <a name="overview-of-azure-monitor-agents"></a>Vue d’ensemble des agents Azure Monitor

Les machines virtuelles et autres ressources de calcul nécessitent qu’un agent collecte les données de supervision nécessaires pour mesurer les performances et la disponibilité de leur système d’exploitation invité et de leurs charges de travail. Cet article décrit les agents utilisés par Azure Monitor et vous aide à déterminer ceux dont vous avez besoin pour répondre aux exigences de votre environnement.

> [!NOTE]
> Azure Monitor compte plusieurs agents en raison de son regroupement récent avec Log Analytics. Bien qu’il puisse y avoir des fonctionnalités qui se chevauchent, chacun possède des fonctionnalités uniques. Selon vos exigences, il peut vous falloir un ou plusieurs agents sur vos machines. 

Il se peut que vous ayez un ensemble spécifique d’exigences auxquelles un seul agent pour une machine donnée ne peut pas satisfaire. Par exemple, vous pouvez utiliser des alertes de métrique qui nécessitent l’extension Diagnostics Azure, mais souhaiter également tirer parti des fonctionnalités de VM Insights qui nécessitent l’agent Log Analytics et l’agent de dépendances. Dans un cas pareil, vous pouvez utiliser plusieurs agents ; il s’agit d’un scénario courant pour les clients qui nécessitent des fonctionnalités de chacun d’eux.

## <a name="summary-of-agents"></a>Résumé des agents

Les tableaux suivants permettent de comparer rapidement les agents Azure Monitor pour Windows et Linux. Pour plus d’informations sur chacun d’eux, reportez-vous à la section ci-après.

> [!NOTE]
> L’agent Azure Monitor est actuellement disponible en préversion avec des fonctionnalités limitées. Ce tableau sera mis à jour 

### <a name="windows-agents"></a>Agents Windows

| | Agent Azure Monitor (préversion) | Diagnostics<br>extension (WAD) | Log Analytics<br>agent | Dépendance<br>agent |
|:---|:---|:---|:---|:---|
| **Environnements pris en charge** | Azure<br>Autre cloud (Azure Arc)<br>Local (Azure Arc)  | Azure | Azure<br>Autre cloud<br>Local | Azure<br>Autre cloud<br>Local | 
| **Exigences liées à l’agent**  | None | None | None | Requiert l’agent Log Analytics |
| **Données collectées** | Journaux des événements<br>Performances | Journaux des événements<br>Événements ETW<br>Performances<br>Journaux basés sur des fichiers<br>Journaux d’activité IIS<br>Journaux d’application .NET<br>Vidages sur incident<br>Journaux de diagnostics de l’agent | Journaux des événements<br>Performances<br>Journaux basés sur des fichiers<br>Journaux d’activité IIS<br>Insights et solutions<br>Autres services | Dépendances de processus<br>Métriques de connexion réseau |
| **Données envoyées à** | Journaux Azure Monitor<br>Métriques Azure Monitor | Stockage Azure<br>Métriques Azure Monitor<br>Event Hub | Journaux Azure Monitor | Journaux Azure Monitor<br>(via l’agent Log Analytics) |
| **Services et**<br>**fonctionnalités**<br>**pris en charge** | Log Analytics<br>Metrics Explorer | Metrics Explorer | VM Insights<br>Log Analytics<br>Azure Automation<br>Azure Security Center<br>Azure Sentinel | VM Insights<br>Service Map |

### <a name="linux-agents"></a>Agents Linux

| | Agent Azure Monitor (préversion) | Diagnostics<br>extension (LAD) | Telegraf<br>agent | Log Analytics<br>agent | Dépendance<br>agent |
|:---|:---|:---|:---|:---|:---|
| **Environnements pris en charge** | Azure<br>Autre cloud (Azure Arc)<br>Local (Azure Arc) | Azure | Azure<br>Autre cloud<br>Local | Azure<br>Autre cloud<br>Local | Azure<br>Autre cloud<br>Local |
| **Exigences liées à l’agent**  | None | None | None | None | Requiert l’agent Log Analytics |
| **Données collectées** | syslog<br>Performances | syslog<br>Performances | Performances | syslog<br>Performances| Dépendances de processus<br>Métriques de connexion réseau |
| **Données envoyées à** | Journaux Azure Monitor<br>Métriques Azure Monitor | Stockage Azure<br>Event Hub | Métriques Azure Monitor | Journaux Azure Monitor | Journaux Azure Monitor<br>(via l’agent Log Analytics) |
| **Services et**<br>**fonctionnalités**<br>**pris en charge** | Log Analytics<br>Metrics Explorer | | Metrics Explorer | VM Insights<br>Log Analytics<br>Azure Automation<br>Azure Security Center<br>Azure Sentinel | VM Insights<br>Service Map |


## <a name="azure-monitor-agent-preview"></a>Agent Azure Monitor (préversion)

[L’agent Azure Monitor](azure-monitor-agent-overview.md), actuellement en préversion, remplacera l’agent Log Analytics et l’agent Telegraf pour les machines Windows et Linux. Il peut envoyer des données aux Journaux Azure Monitor et aux Métriques Azure Monitor, et utilise des [règles de collecte de données (DCR)](data-collection-rule-overview.md) qui offrent une méthode plus scalable pour la configuration de la collecte de données et des destinations pour chaque agent.

Utilisez l’agent Azure Monitor si vous avez besoin de faire ce qui suit :

- Collecter les journaux et les métriques d’invités sur n’importe quelle machine sur Azure, dans d’autres clouds ou en local. ([Serveurs avec Azure Arc](../../azure-arc/servers/overview.md) requis pour les machines extérieures à Azure.) 
- Envoyer des données aux Journaux Azure Monitor et aux Métriques Azure Monitor à des fins d’analyse avec Azure Monitor. 
- Envoyer des données à Stockage Azure à des fins d’archivage.
- Envoyer les données à des outils tiers à l’aide d’[Azure Event Hubs](./diagnostics-extension-stream-event-hubs.md).
- Gérer la sécurité des machines avec [Azure Security Center](../../security-center/security-center-introduction.md) ou [Azure Sentinel](../../sentinel/overview.md). (Non disponible en préversion.)

Les limites de l’agent Azure Monitor sont les suivantes :

- Actuellement disponible en préversion publique. Consultez [Limites actuelles](azure-monitor-agent-overview.md#current-limitations) pour obtenir la liste des limites de la préversion publique.

## <a name="log-analytics-agent"></a>Agent Log Analytics

[L’agent Log Analytics](./log-analytics-agent.md) collecte des données de monitoring à partir du système d’exploitation invité et des charges de travail des machines virtuelles sur Azure, des machines virtuelles d’autres fournisseurs de cloud et des machines locales. Il envoie les données à un espace de travail Log Analytics. L’agent Log Analytics est le même agent que celui utilisé par System Center Operations Manager et les ordinateurs d’agent multirésidents peuvent communiquer avec votre groupe d’administration et Azure Monitor simultanément. Cet agent est également nécessaire pour certains insights dans Azure Monitor et d’autres services dans Azure.

> [!NOTE]
> L’agent Log Analytics pour Windows est souvent appelé Microsoft Monitoring Agent (MMA). L’agent Log Analytics pour Linux est souvent appelé agent OMS.

Utilisez l’agent Log Analytics si vous avez besoin d’effectuer les opérations suivantes :

* Collecter des journaux et des données de performances à partir de machines virtuelles Azure ou de machines hybrides hébergées en dehors d’Azure.
* Envoyer les données à un espace de travail Log Analytics pour tirer parti des fonctionnalités prises en charge par les [journaux Azure Monitor](../logs/data-platform-logs.md) comme les [requêtes de journal](../logs/log-query-overview.md).
* Utilisez [VM Insights](../vm/vminsights-overview.md), qui vous permet de superviser vos machines à grande échelle, y compris leurs processus et dépendances vis-à-vis d’autres ressources et de processus externes.  
* Gérer la sécurité des machines avec [Azure Security Center](../../security-center/security-center-introduction.md) ou [Azure Sentinel](../../sentinel/overview.md).
* Utiliser [Azure Automation Update Management](../../automation/update-management/overview.md), [Azure Automation State Configuration](../../automation/automation-dsc-overview.md) ou le [Suivi des modifications et inventaire Azure Automation](../../automation/change-tracking/overview.md) pour assurer une gestion complète de vos machines Azure et autres.
* Utiliser des [solutions](../monitor-reference.md#insights-and-core-solutions) différentes pour superviser un service ou une application spécifique.

Les limitations de l’agent Log Analytics sont les suivantes :

- Impossible d’envoyer des données aux métriques Azure Monitor, à Stockage Azure ou à Azure Event Hubs.
- Difficile de configurer des définitions de supervision uniques pour chaque agent.
- Difficile de gérer à grande échelle dans la mesure où chaque machine virtuelle possède une configuration unique.

## <a name="azure-diagnostics-extension"></a>Extension Diagnostics Azure

L’[extension Diagnostics Azure](./diagnostics-extension-overview.md) collecte des données de supervision à partir du système d’exploitation invité et des charges de travail des machines virtuelles Azure et autres ressources de calcul. Elle collecte principalement les données dans le Stockage Azure, mais vous permet également de définir des récepteurs de données pour envoyer les données à d’autres destinations, telles que des métriques Azure Monitor et Azure Event Hubs.

Utilisez l’extension Diagnostics Azure si vous devez effectuer les opérations suivantes :

- Envoyer les données au Stockage Azure à des fins d’archivage ou pour les analyser à l’aide d’outils tels que l’[Explorateur Stockage Azure](../../vs-azure-tools-storage-manage-with-storage-explorer.md).
- Envoyer les données aux [métriques Azure Monitor](../essentials/data-platform-metrics.md) pour les analyser avec [Metrics Explorer](../essentials/metrics-getting-started.md) et tirer parti des fonctionnalités telles que les [alertes de métrique](../alerts/alerts-metric-overview.md) en temps quasi-réel et la [mise à l’échelle automatique](../autoscale/autoscale-overview.md) (Windows uniquement).
- Envoyer les données à des outils tiers à l’aide d’[Azure Event Hubs](./diagnostics-extension-stream-event-hubs.md).
- Collecter des [diagnostics de démarrage](../../virtual-machines/troubleshooting/boot-diagnostics.md) afin d’étudier les problèmes de démarrage de machines virtuelles.

Les limitations de l’extension Diagnostics Azure sont les suivantes :

- Peut uniquement être utilisée avec des ressources Azure.
- Capacité limitée à envoyer des données aux journaux Azure Monitor.

## <a name="telegraf-agent"></a>Agent Telegraf

L’[agent InfluxData Telegraf](../essentials/collect-custom-metrics-linux-telegraf.md) est utilisé pour collecter les données de performances des ordinateurs Linux sur les métriques Azure Monitor.

Utilisez l’agent Telegraf si vous avez besoin d’effectuer l’opération suivante :

* Envoyer les données aux [métriques Azure Monitor](../essentials/data-platform-metrics.md) pour les analyser avec [Metrics Explorer](../essentials/metrics-getting-started.md) et tirer parti des fonctionnalités telles que les [alertes de métrique](../alerts/alerts-metric-overview.md) en temps quasi-réel et la [mise à l’échelle automatique](../autoscale/autoscale-overview.md) (Linux uniquement).

## <a name="dependency-agent"></a>Agent de dépendances

L’agent de dépendances collecte les données découvertes sur les processus exécutés sur la machine virtuelle et les dépendances vis-à-vis de processus externes. 

Utilisez l’agent de dépendances si vous avez besoin d’effectuer l’opération suivante :

* Utilisez la fonctionnalité de carte [VM Insights](../vm/vminsights-overview.md) ou la solution [Service Map](../vm/service-map.md).

Tenez compte des éléments suivants lors de l’utilisation de l’agent de dépendances :

- L’agent Log Analytics doit être installé sur la même machine que l’agent de dépendances.
- Sur les ordinateurs Linux, l’agent Log Analytics doit être installé avant l’extension Diagnostics Azure.

## <a name="virtual-machine-extensions"></a>Extensions de machine virtuelle

L’extension Log Analytics pour [Windows](../../virtual-machines/extensions/oms-windows.md) et [Linux](../../virtual-machines/extensions/oms-linux.md) installe l’agent Log Analytics sur les machines virtuelles Azure. L’extension Azure Monitor Dependency pour [Windows](../../virtual-machines/extensions/agent-dependency-windows.md) et [Linux](../../virtual-machines/extensions/agent-dependency-linux.md) installe l’agent de dépendances sur les machines virtuelles Azure. Il s’agit des mêmes agents que ceux décrits ci-dessus, mais vous pouvez les gérer par le biais d’[extensions de machine virtuelle](../../virtual-machines/extensions/overview.md). Dans la mesure du possible, utilisez des extensions pour installer et gérer les agents.

Sur les machines hybrides, utilisez des [serveurs avec Azure Arc](../../azure-arc/servers/manage-vm-extensions.md) pour déployer les extensions de machines virtuelles Log Analytics et Azure Monitor Dependency.

## <a name="supported-operating-systems"></a>Systèmes d’exploitation pris en charge

Les tableaux suivants listent les systèmes d’exploitation pris en charge par les agents Azure Monitor. Consultez la documentation de chaque agent pour en connaître les spécificités et le processus d’installation. Consultez la documentation Telegraf pour connaître les systèmes d’exploitation pris en charge. Tous les systèmes d’exploitation sont censés être x64. L’architecture x86 n’est prise en charge pour aucun système d’exploitation.

### <a name="windows"></a>Windows

| Système d’exploitation | Agent Azure Monitor | Agent Log Analytics | Agent de dépendances | Extension Diagnostics | 
|:---|:---:|:---:|:---:|:---:|
| Windows Server 2019                                      | X | X | X | X |
| Windows Server 2016                                      | X | X | X | X |
| Windows Server 2016 Core                                 |   |   |   | X |
| Windows Server 2012 R2                                   | X | X | X | X |
| Windows Server 2012                                      | X | X | X | X |
| Windows Server 2008 R2 SP1                               | X | X | X | X |
| Windows Server 2008 R2                                   |   | X | X | X |
| Windows 10 Entreprise<br>(multisession inclus) et Pro<br>(Scénarios de serveur uniquement)  | X | X | X | X |
| Windows 8 Entreprise et Professionnel<br>(Scénarios de serveur uniquement)  |   | X | X |   |
| Windows 7 SP1<br>(Scénarios de serveur uniquement)                 |   | X | X |   |

### <a name="linux"></a>Linux

| Système d’exploitation | Agent Azure Monitor<sup>1</sup> | Agent Log Analytics<sup>1</sup> | Agent de dépendances | Extension Diagnostics<sup>2</sup>| 
|:---|:---:|:---:|:---:|:---:
| Amazon Linux 2017.09                                        |   | X |   |   |
| CentOS Linux 8                                              | X<sup>3</sup> | X | X |   |
| CentOS Linux 7                                              | X | X | X | X |
| CentOS Linux 6                                              |   | X |   |   |
| CentOS Linux 6.5+                                           |   | X | X | X |
| Debian 10 <sup>1</sup>                                      | X |   |   |   |
| Debian 9                                                    | X | X | x | X |
| Debian 8                                                    |   | X | X |   |
| Debian 7                                                    |   |   |   | X |
| OpenSUSE 13.1+                                              |   |   |   | X |
| Oracle Linux 8                                              | X<sup>3</sup> | X |   |   |
| Oracle Linux 7                                              | X | X |   | X |
| Oracle Linux 6                                              |   | X |   |   |
| Oracle Linux 6.4+                                           |   | X |   | X |
| Red Hat Enterprise Linux Server 8                           | X<sup>3</sup> | X | X |   |
| Red Hat Enterprise Linux Server 7                           | X | X | X | X |
| Red Hat Enterprise Linux Server 6                           |   | X | X |   |
| Red Hat Enterprise Linux Server 6.7+                        |   | X | X | X |
| SUSE Linux Enterprise Server 15.2                           | X<sup>3</sup> |   |   |   |
| SUSE Linux Enterprise Server 15.1                           | X<sup>3</sup> | X |   |   |
| SUSE Linux Enterprise Server 15                             | X | X | X |   |
| SUSE Linux Enterprise Server 12                             | X | X | X | X |
| Ubuntu 20.04 LTS                                            | X | X | X |   |
| Ubuntu 18.04 LTS                                            | X | X | X | X |
| Ubuntu 16.04 LTS                                            | X | X | X | X |
| Ubuntu 14.04 LTS                                            |   | X |   | X |

<sup>1</sup> Exige l’installation de Python (2 or 3) sur la machine.

<sup>2</sup> Exige l’installation de Python 2 sur la machine.

<sup>3</sup> Problème connu lié à la collecte d’événements Syslog. Seules les données de performances sont actuellement prises en charge.
#### <a name="dependency-agent-linux-kernel-support"></a>Prise en charge du noyau Linux par Dependency Agent

Sachant que Dependency Agent fonctionne au niveau du noyau, la prise en charge dépend aussi de la version du noyau. Le tableau suivant liste les versions majeures et mineures du système d’exploitation Linux ainsi que les versions de noyau prises en charge pour Dependency Agent.

| Distribution | Version du SE | Version du noyau |
|:---|:---|:---|
|  Red Hat Linux 8   | 8,2     | 4.18.0-193.\*el8_2.x86_64 |
|                    | 8.1     | 4.18.0-147.\*el8_1.x86_64 |
|                    | 8.0     | 4.18.0-80.\*el8.x86_64<br>4.18.0-80.\*el8_0.x86_64 |
|  Red Hat Linux 7   | 7.9     | 3.10.0-1160 |
|                    | 7.8     | 3.10.0-1136 |
|                    | 7,7     | 3.10.0-1062 |
|                    | 7.6     | 3.10.0-957  |
|                    | 7.5     | 3.10.0-862  |
|                    | 7.4     | 3.10.0-693  |
| Red Hat Linux 6    | 6.10    | 2.6.32-754 |
|                    | 6.9     | 2.6.32-696  |
| CentOS Linux 8     | 8,2     | 4.18.0-193.\*el8_2.x86_64 |
|                    | 8.1     | 4.18.0-147.\*el8_1.x86_64 |
|                    | 8.0     | 4.18.0-80.\*el8.x86_64<br>4.18.0-80.\*el8_0.x86_64 |
| CentOS Linux 7     | 7.9     | 3.10.0-1160 |
|                    | 7.8     | 3.10.0-1136 |
|                    | 7,7     | 3.10.0-1062 |
| CentOS Linux 6     | 6.10    | 2.6.32-754.3.5<br>2.6.32-696.30.1 |
|                    | 6.9     | 2.6.32-696.30.1<br>2.6.32-696.18.7 |
| Serveur Ubuntu      | 20.04   | 5.4\* |
|                    | 18,04   | 5.3.0-1020<br>5.0 (inclut un noyau optimisé pour Azure)<br>4.18 *<br>4.15* |
|                    | 16.04.3 | 4.15.\* |
|                    | 16.04   | 4.13.\*<br>4.11.\*<br>4.10.\*<br>4.8.\*<br>4.4.\* |
| SUSE Linux 12 Enterprise Server | 15     | 4.12.14-150\*
|                                 | 12 SP4 | 4.12.* (inclut un noyau optimisé pour Azure) |
|                                 | 12 SP3 | 4.4.* |
|                                 | 12 SP2 | 4.4.* |
| Debian                          | 9      | 4,9  | 

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur chacun des agents, consultez les articles suivants :

- [Vue d’ensemble de l’agent Log Analytics](./log-analytics-agent.md)
- [Vue d’ensemble de l’extension Diagnostics Azure](./diagnostics-extension-overview.md)
- [Collecter des métriques personnalisées pour une machine virtuelle Linux avec l’agent InfluxData Telegraf](../essentials/collect-custom-metrics-linux-telegraf.md)
