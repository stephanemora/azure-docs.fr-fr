---
title: Utiliser PowerShell pour déployer des machines virtuelles Azure Spot
description: Découvrez comment utiliser Azure PowerShell afin de déployer des machines virtuelles Spot pour réaliser des économies sur les coûts.
services: virtual-machines-windows
author: cynthn
manager: gwallace
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: article
ms.date: 02/11/2020
ms.author: cynthn
ms.openlocfilehash: 17186d1d7b50ea872dc47eca8c2c4491787d2a38
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77158942"
---
# <a name="preview-deploy-spot-vms-using-azure-powershell"></a>Aperçu : Déployer des machines virtuelles Spot à l’aide d’Azure PowerShell


L’utilisation de [machines virtuelles Spot](spot-vms.md) vous permet de tirer parti de notre capacité inutilisée en réalisant des économies significatives. Dès qu’Azure a besoin de récupérer toute la capacité, l’infrastructure Azure supprime les machines virtuelles Spot. Les machines virtuelles Spot sont donc appropriées pour les charges de travail capables de gérer les interruptions, comme les travaux de traitement par lots, les environnements de développement et de test, les charges de travail de calcul importantes, entre autres.

Les tarifs des machines virtuelles Spot sont variables, en fonction de la région et de la référence SKU. Pour plus d’informations, consultez les prix des machines virtuelles pour [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) et [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). Pour plus d’informations sur la définition du prix maximal, consultez [Machines virtuelles Spot - Tarifs](spot-vms.md#pricing).

Vous avez la possibilité de définir un prix maximal que vous êtes prêt à payer, par heure, pour la machine virtuelle. Le prix maximal d’une machine virtuelle Spot peut être défini en dollars américains (USD), en utilisant jusqu’à 5 décimales. Par exemple, la valeur `0.98765` correspond à un prix maximal de 0,98765 $ USD par heure. Si vous définissez `-1` comme prix maximal, la machine virtuelle n’est pas supprimée en fonction du prix. Le prix de la machine virtuelle sera le prix actuel de Spot ou le prix d’une machine virtuelle standard, la valeur la plus faible étant retenue, à condition que la capacité et le quota soient disponibles.

> [!IMPORTANT]
> Les instances Spot sont actuellement en préversion publique.
> Cette préversion n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>



## <a name="create-the-vm"></a>Création de la machine virtuelle

Créez une machine virtuelle Spot avec [New-AzVmConfig](/powershell/module/az.compute/new-azvmconfig) pour créer la configuration. Incluez `-Priority Spot` et affectez à `-MaxPrice` :
- `-1` afin que la machine virtuelle ne soit pas supprimée en fonction du prix.
- un montant en dollars, jusqu’à 5 chiffres. Par exemple, `-MaxPrice .98765` signifie que la machine virtuelle sera désallouée une fois que le prix d’une machine virtuelle Spot atteindra 0,98765 $ par heure.


Cet exemple crée une machine virtuelle Spot qui n’est pas désallouée en fonction du prix (uniquement quand Azure a besoin de récupérer toute la capacité).

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

# Create a virtual machine configuration and set this to be a Spot VM

$vmConfig = New-AzVMConfig -VMName $vmName -VMSize Standard_D1 -Priority "Spot" -MaxPrice -1| `
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

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez également créer une machine virtuelle Spot à l’aide d’[Azure CLI](../linux/spot-cli.md) ou d’un [modèle](../linux/spot-template.md).

Si vous rencontrez une erreur, consultez [Codes d’erreur](../error-codes-spot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).