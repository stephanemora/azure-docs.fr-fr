---
title: Créer une machine virtuelle avec une adresse IP publique statique - Azure PowerShell
titlesuffix: Azure Virtual Network
description: Créez une machine virtuelle avec une adresse IP publique statique à l’aide d’Azure PowerShell. Les adresses IP publiques statiques sont des adresses qui ne changent jamais.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 10/01/2021
ms.custom: template-how-to
ms.openlocfilehash: 60c9d4827a83475b56abcc769bb1fa7f68467a24
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129368487"
---
# <a name="create-a-virtual-machine-with-a-static-public-ip-address-using-azure-powershell"></a>Créer une machine virtuelle avec une adresse IP publique statique à l’aide d’Azure PowerShell

Dans cet article, vous allez créer une machine virtuelle avec une adresse IP publique statique. Une adresse IP publique vous permet de communiquer avec une machine virtuelle à partir d’Internet. Attribuez une adresse IP publique statique, plutôt qu’une adresse dynamique, pour être certain que l’adresse ne changera jamais. 

Les adresses IP publiques ont un [coût nominal](https://azure.microsoft.com/pricing/details/ip-addresses). Le nombre d’adresses IP publiques que vous pouvez utiliser dans un abonnement est [limité](../../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

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

## <a name="create-a-public-ip-address"></a>Créer une adresse IP publique

Utilisez [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) pour créer une adresse IPv4 publique standard.

La commande suivante crée une adresse IP publique redondante interzone nommée **myPublicIP** dans **myResourceGroup**.

```azurepowershell-interactive
## Create IP. ##
$ip = @{
    Name = 'myPublicIP'
    ResourceGroupName = 'myResourceGroup'
    Location = 'eastus2'
    Sku = 'Standard'
    AllocationMethod = 'Static'
    IpAddressVersion = 'IPv4'
    Zone = 1,2,3   
}
New-AzPublicIpAddress @ip
```
## <a name="create-a-virtual-machine"></a>Création d'une machine virtuelle

Créez une machine virtuelle avec [New-AzVM](/powershell/module/az.Compute/new-azvm). 

La commande suivante crée une machine virtuelle Windows Server. Entrez le nom de l’adresse IP publique créée précédemment dans le paramètre **`-PublicIPAddressName`** . Quand vous y êtes invité, indiquez le nom d’utilisateur et le mot de passe à utiliser comme informations d’identification pour la machine virtuelle :

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

Pour plus d’informations sur les références SKU d’adresses IP publiques, consultez [Références SKU d’adresse IP publique](public-ip-addresses.md#sku). Une machine virtuelle peut être ajoutée au pool principal d’un équilibreur Azure Load Balancer. La référence SKU de l’adresse IP publique doit correspondre à la référence SKU de l’adresse IP publique d’un équilibreur de charge. Pour plus d’informations, consultez la page [Équilibrage de charge Azure](../../load-balancer/skus.md).

Examinez l’adresse IP publique qui a été affectée et vérifiez qu’elle a été créée en tant qu’adresse statique, à l’aide de [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) :

```azurepowershell-interactive
## Retrieve public IP address settings. ##
$ip = @{
    Name = 'myPublicIP'
    ResourceGroupName = 'myResourceGroup'
}
Get-AzPublicIpAddress @ip | Select "IpAddress","PublicIpAllocationMethod" | Format-Table

```

> [!WARNING]
> Ne modifiez pas les paramètres d’adresse IP dans le système d’exploitation de la machine virtuelle. Le système d’exploitation ne reconnaît pas les adresses IP publiques Azure. Même si vous avez la possibilité d’ajouter des paramètres d’adresse IP privée au système d’exploitation, nous vous déconseillons de le faire, à moins que cela soit nécessaire et pas avant d’avoir lu [Ajouter une adresse IP privée à un système d’exploitation](virtual-network-network-interface-addresses.md#private).

[!INCLUDE [ephemeral-ip-note.md](../../../includes/ephemeral-ip-note.md)]

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