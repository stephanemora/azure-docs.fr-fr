---
title: Configurer une préférence de routage pour une adresse IP publique – Azure PowerShell
titlesuffix: Azure Virtual Network
description: Apprenez à configurer une préférence de routage pour une adresse IP publique à l’aide d’Azure PowerShell.
services: virtual-network
documentationcenter: na
author: asudbring
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2021
ms.author: allensu
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 9b2e38ca43bfa3bc8dcd229d6538f8a7784f9882
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129368320"
---
# <a name="configure-routing-preference-for-a-public-ip-address-using-azure-powershell"></a>Configurer une préférence de routage pour une adresse IP publique à l’aide d’Azure PowerShell

Cet article montre comment configurer une préférence de routage via un réseau de fournisseur de services Internet (option **Internet**) pour une adresse IP publique à l’aide d’Azure PowerShell. Une fois l’adresse IP publique créée, vous pouvez l’associer aux ressources Azure suivantes pour les trafics entrant et sortant via Internet :

* Machine virtuelle
* Jeu de mise à l’échelle de machine virtuelle
* Azure Kubernetes Service (AKS)
* Équilibreur de charge accessible via Internet
* Application Gateway
* Pare-feu Azure

Par défaut, la préférence de routage pour l’adresse IP publique est définie sur le réseau Microsoft mondial pour tous les services Azure, et peut être associée à n’importe lequel de ceux-ci.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) maintenant.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]
Si vous choisissez d’installer et d’utiliser PowerShell en local, vous devez exécuter le module Azure PowerShell version 6.9.0 ou ultérieure pour les besoins de cet article. Exécutez `Get-Module -ListAvailable Az` pour rechercher la version installée. Si vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-Az-ps). Si vous exécutez PowerShell en local, vous devez également lancer `Connect-AzAccount` pour créer une connexion avec Azure.

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Créez un groupe de ressources avec [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup). Cet exemple crée un groupe de ressources nommé *myResourceGroup* dans la région *eastus* :

```azurepowershell
$rg = New-AzResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-a-public-ip-with-internet-routing-preference"></a>Créer une adresse IP publique avec une préférence de routage Internet

La commande suivante crée une adresse IP publique avec un type de préférence de routage *Internet* dans la région Azure *USA Est* :

```azurepowershell
$iptagtype="RoutingPreference"
$tagName = "Internet"
$ipTag = New-AzPublicIpTag -IpTagType $iptagtype -Tag $tagName 
# attach the tag
$publicIp = New-AzPublicIpAddress  `
-Name "MyPublicIP" `
-ResourceGroupName $rg.ResourceGroupName `
-Location $rg.Location `
-IpTag $ipTag `
-AllocationMethod Static `
-Sku Standard `
-IpAddressVersion IPv4
```

Vous pouvez associer l’adresse IP publique créée ci-dessus à une machine virtuelle [Windows](../../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [Linux](../../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Utilisez la section CLI sur la page du tutoriel : [Associez une adresse IP publique à une machine virtuelle](../../virtual-network/associate-public-ip-address-vm.md#azure-cli) pour associer l’adresse IP publique à votre machine virtuelle. Vous pouvez associer l’adresse IP publique créée ci-dessus avec un [équilibreur de charge Azure](../../load-balancer/load-balancer-overview.md) en l’assignant à la configuration **frontale** de l’équilibreur de charge. L’adresse IP publique sert d’adresse IP virtuelle (VIP) à charge équilibrée.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Vous pouvez utiliser la commande [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) pour supprimer le groupe de ressources, la machine virtuelle et toutes les ressources associées si vous n’en avez plus besoin.

```azurepowershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur la [préférence de routage dans les adresses IP publiques](routing-preference-overview.md).
- [Configurer la préférence de routage pour une machine virtuelle à l’aide d’Azure PowerShell](../../virtual-network/configure-routing-preference-virtual-machine-powershell.md).
