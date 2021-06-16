---
title: Afficher l’état et les métriques BGP
titleSuffix: Azure VPN Gateway
description: Découvrez comment afficher des informations importantes relatives au protocole BGP pour la résolution des problèmes.
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: sample
ms.date: 03/10/2021
ms.author: alzam
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 65ebf90d2485969343092bbee1fd3935112b6466
ms.sourcegitcommit: df574710c692ba21b0467e3efeff9415d336a7e1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2021
ms.locfileid: "110672146"
---
# <a name="view-bgp-metrics-and-status"></a>Afficher et l’état et les métriques BGP

Vous pouvez afficher les métriques et l’état BGP avec le portail Azure ou Azure PowerShell.

## <a name="azure-portal"></a>Portail Azure

Dans le portail Azure, vous pouvez afficher les pairs BGP, les routes apprises et les routes publiées. Vous pouvez également télécharger des fichiers .csv contenant ces données.

1. Dans le [portail Azure](https://portal.azure.com), accédez à votre passerelle de réseau virtuel.
1. Sous **Supervision**, sélectionnez **Pairs de protocole de passerelle frontière (BGP)** pour ouvrir la page des pairs BGP.

   :::image type="content" source="./media/bgp-diagnostics/bgp-portal.jpg" alt-text="Capture d’écran des métriques dans le portail Azure":::

### <a name="learned-routes"></a>Routes apprises

1. Vous pouvez afficher jusqu’à 50 routes apprises dans le portail.

   :::image type="content" source="./media/bgp-diagnostics/learned-routes.jpg" alt-text="Capture d’écran des routes apprises":::

1. Vous pouvez également télécharger le fichier des routes apprises. Si vous avez plus de 50 routes apprises, le seul moyen de les afficher toutes consiste à télécharger et à afficher le fichier .csv. Pour effectuer le téléchargement, sélectionnez **Télécharger les routes apprises**.

   :::image type="content" source="./media/bgp-diagnostics/download-routes.jpg" alt-text="Capture d’écran du téléchargement des routes apprises":::
1. Ensuite, affichez le fichier.

   :::image type="content" source="./media/bgp-diagnostics/learned-routes-file.jpg" alt-text="Capture d’écran des routes apprises téléchargées":::

### <a name="advertised-routes"></a>Routes publiées

1. Pour afficher les routes publiées, sélectionnez les points de suspension ( **...** ) à la fin du réseau que vous souhaitez afficher, puis cliquez sur **Voir les routes publiées**.

   :::image type="content" source="./media/bgp-diagnostics/select-route.png" alt-text="Capture d’écran montrant comment voir les routes publiées." lightbox="./media/bgp-diagnostics/route-expand.png":::
1. Dans la page **Routes publiées pour le pair**, vous pouvez voir jusqu’à 50 routes publiées.

   :::image type="content" source="./media/bgp-diagnostics/advertised-routes.jpg" alt-text="Capture d’écran des routes publiées":::
1. Vous pouvez également télécharger le fichier des routes publiées. Si vous avez plus de 50 routes publiées, le seul moyen de les afficher toutes consiste à télécharger et à afficher le fichier .csv. Pour effectuer le téléchargement, sélectionnez **Télécharger les routes publiées**.

   :::image type="content" source="./media/bgp-diagnostics/download-advertised.jpg" alt-text="Capture d’écran de la sélection des routes publiées téléchargées":::
1. Ensuite, affichez le fichier.

   :::image type="content" source="./media/bgp-diagnostics/advertised-routes-file.jpg" alt-text="Capture d’écran des routes publiées téléchargées":::

### <a name="bgp-peers"></a>Pairs BGP

1. Vous pouvez afficher jusqu’à 50 pairs BGP dans le portail.

   :::image type="content" source="./media/bgp-diagnostics/peers.jpg" alt-text="Capture d’écran des pairs BGP":::
1. Vous pouvez également télécharger le fichier des pairs BGP. Si vous avez plus de 50 pairs BGP, le seul moyen de les afficher tous consiste à télécharger et à afficher le fichier .csv. Pour effectuer le téléchargement, sélectionnez **Télécharger les pairs BGP** dans la page du portail.

   :::image type="content" source="./media/bgp-diagnostics/download-peers.jpg" alt-text="Capture d’écran du téléchargement des pairs BGP":::
1. Ensuite, affichez le fichier.

   :::image type="content" source="./media/bgp-diagnostics/peers-file.jpg" alt-text="Capture d’écran des pairs BGP téléchargés":::

## <a name="powershell"></a>PowerShell

Utilisez la commande **Get-AzVirtualNetworkGatewayBGPPeerStatus** pour afficher les pairs et l’état BGP.

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

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur BGP, consultez [Configurer le protocole BGP pour une passerelle VPN](bgp-howto.md).
