---
title: 'Démarrage rapide : Créer un préfixe d’adresse IP publique – PowerShell'
titlesuffix: Azure Virtual Network
description: Découvrez comment créer un préfixe d’adresse IP publique statique à l’aide de PowerShell.
services: virtual-network
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: quickstart
ms.date: 10/01/2021
ms.author: allensu
ms.openlocfilehash: 24c046aa7aab0932065f87ba3179185e9c5ed2fc
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129369155"
---
# <a name="quickstart-create-a-public-ip-address-prefix-using-powershell"></a>Démarrage rapide : Créer un préfixe d’adresse IP publique en utilisant PowerShell

Découvrez les préfixes d’adresse IP publique et comment en créer, changer et supprimer un. Un préfixe d’adresse IP publique est une plage contiguë d’adresses IP publiques à référence SKU standard. 

Lorsque vous créez une ressource d’adresse IP publique, vous pouvez attribuer une adresse IP publique statique à partir du préfixe et l’associer à des machines virtuelles, à des équilibreurs de charge ou à d’autres ressources. Pour plus d’informations, consultez [Vue d’ensemble du préfixe d’adresse IP publique](public-ip-address-prefix.md).

## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Azure PowerShell installé localement ou Azure Cloud Shell

Si vous choisissez d’installer et d’utiliser PowerShell en local, vous devez exécuter le module Azure PowerShell version 5.4.1 ou ultérieure pour les besoins de cet article. Exécutez `Get-Module -ListAvailable Az` pour rechercher la version installée. Si vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-Az-ps). Si vous exécutez PowerShell en local, vous devez également exécuter `Connect-AzAccount` pour créer une connexion avec Azure.

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Un groupe de ressources Azure est un conteneur logique dans lequel les ressources Azure sont déployées et gérées.

À l’aide de [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup), créez un groupe de ressources nommé **QuickStartCreateIPPrefix-rg** dans l’emplacement **eastus2**.

```azurepowershell-interactive
$rg =@{
    Name = 'QuickStartCreateIPPrefix-rg'
    Location = 'eastus2'
}
New-AzResourceGroup @rg
```

## <a name="create-a-public-ip-address-prefix"></a>Créer un préfixe d’adresse IP publique

Dans cette section, vous allez créer un préfixe d’IP publique non zonale, zonale et redondant interzone à l’aide d’Azure PowerShell. 

Les préfixes des exemples sont les suivants :

* **IPv4** - /28 (16 adresses)

* **IPv6** - /124 (16 adresses)

Pour plus d’informations sur les tailles de préfixe disponibles, consultez [Tailles de préfixe](public-ip-address-prefix.md#prefix-sizes).

Créez un préfixe d’IP publique à l’aide de [New-AzPublicIpPrefix](/powershell/module/az.network/new-azpublicipprefix), nommé **myPublicIpPrefix**, dans l’emplacement **eastus2**.

## <a name="ipv4"></a>IPv4

# <a name="zone-redundant-ipv4-prefix"></a>[**Préfixe IPv4 redondant interzone**](#tab/ipv4-zone-redundant)

Pour créer un préfixe d’IP publique IPv4, entrez **IPv4** dans le paramètre **`-IpAddressVersion`** . Pour créer un préfixe IPv4 redondant interzone, indiquez **1, 2, 3** dans le paramètre **`-Zone`** .

```azurepowershell-interactive
$ipv4 =@{
    Name = 'myPublicIpPrefix'
    ResourceGroupName = 'QuickStartCreateIPPrefix-rg'
    Location = 'eastus2'
    PrefixLength = '28'
    IpAddressVersion = 'IPv4'
    Zone = 1,2,3
}
New-AzPublicIpPrefix @ipv4
```

# <a name="zonal-ipv4-prefix"></a>[**Préfixe IPv4 zonal**](#tab/ipv4-zonal)

Pour créer un préfixe d’IP publique IPv4, entrez **IPv4** dans le paramètre **`-IpAddressVersion`** . Spécifiez **2** dans le paramètre **`-Zone`** pour créer un préfixe d’IP zonale dans la zone 2.

```azurepowershell-interactive
$ipv4 =@{
    Name = 'myPublicIpPrefix-zonal'
    ResourceGroupName = 'QuickStartCreateIPPrefix-rg'
    Location = 'eastus2'
    PrefixLength = '28'
    IpAddressVersion = 'IPv4'
    Zone = 2
}
New-AzPublicIpPrefix @ipv4
```

>[!NOTE]
>Les sélections des options de zone ci-dessus sont valides uniquement dans les régions pourvues de [zones de disponibilité](../../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones).

# <a name="non-zonal-ipv4-prefix"></a>[**Préfixe IPv4 zonal**](#tab/ipv4-non-zonal)

Pour créer un préfixe d’IP publique IPv4, entrez **IPv4** dans le paramètre **`-IpAddressVersion`** . Supprimez le paramètre **`-Zone`** pour créer un préfixe d’IP non zonale.

```azurepowershell-interactive
$ipv4 =@{
    Name = 'myPublicIpPrefix-nozone'
    ResourceGroupName = 'QuickStartCreateIPPrefix-rg'
    Location = 'eastus2'
    PrefixLength = '28'
    IpAddressVersion = 'IPv4'
}
New-AzPublicIpPrefix @ipv4
```

La suppression du paramètre **`-Zone`** dans la commande est valide dans toutes les régions.  

La suppression du paramètre **`-Zone`** constitue la sélection par défaut des adresses IP publiques standard dans les régions sans [Zone de disponibilité](../../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones).

---

## <a name="ipv6"></a>IPv6

# <a name="zone-redundant-ipv6-prefix"></a>[**Préfixe IPv6 redondant interzone**](#tab/ipv6-zone-redundant)

Pour créer un préfixe d’IP publique IPv6, entrez **IPv6** dans le paramètre **`-IpAddressVersion`** . Pour créer un préfixe IPv6 redondant interzone, indiquez **1, 2, 3** dans le paramètre **`-Zone`** .

```azurepowershell-interactive
$ipv6 =@{
    Name = 'myPublicIpPrefix'
    ResourceGroupName = 'QuickStartCreateIPPrefix-rg'
    Location = 'eastus2'
    PrefixLength = '124'
    IpAddressVersion = 'IPv6'
    Zone = 1,2,3
}
New-AzPublicIpPrefix @ipv6
```

# <a name="zonal-ipv6-prefix"></a>[**Préfixe IPv6 zonal**](#tab/ipv6-zonal)

Pour créer un préfixe d’IP publique IPv6, entrez **IPv6** dans le paramètre **`-IpAddressVersion`** . Spécifiez **2** dans le paramètre **`-Zone`** pour créer un préfixe d’IP zonale dans la zone 2.

```azurepowershell-interactive
$ipv6 =@{
    Name = 'myPublicIpPrefix-zonal'
    ResourceGroupName = 'QuickStartCreateIPPrefix-rg'
    Location = 'eastus2'
    PrefixLength = '124'
    IpAddressVersion = 'IPv6'
    Zone = 2
}
New-AzPublicIpPrefix @ipv6
```

>[!NOTE]
>Les sélections des options de zone ci-dessus sont valides uniquement dans les régions pourvues de [zones de disponibilité](../../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones).

# <a name="non-zonal-ipv6-prefix"></a>[**Préfixe IPv6 non zonal**](#tab/ipv6-non-zonal)

Pour créer un préfixe d’IP publique IPv6, entrez **IPv6** dans le paramètre **`-IpAddressVersion`** . Supprimez le paramètre **`-Zone`** pour créer un préfixe d’IP non zonale.

```azurepowershell-interactive
$ipv6 =@{
    Name = 'myPublicIpPrefix-nozone'
    ResourceGroupName = 'QuickStartCreateIPPrefix-rg'
    Location = 'eastus2'
    PrefixLength = '124'
    IpAddressVersion = 'IPv6'
}
New-AzPublicIpPrefix @ipv6
```

La suppression du paramètre **`-Zone`** dans la commande est valide dans toutes les régions.  

La suppression du paramètre **`-Zone`** constitue la sélection par défaut des adresses IP publiques standard dans les régions sans [Zone de disponibilité](../../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones).

---

## <a name="create-a-static-public-ip-address-from-a-prefix"></a>Créer une adresse IP publique statique à partir d’un préfixe

Une fois que vous avez créé un préfixe, vous devez créer des adresses IP statiques à partir du préfixe. Dans cette section, vous allez créer une adresse IP statique à partir du préfixe que vous avez créé précédemment.

Créez une adresse IP publique à l’aide de [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) dans le préfixe **myPublicIpPrefix**.

# <a name="ipv4-address"></a>[**Adresse IPv4**](#tab/ipv4-address)

Pour créer une adresse IP publique IPv4, entrez **IPv4** dans le paramètre **`-IpAddressVersion`** .

```azurepowershell-interactive
$pf =@{
    Name = 'myPublicIpPrefix'
    ResourceGroupName = 'QuickStartCreateIPPrefix-rg'
}
$prefix = Get-AzPublicIpPrefix @pf

$ipv4 =@{
    Name = 'myPublicIpAddress'
    ResourceGroupName = 'QuickStartCreateIPPrefix-rg'
    Location = 'eastus2'
    Sku = 'Standard'
    Tier = 'Regional'
    AllocationMethod = 'Static'
    IpAddressVersion = 'IPv4'
    PublicIpPrefix = $prefix
}
New-AzPublicIpAddress @ipv4
```

# <a name="ipv6-address"></a>[**Adresse IPv6**](#tab/ipv6-address)

Pour créer une adresse IP publique IPv6, entrez **IPv6** dans le paramètre **`-IpAddressVersion`** .
```azurepowershell-interactive
$pf =@{
    Name = 'myPublicIpPrefix'
    ResourceGroupName = 'QuickStartCreateIPPrefix-rg'
}
$prefix = Get-AzPublicIpPrefix @pf

$ipv6 =@{
    Name = 'myPublicIpAddress'
    ResourceGroupName = 'QuickStartCreateIPPrefix-rg'
    Location = 'eastus2'
    Sku = 'Standard'
    Tier = 'Regional'
    AllocationMethod = 'Static'
    IpAddressVersion = 'IPv6'
    PublicIpPrefix = $prefix
}
New-AzPublicIpAddress @ipv6
```

---

>[!NOTE]
>Seules les adresses IP publiques créées avec la référence SKU standard peuvent être attribuées à partir de la plage du préfixe. Pour en savoir plus sur les références (SKU) d’adresses IP publiques, consultez [Adresse IP publique](public-ip-addresses.md#public-ip-addresses).

## <a name="delete-a-prefix"></a>Supprimer un préfixe

Dans cette section, vous allez apprendre à supprimer un préfixe.

Pour supprimer un préfixe d’IP publique, utilisez [Remove-AzPublicIpAddress](/powershell/module/az.network/remove-azpublicipprefix).

```azurepowershell-interactive
$pr =@{
    Name = 'myPublicIpPrefix'
    ResourceGroupName = 'QuickStartCreateIPPrefix-rg'
}
Remove-AzPublicIpPrefix @pr
```

>[!NOTE]
>Si les adresses dans le préfixe sont associées à des ressources d’adresse IP publique, vous devez d’abord supprimer les ressources d’adresse IP publique. Consultez [Supprimer une adresse IP publique](virtual-network-public-ip-address.md#view-modify-settings-for-or-delete-a-public-ip-address).

## <a name="clean-up-resources"></a>Nettoyer les ressources

Dans cet article, vous avez créé un préfixe d’adresse IP publique et une adresse IP publique à partir de ce préfixe. 

Lorsque vous en avez terminé avec le préfixe d’adresse IP publique, supprimez le groupe de ressources et toutes les ressources qu’il contient :

```azurepowershell-interactive
Remove-AzResourceGroup -ResourceGroupName 'QuickStartCreateIPPrefix-rg'
```

## <a name="next-steps"></a>Étapes suivantes

Passez à l’article suivant pour apprendre à créer un préfixe d’adresse IP publique à l’aide de l’interface Azure CLI :
> [!div class="nextstepaction"]
> [Créer une adresse IP publique à l’aide de PowerShell](create-public-ip-powershell.md)
