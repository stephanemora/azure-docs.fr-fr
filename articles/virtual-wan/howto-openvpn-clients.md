---
title: Configurer des clients OpenVPN pour Azure Virtual WAN
description: Étapes de configuration des clients OpenVPN pour Azure Virtual WAN
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: 68238a084684aeda557690812403995a4dc1e8c9
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91317665"
---
# <a name="configure-an-openvpn-client-for-azure-virtual-wan"></a>Configurer un client OpenVPN pour Azure Virtual WAN

Cet article vous permet de configurer des clients pour l’utilisation du **protocole&reg; OpenVPN**.

## <a name="before-you-begin"></a>Avant de commencer

Créez une configuration VPN (point à site) utilisateur. Veillez à sélectionner « OpenVPN » comme type de tunnel. Pour les étapes, consultez [Créer une configuration P2S pour Azure Virtual WAN](virtual-wan-point-to-site-portal.md#p2sconfig).

[!INCLUDE [configuration steps](../../includes/vpn-gateway-vwan-config-openvpn-clients.md)]

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le VPN utilisateur (point à site), consultez [Création de connexions VPN utilisateur](virtual-wan-point-to-site-portal.md).

**« OpenVPN » est une marque d’OpenVPN Inc.**
