---
title: Vue d’ensemble de Azure Status Monitor v2 | Microsoft Docs
description: Vue d’ensemble du moniteur d’état v2. Surveiller les performances de site Web sans avoir à redéployer le site Web. Fonctionne avec les applications web ASP.NET hébergées en local, dans les machines virtuelles, ou sur Azure.
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
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/06/2019
ms.locfileid: "66734160"
---
# <a name="status-monitor-v2"></a>Status Monitor version 2

État du moniteur v2 est un PowerShell module publié dans le [PowerShell Gallery](https://www.powershellgallery.com/packages/Az.ApplicationMonitor).
Il remplace [Status Monitor](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now).
Le module fournit l’instrumentation d’applications .NET web hébergé par IIS.
La télémétrie au portail Azure, dans laquelle vous pouvez [moniteur](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) votre application.

> [!IMPORTANT]
> État du moniteur v2 est actuellement en version préliminaire publique.
> Cette version préliminaire est fournie sans contrat de niveau de service, et nous n’est pas recommandé pour les charges de travail de production. Certaines fonctionnalités ne peuvent pas être pris en charge, et certains peuvent avoir des fonctionnalités limitées.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="powershell-gallery"></a>PowerShell Gallery

PowerShell Gallery se trouve ici : https://www.powershellgallery.com/packages/Az.ApplicationMonitor.


## <a name="instructions"></a>Instructions
- Consultez le [mise en route des instructions](status-monitor-v2-get-started.md) pour vous familiariser avec les exemples de code concis.
- Consultez le [des instructions détaillées](status-monitor-v2-detailed-instructions.md) pour une présentation approfondie sur la prise en main.

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

  *Oui*. Il existe plusieurs façons de télécharger Status Monitor v2. Si votre ordinateur a accès à internet, vous pouvez les intégrer dans PowerShell Gallery à l’aide de `-Proxy` paramètres.
Vous pouvez également télécharger manuellement le module et installez-la sur votre ordinateur ou l’utiliser directement.
Chacune de ces options est décrite dans le [des instructions détaillées](status-monitor-v2-detailed-instructions.md).
  
- Comment vérifier que l’activation a réussi ?

   Il n’existe aucune applet de commande pour vérifier l’activation a réussi.
Nous vous recommandons d’utiliser [métriques temps réel](https://docs.microsoft.com/azure/azure-monitor/app/live-stream) pour déterminer rapidement si votre application envoie la télémétrie.

   Vous pouvez également utiliser [Analytique de journal](../log-query/get-started-portal.md) pour répertorier tous les rôles de cloud actuellement envoyer la télémétrie :
   ```Kusto
   union * | summarize count() by cloud_RoleName, cloud_RoleInstance
   ```

## <a name="next-steps"></a>Étapes suivantes

Affichez vos données de télémétrie :

* [Exploration des mesures](../../azure-monitor/app/metrics-explorer.md) pour surveiller les performances et l’utilisation.
* [Recherchez les événements et journaux](../../azure-monitor/app/diagnostic-search.md) pour diagnostiquer les problèmes.
* [Utiliser l’Analytique](../../azure-monitor/app/analytics.md) pour plus d’informations de requêtes avancées.
* [Créer des tableaux de bord](../../azure-monitor/app/overview-dashboard.md).

Ajoutez des données de télémétrie :

* [Créez des tests web](monitor-web-app-availability.md) s’assurer que votre site reste actif.
* [Ajoutez la télémétrie de client web](../../azure-monitor/app/javascript.md) pour afficher les exceptions à partir du code de page web et pour activer les appels de trace.
* [Ajouter le SDK Application Insights à votre code](../../azure-monitor/app/asp-net.md) afin de pouvoir insérer un suivi et enregistrer les appels.

