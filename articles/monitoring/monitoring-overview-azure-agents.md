---
title: Vue d’ensemble des agents de surveillance Azure | Microsoft Docs
description: Cet article fournit une présentation détaillée des agents Azure disponibles prenant en charge la surveillance de machines virtuelles Azure.
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2018
ms.author: magoedte
ms.openlocfilehash: 62edeb36fcd56733630edc6fa27c9963f20b0186
ms.sourcegitcommit: 799a4da85cf0fec54403688e88a934e6ad149001
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2018
ms.locfileid: "50913544"
---
# <a name="overview-of-the-azure-agents-to-monitor-azure-virtual-machines"></a>Présentation des agents Azure pour surveiller les machines virtuelles Azure
Microsoft Azure offre plusieurs moyens de collecter différents types de données des machines virtuelles hébergées dans Azure ou d’autres fournisseurs de cloud s’exécutant sur Microsoft Windows et Linux.  Cet article décrit les différences et les fonctionnalités disponibles pour chaque agent afin que vous puissiez déterminer celui qui prendra en charge la gestion de votre service ou les exigences générales de surveillance.  

## <a name="comparing-azure-diagnostic-and-log-analytics-agent"></a>Comparaison d’Azure Diagnostic et de l’agent Log Analytics
Aujourd’hui, dans Azure, il existe deux types d’agents disponibles pour surveiller une machine virtuelle Azure - l’extension Azure Diagnostics et l’agent Log Analytics pour Linux et Windows.  Fondamentalement, ces agents sont conçus pour collecter les métriques et journaux, et les envoyer à un référentiel. Toutefois, ce sont leurs uniques points communs.  

L’[extension Azure Diagnostics](../monitoring-and-diagnostics/azure-diagnostics.md), qui a été fournie pour Azure Cloud Services depuis qu’il a été mis à la disposition générale en 2010, est un agent qui fournit une collection simple de données de diagnostic à partir d’une ressource Azure IaaS comme une machine virtuelle, et la conserve dans un élément de stockage Azure.  Une fois dans le stockage, vous pouvez choisir de l’afficher avec l’un des outils disponibles, tels que [Explorateur de serveurs dans Visual Studio](../vs-azure-tools-storage-resources-server-explorer-browse-manage.md) et [Explorateur Stockage Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md).

Vous pouvez choisir de collecter :

* Un ensemble prédéfini de compteurs de performance et de journaux d’événements du système d’exploitation, ou vous pouvez spécifier ce que vous souhaitez collecter. 
* Toutes les requêtes et/ou requêtes ayant échoué vers un serveur web IIS
* Journaux de sortie de suivi d’application .NET
* Suivi d’événements pour les événements Windows (ETW) 
* Collecter les événements de journaux de syslog  
* Vidages sur incident 

Les données peuvent également être transmises à [Application Insights](../application-insights/app-insights-cloudservices.md), [Log Analytics](../log-analytics/log-analytics-overview.md), ou des services non-Azure à l’aide d’[Event Hub](../event-hubs/event-hubs-about.md). 

Pour une surveillance avancée pour laquelle vous avez besoin de quelque chose de plus que la collecte des métriques et d’un sous-ensemble de journaux, l’agent Log Analytics pour Windows et Linux est nécessaire.  Avec cet agent, vous êtes en mesure d’utiliser les services Azure tels qu’Automation et Log Analytics, ainsi que l’ensemble complet des fonctionnalités qu’ils offrent, pour une gestion complète de vos machines virtuelles Azure au cours de leur cycle de vie, notamment :

* [La gestion de la mise à jour d’Azure Automation](../automation/automation-update-management.md) des mises à jour du système d’exploitation
* [La configuration de l’état désiré d’Azure Automation](../automation/automation-dsc-overview.md) pour maintenir un état de configuration cohérent
* Le suivi des modifications de configuration avec [Azure Automation Change Tracking and Inventory](../automation/automation-change-tracking.md)
* La collection de journaux personnalisés à partir du système d’exploitation et des applications hébergées comme [FluentD](../log-analytics/log-analytics-data-sources-json.md), [les journaux personnalisés](../log-analytics/log-analytics-data-sources-custom-logs.md), [MySQL et Apache](../log-analytics/log-analytics-data-sources-linux-applications.md) grâce à Log Analytics
* Les services Azure tels que [Application Insights](https://docs.microsoft.com/azure/application-insights/) et [Azure Security Center](https://docs.microsoft.com/azure/security-center/) stockent leurs données directement dans Log Analytics.  

## <a name="next-steps"></a>Étapes suivantes

- Consultez [Collecter des données d’ordinateurs dans un environnement avec Log Analytics](../log-analytics/log-analytics-concept-hybrid.md) pour connaître les exigences et méthodes disponibles pour déployer l’agent sur les ordinateurs de votre centre de données ou d’un autre environnement cloud.
- Consultez [Collecter des données sur les machines virtuelles Azure](../log-analytics/log-analytics-quick-collect-azurevm.md) pour configurer la collecte de données à partir de machines virtuelles Azure. 
