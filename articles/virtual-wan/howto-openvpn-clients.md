---
title: Configurer des clients OpenVPN pour Azure Virtual WAN
description: Apprenez à configurer des clients OpenVPN pour Azure Virtual WAN. Cet article présente la procédure à suivre pour configurer des clients Windows, Mac, iOS et Linux.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 04/27/2021
ms.author: cherylmc
ms.openlocfilehash: 83c70aca81eaa888186807d43fff5a7bbaccb700
ms.sourcegitcommit: e1d5abd7b8ded7ff649a7e9a2c1a7b70fdc72440
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2021
ms.locfileid: "110579602"
---
# <a name="configure-an-openvpn-client-for-azure-virtual-wan"></a>Configurer un client OpenVPN pour Azure Virtual WAN

Cet article vous permet de configurer des clients pour l’utilisation du **protocole&reg; OpenVPN**. Vous pouvez également utiliser Azure VPN Client pour Windows 10 pour vous connecter via le protocole OpenVPN. Pour plus d'informations, consultez [Configurer un client VPN pour les connexions P2S OpenVPN](openvpn-azure-ad-client.md).

## <a name="before-you-begin"></a>Avant de commencer

Créez une configuration VPN (point à site) utilisateur. Veillez à sélectionner « OpenVPN » comme type de tunnel. Pour les étapes, consultez [Créer une configuration P2S pour Azure Virtual WAN](virtual-wan-point-to-site-portal.md#p2sconfig).

[!INCLUDE [configuration steps](../../includes/vpn-gateway-vwan-config-openvpn-clients.md)]

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le VPN utilisateur (point à site), consultez [Création de connexions VPN utilisateur](virtual-wan-point-to-site-portal.md).

**« OpenVPN » est une marque d’OpenVPN Inc.**
