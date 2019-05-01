---
title: Créer une machine virtuelle avec une adresse IP privée statique - Azure PowerShell | Microsoft Docs
description: Découvrez comment créer une machine virtuelle avec une adresse IP privée à l’aide de PowerShell.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: d5f18929-15e3-40a2-9ee3-8188bc248ed8
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/07/2019
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: 9115386b0543e1ac840aec29fc7f57e7c98c03bb
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64685347"
---
# <a name="create-a-virtual-machine-with-a-static-private-ip-address-using-powershell"></a>Créer une machine virtuelle avec une adresse IP privée statique à l’aide de PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Vous pouvez créer une machine virtuelle (VM) avec une adresse IP privée statique. Affecter une adresse IP privée statique, plutôt qu’une adresse dynamique, si vous souhaitez sélectionner quelle adresse à partir d’un sous-réseau est affecté à une machine virtuelle. En savoir plus sur [des adresses IP privées statiques](virtual-network-ip-addresses-overview-arm.md#allocation-method). Pour modifier une adresse IP privée affectée à une machine virtuelle existante à partir de dynamique à statique, ou pour travailler avec des adresses IP publiques, consultez [ajouter, modifier ou supprimer des adresses IP](virtual-network-network-interface-addresses.md).

## <a name="create-a-virtual-machine"></a>Création d'une machine virtuelle

Vous pouvez effectuer les étapes suivantes à partir de votre ordinateur local ou à l’aide d’Azure Cloud Shell. Pour pouvoir utiliser votre ordinateur local, vous devez avoir installé [Azure PowerShell](/powershell/azure/install-az-ps?toc=%2fazure%2fvirtual-network%2ftoc.json). Pour utiliser Azure Cloud Shell, sélectionnez **Essayer** dans l’angle supérieur droit de l’une des zones de commande suivantes. Cloud Shell vous connecte à Azure.

1. Si vous utilisez Cloud Shell, passez à l’étape 2. Ouvrez une session de commande et connectez-vous à Azure avec `Connect-AzAccount`.
2. Créez un groupe de ressources avec la commande [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). L’exemple suivant crée un groupe de ressources dans la région Azure USA Est :

   ```azurepowershell-interactive
   $RgName = "myResourceGroup"
   $Location = "eastus"
   New-AzResourceGroup -Name $RgName -Location $Location
   ```

3. Créer une configuration de sous-réseau et le réseau virtuel avec le [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) et [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) commandes :

   ```azurepowershell-interactive
   # Create a subnet configuration
   $SubnetConfig = New-AzVirtualNetworkSubnetConfig `
   -Name MySubnet `
   -AddressPrefix 10.0.0.0/24

   # Create a virtual network
   $VNet = New-AzVirtualNetwork `
   -ResourceGroupName $RgName `
   -Location $Location `
   -Name MyVNet `
   -AddressPrefix 10.0.0.0/16 `
   -Subnet $subnetConfig

   # Get the subnet object for use in a later step.
   $Subnet = Get-AzVirtualNetworkSubnetConfig -Name $SubnetConfig.Name -VirtualNetwork $VNet
   ```

4. Créer une interface réseau dans le réseau virtuel et attribuer une adresse IP privée à partir du sous-réseau à l’interface réseau avec la [New-AzNetworkInterfaceIpConfig](/powershell/module/Az.Network/New-AzNetworkInterfaceIpConfig) et [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) commandes :

   ```azurepowershell-interactive
   $IpConfigName1 = "IPConfig-1"
   $IpConfig1     = New-AzNetworkInterfaceIpConfig `
     -Name $IpConfigName1 `
     -Subnet $Subnet `
     -PrivateIpAddress 10.0.0.4 `
     -Primary

   $NIC = New-AzNetworkInterface `
     -Name MyNIC `
     -ResourceGroupName $RgName `
     -Location $Location `
     -IpConfiguration $IpConfig1
   ```

5. Créer une configuration de machine virtuelle avec [New-AzVMConfig](/powershell/module/Az.Compute/New-AzVMConfig), puis créez la machine virtuelle avec [New-AzVM](/powershell/module/az.Compute/New-azVM). Lorsque vous y êtes invité, fournissez un nom d’utilisateur et le mot de passe à utiliser comme informations d’identification de la connexion pour la machine virtuelle :

   ```azurepowershell-interactive
   $VirtualMachine = New-AzVMConfig -VMName MyVM -VMSize "Standard_DS3"
   $VirtualMachine = Set-AzVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName MyServerVM -ProvisionVMAgent -EnableAutoUpdate
   $VirtualMachine = Add-AzVMNetworkInterface -VM $VirtualMachine -Id $NIC.Id
   $VirtualMachine = Set-AzVMSourceImage -VM $VirtualMachine -PublisherName 'MicrosoftWindowsServer' -Offer 'WindowsServer' -Skus '2012-R2-Datacenter' -Version latest
   New-AzVM -ResourceGroupName $RgName -Location $Location -VM $VirtualMachine -Verbose
   ```

> [!WARNING]
> Si vous pouvez ajouter des paramètres d’adresse IP privées pour le système d’exploitation, nous vous recommandons de ne pas le faire jusqu'à ce qu’après avoir lu [ajouter une adresse IP privée à un système d’exploitation](virtual-network-network-interface-addresses.md#private).
> 
> 
> <a name = "change-the-allocation-method-for-a-private-ip-address-assigned-to-a-network-interface"></a>
> 
> [!IMPORTANT]
> Pour accéder à la machine virtuelle à partir d’internet, vous devez attribuer une adresse IP publique à la machine virtuelle. Vous pouvez également modifier une attribution d’adresse IP privée dynamique à une affectation statique. Pour plus d’informations, consultez [ajouter ou modifier les adresses IP](virtual-network-network-interface-addresses.md). En outre, il est recommandé de limiter le trafic réseau à votre machine virtuelle en associant un groupe de sécurité réseau à l’interface réseau, le sous-réseau que vous avez créé l’interface réseau ou les deux. Pour plus d’informations, consultez [gérer les groupes de sécurité réseau](manage-network-security-group.md).

## <a name="clean-up-resources"></a>Supprimer des ressources

Quand vous n’avez plus besoin d’un groupe de ressources, vous pouvez utiliser [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) pour le supprimer ainsi que toutes les ressources qu’il contient :

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur [des adresses IP privées](virtual-network-ip-addresses-overview-arm.md#private-ip-addresses) et en assignant un [adresse IP privée statique](virtual-network-network-interface-addresses.md#add-ip-addresses) à une machine virtuelle Azure.
- En savoir plus sur la création de [Linux](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) et [Windows](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) machines virtuelles.
