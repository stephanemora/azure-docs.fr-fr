---
title: Fichier Include
description: Fichier Include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/04/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 4842c55b2b1fd23f4d6b7996ccf02e7141504836
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "73495746"
---
Vous pouvez utiliser l’applet de commande PowerShell `Resize-AzVirtualNetworkGateway` modifier le niveau d’une référence (SKU) Generation1 ou Generation2 (toutes les références SKU VpnGw peuvent être redimensionnées, à l’exception des références De base). Si vous utilisez la référence SKU de passerelle De base [utilisez ces instructions à la place](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md#resize) pour redimensionner votre passerelle.

L’exemple PowerShell suivant montre une référence SKU de passerelle redimensionnée sur VpnGw2.

```azurepowershell-interactive
$gw = Get-AzVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
Resize-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku VpnGw2
```

Vous pouvez également redimensionner une passerelle dans le portail Azure en accédant à la page **Configuration** de la passerelle de votre réseau virtuel, puis en sélectionnant une autre référence SKU dans la liste déroulante.
