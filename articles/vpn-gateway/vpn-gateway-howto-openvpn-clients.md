---
title: Configurer des clients OpenVPN sur la passerelle VPN Azure | Microsoft Docs
description: Étapes de configuration de clients OpenVPN pour la passerelle VPN Azure
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 12/12/2019
ms.author: cherylmc
ms.openlocfilehash: bdddf097265e7af688175688b6f3214413a90fdc
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84984089"
---
# <a name="configure-openvpn-clients-for-azure-vpn-gateway"></a>Configurer des clients OpenVPN sur la passerelle VPN Azure

Cet article vous permet de configurer des clients pour l’utilisation du **protocole&reg; OpenVPN**.

## <a name="before-you-begin"></a>Avant de commencer

Vérifiez que vous avez terminé les étapes de configuration de OpenVPN pour votre passerelle VPN. Pour plus d’informations, consultez [Configurer OpenVPN pour la passerelle VPN Azure](vpn-gateway-howto-openvpn.md).

[!INCLUDE [configuration steps](../../includes/vpn-gateway-vwan-config-openvpn-clients.md)]

## <a name="next-steps"></a>Étapes suivantes

Si vous souhaitez que les clients VPN puissent accéder aux ressources d’un autre réseau virtuel, suivez les instructions de l’article [Réseau virtuel à réseau virtuel](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) qui explique comment définir une connexion entre des réseaux virtuels. Veillez à activer BGP sur les passerelles et les connexions, sinon le trafic ne passe pas.

**« OpenVPN » est une marque d’OpenVPN Inc.**
