---
title: 'Démarrage rapide : Créer et configurer Route Server à l’aide d’Azure PowerShell'
description: Dans ce guide de démarrage rapide, vous apprenez à créer et à configurer un serveur de routage à l’aide d’Azure PowerShell.
services: route-server
author: duongau
ms.author: duau
ms.date: 04/23/2021
ms.topic: quickstart
ms.service: route-server
ms.custom: devx-track-azurepowershell - mode-api
ms.openlocfilehash: 2000c30a96f241ff5500552277f16b8b789ba8c9
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/29/2021
ms.locfileid: "110690795"
---
# <a name="quickstart-create-and-configure-route-server-using-azure-powershell"></a>Démarrage rapide : Créer et configurer Route Server à l’aide d’Azure PowerShell

Cet article vous aide à configurer Azure Route Server pour effectuer un peering avec une appliance virtuelle réseau (NVA) dans votre réseau virtuel à l’aide de PowerShell. Azure Route Server va apprendre les routes provenant de l’appliance virtuelle réseau et il les programmera sur les machines virtuelles du réseau virtuel. Azure Route Server publiera également les routes du réseau virtuel menant à l’appliance virtuelle réseau. Pour plus d’informations, consultez [Azure Route Server](overview.md).

:::image type="content" source="media/quickstart-configure-route-server-portal/environment-diagram.png" alt-text="Diagramme de l’environnement de déploiement du serveur de routes avec Azure PowerShell." border="false":::

> [!IMPORTANT]
> Azure Route Server (préversion) est en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prérequis

* Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Assurez-vous que vous disposez des tout derniers modules PowerShell, sinon vous pouvez utiliser Azure Cloud Shell dans le portail.
* Vérifiez les [limites du service pour Azure Route Server](route-server-faq.md#limitations).

## <a name="create-a-route-server"></a>Créer un serveur de routage

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Vous connecter à votre compte Azure et sélectionner votre abonnement.

[!INCLUDE [sign in](../../includes/expressroute-cloud-shell-connect.md)]

### <a name="create-a-resource-group-and-virtual-network"></a>Créer un groupe de ressources et un réseau virtuel

Avant de pouvoir créer un serveur de routage Azure, vous devez prévoir un réseau virtuel pour héberger le déploiement. Utilisez la commande suivante pour créer un groupe de ressources et un réseau virtuel. Si vous disposez déjà d’un réseau virtuel, vous pouvez passer à la section suivante.

```azurepowershell-interactive
New-AzResourceGroup –Name "RouteServerRG” -Location “West US"
New-AzVirtualNetwork –ResourceGroupName "RouteServerRG" -Location "West US" -Name myVirtualNetwork –AddressPrefix 10.0.0.0/16
```

### <a name="add-a-subnet"></a>Ajouter un sous-réseau

1. Ajoutez un sous-réseau nommé *RouteServerSubnet*, dans lequel déployer le serveur de routage Azure. Ce sous-réseau est un sous-réseau dédié uniquement pour Azure Route Server. RouteServerSubnet doit correspondre à /27 ou à un préfixe plus court (comme /26, /25), à défaut de quoi un message d’erreur s’affichera lorsque vous ajouterez le serveur de routage Azure.

    ```azurepowershell-interactive
    $vnet = Get-AzVirtualNetwork –Name "myVirtualNetwork" - ResourceGroupName "RouteServerRG"
    Add-AzVirtualNetworkSubnetConfig –Name "RouteServerSubnet" -AddressPrefix 10.0.0.0/24 -VirtualNetwork $vnet
    $vnet | Set-AzVirtualNetwork
    ```

1. Obtenez l’ID RouteServerSubnet. Pour afficher l’ID de ressource de tous les sous-réseaux du réseau virtuel, utilisez la commande suivante :

    ```azurepowershell-interactive
    $vnet = Get-AzVirtualNetwork –Name "vnet_name" -ResourceGroupName "RouteServerRG"
    $vnet.Subnets
    ```

L’ID RouteServerSubnet ressemble à ce qui suit :

`/subscriptions/<subscriptionID>/resourceGroups/RouteServerRG/providers/Microsoft.Network/virtualNetworks/myVirtualNetwork/subnets/RouteServerSubnet`

## <a name="create-the-route-server"></a>Créer le serveur de routage

Créez le serveur de routage à l’aide de la commande suivante :

```azurepowershell-interactive 
New-AzRouteServer -RouteServerName myRouteServer -ResourceGroupName RouteServerRG -Location "West US" -HostedSubnet "RouteServerSubnet_ID"
```

L’emplacement doit correspondre à l’emplacement de votre réseau virtuel. HostedSubnet est l’ID de RouteServerSubnet que vous avez obtenu à la section précédente.

## <a name="create-peering-with-an-nva"></a>Créer un peering avec une appliance virtuelle réseau

Utilisez la commande suivante pour établir le peering BGP, du serveur de routage à l’appliance virtuelle réseau :

```azurepowershell-interactive 
Add-AzRouteServerPeer -PeerName "myNVA" -PeerIp "nva_ip" -PeerAsn "nva_asn" -RouteServerName myRouteServer -ResourceGroupName RouteServerRG
```

« nva_ip » est l’adresse IP de réseau virtuel affectée à l’appliance virtuelle réseau. « nva_asn » est le numéro de système autonome (NSA) configuré dans l’appliance virtuelle réseau. Le NSA peut être n’importe quel nombre de 16 bits situé en dehors de la plage 65515-65520. Cette plage de NSA est réservée par Microsoft.

Pour configurer le peering avec différentes appliances virtuelles réseau, ou une autre instance de la même appliance virtuelle réseau à des fins de redondance, utilisez la commande suivante :

```azurepowershell-interactive 
Add-AzRouteServerPeer -PeerName "NVA2_name" -PeerIp "nva2_ip" -PeerAsn "nva2_asn" -RouteServerName myRouteServer -ResourceGroupName RouteServerRG 
```

## <a name="complete-the-configuration-on-the-nva"></a>Terminer la configuration sur l’appliance virtuelle réseau

Pour achever la configuration sur l’appliance virtuelle réseau et activer les sessions BGP, vous avez besoin de l’adresse IP et du NSA d’Azure Route Server. Vous pouvez récupérer ces informations au moyen de cette commande :

```azurepowershell-interactive 
Get-AzRouteServer -RouterServerName myRouteServer -ResourceGroupName RouteServerRG
```

La sortie contient les informations suivantes :

``` 
RouteServerAsn : 65515
RouteServerIps : {10.5.10.4, 10.5.10.5}
```

## <a name="configure-route-exchange"></a><a name = "route-exchange"></a>Configurer l’échange de routage

Si vous disposez d’une passerelle ExpressRoute et d’une passerelle VPN Azure dans le même réseau virtuel, et que vous souhaitez qu’elles échangent des routes, vous pouvez activer l’échange de routage sur le serveur de routage Azure.

1. Pour activer l’échange de routage entre Azure Route Server et la ou les passerelles, utilisez cette commande :

```azurepowershell-interactive 
Update-AzRouteServer -RouteServerName myRouteServer -ResourceGroupName RouteServerRG -AllowBranchToBranchTraffic 
```

2. Pour désactiver l’échange de routage entre Azure Route Server et la ou les passerelles, utilisez cette commande :

```azurepowershell-interactive 
Update-AzRouteServer -RouteServerName myRouteServer -ResourceGroupName RouteServerRG
```

## <a name="troubleshooting"></a>Dépannage

Vous pouvez afficher les routes publiées et reçues par Azure Route Server à l’aide de cette commande :

```azurepowershell-interactive
Get-AzRouteServerPeerAdvertisedRoute
Get-AzRouteServerPeerLearnedRoute
```
## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous n’avez plus besoin du serveur de routage Azure, utilisez ces commandes pour supprimer le peering BGP, puis le serveur de routage. 

1. Supprimez le peering BGP entre Azure Route Server et une appliance virtuelle réseau à l’aide de cette commande :

```azurepowershell-interactive 
Remove-AzRouteServerPeer -PeerName "nva_name" -RouteServerName myRouteServer -ResourceGroupName RouteServerRG 
```

2. Supprimez Azure Route Server au moyen de cette commande :

```azurepowershell-interactive 
Remove-AzRouteServer -RouteServerName myRouteServer -ResourceGroupName RouteServerRG
```

## <a name="next-steps"></a>Étapes suivantes

Après avoir créé le serveur de routage Azure, découvrez à présent de quelle façon Azure Route Server interagit avec les passerelles VPN et ExpressRoute : 

> [!div class="nextstepaction"]
> [Prise en charge d’Azure ExpressRoute et de VPN Azure](expressroute-vpn-support.md)
