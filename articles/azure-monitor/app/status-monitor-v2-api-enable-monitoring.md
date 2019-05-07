---
title: 'Référence des API v2 statut Azure Monitor : Activer la surveillance | Microsoft Docs'
description: État du moniteur v2 API référence Enable-ApplicationInsightsMonitoring. Surveiller les performances de site Web sans avoir à redéployer le site Web. Fonctionne avec les applications web ASP.NET hébergées localement, dans des machines virtuelles ou sur Azure.
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
ms.openlocfilehash: 17798c78b167821f5f30a11996ac90cf67fb0179
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65144957"
---
# <a name="status-monitor-v2-api-enable-applicationinsightsmonitoring-v021-alpha"></a>État du moniteur v2 API : Enable-ApplicationInsightsMonitoring (v0.2.1-alpha)

Ce document décrit une applet de commande qui est fourni en tant que membre de la [Az.ApplicationMonitor PowerShell module](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

> [!IMPORTANT]
> État du moniteur v2 est actuellement en version préliminaire publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [conditions d’utilisation supplémentaires pour les versions préliminaires de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="description"></a>Description

Activer code sans attacher de surveillance d’applications IIS sur un ordinateur cible.
Cette applet de commande sera modifier le fichier applicationHost.config IIS et définir des clés de Registre.
Cette applet de commande crée également un applicationinsights.ikey.config, qui définit la clé d’instrumentation est utilisé par l’application.
IIS chargera le RedfieldModule au démarrage, qui injecte le SDK Application Insights dans des applications comme les applications démarrent.
Redémarrez IIS pour que vos modifications prennent effet.

Après avoir activé la surveillance, nous vous recommandons d’utiliser [métriques temps réel](live-stream.md) à observer rapidement si votre application nous est envoyant des données de télémétrie.


> [!NOTE] 
> Pour commencer, vous devez disposer d’une clé d’instrumentation. Pour plus d’informations, consultez [Créer nouvelle ressource](create-new-resource.md#copy-the-instrumentation-key).


> [!IMPORTANT] 
> Cette applet de commande requiert une PowerShell Session avec des autorisations d’administrateur et avec une stratégie d’exécution avec élévation de privilèges. Lecture [ici](status-monitor-v2-detailed-instructions.md#run-powershell-as-administrator-with-an-elevated-execution-policy) pour plus d’informations.

> [!NOTE] 
> Cette applet de commande vous obligera à Lisez et acceptez notre licence et déclaration de confidentialité.


## <a name="examples"></a>Exemples

### <a name="example-with-single-instrumentation-key"></a>Exemple avec la clé d’instrumentation unique
Dans cet exemple, toutes les applications sur l’ordinateur actuel recevront une clé d’instrumentation unique.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

### <a name="example-with-instrumentation-key-map"></a>Exemple de mappage de clés d’instrumentation
Dans cet exemple, 
- `MachineFilter` correspond à la machine actuelle en utilisant le `'.*'` générique.
- `AppFilter='WebAppExclude'` fournit un `null` InstrumentationKey. Cette application ne sera pas instrumentée.
- `AppFilter='WebAppOne'` affecte cette application spécifique une clé d’instrumentation unique.
- `AppFilter='WebAppTwo'` seront également affecter cette application spécifique une clé d’instrumentation unique.
- Enfin, `AppFilter` utilise également le `'.*'` génériques pour faire correspondre toutes les autres applications web non mis en correspondance par les règles antérieures et assigne une clé d’instrumentation par défaut.
- Espaces ajoutés pour une meilleure lisibilité uniquement.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKeyMap 
    @(@{MachineFilter='.*';AppFilter='WebAppExclude'},
      @{MachineFilter='.*';AppFilter='WebAppOne';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx1'},
      @{MachineFilter='.*';AppFilter='WebAppTwo';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2'},
      @{MachineFilter='.*';AppFilter='.*';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault'})

```


## <a name="parameters"></a>parameters 

### <a name="-instrumentationkey"></a>-InstrumentationKey
**Obligatoire.** Utilisez ce paramètre pour fournir une seule iKey pour une utilisation par toutes les applications sur l’ordinateur cible.

### <a name="-instrumentationkeymap"></a>-InstrumentationKeyMap
**Obligatoire.** Utilisez ce paramètre pour fournir plusieurs ikeys et un mappage de quelles applications d’utiliser l’ikey. Vous pouvez créer un script d’installation unique pour plusieurs ordinateurs en définissant le MachineFilter. 

> [!IMPORTANT] 
> Applications correspondra par rapport aux règles dans l’ordre dans lequel elles sont proposées. Par conséquent vous devez tout d’abord spécifier les règles les plus spécifiques et les règles plus génériques de la dernière.

#### <a name="schema"></a>Schéma
`@(@{MachineFilter='.*';AppFilter='.*';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'})`

- **MachineFilter** est un regex requis c# du nom d’ordinateur ou machine virtuelle.
    - «. * » correspond à tous
    - « ComputerName » correspond uniquement aux ordinateurs portant ce nom exact.
- **AppFilter** est un regex requis c# du nom d’ordinateur ou machine virtuelle.
    - «. * » correspond à tous
    - « ApplicationName » correspondra uniquement les applications IIS portant ce nom exact.
- **Clé d’instrumentation** est requis pour activer l’analyse des applications qui correspondent aux deux filtres ci-dessus.
    - Laissez cette valeur null si vous souhaitez définir des règles pour exclure la surveillance


### <a name="-enableinstrumentationengine"></a>-EnableInstrumentationEngine
**Facultatif.** Utilisez ce commutateur pour activer le moteur d’Instrumentation collecter des événements et des messages de ce qui se passe à pendant l’exécution d’un processus managé. Y compris mais pas limité à des Codes de résultat de dépendance, les verbes HTTP et texte de commande SQL. Le moteur d’Instrumentation ajoutera une charge supplémentaire et est désactivée par défaut.

### <a name="-acceptlicense"></a>-AcceptLicense
**Facultatif.** Utilisez ce commutateur pour accepter la licence et déclaration de confidentialité dans les installations sans périphérique de contrôle.

### <a name="-verbose"></a>-Verbose
**Paramètre commun.** Utilisez ce commutateur pour générer des journaux détaillés.

### <a name="-whatif"></a>-WhatIf 
**Paramètre commun.** Utilisez ce commutateur pour tester et valider vos paramètres d’entrée sans réellement activer l’analyse.

## <a name="output"></a>Sortie


#### <a name="example-output-from-a-successful-enablement"></a>Exemple de sortie à partir d’une activation réussie

```
Initiating Disable Process
Applying transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config'
'C:\Windows\System32\inetsrv\config\applicationHost.config' backed up to 'C:\Windows\System32\inetsrv\config\applicationHost.config.backup-2019-03-26_08-59-52z'
in :1,237
No element in the source document matches '/configuration/location[@path='']/system.webServer/modules/add[@name='ManagedHttpModuleHelper']'
Not executing RemoveAll (transform line 1, 546)
Transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config' was successfully applied. Operation: 'disable'
GAC Module will not be removed, since this operation might cause IIS instabilities
Configuring IIS Environment for codeless attach...
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]
Configuring IIS Environment for instrumentation engine...
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]
Configuring registry for instrumentation engine...
Successfully disabled Application Insights Status Monitor
Installing GAC module 'C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\0.2.0\content\Runtime\Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.dll'
Applying transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config'
Found GAC module Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.ManagedHttpModuleHelper, Microsoft.AppInsights.IIS.ManagedHttpModuleHelper, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35
'C:\Windows\System32\inetsrv\config\applicationHost.config' backed up to 'C:\Windows\System32\inetsrv\config\applicationHost.config.backup-2019-03-26_08-59-52z_1'
Transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config' was successfully applied. Operation: 'enable'
Configuring IIS Environment for codeless attach...
Configuring IIS Environment for instrumentation engine...
Configuring registry for instrumentation engine...
Updating app pool permissions...
Successfully enabled Application Insights Status Monitor
```

## <a name="next-steps"></a>Étapes suivantes

  Affichez vos données de télémétrie :
 - [Explorez les mesures](../../azure-monitor/app/metrics-explorer.md) pour surveiller les performances et l’utilisation
- [Recherchez les événements et journaux](../../azure-monitor/app/diagnostic-search.md) pour diagnostiquer les problèmes
- [Utilisez la fonctionnalité Analytics](../../azure-monitor/app/analytics.md) pour des requêtes plus élaborées
- [Créez des tableaux de bord](../../azure-monitor/app/app-insights-dashboards.md)
 
 Ajoutez des données de télémétrie :
 - [Créez des tests web](monitor-web-app-availability.md) s’assurer que votre site reste actif.
- [Ajoutez la télémétrie de client web](../../azure-monitor/app/javascript.md) pour afficher les exceptions à partir du code de page web et vous permettre d’insérer des appels de trace.
- [Ajoutez le kit SDK Application Insights à votre code](../../azure-monitor/app/asp-net.md) afin que vous pouvez insérer un suivi et enregistrer les appels
 
 Faire plus avec Status Monitor v2 :
 - Utilisez notre guide [dépannage](status-monitor-v2-troubleshoot.md) Status Monitor v2.
 - [Obtenir la configuration](status-monitor-v2-api-get-config.md) pour confirmer que vos paramètres ont été enregistrés correctement.
 - [Obtenir l’état](status-monitor-v2-api-get-status.md) pour inspecter la surveillance.
