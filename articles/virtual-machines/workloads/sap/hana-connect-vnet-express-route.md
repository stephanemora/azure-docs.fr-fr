---
title: Configuration de la connectivité d’un réseau virtuel à SAP HANA sur Azure (grandes instances) | Microsoft Docs
description: Configuration de la connectivité d’un réseau virtuel à SAP HANA sur Azure (grandes instances).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: cb14d0784ecb87c85b02952880e9eb5744d205a2
ms.sourcegitcommit: 04716e13cc2ab69da57d61819da6cd5508f8c422
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/02/2019
ms.locfileid: "58850666"
---
# <a name="connect-a-virtual-network-to-hana-large-instances"></a>Connecter un réseau virtuel à de grandes instances HANA

Après avoir créé un réseau virtuel Azure, vous pouvez connecter ce réseau à SAP HANA sur de grandes instances Azure. Créez une passerelle Azure ExpressRoute sur le réseau virtuel. Cette passerelle vous permet de lier le réseau virtuel au circuit ExpressRoute qui se connecte au locataire du client sur le tampon de grande instance.

> [!NOTE] 
> Cette étape peut prendre jusqu’à 30 minutes. La nouvelle passerelle est créée dans l’abonnement Azure désigné, puis connectée au réseau virtuel Azure spécifié.

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

S’il existe déjà une passerelle, vérifiez qu’il s’agit d’une passerelle ExpressRoute. Si ce n’est pas le cas, supprimez la passerelle et recréez-la comme passerelle ExpressRoute. Si une passerelle ExpressRoute est déjà établie, consultez la section « Lier des réseaux virtuels » de cet article. 

- Utilisez soit le [portail Azure](https://portal.azure.com/), soit PowerShell pour créer une passerelle VPN ExpressRoute connectée à votre réseau virtuel.
  - Si vous utilisez le portail Azure, ajoutez une nouvelle **passerelle de réseau virtuel**, puis sélectionnez **ExpressRoute** comme type de passerelle.
  - Si vous utilisez PowerShell, commencez par télécharger et par utiliser le dernier [SDK Azure PowerShell](https://azure.microsoft.com/downloads/). Les commandes suivantes créent une passerelle ExpressRoute. Le texte précédé par un _$_ correspond à des variables définies par l’utilisateur qui doivent être mises à jour avec vos propres informations.

```powershell
# These Values should already exist, update to match your environment
$myAzureRegion = "eastus"
$myGroupName = "SAP-East-Coast"
$myVNetName = "VNet01"

# These values are used to create the gateway, update for how you wish the GW components to be named
$myGWName = "VNet01GW"
$myGWConfig = "VNet01GWConfig"
$myGWPIPName = "VNet01GWPIP"
$myGWSku = "HighPerformance" # Supported values for HANA large instances are: HighPerformance or UltraPerformance

# These Commands create the Public IP and ExpressRoute Gateway
$vnet = Get-AzVirtualNetwork -Name $myVNetName -ResourceGroupName $myGroupName
$subnet = Get-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
New-AzPublicIpAddress -Name $myGWPIPName -ResourceGroupName $myGroupName `
-Location $myAzureRegion -AllocationMethod Dynamic
$gwpip = Get-AzPublicIpAddress -Name $myGWPIPName -ResourceGroupName $myGroupName
$gwipconfig = New-AzVirtualNetworkGatewayIpConfig -Name $myGWConfig -SubnetId $subnet.Id `
-PublicIpAddressId $gwpip.Id

New-AzVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName -Location $myAzureRegion `
-IpConfigurations $gwipconfig -GatewayType ExpressRoute `
-GatewaySku $myGWSku -VpnType PolicyBased -EnableBgp $true
```

Dans cet exemple, la référence SKU de passerelle HighPerformance a été utilisée. Vous pouvez opter pour HighPerformance ou pour UltraPerformance, les seules références SKU de passerelle prises en charge pour SAP HANA sur Azure (grandes instances).

> [!IMPORTANT]
> Pour les grandes instances HANA de classe Type II, vous devez utiliser la référence SKU de la passerelle UltraPerformance.

## <a name="link-virtual-networks"></a>Lier des réseaux virtuels

Le réseau virtuel Azure a maintenant une passerelle ExpressRoute. Utilisez les informations d’autorisation fournies par Microsoft pour connecter la passerelle ExpressRoute au circuit ExpressRoute SAP HANA sur Azure (grandes instances). Vous pouvez vous connecter à l’aide du portail Azure ou de PowerShell. L’utilisation du portail est recommandée, mais vous trouverez ci-dessous les instructions pour PowerShell. 

Exécutez les commandes suivantes pour chaque passerelle de réseau virtuel, en utilisant un AuthGUID différent pour chaque connexion. Les deux premières entrées dans le script suivant proviennent des informations fournies par Microsoft. En outre, AuthGUID est spécifique à chaque réseau virtuel et à sa passerelle. Si vous souhaitez ajouter un autre réseau virtuel Azure, vous devez obtenir un autre AuthID pour votre circuit ExpressRoute qui connecte les grandes instances HANA dans Azure. 

```powershell
# Populate with information provided by Microsoft Onboarding team
$PeerID = "/subscriptions/9cb43037-9195-4420-a798-f87681a0e380/resourceGroups/Customer-USE-Circuits/providers/Microsoft.Network/expressRouteCircuits/Customer-USE01"
$AuthGUID = "76d40466-c458-4d14-adcf-3d1b56d1cd61"

# Your ExpressRoute Gateway information
$myGroupName = "SAP-East-Coast"
$myGWName = "VNet01GW"
$myGWLocation = "East US"

# Define the name for your connection
$myConnectionName = "VNet01GWConnection"

# Create a new connection between the ER Circuit and your Gateway using the Authorization
$gw = Get-AzVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName
    
New-AzVirtualNetworkGatewayConnection -Name $myConnectionName `
-ResourceGroupName $myGroupName -Location $myGWLocation -VirtualNetworkGateway1 $gw `
-PeerId $PeerID -ConnectionType ExpressRoute -AuthorizationKey $AuthGUID
```

Pour connecter la passerelle à plusieurs circuits ExpressRoute associés à votre abonnement, vous devrez peut-être exécuter plusieurs fois cette étape. Par exemple, vous allez probablement connecter la même passerelle de réseau virtuel au circuit ExpressRoute qui relie le réseau virtuel à votre réseau local.

## <a name="next-steps"></a>Étapes suivantes

- [Configuration réseau requise supplémentaire pour HLI](hana-additional-network-requirements.md)
