---
title: Informations de référence sur l’API de l’agent Azure Application Insights
description: Référence API d’Application Insights Agent. Get-ApplicationInsightsMonitoringConfig. Surveillez les performances de site web sans avoir à redéployer le site web. Fonctionne avec les applications web ASP.NET hébergées en local, dans des machines virtuelles ou sur Azure.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: 0cb5991b381acc9a0ec36113d0e15a7731b5074d
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537489"
---
# <a name="application-insights-agent-api-get-applicationinsightsmonitoringconfig"></a>API d’Application Insights Agent : Get-ApplicationInsightsMonitoringConfig

Cet article décrit une cmdlet appartenant au [module PowerShell Az.ApplicationMonitor](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

## <a name="description"></a>Description

Obtient le fichier de configuration et imprime les valeurs dans la console.

> [!IMPORTANT] 
> Cette cmdlet requiert une session PowerShell avec des autorisations d’administrateur.

## <a name="examples"></a>Exemples

```powershell
PS C:\> Get-ApplicationInsightsMonitoringConfig
```

## <a name="parameters"></a>Paramètres

Aucun paramètre requis.

## <a name="output"></a>Output


#### <a name="example-output-from-reading-the-config-file"></a>Exemple de sortie à partir de la lecture du fichier de configuration

```
RedfieldConfiguration:
Filters:
0)InstrumentationKey:  AppFilter: WebAppExclude MachineFilter: .*
1)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2 AppFilter: WebAppTwo MachineFilter: .*
2)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault AppFilter: .* MachineFilter: .*
```

## <a name="next-steps"></a>Étapes suivantes

  Affichez vos données de télémétrie :
 - [Explorez les métriques](../../azure-monitor/platform/metrics-charts.md) pour surveiller les performances et l’utilisation.
- [Effectuez des recherches dans les événements et les journaux](../../azure-monitor/app/diagnostic-search.md) pour diagnostiquer les problèmes.
- Utilisez la fonctionnalité [Analytics](../../azure-monitor/app/analytics.md) pour des requêtes plus élaborées.
- [Créez des tableaux de bord](../../azure-monitor/app/overview-dashboard.md).
 
 Ajoutez des données de télémétrie :
 - [Créez des tests web](monitor-web-app-availability.md) pour vous assurer que votre site reste actif.
- [Ajoutez la télémétrie de client web](../../azure-monitor/app/javascript.md) pour afficher les exceptions à partir du code de la page web et activer le suivi des appels.
- [Ajoutez le kit de développement logiciel (SDK) Application Insights à votre code](../../azure-monitor/app/asp-net.md) afin de pouvoir insérer un suivi et journaliser les appels.
 
 En faire plus avec Application Insights Agent :
 - Utilisez notre guide pour [résoudre les problèmes](status-monitor-v2-troubleshoot.md) d’Application Insights Agent.
 - Apportez des modifications à la configuration à l’aide de la cmdlet [Set config](status-monitor-v2-api-set-config.md).
