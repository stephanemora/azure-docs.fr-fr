---
title: Azure Status Monitor v2 mise en route | Microsoft Docs
description: Guide de démarrage rapide pour Status Monitor v2. Surveiller les performances de site Web sans avoir à redéployer le site Web. Fonctionne avec les applications web ASP.NET hébergées localement, dans des machines virtuelles ou sur Azure.
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
ms.openlocfilehash: 3dcd50c3aa516f2af40c1e28a36a8039773e069c
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2019
ms.locfileid: "66255054"
---
# <a name="getting-started-with-status-monitor-v2"></a>Mise en route avec Status Monitor v2

Ce document contient les commandes de démarrage rapide doit fonctionner pour la plupart des environnements. Ces instructions dépendent de la galerie PowerShell pour distribuer des mises à jour. Ces commandes prennent en charge la commande PowerShell `-Proxy` paramètre.

Consultez notre [des instructions détaillées](status-monitor-v2-detailed-instructions.md) page pour obtenir une explication de ces commandes, des instructions sur la personnalisation et comment résoudre les problèmes.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer. 

> [!IMPORTANT]
> État du moniteur v2 est actuellement en version préliminaire publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [conditions d’utilisation supplémentaires pour les versions préliminaires de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="download--install-via-powershell-gallery"></a>Télécharger et installer par le biais de PowerShell Gallery

### <a name="install-prerequisites"></a>Installation des composants requis
Exécutez PowerShell en tant qu’administrateur
```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted
Install-Module -Name PowerShellGet -Force
``` 
Quittez PowerShell

### <a name="install-status-monitor-v2"></a>État de l’installation surveiller v2
Exécutez PowerShell en tant qu’administrateur
```powershell   
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
Install-Module -Name Az.ApplicationMonitor -AllowPrerelease -AcceptLicense
``` 

### <a name="enable-monitoring"></a>Activer la supervision
```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```
    
        
## <a name="download--install-manually-offline-option"></a>Télécharger et installer manuellement (option hors connexion)
### <a name="manual-download"></a>Téléchargement manuel
Télécharger manuellement la dernière version du Module à partir de : https://www.powershellgallery.com/packages/Az.ApplicationMonitor

### <a name="unzip-and-install-status-monitor-v2"></a>Décompressez et installez Status Monitor v2
```powershell
$pathToNupkg = "C:\Users\t\Desktop\Az.ApplicationMonitor.0.2.1-alpha.nupkg"
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

- [Explorez les mesures](../../azure-monitor/app/metrics-explorer.md) pour surveiller les performances et l’utilisation
- [Recherchez les événements et journaux](../../azure-monitor/app/diagnostic-search.md) pour diagnostiquer les problèmes
- [Utilisez la fonctionnalité Analytics](../../azure-monitor/app/analytics.md) pour des requêtes plus élaborées
- [Créez des tableaux de bord](../../azure-monitor/app/overview-dashboard.md)

 Ajoutez des données de télémétrie :

- [Créez des tests web](monitor-web-app-availability.md) s’assurer que votre site reste actif.
- [Ajoutez la télémétrie de client web](../../azure-monitor/app/javascript.md) pour afficher les exceptions à partir du code de page web et vous permettre d’insérer des appels de trace.
- [Ajoutez le kit SDK Application Insights à votre code](../../azure-monitor/app/asp-net.md) afin que vous pouvez insérer un suivi et enregistrer les appels

Faire plus avec Status Monitor v2 :

- Examinez le [des instructions détaillées](status-monitor-v2-detailed-instructions.md) pour obtenir une explication des commandes de ce guide.
- Utilisez notre guide [dépannage](status-monitor-v2-troubleshoot.md) Status Monitor v2.
