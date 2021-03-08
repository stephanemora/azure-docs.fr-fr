---
title: Afficher l’état et les métriques BGP
titleSuffix: Azure VPN Gateway
description: Affichez des informations importantes relatives au protocole BGP pour la résolution des problèmes.
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.devlang: powershell
ms.topic: sample
ms.date: 02/22/2021
ms.author: alzam
ms.openlocfilehash: 097568dddd5c8568d4523cdeb05ab0c889c31670
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101742805"
---
# <a name="view-bgp-metrics-and-status-using-powershell"></a>Afficher et l’état et les métriques BGP à l’aide de PowerShell

Utilisez la commande **Get-AzVirtualNetworkGatewayBGPPeerStatus** pour afficher les homologues et l’état BGP.

[!INCLUDE [VPN Gateway PowerShell instructions](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

```azurepowershell-interactive
Get-AzVirtualNetworkGatewayBgpPeerStatus -ResourceGroupName resourceGroup -VirtualNetworkGatewayName gatewayName

Asn               : 65515
ConnectedDuration : 9.01:04:53.5768637
LocalAddress      : 10.1.0.254
MessagesReceived  : 14893
MessagesSent      : 14900
Neighbor          : 10.0.0.254
RoutesReceived    : 1
State             : Connected
```

Utilisez la commande **Get-AzVirtualNetworkGatewayLearnedRoute** pour afficher tous les itinéraires que la passerelle a appris via le protocole BGP.

```azurepowershell-interactive
Get-AzVirtualNetworkGatewayLearnedRoute -ResourceGroupName resourceGroup -VirtualNetworkGatewayname gatewayName

AsPath       :
LocalAddress : 10.1.0.254
Network      : 10.1.0.0/16
NextHop      :
Origin       : Network
SourcePeer   : 10.1.0.254
Weight       : 32768

AsPath       :
LocalAddress : 10.1.0.254
Network      : 10.0.0.254/32
NextHop      :
Origin       : Network
SourcePeer   : 10.1.0.254
Weight       : 32768

AsPath       : 65515
LocalAddress : 10.1.0.254
Network      : 10.0.0.0/16
NextHop      : 10.0.0.254
Origin       : EBgp
SourcePeer   : 10.0.0.254
Weight       : 32768
```

Utilisez la commande **Get-AzVirtualNetworkGatewayAdvertisedRoute** pour afficher tous les itinéraires que la passerelle publie pour ses homologues via le protocole BGP.

```azurepowershell-interactive
Get-AzVirtualNetworkGatewayAdvertisedRoute -VirtualNetworkGatewayName gatewayName -ResourceGroupName resourceGroupName -Peer 10.0.0.254
```

## <a name="clean-up-resources"></a>Nettoyer les ressources

Lorsque vous n'avez plus besoin des ressources que vous avez créées, utilisez la commande [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) pour supprimer le groupe de ressources. Cette commande supprime le groupe de ressources et les ressources qu’il contient.

```azurepowershell-interactive
Remove-AzResourceGroup -Name ResourceGroupName
```

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le module Azure PowerShell, consultez [Documentation Azure PowerShell](/powershell/azure/).
