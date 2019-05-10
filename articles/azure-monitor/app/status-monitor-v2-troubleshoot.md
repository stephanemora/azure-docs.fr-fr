---
title: Problèmes connus et résolution des problèmes Azure Status Monitor v2 | Microsoft Docs
description: Les problèmes connus de Status Monitor v2 et le dépannage des exemples. Surveiller les performances de site Web sans avoir à redéployer le site Web. Fonctionne avec les applications web ASP.NET hébergées localement, dans des machines virtuelles ou sur Azure.
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
ms.openlocfilehash: d640206fd72b4eb89afe5ed1750627823bca9637
ms.sourcegitcommit: 300cd05584101affac1060c2863200f1ebda76b7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/08/2019
ms.locfileid: "65415874"
---
# <a name="troubleshooting-status-monitor-v2"></a>Résolution des problèmes d’état surveiller v2

Lorsque vous activez l’analyse, vous pouvez rencontrer des problèmes qui empêchent la collecte de données. Ce document répertorie tous les problèmes connus et résolution des problèmes d’exemples.
Si vous rencontrez un problème non répertorié ici, vous pouvez nous contacter [ici](https://github.com/Microsoft/ApplicationInsights-Home/issues).


> [!IMPORTANT]
> État du moniteur v2 est actuellement en version préliminaire publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [conditions d’utilisation supplémentaires pour les versions préliminaires de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="known-issues"></a>Problèmes connus

### <a name="conflicting-dlls-in-an-applications-bin-directory"></a>DLL en conflit dans le répertoire bin de l’application

Si une de ces DLL sont présente dans le répertoire bin, la surveillance peut échouer.

- Microsoft.ApplicationInsights.dll
- Microsoft.AspNet.TelemetryCorrelation.dll
- System.Diagnostics.DiagnosticSource.dll

Certaines de ces DLL sont inclus dans les modèles d’application par défaut de Visual Studio, même si votre application ne les utilise.
Comportement symptomatique peut être consultée à l’aide des outils de dépannage :

- PerfView :
    ```
    ThreadID="7,500" 
    ProcessorNumber="0" 
    msg="Found 'System.Diagnostics.DiagnosticSource, Version=4.0.2.1, Culture=neutral, PublicKeyToken=cc7b13ffcd2ddd51' assembly, skipping attaching redfield binaries" 
    ExtVer="2.8.13.5972" 
    SubscriptionId="" 
    AppName="" 
    FormattedMessage="Found 'System.Diagnostics.DiagnosticSource, Version=4.0.2.1, Culture=neutral, PublicKeyToken=cc7b13ffcd2ddd51' assembly, skipping attaching redfield binaries" 
    ```

- iisreset + application charge (télémétrie non). Examiner avec Sysinternals (Handle.exe et ListDLLs.exe)
    ```
    .\handle64.exe -p w3wp | findstr /I "InstrumentationEngine AI. ApplicationInsights"
    E54: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll

    .\Listdlls64.exe w3wp | findstr /I "InstrumentationEngine AI ApplicationInsights"
    0x0000000009be0000  0x127000  C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\MicrosoftInstrumentationEngine_x64.dll
    0x0000000009b90000  0x4f000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.ExtensionsHost_x64.dll
    0x0000000004d20000  0xb2000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.Extensions.Base_x64.dll
    ```

### <a name="conflict-with-iis-shared-configuration"></a>Conflit avec la configuration partagée IIS

Si vous avez un cluster de serveurs web, vous utilisez peut-être un [Configuration partagée](https://docs.microsoft.com/iis/web-hosting/configuring-servers-in-the-windows-web-platform/shared-configuration_211). Nous ne pouvons pas automatiquement injecter notre HttpModule dans cette configuration partagée. Chaque serveur web devez d’abord exécuter la commande de l’activer pour installer notre DLL dans son GAC.

Après avoir exécuté la commande Enable, 
1. Accédez à votre répertoire de Configuration partagée et recherchez votre `applicationHost.config` fichier.
2. Ajoutez la ligne à votre configuration dans la section modules :
    ```
    <modules>
        <!-- Registered global managed http module handler. The 'Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.dll' must be installed in the GAC before this config is applied. -->
        <add name="ManagedHttpModuleHelper" type="Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.ManagedHttpModuleHelper, Microsoft.AppInsights.IIS.ManagedHttpModuleHelper, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" preCondition="managedHandler,runtimeVersionv4.0" />
    </modules>
    ```
    


## <a name="troubleshooting"></a>Résolution de problèmes
    
### <a name="troubleshooting-powershell"></a>Résolution des problèmes de PowerShell

#### <a name="how-to-inspect-what-modules-are-available"></a>Comment inspecter les modules qui sont disponibles ?
Vous pouvez auditer les Modules installés à l’aide de la commande : `Get-Module -ListAvailable`

#### <a name="how-to-import-a-module-into-the-current-session"></a>Comment importer un module dans la session active ?
Si le Module n’a pas été chargé dans une session PowerShell, peut charger manuellement à l’aide de la commande : `Import-Module <path to psd1>`


### <a name="troubleshooting-the-status-monitor-v2-module"></a>Le module de v2 du moniteur d’état de résolution des problèmes

#### <a name="how-to-review-what-commands-are-available-in-the-status-monitor-v2-module"></a>Comment faire pour passer en revue les commandes sont disponibles dans le module de v2 Status Monitor ?
- Exécutez la commande : `Get-Command -Module Az.ApplicationMonitor` pour obtenir les commandes disponibles :

    ```
    CommandType     Name                                               Version    Source
    -----------     ----                                               -------    ------
    Cmdlet          Disable-ApplicationInsightsMonitoring              0.2.1      Az.ApplicationMonitor
    Cmdlet          Disable-InstrumentationEngine                      0.2.1      Az.ApplicationMonitor
    Cmdlet          Enable-ApplicationInsightsMonitoring               0.2.1      Az.ApplicationMonitor
    Cmdlet          Enable-InstrumentationEngine                       0.2.1      Az.ApplicationMonitor
    Cmdlet          Get-ApplicationInsightsMonitoringConfig            0.2.1      Az.ApplicationMonitor
    Cmdlet          Get-ApplicationInsightsMonitoringStatus            0.2.1      Az.ApplicationMonitor
    Cmdlet          Set-ApplicationInsightsMonitoringConfig            0.2.1      Az.ApplicationMonitor
    ```

#### <a name="what-is-the-current-version-of-the-status-monitor-v2-module"></a>Qu’est la version actuelle du module v2 Status Monitor ?
- Exécutez la commande : `Get-ApplicationInsightsMonitoringStatus` pour obtenir une sortie d’informations sur ce module :
    - Version du module PowerShell
    - Version d’application Insights SDK
    - Chemins d’accès de fichier du module PowerShell
    
Consultez notre [référence de l’API](status-monitor-v2-api-get-status.md) pour une description détaillée de l’utilisation de cette applet de commande.



### <a name="troubleshooting-running-processes"></a>Résolution des problèmes de processus en cours d’exécution

Vous pouvez inspecter le processus sur l’ordinateur instrumenté pour voir si toutes les DLL sont chargées.
Si le fonctionnement de l’analyse, au moins 12 DLL doivent être chargés.

- Cmd : `Get-ApplicationInsightsMonitoringStatus -InspectProcess`

Consultez notre [référence de l’API](status-monitor-v2-api-get-status.md) pour une description détaillée de l’utilisation de cette applet de commande.


### <a name="collect-etw-logs-with-perfview"></a>Collecter les journaux ETW avec PerfView

#### <a name="setup"></a>Paramétrage

1. Télécharger PerfView.exe et PerfView64.exe à partir de https://github.com/Microsoft/perfview/releases
2. Lancez PerfView64.exe
3. Développez « Options avancées »
4. Décochez la case :
    - Zip
    - Fusionner
    - Collection de symboles .NET
5. Définissez des fournisseurs supplémentaires : `61f6ca3b-4b5f-5602-fa60-759a2a2d1fbd,323adc25-e39b-5c87-8658-2c1af1a92dc5,925fa42b-9ef6-5fa7-10b8-56449d7a2040,f7d60e07-e910-5aca-bdd2-9de45b46c560,7c739bb9-7861-412e-ba50-bf30d95eae36,61f6ca3b-4b5f-5602-fa60-759a2a2d1fbd,323adc25-e39b-5c87-8658-2c1af1a92dc5,252e28f4-43f9-5771-197a-e8c7e750a984`


#### <a name="collecting-logs"></a>Collecte des journaux

1. Dans une console cmd avec des privilèges d’administrateur, exécutez `iisreset /stop` désactiver IIS et toutes les applications web.
2. Dans PerfView, cliquez sur « Démarrer la collecte »
3. Dans une console cmd avec des privilèges d’administrateur, exécutez `iisreset /start` pour démarrer les services IIS.
4. Essayez d’accéder à votre application.
5. Une fois que votre application est chargée, revenez à PerfView et cliquez sur « Arrêter la collecte »



## <a name="next-steps"></a>Étapes suivantes

- Consultez notre [référence de l’API](status-monitor-v2-overview.md#powershell-api-reference) pour trouver un paramètre que vous avez omis.
- Si vous rencontrez un problème non répertorié ici, vous pouvez nous contacter [ici](https://github.com/Microsoft/ApplicationInsights-Home/issues).
