---
title: Profiler des applications web exécutées sur une machine virtuelle Azure avec Application Insights Profiler | Microsoft Docs
description: Profilez des applications web sur une machine virtuelle Azure avec Application Insights Profiler.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: cawa
ms.date: 08/06/2018
ms.author: mbullwin
ms.openlocfilehash: 3f720cdf369e7377f16bb2ea9cba7e898097cc29
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/17/2019
ms.locfileid: "54359780"
---
# <a name="profile-web-apps-running-on-an-azure-virtual-machine-or-a-virtual-machine-scale-set-by-using-application-insights-profiler"></a>Profiler des applications web s’exécutant sur une machine virtuelle Azure ou un groupe de machines virtuelles identiques avec Application Insights Profiler

Vous pouvez également déployer Azure Application Insights Profiler sur ces services :
* [Azure App Service](../../azure-monitor/app/profiler.md?toc=/azure/azure-monitor/toc.json)
* [Azure Cloud Services](profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric](profiler-vm.md?toc=/azure/azure-monitor/toc.json)

## <a name="deploy-profiler-on-a-virtual-machine-or-a-virtual-machine-scale-set"></a>Déployer Profiler sur une machine virtuelle ou un groupe de machines virtuelles identiques
Cet article vous montre comment exécuter Application Insights Profiler sur votre machine virtuelle Azure ou votre groupe de machines virtuelles identiques Azure. Profiler est installé avec l’extension Azure Diagnostics pour machines virtuelles. Configurez l’extension pour exécuter Profiler et générez le SDK Application Insights dans votre application.

1. Ajoutez le SDK Application Insights à votre [application ASP.NET](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net) ou [application .NET](https://docs.microsoft.com/azure/application-insights/windows-services?toc=/azure/azure-monitor/toc.json) ordinaire.  
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

   Les commandes PowerShell suivantes offrent une autre approche pour les machines virtuelles existantes, qui touche uniquement l’extension Azure Diagnostics. Ajoutez le ProfilerSink précédemment mentionné à la configuration retournée par la commande Get-AzureRmVMDiagnosticsExtension, puis passez la configuration mise à jour à la commande Set-AzureRmVMDiagnosticsExtension.

    ```powershell
    $ConfigFilePath = [IO.Path]::GetTempFileName()
    # After you export the currently deployed Diagnostics config to a file, edit it to include the ApplicationInsightsProfiler sink.
    (Get-AzureRmVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM").PublicSettings | Out-File -Verbose $ConfigFilePath
    # Set-AzureRmVMDiagnosticsExtension might require the -StorageAccountName argument
    # If your original diagnostics configuration had the storageAccountName property in the protectedSettings section (which is not downloadable), be sure to pass the same original value you had in this cmdlet call.
    Set-AzureRmVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM" -DiagnosticsConfigurationPath $ConfigFilePath
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

- Générez le trafic vers votre application (par exemple, lancez un [test de disponibilité](https://docs.microsoft.com/azure/application-insights/monitor-web-app-availability)). Attendez ensuite 10 à 15 minutes le temps que les traces soient envoyées à l’instance Application Insights.
- Consultez [Traces du profileur](https://docs.microsoft.com/azure/application-insights/profiler-overview?toc=/azure/azure-monitor/toc.json) dans le portail Azure.
- Consultez [Résolution des problèmes de Profiler](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json) pour obtenir de l’aide sur la résolution des problèmes de Profiler.
