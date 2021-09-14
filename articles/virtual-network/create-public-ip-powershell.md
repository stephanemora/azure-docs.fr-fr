---
title: Créer une adresse IP publique - Azure PowerShell
titleSuffix: Azure Virtual Network
description: Découvrez comment créer une adresse IP publique à l’aide d’Azure PowerShell
services: virtual-network
author: asudbring
ms.service: virtual-network
ms.topic: how-to
ms.date: 05/03/2021
ms.author: allensu
ms.openlocfilehash: dc9a6aa398a8cab2927bc8890ceaeb4d0026714c
ms.sourcegitcommit: beff1803eeb28b60482560eee8967122653bc19c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/07/2021
ms.locfileid: "113434804"
---
# <a name="create-a-public-ip-address-using-azure-powershell"></a>Créer une adresse IP publique à l’aide d’Azure PowerShell

Cet article explique comment créer une ressource d’adresse IP publique à l’aide d’Azure PowerShell. 

Pour plus d’informations sur les ressources qui prennent en charge des adresses IP publiques, consultez [Adresses IP publiques](./public-ip-addresses.md).
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
## <a name="create-standard-sku-public-ip-with-zones"></a>Créer une IP publique de référence SKU standard avec des zones

Dans cette section, vous allez créer une IP publique avec des zones. Les adresses IP publiques peuvent être redondantes interzones ou être zonales.

### <a name="zone-redundant"></a>Redondant interzone

>[!NOTE]
>La commande suivante fonctionne pour le module Az.Network version 4.5.0 ou ultérieure.  Pour plus d’informations sur les modules PowerShell en cours d’utilisation, reportez-vous à la [documentation de PowerShellGet](/powershell/module/powershellget/).

Utilisez la commande [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) pour créer une adresse IPv4 publique redondante interzone standard, nommée **myStandardZRPublicIP**, dans **myResourceGroup**. 

Pour créer une adresse IPv6, changez la valeur du paramètre **version** pour **IPv6**.

```azurepowershell-interactive
$ip = @{
    Name = 'myStandardZRPublicIP'
    ResourceGroupName = 'myResourceGroup'
    Location = 'eastus2'
    Sku = 'Standard'
    AllocationMethod = 'Static'
    IpAddressVersion = 'IPv4'
    Zone = 1,2,3
}
New-AzPublicIpAddress @ip
```
> [!IMPORTANT]
> Pour les modules Az.Network antérieurs à la version 4.5.0, exécutez la commande ci-dessus sans spécifier de paramètre de zone pour créer une adresse IP redondante interzone. 
>

### <a name="zonal"></a>Zonal

Pour créer une adresse IPv4 publique zonale standard à l’intérieur de la zone 2, nommée **myStandardZonalPublicIP**, dans **myResourceGroup**, exécutez la commande suivante.

Pour créer une adresse IPv6, changez la valeur du paramètre **version** pour **IPv6**.

```azurepowershell-interactive
$ip = @{
    Name = 'myStandardZonalPublicIP'
    ResourceGroupName = 'myResourceGroup'
    Location = 'eastus2'
    Sku = 'Standard'
    AllocationMethod = 'Static'
    IpAddressVersion = 'IPv4'
    Zone = 2
}
New-AzPublicIpAddress @ip
```
>[!NOTE]
>Les sélections des options de zone ci-dessus sont valides uniquement dans les régions pourvues de [zones de disponibilité](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones).

## <a name="create-standard-public-ip-without-zones"></a>Créer une IP publique standard sans zone

Dans cette section, vous allez créer une adresse IP non zonale.  

>[!NOTE]
>La commande suivante fonctionne pour le module Az.Network version 4.5.0 ou ultérieure.  Pour plus d’informations sur les modules PowerShell en cours d’utilisation, reportez-vous à la [documentation de PowerShellGet](/powershell/module/powershellget/).

Utilisez la commande [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) pour créer une adresse IPv4 publique standard en tant que ressource non zonale, nommée **myStandardPublicIP**, dans **myResourceGroup**. 

Pour créer une adresse IPv6, changez la valeur du paramètre **version** pour **IPv6**.

```azurepowershell-interactive
$ip = @{
    Name = 'myStandardPublicIP'
    ResourceGroupName = 'myResourceGroup'
    Location = 'eastus2'
    Sku = 'Standard'
    AllocationMethod = 'Static'
    IpAddressVersion = 'IPv4'
}
New-AzPublicIpAddress @ip
```
La suppression du paramètre **zone** dans la commande est valide dans toutes les régions.  

La suppression du paramètre **zone** constitue la sélection par défaut pour les adresses IP publiques standard dans les régions sans [zone de disponibilité](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones).

## <a name="create-a-basic-public-ip"></a>Créer une IP publique de base

Dans cette section, vous allez créer une IP de base. Les adresses IP publiques de base ne prennent pas en charge les zones de disponibilité.

Utilisez la commande [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) pour créer une adresse IPv4 publique statique de base, nommée **myBasicPublicIP**, dans **myResourceGroup**.  

Pour créer une adresse IPv6, changez la valeur du paramètre **version** pour **IPv6**. 

```azurepowershell-interactive
$ip = @{
    Name = 'myStandardPublicIP'
    ResourceGroupName = 'myResourceGroup'
    Location = 'eastus2'
    Sku = 'Basic'
    AllocationMethod = 'Static'
    IpAddressVersion = 'IPv4'
}
New-AzPublicIpAddress @ip
```
S’il est acceptable que l’adresse IP change au fil du temps, l’affectation **dynamique** d’adresses IP peut être sélectionnée en changeant la méthode d’allocation (AllocationMethod) pour **Dynamique**. 

>[!NOTE]
> Une adresse IPv6 de base doit toujours être « dynamique ».

## <a name="routing-preference-and-tier"></a>Préférence de routage et niveau

Les adresses IPv4 publiques statiques de référence SKU standard prennent en charge la Préférence de routage ou la fonctionnalité Niveau global.

### <a name="routing-preference"></a>Préférence de routage

Par défaut, la préférence de routage pour les adresses IP publiques est définie sur le « Réseau Microsoft », qui remet le trafic à l’utilisateur via le réseau étendu mondial de Microsoft.  

La sélection de **Internet** réduit les déplacements sur le réseau de Microsoft, et utilise plutôt le réseau du fournisseur de services Internet pour acheminer le trafic à un coût optimisé.  

Pour plus d’informations sur la préférence de routage, consultez la section [Qu’est-ce qu’une préférence de routage (préversion) ?](./routing-preference-overview.md).

La commande crée une nouvelle adresse IPv4 publique redondante interzone standard avec une préférence de routage de type **Internet** :

```azurepowershell-interactive
## Create IP tag for Internet and Routing Preference. ##
$tag = ${
    IpTagType = 'RoutingPreference'
    Tag = 'Internet'   
}
$ipTag = New-AzPublicIpTag @tag

## Create IP. ##
$ip = @{
    Name = 'myStandardPublicIP'
    ResourceGroupName = 'myResourceGroup'
    Location = 'eastus2'
    Sku = 'Standard'
    AllocationMethod = 'Static'
    IpAddressVersion = 'IPv4'
    IpTag = $ipTag
    Zone = 1,2,3   
}
New-AzPublicIpAddress @ip
```
### <a name="tier"></a>Niveau

Les adresses IP publiques sont associées à une seule région. Le niveau **Global** s’étend sur une adresse IP dans plusieurs régions. Le niveau **Global** est exigé pour les front-ends des équilibreurs de charge inter-régions.  

Pour plus d’informations, consultez [Équilibreur de charge inter-région](../load-balancer/cross-region-overview.md).

La commande suivante crée une adresse IPv4 globale. Cette adresse peut être associée au serveur front-end d’un équilibreur de charge inter-région.

```azurepowershell-interactive
$ip = @{
    Name = 'myStandardPublicIP-Global'
    ResourceGroupName = 'myResourceGroup'
    Location = 'eastus2'
    Sku = 'Standard'
    AllocationMethod = 'Static'
    IpAddressVersion = 'IPv4'
    Tier = 'Global'
}
New-AzPublicIpAddress @ip
```
>[!NOTE]
>Les adresses du niveau Global ne prennent pas en charge les zones de disponibilité.

## <a name="additional-information"></a>Informations supplémentaires 

Pour plus d’informations sur les paramètres listés dans ce guide pratique, consultez [Gérer les adresses IP publiques](./virtual-network-public-ip-address.md#create-a-public-ip-address).

## <a name="next-steps"></a>Étapes suivantes
- Associer une [adresse IP publique à une machine virtuelle](./associate-public-ip-address-vm.md#azure-portal)
- En savoir plus sur les [adresses IP publiques](./public-ip-addresses.md#public-ip-addresses) dans Azure.
- En savoir plus sur tous les [paramètres d’adresse IP publique](virtual-network-public-ip-address.md#create-a-public-ip-address).
