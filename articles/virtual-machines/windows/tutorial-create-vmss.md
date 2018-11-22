---
title: 'Didacticiel : créer un groupe de machines virtuelles identiques pour Windows dans Azure | Microsoft Docs'
description: Avec ce didacticiel, vous allez apprendre à utiliser Azure PowerShell pour créer et déployer une application hautement disponible sur les machines virtuelles Windows à l’aide d’un groupe de machines virtuelles identiques
services: virtual-machine-scale-sets
documentationcenter: ''
author: zr-msft
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: ''
ms.topic: tutorial
ms.date: 11/07/2018
ms.author: zarhoads
ms.custom: mvc
ms.openlocfilehash: 561bd71f371be9261686c0a4c34f388ed8324d76
ms.sourcegitcommit: 022cf0f3f6a227e09ea1120b09a7f4638c78b3e2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/21/2018
ms.locfileid: "52284419"
---
# <a name="tutorial-create-a-virtual-machine-scale-set-and-deploy-a-highly-available-app-on-windows-with-azure-powershell"></a>Didacticiel : créer un groupe de machines virtuelles identiques et déployer une application hautement disponible sur Windows avec Azure PowerShell
Un groupe de machines virtuelles identiques vous permet de déployer et de gérer un ensemble de machines virtuelles identiques prenant en charge la mise à l’échelle automatique. Vous pouvez mettre à l’échelle manuellement le nombre de machines virtuelles du groupe identique. Vous pouvez également définir des règles pour mettre à l’échelle automatiquement en fonction de l’utilisation des ressources (processeur, demande de mémoire, trafic réseau, etc.). Ce tutoriel explique comment déployer un groupe de machines virtuelles identiques dans Azure et vous apprend à :

> [!div class="checklist"]
> * Utiliser l’extension de script personnalisé pour définir un site IIS à mettre à l’échelle
> * Créer un équilibrage de charge pour votre groupe identique
> * Créer un groupe de machines virtuelles identiques
> * Augmenter ou réduire le nombre d’instances dans un groupe identique
> * Créer des règles de mise à l’échelle automatique

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Si vous choisissez d’installer et d’utiliser PowerShell en local, ce didacticiel requiert le module Azure PowerShell version 6.0.0 ou ultérieure. Exécutez `Get-Module -ListAvailable AzureRM` pour trouver la version. Si vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-azurerm-ps). Si vous exécutez PowerShell en local, vous devez également exécuter `Connect-AzureRmAccount` pour créer une connexion avec Azure.


## <a name="scale-set-overview"></a>Vue d’ensemble des groupes identiques
Un groupe de machines virtuelles identiques vous permet de déployer et de gérer un ensemble de machines virtuelles identiques prenant en charge la mise à l’échelle automatique. Les machines virtuelles d’un groupe identique sont réparties entre les domaines d’erreur logique et de mise à jour, dans un ou plusieurs *groupes de placement*. Les groupes de placement contiennent des machines virtuelles configurées de manière similaire, semblables à des [groupes à haute disponibilité](tutorial-availability-sets.md).

Les machines virtuelles sont créées en fonction des besoins dans un groupe identique. En définissant des règles de mise à l’échelle automatique, vous pouvez contrôler quand et comment les machines virtuelles sont ajoutées ou supprimées au niveau du groupe identique. Ces règles peuvent se déclencher en fonction de mesures telles que la charge du processeur, l’utilisation de la mémoire ou le trafic réseau.

Les groupes identiques peuvent prendre en charge jusqu’à 1 000 machines virtuelles quand vous utilisez une image de plateforme Azure. Pour les charges de travail qui s’accompagnent de contraintes importantes en matière d’installation ou de personnalisation de machines virtuelles, vous pouvez [créer une image de machine virtuelle personnalisée](tutorial-custom-images.md). Vous pouvez créer un maximum de 300 machines virtuelles dans un groupe identique lorsque vous utilisez une image personnalisée.


## <a name="create-a-scale-set"></a>Créer un groupe identique
Créez un groupe identique de machines virtuelles avec [New-AzureRmVmss](/powershell/module/azurerm.compute/new-azurermvmss). L’exemple suivant crée un groupe identique nommé *myScaleSet* qui utilise l’image de plateforme *Windows Server 2016 Datacenter*. Les ressources réseau Azure pour le réseau virtuel, l’adresse IP publique et l’équilibreur de charge sont automatiquement créées. Quand vous y êtes invité, vous pouvez définir vos propres informations d’identification d’administration pour les instances de machine virtuelle du groupe identique :

```azurepowershell-interactive
New-AzureRmVmss `
  -ResourceGroupName "myResourceGroupScaleSet" `
  -Location "EastUS" `
  -VMScaleSetName "myScaleSet" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicyMode "Automatic"
```

La création et la configuration des l’ensemble des ressources et des machines virtuelles du groupe identique prennent quelques minutes.


## <a name="deploy-sample-application"></a>Déployer un exemple d’application
Pour tester votre groupe identique, installez une application web de base. L’extension de script personnalisé Azure permet de télécharger et d’exécuter un script qui installe IIS sur les instances de machine virtuelle. Cette extension est utile pour la configuration post-déploiement, l’installation de logiciels ou toute autre tâche de configuration ou de gestion. Pour plus d’informations, consultez [Vue d’ensemble de l’extension de script personnalisé](extensions-customscript.md).

Utilisez l’extension de script personnalisé pour installer un serveur web IIS de base. Appliquez l’extension de script personnalisé qui installe IIS comme suit :

```azurepowershell-interactive
# Define the script for your Custom Script Extension to run
$publicSettings = @{
    "fileUris" = (,"https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate-iis.ps1");
    "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File automate-iis.ps1"
}

# Get information about the scale set
$vmss = Get-AzureRmVmss `
            -ResourceGroupName "myResourceGroupScaleSet" `
            -VMScaleSetName "myScaleSet"

# Use Custom Script Extension to install IIS and configure basic website
Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmss `
    -Name "customScript" `
    -Publisher "Microsoft.Compute" `
    -Type "CustomScriptExtension" `
    -TypeHandlerVersion 1.8 `
    -Setting $publicSettings

# Update the scale set and apply the Custom Script Extension to the VM instances
Update-AzureRmVmss `
    -ResourceGroupName "myResourceGroupScaleSet" `
    -Name "myScaleSet" `
    -VirtualMachineScaleSet $vmss
```

## <a name="allow-traffic-to-application"></a>Autoriser le trafic vers l’application

Pour autoriser l’accès à l’application web de base, créez un groupe de sécurité réseau avec [New-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig) et [New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup). Pour plus d’informations, consultez [Fonctionnalités réseau pour les groupes de machines virtuelles identiques Azure](../../virtual-machine-scale-sets/virtual-machine-scale-sets-networking.md).

```azurepowershell-interactive
# Get information about the scale set
$vmss = Get-AzureRmVmss `
            -ResourceGroupName "myResourceGroupScaleSet" `
            -VMScaleSetName "myScaleSet"

#Create a rule to allow traffic over port 80
$nsgFrontendRule = New-AzureRmNetworkSecurityRuleConfig `
  -Name myFrontendNSGRule `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 200 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 80 `
  -Access Allow

#Create a network security group and associate it with the rule
$nsgFrontend = New-AzureRmNetworkSecurityGroup `
  -ResourceGroupName  "myResourceGroupScaleSet" `
  -Location EastUS `
  -Name myFrontendNSG `
  -SecurityRules $nsgFrontendRule

$vnet = Get-AzureRmVirtualNetwork `
  -ResourceGroupName  "myResourceGroupScaleSet" `
  -Name myVnet

$frontendSubnet = $vnet.Subnets[0]

$frontendSubnetConfig = Set-AzureRmVirtualNetworkSubnetConfig `
  -VirtualNetwork $vnet `
  -Name mySubnet `
  -AddressPrefix $frontendSubnet.AddressPrefix `
  -NetworkSecurityGroup $nsgFrontend

Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

# Update the scale set and apply the Custom Script Extension to the VM instances
Update-AzureRmVmss `
    -ResourceGroupName "myResourceGroupScaleSet" `
    -Name "myScaleSet" `
    -VirtualMachineScaleSet $vmss
```

## <a name="test-your-scale-set"></a>Tester votre groupe identique
Pour voir votre groupe identique en action, obtenez l’adresse IP publique de votre équilibreur de charge avec [Get-AzureRmPublicIPAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress). L’exemple suivant affiche l’adresse IP pour *myPublicIP* qui a été créée dans le cadre du groupe identique :

```azurepowershell-interactive
Get-AzureRmPublicIPAddress `
    -ResourceGroupName "myResourceGroupScaleSet" `
    -Name "myPublicIPAddress" | select IpAddress
```

Entrez l’adresse IP publique dans un navigateur web. L’application Web s’affiche, avec notamment le nom d’hôte de la machine virtuelle sur laquelle l’équilibrage de charge a distribué le trafic :

![Site IIS en cours d’exécution](./media/tutorial-create-vmss/running-iis-site.png)

Pour voir fonctionner le groupe identique, vous pouvez forcer l’actualisation de votre navigateur web. L’équilibreur de charge répartit alors le trafic entre toutes les machines virtuelles exécutant votre application.


## <a name="management-tasks"></a>Tâches de gestion
Tout au long du cycle de vie du groupe identique, vous devrez peut-être exécuter une ou plusieurs tâches de gestion. En outre, vous souhaiterez peut-être créer des scripts pour automatiser les diverses tâches liées au cycle de vie. Azure PowerShell offre un moyen rapide d’effectuer ces tâches. Voici quelques tâches courantes.

### <a name="view-vms-in-a-scale-set"></a>Afficher les machines virtuelles d’un groupe identique
Pour afficher une liste d’instances de machine virtuelle dans un groupe identique, utilisez [Get-AzureRmVmssVM](/powershell/module/azurerm.compute/get-azurermvmssvm) comme suit :

```azurepowershell-interactive
Get-AzureRmVmssVM -ResourceGroupName "myResourceGroupScaleSet" -VMScaleSetName "myScaleSet"
```

L’exemple suivant illustre deux instances de machine virtuelle dans le groupe identique :

```powershell
ResourceGroupName                 Name Location             Sku InstanceID ProvisioningState
-----------------                 ---- --------             --- ---------- -----------------
MYRESOURCEGROUPSCALESET   myScaleSet_0   eastus Standard_DS1_v2          0         Succeeded
MYRESOURCEGROUPSCALESET   myScaleSet_1   eastus Standard_DS1_v2          1         Succeeded
```

Pour afficher des informations supplémentaires sur une instance spécifique de la machine virtuelle, ajoutez le paramètre `-InstanceId` à [Get-AzureRmVmssVM](/powershell/module/azurerm.compute/get-azurermvmssvm). L’exemple suivant présente des informations sur l’instance de machine virtuelle *1* :

```azurepowershell-interactive
Get-AzureRmVmssVM -ResourceGroupName "myResourceGroupScaleSet" -VMScaleSetName "myScaleSet" -InstanceId "1"
```


### <a name="increase-or-decrease-vm-instances"></a>Augmenter ou diminuer les instances de machines virtuelles
Pour afficher le nombre d’instances actuellement présentes dans un groupe identique, utilisez [Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss) et formulez une requête *sku.capacity* :

```azurepowershell-interactive
Get-AzureRmVmss -ResourceGroupName "myResourceGroupScaleSet" `
    -VMScaleSetName "myScaleSet" | `
    Select -ExpandProperty Sku
```

Vous pouvez ensuite augmenter ou diminuer manuellement le nombre de machines virtuelles présentes dans le groupe identique avec [Update-AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss). L’exemple suivant fixe le nombre de machines virtuelles présentes dans votre groupe identique à *3* :

```azurepowershell-interactive
# Get current scale set
$scaleset = Get-AzureRmVmss `
  -ResourceGroupName "myResourceGroupScaleSet" `
  -VMScaleSetName "myScaleSet"

# Set and update the capacity of your scale set
$scaleset.sku.capacity = 3
Update-AzureRmVmss -ResourceGroupName "myResourceGroupScaleSet" `
    -Name "myScaleSet" `
    -VirtualMachineScaleSet $scaleset
```

Plusieurs minutes sont nécessaires avant que le nombre d’instances spécifié soit mis à jour pour votre groupe identique.


### <a name="configure-autoscale-rules"></a>Configurer des règles de mise à l’échelle automatique
Au lieu d’adapter manuellement le nombre d’instances présentes dans votre groupe identique, vous pouvez définir des règles de mise à l’échelle automatique. Ces règles surveillent les instances présentes dans votre groupe identique et répondent en conséquence en fonction des métriques et des seuils que vous définissez. L’exemple suivant augmente le nombre d’instances d’une unité dès que la charge moyenne du processeur dépasse 60 % sur une période de 5 minutes. Si la charge moyenne du processeur descend ensuite en dessous de 30 % sur une période de 5 minutes, le nombre d’instances diminue d’une unité :

```azurepowershell-interactive
# Define your scale set information
$mySubscriptionId = (Get-AzureRmSubscription)[0].Id
$myResourceGroup = "myResourceGroupScaleSet"
$myScaleSet = "myScaleSet"
$myLocation = "East US"
$myScaleSetId = (Get-AzureRmVmss -ResourceGroupName $myResourceGroup -VMScaleSetName $myScaleSet).Id 

# Create a scale up rule to increase the number instances after 60% average CPU usage exceeded for a 5-minute period
$myRuleScaleUp = New-AzureRmAutoscaleRule `
  -MetricName "Percentage CPU" `
  -MetricResourceId $myScaleSetId `
  -Operator GreaterThan `
  -MetricStatistic Average `
  -Threshold 60 `
  -TimeGrain 00:01:00 `
  -TimeWindow 00:05:00 `
  -ScaleActionCooldown 00:05:00 `
  -ScaleActionDirection Increase `
  -ScaleActionValue 1

# Create a scale down rule to decrease the number of instances after 30% average CPU usage over a 5-minute period
$myRuleScaleDown = New-AzureRmAutoscaleRule `
  -MetricName "Percentage CPU" `
  -MetricResourceId $myScaleSetId `
  -Operator LessThan `
  -MetricStatistic Average `
  -Threshold 30 `
  -TimeGrain 00:01:00 `
  -TimeWindow 00:05:00 `
  -ScaleActionCooldown 00:05:00 `
  -ScaleActionDirection Decrease `
  -ScaleActionValue 1

# Create a scale profile with your scale up and scale down rules
$myScaleProfile = New-AzureRmAutoscaleProfile `
  -DefaultCapacity 2  `
  -MaximumCapacity 10 `
  -MinimumCapacity 2 `
  -Rule $myRuleScaleUp,$myRuleScaleDown `
  -Name "autoprofile"

# Apply the autoscale rules
Add-AzureRmAutoscaleSetting `
  -Location $myLocation `
  -Name "autosetting" `
  -ResourceGroup $myResourceGroup `
  -TargetResourceId $myScaleSetId `
  -AutoscaleProfile $myScaleProfile
```

Pour plus d’informations de conception sur l’utilisation de la mise à l’échelle automatique, consultez [Meilleures pratiques relatives à la mise à l’échelle automatique](/azure/architecture/best-practices/auto-scaling).


## <a name="next-steps"></a>Étapes suivantes
Ce didacticiel vous a montré comment créer un groupe de machines virtuelles identiques. Vous avez appris à effectuer les actions suivantes :

> [!div class="checklist"]
> * Utiliser l’extension de script personnalisé pour définir un site IIS à mettre à l’échelle
> * Créer un équilibrage de charge pour votre groupe identique
> * Créer un groupe de machines virtuelles identiques
> * Augmenter ou réduire le nombre d’instances dans un groupe identique
> * Créer des règles de mise à l’échelle automatique

Passez au didacticiel suivant pour en savoir plus sur les concepts de l’équilibrage de charge des machines virtuelles.

> [!div class="nextstepaction"]
> [Équilibrage de charge des machines virtuelles](tutorial-load-balancer.md)
