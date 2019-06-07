---
title: 'Référence des API Azure Status Monitor v2 : Obtenir la configuration | Microsoft Docs'
description: Référence d’API v2 de moniteur d’état. Get-ApplicationInsightsMonitoringConfig. Surveiller les performances de site Web sans avoir à redéployer le site Web. Fonctionne avec les applications web ASP.NET hébergées en local, dans les machines virtuelles, ou sur Azure.
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
ms.openlocfilehash: 1f7061c9823ddc8ff7f8f42976041f1c9ff68fc0
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66514400"
---
# <a name="status-monitor-v2-api-get-applicationinsightsmonitoringconfig-v021-alpha"></a>État du moniteur v2 API : Get-ApplicationInsightsMonitoringConfig (v0.2.1-alpha)

Cet article décrit une applet de commande qui est un membre de la [Az.ApplicationMonitor PowerShell module](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

> [!IMPORTANT]
> État du moniteur v2 est actuellement en version préliminaire publique.
> Cette version préliminaire est fournie sans contrat de niveau de service, et nous n’est pas recommandé pour les charges de travail de production. Certaines fonctionnalités ne peuvent pas être pris en charge, et certains peuvent avoir des fonctionnalités limitées.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="description"></a>Description

Obtient le fichier de configuration et imprime les valeurs dans la console.

> [!IMPORTANT] 
> Cette applet de commande requiert une session PowerShell avec des autorisations d’administrateur.

## <a name="examples"></a>Exemples

```powershell
PS C:\> Get-ApplicationInsightsMonitoringConfig
```

## <a name="parameters"></a>parameters

Aucun paramètre requis.

## <a name="output"></a>Sortie


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
 - [Exploration des mesures](../../azure-monitor/app/metrics-explorer.md) pour surveiller les performances et l’utilisation.
- [Recherchez les événements et journaux](../../azure-monitor/app/diagnostic-search.md) pour diagnostiquer les problèmes.
- Utilisez [analytique](../../azure-monitor/app/analytics.md) pour plus d’informations de requêtes avancées.
- [Créer des tableaux de bord](../../azure-monitor/app/overview-dashboard.md).
 
 Ajoutez des données de télémétrie :
 - [Créez des tests web](monitor-web-app-availability.md) s’assurer que votre site reste actif.
- [Ajoutez la télémétrie de client web](../../azure-monitor/app/javascript.md) pour afficher les exceptions à partir du code de page web et pour activer les appels de trace.
- [Ajouter le SDK Application Insights à votre code](../../azure-monitor/app/asp-net.md) afin de pouvoir insérer un suivi et enregistrer les appels.
 
 Faire plus avec Status Monitor v2 :
 - Utilisez notre guide [dépanner](status-monitor-v2-troubleshoot.md) Status Monitor v2.
 - Apporter des modifications à la configuration à l’aide de la [Set config](status-monitor-v2-api-set-config.md) applet de commande.
