---
title: 'Démarrage rapide : Créer et configurer Route Server à l’aide d’Azure PowerShell'
description: Dans ce guide de démarrage rapide, vous apprenez à créer et à configurer un serveur de routage à l’aide d’Azure PowerShell.
services: route-server
author: duongau
ms.author: duau
ms.date: 09/01/2021
ms.topic: quickstart
ms.service: route-server
ms.custom: devx-track-azurepowershell - mode-api
ms.openlocfilehash: 65f5957d52dcf510601f4a4773cde4c8a477dc97
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2021
ms.locfileid: "123475617"
---
# <a name="quickstart-create-and-configure-route-server-using-azure-powershell"></a>Démarrage rapide : Créer et configurer Route Server à l’aide d’Azure PowerShell

Cet article vous aide à configurer Serveur de routes Azure pour effectuer un peering avec une appliance virtuelle réseau (NVA) dans votre réseau virtuel à l’aide d’Azure PowerShell. Serveur de routes apprend les routes provenant de votre appliance virtuelle réseau et il les programme sur les machines virtuelles du réseau virtuel. Azure Route Server publiera également les routes du réseau virtuel menant à l’appliance virtuelle réseau. Pour plus d’informations, consultez [Serveur de routes Azure](overview.md).

:::image type="content" source="media/quickstart-configure-route-server-portal/environment-diagram.png" alt-text="Diagramme de l’environnement de déploiement du serveur de routes avec Azure PowerShell." border="false":::

## <a name="prerequisites"></a>Prérequis

* Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Assurez-vous que vous disposez des tout derniers modules PowerShell, sinon vous pouvez utiliser Azure Cloud Shell dans le portail.
* Vérifiez les [limites du service pour Azure Route Server](route-server-faq.md#limitations).
* Si vous exécutez PowerShell en local, vous devez également exécuter `Connect-AzAccount` pour créer une connexion avec Azure.

## <a name="create-resource-group-and-a-virtual-network"></a>Créer un groupe de ressources et un réseau virtuel

### <a name="create-a-resource-group"></a>Créer un groupe de ressources 

Avant de pouvoir créer un serveur de routes Azure, vous devez créer un groupe de ressources pour l’héberger. Créez un groupe de ressources avec [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup). Cet exemple crée un groupe de ressources nommé **myRouteServerRG** dans la localisation **WestUS** :

```azurepowershell-interactive
$rg = @{
    Name = 'myRouteServerRG'
    Location = 'WestUS'
}
New-AzResourceGroup @rg
```

### <a name="create-a-virtual-network"></a>Créez un réseau virtuel

Créez un réseau virtuel avec [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). Cet exemple crée un réseau virtuel par défaut nommé **myVirtualNetwork** dans la localisation **WestUS** : si vous disposez déjà d’un réseau virtuel, vous pouvez passer à la section suivante.

```azurepowershell-interactive
$vnet = @{
    Name = 'myVirtualNetwork'
    ResourceGroupName = 'myRouteServerRG'
    Location = 'WestUS'
    AddressPrefix = '10.0.0.0/16'    
}
$virtualNetwork = New-AzVirtualNetwork @vnet
```

### <a name="add-a-dedicated-subnet"></a>Ajouter un sous-réseau dédié

Serveur de routes Azure nécessite un sous-réseau dédié nommé *RouteServerSubnet*. La taille du sous-réseau doit être au moins égale à 27 ou un préfixe abrégé (par exemple /26 ou /25), sinon vous recevez un message d’erreur lors du déploiement du serveur de routes. Créez une configuration de sous-réseau nommée **RouteServerSubnet** avec [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) :

```azurepowershell-interactive
$subnet = @{
    Name = 'RouteServerSubnet'
    VirtualNetwork = $virtualNetwork
    AddressPrefix = '10.0.0.0/24'
}
$subnetConfig = Add-AzVirtualNetworkSubnetConfig @subnet

$virtualnetwork | Set-AzVirtualNetwork
```

## <a name="create-the-route-server"></a>Créer le serveur de routage

1. Pour garantir la connectivité au service back-end qui gère la configuration de Serveur de routes, l’affectation d’une adresse IP publique est nécessaire. Créez une adresse IP publique standard nommée **RouteServerIP** avec [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) :

    ```azurepowershell-interactive
    $ip = @{
        Name = 'myRouteServerIP'
        ResourceGroupName = 'myRouteServerRG'
        Location = 'WestUS'
        AllocationMethod = 'Static'
        IpAddressVersion = 'Ipv4'
        Sku = 'Standard'
    }
    $publicIp = New-AzPublicIpAddress @ip
    ```
    
2. Créez le serveur de routes Azure avec [New-AzRouteServer](/powershell/module/az.network/new-azrouteserver). Cet exemple crée un serveur de routes Azure nommé **myRouteServer** dans la localisation **WestUS**. *HostedSubnet* est l’ID de ressource du RouteServerSubnet créé dans la section précédente.

    ```azurepowershell-interactive
    $rs = @{
        RouteServerName = 'myRouteServer'
        ResourceGroupName = 'myRouteServerRG'
        Location = 'WestUS'
        HostedSubnet = $subnetConfig.Id
        PublicIP = $publicIp
    }
    New-AzRouteServer @rs 
    ```

## <a name="create-bgp-peering-with-an-nva"></a>Créer un peering BGP avec une appliance virtuelle réseau

Pour établir un peering BGP à partir du serveur de routes vers votre appliance virtuelle réseau, utilisez [Add-AzRouteServerPeer](/powershell/module/az.network/add-azrouteserverpeer) :

« your_nva_ip » est l’adresse IP du réseau virtuel affectée à l’appliance virtuelle réseau. « your_nva_asn » est le numéro de système autonome (NSA) configuré dans l’appliance virtuelle réseau. Le NSA peut être n’importe quel nombre de 16 bits situé en dehors de la plage 65515-65520. Cette plage de NSA est réservée par Microsoft.

```azurepowershell-interactive
$peer = @{
    PeerName = 'myNVA"
    PeerIp = '192.168.0.1'
    PeerAsn = '65501'
    RouteServerName = 'myRouteServer'
    ResourceGroupName = myRouteServerRG'
}
Add-AzRouteServerPeer @peer
```

Pour configurer le peering avec une autre appliance virtuelle réseau ou une autre instance de la même appliance pour la redondance, utilisez la même commande que ci-dessus avec des valeurs de *PeerName*, *PeerIp* et *PeerAsn* différentes.

## <a name="complete-the-configuration-on-the-nva"></a>Terminer la configuration sur l’appliance virtuelle réseau

Pour achever la configuration sur l’appliance virtuelle réseau et activer les sessions BGP, vous avez besoin de l’adresse IP et du NSA d’Azure Route Server. Vous pouvez obtenir ces informations en utilisant [Get-AzRouteServer](/powershell/module/az.network/get-azrouteserver) :

```azurepowershell-interactive
$routeserver = @{
    RouteServerName = 'myRouteServer'
    ResourcGroupName = 'myRouteServerRG'
} 
Get-AzRouteServer @routeserver
```

La sortie se présente comme suit :

``` 
RouteServerAsn : 65515
RouteServerIps : {10.5.10.4, 10.5.10.5}
```

## <a name="configure-route-exchange"></a><a name = "route-exchange"></a>Configurer l’échange de routage

Si vous disposez d’ExpressRoute et d’une passerelle VPN Azure dans le même réseau virtuel, et que vous souhaitez qu’ils échangent des routes, vous pouvez activer l’échange de routes sur le serveur de routes Azure.

1. Pour activer l’échange de routes entre le serveur de routes Azure et la ou les passerelles, utilisez [Update-AzRouteServer](/powershell/module/az.network/update-azrouteserver) avec l’indicateur *-AllowBranchToBranchTraffic* :

```azurepowershell-interactive
$routeserver = @{
    RouteServerName = 'myRouteServer'
    ResourcGroupName = 'myRouteServerRG'
    AllowBranchToBranchTraffic
}  
Update-AzRouteServer @routeserver 
```

2. Pour désactiver l’échange de routes entre le serveur de routes Azure et la ou les passerelles, utilisez [Update-AzRouteServer](/powershell/module/az.network/update-azrouteserver) sans l’indicateur *-AllowBranchToBranchTraffic* :

```azurepowershell-interactive
$routeserver = @{
    RouteServerName = 'myRouteServer'
    ResourcGroupName = 'myRouteServerRG'
}  
Update-AzRouteServer @routeserver 
```

## <a name="troubleshooting"></a>Dépannage

Utilisez [Get-AzRouteServerPeerAdvertisedRoute](/powershell/module/az.network/get-azrouteserverpeeradvertisedroute) pour afficher les routes annoncées par le serveur de routes Azure.

```azurepowershell-interactive
$remotepeer = @{
    RouteServerName = 'myRouteServer'
    ResourcGroupName = 'myRouteServerRG'
    PeerName = 'myNVA'
}
Get-AzRouteServerPeerAdvertisedRoute @routeserver
```

Utilisez [Get-AzRouteServerPeerLearnedRoute](/powershell/module/az.network/get-azrouteserverpeerlearnedroute) pour afficher les routes apprises par le serveur de routes Azure.

```azurepowershell-interactive
$routeserver = @{
    RouteServerName = 'myRouteServer'
    ResourcGroupName = 'myRouteServerRG'
    AllowBranchToBranchTraffic
}  
Get-AzRouteServerPeerLearnedRoute @routeserver
```
## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous n’avez plus besoin du serveur de routes Azure, utilisez la première commande pour supprimer le peering BGP, puis la seconde pour supprimer le serveur de routes. 

1. Supprimez le peering BGP entre le serveur de routes Azure et une appliance virtuelle réseau avec [Remove-AzRouteServerPeer](/powershell/module/az.network/remove-azrouteserverpeer) :

```azurepowershell-interactive
$peer = @{
    PeerName = 'myNVA'
    RouteServerName = 'myRouteServer'
    ResourceGroupName = 'myRouteServerRG'
} 
Remove-AzRouteServerPeer @peer
```

2. Supprimez le serveur de routes Azure avec [Remove-AzRouteServer](/powershell/module/az.network/remove-azrouteserver) :

```azurepowershell-interactive 
$routeserver = @{
    RouteServerName = 'myRouteServer'
    ResourceGroupName = 'myRouteServerRG'
} 
Remove-AzRouteServer @routeserver
```

## <a name="next-steps"></a>Étapes suivantes

Après avoir créé le serveur de routes Azure, découvrez à présent de quelle façon Serveur de routes Azure interagit avec les passerelles VPN et ExpressRoute : 

> [!div class="nextstepaction"]
> [Prise en charge d’Azure ExpressRoute et de VPN Azure](expressroute-vpn-support.md)
