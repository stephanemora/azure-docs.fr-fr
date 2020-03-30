---
title: Vue d’ensemble d’Azure Application Insights Agent | Microsoft Docs
description: Vue d’ensemble d’Application Insights Agent. Surveillez les performances de site web sans avoir à redéployer le site web. Fonctionne avec les applications web ASP.NET hébergées en local, dans des machines virtuelles ou sur Azure.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 09/16/2019
ms.openlocfilehash: 4a240bc62816a46bc37108777a8b081b74047738
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79234617"
---
# <a name="deploy-azure-monitor-application-insights-agent-for-on-premises-servers"></a>Déployer Azure Monitor Application Insights Agent pour les serveurs locaux

> [!IMPORTANT]
> Ces informations d’aide sont recommandées pour les déploiements locaux et les déploiements sur les environnements cloud non Azure d’Application Insights Agent. Voici l’approche recommandée pour les [déploiements de machines virtuelles et de groupes de machines virtuelles identiques Azure](https://docs.microsoft.com/azure/azure-monitor/app/azure-vm-vmss-apps).

Application Insights Agent (anciennement appelé Status Monitor V2) est un module PowerShell publié sur [PowerShell Gallery](https://www.powershellgallery.com/packages/Az.ApplicationMonitor).
Il remplace [Status Monitor](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now).
Les données de télémétrie sont envoyées au Portail Azure, dans lequel vous pouvez [surveiller](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) votre application.

> [!NOTE]
> Le module prend uniquement en charge l’instrumentation sans code des applications web .NET hébergées à l’aide d’IIS. Utilisez un kit SDK pour instrumenter les applications ASP.NET Core, Java et Node.js.

## <a name="powershell-gallery"></a>PowerShell Gallery

Application Insights Agent se trouve ici : https://www.powershellgallery.com/packages/Az.ApplicationMonitor.

![PowerShell Gallery](https://img.shields.io/powershellgallery/v/Az.ApplicationMonitor.svg?color=Blue&label=Current%20Version&logo=PowerShell&style=for-the-badge)


## <a name="instructions"></a>Instructions
- Consultez les [instructions de prise en main](status-monitor-v2-get-started.md) pour vous familiariser avec les exemples de code concis.
- Consultez les [instructions détaillées](status-monitor-v2-detailed-instructions.md) pour une présentation approfondie de la prise en main.

## <a name="powershell-api-reference"></a>Référence API PowerShell
- [Disable-ApplicationInsightsMonitoring](status-monitor-v2-api-disable-monitoring.md)
- [Disable-InstrumentationEngine](status-monitor-v2-api-disable-instrumentation-engine.md)
- [Enable-ApplicationInsightsMonitoring](status-monitor-v2-api-enable-monitoring.md)
- [Enable-InstrumentationEngine](status-monitor-v2-api-enable-instrumentation-engine.md)
- [Get-ApplicationInsightsMonitoringConfig](status-monitor-v2-api-get-config.md)
- [Get-ApplicationInsightsMonitoringStatus](status-monitor-v2-api-get-status.md)
- [Set-ApplicationInsightsMonitoringConfig](status-monitor-v2-api-set-config.md)
- [Start-ApplicationInsightsMonitoringTrace](status-monitor-v2-api-start-trace.md)

## <a name="troubleshooting"></a>Dépannage
- [Dépannage](status-monitor-v2-troubleshoot.md)
- [Problèmes connus](status-monitor-v2-troubleshoot.md#known-issues)


## <a name="faq"></a>Questions fréquentes (FAQ)

- Application Insights Agent prend-il en charge les installations basées sur un serveur proxy ?

  *Oui*. Il existe plusieurs façons de télécharger Application Insights Agent. Si votre ordinateur a accès à Internet, vous pouvez intégrer PowerShell Gallery à l’aide des paramètres `-Proxy`.
Vous pouvez également télécharger le module manuellement et l’installer sur votre ordinateur ou l’utiliser directement.
Chacune de ces options est décrite dans les [instructions détaillées](status-monitor-v2-detailed-instructions.md).

- Status Monitor v2 prend-il en charge les applications ASP.NET Core ?

  *Non*. Pour obtenir des instructions sur l’activation de la supervision des applications ASP.NET Core, consultez [Application Insights pour applications ASP.NET Core](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core). Il n’est pas nécessaire d’installer StatusMonitor pour une application ASP.NET Core. C’est vrai même si l’application ASP.NET Core est hébergée dans IIS.

- Comment vérifier que l’activation a réussi ?

  - Le cmdlet [Get-ApplicationInsightsMonitoringStatus](status-monitor-v2-api-get-status.md) peut être utilisé pour vérifier que l’activation a réussi.
  - Nous vous recommandons d’utiliser les [métriques en temps réel](https://docs.microsoft.com/azure/azure-monitor/app/live-stream) pour déterminer rapidement si votre application envoie les données de télémétrie.

  - Vous pouvez également utiliser [Log Analytics](../log-query/get-started-portal.md) pour répertorier tous les rôles de cloud qui envoient des données de télémétrie :
      ```Kusto
      union * | summarize count() by cloud_RoleName, cloud_RoleInstance
      ```

## <a name="next-steps"></a>Étapes suivantes

Affichez vos données de télémétrie :

* [Explorez les métriques](../../azure-monitor/app/metrics-explorer.md) pour surveiller les performances et l’utilisation.
* [Effectuez des recherches dans les événements et les journaux](../../azure-monitor/app/diagnostic-search.md) pour diagnostiquer les problèmes.
* [Utilisez la fonctionnalité Analytics](../../azure-monitor/app/analytics.md) pour des requêtes plus élaborées.
* [Créez des tableaux de bord](../../azure-monitor/app/overview-dashboard.md).

Ajoutez des données de télémétrie :

* [Créez des tests web](monitor-web-app-availability.md) pour vous assurer que votre site reste actif.
* [Ajoutez la télémétrie de client web](../../azure-monitor/app/javascript.md) pour afficher les exceptions à partir du code de la page web et activer le suivi des appels.
* [Ajoutez le kit de développement logiciel (SDK) Application Insights à votre code](../../azure-monitor/app/asp-net.md) afin de pouvoir insérer un suivi et journaliser les appels.

