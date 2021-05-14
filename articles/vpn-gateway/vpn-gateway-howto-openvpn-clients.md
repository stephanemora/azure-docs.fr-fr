---
title: Comment configurer les clients OpenVPN pour les passerelles VPN P2S
titleSuffix: Azure VPN Gateway
description: Découvrez comment configurer les clients OpenVPN pour la passerelle VPN Azure. Cet article vous aide à configurer les clients Windows, Linux, iOS et Mac.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 04/29/2021
ms.author: cherylmc
ms.openlocfilehash: 5a7b125ef89d5d598e22fc4458b5b1c7f8c4a41c
ms.sourcegitcommit: fc9fd6e72297de6e87c9cf0d58edd632a8fb2552
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2021
ms.locfileid: "108289351"
---
# <a name="configure-openvpn-clients-for-azure-vpn-gateway"></a>Configurer des clients OpenVPN sur la passerelle VPN Azure

Cet article vous permet de configurer des clients pour l’utilisation du **protocole&reg; OpenVPN**.

## <a name="before-you-begin"></a>Avant de commencer

Vérifiez que vous avez terminé les étapes de configuration de OpenVPN pour votre passerelle VPN. Pour plus d’informations, consultez [Configurer OpenVPN pour la passerelle VPN Azure](vpn-gateway-howto-openvpn.md).

[!INCLUDE [configuration steps](../../includes/vpn-gateway-vwan-config-openvpn-clients.md)]

## <a name="next-steps"></a>Étapes suivantes

Si vous souhaitez que les clients VPN puissent accéder aux ressources d’un autre réseau virtuel, suivez les instructions de l’article [Réseau virtuel à réseau virtuel](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) qui explique comment définir une connexion entre des réseaux virtuels. Veillez à activer BGP sur les passerelles et les connexions, sinon le trafic ne passe pas.

**« OpenVPN » est une marque d’OpenVPN Inc.**
