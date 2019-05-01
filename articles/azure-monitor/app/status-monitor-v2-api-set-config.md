---
title: 'Référence des API v2 statut Azure Monitor : Définir la configuration | Microsoft Docs'
description: État du moniteur v2 API référence Set-ApplicationInsightsMonitoringConfig. Surveiller les performances de site Web sans avoir à redéployer le site Web. Fonctionne avec les applications web ASP.NET hébergées localement, dans des machines virtuelles ou sur Azure.
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
ms.openlocfilehash: 2ca738d5d79fc73f892922825d4b731e8ee92b72
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/29/2019
ms.locfileid: "64870491"
---
# <a name="status-monitor-v2-api-set-applicationinsightsmonitoringconfig-v021-alpha"></a>État du moniteur v2 API : Set-ApplicationInsightsMonitoringConfig (v0.2.1-alpha)

Ce document décrit une applet de commande qui est fourni en tant que membre de la [Az.ApplicationMonitor PowerShell module](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

> [!IMPORTANT]
> État du moniteur v2 est actuellement en version préliminaire publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [conditions d’utilisation supplémentaires pour les versions préliminaires de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="description"></a>Description

Définir le fichier de configuration sans avoir à répéter une réinstallation complète. Redémarrez IIS pour que vos modifications prennent effet.

> [!IMPORTANT] 
> Cette applet de commande requiert une PowerShell Session avec des autorisations d’administrateur.


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


### <a name="-verbose"></a>-Verbose
**Paramètre commun.** Utilisez ce commutateur pour générer des journaux détaillés.


## <a name="output"></a>Sortie

Aucune sortie par défaut.

#### <a name="example-verbose-output-from-setting-the-config-file-via--instrumentationkey"></a>Exemple détaillé de la sortie à partir de la définition du fichier de configuration via la clé d’instrumentation-

```
VERBOSE: Operation: InstallWithIkey
VERBOSE: InstrumentationKeyMap parsed:
Filters:
0)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx AppFilter: .* MachineFilter: .*
VERBOSE: set config file
VERBOSE: Config File Path:
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\applicationInsights.ikey.config
```

#### <a name="example-verbose-output-from-setting-the-config-file-via--instrumentationkeymap"></a>Exemple détaillé de la sortie à partir de la définition du fichier de configuration via - InstrumentationKeyMap

```
VERBOSE: Operation: InstallWithIkeyMap
VERBOSE: InstrumentationKeyMap parsed:
Filters:
0)InstrumentationKey:  AppFilter: WebAppExclude MachineFilter: .*
1)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2 AppFilter: WebAppTwo MachineFilter: .*
2)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault AppFilter: .* MachineFilter: .*
VERBOSE: set config file
VERBOSE: Config File Path:
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\applicationInsights.ikey.config
```
