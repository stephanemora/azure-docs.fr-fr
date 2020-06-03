---
title: Surveiller des applications sur Azure Kubernetes service (AKS) avec Application Insights – Azure Monitor | Microsoft Docs
description: Azure Monitor s’intègre sans difficulté avec votre application s’exécutant sur Kubernetes, et vous permet d’épingler les problèmes liés à vos applications en un rien de temps.
ms.topic: conceptual
author: MS-jgol
ms.author: jgol
ms.date: 05/13/2020
ms.openlocfilehash: 76f9f922697ef6be6c959ea7f9bafd0872dba6ff
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83773769"
---
# <a name="zero-instrumentation-application-monitoring-for-kubernetes---azure-monitor-application-insights"></a>Surveillance d’application sans instrumentation pour Kubernetes – Azure Monitor Application Insights

> [!IMPORTANT]
>  Actuellement, vous pouvez activer la surveillance de vos applications Java s’exécutant sur Kubernetes sans instrumentation de votre code, en utilisant l’[agent autonome Java](https://docs.microsoft.com/azure/azure-monitor/app/java-in-process-agent). Pendant que la solution permettant d’activer en toute transparence la surveillance des applications est en construction pour d’autres langages, utilisez les kits de développement logiciel (SDK) pour surveiller vos applications s’exécutant sur AKS : [ASP.NET Core](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core), [ASP.NET](https://docs.microsoft.com/azure/azure-monitor/app/asp-net), [Node.js](https://docs.microsoft.com/azure/azure-monitor/app/nodejs), [JavaScript](https://docs.microsoft.com/azure/azure-monitor/app/javascript) et [Python](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python).

## <a name="application-monitoring-without-instrumenting-the-code"></a>Surveillance d’applications sans instrumentation du code
Actuellement, seul Java vous permet d’activer la surveillance d’applications sans instrumentation du code. Pour surveiller des applications dans d’autres langages, utilisez les kits de développement logiciel (SDK). 

## <a name="java"></a>Java
Une fois activé, l’agent Java collecte automatiquement une multitude de demandes, dépendances, journaux et métriques à partir des bibliothèques et infrastructures les plus utilisées.

Suivez [les instructions détaillées](https://docs.microsoft.com/azure/azure-monitor/app/java-in-process-agent) pour surveiller vos applications Java qui s’exécutent dans des applications Kubernetes ainsi que dans d’autres environnements. 

## <a name="other-languages"></a>Autres langages

Pour les applications dans d’autres langages, nous vous recommandons actuellement d’utiliser les kits de développement logiciel (SDK) :
* [ASP.NET Core](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core)
* [ASP.NET](https://docs.microsoft.com/azure/azure-monitor/app/asp-net)
* [Node.JS](https://docs.microsoft.com/azure/azure-monitor/app/nodejs) 
* [JavaScript](https://docs.microsoft.com/azure/azure-monitor/app/javascript)
* [Python](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python)

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) et [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview)
* Obtenir une vue d’ensemble du [Suivi distribué](https://docs.microsoft.com/azure/azure-monitor/app/distributed-tracing) et découvrir ce que la [cartographie d’application](https://docs.microsoft.com/azure/azure-monitor/app/app-map?tabs=net) peut faire pour vous