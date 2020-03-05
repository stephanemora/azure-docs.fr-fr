---
title: 'Création d’une passerelle VPN basée sur un itinéraire : portail'
titleSuffix: Azure VPN Gateway
description: Créer une passerelle VPN basée sur des itinéraires à l’aide du portail Azure
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 03/03/2020
ms.author: cherylmc
ms.openlocfilehash: f2b8791c4a8714257f1e085fcf1af5517634daf1
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2020
ms.locfileid: "78303390"
---
# <a name="create-a-route-based-vpn-gateway-using-the-azure-portal"></a>Créer une passerelle VPN basée sur des itinéraires à l’aide du portail Azure

Cet article vous aidera à créer rapidement une passerelle VPN basée sur des itinéraires à l’aide du portail Azure.  Une passerelle VPN permet de créer une connexion VPN à un réseau local. Vous pouvez également vous en servir pour connecter des réseaux virtuels. 

Les étapes décrites dans cet article permettent de créer un réseau virtuel, un sous-réseau, un sous-réseau de passerelle et une passerelle VPN basée sur des itinéraires (passerelle de réseau virtuel). Vous pourrez ensuite créer des connexions. Ces étapes nécessitent un abonnement Azure. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="vnet"></a>Créer un réseau virtuel

[!INCLUDE [Create a virtual network](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="gwvalues"></a>Configurer et créer la passerelle

Dans cette étape, vous créez la passerelle de réseau virtuel de votre réseau virtuel. La création d’une passerelle nécessite généralement au moins 45 minutes, selon la référence SKU de passerelle sélectionnée.

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-portal-include.md)]

[!INCLUDE [create-gateway](../../includes/vpn-gateway-add-gw-p2s-rm-portal-include.md)]

>[!NOTE]
>La référence SKU de passerelle De base ne prend pas en charge IKEv2 ou l’authentification RADIUS. Si vous envisagez de connecter des clients Mac à votre réseau virtuel, n’utilisez pas la référence SKU De base.

[!INCLUDE [NSG warning](../../includes/vpn-gateway-no-nsg-include.md)]

## <a name="viewgw"></a>Afficher la passerelle VPN

1. Une fois la passerelle créée, accédez à VNet1 dans le portail. La passerelle VPN apparaît dans la page Vue d’ensemble en tant qu’appareil connecté.

   ![Appareils connectés](./media/create-routebased-vpn-gateway-portal/view-connected-devices.png "Appareils connectés")

2. Dans la liste des appareils, cliquez sur **VNet1GW** pour afficher des informations supplémentaires.

   ![Afficher la passerelle VPN](./media/create-routebased-vpn-gateway-portal/view-gateway.png "Afficher la passerelle VPN")

## <a name="next-steps"></a>Étapes suivantes

Une fois la création de la passerelle terminée, vous pouvez établir une connexion entre votre réseau virtuel et un autre réseau virtuel. Sinon, créez une connexion entre votre réseau virtuel et un emplacement local.

> [!div class="nextstepaction"]
> [Créer une connexion de site à site](vpn-gateway-howto-site-to-site-resource-manager-portal.md)<br><br>
> [Créer une connexion de point à site](vpn-gateway-howto-point-to-site-resource-manager-portal.md)<br><br>
> [Créer une connexion à un autre réseau virtuel](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
