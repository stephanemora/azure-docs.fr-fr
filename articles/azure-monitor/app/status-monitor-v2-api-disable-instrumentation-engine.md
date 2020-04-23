---
title: Agent Azure Application Insights - Disable-InstrumentationEngine
description: Référence API d’Application Insights Agent. Disable-InstrumentationEngine. Surveillez les performances de site web sans avoir à redéployer le site web. Fonctionne avec les applications web ASP.NET hébergées en local, dans des machines virtuelles ou sur Azure.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: 6c52d429b527c437db5bb8aaf7e3cc1f1c52af57
ms.sourcegitcommit: 25490467e43cbc3139a0df60125687e2b1c73c09
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/09/2020
ms.locfileid: "80998385"
---
# <a name="application-insights-agent-api-disable-instrumentationengine"></a>API d’Application Insights Agent : Disable-InstrumentationEngine

Cet article décrit une cmdlet appartenant au [module PowerShell Az.ApplicationMonitor](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

## <a name="description"></a>Description
Désactive le moteur d’instrumentation en supprimant certaines clés de Registre.
Redémarrez IIS pour que les modifications soient prises en compte.

> [!IMPORTANT] 
> Cette cmdlet requiert une session PowerShell avec des autorisations d’administrateur.

## <a name="examples"></a>Exemples

```powershell
PS C:\> Disable-InstrumentationEngine
```

## <a name="parameters"></a>Paramètres 

### <a name="-verbose"></a>-Verbose
**Paramètre commun.** Utilisez ce commutateur pour générer des journaux détaillés.

## <a name="output"></a>Output


#### <a name="example-output-from-successfully-disabling-the-instrumentation-engine"></a>Exemple de sortie à partir d’une désactivation réussie du moteur d’instrumentation

```
Configuring IIS Environment for instrumentation engine...
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]'
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]'
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]'
Configuring registry for instrumentation engine...
```


## <a name="next-steps"></a>Étapes suivantes

 En faire plus avec Application Insights Agent :
 - Utilisez notre guide pour [résoudre les problèmes](status-monitor-v2-troubleshoot.md) d’Application Insights Agent.
