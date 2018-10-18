---
title: Configuration de la connectivité d’un réseau virtuel à SAP HANA sur Azure (grandes Instances) | Microsoft Docs
description: Configuration de la connectivité d’un réseau virtuel à SAP HANA sur Azure (grandes Instances).
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
ms.openlocfilehash: 5efdda485e4e1f5013948c6636b267f0d388f4d5
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/07/2018
ms.locfileid: "44167603"
---
# <a name="connecting-a-vnet-to-hana-large-instance-expressroute"></a>Connexion d’un réseau virtuel à une grande instance HANA ExpressRoute

Puisque vous avez défini toutes les plages d’adresses IP et que vous avez reçu les données de Microsoft, vous pouvez maintenant commencer à connecter le réseau virtuel créé aux grandes instances HANA. Une fois le réseau virtuel Azure créé, vous devez créer une passerelle ExpressRoute sur le réseau virtuel pour lier le réseau virtuel au circuit ExpressRoute qui se connecte au locataire du client sur la grande instance.

> [!NOTE] 
> Cette étape peut prendre jusqu'à 30 minutes, dans la mesure où la nouvelle passerelle est créée dans l’abonnement Azure désigné, puis connectée au réseau virtuel Azure spécifié.

S’il existe déjà une passerelle, vérifiez s’il s’agit d’une passerelle ExpressRoute. Si ce n’est pas le cas, la passerelle doit être supprimée et recréée comme passerelle ExpressRoute. Si une passerelle ExpressRoute est déjà établie, étant donné que le réseau virtuel Azure est déjà connecté au circuit ExpressRoute pour la connectivité locale, passez à la section Liaison des réseaux virtuels ci-dessous.

- Utilisez soit le (nouveau) [portail Azure](https://portal.azure.com/), soit PowerShell pour créer une passerelle VPN ExpressRoute connectée à votre réseau virtuel.
  - Si vous utilisez le portail Azure, ajoutez une nouvelle **passerelle de réseau virtuel**, puis sélectionnez **ExpressRoute** comme type de passerelle.
  - Si vous avez choisi PowerShell, téléchargez et utilisez la dernière version du [kit de développement logiciel (SDK) Azure PowerShell](https://azure.microsoft.com/downloads/) pour garantir une expérience optimale. Les commandes suivantes créent une passerelle ExpressRoute. Le texte précédé par un _$_ correspond à des variables définies par l’utilisateur, qui doivent être mises à jour avec vos propres informations.

```PowerShell
# These Values should already exist, update to match your environment
$myAzureRegion = "eastus"
$myGroupName = "SAP-East-Coast"
$myVNetName = "VNet01"

# These values are used to create the gateway, update for how you wish the GW components to be named
$myGWName = "VNet01GW"
$myGWConfig = "VNet01GWConfig"
$myGWPIPName = "VNet01GWPIP"
$myGWSku = "HighPerformance" # Supported values for HANA Large Instances are: HighPerformance or UltraPerformance

# These Commands create the Public IP and ExpressRoute Gateway
$vnet = Get-AzureRmVirtualNetwork -Name $myVNetName -ResourceGroupName $myGroupName
$subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
New-AzureRmPublicIpAddress -Name $myGWPIPName -ResourceGroupName $myGroupName `
-Location $myAzureRegion -AllocationMethod Dynamic
$gwpip = Get-AzureRmPublicIpAddress -Name $myGWPIPName -ResourceGroupName $myGroupName
$gwipconfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name $myGWConfig -SubnetId $subnet.Id `
-PublicIpAddressId $gwpip.Id

New-AzureRmVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName -Location $myAzureRegion `
-IpConfigurations $gwipconfig -GatewayType ExpressRoute `
-GatewaySku $myGWSku -VpnType PolicyBased -EnableBgp $true
```

Dans cet exemple, la référence SKU de passerelle HighPerformance a été utilisée. Vous pouvez opter pour HighPerformance ou pour UltraPerformance, les seules références SKU de passerelle prises en charge pour SAP HANA sur Azure (grandes instances).

> [!IMPORTANT]
> Pour les grandes instances HANA de classe Type II, l’utilisation de la référence SKU de la passerelle UltraPerformance est obligatoire.

**Liaison de réseaux virtuels**

Maintenant que le réseau virtuel Azure dispose d’une passerelle ExpressRoute, utilisez les informations d’autorisation fournies par Microsoft pour connecter la passerelle ExpressRoute au circuit ExpressRoute SAP HANA sur Azure (grandes instances) créé pour cette connexion. Vous pouvez effectuer cette opération à l’aide du portail Azure ou de PowerShell. L’utilisation du portail est recommandée, mais vous trouverez ci-dessous les instructions pour PowerShell. 

- Vous exécutez les commandes suivantes pour chaque passerelle de réseau virtuel, en utilisant un paramètre AuthGUID différent pour chaque connexion. Les deux premières entrées dans le script ci-dessous proviennent des informations fournies par Microsoft. En outre, le paramètre AuthGUID est spécifique à chaque réseau virtuel et à sa passerelle. Cela signifie que, si vous souhaitez ajouter un autre réseau virtuel Azure, vous devez obtenir un autre AuthID pour votre circuit ExpressRoute qui connecte les grandes instances HANA dans Azure. 

```PowerShell
# Populate with information provided by Microsoft Onboarding team
$PeerID = "/subscriptions/9cb43037-9195-4420-a798-f87681a0e380/resourceGroups/Customer-USE-Circuits/providers/Microsoft.Network/expressRouteCircuits/Customer-USE01"
$AuthGUID = "76d40466-c458-4d14-adcf-3d1b56d1cd61"

# Your ExpressRoute Gateway Information
$myGroupName = "SAP-East-Coast"
$myGWName = "VNet01GW"
$myGWLocation = "East US"

# Define the name for your connection
$myConnectionName = "VNet01GWConnection"

# Create a new connection between the ER Circuit and your Gateway using the Authorization
$gw = Get-AzureRmVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName
    
New-AzureRmVirtualNetworkGatewayConnection -Name $myConnectionName `
-ResourceGroupName $myGroupName -Location $myGWLocation -VirtualNetworkGateway1 $gw `
-PeerId $PeerID -ConnectionType ExpressRoute -AuthorizationKey $AuthGUID
```

Si vous souhaitez connecter la passerelle à plusieurs circuits ExpressRoute associés à votre abonnement, vous devrez peut-être exécuter plusieurs fois cette étape. Par exemple, vous allez probablement connecter la même passerelle de réseau virtuel au circuit ExpressRoute qui relie le réseau virtuel à votre réseau local.

**Étapes suivantes**

- Voir [Configuration réseau requise supplémentaire pour HLI](hana-additional-network-requirements.md).