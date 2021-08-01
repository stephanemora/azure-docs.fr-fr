---
title: Configurer un tunnel utilisateur VPN Always On
titleSuffix: Azure Virtual WAN
description: Apprenez à configurer un tunnel utilisateur VPN Always On pour votre instance de Virtual WAN.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 05/26/2021
ms.author: cherylmc
ms.openlocfilehash: 65cddc3c850ada4e89e23da4d900afbd7476ce13
ms.sourcegitcommit: e1d5abd7b8ded7ff649a7e9a2c1a7b70fdc72440
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2021
ms.locfileid: "110579693"
---
# <a name="configure-an-always-on-vpn-user-tunnel-for-virtual-wan"></a>Configurer un tunnel utilisateur VPN Always On pour un WAN virtuel

[!INCLUDE [intro](../../includes/vpn-gateway-vwan-always-on-intro.md)]

## <a name="prerequisites"></a>Prérequis

Vous devez créer une configuration de point à site et modifier l’affectation du hub virtuel. Consultez les sections suivantes pour obtenir des instructions :

* [Créer une configuration P2S](virtual-wan-point-to-site-portal.md#p2sconfig)
* [Créer un hub avec la passerelle P2S](virtual-wan-point-to-site-portal.md#hub)

## <a name="configure-a-user-tunnel"></a>Configurer un tunnel utilisateur

[!INCLUDE [user tunnel](../../includes/vpn-gateway-vwan-always-on-user.md)]

## <a name="to-remove-a-profile"></a>Pour supprimer un profil

Pour supprimer un profil, effectuez les étapes suivantes :

1. Exécutez la commande suivante :

   ```powershell
   C:\> Remove-VpnConnection UserTest  
   ```

1. Arrêtez la connexion et désactivez la case à cocher **Se connecter automatiquement**.

   ![Nettoyage](./media/howto-always-on-user-tunnel/disconnect.jpg)

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur Virtual WAN, consultez la [FAQ](virtual-wan-faq.md).
