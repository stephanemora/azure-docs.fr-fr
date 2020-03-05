---
title: Informations de référence sur l’API de l’agent Azure Application Insights
description: Référence API d’Application Insights Agent. Start-Trace. Collectez les journaux ETW de Status Monitor et du kit de développement logiciel (SDK) Application Insights.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: b9680101f1a22dd6d9c1617c8afc13a10ad1c594
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2020
ms.locfileid: "77671220"
---
# <a name="application-insights-agent-api-start-applicationinsightsmonitoringtrace"></a>API d’Application Insights Agent : Start-ApplicationInsightsMonitoringTrace

Cet article décrit une cmdlet appartenant au [module PowerShell Az.ApplicationMonitor](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

## <a name="description"></a>Description

Collecte des [événements ETW](https://docs.microsoft.com/windows/desktop/etw/event-tracing-portal) à partir du runtime joint sans code. Cette cmdlet est une alternative à l’exécution de [PerfView](https://github.com/microsoft/perfview).

Les événements collectés seront imprimés dans la console en temps réel et enregistrés dans un fichier ETL. Vous pouvez ouvrir le fichier ETL de sortie avec [PerfView](https://github.com/microsoft/perfview) pour un examen approfondi.

Cette cmdlet s’exécute jusqu’à la fin du délai d’expiration (5 minutes par défaut) ou vous pouvez l’arrêter manuellement (`Ctrl + C`).

> [!IMPORTANT] 
> Cette cmdlet requiert une session PowerShell avec des autorisations d’administrateur.

## <a name="examples"></a>Exemples

### <a name="how-to-collect-events"></a>Comment collecter des événements

Normalement, nous vous demandons de collecter des événements pour examiner pourquoi votre application n’est pas instrumentée actuellement.

Le runtime joint sans code émettra des événements ETW au démarrage d’IIS et au lancement de votre application.

Pour collecter ces événements, procédez comme suit :
1. Dans une console de commande avec des privilèges d’administrateur, exécutez la commande `iisreset /stop` pour désactiver IIS et toutes les applications web.
2. Exécuter cette cmdlet
3. Dans une console de commande avec des privilèges d’administrateur, exécutez la commande `iisreset /start` pour démarrer IIS.
4. Essayez d’accéder à votre application.
5. Une fois votre application chargée, vous pouvez l’arrêter manuellement (`Ctrl + C`) ou attendre la fin du délai d’attente.

### <a name="what-events-to-collect"></a>Événements à collecter

Vous disposez de trois options lors de la collecte des événements :
1. Utiliser le commutateur `-CollectSdkEvents` pour collecter les événements émis par le kit de développement logiciel (SDK) Application Insights.
2. Utiliser le commutateur `-CollectRedfieldEvents` pour collecter les événements émis par Status Monitor et le runtime Redfield. Ces journaux sont utiles pour le diagnostic d’IIS et le démarrage de l’application.
3. Utiliser les deux commutateurs pour collecter les deux types d’événements.
4. Par défaut, si aucun commutateur n’est spécifié, les deux types d’événements seront collectés.


## <a name="parameters"></a>Paramètres

### <a name="-maxdurationinminutes"></a>-MaxDurationInMinutes
**Facultatif.** Utilisez ce paramètre pour définir la durée pendant laquelle ce script doit collecter les événements. La valeur par défaut est de 5 minutes.

### <a name="-logdirectory"></a>-LogDirectory
**Facultatif.** Utilisez ce commutateur pour définir le répertoire de sortie du fichier ETL. Par défaut, ce fichier sera être créé dans le répertoire des modules PowerShell. Le chemin d’accès complet sera affiché lors de l’exécution du script.


### <a name="-collectsdkevents"></a>-CollectSdkEvents
**Facultatif.** Utilisez ce commutateur pour collecter les événements du kit de développement logiciel (SDK) Application Insights.

### <a name="-collectredfieldevents"></a>-CollectRedfieldEvents
**Facultatif.** Utiliser ce commutateur pour collecter les événements émis par Status Monitor et le runtime Redfield.

### <a name="-verbose"></a>-Verbose
**Paramètre commun.** Utilisez ce commutateur pour générer des journaux détaillés.



## <a name="output"></a>Output


### <a name="example-of-application-startup-logs"></a>Exemple de journaux de démarrage d’application
```
PS C:\Windows\system32> Start-ApplicationInsightsMonitoringTrace -ColectRedfieldEvents
Starting...
Log File: C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\logs\20190627_144217_ApplicationInsights_ETW_Trace.etl
Tracing enabled, waiting for events.
Tracing will timeout in 5 minutes. Press CTRL+C to cancel.

2:42:31 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Resolved variables to: MicrosoftAppInsights_ManagedHttpModulePath='C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll', MicrosoftAppInsights_ManagedHttpModuleType='Microsoft.ApplicationInsights.RedfieldIISModule.RedfieldIISModule'
2:42:31 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Resolved variables to: MicrosoftDiagnosticServices_ManagedHttpModulePath2='', MicrosoftDiagnosticServices_ManagedHttpModuleType2=''
2:42:31 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Environment variable 'MicrosoftDiagnosticServices_ManagedHttpModulePath2' or 'MicrosoftDiagnosticServices_ManagedHttpModuleType2' is null, skipping managed dll loading
2:42:31 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace MulticastHttpModule.constructor, success, 70 ms
2:42:31 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace Current assembly 'Microsoft.ApplicationInsights.RedfieldIISModule, Version=2.8.18.27202, Culture=neutral, PublicKeyToken=f23a46de0be5d6f3' location 'C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll'
2:42:31 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace Matched filter '.*'~'STATUSMONITORTE', '.*'~'DemoWithSql'
2:42:31 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace Lightup assembly calculated path: 'C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.Redfield.Lightup.dll'
2:42:31 PM EVENT: Microsoft-ApplicationInsights-FrameworkLightup Trace Loaded applicationInsights.config from assembly's resource Microsoft.ApplicationInsights.Redfield.Lightup, Version=2.8.18.27202, Culture=neutral, PublicKeyToken=f23a46de0be5d6f3/Microsoft.ApplicationInsights.Redfield.Lightup.ApplicationInsights-recommended.config
2:42:34 PM EVENT: Microsoft-ApplicationInsights-FrameworkLightup Trace Successfully attached ApplicationInsights SDK
2:42:34 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace RedfieldIISModule.LoadLightupAssemblyAndGetLightupHttpModuleClass, success, 2687 ms
2:42:34 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace RedfieldIISModule.CreateAndInitializeApplicationInsightsHttpModules(lightupHttpModuleClass), success
2:42:34 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace ManagedHttpModuleHelper, multicastHttpModule.Init() success, 3288 ms
2:42:35 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Resolved variables to: MicrosoftAppInsights_ManagedHttpModulePath='C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll', MicrosoftAppInsights_ManagedHttpModuleType='Microsoft.ApplicationInsights.RedfieldIISModule.RedfieldIISModule'
2:42:35 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Resolved variables to: MicrosoftDiagnosticServices_ManagedHttpModulePath2='', MicrosoftDiagnosticServices_ManagedHttpModuleType2=''
2:42:35 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Environment variable 'MicrosoftDiagnosticServices_ManagedHttpModulePath2' or 'MicrosoftDiagnosticServices_ManagedHttpModuleType2' is null, skipping managed dll loading
2:42:35 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace MulticastHttpModule.constructor, success, 0 ms
2:42:35 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace RedfieldIISModule.CreateAndInitializeApplicationInsightsHttpModules(lightupHttpModuleClass), success
2:42:35 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace ManagedHttpModuleHelper, multicastHttpModule.Init() success, 0 ms
Timeout Reached. Stopping...
```


## <a name="next-steps"></a>Étapes suivantes

Résolution de problèmes supplémentaires :

- Consultez les étapes de dépannage supplémentaires ici : https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-troubleshoot
- Consultez la [documentation de référence de l’API](status-monitor-v2-overview.md#powershell-api-reference) pour en savoir plus sur les paramètres que vous avez omis.
- Si vous avez besoin d’aide supplémentaire, vous pouvez nous contacter sur [GitHub](https://github.com/Microsoft/ApplicationInsights-Home/issues).



 En faire plus avec Application Insights Agent :
 - Utilisez notre guide pour [résoudre les problèmes](status-monitor-v2-troubleshoot.md) d’Application Insights Agent.
 - [Obtenez la configuration](status-monitor-v2-api-get-config.md) pour confirmer que vos paramètres ont été enregistrés correctement.
 - [Obtenez l’état](status-monitor-v2-api-get-status.md) pour inspecter la surveillance.
