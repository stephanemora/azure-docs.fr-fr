---
title: Configurer un tunnel VPN Always On
titleSuffix: Azure Virtual WAN
description: Découvrez comment configurer un tunnel d’appareil VPN Always On pour un WAN virtuel.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 05/26/2021
ms.author: cherylmc
ms.openlocfilehash: 69899179b65400e8a3b81f497e950aa3123c624e
ms.sourcegitcommit: e1d5abd7b8ded7ff649a7e9a2c1a7b70fdc72440
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2021
ms.locfileid: "110579738"
---
# <a name="configure-an-always-on-vpn-device-tunnel-for-virtual-wan"></a>Configurer un tunnel d’appareil VPN Always On pour un WAN virtuel

[!INCLUDE [intro](../../includes/vpn-gateway-vwan-always-on-intro.md)]

## <a name="prerequisites"></a>Prérequis

Vous devez créer une configuration de point à site et modifier l’affectation du hub virtuel. Consultez les sections suivantes pour obtenir des instructions :

* [Créer une configuration P2S](virtual-wan-point-to-site-portal.md#p2sconfig)
* [Créer un hub avec la passerelle P2S](virtual-wan-point-to-site-portal.md#hub)

## <a name="configure-the-device-tunnel"></a>Configurer le tunnel d’appareil

[!INCLUDE [device tunnel](../../includes/vpn-gateway-vwan-always-on-device.md)]

## <a name="to-remove-a-profile"></a>Pour supprimer un profil

Pour supprimer le profil, exécutez la commande suivante :

![La capture d’écran affiche une fenêtre PowerShell exécutant la commande Remove-VpnConnection -Name MachineCertTest.](./media/howto-always-on-device-tunnel/cleanup.png)

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur Virtual WAN, consultez la [FAQ](virtual-wan-faq.md).