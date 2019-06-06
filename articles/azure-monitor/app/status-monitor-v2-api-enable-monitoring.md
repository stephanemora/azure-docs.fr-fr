---
title: 'Référence des API Azure Status Monitor v2 : Activer la surveillance | Microsoft Docs'
description: Référence d’API v2 de moniteur d’état. Enable-ApplicationInsightsMonitoring. Surveiller les performances de site Web sans avoir à redéployer le site Web. Fonctionne avec les applications web ASP.NET hébergées en local, dans les machines virtuelles, ou sur Azure.
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
ms.openlocfilehash: e87bfad11eee5b86d35e6b4f2846b094c467e0ef
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/06/2019
ms.locfileid: "66734182"
---
# <a name="status-monitor-v2-api-enable-applicationinsightsmonitoring-v021-alpha"></a>État du moniteur v2 API : Enable-ApplicationInsightsMonitoring (v0.2.1-alpha)

Cet article décrit une applet de commande qui est un membre de la [Az.ApplicationMonitor PowerShell module](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

> [!IMPORTANT]
> État du moniteur v2 est actuellement en version préliminaire publique.
> Cette version préliminaire est fournie sans contrat de niveau de service, et nous n’est pas recommandé pour les charges de travail de production. Certaines fonctionnalités ne peuvent pas être pris en charge, et certains peuvent avoir des fonctionnalités limitées.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="description"></a>Description

Permet l’attachement de surveillance d’applications IIS sur un ordinateur cible.

Cette applet de commande sera modifier le fichier applicationHost.config IIS et définir des clés de Registre.
Il crée également un fichier applicationinsights.ikey.config, qui définit la clé d’instrumentation utilisée par chaque application.
IIS chargera le RedfieldModule au démarrage, qui injecte le SDK Application Insights dans des applications comme les applications de démarrage.
Redémarrez IIS pour que vos modifications prennent effet.

Une fois que vous activez l’analyse, nous vous recommandons d’utiliser [métriques temps réel](live-stream.md) pour vérifier rapidement si votre application nous est envoyant des données de télémétrie.


> [!NOTE] 
> - Pour commencer, vous avez besoin d’une clé d’instrumentation. Pour plus d’informations, consultez [créer une ressource](create-new-resource.md#copy-the-instrumentation-key).
> - Cette applet de commande nécessite que vous passez en revue et acceptez notre licence et déclaration de confidentialité.

> [!IMPORTANT] 
> Cette applet de commande requiert une session PowerShell avec des autorisations d’administrateur et une stratégie d’exécution avec élévation de privilèges. Pour plus d’informations, consultez [PowerShell de s’exécuter en tant qu’administrateur avec une stratégie d’exécution avec élévation de privilèges](status-monitor-v2-detailed-instructions.md#run-powershell-as-admin-with-an-elevated-execution-policy).

## <a name="examples"></a>Exemples

### <a name="example-with-a-single-instrumentation-key"></a>Exemple avec une clé d’instrumentation unique
Dans cet exemple, toutes les applications sur l’ordinateur actuel sont affectées à une clé d’instrumentation unique.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

### <a name="example-with-an-instrumentation-key-map"></a>Exemple avec une carte de clé d’instrumentation
Dans cet exemple :
- `MachineFilter` correspond à l’ordinateur actuel en utilisant le `'.*'` générique.
- `AppFilter='WebAppExclude'` fournit un `null` clé d’instrumentation. L’application spécifiée ne sera pas instrumentée.
- `AppFilter='WebAppOne'` affecte à l’application spécifiée une clé d’instrumentation unique.
- `AppFilter='WebAppTwo'` affecte à l’application spécifiée une clé d’instrumentation unique.
- Enfin, `AppFilter` utilise également le `'.*'` génériques pour faire correspondre toutes les applications web qui ne sont pas conformes à ces règles antérieures et attribuer une clé d’instrumentation par défaut.
- Les espaces sont ajoutés pour une meilleure lisibilité.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKeyMap 
    @(@{MachineFilter='.*';AppFilter='WebAppExclude'},
      @{MachineFilter='.*';AppFilter='WebAppOne';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx1'},
      @{MachineFilter='.*';AppFilter='WebAppTwo';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2'},
      @{MachineFilter='.*';AppFilter='.*';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault'})

```


## <a name="parameters"></a>parameters

### <a name="-instrumentationkey"></a>-InstrumentationKey
**Obligatoire.** Utilisez ce paramètre pour fournir une clé d’instrumentation unique pour une utilisation par toutes les applications sur l’ordinateur cible.

### <a name="-instrumentationkeymap"></a>-InstrumentationKeyMap
**Obligatoire.** Utilisez ce paramètre pour fournir plusieurs clés d’instrumentation et un mappage de clés d’instrumentation utilisée par chaque application.
Vous pouvez créer un script d’installation unique pour plusieurs ordinateurs en définissant `MachineFilter`.

> [!IMPORTANT]
> Applications correspondra par rapport aux règles dans l’ordre dans lequel les règles sont fournies. Par conséquent, vous devez tout d’abord spécifier les règles les plus spécifiques et les règles plus génériques de la dernière.

#### <a name="schema"></a>Schéma
`@(@{MachineFilter='.*';AppFilter='.*';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'})`

- **MachineFilter** est obligatoire C# expression régulière de l’ordinateur ou le nom de la machine virtuelle.
    - «. * » correspond à tous
    - « ComputerName » correspondra uniquement les ordinateurs avec le nom exact spécifié.
- **AppFilter** est obligatoire C# expression régulière de l’ordinateur ou le nom de la machine virtuelle.
    - «. * » correspond à tous
    - « ApplicationName » correspondra uniquement les applications IIS avec le nom exact spécifié.
- **Clé d’instrumentation** est requis pour activer l’analyse des applications qui correspondent aux deux filtres précédents.
    - Conserver la valeur null si vous souhaitez définir des règles pour exclure la surveillance.


### <a name="-enableinstrumentationengine"></a>-EnableInstrumentationEngine
**Facultatif.** Utilisez ce commutateur pour activer le moteur d’instrumentation collecter des événements et des messages sur ce qui se passe pendant l’exécution d’un processus managé. Ces événements et les messages incluent les codes de résultat de dépendance, les verbes HTTP et texte de commande SQL.

Le moteur d’instrumentation ajoute une surcharge et est désactivée par défaut.

### <a name="-acceptlicense"></a>-AcceptLicense
**Facultatif.** Utilisez ce commutateur pour accepter la licence et déclaration de confidentialité dans les installations sans périphérique de contrôle.

### <a name="-verbose"></a>-Verbose
**Paramètre commun.** Utilisez ce commutateur pour afficher les journaux détaillés.

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
 - [Exploration des mesures](../../azure-monitor/app/metrics-explorer.md) pour surveiller les performances et l’utilisation.
- [Recherchez les événements et journaux](../../azure-monitor/app/diagnostic-search.md) pour diagnostiquer les problèmes.
- [Utiliser l’Analytique](../../azure-monitor/app/analytics.md) pour plus d’informations de requêtes avancées.
- [Créer des tableaux de bord](../../azure-monitor/app/overview-dashboard.md).
 
 Ajoutez des données de télémétrie :
 - [Créez des tests web](monitor-web-app-availability.md) s’assurer que votre site reste actif.
- [Ajoutez la télémétrie de client web](../../azure-monitor/app/javascript.md) pour afficher les exceptions à partir du code de page web et pour activer les appels de trace.
- [Ajouter le SDK Application Insights à votre code](../../azure-monitor/app/asp-net.md) afin de pouvoir insérer un suivi et enregistrer les appels.
 
 Faire plus avec Status Monitor v2 :
 - Utilisez notre guide [dépanner](status-monitor-v2-troubleshoot.md) Status Monitor v2.
 - [Obtenir la configuration](status-monitor-v2-api-get-config.md) pour confirmer que vos paramètres ont été enregistrés correctement.
 - [Obtenir l’état](status-monitor-v2-api-get-status.md) pour inspecter la surveillance.
