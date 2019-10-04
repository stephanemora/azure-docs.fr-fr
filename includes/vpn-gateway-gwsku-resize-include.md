---
title: Fichier Include
description: Fichier Include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/15/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: f6fd4039614dbd7c1a2b2c6ba8403502a6420fe3
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67176946"
---
Pour les références SKU en cours (VpnGw1, VpnGw2 et VPNGW3) pour lesquelles vous souhaitez redimensionner la référence SKU de votre passerelle en vue d’une mise à niveau vers une référence SKU plus puissante, vous pouvez utiliser la cmdlet PowerShell `Resize-AzVirtualNetworkGateway`. Vous pouvez également réduire la taille de référence SKU à l’aide de cet applet de commande. Si vous utilisez la référence SKU de passerelle De base [utilisez ces instructions à la place](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md#resize) pour redimensionner votre passerelle.

L’exemple PowerShell suivant montre une référence SKU de passerelle redimensionnée sur VpnGw2.

```azurepowershell-interactive
$gw = Get-AzVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
Resize-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku VpnGw2
```

Vous pouvez également redimensionner une passerelle dans le portail Azure en accédant à la page **Configuration** de la passerelle de votre réseau virtuel, puis en sélectionnant une autre référence SKU dans la liste déroulante.