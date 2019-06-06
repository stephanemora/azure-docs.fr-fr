---
title: Problèmes connus et résolution des problèmes Azure Status Monitor v2 | Microsoft Docs
description: Les problèmes connus de Status Monitor v2 et le dépannage des exemples. Surveiller les performances de site Web sans avoir à redéployer le site Web. Fonctionne avec les applications web ASP.NET hébergées en local, dans les machines virtuelles, ou sur Azure.
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
ms.openlocfilehash: f2115ee14b58030f695c9410870615f03d353cd2
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/06/2019
ms.locfileid: "66734964"
---
# <a name="troubleshooting-status-monitor-v2"></a>Résolution des problèmes d’état surveiller v2

Lorsque vous activez l’analyse, vous pouvez rencontrer des problèmes qui empêchent la collecte de données.
Cet article répertorie tous les problèmes connus et fournit des exemples de résolution des problèmes.
Si vous rencontrez un problème qui n’est pas répertorié ici, vous pouvez nous contacter sur [GitHub](https://github.com/Microsoft/ApplicationInsights-Home/issues).


> [!IMPORTANT]
> État du moniteur v2 est actuellement en version préliminaire publique.
> Cette version préliminaire est fournie sans contrat de niveau de service, et nous n’est pas recommandé pour les charges de travail de production. Certaines fonctionnalités ne peuvent pas être pris en charge, et certains peuvent avoir des fonctionnalités limitées.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="known-issues"></a>Problèmes connus

### <a name="conflicting-dlls-in-an-apps-bin-directory"></a>DLL en conflit dans le répertoire bin de l’application

Si une de ces DLL sont présente dans le répertoire bin, surveillance risque d’échouer :

- Microsoft.ApplicationInsights.dll
- Microsoft.AspNet.TelemetryCorrelation.dll
- System.Diagnostics.DiagnosticSource.dll

Certaines de ces DLL sont inclus dans les modèles d’applications Visual Studio par défaut, même si votre application ne les utilise.
Vous pouvez utiliser les outils de dépannage pour constater un comportement symptomatique :

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

- IISReset et application de chargement (sans les données de télémétrie). Examiner avec Sysinternals (Handle.exe et ListDLLs.exe) :
    ```
    .\handle64.exe -p w3wp | findstr /I "InstrumentationEngine AI. ApplicationInsights"
    E54: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll

    .\Listdlls64.exe w3wp | findstr /I "InstrumentationEngine AI ApplicationInsights"
    0x0000000009be0000  0x127000  C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\MicrosoftInstrumentationEngine_x64.dll
    0x0000000009b90000  0x4f000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.ExtensionsHost_x64.dll
    0x0000000004d20000  0xb2000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.Extensions.Base_x64.dll
    ```

### <a name="conflict-with-iis-shared-configuration"></a>Conflit avec la configuration partagée IIS

Si vous avez un cluster de serveurs web, vous utilisez peut-être un [configuration partagée](https://docs.microsoft.com/iis/web-hosting/configuring-servers-in-the-windows-web-platform/shared-configuration_211).
Le HttpModule ne peut pas être injecté dans cette configuration partagée.
Exécutez la commande de l’activer sur chaque serveur web pour installer la DLL dans global assembly cache de chaque serveur.

Après avoir exécuté la commande de l’activer, procédez comme suit :
1. Accédez au répertoire de configuration partagée et recherchez le fichier applicationHost.config.
2. Ajoutez la ligne à la section modules de votre configuration :
    ```
    <modules>
        <!-- Registered global managed http module handler. The 'Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.dll' must be installed in the GAC before this config is applied. -->
        <add name="ManagedHttpModuleHelper" type="Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.ManagedHttpModuleHelper, Microsoft.AppInsights.IIS.ManagedHttpModuleHelper, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" preCondition="managedHandler,runtimeVersionv4.0" />
    </modules>
    ```
## <a name="troubleshooting"></a>Résolution de problèmes
    
### <a name="troubleshooting-powershell"></a>Résolution des problèmes de PowerShell

#### <a name="determine-which-modules-are-available"></a>Déterminer quels modules sont disponibles
Vous pouvez utiliser la `Get-Module -ListAvailable` commande pour déterminer quels modules sont installés.

#### <a name="import-a-module-into-the-current-session"></a>Importer un module dans la session active
Si un module n’a pas été chargé dans une session PowerShell, vous pouvez le charger manuellement à l’aide de la `Import-Module <path to psd1>` commande.


### <a name="troubleshooting-the-status-monitor-v2-module"></a>Le module de v2 du moniteur d’état de résolution des problèmes

#### <a name="list-the-commands-available-in-the-status-monitor-v2-module"></a>Répertorier les commandes disponibles dans le module de v2 Status Monitor
Exécutez la commande `Get-Command -Module Az.ApplicationMonitor` pour obtenir les commandes disponibles :

```
Command type     Name                                               Version    Source
-----------     ----                                               -------    ------
Cmdlet          Disable-ApplicationInsightsMonitoring              0.2.1      Az.ApplicationMonitor
Cmdlet          Disable-InstrumentationEngine                      0.2.1      Az.ApplicationMonitor
Cmdlet          Enable-ApplicationInsightsMonitoring               0.2.1      Az.ApplicationMonitor
Cmdlet          Enable-InstrumentationEngine                       0.2.1      Az.ApplicationMonitor
Cmdlet          Get-ApplicationInsightsMonitoringConfig            0.2.1      Az.ApplicationMonitor
Cmdlet          Get-ApplicationInsightsMonitoringStatus            0.2.1      Az.ApplicationMonitor
Cmdlet          Set-ApplicationInsightsMonitoringConfig            0.2.1      Az.ApplicationMonitor
```

#### <a name="determine-the-current-version-of-the-status-monitor-v2-module"></a>Déterminer la version actuelle du module v2 Status Monitor
Exécutez le `Get-ApplicationInsightsMonitoringStatus` commande pour afficher les informations suivantes sur le module :
   - Version du module PowerShell
   - Version d’application Insights SDK
   - Chemins d’accès de fichier du module PowerShell
    
Examinez le [référence de l’API](status-monitor-v2-api-get-status.md) pour une description détaillée de l’utilisation de cette applet de commande.


### <a name="troubleshooting-running-processes"></a>Résolution des problèmes de processus en cours d’exécution

Vous pouvez inspecter les processus sur l’ordinateur instrumenté pour déterminer si toutes les DLL sont chargées.
Si le fonctionnement de l’analyse, au moins 12 DLL doivent être chargés.

Utilisez le `Get-ApplicationInsightsMonitoringStatus -InspectProcess` commande pour vérifier les DLL.

Examinez le [référence de l’API](status-monitor-v2-api-get-status.md) pour une description détaillée de l’utilisation de cette applet de commande.


### <a name="collect-etw-logs-by-using-perfview"></a>Collecter les journaux ETW à l’aide de PerfView

#### <a name="setup"></a>Paramétrage

1. Télécharger PerfView.exe et PerfView64.exe de [GitHub](https://github.com/Microsoft/perfview/releases).
2. Démarrer PerfView64.exe.
3. Développez **les Options avancées**.
4. Désactivez ces cases à cocher :
    - **Zip**
    - **fusion**
    - **Collection de symboles .NET**
5. Définissez-les **des fournisseurs supplémentaires**: `61f6ca3b-4b5f-5602-fa60-759a2a2d1fbd,323adc25-e39b-5c87-8658-2c1af1a92dc5,925fa42b-9ef6-5fa7-10b8-56449d7a2040,f7d60e07-e910-5aca-bdd2-9de45b46c560,7c739bb9-7861-412e-ba50-bf30d95eae36,61f6ca3b-4b5f-5602-fa60-759a2a2d1fbd,323adc25-e39b-5c87-8658-2c1af1a92dc5,252e28f4-43f9-5771-197a-e8c7e750a984`


#### <a name="collecting-logs"></a>Collecte des journaux

1. Dans une console de commande avec des privilèges d’administrateur, exécutez le `iisreset /stop` commande pour désactiver IIS et toutes les applications web.
2. Dans PerfView, sélectionnez **démarrer la collecte**.
3. Dans une console de commande avec des privilèges d’administrateur, exécutez le `iisreset /start` commande pour démarrer les services IIS.
4. Essayez d’accéder à votre application.
5. Une fois votre application est chargée, revenez au PerfView et sélectionnez **arrêter la Collection**.



## <a name="next-steps"></a>Étapes suivantes

- Examinez le [référence de l’API](status-monitor-v2-overview.md#powershell-api-reference) pour en savoir plus sur les paramètres que vous avez omis.
- Si vous rencontrez un problème qui n’est pas répertorié ici, vous pouvez nous contacter sur [GitHub](https://github.com/Microsoft/ApplicationInsights-Home/issues).
