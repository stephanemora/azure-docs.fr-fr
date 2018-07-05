---
title: Créer une passerelle de réseau virtuel redondante dans une zone dans les zones de disponibilité Azure - Préversion | Microsoft Docs
description: Déployer des passerelles VPN et ExpressRoute dans les zones de disponibilité - Préversion.
services: vpn-gateway
documentationcenter: na
author: cherylmc
Customer intent: As someone with a basic network background, I want to understand how to create zone-redundant gateways.
ms.service: vpn-gateway
ms.topic: article
ms.date: 06/25/2018
ms.author: cherylmc
ms.openlocfilehash: a08c0f772965ddb2b40ac1ced1f26ade4cba3197
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37017030"
---
# <a name="create-a-zone-redundant-virtual-network-gateway-in-azure-availability-zones---preview"></a>Créer une passerelle de réseau virtuel redondante dans une zone dans les zones de disponibilité Azure - Préversion

Vous pouvez déployer des passerelles VPN et ExpressRoute dans des [zones de disponibilité Azure](../availability-zones/az-overview.md). Cela apporte de la résilience, de l’extensibilité et une plus grande disponibilité aux passerelles de réseau virtuel. Le déploiement de passerelles dans les zones de disponibilité Azure sépare les passerelles physiquement et logiquement au sein d’une région, tout en protégeant votre connectivité de réseau local à Azure à partir d’échecs au niveau de la zone.

Les passerelles zonales et redondantes dans une zone bénéficient d’améliorations des performances fondamentales en comparaison avec les passerelles de réseau virtuel régulières. En outre, la création d’une passerelle de réseau virtuel zonale ou redondante dans une zone est plus rapide que la création d’autres passerelles. Au lieu de prendre 45 minutes, les temps de création prennent environ 15 minutes pour une passerelle ExpressRoute et 19 minutes pour une passerelle VPN.

### <a name="zrgw"></a>Passerelles redondantes dans une zone

Pour déployer automatiquement vos passerelles de réseau virtuel entre des zones de disponibilité, vous pouvez utiliser des passerelles de réseau virtuel redondantes dans une zone. Avec les passerelles redondantes dans une zone, vous pouvez exploiter le contrat de niveau de service à 99,99 % de durée de fonctionnement à la disposition générale, afin d’accéder à vos services évolutifs et critiques sur Azure.

<br>
<br>

![graphique des passerelles redondantes dans une zone](./media/create-zone-redundant-vnet-gateway/zonered.png)

### <a name="zgw"></a>Passerelles zonales

Pour le déploiement de passerelles dans une zone spécifique, vous utilisez des passerelles zonales. Lorsque vous déployez une passerelle zonale, les deux instances de la passerelle sont déployées dans la même zone de disponibilité.

<br>
<br>

![graphique sur les passerelles zonales](./media/create-zone-redundant-vnet-gateway/zonal.png)

## <a name="gwskus"></a>SKU de passerelle

Les passerelles zonales et redondantes dans une zone doivent utiliser les nouvelles références (SKU) de passerelle. Une fois que vous êtes [inscrit automatiquement dans la préversion](#enroll), vous verrez les nouvelles références (SKU) de passerelle de réseau virtuel dans toutes les régions Azure AZ. Ces références SKU sont semblables aux références SKU correspondantes pour les passerelles VPN et ExpressRoute, sauf qu’elles sont spécifiques aux passerelles zonales et redondantes dans une zone.

Les nouvelles références SKU de passerelle sont :

### <a name="vpn-gateway"></a>Passerelle VPN

* VpnGw1AZ
* VpnGw2AZ
* VpnGw3AZ

### <a name="expressroute"></a>ExpressRoute

* ErGw1AZ
* ErGw2AZ
* ErGw3AZ

## <a name="pipskus"></a>Références SKU d’adresse IP publique

Les passerelles zonales et redondantes dans une zone s’appuient sur la référence (SKU) de ressource d’adresse IP publique *Standard*. La configuration de la ressource d’adresse IP publique Azure détermine si la passerelle que vous déployez est zonale ou redondante dans une zone. Si vous créez une ressource d’adresse IP publique avec une référence (SKU) *De base*, la passerelle n’aura aucune redondance de zone, et les ressources de la passerelle seront régionales.

### <a name="pipzrg"></a>Passerelles redondantes dans une zone

Lorsque vous créez une adresse IP publique à l’aide de la référence SKU d’adresse IP publique **Standard** sans spécifier de zone, le comportement diffère selon que la passerelle est une passerelle VPN ou une passerelle ExpressRoute. 

* Pour une passerelle VPN, les deux instances de passerelle seront déployées dans 2 de ces zones, parmi les trois zones assurant la redondance de zone. 
* Pour une passerelle ExpressRoute, dans la mesure où il peut y avoir plus de deux instances, la passerelle peut s’étendre sur les trois zones.

### <a name="pipzg"></a>Passerelles zonales

Lorsque vous créez une adresse IP publique à l’aide de la référence SKU d’adresse IP **Standard** et spécifiez la zone (1, 2 ou 3), toutes les instances de la passerelle sont déployées dans la même zone.

### <a name="piprg"></a>Passerelles régionales

Lorsque vous créez une adresse IP publique à l’aide de la référence SKU d’adresse IP publique **De base**, la passerelle est déployée comme une passerelle régionale et n’a pas de redondance de zone intégrée.

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

## <a name="enroll"></a>1. S’inscrire à la préversion

Avant de pouvoir configurer une passerelle zonale ou redondante dans une zone, vous devez tout d’abord inscrire automatiquement votre abonnement à la préversion. Une fois que votre abonnement a été configuré, vous commencerez à voir les nouvelles références (SKU) de passerelle dans toutes les régions Azure AZ. 

Assurez-vous que vous êtes connecté à votre compte Azure et que vous utilisez l’abonnement que vous souhaitez mettre sur liste verte pour cette préversion. Consultez l’exemple qui suit pour vous inscrire :

```azurepowershell-interactive
Register-AzureRmProviderFeature -FeatureName AllowVMSSVirtualNetworkGateway -ProviderNamespace Microsoft.Network
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
```

Utilisez la commande suivante pour vérifier que la fonctionnalité « AllowVMSSVirtualNetworkGateway » est inscrite avec votre abonnement :

```azurepowershell-interactive
Get-AzureRmProviderFeature -ProviderNamespace Microsoft.Network
```

Le résultat ressemble à l’exemple suivant :

![approvisionné](./media/create-zone-redundant-vnet-gateway/verifypreview.png)

## <a name="variables"></a>2. Déclarer vos variables

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

## <a name="configure"></a>3. Créer un réseau virtuel

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

## <a name="gwsub"></a>4. Ajouter le sous-réseau de passerelle

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
## <a name="publicip"></a>5. Demander une adresse IP publique
 
Dans cette étape, choisissez les instructions qui s’appliquent à la passerelle que vous souhaitez créer. La sélection des zones pour le déploiement des passerelles varie selon les zones spécifiées pour l’adresse IP publique.

### <a name="ipzoneredundant"></a>Pour les passerelles redondantes dans une zone

Demandez une adresse IP publique avec une référence SKU PublicIpaddress **Standard** et ne spécifiez aucune zone. Dans ce cas, l’adresse IP publique Standard créée sera une adresse IP publique redondante dans une zone.   

```azurepowershell-interactive
$pip1 = New-AzureRmPublicIpAddress -ResourceGroup $RG1 -Location $Location1 -Name $GwIP1 -AllocationMethod Dynamic -Sku Standard
```

### <a name="ipzonalgw"></a>Pour les passerelles zonales

Demandez une adresse IP publique avec une référence SKU PublicIpaddress **Standard**. Spécifiez la zone (1, 2 ou 3). Toutes les instances de la passerelle seront déployées dans cette zone.

```azurepowershell-interactive
$pip1 = New-AzureRmPublicIpAddress -ResourceGroup $RG1 -Location $Location1 -Name $GwIP1 -AllocationMethod Dynamic -Sku Standard -Zone 1
```

### <a name="ipregionalgw"></a>Pour les passerelles régionales

Demandez une adresse IP publique avec une référence SKU PublicIpaddress **De base**. Dans ce cas, la passerelle est déployée comme une passerelle régionale et n’a pas de redondance de zone intégrée. Les instances de passerelle sont créées dans des zones, respectivement.

```azurepowershell-interactive
$pip1 = New-AzureRmPublicIpAddress -ResourceGroup $RG1 -Location $Location1 -Name $GwIP1 -AllocationMethod Dynamic -Sku Basic
```
## <a name="gwipconfig"></a>6. Créer la configuration de l’adresse IP

```azurepowershell-interactive
$getvnet = Get-AzureRmVirtualNetwork -ResourceGroupName $RG1 -Name $VNet1
$subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name $GwSubnet1 -VirtualNetwork $getvnet
$gwipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GwIPConf1 -Subnet $subnet -PublicIpAddress $pip1
```

## <a name="gwconfig"></a>7. Créer la passerelle

Créez la passerelle de réseau virtuel.

>[!NOTE]
>À ce stade, vous ne pouvez pas spécifier la référence (SKU) de la passerelle. La référence (SKU) sera automatiquement par défaut ErGw1AZ pour la passerelle ExpressRoute et VpnGw1AZ pour la passerelle VPN.
>

### <a name="for-expressroute"></a>Pour la passerelle ExpressRoute

```azurepowershell-interactive
New-AzureRmVirtualNetworkGateway -ResourceGroup $RG1 -Location $Location1 -Name $Gw1 -IpConfigurations $GwIPConf1 -GatewayType ExpressRoute
```

### <a name="for-vpn-gateway"></a>Pour la passerelle VPN

```azurepowershell-interactive
New-AzureRmVirtualNetworkGateway -ResourceGroup $RG1 -Location $Location1 -Name $Gw1 -IpConfigurations $GwIPConf1 -GatewayType Vpn -VpnType RouteBased
```

## <a name="feedback"></a>Comment fournir des commentaires

Nous aimerions recevoir vos commentaires. Envoyez un courrier électronique à aznetworkgateways@microsoft.com pour signaler d’éventuels problèmes ou fournir des commentaires (positifs ou négatifs) pour les passerelles VPN et ExpressRoute zonales ou redondantes dans une zone. Ajoutez le nom de votre société entre crochets « [] » dans la ligne Objet. Ajoutez également votre ID d’abonnement, si vous signalez un problème.

## <a name="faq"></a>Forum Aux Questions

### <a name="how-do-i-sign-up-for-the-preview"></a>Comment puis-je m’inscrire à la préversion ?

Vous pouvez vous [inscrire automatiquement](#enroll) en utilisant les commandes PowerShell dans cet article.

### <a name="what-will-change-when-i-enroll"></a>Qu’est-ce qui changera lorsque je serais inscrit ?

De votre point de vue, lors de la préversion, vous pouvez déployer vos passerelles avec la redondance dans une zone. Cela signifie que toutes les instances des passerelles seront déployées sur des zones de disponibilité Azure, et que chaque zone de disponibilité est un autre domaine d’erreur et de mise à jour. Cela rend vos passerelles plus fiables, disponibles et résilientes aux défaillances de zone.

### <a name="what-regions-are-available-for-the-preview"></a>Quelles régions sont disponibles pour la préversion ?

Les passerelles zonales ou redondantes dans une zone sont disponibles dans les régions de production/Azure publiques.

### <a name="will-i-be-billed-for-participating-in-this-preview"></a>Serais-je facturé pour ma participation à cette préversion ?

Vous ne serez pas facturé pour vos passerelles lors de la préversion. Toutefois, il n’existe aucun Contrat de niveau de service associé à votre déploiement. Nous sommes très intéressés par vos commentaires.

> [!NOTE]
> Pour les passerelles ExpressRoute, la passerelle n’est pas facturée. Toutefois, vous serez facturé pour le circuit (pas pour la passerelle).

### <a name="what-regions-are-available-for-me-to-try-this-in"></a>Quelles régions sont disponibles pour en faire l’essai ?

La préversion publique est disponible dans les régions USA Centre et France Centre (régions Azure qui possèdent des zones de disponibilité généralement disponible). À l’avenir, nous rendrons les passerelles redondantes dans une zone disponibles pour vous dans d’autres régions Azure publiques.

### <a name="can-i-change-my-existing-virtual-network-gateways-to-zone-redundant-or-zonal-gateways"></a>Puis-je changer mes passerelles de réseau virtuel existantes en passerelles zonales ou redondantes dans une zone ?

La migration de vos passerelles de réseau virtuel existantes vers des passerelles zonales ou redondantes dans une zone n’est pas prise en charge pour le moment. Toutefois, vous pouvez supprimer votre passerelle existante et recréer une passerelle zonale ou redondante dans une zone.

### <a name="can-i-deploy-both-vpn-and-express-route-gateways-in-same-virtual-network"></a>Puis-je déployer des passerelles VPN et ExpressRoute dans le même réseau virtuel ?

La coexistence des passerelles VPN et ExpressRoute dans le même réseau virtuel est prise en charge dans la Préversion publique. Toutefois, tenez compte des exigences et des limitations suivantes :

* Réservez une plage d’adresse IP de /27 pour le sous-réseau de passerelle.
* Les passerelles ExpressRoute zonales ou redondantes dans une zone peuvent coexister uniquement avec des passerelles VPN zonales ou redondantes dans une zone.
* Déployez les passerelles ExpressRoute zonales ou redondantes dans une zone avant de déployer les passerelles VPN zonales ou redondantes dans une zone.
* Une passerelle ExpressRoute zonale ou redondante dans une zone peut être connectée à 4 circuits maximum.

## <a name="next-steps"></a>Étapes suivantes

Nous aimerions recevoir vos commentaires. Envoyez un courrier électronique à aznetworkgateways@microsoft.com pour signaler d’éventuels problèmes ou fournir des commentaires (positifs ou négatifs) pour les passerelles VPN et ExpressRoute zonales ou redondantes dans une zone. Ajoutez le nom de votre société entre crochets « [] » dans la ligne Objet. Ajoutez également votre ID d’abonnement, si vous signalez un problème.