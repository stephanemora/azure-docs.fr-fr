---
title: 'Azure ExpressRoute : ajouter la prise en charge IPv6 à l’aide du Portail Azure'
description: Découvrez comment ajouter la prise en charge IPv6 pour se connecter à des déploiements Azure à l’aide du Portail Azure.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 03/09/2021
ms.author: duau
ms.openlocfilehash: 7f5afc05a8d03d33366a2f76318bcf5e039d4d30
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105561659"
---
# <a name="add-ipv6-support-for-private-peering-using-the-azure-portal-preview"></a>Ajouter la prise en charge IPv6 pour le Peering privé à l’aide du Portail Azure (préversion)

Cet article explique comment ajouter la prise en charge IPv6 pour se connecter via ExpressRoute à vos ressources dans Azure à l’aide du Portail Azure. 

> [!Note]
> Cette fonctionnalité est actuellement disponible en préversion dans les [régions Azure avec Zones de disponibilité](../availability-zones/az-region.md#azure-regions-with-availability-zones). Votre circuit ExpressRoute peut donc être créé à l’aide de n’importe quel emplacement de Peering, mais les déploiements basés sur IPv6 auxquels il se connecte doivent se trouver dans une région avec Zones de disponibilité.

## <a name="register-for-public-preview"></a>S’inscrire pour la préversion publique
Avant d’ajouter la prise en charge IPv6, vous devez d’abord inscrire votre abonnement. Pour vous inscrire, exécutez les commandes suivantes via Azure PowerShell :

1.  Connectez-vous à Azure et sélectionnez l’abonnement. Exécutez ces commandes pour l’abonnement contenant votre circuit ExpressRoute, ainsi que l’abonnement contenant vos déploiements Azure (s’ils sont différents).

    ```azurepowershell-interactive
    Connect-AzAccount 

    Select-AzSubscription -Subscription "<SubscriptionID or SubscriptionName>"
    ```

1. Inscrivez votre abonnement pour la préversion publique à l’aide de la commande suivante :
    ```azurepowershell-interactive
    Register-AzProviderFeature -FeatureName AllowIpv6PrivatePeering -ProviderNamespace Microsoft.Network
    ```

Votre requête sera ensuite approuvée par l’équipe ExpressRoute dans un délai de 2-3 jours ouvrables.

## <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Dans un navigateur, accédez au [Portail Azure](https://portal.azure.com) et connectez-vous avec votre compte Azure.

## <a name="add-ipv6-private-peering-to-your-expressroute-circuit"></a>Ajouter le peering privé IPv6 à votre circuit ExpressRoute

1. [Créez un circuit ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md) ou accédez au circuit existant que vous souhaitez modifier.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/navigate-to-circuit.png" alt-text="Capture d’écran de la liste des circuits ExpressRoute.":::

1. Sélectionnez la configuration de peering **privé Azure**.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/navigate-to-peering.png" alt-text="Capture d’écran de la page de présentation d’ExpressRoute.":::

1. Ajoutez un peering privé IPv6 à votre configuration de peering privé IPv4 existante en sélectionnant « Les deux » pour les **sous-réseaux**, ou activez uniquement le Peering privé IPv6 sur votre nouveau circuit en sélectionnant « IPv6 ». Indiquez une paire de sous-réseaux IPv6 /126 dont vous êtes propriétaire pour votre lien principal et vos liens secondaires. À partir de chacun de ces sous-réseaux, vous allez attribuer la première adresse IP utilisable à votre routeur. Microsoft utilise la deuxième IP utilisable pour son routeur. **Enregistrez** votre configuration de peering après avoir spécifié tous les paramètres.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/add-ipv6-peering.png" alt-text="Capture d’écran de l’ajout d’Ipv6 sur la page de Peering privé.":::

1. Une fois la configuration acceptée, vous devez voir quelque chose de similaire à l’exemple suivant.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/view-ipv6-peering.png" alt-text="Capture d’écran d’Ipv6 configuré pour le Peering privé.":::

## <a name="update-your-connection-to-an-existing-virtual-network"></a>Mettre à jour votre connexion à un réseau virtuel existant

Suivez les étapes ci-dessous si vous disposez d’un environnement de ressources Azure existant dans une région avec Zones de disponibilité que vous souhaitez utiliser avec votre Peering privé IPv6.

1. Accédez au réseau virtuel auquel votre circuit ExpressRoute est connecté.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/navigate-to-vnet.png" alt-text="Capture d’écran de la liste des réseaux virtuels.":::

1. Accédez à l’onglet **Espace d’adressage** et ajoutez un espace d’adressage IPv6 à votre réseau virtuel. **Enregistrez** votre espace d’adressage.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/add-ipv6-space.png" alt-text="Capture d’écran de l’ajout de l’espace d’adressage IPv6 au réseau virtuel.":::

1. Accédez à l’onglet **Sous-réseaux** et sélectionnez **GatewaySubnet**. Cochez la case **Ajouter un espace d’adressage IPv6** et indiquez un espace d’adressage IPv6 pour votre sous-réseau. Le sous-réseau IPv6 de la passerelle doit être /64 ou plus. **Enregistrez** votre configuration après avoir spécifié tous les paramètres.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/add-ipv6-gateway-space.png" alt-text="Capture d’écran de l’ajout de l’espace d’adressage Ipv6 au sous-réseau.":::

1. Si vous disposez d’une passerelle redondante dans une zone, accédez à votre passerelle ExpressRoute et actualisez la ressource pour activer la connectivité IPv6. Sinon, [créez la passerelle de réseau virtuel](expressroute-howto-add-gateway-portal-resource-manager.md) à l’aide d’une référence SKU redondante dans une zone (ErGw1AZ, ErGw2AZ, ErGw3AZ). Si vous envisagez d’utiliser FastPath, utilisez ErGw3AZ.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/refresh-gateway.png" alt-text="Capture d’écran de l’actualisation de la passerelle.":::

## <a name="create-a-connection-to-a-new-virtual-network"></a>Créer une connexion à un nouveau réseau virtuel

Suivez les étapes ci-dessous si vous envisagez de vous connecter à un nouvel ensemble de ressources Azure dans une région avec Zones de disponibilité à l’aide de votre Peering privé IPv6.

1. Créez un réseau virtuel à double pile avec l’espace d’adressage IPv4 et IPv6. Pour plus d’informations, consultez [Créer un réseau virtuel](../virtual-network/quick-create-portal.md#create-a-virtual-network).

1. [Créez le sous-réseau de passerelle à double pile](expressroute-howto-add-gateway-portal-resource-manager.md#create-the-gateway-subnet).

1. [Créez la passerelle de réseau virtuel](expressroute-howto-add-gateway-portal-resource-manager.md#create-the-virtual-network-gateway) à l’aide d’une référence SKU redondante dans une zone (ErGw1AZ, ErGw2AZ, ErGw3AZ). Si vous envisagez d’utiliser FastPath, utilisez la référence ErGw3AZ (notez que cette option n’est disponible que pour les circuits utilisant ExpressRoute Direct).

1. [Liez votre réseau virtuel à votre circuit ExpressRoute](expressroute-howto-linkvnet-portal-resource-manager.md).

## <a name="limitations"></a>Limites
Bien que la prise en charge d’IPV6 soit disponible pour les connexions à des déploiements dans des régions avec Zones de disponibilité, les cas d’usage suivants ne sont pas pris en charge :

* Connexions à des déploiements dans Azure via une référence SKU de passerelle ExpressRoute sans AZ
* Connexions à des déploiements dans des régions sans AZ
* Connexions Global Reach entre des circuits ExpressRoute
* Utiliser ExpressRoute avec un WAN virtuel
* FastPath avec circuits non ExpressRoute Direct
* FastPath avec circuits dans les emplacements de Peering suivants : Dubaï
* Coexistence avec une passerelle VPN

## <a name="next-steps"></a>Étapes suivantes

Pour résoudre des problèmes liés à ExpressRoute, consultez les articles suivants :

* [Vérification de la connectivité ExpressRoute](expressroute-troubleshooting-expressroute-overview.md)
* [Résolution des problèmes de performances réseau](expressroute-troubleshooting-network-performance.md)