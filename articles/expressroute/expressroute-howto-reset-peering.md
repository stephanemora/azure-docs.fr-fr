---
title: 'Azure ExpressRoute : Réinitialiser le peering d’un circuit'
description: Comment désactiver et activer les peerings d’un circuit ExpressRoute.
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: conceptual
ms.date: 08/15/2018
ms.author: charwen
ms.openlocfilehash: b14b8a9a2bc43c33095bf07e7fb7ebcc2d6c1ffa
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74769521"
---
# <a name="reset-expressroute-circuit-peerings"></a>Réinitialiser les peerings d’un circuit ExpressRoute

Cet article décrit comment désactiver et activer des peerings d’un circuit ExpressRoute à l’aide de PowerShell. Lorsque vous désactivez un peering, la session BGP sur la connexion principale et la connexion secondaire de votre circuit ExpressRoute s’arrête. Vous perdez la connectivité via ce peering Microsoft. Lorsque vous activez un peering, la session BGP sur la connexion principale et la connexion secondaire de votre circuit ExpressRoute s’affiche. Vous retrouvez la connectivité via ce peering Microsoft. Vous pouvez activer et désactiver le peering Microsoft et le peering privé Azure de manière indépendante sur un circuit ExpressRoute. Lorsque vous commencez par configurer les peerings sur votre circuit ExpressRoute, ils sont activés par défaut.

Il existe quelques scénarios où il peut s’avérer utile de réinitialiser vos peerings ExpressRoute.
* Testez votre conception et votre implémentation de récupération d’urgence. Par exemple, vous avez deux circuits ExpressRoute. Vous pouvez désactiver les peerings d’un circuit et forcer votre trafic réseau à basculer sur l’autre circuit.
* Activez le protocole Bidirectional Forwarding Detection (BFD) sur le peering privé Azure de votre circuit ExpressRoute. Si votre circuit ExpressRoute a été créé après le 1er août 2018, BFD est activé par défaut. Si votre circuit a été créé avant, BFD n’a pas été activé. Vous pouvez activer BFD en désactivant le peering, puis en le réactivant. Il faut noter que BFD est pris en charge uniquement sur le peering privé Azure.

### <a name="working-with-azure-powershell"></a>Utilisation d’Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="reset-a-peering"></a>Réinitialiser un peering

1. Si vous exécutez PowerShell localement, ouvrez votre console PowerShell avec des privilèges élevés et connectez-vous à votre compte. Utilisez l’exemple suivant pour faciliter votre connexion :

   ```azurepowershell
   Connect-AzAccount
   ```
2. Si vous disposez de plusieurs abonnements Azure, vérifiez les abonnements associés au compte.

   ```azurepowershell-interactive
   Get-AzSubscription
   ```
3. Spécifiez l’abonnement à utiliser.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionName "Replace_with_your_subscription_name"
   ```
4. Exécutez les commandes suivantes pour récupérer votre circuit ExpressRoute.

   ```azurepowershell-interactive
   $ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
   ```
5. Identifiez le peering que vous souhaitez activer ou désactiver. *Peerings* est un tableau. Dans l’exemple suivant, Peerings[0] est un peering privé Azure et Peerings[1] un peering Microsoft.

   ```azurepowershell-interactive
   Name                             : ExpressRouteARMCircuit
   ResourceGroupName                : ExpressRouteResourceGroup
   Location                         : westus
   Id                               : /subscriptions/########-####-####-####-############/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
   Etag                             : W/"cd011bef-dc79-49eb-b4c6-81fb6ea5d178"
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
                                     "ServiceProviderName": "Coresite",
                                     "PeeringLocation": "Los Angeles",
                                     "BandwidthInMbps": 50
                                   }
   ServiceKey                       : ########-####-####-####-############
   Peerings                         : [
                                     {
                                       "Name": "AzurePrivatePeering",
                                       "Etag": "W/\"cd011bef-dc79-49eb-b4c6-81fb6ea5d178\"",
                                       "Id": "/subscriptions/########-####-####-####-############/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit/peerings/AzurePrivatePeering",
                                       "PeeringType": "AzurePrivatePeering",
                                       "State": "Enabled",
                                       "AzureASN": 12076,
                                       "PeerASN": 123,
                                       "PrimaryPeerAddressPrefix": "10.0.0.0/30",
                                       "SecondaryPeerAddressPrefix": "10.0.0.4/30",
                                       "PrimaryAzurePort": "",
                                       "SecondaryAzurePort": "",
                                       "VlanId": 789,
                                       "MicrosoftPeeringConfig": {
                                         "AdvertisedPublicPrefixes": [],
                                         "AdvertisedCommunities": [],
                                         "AdvertisedPublicPrefixesState": "NotConfigured",
                                         "CustomerASN": 0,
                                         "LegacyMode": 0,
                                         "RoutingRegistryName": "NONE"
                                       },
                                       "ProvisioningState": "Succeeded",
                                       "GatewayManagerEtag": "",
                                       "LastModifiedBy": "Customer",
                                       "Connections": []
                                     },
                                     {
                                       "Name": "MicrosoftPeering",
                                       "Etag": "W/\"cd011bef-dc79-49eb-b4c6-81fb6ea5d178\"",
                                       "Id": "/subscriptions/########-####-####-####-############/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit/peerings/MicrosoftPeering",
                                       "PeeringType": "MicrosoftPeering",
                                       "State": "Enabled",
                                       "AzureASN": 12076,
                                       "PeerASN": 123,
                                       "PrimaryPeerAddressPrefix": "3.0.0.0/30",
                                       "SecondaryPeerAddressPrefix": "3.0.0.4/30",
                                       "PrimaryAzurePort": "",
                                       "SecondaryAzurePort": "",
                                       "VlanId": 345,
                                       "MicrosoftPeeringConfig": {
                                         "AdvertisedPublicPrefixes": [
                                           "3.0.0.3/32"
                                         ],
                                         "AdvertisedCommunities": [],
                                         "AdvertisedPublicPrefixesState": "ValidationNeeded",
                                         "CustomerASN": 0,
                                         "LegacyMode": 0,
                                         "RoutingRegistryName": "NONE"
                                       },
                                       "ProvisioningState": "Succeeded",
                                       "GatewayManagerEtag": "",
                                       "LastModifiedBy": "Customer",
                                       "Connections": []
                                     }
                                   ]
   Authorizations                   : []
   AllowClassicOperations           : False
   GatewayManagerEtag               :
   ```
6. Exécutez les commandes suivantes pour modifier l’état du peering.

   ```azurepowershell-interactive
   $ckt.Peerings[0].State = "Disabled"
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```
   Le peering doit être dans un état que vous définissez. 

## <a name="next-steps"></a>Étapes suivantes
Si vous avez besoin d’aide pour résoudre un problème avec ExpressRoute, consultez les articles suivants :
* [Vérification de la connectivité ExpressRoute](expressroute-troubleshooting-expressroute-overview.md)
* [Résolution des problèmes de performances réseau](expressroute-troubleshooting-network-performance.md)
