---
title: Configurer un tunnel utilisateur VPN Always On
titleSuffix: Azure Virtual WAN
description: Cet article explique comment configurer un tunnel utilisateur VPN Always On pour votre WAN virtuel
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/10/2020
ms.author: cherylmc
ms.openlocfilehash: 14790d4be91ffd0463436b6ca1d5c8794c102697
ms.sourcegitcommit: 43558caf1f3917f0c535ae0bf7ce7fe4723391f9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/11/2020
ms.locfileid: "90016349"
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
