---
title: À propos des passerelles de réseau virtuel Azure ExpressRoute | Microsoft Docs
description: En savoir plus sur les passerelles de réseau virtuel pour ExpressRoute.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 07/19/2018
ms.author: cherylmc
ms.openlocfilehash: d89463826cb05bf4cafd36cf4a38e60852d93094
ms.sourcegitcommit: 1af4bceb45a0b4edcdb1079fc279f9f2f448140b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/09/2018
ms.locfileid: "42141258"
---
# <a name="about-virtual-network-gateways-for-expressroute"></a>À propos des passerelles de réseau virtuel pour ExpressRoute
Une passerelle de réseau virtuel est conçue pour faire circuler le trafic réseau entre les réseaux virtuels Azure et les emplacements locaux. Vous pouvez utiliser une passerelle de réseau virtuel pour le trafic ExpressRoute ou le trafic VPN. Cet article décrit la passerelle de réseau virtuelle ExpressRoute.

## <a name="gateway-types"></a>Types de passerelle

Lorsque vous créez une passerelle de réseau virtuel, vous spécifiez plusieurs paramètres. L’un des paramètres requis, « Type Passerelle », spécifie si la passerelle est utilisée pour le trafic ExpressRoute, ou le trafic VPN. Les types de passerelles sont les suivants: 

* **Vpn** : Pour envoyer le trafic chiffré sur l’Internet public, vous utilisez le type de passerelle « Vpn ». Il s’agit alors d’une passerelle VPN. Les connexions site à site, point à site et réseau virtuel à réseau virtuel utilisent toutes une passerelle VPN.

* **ExpressRoute** : Pour envoyer le trafic réseau sur une connexion privée, vous utilisez le type de passerelle « ExpressRoute ». C’est également appelé une passerelle ExpressRoute et c’est le type de passerelle que vous utilisez lors de la configuration ExpressRoute.


Chaque réseau virtuel ne peut posséder qu’une seule passerelle de réseau virtuel par type de passerelle. Par exemple, une passerelle de réseau virtuel peut utiliser le type de passerelle VPN et une autre le type de passerelle ExpressRoute.

## <a name="gwsku"></a>SKU de passerelle
[!INCLUDE [expressroute-gwsku-include](../../includes/expressroute-gwsku-include.md)]

Si vous souhaitez mettre à niveau votre passerelle vers une référence (SKU) de passerelle plus puissante, dans la plupart des cas, vous pouvez utiliser l’applet de commande PowerShell « Resize-AzureRmVirtualNetworkGateway ». Cela fonctionne pour les mises à niveau vers les références (SKU) Standard HighPerformance. Toutefois, pour mettre à niveau vers la référence (SKU) UltraPerformance, vous devez recréer la passerelle.

### <a name="aggthroughput"></a>Performances estimées par référence (SKU) de passerelle
Le tableau ci-dessous présente les types de passerelle et les performances estimées. Cette table s’applique aux modèles de déploiement classique et Resource Manager.

[!INCLUDE [expressroute-table-aggthroughput](../../includes/expressroute-table-aggtput-include.md)]

> [!IMPORTANT]
> Les performances de l’application dépendent de plusieurs facteurs, tels que la latence de bout en bout et le nombre de flux de trafic que l’application ouvre. Les numéros indiqués dans le tableau représentent la limite supérieure que l’application peut théoriquement atteindre dans un environnement idéal. 
> 
>

### <a name="zrgw"></a>Référence SKU de passerelle redondante dans la zone (préversion)

Vous pouvez également déployer des passerelles ExpressRoute dans des zones de disponibilité Azure. Cela les sépare physiquement et logiquement dans différentes zones de disponibilité, tout en protégeant votre connectivité de réseau local à Azure des échecs au niveau de la zone.

![Passerelle ExpressRoute redondante dans la zone](./media/expressroute-about-virtual-network-gateways/zone-redundant.png)

Les passerelles redondante dans la zone utilisent de nouvelles références SKU spécifiques de passerelle pour la passerelle ExpressRoute. Les nouvelles références SKU sont actuellement disponibles en **Préversion publique**.

* ErGw1AZ
* ErGw2AZ
* ErGw3AZ

Les nouvelles références SKU de passerelle prennent également en charge les autres options de déploiement pour mieux répondre à vos besoins. Lorsque vous créez une passerelle de réseau virtuel avec les nouvelles références SKU de passerelle, vous avez également la possibilité de déployer la passerelle dans une zone spécifique. Il s’agit alors d’une passerelle zonale. Lorsque vous déployez une passerelle zonale, les deux instances de la passerelle sont déployées dans la même zone de disponibilité. Pour s’inscrire à la préversion, consultez [Créer une passerelle de réseau virtuel redondante dans la zone](../../articles/vpn-gateway/create-zone-redundant-vnet-gateway.md).

## <a name="resources"></a>API REST et applets de commande PowerShell
Pour accéder à des ressources techniques supplémentaires et connaître les exigences spécifiques en matière de syntaxe lors de l’utilisation d’API REST et d’applets de commande PowerShell pour les configurations de passerelles de réseau virtuel, consultez les pages suivantes :

| **Classique** | **Resource Manager** |
| --- | --- |
| [PowerShell](https://msdn.microsoft.com/library/mt270335.aspx) |[PowerShell](https://docs.microsoft.com/powershell/module/azurerm.network#networking) |
| [API REST](https://msdn.microsoft.com/library/jj154113.aspx) |[API REST](https://msdn.microsoft.com/library/mt163859.aspx) |

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur les configurations de connexion disponibles, consultez la rubrique [Vue d’ensemble d’ExpressRoute](expressroute-introduction.md) .

Consultez [Créer une passerelle de réseau virtuel pour ExpressRoute](expressroute-howto-add-gateway-resource-manager.md) pour plus d’informations sur la création des passerelles ExpressRoute.
