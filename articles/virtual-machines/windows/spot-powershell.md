---
title: Utiliser PowerShell pour déployer des machines virtuelles Azure Spot
description: Découvrez comment utiliser Azure PowerShell afin de déployer des machines virtuelles Azure Spot pour réaliser des économies sur les coûts.
author: cynthn
ms.service: virtual-machines
ms.subservice: spot
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 03/22/2021
ms.author: cynthn
ms.reviewer: jagaveer
ms.openlocfilehash: bcf3cb5a020bb2efd5a2528c28421deba206ee31
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108125262"
---
# <a name="deploy-azure-spot-virtual-machines-using-azure-powershell"></a>Déployer des machines virtuelles Azure Spot à l’aide d’Azure PowerShell


L’utilisation de [machines virtuelles Azure Spot](../spot-vms.md) vous permet de disposer de notre capacité inutilisée en réalisant des économies significatives. Dès qu’Azure aura besoin de récupérer la capacité, l’infrastructure Azure supprimera les machines virtuelles Azure Spot. Les machines virtuelles Azure Spot sont donc appropriées pour les charges de travail capables de gérer les interruptions, comme les travaux de traitement par lots, les environnements de développement et de test, les charges de travail de calcul importantes, entre autres.

Les tarifs des machines virtuelles Azure Spot sont variables, en fonction de la région et de la référence SKU. Pour plus d’informations, consultez les prix des machines virtuelles pour [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) et [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). Pour plus d’informations sur la définition du prix maximal, consultez [Machines virtuelles Azure Spot - Tarifs](../spot-vms.md#pricing).

Vous avez la possibilité de définir un prix maximal que vous êtes prêt à payer, par heure, pour la machine virtuelle. Le prix maximal d’une machine virtuelle Azure Spot peut être défini en dollars américains (USD), en utilisant jusqu’à 5 décimales. Par exemple, la valeur `0.98765` correspond à un prix maximal de 0,98765 $ USD par heure. Si vous définissez `-1` comme prix maximal, la machine virtuelle n’est pas supprimée en fonction du prix. Le prix de la machine virtuelle sera le prix actuel de Spot ou le prix d’une machine virtuelle standard, la valeur la plus faible étant retenue, à condition que la capacité et le quota soient disponibles.


## <a name="create-the-vm"></a>Création de la machine virtuelle

Créez une machine virtuelle Spot avec [New-AzVmConfig](/powershell/module/az.compute/new-azvmconfig) pour créer la configuration. Incluez `-Priority Spot` et affectez à `-MaxPrice` :
- `-1` afin que la machine virtuelle ne soit pas supprimée en fonction du prix.
- un montant en dollars, jusqu’à 5 chiffres. Par exemple, `-MaxPrice .98765` signifie que la machine virtuelle sera désallouée une fois que le prix d’une machine virtuelle Spot atteindra 0,98765 $ par heure.


Cet exemple crée une machine virtuelle Spot qui n’est pas désallouée en fonction du prix (uniquement quand Azure a besoin de récupérer toute la capacité). La stratégie d’éviction est définie pour désallouer la machine virtuelle, afin qu’elle puisse être redémarrée ultérieurement. Si vous souhaitez supprimer la machine virtuelle et le disque sous-jacent lorsque la machine virtuelle est supprimée, définissez `-EvictionPolicy` sur `Delete` dans `New-AzVMConfig`.


```azurepowershell-interactive
$resourceGroup = "mySpotRG"
$location = "eastus"
$vmName = "mySpotVM"
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."
New-AzResourceGroup -Name $resourceGroup -Location $location
$subnetConfig = New-AzVirtualNetworkSubnetConfig `
   -Name mySubnet -AddressPrefix 192.168.1.0/24
$vnet = New-AzVirtualNetwork -ResourceGroupName $resourceGroup `
   -Location $location -Name MYvNET -AddressPrefix 192.168.0.0/16 `
   -Subnet $subnetConfig
$pip = New-AzPublicIpAddress -ResourceGroupName $resourceGroup -Location $location `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4
$nsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
  -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389 -Access Allow
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location $location `
  -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP
$nic = New-AzNetworkInterface -Name myNic -ResourceGroupName $resourceGroup -Location $location `
  -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration and set this to be an Azure Spot Virtual Machine

$vmConfig = New-AzVMConfig -VMName $vmName -VMSize Standard_D1 -Priority "Spot" -MaxPrice -1 -EvictionPolicy Deallocate | `
Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred | `
Set-AzVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2016-Datacenter -Version latest | `
Add-AzVMNetworkInterface -Id $nic.Id

New-AzVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig
```

Une fois la machine virtuelle créée, vous pouvez effectuer une requête pour afficher le prix maximal pour toutes les machines virtuelles du groupe de ressources.

```azurepowershell-interactive
Get-AzVM -ResourceGroupName $resourceGroup | `
   Select-Object Name,@{Name="maxPrice"; Expression={$_.BillingProfile.MaxPrice}}
```

## <a name="simulate-an-eviction"></a>Simuler une éviction

Vous pouvez simuler l’éviction d’une machine virtuelle spot Azure à l’aide de REST, de PowerShell ou de CLI, afin de tester l’efficacité de la réponse de votre application à une éviction soudaine.

Dans la plupart des cas, vous pouvez utiliser l’API REST [Machines virtuelles – Simuler une éviction](/rest/api/compute/virtualmachines/simulateeviction) pour faciliter le test automatisé des applications. Pour REST, un `Response Code: 204` signifie que l’éviction simulée a réussi. Vous pouvez combiner des évictions simulées avec le [service d’événement planifié](scheduled-events.md) pour automatiser la manière dont votre application répondra lors de l’éviction de la machine virtuelle.

Pour voir les événements planifiés en action, regardez [Azure Friday – Utilisation d’événements planifiés Azure pour préparer la maintenance de machines virtuelles](https://channel9.msdn.com/Shows/Azure-Friday/Using-Azure-Scheduled-Events-to-Prepare-for-VM-Maintenance).


### <a name="quick-test"></a>Test rapide

Pour un test rapide montrant le fonctionnement d’une éviction simulée, nous allons examiner l’interrogation du service d’événement planifié pour voir à quoi ressemble la simulation d’une éviction à l’aide de PowerShell.

Le service d’événement planifié est activé pour votre service la première fois que vous effectuez une demande d’événements. 

Accédez à distance à votre machine virtuelle, puis ouvrez une invite de commandes. 

Dans l’invite de commandes sur votre machine virtuelle, tapez :

```
curl -H Metadata:true http://169.254.169.254/metadata/scheduledevents?api-version=2019-08-01
```

Cette première réponse peut prendre jusqu’à 2 minutes. À partir de maintenant, ils devraient afficher la sortie presque immédiatement.

À partir d’un ordinateur sur lequel le module Az PowerShell est installé (comme votre ordinateur local), simulez une éviction à l’aide de la commande [Set-AzVM](/powershell/module/az.compute/set-azvm). Remplacez le nom du groupe de ressources et le nom de la machine virtuelle par les vôtres 

```azurepowershell-interactive
Set-AzVM -ResourceGroupName "mySpotRG" -Name "mySpotVM" -SimulateEviction
```

La sortie de la réponse contiendra `Status: Succeeded` si la demande a été effectuée avec succès.

Revenez rapidement à votre connexion à distance à votre machine virtuelle spot et interrogez à nouveau le point de terminaison Scheduled Events. Répétez la commande suivante jusqu’à obtenir une sortie contenant plus d’informations :

```
curl -H Metadata:true http://169.254.169.254/metadata/scheduledevents?api-version=2019-08-01
```

Lorsque le service d’événement planifié reçoit la notification d’éviction, vous obtenez une réponse ressemblant à ceci :

```output
{"DocumentIncarnation":1,"Events":[{"EventId":"A123BC45-1234-5678-AB90-ABCDEF123456","EventStatus":"Scheduled","EventType":"Preempt","ResourceType":"VirtualMachine","Resources":["myspotvm"],"NotBefore":"Tue, 16 Mar 2021 00:58:46 GMT","Description":"","EventSource":"Platform"}]}
```

Vous pouvez voir `"EventType":"Preempt"`, et la ressource est la ressource de machine virtuelle `"Resources":["myspotvm"]`. 

Vous pouvez également voir quand la machine virtuelle sera supprimée en vérifiant la `"NotBefore"` valeur. La machine virtuelle ne sera pas supprimée avant l’heure indiquée dans `NotBefore`. Cela indique la fenêtre pendant laquelle votre application peut se fermer normalement.


## <a name="next-steps"></a>Étapes suivantes

Vous pouvez également créer une machine virtuelle Azure Spot à l’aide d’[Azure CLI](../linux/spot-cli.md), du [portail](../spot-portal.md) ou d’un [modèle](../linux/spot-template.md).

Interroger les informations de tarification actuelles à l’aide de l’[API des prix de vente au détail d’Azure](/rest/api/cost-management/retail-prices/azure-retail-prices) pour plus d’informations sur la tarification des machines virtuelles Azure Spot. Les `meterName` et `skuName` contiennent tous les deux `Spot`.

Si vous rencontrez une erreur, consultez [Codes d’erreur](../error-codes-spot.md).