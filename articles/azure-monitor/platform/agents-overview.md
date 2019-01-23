---
title: Vue d’ensemble des agents de surveillance Azure | Microsoft Docs
description: Cet article fournit une présentation détaillée des agents Azure disponibles prenant en charge la surveillance de machines virtuelles Azure hébergées dans un environnement Azure ou hybride.
services: azure-monitor
documentationcenter: azure-monitor
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/14/2018
ms.author: magoedte
ms.openlocfilehash: dececc961e40d83cf44442dc61054bc8833208f0
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/14/2019
ms.locfileid: "54265026"
---
# <a name="overview-of-the-azure-monitoring-agents"></a>Vue d’ensemble des agents de surveillance Azure 
Microsoft Azure offre plusieurs moyens de collecter différents types de données de machines virtuelles exécutant Microsoft Windows et Linux hébergées sur Azure, dans votre centre de données ou chez d’autres fournisseurs de cloud. Les trois types d’agents suivants sont disponibles pour surveiller une machine virtuelle :

* Extensions Azure Diagnostics
* Agent Log Analytics pour Linux et Windows
* Agent de dépendances

Cet article décrit les différences entre eux afin que vous puissiez déterminer lequel prendra en charge la gestion de votre service informatique ou les exigences générales de surveillance.  

## <a name="azure-diagnostic-extension"></a>Extension Azure Diagnostics
L’[extension Azure Diagnostics](../../azure-monitor/platform/diagnostics-extension-overview.md) (couramment appelée extension Windows Azure Diagnostics (WAD) ou Linux Azure Diagnostics (LAD)), qui a été fournie pour Azure Cloud Services depuis qu’il a été mis à la disposition générale en 2010, est un agent qui fournit une collection simple de données de diagnostic à partir d’une ressource de calcul Azure comme une machine virtuelle, et la conserve dans un élément de stockage Azure. Une fois dans le stockage, vous pouvez choisir de l’afficher avec l’un des outils disponibles, tels que [Explorateur de serveurs dans Visual Studio](/visualstudio/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage) et [Explorateur Stockage Azure](../../vs-azure-tools-storage-manage-with-storage-explorer.md).

Vous pouvez choisir de collecter :

* Un ensemble prédéfini de compteurs de performance et de journaux d’événements du système d’exploitation, ou vous pouvez spécifier ce que vous souhaitez collecter. 
* Toutes les requêtes et/ou requêtes ayant échoué vers un serveur web IIS
* Journaux de sortie de suivi d’application .NET
* Suivi d’événements pour les événements Windows (ETW) 
* Collecter les événements de journaux de syslog  
* Vidages sur incident 

L’agent Azure Diagnostics doit être utilisé lorsque vous souhaitez effectuer les opérations suivantes :

* Archiver des journaux et des mesures dans le stockage Azure
* Intégrer des données de surveillance dans des outils tiers. Ces outils utilisent une variété de méthodes incluant l'interrogation du compte de stockage, transféré vers [Event Hubs](../../event-hubs/event-hubs-about.md), ou l'interrogation avec [Azure Monitoring REST API](../../azure-monitor/platform/rest-api-walkthrough.md).
* Charger des données dans Azure Monitor pour créer des graphiques de métriques dans le portail Azure ou créer quasiment en temps réel [alertes de métriques](../../azure-monitor/platform/alerts-metric-overview.md). 
* Mettre à l’échelle automatiquement des jeux de mise à l’échelle de machines virtuelles et des services cloud classiques sur la base de métriques du système d'exploitation.
* Étudier les problèmes de démarrage de machines virtuelles grâce à des [Diagnostics de démarrage](../../virtual-machines/troubleshooting/boot-diagnostics.md).
* Comprendre le fonctionnement de vos applications et identifier de façon proactive les problèmes qui les affectent avec [Application Insights](../../azure-monitor/overview.md).
* Configurer Log Analytics pour importer des métriques et journaliser des données collectées à partir de services coud, de machines virtuelles classiques et de nœuds Service Fabric stockés dans un compte de stockage Azure.

## <a name="log-analytics-agent"></a>Agent Log Analytics
Pour une surveillance avancée où vous devez faire plus que collecter des métriques et d’un sous-ensemble de journaux, l’agent Log Analytics pour Windows et Linux est nécessaire. L’agent Log Analytics a été développé pour une gestion complète des machines physiques locales et virtuelles, des ordinateurs surveillés par System Center Operations Manager et des machines virtuelles hébergées dans d’autres clouds. Les agents Windows et Linux se connectent à un espace de travail Log Analytics pour collecter des données de surveillance basées sur des solutions ainsi que des sources de données personnalisées que vous configurez.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

L’agent Log Analytics doit être utilisé lorsque vous souhaitez :

* collecter des données à partir de diverses sources au sein d’Azure, d’autres fournisseur de cloud et de ressources locales. 
* Utilisation de l’une des solutions de surveillance Azure Monitor, telles qu’[Azure Monitor pour machines virtuelles](../insights/vminsights-overview.md), [Azure Monitor pour conteneurs](../insights/container-insights-overview.md), etc.  
* Utiliser l’un des autres services de gestion Azure, tel qu’[Azure Security Center](../../security-center/security-center-intro.md), [Azure Automation](../../automation/automation-intro.md), etc.

Auparavant, plusieurs services Azure étaient regroupés dabs *Operations Management Suite* et, par conséquent, l’agent Log Analytics est partagé entre des services, notamment Azure Security Center et Azure Automation.  Ceci inclut l’ensemble complet des fonctionnalités qu’ils offrent et fournit une gestion complète de vos machines virtuelles Azure pendant tout leur cycle de vie.  Quelques exemples :

* [Gestion de la mise à jour d’Azure Automation](../../automation/automation-update-management.md) des mises à jour du système d’exploitation.
* [La configuration de l’état désiré d’Azure Automation](../../automation/automation-dsc-overview.md) pour maintenir un état de configuration cohérent.
* Le suivi des modifications de configuration avec [Azure Automation Change Tracking and Inventory](../../automation/automation-change-tracking.md).
* Les services Azure tels que [Application Insights](https://docs.microsoft.com/azure/application-insights/) et [Azure Security Center](https://docs.microsoft.com/azure/security-center/), qui stockent leurs données directement dans Log Analytics.  

## <a name="dependency-agent"></a>Agent de dépendances
L’agent de dépendances a été développé dans le cadre de la solution Service Map qui, à l’origine, a été développée en externe par Microsoft. [Service Map](../insights/service-map.md) et [Azure Monitor pour machines virtuelles](../insights/vminsights-overview.md) nécessitent un agent de dépendance sur les machines virtuelles Windows et Linux et s’intègrent dans l’agent Log Analytics pour collecter des données découvertes relatives aux processus en cours d’exécution sur la machine virtuelle et aux dépendances de processus externes. Elle stocke ces données dans Log Analytics et visualise les composants interconnectés découverts.

Vous devez peut-être combiner ces agents pour surveiller votre machine virtuelle. Les agents peuvent être installés côte à côte en tant qu’extensions Azure. Cependant, sur Linux, l’agent Log Analytics *doit* être installé en premier, sinon l’installation échouera. 

## <a name="next-steps"></a>Étapes suivantes

- Consultez [Vue d’ensemble de l’agent Log Analytics](../../azure-monitor/platform/log-analytics-agent.md) afin de passer en revue les exigences et les méthodes prises en charge pour déployer l’agent sur des machines hébergées dans Azure, dans votre centre de données, ou dans un autre environnement cloud.

