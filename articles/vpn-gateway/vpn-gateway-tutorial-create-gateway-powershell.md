---
title: Tutoriel - Créer et gérer une passerelle avec une passerelle VPN Azure
description: Suivez ce didacticiel pour apprendre à créer, déployer et gérer une passerelle VPN Azure à l’aide de PowerShell.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: tutorial
ms.date: 10/13/2020
ms.author: cherylmc
ms.openlocfilehash: aaeb38b4d46188205841d6a93437533e30061485
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/02/2020
ms.locfileid: "96512089"
---
# <a name="tutorial-create-and-manage-a-vpn-gateway-using-powershell"></a>Tutoriel : Créer et gérer une passerelle VPN à l’aide de PowerShell

Les passerelles VPN Azure fournissent une connectivité entre les locaux du client et Azure. Ce tutoriel décrit les éléments de base du déploiement d’une passerelle VPN Azure, notamment la création et la gestion d’une passerelle VPN. Vous allez apprendre à effectuer les actions suivantes :

> [!div class="checklist"]
> * Créer une passerelle VPN
> * Afficher l’adresse IP publique
> * Redimensionner une passerelle VPN
> * Réinitialiser une passerelle VPN

Le diagramme suivant illustre le réseau virtuel et la passerelle VPN créés dans le cadre de ce tutoriel.

:::image type="content" source="./media/vpn-gateway-tutorial-create-gateway-powershell/diagram.png" alt-text="Diagramme de réseau virtuel et passerelle VPN":::

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [working with cloud shell](../../includes/vpn-gateway-cloud-shell-powershell.md)]

## <a name="common-network-parameter-values"></a>Valeurs des paramètres réseau communs

Vous trouverez ci-dessous les valeurs de paramètres utilisées pour ce tutoriel. Dans les exemples, les variables se traduisent par les éléments suivants :

```
#$RG1         = The name of the resource group
#$VNet1       = The name of the virtual network
#$Location1   = The location region
#$FESubnet1   = The name of the first subnet
#$BESubnet1   = The name of the second subnet
#$VNet1Prefix = The address range for the virtual network
#$FEPrefix1   = Addresses for the first subnet
#$BEPrefix1   = Addresses for the second subnet
#$GwPrefix1   = Addresses for the GatewaySubnet
#$VNet1ASN    = ASN for the virtual network
#$DNS1        = The IP address of the DNS server you want to use for name resolution
#$Gw1         = The name of the virtual network gateway
#$GwIP1       = The public IP address for the virtual network gateway
#$GwIPConf1   = The name of the IP configuration
```

Changez les valeurs ci-dessous en fonction de votre environnement et de la configuration du réseau, puis faites un copier-coller pour définir les variables de ce tutoriel. Si votre session Cloud Shell arrive à expiration ou que vous devez utiliser une fenêtre PowerShell différente, copiez et collez les variables dans votre nouvelle session et continuez le tutoriel.

```azurepowershell-interactive
$RG1         = "TestRG1"
$VNet1       = "VNet1"
$Location1   = "East US"
$FESubnet1   = "FrontEnd"
$BESubnet1   = "Backend"
$VNet1Prefix = "10.1.0.0/16"
$FEPrefix1   = "10.1.0.0/24"
$BEPrefix1   = "10.1.1.0/24"
$GwPrefix1   = "10.1.255.0/27"
$VNet1ASN    = 65010
$DNS1        = "8.8.8.8"
$Gw1         = "VNet1GW"
$GwIP1       = "VNet1GWIP"
$GwIPConf1   = "gwipconf1"
```

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Créez un groupe de ressources avec la commande [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Un groupe de ressources Azure est un conteneur logique dans lequel les ressources Azure sont déployées et gérées. Vous devez d’abord créer un groupe de ressources. Dans l’exemple suivant, un groupe de ressources nommé *TestRG1* est créé dans la région *USA Est* :

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName $RG1 -Location $Location1
```

## <a name="create-a-virtual-network"></a>Créez un réseau virtuel

Une passerelle VPN Azure fournit une connectivité entre différents locaux et des fonctionnalités de serveur VPN P2S pour votre réseau virtuel. Ajoutez la passerelle VPN à un réseau virtuel existant ou créez un réseau virtuel et la passerelle. Notez que l’exemple spécifie le nom du sous-réseau de passerelle de manière spécifique. Vous devez toujours spécifier « GatewaySubnet » comme nom du sous-réseau de passerelle afin qu’il fonctionne correctement. Cet exemple crée un réseau virtuel comprenant les trois sous-réseaux Frontend, Backend et GatewaySubnet à l’aide de [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) et de [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) :

```azurepowershell-interactive
$fesub1 = New-AzVirtualNetworkSubnetConfig -Name $FESubnet1 -AddressPrefix $FEPrefix1
$besub1 = New-AzVirtualNetworkSubnetConfig -Name $BESubnet1 -AddressPrefix $BEPrefix1
$gwsub1 = New-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -AddressPrefix $GwPrefix1
$vnet   = New-AzVirtualNetwork `
            -Name $VNet1 `
            -ResourceGroupName $RG1 `
            -Location $Location1 `
            -AddressPrefix $VNet1Prefix `
            -Subnet $fesub1,$besub1,$gwsub1
```

## <a name="request-a-public-ip-address-for-the-vpn-gateway"></a>Demander une adresse IP publique de la passerelle VPN

Les passerelles VPN Azure communiquent avec vos périphériques VPN locaux sur Internet pour effectuer les négociations IKE (Internet Key Exchange) et établir des tunnels IPsec. Créez une adresse IP publique et affectez-la à votre passerelle VPN à l’aide des applets de commande [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) et [New-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig), comme dans l’exemple ci-dessous :

> [!IMPORTANT]
> Actuellement, vous pouvez uniquement utiliser une adresse IP publique dynamique pour la passerelle. Les adresses IP statiques ne sont pas prises en charge sur les passerelles VPN Azure.

```azurepowershell-interactive
$gwpip    = New-AzPublicIpAddress -Name $GwIP1 -ResourceGroupName $RG1 `
              -Location $Location1 -AllocationMethod Dynamic
$subnet   = Get-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' `
              -VirtualNetwork $vnet
$gwipconf = New-AzVirtualNetworkGatewayIpConfig -Name $GwIPConf1 `
              -Subnet $subnet -PublicIpAddress $gwpip
```

## <a name="create-a-vpn-gateway"></a>Créer une passerelle VPN

La création de la passerelle VPN peut prendre 45 minutes, voire plus. Une fois l’opération terminée, vous pouvez créer une connexion entre votre réseau virtuel et un autre réseau virtuel. Vous pouvez également créer une connexion entre votre réseau virtuel et un emplacement local. Créez une passerelle VPN à l'aide de la cmdlet [New-AzVirtualNetworkGateway](/powershell/module/az.network/New-azVirtualNetworkGateway).

```azurepowershell-interactive
New-AzVirtualNetworkGateway -Name $Gw1 -ResourceGroupName $RG1 `
  -Location $Location1 -IpConfigurations $gwipconf -GatewayType Vpn `
  -VpnType RouteBased -GatewaySku VpnGw1
```

Valeurs des paramètres clés :
* GatewayType : utilisez **Vpn** pour les connexions de site à site, et de réseau virtuel à réseau virtuel.
* VpnType : utilisez **RouteBased** pour interagir avec une plus grande gamme de périphériques VPN et bénéficier d’autres fonctionnalités de routage.
* GatewaySku : **VpnGw1** est la valeur par défaut. Remplacez-la par une autre référence SKU VpnGw si vous avez besoin de débits plus élevés ou de connexions supplémentaires. Pour plus d’informations, consultez l’article [Références (SKU) de passerelle](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

Si vous utilisez TryIt, votre session peut expirer. C’est normal. La passerelle est quand même créée.

Une fois la passerelle créée, vous pouvez créer une connexion entre votre réseau virtuel et un autre réseau virtuel, ou entre votre réseau virtuel et un emplacement local. Vous pouvez également configurer une connexion P2S à votre réseau virtuel à partir d’un ordinateur client.

## <a name="view-the-gateway-public-ip-address"></a>Afficher l’adresse IP publique de la passerelle

Si vous connaissez le nom de l’adresse IP publique, utilisez [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) pour afficher l’adresse IP publique affectée à la passerelle.

Si votre session a expiré, copiez les paramètres réseau communs figurant au début de ce tutoriel dans votre nouvelle session, puis continuez.

```azurepowershell-interactive
$myGwIp = Get-AzPublicIpAddress -Name $GwIP1 -ResourceGroup $RG1
$myGwIp.IpAddress
```

## <a name="resize-a-gateway"></a>Redimensionner une passerelle

Vous pouvez changer la référence SKU de la passerelle VPN une fois celle-ci créée. Il existe différentes références SKU de passerelle qui prennent en charge différentes spécifications, comme le débit, le nombre de connexions, etc. L’exemple suivant utilise [Resize-AzVirtualNetworkGateway](/powershell/module/az.network/Resize-azVirtualNetworkGateway) pour redimensionner votre passerelle de VpnGw1 à VpnGw2. Pour plus d’informations, consultez l’article [Références (SKU) de passerelle](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

```azurepowershell-interactive
$gateway = Get-AzVirtualNetworkGateway -Name $Gw1 -ResourceGroup $RG1
Resize-AzVirtualNetworkGateway -GatewaySku VpnGw2 -VirtualNetworkGateway $gateway
```

Le redimensionnement d’une passerelle VPN nécessite 30 à 45 minutes environ. Notez toutefois que cette opération **n’interrompt pas et ne supprime pas** les connexions et configurations existantes.

## <a name="reset-a-gateway"></a>Réinitialiser une passerelle

Dans le cadre de la procédure de dépannage, vous pouvez réinitialiser votre passerelle VPN Azure pour forcer la passerelle VPN à redémarrer les configurations de tunnel IPsec/IKE. Utilisez [Reset-AzVirtualNetworkGateway](/powershell/module/az.network/Reset-azVirtualNetworkGateway) pour réinitialiser votre passerelle.

```azurepowershell-interactive
$gateway = Get-AzVirtualNetworkGateway -Name $Gw1 -ResourceGroup $RG1
Reset-AzVirtualNetworkGateway -VirtualNetworkGateway $gateway
```

Pour plus d’informations, consultez [Réinitialiser une passerelle VPN](./reset-gateway.md).

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous passez au [tutoriel suivant](./vpn-gateway-create-site-to-site-rm-powershell.md), conservez ces ressources car elles vous seront demandées en prérequis.

Toutefois, si la passerelle fait partie d’un déploiement utilisé comme prototype, test ou preuve de concept, utilisez la commande [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) pour supprimer le groupe de ressources, la passerelle VPN et toutes les ressources associées.

```azurepowershell-interactive
Remove-AzResourceGroup -Name $RG1
```

## <a name="next-steps"></a>Étapes suivantes

Ce tutoriel vous a montré les tâches de base de création et de gestion d’une passerelle VPN, notamment :

> [!div class="checklist"]
> * Créer une passerelle VPN
> * Afficher l’adresse IP publique
> * Redimensionner une passerelle VPN
> * Réinitialiser une passerelle VPN

Passez ensuite au tutoriel suivant :

> [!div class="nextstepaction"]
> * [Créer une connexion S2S](vpn-gateway-create-site-to-site-rm-powershell.md)