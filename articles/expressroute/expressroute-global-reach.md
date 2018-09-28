---
title: Azure ExpressRoute Global Reach | Microsoft Docs
description: Cet article vous aide à associer des circuits ExpressRoute afin de constituer un réseau privé entre vos réseaux locaux et d’activer Global Reach.
documentationcenter: na
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/21/2018
ms.author: cherylmc
ms.openlocfilehash: 90f34da10e0193e31a0f70187463799013f52be8
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46966813"
---
# <a name="link-expressroute-circuits-to-enable-expressroute-global-reach-preview"></a>Associer des circuits ExpressRoute pour activer ExpressRoute Global Reach (préversion)

ExpressRoute vous permet de connecter vos réseaux locaux à Microsoft Cloud de manière privée et résiliente. Vous pouvez accéder à de nombreux services Microsoft Cloud, tels que Azure, Office 365 et Dynamics 365, depuis votre centre de données privé ou le réseau de votre entreprise. Par exemple, vous pouvez avoir une succursale à San Francisco avec un circuit ExpressRoute dans la Silicon Valley, et une autre à Baltimore avec un circuit ExpressRoute à Washington. 

Les deux succursales peuvent disposer d’une connexion haut débit aux ressources Azure dans l’est et l’ouest des États-Unis. Toutefois, elles ne peuvent pas échanger de données directement entre elles. Autrement dit, 10.0.1.0/24 peut échanger des données avec 10.0.3.0/24 et 10.0.4.0/24, mais PAS avec 10.0.2.0/24.

![without][1]

Avec **ExpressRoute Global Reach**, vous pouvez associer des circuits ExpressRoute afin de constituer un réseau de données privé entre vos réseaux locaux. Dans l’exemple ci-dessus, grâce à l’ajout d’ExpressRoute Global Reach, votre bureau de San Francisco (10.0.1.0/24) peut échanger directement des données avec le bureau de Baltimore (10.0.2.0/24) par le biais des circuits ExpressRoute existants et du réseau mondial de Microsoft. 

![with][2]

ExpressRoute Global Reach est actuellement pris en charge dans les pays suivants :

* États-Unis
* Royaume-Uni 
* Japon

Vos circuits ExpressRoute doivent être créés sur les [sites d’homologation ExpressRoute](expressroute-locations.md) dans ces pays. Pour activer ExpressRoute Global Reach entre [différentes régions](expressroute-locations.md), vous devez disposer de circuits Premium.


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

## <a name="to-enable-connectivity-between-your-on-premises-networks"></a>Pour autoriser la connexion entre vos réseaux locaux

Utilisez les commandes suivantes pour obtenir le circuit 1 et le circuit 2. Les deux circuits se trouvent dans le même abonnement.

```powershell
$ckt_1 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
$ckt_2 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_2_name" -ResourceGroupName "Your_resource_group"
```

Exécutez la commande suivante sur le circuit 1 et transférez l’ID de l’homologation privée du circuit 2.

Voici à quoi ressemble l’ID de l’homologation privée :

`/subscriptions/{your_subscription_id}/resourceGroups/{your_resource_group}/providers/Microsoft.Network/expressRouteCircuits/{your_circuit_name}/peerings/AzurePrivatePeering`


```powershell
Add-AzureRmExpressRouteCircuitConnectionConfig -Name 'Your_connection_name' -ExpressRouteCircuit $ckt_1 -PeerExpressRouteCircuitPeering $ckt_2.Peerings[0].Id -AddressPrefix '__.__.__.__/29'
```

La valeur *- AddressPrefix* doit être un sous-réseau /29 IPv4, par exemple « 10.0.0.0/29 ». Nous allons utiliser les adresses IP de ce sous-réseau pour établir la connexion entre les deux circuits ExpressRoute. Vous ne devez pas les utiliser dans vos réseaux virtuels Azure ou vos réseaux locaux. 


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

## <a name="to-get-and-verify-the-configuration"></a>Pour obtenir et vérifier la configuration

Utilisez la commande suivante pour vérifier la configuration sur le circuit où elle a été effectuée. Cet exemple utilise le circuit 1 de l’exemple précédent.

```powershell
$ckt1 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
```

Si vous exécutez simplement *$ckt1* dans PowerShell, vous verrez s’afficher la valeur *CircuitConnectionStatus*. Cela indique si la connectivité est établie, « Connecté », ou non, « Déconnecté ». 

## <a name="to-disable-connectivity-between-your-on-premises-networks"></a>Pour désactiver la connexion entre vos réseaux locaux

Exécutez les commandes sur le circuit où la configuration a été effectuée. Cet exemple utilise le circuit 1 des exemples précédents.

```powershell
$ckt1 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
Remove-AzureRmExpressRouteCircuitConnectionConfig -Name "Your_connection_name" -ExpressRouteCircuit $ckt_1
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_1
```

Vous pouvez exécuter l’opération Get pour vérifier l’état. 

Une fois l’opération terminée, la connexion entre vos réseaux locaux n’est plus établie. 

## <a name="next-steps"></a>Étapes suivantes
1. [En savoir plus sur ExpressRoute Global Reach](expressroute-faqs.md#globalreach)
2. [Vérifier la connectivité ExpressRoute](expressroute-troubleshooting-expressroute-overview.md)
3. [Lier un circuit ExpressRoute à un réseau virtuel Azure](expressroute-howto-linkvnet-arm.md)


<!--Image References-->
[1]: ./media/expressroute-global-reach/1.png "schéma sans portée globale"
[2]: ./media/expressroute-global-reach/2.png "schéma avec portée globale"