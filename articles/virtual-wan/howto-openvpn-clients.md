---
title: Configurer des clients OpenVPN pour Azure Virtual WAN
description: Cet article vous aide à configurer les clients OpenVPN pour Azure Virtual WAN. Il comprend la procédure de configuration de client Windows, Mac, iOS et Linux.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 04/27/2021
ms.author: cherylmc
ms.openlocfilehash: 91b4a9a69912f7a5980348cc2b9a874673fa0eb2
ms.sourcegitcommit: 62e800ec1306c45e2d8310c40da5873f7945c657
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108163388"
---
# <a name="configure-an-openvpn-client-for-azure-virtual-wan"></a>Configurer un client OpenVPN pour Azure Virtual WAN

Cet article vous permet de configurer des clients pour l’utilisation du **protocole&reg; OpenVPN**. Vous pouvez également utiliser Azure VPN Client pour Windows 10 pour vous connecter via le protocole OpenVPN. Vous trouverez plus d’instructions [ici](openvpn-azure-ad-client.md)

## <a name="before-you-begin"></a>Avant de commencer

Créez une configuration VPN (point à site) utilisateur. Veillez à sélectionner « OpenVPN » comme type de tunnel. Pour les étapes, consultez [Créer une configuration P2S pour Azure Virtual WAN](virtual-wan-point-to-site-portal.md#p2sconfig).

[!INCLUDE [configuration steps](../../includes/vpn-gateway-vwan-config-openvpn-clients.md)]

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le VPN utilisateur (point à site), consultez [Création de connexions VPN utilisateur](virtual-wan-point-to-site-portal.md).

**« OpenVPN » est une marque d’OpenVPN Inc.**
