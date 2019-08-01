---
title: Surveiller les performances des applications hébergées sur les machines virtuelles Azure et les groupes de machines virtuelles identiques Azure | Microsoft Docs
description: Analyse des performances des applications pour les machines virtuelles Azure et les groupes de machines virtuelles identiques Azure. Analysez la charge, le temps de réponse et les dépendances dans des graphiques, et définissez des alertes sur les performances.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.topic: conceptual
ms.date: 06/27/2019
ms.author: mbullwin
ms.openlocfilehash: f2c6b98fd0be2061e9d8cab5c063cafadf71476a
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68597454"
---
# <a name="monitor-application-performance-hosted-on-azure-vm-and-azure-virtual-machine-scale-sets"></a>Surveiller les performances des applications hébergées sur les machines virtuelles Azure et les groupes de machines virtuelles identiques Azure

L’activation de la surveillance sur vos applications Web .NET en cours d’exécution sur des [machines virtuelles Azure](https://azure.microsoft.com/services/virtual-machines/) et sur des [groupe de machines virtuelles identiques Azure](https://docs.microsoft.com/azure/virtual-machine-scale-sets/) est désormais plus facile que jamais. Bénéficiez de tous les avantages de l’utilisation de Application Insights sans modifier votre code.

Cet article vous guide dans l’activation de la surveillance Application Insights à l’aide de l’extension ApplicationMonitoringWindows et fournit des conseils préliminaires pour l’automatisation du processus pour les déploiements à grande échelle.

> [!IMPORTANT]
> L’extension ApplicationMonitoringWindows Azure est actuellement disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou n’être pas prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="enable-application-insights"></a>Activer Application Insights

Il existe deux façons d’activer l’analyse des applications pour les machines virtuelles Azure et les applications hébergées d’un groupe de machines virtuelles identiques Azure :

* **Analyse des applications basées sur les agents** (extension ApplicationMonitoringWindows).
    * Cette méthode est la plus facile à activer, car elle ne nécessite aucune configuration avancée. Elle est souvent appelée supervision « runtime ». Pour les machines virtuelles Azure et les groupes de machines virtuelles identiques Azure, nous vous recommandons d’activer ce niveau de surveillance au minimum. Après cela, en fonction de votre scénario spécifique, vous pouvez évaluer si une instrumentation manuelle est nécessaire.
    * Actuellement, seules les applications hébergées sur .NET IIS sont prises en charge.

* L’**instrumentation manuelle de l’application avec le code** en installant le SDK Application Insights.

    * Cette approche est beaucoup plus personnalisable, mais elle nécessite d’[ajouter une dépendance sur les packages NuGet du SDK Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net). Cette méthode implique également de gérer vous-même l’installation des mises à jour vers la dernière version des packages.

    * Utilisez cette méthode si vous devez effectuer des appels d’API personnalisés pour suivre les événements/dépendances qui ne sont pas capturés par défaut avec la supervision basée sur un agent. Pour en savoir plus, consultez l’[article sur l’API Application Insights pour les événements et mesures personnalisés](https://docs.microsoft.com/azure/azure-monitor/app/api-custom-events-metrics).

> [!NOTE]
> Si les deux méthodes, la supervision basée sur un agent et l’instrumentation manuelle basée sur un SDK, sont détectées, seuls les paramètres de l’instrumentation manuelle sont appliqués. Cela évite que des données en double soient envoyées. Pour en savoir plus, consultez la [section de résolution des problèmes](#troubleshooting) ci-après.

## <a name="manage-agent-based-monitoring-for-net-applications-on-vm-using-powershell"></a>Gérer l’analyse basée sur les agents pour les applications .NET sur une machine virtuelle à l’aide de PowerShell

Installer ou mettre à jour l’extension d’analyse des applications pour la machine virtuelle
```powershell
$publicCfgJsonString = '
{
  "RedfieldConfiguration": {
    "InstrumentationKeyMap": {
      "Filters": [
        {
          "AppFilter": ".*",
          "MachineFilter": ".*",
          "InstrumentationSettings" : {
            "InstrumentationKey": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
          }
        }
      ]
    }
  }
}
';
$privateCfgJsonString = '{}';

Set-AzVMExtension -ResourceGroupName "<myVmResourceGroup>" -VMName "<myVmName>" -Location "South Central US" -Name "ApplicationMonitoring" -Publisher "Microsoft.Azure.Diagnostics" -Type "ApplicationMonitoringWindows" -Version "2.8" -SettingString $publicCfgJsonString -ProtectedSettingString $privateCfgJsonString
```

Désinstaller l’extension d’analyse des applications de la machine virtuelle
```powershell
Remove-AzVMExtension -ResourceGroupName "<myVmResourceGroup>" -VMName "<myVmName>" -Name "ApplicationMonitoring"
```

Interroger l’état de l’extension d’analyse des applications pour la machine virtuelle
```powershell
Get-AzVMExtension -ResourceGroupName "<myVmResourceGroup>" -VMName "<myVmName>" -Name ApplicationMonitoring -Status
```

Obtenir la liste des extensions installées pour la machine virtuelle
```powershell
Get-AzResource -ResourceId "/subscriptions/<mySubscriptionId>/resourceGroups/<myVmResourceGroup>/providers/Microsoft.Compute/virtualMachines/<myVmName>/extensions"

# Name              : ApplicationMonitoring
# ResourceGroupName : <myVmResourceGroup>
# ResourceType      : Microsoft.Compute/virtualMachines/extensions
# Location          : southcentralus
# ResourceId        : /subscriptions/<mySubscriptionId>/resourceGroups/<myVmResourceGroup>/providers/Microsoft.Compute/virtualMachines/<myVmName>/extensions/ApplicationMonitoring
```

## <a name="manage-agent-based-monitoring-for-net-applications-on-azure-virtual-machine-scale-set-using-powershell"></a>Gérer la surveillance basée sur les agents pour les applications .NET sur un groupe de machines virtuelles identiques Azure à l’aide de PowerShell

Installer ou mettre à jour l’extension d’analyse des applications pour le groupe de machines virtuelles identiques Azure
```powershell
$publicCfgHashtable =
@{
  "RedfieldConfiguration"= @{
    "InstrumentationKeyMap"= @{
      "Filters"= @(
        @{
          "AppFilter"= ".*";
          "MachineFilter"= ".*";
          "InstrumentationSettings"= @{
            "InstrumentationKey"= "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"; # Application Insights Instrumentation Key, create new Application Insights resource if you don't have one. https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/microsoft.insights%2Fcomponents
          }
        }
      )
    }
  }
};
$privateCfgHashtable = @{};

$vmss = Get-AzVmss -ResourceGroupName "<myResourceGroup>" -VMScaleSetName "<myVmssName>"

Add-AzVmssExtension -VirtualMachineScaleSet $vmss -Name "ApplicationMonitoring" -Publisher "Microsoft.Azure.Diagnostics" -Type "ApplicationMonitoringWindows" -TypeHandlerVersion "2.8" -Setting $publicCfgHashtable -ProtectedSetting $privateCfgHashtable

Update-AzVmss -ResourceGroupName $vmss.ResourceGroupName -Name $vmss.Name -VirtualMachineScaleSet $vmss

# Note: depending on your update policy, you might need to run Update-AzVmssInstance for each instance
```

Désinstaller l’extension d’analyse des applications d’un groupe de machines virtuelles identiques Azure
```powershell
$vmss = Get-AzVmss -ResourceGroupName "<myResourceGroup>" -VMScaleSetName "<myVmssName>"

Remove-AzVmssExtension -VirtualMachineScaleSet $vmss -Name "ApplicationMonitoring"

Update-AzVmss -ResourceGroupName $vmss.ResourceGroupName -Name $vmss.Name -VirtualMachineScaleSet $vmss

# Note: depending on your update policy, you might need to run Update-AzVmssInstance for each instance
```

Interroger l’état de l’extension d’analyse des applications pour le groupe de machines virtuelles identiques Azure
```powershell
# Not supported by extensions framework
```

Obtenir la liste des extensions installées pour le groupe de machines virtuelles identiques Azure
```powershell
Get-AzResource -ResourceId /subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.Compute/virtualMachineScaleSets/<myVmssName>/extensions

# Name              : ApplicationMonitoringWindows
# ResourceGroupName : <myResourceGroup>
# ResourceType      : Microsoft.Compute/virtualMachineScaleSets/extensions
# Location          :
# ResourceId        : /subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.Compute/virtualMachineScaleSets/<myVmssName>/extensions/ApplicationMonitoringWindows
```

## <a name="troubleshooting"></a>Résolution de problèmes

Vous trouverez ci-dessous notre guide de résolution des problèmes pas à pas pour la surveillance basée sur une extension pour les applications .NET exécutées sur les machines virtuelles Azure et les groupes de machines virtuelles identiques Azure.

> [!NOTE]
> Les applications .NET Core, Java et Node.js sont uniquement prises en charge sur les machines virtuelles Azure et les groupes de machines virtuelles identiques Azure via une instrumentation manuelle basée sur un kit de développement logiciel et, par conséquent, les étapes ci-dessous ne s’appliquent pas à ces scénarios.

La sortie de l’exécution de l’extension est journalisées dans des fichiers figurant dans les répertoires suivants :
```Windows
C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.ApplicationMonitoringWindows\<version>\
```

## <a name="next-steps"></a>Étapes suivantes
* Découvrez comment [déployer une application sur un groupe de machines virtuelles identiques Azure](../../virtual-machine-scale-sets/virtual-machine-scale-sets-deploy-app.md).
* [Configuration des tests de disponibilité web](monitor-web-app-availability.md), pour recevoir des alertes en cas d’interruption de votre point de terminaison.
