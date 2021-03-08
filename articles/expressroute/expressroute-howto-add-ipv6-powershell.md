---
title: 'Azure ExpressRoute : ajouter la prise en charge d’IPv6 à l’aide d’Azure PowerShell'
description: Découvrez comment ajouter la prise en charge d’IPv6 pour se connecter à des déploiements Azure à l’aide d’Azure PowerShell.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 2/9/2021
ms.author: duau
ms.openlocfilehash: 402714b55d7513e41458503b12c68768d0c6ad5e
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101743245"
---
# <a name="add-ipv6-support-for-private-peering-using-azure-powershell-preview"></a>Ajouter la prise en charge d’IPv6 pour le Peering privé à l’aide d’Azure PowerShell (préversion)

Cet article explique comment ajouter la prise en charge d’IPv6 pour se connecter via ExpressRoute à vos ressources dans Azure à l’aide d’Azure PowerShell.

> [!Note]
> Cette fonctionnalité est actuellement disponible en préversion dans les [régions Azure avec Zones de disponibilité](https://docs.microsoft.com/azure/availability-zones/az-region#azure-regions-with-availability-zones). Votre circuit ExpressRoute peut donc être créé à l’aide de n’importe quel emplacement de Peering, mais les déploiements basés sur IPv6 auxquels il se connecte doivent se trouver dans une région avec Zones de disponibilité.

## <a name="working-with-azure-powershell"></a>Utilisation d’Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="register-for-public-preview"></a>S’inscrire pour la préversion publique
Avant d’ajouter la prise en charge d’IPv6, vous devez inscrire votre abonnement. Pour vous inscrire, procédez comme suit via Azure PowerShell :
1.  Connectez-vous à Azure et sélectionnez l’abonnement. Vous devez effectuer cette opération pour l’abonnement contenant votre circuit ExpressRoute, ainsi que l’abonnement contenant vos déploiements Azure (s’ils sont différents).

    ```azurepowershell-interactive
    Connect-AzAccount 

    Select-AzSubscription -Subscription "<SubscriptionID or SubscriptionName>"
    ```

2. Demandez à inscrire votre abonnement pour la préversion publique à l’aide de la commande suivante :
    ```azurepowershell-interactive
    Register-AzProviderFeature -FeatureName AllowIpv6PrivatePeering -ProviderNamespace Microsoft.Network
    ```

Votre requête sera ensuite approuvée par l’équipe ExpressRoute dans un délai de 2-3 jours ouvrables.

## <a name="add-ipv6-private-peering-to-your-expressroute-circuit"></a>Ajouter le peering privé IPv6 à votre circuit ExpressRoute

1. [Créez un circuit ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-howto-circuit-arm) ou utilisez un circuit existant. Récupérez le circuit en exécutant la commande **Get-AzExpressRouteCircuit** :

    ```azurepowershell-interactive
    $ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
    ```

2. Récupérez la configuration de Peering privé pour le circuit en exécutant la commande **AzExpressRouteCircuitPeeringConfig** :

    ```azurepowershell-interactive
    Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt
    ```

3. Ajoutez un Peering privé IPv6 à votre configuration de Peering privé IPv4. Indiquez une paire de sous-réseaux IPv6 /126 dont vous êtes propriétaire pour votre lien principal et vos liens secondaires. À partir de chacun de ces sous-réseaux, vous allez attribuer la première adresse IP utilisable à votre routeur. Microsoft utilise la deuxième IP utilisable pour son routeur.

> [!Note]
> L’ASN et le VlanId de l’homologue doivent correspondre à ceux de votre configuration de Peering privé IPv4.

    ```azurepowershell-interactive
    Set-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "3FFE:FFFF:0:CD30::/126" -SecondaryPeerAddressPrefix "3FFE:FFFF:0:CD30::4/126" -VlanId 200 -PeerAddressType IPv6

    Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
    ```

4. Une fois que la configuration a été enregistrée avec succès, récupérez à nouveau le circuit en exécutant la commande **Az-ExpressRouteCircuit**. La réponse doit être similaire à l’exemple suivant :

    ```azurepowershell
    Name                             : ExpressRouteARMCircuit
    ResourceGroupName                : ExpressRouteResourceGroup
    Location                         : eastus
    Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
    Etag                             : W/"################################"
    ProvisioningState                : Succeeded
    Sku                              : {
                                         "Name": "Standard_MeteredData",
                                         "Tier": "Standard",
                                         "Family": "MeteredData"
                                       }
    CircuitProvisioningState         : Enabled
    ServiceProviderProvisioningState : Provisioned
    ServiceProviderNotes             :
    ServiceProviderProperties        : {
                                         "ServiceProviderName": "Equinix",
                                         "PeeringLocation": "Washington DC",
                                         "BandwidthInMbps": 50
                                       }
    ExpressRoutePort                 : null
    BandwidthInGbps                  :
    Stag                             : 29
    ServiceKey                       : **************************************
    Peerings                         : [
                                         {
                                           "Name": "AzurePrivatePeering",
                                           "Etag": "W/\"facc8972-995c-4861-a18d-9a82aaa7167e\"",
                                           "Id": "/subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit/peerings/AzurePrivatePeering",
                                           "PeeringType": "AzurePrivatePeering",
                                           "State": "Enabled",
                                           "AzureASN": 12076,
                                           "PeerASN": 100,
                                           "PrimaryPeerAddressPrefix": "192.168.15.16/30",
                                           "SecondaryPeerAddressPrefix": "192.168.15.20/30",
                                           "PrimaryAzurePort": "",
                                           "SecondaryAzurePort": "",
                                           "VlanId": 200,
                                           "ProvisioningState": "Succeeded",
                                           "GatewayManagerEtag": "",
                                           "LastModifiedBy": "Customer",
                                           "Ipv6PeeringConfig": {
                                             "State": "Enabled",
                                             "PrimaryPeerAddressPrefix": "3FFE:FFFF:0:CD30::/126",
                                             "SecondaryPeerAddressPrefix": "3FFE:FFFF:0:CD30::4/126"
                                           },
                                           "Connections": [],
                                           "PeeredConnections": []
                                         },
                                       ]
    Authorizations                   : []
    AllowClassicOperations           : False
    GatewayManagerEtag               :
    ```

## <a name="update-your-connection-to-an-existing-virtual-network"></a>Mettre à jour votre connexion à un réseau virtuel existant

Suivez les étapes ci-dessous si vous disposez d’un environnement de ressources Azure existant dans une région avec Zones de disponibilité que vous souhaitez utiliser avec votre Peering privé IPv6.

1. Récupérez réseau virtuel auquel votre circuit ExpressRoute est connecté.

    ```azurepowershell-interactive
    $vnet = Get-AzVirtualNetwork -Name "VirtualNetwork" -ResourceGroupName "ExpressRouteResourceGroup"
    ```

2. Ajoutez un espace d’adressage IPv6 à votre réseau virtuel.

    ```azurepowershell-interactive
    $vnet.AddressSpace.AddressPrefixes.add("ace:daa:daaa:deaa::/64")
    Set-AzVirtualNetwork -VirtualNetwork $vnet
    ```

3. Ajoutez un espace d’adressage IPv6 à votre sous-réseau de passerelle. Le sous-réseau IPv6 de passerelle doit être /64 ou plus.

    ```azurepowershell-interactive
    Set-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet -AddressPrefix "10.0.0.0/26", "ace:daa:daaa:deaa::/64"
    Set-AzVirtualNetwork -VirtualNetwork $vnet
    ```

4. Si vous disposez d’une passerelle existante redondante dans une zone, exécutez la commande suivante pour activer la connectivité IPv6. Sinon, [créez la passerelle de réseau virtuel](https://docs.microsoft.com/azure/expressroute/expressroute-howto-add-gateway-resource-manager) à l’aide d’une référence SKU redondante dans une zone (ErGw1AZ, ErGw2AZ, ErGw3AZ).

    ```azurepowershell-interactive
    $gw = Get-AzVirtualNetworkGateway -Name "GatewayName" -ResourceGroupName "ExpressRouteResourceGroup"
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw
    ```

## <a name="create-a-connection-to-a-new-virtual-network"></a>Créer une connexion à un nouveau réseau virtuel

Suivez les étapes ci-dessous si vous envisagez de vous connecter à un nouvel ensemble de ressources Azure dans une région avec Zones de disponibilité à l’aide de votre Peering privé IPv6.

1. Créez un réseau virtuel à double pile avec l’espace d’adressage IPv4 et IPv6. Pour plus d’informations, consultez [Créer un réseau virtuel](https://docs.microsoft.com/azure/virtual-network/quick-create-portal#create-a-virtual-network).

2. [Créez le sous-réseau de passerelle à double pile](https://docs.microsoft.com/azure/expressroute/expressroute-howto-add-gateway-resource-manager#add-a-gateway).

3. [Créez la passerelle de réseau virtuel](https://docs.microsoft.com/azure/expressroute/expressroute-howto-add-gateway-resource-manager#add-a-gateway) à l’aide d’une référence SKU redondante dans une zone (ErGw1AZ, ErGw2AZ, ErGw3AZ). Si vous envisagez d’utiliser FastPath, utilisez ErGw3AZ.

4. [Liez votre réseau virtuel à votre circuit ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-arm).

## <a name="limitations"></a>Limites
Bien que la prise en charge IPV6 soit disponible pour les connexions à des déploiements dans des régions avec Zones de disponibilité, elle ne prend pas en charge les cas d’utilisation suivants :

* Connexions à des déploiements dans Azure via une référence SKU de passerelle ExpressRoute sans AZ
* Connexions à des déploiements dans des régions sans AZ
* Connexions Global Reach entre des circuits ExpressRoute

## <a name="next-steps"></a>Étapes suivantes

Pour résoudre des problèmes liés à ExpressRoute, consultez les articles suivants :

* [Vérification de la connectivité ExpressRoute](expressroute-troubleshooting-expressroute-overview.md)
* [Résolution des problèmes de performances réseau](expressroute-troubleshooting-network-performance.md)
