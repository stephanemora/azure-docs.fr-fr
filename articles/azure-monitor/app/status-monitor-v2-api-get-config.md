---
title: 'Référence des API v2 statut Azure Monitor : Obtenir la configuration | Microsoft Docs'
description: État du moniteur v2 API référence Get-ApplicationInsightsMonitoringConfig. Surveiller les performances de site Web sans avoir à redéployer le site Web. Fonctionne avec les applications web ASP.NET hébergées localement, dans des machines virtuelles ou sur Azure.
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
ms.openlocfilehash: 0e6b47c9b629aed28fa217cb6299edb57423fc6f
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/29/2019
ms.locfileid: "64870476"
---
# <a name="status-monitor-v2-api-get-applicationinsightsmonitoringconfig-v021-alpha"></a>État du moniteur v2 API : Get-ApplicationInsightsMonitoringConfig (v0.2.1-alpha)

Ce document décrit une applet de commande qui est fourni en tant que membre de la [Az.ApplicationMonitor PowerShell module](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

> [!IMPORTANT]
> État du moniteur v2 est actuellement en version préliminaire publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [conditions d’utilisation supplémentaires pour les versions préliminaires de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="description"></a>Description

Obtenir le fichier de configuration et imprimer les valeurs dans la console.

> [!IMPORTANT] 
> Cette applet de commande requiert une PowerShell Session avec des autorisations d’administrateur.

## <a name="examples"></a>Exemples

```powershell
PS C:\> Get-ApplicationInsightsMonitoringConfig
```

## <a name="parameters"></a>parameters 

(Aucun paramètre requis)

## <a name="output"></a>Sortie


#### <a name="example-output-from-reading-the-config-file"></a>Exemple de sortie à partir de la lecture du fichier de configuration

```
RedfieldConfiguration:
Filters:
0)InstrumentationKey:  AppFilter: WebAppExclude MachineFilter: .*
1)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2 AppFilter: WebAppTwo MachineFilter: .*
2)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault AppFilter: .* MachineFilter: .*
```
