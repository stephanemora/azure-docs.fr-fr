---
title: Créer une machine virtuelle avec une adresse IP privée statique - Azure PowerShell
description: Découvrez comment créer une machine virtuelle avec une adresse IP privée statique à l’aide d’Azure PowerShell.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 10/01/2021
ms.custom: template-how-to
ms.openlocfilehash: a98fab6f07e2616e8f3732f28446c24e9051bf09
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129368360"
---
# <a name="create-a-virtual-machine-with-a-static-private-ip-address-using-azure-powershell"></a>Créer une machine virtuelle avec une adresse IP privée statique à l’aide de Azure PowerShell

Une adresse IP privée est automatiquement assignée à une machine virtuelle (VM) Azure, dans une plage que vous définissez. Cette plage est basée sur le sous-réseau dans lequel la machine virtuelle est déployée. Cette adresse est conservée jusqu’à ce que la machine virtuelle soit supprimée. Azure attribue dynamiquement l’adresse IP privée disponible suivante du sous-réseau dans lequel vous créez une machine virtuelle. Assignez une adresse IP statique à la machine virtuelle si vous souhaitez assigner une adresse IP spécifique du sous-réseau.

## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Azure PowerShell installé localement ou Azure Cloud Shell

Si vous choisissez d’installer et d’utiliser PowerShell en local, vous devez exécuter le module Azure PowerShell version 5.4.1 ou ultérieure pour les besoins de cet article. Exécutez `Get-Module -ListAvailable Az` pour rechercher la version installée. Si vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-Az-ps). Si vous exécutez PowerShell en local, vous devez également exécuter `Connect-AzAccount` pour créer une connexion avec Azure.

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Un groupe de ressources Azure est un conteneur logique dans lequel les ressources Azure sont déployées et gérées.

Avec la commande [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup), créez un groupe de ressources nommé **myResourceGroup** dans l’emplacement **eastus2**.

```azurepowershell-interactive
$rg =@{
    Name = 'myResourceGroup'
    Location = 'eastus2'
}
New-AzResourceGroup @rg

```

## <a name="create-a-virtual-machine"></a>Création d'une machine virtuelle

Créez une machine virtuelle avec [New-AzVM](/powershell/module/az.compute/new-azvm). 

La commande suivante crée une machine virtuelle Windows Server 2016. Quand vous y êtes invité, indiquez le nom d’utilisateur et le mot de passe à utiliser comme informations d’identification pour la machine virtuelle :

```azurepowershell-interactive
## Create virtual machine. ##
$vm = @{
    ResourceGroupName = 'myResourceGroup'
    Location = 'East US 2'
    Name = 'myVM'
    PublicIpAddressName = 'myPublicIP'
}
New-AzVM @vm
```

## <a name="change-private-ip-address-to-static"></a>Passer une adresse IP privée en statique

Dans cette section, vous allez faire passer l’adresse IP privée de **dynamique** à **statique** pour la machine virtuelle que vous avez créée précédemment. 

Utilisez [AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) pour placer la configuration du réseau virtuel dans une variable. Utilisez [AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) pour placer la configuration de sous-réseau dans une variable. Utilisez [AzNetworkInterface](/powershell/module/az.network/get-aznetworkinterface) pour obtenir la configuration de l’interface réseau et la placer dans une variable. Utilisez [Set-AzNetworkInterfaceIpConfig](/powershell/module/az.network/set-aznetworkinterfaceipconfig) pour définir la configuration de l’interface réseau. Enfin, utilisez [Set-AzNetworkInterface](/powershell/module/az.network/set-aznetworkinterface) pour définir la configuration de la machine virtuelle.

La commande suivante remplace l’adresse IP privée de la machine virtuelle par une adresse statique :

```azurepowershell-interactive
## Place virtual network configuration into a variable. ##
$net = @{
    Name = 'myVM'
    ResourceGroupName = 'myResourceGroup'
}
$vnet = Get-AzVirtualNetwork @net

## Place subnet configuration into a variable. ##
$sub = @{
    Name = 'myVM'
    VirtualNetwork = $vnet
}
$subnet = Get-AzVirtualNetworkSubnetConfig @sub

## Get name of network interface and place into a variable ##
$int1 = @{
    Name = 'myVM'
    ResourceGroupName = 'myResourceGroup'
}
$vm = Get-AzVM @int1

## Place network interface configuration into a variable. ##
$nic = Get-AzNetworkInterface -ResourceId $vm.NetworkProfile.NetworkInterfaces.Id

## Set interface configuration. ##
$config =@{
    Name = 'myVM'
    PrivateIpAddress = '192.168.1.4'
    Subnet = $subnet
}
$nic | Set-AzNetworkInterfaceIpConfig @config -Primary

## Save interface configuration. ##
$nic | Set-AzNetworkInterface
```

> [!WARNING]
> Même si vous avez la possibilité d’ajouter des paramètres d’adresse IP privée au système d’exploitation, nous vous déconseillons de le faire avant d’avoir lu [Add a private IP address to an operating system](virtual-network-network-interface-addresses.md#private) (Ajouter une adresse IP privée à un système d’exploitation).

## <a name="clean-up-resources"></a>Nettoyer les ressources

Quand vous n’avez plus besoin d’un groupe de ressources, vous pouvez utiliser [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) pour le supprimer ainsi que toutes les ressources qu’il contient :

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur les [adresses IP publiques](public-ip-addresses.md#public-ip-addresses) dans Azure.
- En savoir plus sur tous les [paramètres d’adresse IP publique](virtual-network-public-ip-address.md#create-a-public-ip-address).
- En savoir plus sur les [adresses IP privées](private-ip-addresses.md) et l’affectation d’une [adresse IP privée statique](virtual-network-network-interface-addresses.md#add-ip-addresses) à une machine virtuelle Azure.
- En savoir plus sur la création de machines virtuelles [Linux](../../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) et [Windows](../../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json).