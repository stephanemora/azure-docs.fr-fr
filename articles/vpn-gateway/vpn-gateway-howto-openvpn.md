---
title: Guide pratique pour activer OpenVPN sur les passerelles VPN P2S
titleSuffix: Azure VPN Gateway
description: Découvrez comment activer le protocole OpenVPN sur les passerelles VPN pour les configurations point à site.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 07/28/2021
ms.author: cherylmc
ms.openlocfilehash: ab7826a89dc879c1bad62e8c56415047d164c6f9
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122562379"
---
# <a name="configure-openvpn-for-point-to-site-vpn-gateways"></a>Configurer OpenVPN pour des passerelles VPN point à site

Cet article vous aide à configurer le **protocole OpenVPN®** sur la passerelle VPN Azure. Vous pouvez utiliser le portail ou les instructions PowerShell.

## <a name="prerequisites"></a>Prérequis

* Nous partons du principe que vous disposez déjà d’un environnement de point à site fonctionnel. Autrement, créez-en un à l’aide de l’une des méthodes suivantes. Lors de la création de votre passerelle, notez que la référence (SKU) **De base** ne prend pas en charge le type de tunnel OpenVPN.

  * [Portail – Créer une connexion point à site](vpn-gateway-howto-point-to-site-resource-manager-portal.md)

  * [PowerShell – Créer une connexion point à site](vpn-gateway-howto-point-to-site-rm-ps.md)

* Si vous disposez d’une passerelle VPN, vérifiez qu’elle n’utilise pas la référence (SKU) **De base**. La référence De base n’est pas prise en charge pour OpenVPN. Pour plus d’informations sur les références (SKU), consultez [Paramètres de configuration de la passerelle VPN](vpn-gateway-about-vpn-gateway-settings.md). Pour redimensionner une référence (SKU) De base, consultez [Redimensionner une passerelle héritée](vpn-gateway-about-skus-legacy.md#resource-manager). 

## <a name="portal"></a>Portail

1. Dans le portail, accédez à votre **Passerelle de réseau virtuel -> Configuration point à site**.
1. Sous **Type de tunnel**, sélectionnez **OpenVPN (SSL)** dans la liste déroulante.

   :::image type="content" source="./media/vpn-gateway-howto-openvpn/portal.png" alt-text="Sélectionnez OpenVPN SSL dans la liste déroulante.":::
1. Enregistrez vos modifications et passez aux **Étapes suivantes**.

## <a name="powershell"></a>PowerShell

1. Activez OpenVPN sur votre passerelle à l’aide de l’exemple suivant, en ajustant les valeurs selon vos besoins.

   ```azurepowershell-interactive
   $gw = Get-AzVirtualNetworkGateway -ResourceGroupName TestRG1 -name VNet1GW
   Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -VpnClientProtocol OpenVPN
   ```
1. Passez aux **Étapes suivantes**.

## <a name="next-steps"></a>Étapes suivantes

Pour configurer les clients pour OpenVPN, consultez [Configurer les clients OpenVPN](vpn-gateway-howto-openvpn-clients.md).

**« OpenVPN » est une marque d’OpenVPN Inc.**
