---
title: À propos des passerelles de réseau virtuel redondantes dans une zone dans les Zones de disponibilité Azure | Microsoft Docs
description: Apprenez-en plus sur les passerelles VPN et ExpressRoute dans les Zones de disponibilité.
services: vpn-gateway
author: cherylmc
Customer intent: As someone with a basic network background, I want to understand zone-redundant gateways.
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 09/21/2018
ms.author: cherylmc
ms.openlocfilehash: 27bac5265a5e884b808c4ccb58fda0b2fffeb774
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46975649"
---
# <a name="about-zone-redundant-virtual-network-gateways-in-azure-availability-zones"></a>À propos des passerelles de réseau virtuel redondantes dans une zone dans les Zones de disponibilité Azure

Vous pouvez déployer des passerelles VPN et ExpressRoute dans des [zones de disponibilité Azure](../availability-zones/az-overview.md). Cela apporte de la résilience, de l’extensibilité et une plus grande disponibilité aux passerelles de réseau virtuel. Le déploiement de passerelles dans les zones de disponibilité Azure sépare les passerelles physiquement et logiquement au sein d’une région, tout en protégeant votre connectivité de réseau local à Azure à partir d’échecs au niveau de la zone.

### <a name="zrgw"></a>Passerelles redondantes dans une zone

Pour déployer automatiquement vos passerelles de réseau virtuel entre des zones de disponibilité, vous pouvez utiliser des passerelles de réseau virtuel redondantes dans une zone. Avec les passerelles redondantes dans une zone, vous pouvez bénéficier de la résilience aux zones pour accéder à vos services évolutifs et critiques sur Azure.

<br>
<br>

![graphique des passerelles redondantes dans une zone](./media/create-zone-redundant-vnet-gateway/zonered.png)

### <a name="zgw"></a>Passerelles zonales

Pour le déploiement de passerelles dans une zone spécifique, vous pouvez utiliser des passerelles zonales. Lorsque vous déployez une passerelle zonale, toutes les instances de la passerelle sont déployées dans la même Zone de disponibilité.

<br>
<br>

![graphique sur les passerelles zonales](./media/create-zone-redundant-vnet-gateway/zonal.png)

## <a name="gwskus"></a>SKU de passerelle

Les passerelles zonales et redondantes dans une zone sont disponibles en tant que nouvelles références SKU de passerelle. Nous avons ajouté de nouvelles références SKU de passerelle de réseau virtuel dans les régions Azure AZ. Ces références SKU sont semblables aux références SKU correspondantes qui existent déjà pour les passerelles VPN et ExpressRoute, sauf qu’elles sont spécifiques aux passerelles zonales et redondantes dans une zone.

Les nouvelles références SKU de passerelle sont :

### <a name="vpn-gateway"></a>Passerelle VPN

* VpnGw1AZ
* VpnGw2AZ
* VpnGw3AZ

### <a name="expressroute"></a>ExpressRoute

* ErGw1AZ
* ErGw2AZ
* ErGw3AZ

## <a name="pipskus"></a>Références SKU d’adresse IP publique

Les passerelles zonales et redondantes dans une zone s’appuient sur la référence (SKU) de ressource d’adresse IP publique *Standard*. La configuration de la ressource d’adresse IP publique Azure détermine si la passerelle que vous déployez est zonale ou redondante dans une zone. Si vous créez une ressource d’adresse IP publique avec une référence (SKU) *De base*, la passerelle n’aura aucune redondance de zone, et les ressources de la passerelle seront régionales.

### <a name="pipzrg"></a>Passerelles redondantes dans une zone

Lorsque vous créez une adresse IP publique à l’aide de la référence SKU d’adresse IP publique **Standard** sans spécifier de zone, le comportement diffère selon que la passerelle est une passerelle VPN ou une passerelle ExpressRoute. 

* Pour une passerelle VPN, les deux instances de passerelle seront déployées dans 2 de ces zones, parmi les trois zones assurant la redondance de zone. 
* Pour une passerelle ExpressRoute, dans la mesure où il peut y avoir plus de deux instances, la passerelle peut s’étendre sur les trois zones.

### <a name="pipzg"></a>Passerelles zonales

Lorsque vous créez une adresse IP publique à l’aide de la référence SKU d’adresse IP **Standard** et spécifiez la zone (1, 2 ou 3), toutes les instances de la passerelle sont déployées dans la même zone.

### <a name="piprg"></a>Passerelles régionales

Lorsque vous créez une adresse IP publique à l’aide de la référence SKU d’adresse IP publique **De base**, la passerelle est déployée comme une passerelle régionale et n’a pas de redondance de zone intégrée.

## <a name="faq"></a>Forum Aux Questions

### <a name="what-will-change-when-i-deploy-these-new-skus"></a>Qu’est-ce qui va changer lorsque je déploierai ces nouvelles références SKU ?

De votre point de vue, vous pouvez déployer vos passerelles avec la redondance dans une zone. Cela signifie que toutes les instances des passerelles seront déployées sur des zones de disponibilité Azure, et que chaque zone de disponibilité est un autre domaine d’erreur et de mise à jour. Cela rend vos passerelles plus fiables, disponibles et résilientes aux défaillances de zone.

### <a name="can-i-use-the-azure-portal"></a>Puis-je utiliser le portail Azure ?

Oui, vous pouvez utiliser le Portail Azure pour déployer les nouvelles références SKU. Toutefois, vous ne les verrez que dans les régions Azure disposant de Zones de disponibilité Azure.

### <a name="what-regions-are-available-for-me-to-use-the-new-skus"></a>Dans quelles régions puis-je utiliser les nouvelles références SKU ?

Les nouvelles références SKU sont disponibles dans les régions Azure disposant de Zones de disponibilité Azure, c’est-à-dire les régions USA Centre, France Centre et Europe Ouest. À l’avenir, nous rendrons les passerelles redondantes dans une zone disponibles pour vous dans d’autres régions Azure publiques.

### <a name="can-i-changemigrateupgrade-my-existing-virtual-network-gateways-to-zone-redundant-or-zonal-gateways"></a>Puis-je changer/migrer/mettre à niveau mes passerelles de réseau virtuel existantes vers des passerelles zonales ou redondantes dans une zone ?

La migration de vos passerelles de réseau virtuel existantes vers des passerelles zonales ou redondantes dans une zone n’est pas prise en charge pour le moment. Toutefois, vous pouvez supprimer votre passerelle existante et recréer une passerelle zonale ou redondante dans une zone.

### <a name="can-i-deploy-both-vpn-and-express-route-gateways-in-same-virtual-network"></a>Puis-je déployer des passerelles VPN et ExpressRoute dans le même réseau virtuel ?

La coexistence de passerelles VPN et ExpressRoute dans le même réseau virtuel est prise en charge. Toutefois, vous devez réserver une plage d’adresses IP de /27 pour le sous-réseau de passerelle.

## <a name="next-steps"></a>Étapes suivantes

[Créer une passerelle de réseau virtuel redondant dans une zone](create-zone-redundant-vnet-gateway.md)