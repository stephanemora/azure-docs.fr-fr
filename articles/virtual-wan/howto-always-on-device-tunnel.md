---
title: Configurer un tunnel VPN Always On
titleSuffix: Azure Virtual WAN
description: Étapes de configuration d’un tunnel d’appareil VPN Always On pour un WAN virtuel
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: e814487cb4dab9c8c19daab2ea3bb81391d4a98f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "90983681"
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