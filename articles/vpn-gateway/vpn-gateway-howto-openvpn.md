---
title: 'Configuration d’OpenVPN sur la passerelle VPN Azure : PowerShell| Microsoft Docs'
description: Étapes de configuration d’OpenVPN pour la passerelle VPN Azure
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: cherylmc
ms.openlocfilehash: 609c2ef91fafe0ae955252a594292d861e772f87
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/22/2019
ms.locfileid: "66002957"
---
# <a name="configure-openvpn-for-azure-point-to-site-vpn-gateway"></a>Configurer OpenVPN pour la passerelle VPN point à site Azure

Cet article vous aide à configurer **OpenVPN® protocole** sur la passerelle VPN Azure. Nous partons du principe que vous disposez déjà d’un environnement de point à site fonctionnel. Si ce n’est pas le cas, suivez les instructions de l’étape 1 pour créer un réseau VPN de point à site.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="vnet"></a>1. Créer un réseau VPN de point à site

Si vous ne disposez pas d’un environnement de point à site opérationnel, suivez les instructions de création. Consultez l’article [Créer un VPN de point à site](vpn-gateway-howto-point-to-site-resource-manager-portal.md) pour créer et configurer une passerelle VPN de point à site avec l’authentification par certificat native Azure. 

> [!IMPORTANT]
> La référence De base n’est pas prise en charge pour OpenVPN.

## <a name="enable"></a>2. Activer OpenVPN sur la passerelle

Activez OpenVPN sur votre passerelle. Assurez-vous que la passerelle est configurée de point à site (IKEv2 ou SSTP) avant d’exécuter les commandes suivantes :

```azurepowershell-interactive
$gw = Get-AzVirtualNetworkGateway -ResourceGroupName $rgname -name $name
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -VpnClientProtocol OpenVPN
```

## <a name="next-steps"></a>Étapes suivantes

Pour configurer les clients pour OpenVPN, consultez [Configurer les clients OpenVPN](vpn-gateway-howto-openvpn-clients.md).

**« OpenVPN » est une marque d’Inc. OpenVPN**
