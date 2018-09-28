---
title: Créer une passerelle de réseau virtuel redondante dans une zone dans les zones de disponibilité Azure | Microsoft Docs
description: Déployer des passerelles VPN et ExpressRoute dans les zones de disponibilité
services: vpn-gateway
author: cherylmc
Customer intent: As someone with a basic network background, I want to understand how to create zone-redundant gateways.
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 09/21/2018
ms.author: cherylmc
ms.openlocfilehash: f531be5a814ed1805a2938daec1d210f9daccfa5
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46949772"
---
# <a name="create-a-zone-redundant-virtual-network-gateway-in-azure-availability-zones"></a>Créer une passerelle de réseau virtuel redondante dans une zone dans les zones de disponibilité Azure

Vous pouvez déployer des passerelles VPN et ExpressRoute dans des zones de disponibilité Azure. Cela apporte de la résilience, de l’extensibilité et une plus grande disponibilité aux passerelles de réseau virtuel. Le déploiement de passerelles dans les zones de disponibilité Azure sépare les passerelles physiquement et logiquement au sein d’une région, tout en protégeant votre connectivité de réseau local à Azure à partir d’échecs au niveau de la zone. Pour plus d’informations, voir [À propos des passerelles de réseau virtuel redondante dans une zone](about-zone-redundant-vnet-gateways.md) et [À propos des zones de disponibilité Azure](../availability-zones/az-overview.md).

## <a name="before-you-begin"></a>Avant de commencer

Vous pouvez utiliser PowerShell installé localement sur votre ordinateur ou Azure Cloud Shell. Si vous choisissez d’installer et d’utiliser PowerShell en local, cette fonctionnalité requiert la dernière version du module PowerShell.

[!INCLUDE [Cloud shell](../../includes/vpn-gateway-cloud-shell-powershell.md)]

### <a name="to-use-powershell-locally"></a>Pour une utilisation locale de PowerShell

Si vous utilisez PowerShell en local sur votre ordinateur, plutôt que d’utiliser Cloud Shell, installez le module PowerShell 6.1.1 ou une version ultérieure. Pour vérifier la version de PowerShell que vous avez installée, utilisez la commande suivante :

```azurepowershell
Get-Module AzureRM -ListAvailable | Select-Object -Property Name,Version,Path
```

Si vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-azurerm-ps).

[!INCLUDE [PowerShell login](../../includes/vpn-gateway-ps-login-include.md)]

## <a name="variables"></a>1. Déclarer vos variables

Les valeurs utilisées pour les étapes de l’exemple sont répertoriées ci-dessous. En outre, certains des exemples utilisent des variables déclarées dans les étapes. Si vous utilisez ces étapes dans votre propre environnement, veillez à remplacer ces valeurs par les vôtres. Lorsque vous spécifiez un emplacement, vérifiez que la région que vous spécifiez est prise en charge. Pour plus d’informations, visitez le [FAQ](#faq).

```azurepowershell-interactive
$RG1         = "TestRG1"
$VNet1       = "VNet1"
$Location1   = "CentralUS"
$FESubnet1   = "FrontEnd"
$BESubnet1   = "Backend"
$GwSubnet1   = "GatewaySubnet"
$VNet1Prefix = "10.1.0.0/16"
$FEPrefix1   = "10.1.0.0/24"
$BEPrefix1   = "10.1.1.0/24"
$GwPrefix1   = "10.1.255.0/27"
$Gw1         = "VNet1GW"
$GwIP1       = "VNet1GWIP"
$GwIPConf1   = "gwipconf1"
```

## <a name="configure"></a>2. Créer un réseau virtuel

Créez un groupe de ressources.

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName $RG1 -Location $Location1
```

Créez un réseau virtuel.

```azurepowershell-interactive
$fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubnet1 -AddressPrefix $FEPrefix1
$besub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubnet1 -AddressPrefix $BEPrefix1
$vnet = New-AzureRmVirtualNetwork -Name $VNet1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNet1Prefix -Subnet $fesub1,$besub1
```

## <a name="gwsub"></a>3. Ajouter le sous-réseau de passerelle

Le sous-réseau de passerelle contient les adresses IP réservées utilisées par les services de passerelle de réseau virtuel. Appuyez-vous sur les exemples suivants pour ajouter et définir un sous-réseau de passerelle :

Ajoutez le sous-réseau de passerelle.

```azurepowershell-interactive
$getvnet = Get-AzureRmVirtualNetwork -ResourceGroupName $RG1 -Name VNet1
Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.1.255.0/27 -VirtualNetwork $getvnet
```

Définissez la configuration de sous-réseau de passerelle du réseau virtuel.

```azurepowershell-interactive
$getvnet | Set-AzureRmVirtualNetwork
```
## <a name="publicip"></a>4. Demander une adresse IP publique
 
Dans cette étape, choisissez les instructions qui s’appliquent à la passerelle que vous souhaitez créer. La sélection des zones pour le déploiement des passerelles varie selon les zones spécifiées pour l’adresse IP publique.

### <a name="ipzoneredundant"></a>Pour les passerelles redondantes dans une zone

Demandez une adresse IP publique avec une référence SKU PublicIpaddress **Standard** et ne spécifiez aucune zone. Dans ce cas, l’adresse IP publique Standard créée sera une adresse IP publique redondante dans une zone.   

```azurepowershell-interactive
$pip1 = New-AzureRmPublicIpAddress -ResourceGroup $RG1 -Location $Location1 -Name $GwIP1 -AllocationMethod Static -Sku Standard
```

### <a name="ipzonalgw"></a>Pour les passerelles zonales

Demandez une adresse IP publique avec une référence SKU PublicIpaddress **Standard**. Spécifiez la zone (1, 2 ou 3). Toutes les instances de la passerelle seront déployées dans cette zone.

```azurepowershell-interactive
$pip1 = New-AzureRmPublicIpAddress -ResourceGroup $RG1 -Location $Location1 -Name $GwIP1 -AllocationMethod Static -Sku Standard -Zone 1
```

### <a name="ipregionalgw"></a>Pour les passerelles régionales

Demandez une adresse IP publique avec une référence SKU PublicIpaddress **De base**. Dans ce cas, la passerelle est déployée comme une passerelle régionale et n’a pas de redondance de zone intégrée. Les instances de passerelle sont créées dans des zones, respectivement.

```azurepowershell-interactive
$pip1 = New-AzureRmPublicIpAddress -ResourceGroup $RG1 -Location $Location1 -Name $GwIP1 -AllocationMethod Dynamic -Sku Basic
```
## <a name="gwipconfig"></a>5. Créer la configuration de l’adresse IP

```azurepowershell-interactive
$getvnet = Get-AzureRmVirtualNetwork -ResourceGroupName $RG1 -Name $VNet1
$subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name $GwSubnet1 -VirtualNetwork $getvnet
$gwipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GwIPConf1 -Subnet $subnet -PublicIpAddress $pip1
```

## <a name="gwconfig"></a>6. Créer la passerelle

Créez la passerelle de réseau virtuel.

### <a name="for-expressroute"></a>Pour la passerelle ExpressRoute

```azurepowershell-interactive
New-AzureRmVirtualNetworkGateway -ResourceGroup $RG1 -Location $Location1 -Name $Gw1 -IpConfigurations $GwIPConf1 -GatewayType ExpressRoute
```

### <a name="for-vpn-gateway"></a>Pour la passerelle VPN

```azurepowershell-interactive
New-AzureRmVirtualNetworkGateway -ResourceGroup $RG1 -Location $Location1 -Name $Gw1 -IpConfigurations $GwIPConf1 -GatewayType Vpn -VpnType RouteBased
```

## <a name="faq"></a>Forum Aux Questions

### <a name="what-will-change-when-i-deploy-these-new-skus"></a>Qu’est-ce qui va changer lorsque je vais déployer ces nouvelles références SKU ?

De votre point de vue, vous pouvez déployer vos passerelles avec la redondance dans une zone. Cela signifie que toutes les instances des passerelles seront déployées sur des zones de disponibilité Azure, et que chaque zone de disponibilité est un autre domaine d’erreur et de mise à jour. Cela rend vos passerelles plus fiables, disponibles et résilientes aux défaillances de zone.

### <a name="can-i-use-the-azure-portal"></a>Puis-je utiliser le portail Azure ?

Oui, vous pouvez utiliser le portail Azure pour déployer les nouvelles références SKU. Toutefois, vous ne les verrez que dans les régions Azure disposant de zones de disponibilité Azure.

### <a name="what-regions-are-available-for-me-to-use-the-new-skus"></a>Dans quelles régions puis-je utiliser les nouvelles références SKU ?

Les nouvelles références SKU sont disponibles dans les régions Azure ayant des zones de disponibilité Azure, c’est-à-dire les régions USA Centre, France Centre et Europe Ouest. Prochainement, d’autres régions Azure publiques seront disponibles.

### <a name="can-i-changemigrateupgrade-my-existing-virtual-network-gateways-to-zone-redundant-or-zonal-gateways"></a>Puis-je changer/migrer/mettre à niveau mes passerelles de réseau virtuel existantes vers des passerelles zonales ou redondantes dans une zone ?

La migration de vos passerelles de réseau virtuel existantes vers des passerelles zonales ou redondantes dans une zone n’est pas prise en charge pour le moment. Toutefois, vous pouvez supprimer votre passerelle existante et recréer une passerelle zonale ou redondante dans une zone.

### <a name="can-i-deploy-both-vpn-and-express-route-gateways-in-same-virtual-network"></a>Puis-je déployer des passerelles VPN et ExpressRoute dans le même réseau virtuel ?

La coexistence des passerelles VPN et ExpressRoute dans le même réseau virtuel est prise en charge. Toutefois, vous devez réserver une plage d’adresse IP de /27 pour le sous-réseau de passerelle.