---
title: 'Création d’une passerelle VPN basée sur un itinéraire : portail'
titleSuffix: Azure VPN Gateway
description: Utilisez le Portail Azure pour créer rapidement une passerelle VPN Azure basée sur des itinéraires, pour une connexion VPN à votre réseau local ou pour connecter des réseaux virtuels.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/02/2020
ms.author: cherylmc
ms.openlocfilehash: 104d911164d0194efba41f1405c17fc240e8906d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89393766"
---
# <a name="create-a-route-based-vpn-gateway-using-the-azure-portal"></a>Créer une passerelle VPN basée sur des itinéraires à l’aide du portail Azure

Cet article vous aidera à créer rapidement une passerelle VPN basée sur des itinéraires à l’aide du portail Azure.  Une passerelle VPN permet de créer une connexion VPN à un réseau local. Vous pouvez également vous en servir pour connecter des réseaux virtuels. 

Les étapes décrites dans cet article permettent de créer un réseau virtuel, un sous-réseau, un sous-réseau de passerelle et une passerelle VPN basée sur des itinéraires (passerelle de réseau virtuel). Vous pourrez ensuite créer des connexions. Ces étapes nécessitent un abonnement Azure. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="create-a-virtual-network"></a><a name="vnet"></a>Créer un réseau virtuel

[!INCLUDE [Basic Point-to-Site VNet](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="configure-and-create-the-gateway"></a><a name="gwvalues"></a>Configurer et créer la passerelle

Dans cette étape, vous créez la passerelle de réseau virtuel de votre réseau virtuel. La création d’une passerelle nécessite généralement au moins 45 minutes, selon la référence SKU de passerelle sélectionnée.

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-portal-include.md)]

[!INCLUDE [Create a gateway](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

>[!NOTE]
>La référence SKU de passerelle De base ne prend pas en charge IKEv2 ou l’authentification RADIUS. Si vous envisagez de connecter des clients Mac à votre réseau virtuel, n’utilisez pas la référence SKU De base.

[!INCLUDE [NSG warning](../../includes/vpn-gateway-no-nsg-include.md)]

## <a name="view-the-vpn-gateway"></a><a name="viewgw"></a>Afficher la passerelle VPN

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
