---
title: Vue d’ensemble de Azure Status Monitor v2 | Microsoft Docs
description: Vue d’ensemble du moniteur d’état v2. Surveiller les performances de site Web sans avoir à redéployer le site Web. Fonctionne avec les applications web ASP.NET hébergées localement, dans des machines virtuelles ou sur Azure.
services: application-insights
documentationcenter: .net
author: MS-TimothyMothra
manager: alexklim
ms.assetid: 769a5ea4-a8c6-4c18-b46c-657e864e24de
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: tilee
ms.openlocfilehash: 976545f588895421e7c2be7e18b844d7994bc1ff
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/16/2019
ms.locfileid: "65779991"
---
# <a name="status-monitor-v2"></a>Status Monitor version 2

État du moniteur v2 est un Module PowerShell publié sur le [PowerShellGallery](https://www.powershellgallery.com/packages/Az.ApplicationMonitor) et remplace [Status Monitor](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now). Ce module fournit une instrumentation sans code des applications web .NET Web hébergé par IIS.
Données de télémétrie seront envoyées au portail Azure dans laquelle vous pouvez [moniteur](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) votre application.

> [!IMPORTANT]
> État du moniteur v2 est actuellement en version préliminaire publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [conditions d’utilisation supplémentaires pour les versions préliminaires de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="powershell-gallery"></a>PowerShell Gallery

https://www.powershellgallery.com/packages/Az.ApplicationMonitor


## <a name="instructions"></a>Instructions
- Consultez notre [mise en route des instructions](status-monitor-v2-get-started.md) prise en main maintenant des exemples de code concis.
- Consultez notre [des instructions détaillées](status-monitor-v2-detailed-instructions.md) pour une présentation approfondie sur la prise en main.

## <a name="powershell-api-reference"></a>Référence de l’API de PowerShell
- [Disable-ApplicationInsightsMonitoring](status-monitor-v2-api-disable-monitoring.md)
- [Disable-InstrumentationEngine](status-monitor-v2-api-disable-instrumentation-engine.md)
- [Enable-ApplicationInsightsMonitoring](status-monitor-v2-api-enable-monitoring.md)
- [Enable-InstrumentationEngine](status-monitor-v2-api-enable-instrumentation-engine.md)
- [Get-ApplicationInsightsMonitoringConfig](status-monitor-v2-api-get-config.md)
- [Get-ApplicationInsightsMonitoringStatus](status-monitor-v2-api-get-status.md)
- [Set-ApplicationInsightsMonitoringConfig](status-monitor-v2-api-set-config.md)

## <a name="troubleshooting"></a>Résolution de problèmes
- [Dépannage](status-monitor-v2-troubleshoot.md)
- [Problèmes connus](status-monitor-v2-troubleshoot.md#known-issues)


## <a name="faq"></a>Forum Aux Questions

- Status Monitor v2 prend en charge les installations de proxy ?

  **Oui**. Vous avez plusieurs options pour télécharger Status Monitor v2. Si votre ordinateur a accès à internet, vous pouvez les intégrer à l’aide de PowerShell Gallery `-Proxy` paramètres. Vous pouvez également télécharger manuellement ce module et installez-la sur votre ordinateur ou utiliser le module directement. Chacune de ces options est décrite dans notre [obtenir des Instructions détaillées](status-monitor-v2-detailed-instructions.md).
  
- Comment vérifier l’activation a réussi ?

   Nous n’avons une applet de commande pour vérifier que cette activation a réussi. Nous vous recommandons d’utiliser [métriques temps réel](https://docs.microsoft.com/azure/azure-monitor/app/live-stream) à observer rapidement si votre application nous est envoyant des données de télémétrie.

   Vous pouvez également utiliser [Analytique](../log-query/get-started-portal.md) pour répertorier tous les rôles de cloud actuellement envoyer la télémétrie.
   ```Kusto
   union * | summarize count() by cloud_RoleName, cloud_RoleInstance
   ```

## <a name="next-steps"></a>Étapes suivantes

Affichez vos données de télémétrie :

* [Explorez les mesures](../../azure-monitor/app/metrics-explorer.md) pour surveiller les performances et l’utilisation
* [Recherchez les événements et journaux](../../azure-monitor/app/diagnostic-search.md) pour diagnostiquer les problèmes
* [Utilisez la fonctionnalité Analytics](../../azure-monitor/app/analytics.md) pour des requêtes plus élaborées
* [Créez des tableaux de bord](../../azure-monitor/app/app-insights-dashboards.md)

Ajoutez des données de télémétrie :

* [Créez des tests web](monitor-web-app-availability.md) s’assurer que votre site reste actif.
* [Ajoutez la télémétrie de client web](../../azure-monitor/app/javascript.md) pour afficher les exceptions à partir du code de page web et vous permettre d’insérer des appels de trace.
* [Ajoutez le kit SDK Application Insights à votre code](../../azure-monitor/app/asp-net.md) afin que vous pouvez insérer un suivi et enregistrer les appels

