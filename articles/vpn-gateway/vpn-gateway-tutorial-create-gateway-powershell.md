---
title: Créer et gérer une passerelle VPN Azure à l’aide de PowerShell | Microsoft Docs
description: 'Tutoriel : Créer et gérer une passerelle VPN avec le module Azure PowerShell'
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure
ms.date: 05/14/2018
ms.author: yushwang
ms.custom: mvc
ms.openlocfilehash: b1435773f8d05f9cc730e5745c1a916d9b74321f
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/31/2018
ms.locfileid: "43340591"
---
# <a name="create-and-manage-vpn-gateway-with-the-azure-powershell-module"></a>Créer et gérer une passerelle VPN avec le module Azure PowerShell

Les passerelles VPN Azure fournissent une connectivité entre les locaux du client et Azure. Ce tutoriel décrit les éléments de base du déploiement d’une passerelle VPN Azure, notamment la création et la gestion d’une passerelle VPN. Vous allez apprendre à effectuer les actions suivantes :

> [!div class="checklist"]
> * Créer une passerelle VPN
> * Redimensionner une passerelle VPN
> * Réinitialiser une passerelle VPN

Le diagramme suivant illustre le réseau virtuel et la passerelle VPN créés dans le cadre de ce tutoriel.

![Réseau virtuel et passerelle VPN](./media/vpn-gateway-tutorial-create-gateway-powershell/vnet1-gateway.png)

### <a name="azure-cloud-shell-and-azure-powershell"></a>Azure Cloud Shell et Azure PowerShell

[!INCLUDE [working with cloudshell](../../includes/vpn-gateway-cloud-shell-powershell.md)]

Si vous choisissez d’installer et d’utiliser PowerShell en local, vous devez exécuter le module Azure PowerShell version 5.3 ou version ultérieure pour les besoins de ce didacticiel. Exécutez `Get-Module -ListAvailable AzureRM` pour trouver la version. Si vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-azurerm-ps). Si vous exécutez PowerShell en local, vous devez également lancer `Login-AzureRmAccount` pour créer une connexion avec Azure. 

## <a name="common-network-parameter-values"></a>Valeurs des paramètres réseau communs

Changez les valeurs ci-dessous en fonction de l’environnement et de la configuration réseau.

```azurepowershell-interactive
$RG1         = "TestRG1"
$VNet1       = "VNet1"
$Location1   = "East US"
$FESubnet1   = "FrontEnd"
$BESubnet1   = "Backend"
$GwSubnet1   = "GatewaySubnet"
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

## <a name="create-resource-group"></a>Créer un groupe de ressources

Créez un groupe de ressources avec la commande [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Un groupe de ressources Azure est un conteneur logique dans lequel les ressources Azure sont déployées et gérées. Vous devez d’abord créer un groupe de ressources. Dans l’exemple suivant, un groupe de ressources nommé *TestRG1* est créé dans la région *USA Est* :

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName $RG1 -Location $Location1
```

## <a name="create-a-virtual-network"></a>Créez un réseau virtuel

Une passerelle VPN Azure fournit une connectivité entre différents locaux et des fonctionnalités de serveur VPN P2S pour votre réseau virtuel. Ajoutez la passerelle VPN à un réseau virtuel existant ou créez un réseau virtuel et la passerelle. Cet exemple crée un réseau virtuel avec trois sous-réseaux (Frontend, Backend et GatewaySubnet) à l’aide des applets de commande [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) et [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) :

```azurepowershell-interactive
$fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubnet1 -AddressPrefix $FEPrefix1
$besub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubnet1 -AddressPrefix $BEPrefix1
$gwsub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubnet1 -AddressPrefix $GwPrefix1
$vnet   = New-AzureRmVirtualNetwork `
            -Name $VNet1 `
            -ResourceGroupName $RG1 `
            -Location $Location1 `
            -AddressPrefix $VNet1Prefix `
            -Subnet $fesub1,$besub1,$gwsub1
```

## <a name="request-a-public-ip-address-for-the-vpn-gateway"></a>Demander une adresse IP publique de la passerelle VPN

Les passerelles VPN Azure communiquent avec vos périphériques VPN locaux sur Internet pour effectuer les négociations IKE (Internet Key Exchange) et établir des tunnels IPsec. Créez une adresse IP publique et affectez-la à votre passerelle VPN à l’aide des applets de commande [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) et [New-AzureRmVirtualNetworkGatewayIpConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworkgatewayipconfig), comme dans l’exemple ci-dessous :

> [!IMPORTANT]
> Actuellement, vous pouvez uniquement utiliser une adresse IP publique dynamique pour la passerelle. Les adresses IP statiques ne sont pas prises en charge sur les passerelles VPN Azure.

```azurepowershell-interactive
$gwpip    = New-AzureRmPublicIpAddress -Name $GwIP1 -ResourceGroupName $RG1 `
              -Location $Location1 -AllocationMethod Dynamic
$subnet   = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' `
              -VirtualNetwork $vnet
$gwipconf = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GwIPConf1 `
              -Subnet $subnet -PublicIpAddress $gwpip
```

## <a name="create-vpn-gateway"></a>Créer la passerelle VPN

La création de la passerelle VPN peut prendre 45 minutes, voire plus. Une fois l’opération terminée, vous pouvez créer une connexion entre votre réseau virtuel et un autre réseau virtuel. Vous pouvez également créer une connexion entre votre réseau virtuel et un emplacement local. Créez une passerelle VPN à l’aide de l’applet de commande [New-AzureRmVirtualNetworkGateway](/powershell/module/azurerm.network/New-AzureRmVirtualNetworkGateway).

```azurepowershell-interactive
New-AzureRmVirtualNetworkGateway -Name $Gw1 -ResourceGroupName $RG1 `
  -Location $Location1 -IpConfigurations $gwipconf -GatewayType Vpn `
  -VpnType RouteBased -GatewaySku VpnGw1
```

Valeurs des paramètres clés :
* GatewayType : Utilisez **Vpn** pour les connexions de site à site et de réseau virtuel à réseau virtuel.
* VpnType : Utilisez **RouteBased** pour interagir avec une plus grande gamme de périphériques VPN et bénéficier d’autres fonctionnalités de routage.
* GatewaySku : **VpnGw1** est la valeur par défaut. Remplacez-la par VpnGw2 ou VpnGw3 si vous avez besoin de débits plus élevés ou de connexions supplémentaires. Pour plus d’informations, consultez l’article [Références (SKU) de passerelle](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

Une fois la passerelle créée, vous pouvez créer une connexion entre votre réseau virtuel et un autre réseau virtuel, ou entre votre réseau virtuel et un emplacement local. Vous pouvez également configurer une connexion P2S à votre réseau virtuel à partir d’un ordinateur client.

## <a name="resize-vpn-gateway"></a>Redimensionner la passerelle VPN

Vous pouvez changer la référence SKU de la passerelle VPN une fois celle-ci créée. Il existe différentes références SKU de passerelle qui prennent en charge différentes spécifications, comme le débit, le nombre de connexions, etc. L’exemple suivant utilise [Resize-AzureRmVirtualNetworkGateway](/powershell/module/azurerm.network/Resize-AzureRmVirtualNetworkGateway) pour redimensionner la passerelle de VpnGw1 à VpnGw2. Pour plus d’informations, consultez l’article [Références (SKU) de passerelle](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

```azurepowershell-interactive
$gw = Get-AzureRmVirtualNetworkGateway -Name $Gw1 -ResourceGroup $RG1
Resize-AzureRmVirtualNetworkGateway -GatewaySku VpnGw2 -VirtualNetworkGateway $gateway
```

Le redimensionnement d’une passerelle VPN nécessite 30 à 45 minutes environ. Notez toutefois que cette opération **n’interrompt pas et ne supprime pas** les connexions et configurations existantes.

## <a name="reset-vpn-gateway"></a>Réinitialiser la passerelle VPN

Dans le cadre de la procédure de dépannage, vous pouvez réinitialiser votre passerelle VPN Azure pour forcer la passerelle VPN à redémarrer les configurations de tunnel IPsec/IKE. Utilisez [Reset-AzureRmVirtualNetworkGateway](/powershell/module/azurerm.network/Reset-AzureRmVirtualNetworkGateway) pour réinitialiser votre passerelle.

```azurepowershell-interactive
$gw = Get-AzureRmVirtualNetworkGateway -Name $Gw1 -ResourceGroup $RG1
Reset-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gateway
```

Pour plus d’informations, consultez [Réinitialiser une passerelle VPN](vpn-gateway-resetgw-classic.md).

## <a name="get-the-gateway-public-ip-address"></a>Obtenir l’adresse IP publique de la passerelle

Si vous connaissez le nom de l’adresse IP publique, utilisez [Get-AzureRmPublicIpAddress](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermpublicipaddress?view=azurermps-6.8.1) pour afficher l’adresse IP publique affectée à la passerelle.

```azurepowershell-interactive
$myGwIp = Get-AzureRmPublicIpAddress -Name $GwIP1 -ResourceGroup $RG1
$myGwIp.IpAddress
```

## <a name="delete-vpn-gateway"></a>Supprimer la passerelle VPN

Une configuration complète d’une connectivité entre différents locaux et de réseau virtuel à réseau virtuel nécessite, outre une passerelle VPN, plusieurs types de ressources. Supprimez les connexions associées à la passerelle VPN avant de supprimer la passerelle. Une fois la passerelle supprimée, supprimez la ou les adresses IP publiques pour la passerelle. Pour obtenir des étapes détaillées, consultez [Supprimer une passerelle VPN](vpn-gateway-delete-vnet-gateway-powershell.md).

Si la passerelle fait partie d’un déploiement utilisé comme prototype ou preuve de concept, utilisez la commande [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) pour supprimer le groupe de ressources, la passerelle VPN et toutes les ressources associées.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name $RG1
```

## <a name="next-steps"></a>Étapes suivantes

Ce tutoriel vous a montré les tâches de base de création et de gestion d’une passerelle VPN, notamment :

> [!div class="checklist"]
> * Créer une passerelle VPN
> * Redimensionner une passerelle VPN
> * Réinitialiser une passerelle VPN

Pour en savoir plus sur les connexions S2S, P2S et de réseau virtuel à réseau virtuel, accédez aux tutoriels suivants.

> [!div class="nextstepaction"]
> * [Créer des connexions S2S](vpn-gateway-tutorial-vpnconnection-powershell.md)
> * [Créer des connexions de réseau virtuel à réseau virtuel](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [Créer des connexions P2S](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
