---
title: Configurer Azure ExpressRoute Global Reach | Microsoft Docs
description: Cet article vous aide à associer des circuits ExpressRoute afin de constituer un réseau privé entre vos réseaux locaux et d’activer Global Reach.
documentationcenter: na
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 10/09/2018
ms.author: cherylmc
ms.openlocfilehash: 4006626f9768289e75ccd61a1ef0bad5389f0a7a
ms.sourcegitcommit: 7b0778a1488e8fd70ee57e55bde783a69521c912
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/10/2018
ms.locfileid: "49070933"
---
# <a name="configure-expressroute-global-reach-preview"></a>Configurer ExpressRoute Global Reach (préversion)
Cet article vous permet de configurer ExpressRoute Global Reach à l’aide de PowerShell. Pour plus d’informations, consultez [ExpressRoute Global Reach](expressroute-global-reach.md).
 
## <a name="before-you-begin"></a>Avant de commencer
> [!IMPORTANT]
> Cette préversion publique est fournie sans contrat de niveau de service et ne doit pas être utilisée pour les charges de travail de production. Certaines fonctionnalités peuvent ne pas être prises en charge, disposer de capacités limitées ou ne pas être disponibles dans tous les emplacements Azure. Consultez les [Conditions d’utilisation supplémentaires des préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
> 


Avant de commencer la configuration, vous devez vérifier les points suivants.

* Installez la dernière version d’Azure PowerShell. Consultez la rubrique [Installation et configuration d’Azure PowerShell](/powershell/azure/install-azurerm-ps).
* Vous devez connaître les [flux de travail](expressroute-workflows.md) d’approvisionnement du circuit ExpressRoute.
* Assurez-vous que l’état de vos circuits ExpressRoute est Approvisionné.
* Assurez-vous que l’homologation privée Azure est configurée sur vos circuits ExpressRoute.  

### <a name="log-into-your-azure-account"></a>Connectez-vous à votre compte Azure
Pour commencer la configuration, vous devez vous connecter à votre compte Azure. 

Ouvrez la console PowerShell avec des privilèges élevés et connectez-vous à votre compte. La commande vous invite à entrer les informations d’identification de connexion à votre compte Azure.  

```powershell
Connect-AzureRmAccount
```

Si vous disposez de plusieurs abonnements Azure, vérifiez les abonnements associés au compte.

```powershell
Get-AzureRmSubscription
```

Spécifiez l’abonnement à utiliser.

```powershell
Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
```

### <a name="identify-your-expressroute-circuits-for-configuration"></a>Identifiez les circuits ExpressRoute à configurer
Vous pouvez activer ExpressRoute Global Reach entre deux circuits ExpressRoute à condition qu’ils se situent dans les pays pris en charge et qu’ils aient été créés sur différents sites d’homologation. Si votre abonnement comprend les deux circuits, vous pouvez choisir celui de votre choix pour exécuter la configuration dans les sections ci-dessous. Si les deux circuits se trouvent dans des abonnements Azure différents, vous devez avoir l’autorisation d’un abonnement Azure et transférer la clé d’autorisation lorsque vous exécutez la commande de configuration dans l’autre abonnement Azure.

## <a name="enable-connectivity-between-your-on-premises-networks"></a>Autoriser la connexion entre vos réseaux locaux

Utilisez les commandes suivantes pour obtenir le circuit 1 et le circuit 2. Les deux circuits se trouvent dans le même abonnement.

```powershell
$ckt_1 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
$ckt_2 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_2_name" -ResourceGroupName "Your_resource_group"
```

Exécutez la commande suivante sur le circuit 1 et transférez l’ID de l’homologation privée du circuit 2.

> [!NOTE]
> L’ID de l’homologation privée ressemble à */subscriptions/{votre_id_abonnement}/resourceGroups/{votre_groupe_ressources}/providers/Microsoft.Network/expressRouteCircuits/{votre_nom_circuit}/peerings/AzurePrivatePeering*
> 
>

```powershell
Add-AzureRmExpressRouteCircuitConnectionConfig -Name 'Your_connection_name' -ExpressRouteCircuit $ckt_1 -PeerExpressRouteCircuitPeering $ckt_2.Peerings[0].Id -AddressPrefix '__.__.__.__/29'
```

> [!IMPORTANT]
> La valeur *- AddressPrefix* doit être un sous-réseau /29 IPv4, par exemple « 10.0.0.0/29 ». Nous allons utiliser les adresses IP de ce sous-réseau pour établir la connexion entre les deux circuits ExpressRoute. Vous ne devez pas les utiliser dans vos réseaux virtuels Azure ou vos réseaux locaux.
> 



Enregistrez la configuration sur le circuit 1.
```powershell
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_1
```

Une fois l’opération terminée, la connexion entre vos réseaux locaux doit être établie des deux côtés par le biais des deux circuits ExpressRoute.

### <a name="expressroute-circuits-in-different-azure-subscriptions"></a>Circuits ExpressRoute dans différents abonnements Azure

Si les deux circuits ne se trouvent pas dans le même abonnement Azure, vous devez disposer d’une autorisation. Dans la configuration suivante, l’autorisation est générée dans l’abonnement du circuit 2 et la clé d’autorisation est transférée au circuit 1.

Générez une clé d’autorisation. 
```powershell
$ckt_2 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_2_name" -ResourceGroupName "Your_resource_group"
Add-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $ckt_2 -Name "Name_for_auth_key"
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_2
```
Notez l’ID de l’homologation privée du circuit 2, ainsi que la clé d’autorisation.

Exécutez la commande suivante sur le circuit 1. Transférez l’ID de l’homologation privée du circuit 2 et la clé d’autorisation. 
```powershell
Add-AzureRmExpressRouteCircuitConnectionConfig -Name 'Your_connection_name' -ExpressRouteCircuit $ckt_1 -PeerExpressRouteCircuitPeering "circuit_2_private_peering_id" -AddressPrefix '__.__.__.__/29' -AuthorizationKey '########-####-####-####-############'
```

Enregistrez la configuration sur le circuit 1.
```powershell
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_1
```

Une fois l’opération terminée, la connexion entre vos réseaux locaux doit être établie des deux côtés par le biais des deux circuits ExpressRoute.

## <a name="get-and-verify-the-configuration"></a>Obtenir et vérifier la configuration

Utilisez la commande suivante pour vérifier la configuration sur le circuit où elle a été effectuée. Pour l’exemple précédent, il s’agit du circuit 1.

```powershell
$ckt1 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
```

Si vous exécutez *$ckt1* dans PowerShell, vous verrez s’afficher la valeur *CircuitConnectionStatus*. Cela indique si la connectivité est établie, « Connecté », ou non, « Déconnecté ». 

## <a name="disable-connectivity-between-your-on-premises-networks"></a>Désactiver la connexion entre vos réseaux locaux

Exécutez les commandes sur le circuit où la configuration a été effectuée. Pour l’exemple précédent, il s’agit du circuit 1.

```powershell
$ckt1 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
Remove-AzureRmExpressRouteCircuitConnectionConfig -Name "Your_connection_name" -ExpressRouteCircuit $ckt_1
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_1
```

Vous pouvez exécuter l’opération Get pour vérifier l’état. 

Une fois l’opération terminée, la connexion entre vos réseaux locaux n’est plus établie. 


## <a name="next-steps"></a>Étapes suivantes
1. [En savoir plus sur ExpressRoute Global Reach](expressroute-global-reach.md)
2. [Vérifier la connectivité ExpressRoute](expressroute-troubleshooting-expressroute-overview.md)
3. [Lier un circuit ExpressRoute à un réseau virtuel Azure](expressroute-howto-linkvnet-arm.md)


