---
title: Fichier include
description: Fichier include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/04/2019
ms.author: cherylmc
ms.custom: include file, devx-track-azurepowershell
ms.openlocfilehash: 478856b6e62b06259ae37b049774c9e22c840d4a
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/29/2021
ms.locfileid: "110721541"
---
Vous pouvez utiliser l’applet de commande PowerShell `Resize-AzVirtualNetworkGateway` modifier le niveau d’une référence (SKU) Generation1 ou Generation2 (toutes les références SKU VpnGw peuvent être redimensionnées, à l’exception des références De base). Si vous utilisez la référence SKU de passerelle De base [utilisez ces instructions à la place](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md#resize) pour redimensionner votre passerelle.

L’exemple PowerShell suivant montre une référence SKU de passerelle redimensionnée sur VpnGw2.

```azurepowershell-interactive
$gw = Get-AzVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
Resize-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku VpnGw2
```
