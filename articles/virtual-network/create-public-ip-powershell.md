---
title: Créer une adresse IP publique - Azure PowerShell
description: Découvrez comment créer une adresse IP publique à l’aide d’Azure PowerShell
services: virtual-network
documentationcenter: na
author: blehr
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/28/2020
ms.author: blehr
ms.openlocfilehash: f81e76e32bc0a50b945c54cdfcac0575540484bc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "102502431"
---
# <a name="quickstart-create-a-public-ip-address-using-azure-powershell"></a>Démarrage rapide : Créer une adresse IP publique à l’aide d’Azure PowerShell

Cet article explique comment créer une ressource d’adresse IP publique à l’aide d’Azure PowerShell. Pour savoir quelles ressources peuvent être associées, connaître la différence entre les références SKU De base et Standard et obtenir d’autres informations connexes, consultez [Adresses IP publiques](./public-ip-addresses.md).  Cet exemple traite seulement des adresses IPv4. Pour plus d’informations sur les adresses IPv6, consultez [IPv6 pour les réseaux virtuels Azure](./ipv6-overview.md).

## <a name="prerequisites"></a>Prérequis

- Azure PowerShell installé localement ou Azure Cloud Shell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser PowerShell en local, vous devez exécuter le module Azure PowerShell version 5.4.1 ou ultérieure pour les besoins de cet article. Exécutez `Get-Module -ListAvailable Az` pour rechercher la version installée. Si vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-Az-ps). Si vous exécutez PowerShell en local, vous devez également exécuter `Connect-AzAccount` pour créer une connexion avec Azure.

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Un groupe de ressources Azure est un conteneur logique dans lequel les ressources Azure sont déployées et gérées.

Avec la commande [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup), créez un groupe de ressources nommé **myResourceGroup** dans l’emplacement **eastus2**.

```azurepowershell-interactive
## Variables for the command ##
$rg = 'myResourceGroup'
$loc = 'eastus2'

New-AzResourceGroup -Name $rg -Location $loc
```
## <a name="create-public-ip"></a>Créer une adresse IP publique

---
# <a name="standard-sku---using-zones"></a>[**Référence SKU Standard - Avec des zones**](#tab/option-create-public-ip-standard-zones)

>[!NOTE]
>La commande suivante fonctionne pour le module Az.Network version 4.5.0 ou ultérieure.  Pour plus d’informations sur les modules PowerShell en cours d’utilisation, reportez-vous à la [documentation de PowerShellGet](/powershell/module/powershellget/).

Utilisez la commande [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) pour créer une adresse IP publique redondante interzone standard nommée **myStandardZRPublicIP** dans **myResourceGroup**.

```azurepowershell-interactive
## Variables for the command ##
$rg = 'myResourceGroup'
$loc = 'eastus2'
$pubIP = 'myStandardZRPublicIP'
$sku = 'Standard'
$alloc = 'Static'
$zone = 1,2,3

New-AzPublicIpAddress -ResourceGroupName $rg -Name $pubIP -Location $loc -AllocationMethod $alloc -SKU $sku -zone $zone
```
> [!IMPORTANT]
> Pour les modules Az.Network antérieurs à la version 4.5.0, exécutez la commande ci-dessus sans spécifier de paramètre de zone pour créer une adresse IP redondante interzone. 
>

Pour créer une adresse IP publique zonale standard dans la zone 2 nommée **myStandardZonalPublicIP** dans **myResourceGroup**, exécutez la commande suivante :

```azurepowershell-interactive
## Variables for the command ##
$rg = 'myResourceGroup'
$loc = 'eastus2'
$pubIP = 'myStandardZonalPublicIP'
$sku = 'Standard'
$alloc = 'Static'
$zone = 2

New-AzPublicIpAddress -ResourceGroupName $rg -Name $pubIP -Location $loc -AllocationMethod $alloc -SKU $sku -zone $zone
```

Notez que les sélections des options de zone ci-dessus sont valides uniquement dans des régions avec des [zones de disponibilité](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones).

# <a name="standard-sku---no-zones"></a>[**Référence SKU Standard - Sans zones**](#tab/option-create-public-ip-standard)

>[!NOTE]
>La commande suivante fonctionne pour le module Az.Network version 4.5.0 ou ultérieure.  Pour plus d’informations sur les modules PowerShell en cours d’utilisation, reportez-vous à la [documentation de PowerShellGet](/powershell/module/powershellget/).

Utilisez la commande [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) pour créer une adresse IP publique standard en tant que ressource non zonale nommée **myStandardPublicIP** dans **myResourceGroup**.

```azurepowershell-interactive
## Variables for the command ##
$rg = 'myResourceGroup'
$loc = 'eastus2'
$pubIP = 'myStandardPublicIP'
$sku = 'Standard'
$alloc = 'Static'

New-AzPublicIpAddress -ResourceGroupName $rg -Name $pubIP -Location $loc -AllocationMethod $alloc -SKU $sku
```

Cette sélection est valide dans toutes les régions. Il s’agit de la sélection par défaut pour les adresses IP publiques standard dans les régions sans [zones de disponibilité](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones).

# <a name="basic-sku"></a>[**Référence De base**](#tab/option-create-public-ip-basic)

Utilisez la commande [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) pour créer une adresse IP publique statique de base nommée **myBasicPublicIP** dans **myResourceGroup**.  Les adresses IP publiques de base n’intègrent pas le concept de zones de disponibilité.

```azurepowershell-interactive
## Variables for the command ##
$rg = 'myResourceGroup'
$loc = 'eastus2'
$pubIP = 'myBasicPublicIP'
$sku = 'Basic'
$alloc = 'Static'

New-AzPublicIpAddress -ResourceGroupName $rg -Name $pubIP -Location $loc -AllocationMethod $alloc -SKU $sku
```
S’il est acceptable que l’adresse IP change au fil du temps, l’attribution **dynamique** d’adresse IP peut être sélectionnée en changeant la méthode d’allocation (AllocationMethod) par « Dynamique ».

---

## <a name="additional-information"></a>Informations supplémentaires 

Pour plus d’informations sur chacune des variables listées ci-dessus, consultez [Gérer les adresses IP publiques](./virtual-network-public-ip-address.md#create-a-public-ip-address).

## <a name="next-steps"></a>Étapes suivantes
- Associer une [adresse IP publique à une machine virtuelle](./associate-public-ip-address-vm.md#azure-portal)
- En savoir plus sur les [adresses IP publiques](./public-ip-addresses.md#public-ip-addresses) dans Azure.
- En savoir plus sur tous les [paramètres d’adresse IP publique](virtual-network-public-ip-address.md#create-a-public-ip-address).