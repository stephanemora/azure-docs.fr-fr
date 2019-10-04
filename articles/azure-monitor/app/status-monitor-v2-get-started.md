---
title: Azure Status Monitor v2 - Bien démarrer | Microsoft Docs
description: Guide de démarrage rapide pour Status Monitor v2. Surveiller les performances de site web sans avoir à redéployer le site web. Fonctionne avec les applications web ASP.NET hébergées localement, dans des machines virtuelles ou sur Azure.
services: application-insights
documentationcenter: .net
author: TimothyMothra
manager: alexklim
ms.assetid: 769a5ea4-a8c6-4c18-b46c-657e864e24de
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: tilee
ms.openlocfilehash: d9c354edac3cbd3faccaa261654e56f858befdf6
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058246"
---
# <a name="get-started-with-status-monitor-v2"></a>Bien démarrer avec Status Monitor v2

Cet article contient les commandes de démarrage rapide susceptibles de fonctionner pour la plupart des environnements.
Les instructions dépendent de PowerShell Gallery pour distribuer les mises à jour.
Ces commandes prennent en charge le paramètre `-Proxy` PowerShell.

Pour obtenir une explication de ces commandes, des instructions de personnalisation et des informations sur le dépannage, consultez les [instructions détaillées](status-monitor-v2-detailed-instructions.md).

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="download-and-install-via-powershell-gallery"></a>Télécharger et installer via PowerShell Gallery

### <a name="install-prerequisites"></a>Installation des composants requis
Exécutez PowerShell en tant qu’administrateur.
```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted
Install-Module -Name PowerShellGet -Force
``` 
Fermez PowerShell.

### <a name="install-status-monitor-v2"></a>Installer Status Monitor v2
Exécutez PowerShell en tant qu’administrateur.
```powershell   
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
Install-Module -Name Az.ApplicationMonitor -AllowPrerelease -AcceptLicense
``` 

### <a name="enable-monitoring"></a>Activer la supervision
```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```
    
        
## <a name="download-and-install-manually-offline-option"></a>Télécharger et installer manuellement (option hors connexion)
### <a name="download-the-module"></a>Télécharger le module
Téléchargez manuellement la dernière version du module à partir de [PowerShell Gallery](https://www.powershellgallery.com/packages/Az.ApplicationMonitor).

### <a name="unzip-and-install-status-monitor-v2"></a>Décompresser et installer Status Monitor v2
```powershell
$pathToNupkg = "C:\Users\t\Desktop\Az.ApplicationMonitor.0.3.0-alpha.nupkg"
$pathToZip = ([io.path]::ChangeExtension($pathToNupkg, "zip"))
$pathToNupkg | rename-item -newname $pathToZip
$pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\Az.ApplicationMonitor"
Expand-Archive -LiteralPath $pathToZip -DestinationPath $pathInstalledModule
```
### <a name="enable-monitoring"></a>Activer la supervision
```powershell
Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```



## <a name="next-steps"></a>Étapes suivantes

 Affichez vos données de télémétrie :

- [Explorez les métriques](../../azure-monitor/app/metrics-explorer.md) pour surveiller les performances et l’utilisation.
- [Effectuez des recherches dans les événements et les journaux](../../azure-monitor/app/diagnostic-search.md) pour diagnostiquer les problèmes.
- [Utilisez la fonctionnalité Analytics](../../azure-monitor/app/analytics.md) pour des requêtes plus élaborées.
- [Créez des tableaux de bord](../../azure-monitor/app/overview-dashboard.md).

 Ajoutez des données de télémétrie :

- [Créez des tests web](monitor-web-app-availability.md) pour vous assurer que votre site reste actif.
- [Ajoutez la télémétrie de client web](../../azure-monitor/app/javascript.md) pour afficher les exceptions à partir du code de la page web et activer le suivi des appels.
- [Ajoutez le kit de développement logiciel (SDK) Application Insights à votre code](../../azure-monitor/app/asp-net.md) afin de pouvoir insérer un suivi et journaliser les appels.

En faire plus avec Status Monitor v2 :

- Examinez les [instructions détaillées](status-monitor-v2-detailed-instructions.md) pour obtenir une explication des commandes répertoriées ici.
- Utilisez notre guide pour [dépanner](status-monitor-v2-troubleshoot.md) Status Monitor v2.
