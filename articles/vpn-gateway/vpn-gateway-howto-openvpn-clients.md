---
title: Comment configurer les clients OpenVPN pour les passerelles VPN P2S
titleSuffix: Azure VPN Gateway
description: Découvrez comment configurer les clients OpenVPN pour la passerelle VPN Azure. Cet article vous aide à configurer les clients Windows, Linux, iOS et Mac.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 07/27/2021
ms.author: cherylmc
ms.openlocfilehash: 8c9ddff536c74182e1c13d51dde2900f07fb7470
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122562380"
---
# <a name="configure-openvpn-clients-for-azure-vpn-gateway"></a>Configurer des clients OpenVPN sur la passerelle VPN Azure

Cet article vous permet de configurer des clients pour l’utilisation du **protocole&reg; OpenVPN**.

## <a name="before-you-begin"></a>Avant de commencer

Vérifiez que vous avez terminé les étapes de configuration de OpenVPN pour votre passerelle VPN. Pour plus d’informations, consultez [Configurer OpenVPN pour la passerelle VPN Azure](vpn-gateway-howto-openvpn.md).

## <a name="vpn-client-configuration-files"></a>Fichiers de configuration de client VPN

Vous pouvez générer et télécharger des fichiers de configuration de client VPN à partir du portail ou à l’aide de PowerShell. L’une ou l’autre des méthodes retourne le même fichier zip. Décompressez le fichier pour afficher le dossier OpenVPN.

:::image type="content" source="./media/howto-openvpn-clients/download.png" alt-text="Capture d’écran du téléchargement du client VPN en surbrillance." :::

[!INCLUDE [configuration steps](../../includes/vpn-gateway-vwan-config-openvpn-clients.md)]

## <a name="next-steps"></a>Étapes suivantes

Si vous souhaitez que les clients VPN puissent accéder aux ressources d’un autre réseau virtuel, suivez les instructions de l’article [Réseau virtuel à réseau virtuel](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) qui explique comment définir une connexion entre des réseaux virtuels. Veillez à activer BGP sur les passerelles et les connexions, sinon le trafic ne passe pas.

**« OpenVPN » est une marque d’OpenVPN Inc.**
