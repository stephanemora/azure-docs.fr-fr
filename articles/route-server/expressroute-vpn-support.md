---
title: À propos de la prise en charge d’ExpressRoute et de VPN Azure par Azure Route Server (préversion)
description: Découvrez comment Azure Route Server interagit avec les passerelles ExpressRoute et VPN Azure.
services: route-server
author: duongau
ms.service: route-server
ms.topic: conceptual
ms.date: 03/02/2021
ms.author: duau
ms.openlocfilehash: 6e588c7c0381c6825bcf75cbbe28a1dd6b865940
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101678558"
---
# <a name="about-azure-route-server-preview-support-for-expressroute-and-azure-vpn"></a>À propos de la prise en charge d’ExpressRoute et de VPN Azure par Azure Route Server (préversion)

Non seulement Azure Route Server prend en charge les appliances virtuelles réseau (NVA) tierces s’exécutant sur Azure, mais il s’intègre parfaitement aux passerelles ExpressRoute et VPN Azure. Vous n’avez pas besoin de configurer ou de gérer le peering BGP entre la passerelle et Azure Route Server. Vous pouvez activer l’échange de routage entre la passerelle et Azure Route Server d’une simple [modification de configuration](quickstart-configure-route-server-powershell.md#route-exchange).

> [!IMPORTANT]
> Azure Route Server (préversion) est en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="how-does-it-work"></a>Comment cela fonctionne-t-il ?

Lorsque vous déployez un serveur de routage Azure avec une passerelle ExpressRoute et une appliance virtuelle réseau dans un réseau virtuel par défaut, Azure Route Server ne propage pas entre elles les routes qu’il reçoit de l’appliance virtuelle réseau et de la passerelle ExpressRoute. Dès lors que vous avez activé l’échange de routage, ExpressRoute et l’appliance virtuelle réseau apprennent les routes l’une de l’autre.

Par exemple, dans le diagramme ci-dessous :

* L’appliance SDWAN recevra d’Azure Route Server la route provenant de « On-prem 2 », qui est connecté à ExpressRoute, ainsi que celle du réseau virtuel.

* La passerelle ExpressRoute recevra la route provenant de « On-prem 1 », qui est connecté à l’appliance SDWAN, ainsi que celle du réseau virtuel provenant d’Azure Route Server.

    ![Diagramme montrant ExpressRoute configuré avec Route Server.](./media/expressroute-vpn-support/expressroute-with-route-server.png)

Vous pouvez également remplacer l’appliance SDWAN par la passerelle VPN Azure. Du fait que la passerelle VPN Azure et ExpressRoute sont complètement managés, il vous suffit d’activer l’échange de routage pour que les deux réseaux locaux communiquent entre eux.

> [!IMPORTANT] 
> La passerelle VPN Azure doit être configurée en mode [**actif/actif**](../vpn-gateway/vpn-gateway-activeactive-rm-powershell.md).
>

![Diagramme montrant ExpressRoute et la passerelle VPN configurés avec Route Server.](./media/expressroute-vpn-support/expressroute-and-vpn-with-route-server.png)

## <a name="next-steps"></a>Étapes suivantes

- Apprenez-en davantage sur [Azure Route Server](route-server-faq.md).
- Découvrez comment [configurer Azure Route Server](quickstart-configure-route-server-powershell.md).
- Apprenez-en davantage sur la [coexistence d’Azure ExpressRoute et de VPN Azure](../expressroute/expressroute-howto-coexist-resource-manager.md).
