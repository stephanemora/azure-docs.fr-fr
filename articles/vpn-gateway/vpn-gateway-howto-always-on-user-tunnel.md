---
title: Configurer un tunnel utilisateur VPN Always On
titleSuffix: Azure VPN Gateway
description: Apprenez à configurer un tunnel utilisateur VPN Always On pour votre passerelle VPN.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 04/29/2021
ms.author: cherylmc
ms.openlocfilehash: 59c12333c6cf8ea9d251ce6387e3dce4ff23468b
ms.sourcegitcommit: fc9fd6e72297de6e87c9cf0d58edd632a8fb2552
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2021
ms.locfileid: "108287317"
---
# <a name="configure-an-always-on-vpn-user-tunnel"></a>Configurer un tunnel utilisateur VPN Always On

[!INCLUDE [intro](../../includes/vpn-gateway-vwan-always-on-intro.md)]

## <a name="configure-the-gateway"></a>Configurer la passerelle

 Suivez les instructions de l’article [Configurer une connexion VPN point à site](vpn-gateway-howto-point-to-site-resource-manager-portal.md) pour configurer la passerelle VPN afin d’utiliser IKEv2 et l’authentification basée sur les certificats.

## <a name="configure-a-user-tunnel"></a>Configurer un tunnel utilisateur

[!INCLUDE [user configuration](../../includes/vpn-gateway-vwan-always-on-user.md)]

## <a name="to-remove-a-profile"></a>Pour supprimer un profil

Pour supprimer un profil, effectuez les étapes suivantes :

1. Exécutez la commande suivante :

   ```powershell
   C:\> Remove-VpnConnection UserTest  
   ```

1. Arrêtez la connexion et désactivez la case à cocher **Se connecter automatiquement**.

   ![Nettoyage](./media/vpn-gateway-howto-always-on-user-tunnel/disconnect.jpg)

## <a name="next-steps"></a>Étapes suivantes

Pour résoudre les problèmes de connexion qui peuvent se produire, consultez l’article [Résolution des problèmes : problèmes de connexion point à site Azure](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).
