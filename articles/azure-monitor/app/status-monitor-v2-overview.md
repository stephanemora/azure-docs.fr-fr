---
title: Vue d’ensemble d’Azure Status Monitor v2 | Microsoft Docs
description: Une vue d’ensemble de Status Monitor v2. Surveiller les performances de site web sans avoir à redéployer le site web. Fonctionne avec les applications web ASP.NET hébergées localement, dans des machines virtuelles ou sur Azure.
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
ms.openlocfilehash: 2126408222433e6339723dc2da0d2611bb234fe8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66734160"
---
# <a name="status-monitor-v2"></a>Status Monitor version 2

Status Monitor v2 est un module PowerShell publié dans [PowerShell Gallery](https://www.powershellgallery.com/packages/Az.ApplicationMonitor).
Il remplace [Status Monitor](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now).
Le module fournit une instrumentation sans code d’applications web .NET hébergées par IIS.
Les données de télémétrie sont envoyées au Portail Azure, dans lequel vous pouvez [surveiller](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) votre application.

> [!IMPORTANT]
> Status Monitor v2 est actuellement en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="powershell-gallery"></a>PowerShell Gallery

PowerShell Gallery se trouve ici : https://www.powershellgallery.com/packages/Az.ApplicationMonitor.


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

## <a name="troubleshooting"></a>Résolution de problèmes
- [Dépannage](status-monitor-v2-troubleshoot.md)
- [Problèmes connus](status-monitor-v2-troubleshoot.md#known-issues)


## <a name="faq"></a>Forum Aux Questions

- Les installations de proxy sont-elles prises en charge par Status Monitor v2 ?

  *Oui*. Il existe plusieurs manières de télécharger Status Monitor v2. Si votre ordinateur a accès à Internet, vous pouvez intégrer PowerShell Gallery à l’aide des paramètres `-Proxy`.
Vous pouvez également télécharger le module manuellement et l’installer sur votre ordinateur ou l’utiliser directement.
Chacune de ces options est décrite dans les [instructions détaillées](status-monitor-v2-detailed-instructions.md).
  
- Comment vérifier que l’activation a réussi ?

   Il n’existe aucune cmdlet pour vérifier que l’activation a réussi.
Nous vous recommandons d’utiliser les [métriques en temps réel](https://docs.microsoft.com/azure/azure-monitor/app/live-stream) pour déterminer rapidement si votre application envoie les données de télémétrie.

   Vous pouvez également utiliser [Log Analytics](../log-query/get-started-portal.md) pour répertorier tous les rôles de cloud qui envoient des données de télémétrie :
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
* [Ajoutez le Kit SDK Application Insights à votre code](../../azure-monitor/app/asp-net.md) afin de pouvoir insérer un suivi et journaliser les appels.

