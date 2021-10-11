---
title: 'Démarrage rapide : Créer une IP publique – PowerShell'
titleSuffix: Azure Virtual Network
description: Dans ce guide de démarrage rapide, découvrez comment créer une IP publique à l’aide d’Azure PowerShell.
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: quickstart
ms.date: 10/01/2021
ms.author: allensu
ms.openlocfilehash: 51a59615d0f87943e195a3f189d96eccaf67623f
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129369058"
---
# <a name="quickstart-create-a-public-ip-address-using-powershell"></a>Démarrage rapide : Créer une adresse IP publique à l’aide de PowerShell

Dans ce guide de démarrage rapide, vous allez apprendre à créer une adresse IP publique Azure. Les adresses IP publiques dans Azure sont utilisées pour les connexions publiques aux ressources Azure. Les adresses IP publiques sont disponibles dans deux SKU : de base et standard. Deux niveaux d’adresses IP publiques sont disponibles : régional et mondial. La préférence de routage d’une adresse IP publique est définie lors de sa création. Le routage Internet et le routage Réseau Microsoft sont les choix disponibles.

## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Azure PowerShell installé localement ou Azure Cloud Shell

Si vous choisissez d’installer et d’utiliser PowerShell en local, vous devez exécuter le module Azure PowerShell version 5.4.1 ou ultérieure pour les besoins de cet article. Exécutez `Get-Module -ListAvailable Az` pour rechercher la version installée. Si vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-Az-ps). Si vous exécutez PowerShell en local, vous devez également exécuter `Connect-AzAccount` pour créer une connexion avec Azure.

## <a name="create-a-resource-group"></a>Créer un groupe de ressources
Un groupe de ressources Azure est un conteneur logique dans lequel les ressources Azure sont déployées et gérées.

À l’aide de [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup), créez un groupe de ressources nommé **QuickStartCreateIP-rg** dans l’emplacement **eastus2**.

```azurepowershell-interactive
$rg =@{
    Name = 'QuickStartCreateIP-rg'
    Location = 'eastus2'
}
New-AzResourceGroup @rg
```
## <a name="create-public-ip"></a>Créer une adresse IP publique

# <a name="standard-sku"></a>[**Référence Standard**](#tab/create-public-ip-standard)

>[!NOTE]
>L’adresse IP publique de SKU standard est recommandée pour les charges de travail de production.  Pour plus d’informations sur les SKU, consultez **[Adresses IP publiques](public-ip-addresses.md)** .
>
>La commande suivante fonctionne pour le module Az.Network version 4.5.0 ou ultérieure.  Pour plus d’informations sur les modules PowerShell en cours d’utilisation, reportez-vous à la [documentation de PowerShellGet](/powershell/module/powershellget/).

Dans cette section, vous allez créer une IP publique avec des zones. Les adresses IP publiques peuvent être redondantes interzones ou être zonales.

Utilisez [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) pour créer une adresse IPv4 publique redondante interzone standard, nommée **myStandardPublicIP**, dans **QuickStartCreateIP-rg**. 

Pour créer une adresse IPv6, changez la valeur du paramètre **`--IpAddressVersion`** pour **IPv6**.

```azurepowershell-interactive
$ip = @{
    Name = 'myStandardPublicIP'
    ResourceGroupName = 'QuickStartCreateIP-rg'
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

# <a name="basic-sku"></a>[**Référence De base**](#tab/create-public-ip-basic)

>[!NOTE]
>L’adresse IP publique de SKU standard est recommandée pour les charges de travail de production.  Pour plus d’informations sur les SKU, consultez **[Adresses IP publiques](public-ip-addresses.md)** .

Dans cette section, vous allez créer une IP de base. Les adresses IP publiques de base ne prennent pas en charge les zones de disponibilité.

Utilisez [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) pour créer une adresse IPv4 publique statique de base, nommée **myBasicPublicIP**, dans **QuickStartCreateIP-rg**.  

Pour créer une adresse IPv6, changez la valeur du paramètre **`--IpAddressVersion`** pour **IPv6**. 

```azurepowershell-interactive
$ip = @{
    Name = 'myBasicPublicIP'
    ResourceGroupName = 'QuickStartCreateIP-rg'
    Location = 'eastus2'
    Sku = 'Basic'
    AllocationMethod = 'Static'
    IpAddressVersion = 'IPv4'
}
New-AzPublicIpAddress @ip
```
S’il est acceptable que l’adresse IP change au fil du temps, l’affectation **dynamique** d’adresses IP peut être sélectionnée en changeant la valeur de **`-AllocationMethod`** pour **Dynamique**. 

>[!NOTE]
> Une adresse IPv6 de base doit toujours être « dynamique ».

---

## <a name="create-a-zonal-or-no-zone-public-ip-address"></a>Créer une adresse IP publique zonale ou non zonale

Dans cette section, vous allez apprendre à créer une adresse IP publique zonale ou dépourvue de zone.

# <a name="zonal"></a>[**Zonal**](#tab/create-public-ip-zonal)

Pour créer une adresse IPv4 publique zonale standard à l’intérieur de la zone 2, nommée **myStandardPublicIP-zonal** dans **QuickStartCreateIP-rg**, exécutez la commande suivante.

Pour créer une adresse IPv6, changez la valeur du paramètre **`--IpAddressVersion`** pour **IPv6**.

```azurepowershell-interactive
$ip = @{
    Name = 'myStandardPublicIP-zonal'
    ResourceGroupName = 'QuickStartCreateIP-rg'
    Location = 'eastus2'
    Sku = 'Standard'
    AllocationMethod = 'Static'
    IpAddressVersion = 'IPv4'
    Zone = 2
}
New-AzPublicIpAddress @ip
```
>[!NOTE]
>Les sélections des options de zone ci-dessus sont valides uniquement dans les régions pourvues de [zones de disponibilité](../../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones).

# <a name="non-zonal"></a>[**Non zonale**](#tab/create-public-ip-non-zonal)

Dans cette section, vous allez créer une adresse IP non zonale.  

>[!NOTE]
>La commande suivante fonctionne pour le module Az.Network version 4.5.0 ou ultérieure.  Pour plus d’informations sur les modules PowerShell en cours d’utilisation, reportez-vous à la [documentation de PowerShellGet](/powershell/module/powershellget/).

Utilisez [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) pour créer une adresse IPv4 publique standard sous forme de ressource non zonale, nommée **myStandardPublicIP-nozone**, dans **QuickStartCreateIP-rg**. 

Pour créer une adresse IPv6, changez la valeur du paramètre **`-IpAddressVersion`** pour **IPv6**.

```azurepowershell-interactive
$ip = @{
    Name = 'myStandardPublicIP-nozone'
    ResourceGroupName = 'QuickStartCreateIP-rg'
    Location = 'eastus2'
    Sku = 'Standard'
    AllocationMethod = 'Static'
    IpAddressVersion = 'IPv4'
}
New-AzPublicIpAddress @ip
```
La suppression du paramètre **`-Zone`** dans la commande est valide dans toutes les régions.  

La suppression du paramètre **`-Zone`** constitue la sélection par défaut des adresses IP publiques standard dans les régions sans [Zone de disponibilité](../../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones).

---

## <a name="routing-preference-and-tier"></a>Préférence de routage et niveau

Les adresses IPv4 publiques statiques de référence SKU standard prennent en charge la préférence de routage ou la fonctionnalité Niveau global.

# <a name="routing-preference"></a>[**Préférence de routage**](#tab/routing-preference)

Par défaut, la préférence de routage pour les adresses IP publiques est définie sur le « Réseau Microsoft », qui remet le trafic à l’utilisateur via le réseau étendu mondial de Microsoft.  

La sélection de **Internet** réduit les déplacements sur le réseau de Microsoft en utilisant à la place le réseau du fournisseur de services Internet pour acheminer le trafic à un coût optimisé.  

Pour plus d’informations sur la préférence de routage, consultez la section [Qu’est-ce qu’une préférence de routage (préversion) ?](routing-preference-overview.md).

La commande crée une nouvelle adresse IPv4 publique redondante interzone standard avec une préférence de routage de type **Internet** :

```azurepowershell-interactive
## Create IP tag for Internet and Routing Preference. ##
$tag = @{
    IpTagType = 'RoutingPreference'
    Tag = 'Internet'   
}
$ipTag = New-AzPublicIpTag @tag

## Create IP. ##
$ip = @{
    Name = 'myStandardPublicIP-RP'
    ResourceGroupName = 'QuickStartCreateIP-rg'
    Location = 'eastus2'
    Sku = 'Standard'
    AllocationMethod = 'Static'
    IpAddressVersion = 'IPv4'
    IpTag = $ipTag
    Zone = 1,2,3   
}
New-AzPublicIpAddress @ip
```

# <a name="tier"></a>[**Niveau**](#tab/tier)

Les adresses IP publiques sont associées à une seule région. Le niveau **Global** s’étend sur une adresse IP dans plusieurs régions. Le niveau **Global** est exigé pour les front-ends des équilibreurs de charge interrégionaux.  

Pour plus d’informations, consultez [Équilibreur de charge inter-région](../../load-balancer/cross-region-overview.md).

La commande suivante crée une adresse IPv4 globale. Cette adresse peut être associée au serveur front-end d’un équilibreur de charge inter-région.

```azurepowershell-interactive
$ip = @{
    Name = 'myStandardPublicIP-Global'
    ResourceGroupName = 'QuickStartCreateIP-rg'
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

---

## <a name="clean-up-resources"></a>Nettoyer les ressources

Lorsque vous n’avez plus besoin de la machines virtuelle ni de l’adresse IP publique, supprimez le groupe de ressources et toutes les ressources qu’il contient à l’aide de [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup).

```azurepowershell-interactive
Remove-AzResourceGroup -Name 'TutorVMRoutePref-rg'

```

## <a name="next-steps"></a>Étapes suivantes

Passez à l’article suivant pour apprendre à créer un préfixe d’adresse IP publique :
> [!div class="nextstepaction"]
> [Créer un préfixe d’adresse IP publique à l’aide de PowerShell](create-public-ip-prefix-powershell.md)