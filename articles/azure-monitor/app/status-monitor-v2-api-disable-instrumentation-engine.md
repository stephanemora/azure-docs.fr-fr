---
title: 'Référence des API Azure Status Monitor v2 : Désactiver le moteur d’instrumentation | Microsoft Docs'
description: Référence d’API v2 de moniteur d’état. Disable-InstrumentationEngine. Surveiller les performances de site Web sans avoir à redéployer le site Web. Fonctionne avec les applications web ASP.NET hébergées en local, dans les machines virtuelles, ou sur Azure.
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
ms.openlocfilehash: b5ff218ce7bc7593d38f496b22d2a03402a00cdc
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66514430"
---
# <a name="status-monitor-v2-api-disable-instrumentationengine-v021-alpha"></a>État du moniteur v2 API : Disable-InstrumentationEngine (v0.2.1-alpha)

Cet article décrit une applet de commande qui est un membre de la [Az.ApplicationMonitor PowerShell module](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

> [!IMPORTANT]
> État du moniteur v2 est actuellement en version préliminaire publique.
> Cette version préliminaire est fournie sans contrat de niveau de service, et nous n’est pas recommandé pour les charges de travail de production. Certaines fonctionnalités ne peuvent pas être pris en charge, et certains peuvent avoir des fonctionnalités limitées.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="description"></a>Description
Désactive le moteur d’instrumentation en supprimant certaines clés de Registre.
Redémarrez IIS pour que les modifications prennent effet.

> [!IMPORTANT] 
> Cette applet de commande requiert une session PowerShell avec des autorisations d’administrateur.

## <a name="examples"></a>Exemples

```powershell
PS C:\> Disable-InstrumentationEngine
```

## <a name="parameters"></a>parameters 

### <a name="-verbose"></a>-Verbose
**Paramètre commun.** Utilisez ce commutateur pour générer des journaux détaillés.

## <a name="output"></a>Sortie


#### <a name="example-output-from-successfully-disabling-the-instrumentation-engine"></a>Exemple de sortie de désactiver correctement le moteur d’instrumentation

```
Configuring IIS Environment for instrumentation engine...
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]'
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]'
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]'
Configuring registry for instrumentation engine...
```


## <a name="next-steps"></a>Étapes suivantes

 Faire plus avec Status Monitor v2 :
 - Utilisez notre guide [dépanner](status-monitor-v2-troubleshoot.md) Status Monitor v2.
