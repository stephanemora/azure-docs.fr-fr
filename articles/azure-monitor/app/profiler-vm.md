---
title: Profiler des applications web exécutées sur une machine virtuelle Azure avec Application Insights Profiler | Microsoft Docs
description: Profilez des applications web sur une machine virtuelle Azure avec Application Insights Profiler.
services: application-insights
documentationcenter: ''
author: cweining
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: mbullwin
ms.date: 08/06/2018
ms.author: cweining
ms.openlocfilehash: b72966ebc73953e6a89ca1bb2fd4f7ce15f70fee
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/18/2019
ms.locfileid: "58111376"
---
# <a name="profile-web-apps-running-on-an-azure-virtual-machine-or-a-virtual-machine-scale-set-by-using-application-insights-profiler"></a>Profiler des applications web s’exécutant sur une machine virtuelle Azure ou un groupe de machines virtuelles identiques avec Application Insights Profiler

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Vous pouvez également déployer Azure Application Insights Profiler sur ces services :
* [Azure App Service](../../azure-monitor/app/profiler.md?toc=/azure/azure-monitor/toc.json)
* [Azure Cloud Services](profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric](profiler-vm.md?toc=/azure/azure-monitor/toc.json)

## <a name="deploy-profiler-on-a-virtual-machine-or-a-virtual-machine-scale-set"></a>Déployer Profiler sur une machine virtuelle ou un groupe de machines virtuelles identiques
Cet article vous montre comment exécuter Application Insights Profiler sur votre machine virtuelle Azure ou votre groupe de machines virtuelles identiques Azure. Profiler est installé avec l’extension Azure Diagnostics pour machines virtuelles. Configurez l’extension pour exécuter Profiler et générez le SDK Application Insights dans votre application.

1. Ajoutez le SDK Application Insights à votre [application ASP.NET](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net) ou [application .NET](windows-services.md?toc=/azure/azure-monitor/toc.json) ordinaire.  
   Pour voir les profils de vos demandes, vous devez envoyer les données de télémétrie des demandes à Application Insights.

1. Installez l’extension Azure Diagnostics sur votre machine virtuelle. Pour obtenir des exemples complets de modèles Resource Manager, consultez :  
   * [Machine virtuelle](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachine.json)
   * [Groupe de machines virtuelles identiques](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachineScaleSet.json)
    
     La partie clé est ApplicationInsightsProfilerSink dans WadCfg. Pour qu’Azure Diagnostics autorise Profiler à envoyer des données à votre iKey, ajoutez un autre récepteur à cette section.
    
     ```json
     "SinksConfig": {
       "Sink": [
         {
           "name": "ApplicationInsightsSink",
           "ApplicationInsights": "85f73556-b1ba-46de-9534-606e08c6120f"
         },
         {
           "name": "MyApplicationInsightsProfilerSink",
           "ApplicationInsightsProfiler": "85f73556-b1ba-46de-9534-606e08c6120f"
         }
       ]
     },
     ```

1. Déployez la définition du déploiement d’environnement modifiée.  

   L’application des modifications implique généralement un déploiement de modèle complet ou une publication basée sur les services cloud par le biais d’applets de commande PowerShell ou Visual Studio.  

   Les commandes PowerShell suivantes offrent une autre approche pour les machines virtuelles existantes, qui touche uniquement l’extension Azure Diagnostics. Ajouter le ProfilerSink précédemment mentionnée à la configuration qui est retournée par la commande Get-AzVMDiagnosticsExtension, puis transmettre la configuration mise à jour à la commande Set-AzVMDiagnosticsExtension.

    ```powershell
    $ConfigFilePath = [IO.Path]::GetTempFileName()
    # After you export the currently deployed Diagnostics config to a file, edit it to include the ApplicationInsightsProfiler sink.
    (Get-AzVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM").PublicSettings | Out-File -Verbose $ConfigFilePath
    # Set-AzVMDiagnosticsExtension might require the -StorageAccountName argument
    # If your original diagnostics configuration had the storageAccountName property in the protectedSettings section (which is not downloadable), be sure to pass the same original value you had in this cmdlet call.
    Set-AzVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM" -DiagnosticsConfigurationPath $ConfigFilePath
    ```

1. Si l’application souhaitée s’exécute avec [IIS](https://www.microsoft.com/web/downloads/platform.aspx), activez la fonctionnalité Windows `IIS Http Tracing`.

   a. Établissez un accès à distance à l’environnement, puis utilisez la fenêtre [Ajouter des fonctionnalités Windows]( https://docs.microsoft.com/iis/configuration/system.webserver/tracing/). Sinon, exécutez la commande suivante dans PowerShell (comme administrateur) :  

    ```powershell
    Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All
    ```  
   b. Si l’établissement de l’accès à distance est un problème, vous pouvez utiliser [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) pour exécuter la commande suivante :  

    ```powershell
    az vm run-command invoke -g MyResourceGroupName -n MyVirtualMachineName --command-id RunPowerShellScript --scripts "Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All"
    ```

1. Déployez votre application.

## <a name="can-profiler-run-on-on-premises-servers"></a>Profiler peut-il être exécuté sur des serveurs locaux ?
Nous n’envisageons pas de prendre en charge Application Insights Profiler pour les serveurs locaux.

## <a name="next-steps"></a>Étapes suivantes

- Générez le trafic vers votre application (par exemple, lancez un [test de disponibilité](monitor-web-app-availability.md)). Attendez ensuite 10 à 15 minutes le temps que les traces soient envoyées à l’instance Application Insights.
- Consultez [Traces du profileur](profiler-overview.md?toc=/azure/azure-monitor/toc.json) dans le portail Azure.
- Pour obtenir de l'aide sur la résolution des problèmes liés au profileur, consultez [Résolution des problèmes liés au profileur](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json).
