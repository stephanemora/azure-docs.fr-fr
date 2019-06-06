---
title: 'Référence des API Azure Status Monitor v2 : Définir la configuration | Microsoft Docs'
description: Référence d’API v2 de moniteur d’état. Set-ApplicationInsightsMonitoringConfig. Surveiller les performances de site Web sans avoir à redéployer le site Web. Fonctionne avec les applications web ASP.NET hébergées en local, dans les machines virtuelles, ou sur Azure.
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
ms.openlocfilehash: 562ce8a4267370be9b049e3b56f213f82deb89c0
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/06/2019
ms.locfileid: "66734983"
---
# <a name="status-monitor-v2-api-set-applicationinsightsmonitoringconfig-v021-alpha"></a>État du moniteur v2 API : Set-ApplicationInsightsMonitoringConfig (v0.2.1-alpha)

Ce document décrit une applet de commande qui est un membre de la [Az.ApplicationMonitor PowerShell module](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

> [!IMPORTANT]
> État du moniteur v2 est actuellement en version préliminaire publique.
> Cette version préliminaire est fournie sans contrat de niveau de service, et nous n’est pas recommandé pour les charges de travail de production. Certaines fonctionnalités ne peuvent pas être pris en charge, et certains peuvent avoir des fonctionnalités limitées.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="description"></a>Description

Définit le fichier de configuration sans effectuer une réinstallation complète.
Redémarrez IIS pour que vos modifications prennent effet.

> [!IMPORTANT] 
> Cette applet de commande requiert une session PowerShell avec des autorisations d’administrateur.


## <a name="examples"></a>Exemples

### <a name="example-with-a-single-instrumentation-key"></a>Exemple avec une clé d’instrumentation unique
Dans cet exemple, toutes les applications sur l’ordinateur actuel recevront une clé d’instrumentation unique.

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
    - « ComputerName » correspond uniquement aux ordinateurs portant le nom spécifié.
- **AppFilter** est obligatoire C# expression régulière de l’ordinateur ou le nom de la machine virtuelle.
    - «. * » correspond à tous
    - « ApplicationName » correspondra uniquement les applications IIS avec le nom spécifié.
- **Clé d’instrumentation** est requis pour activer l’analyse des applications qui correspondent aux deux filtres précédents.
    - Conserver la valeur null si vous souhaitez définir des règles pour exclure la surveillance.


### <a name="-verbose"></a>-Verbose
**Paramètre commun.** Utilisez ce commutateur pour afficher les journaux détaillés.


## <a name="output"></a>Sortie

Par défaut, aucune sortie.

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

## <a name="next-steps"></a>Étapes suivantes

  Affichez vos données de télémétrie :
 - [Exploration des mesures](../../azure-monitor/app/metrics-explorer.md) pour surveiller les performances et l’utilisation.
- [Recherchez les événements et journaux](../../azure-monitor/app/diagnostic-search.md) pour diagnostiquer les problèmes.
- [Utiliser l’Analytique](../../azure-monitor/app/analytics.md) pour plus d’informations de requêtes avancées.
- [Créer des tableaux de bord](../../azure-monitor/app/overview-dashboard.md).
 
 Ajoutez des données de télémétrie :
 - [Créez des tests web](monitor-web-app-availability.md) s’assurer que votre site reste actif.
- [Ajoutez la télémétrie de client web](../../azure-monitor/app/javascript.md) pour afficher les exceptions à partir du code de page web et pour activer les appels de trace.
- [Ajouter le SDK Application Insights à votre code](../../azure-monitor/app/asp-net.md) afin de pouvoir insérer un suivi et enregistrer les appels
 
 Faire plus avec Status Monitor v2 :
 - Utilisez notre guide [dépanner](status-monitor-v2-troubleshoot.md) Status Monitor v2.
 - [Obtenir la configuration](status-monitor-v2-api-get-config.md) pour confirmer que vos paramètres ont été enregistrés correctement.
 - [Obtenir l’état](status-monitor-v2-api-get-status.md) pour inspecter la surveillance.
