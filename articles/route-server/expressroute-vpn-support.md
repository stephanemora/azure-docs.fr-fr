---
title: À propos de la prise en charge d’ExpressRoute et de VPN Azure par le Serveur de routes Azure
description: Découvrez comment Azure Route Server interagit avec les passerelles ExpressRoute et VPN Azure.
services: route-server
author: duongau
ms.service: route-server
ms.topic: conceptual
ms.date: 10/01/2021
ms.author: duau
ms.openlocfilehash: 61a712fa747252b07a513cfa6e18e3f3b4a67e6c
ms.sourcegitcommit: 03e84c3112b03bf7a2bc14525ddbc4f5adc99b85
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/03/2021
ms.locfileid: "129400721"
---
# <a name="about-azure-route-server-support-for-expressroute-and-azure-vpn"></a>À propos de la prise en charge d’ExpressRoute et de VPN Azure par le Serveur de routes Azure

Non seulement Azure Route Server prend en charge les appliances virtuelles réseau (NVA) tierces s’exécutant sur Azure, mais il s’intègre parfaitement aux passerelles ExpressRoute et VPN Azure. Vous n’avez pas besoin de configurer ou de gérer le peering BGP entre la passerelle et Azure Route Server. Vous pouvez activer l’échange de routage entre la passerelle et Azure Route Server d’une simple [modification de configuration](quickstart-configure-route-server-powershell.md#route-exchange).


## <a name="how-does-it-work"></a>Comment cela fonctionne-t-il ?

Lorsque vous déployez un serveur de routage Azure avec une passerelle ExpressRoute et une appliance virtuelle réseau dans un réseau virtuel par défaut, Azure Route Server ne propage pas entre elles les routes qu’il reçoit de l’appliance virtuelle réseau et de la passerelle ExpressRoute. Dès lors que vous avez activé l’échange de routage, ExpressRoute et l’appliance virtuelle réseau apprennent les routes l’une de l’autre.

Par exemple, dans le diagramme ci-dessous :

* L’appliance SDWAN recevra d’Azure Route Server la route provenant de « On-prem 2 », qui est connecté à ExpressRoute, ainsi que celle du réseau virtuel.

* La passerelle ExpressRoute recevra la route provenant de « On-prem 1 », qui est connecté à l’appliance SDWAN, ainsi que celle du réseau virtuel provenant d’Azure Route Server.

    ![Diagramme montrant ExpressRoute configuré avec Route Server.](./media/expressroute-vpn-support/expressroute-with-route-server.png)

Vous pouvez également remplacer l’appliance SDWAN par la passerelle VPN Azure. Du fait que la passerelle VPN Azure et ExpressRoute sont complètement managés, il vous suffit d’activer l’échange de routage pour que les deux réseaux locaux communiquent entre eux.

> [!IMPORTANT] 
> La passerelle VPN Azure doit être configurée en mode [**actif/actif**](../vpn-gateway/vpn-gateway-activeactive-rm-powershell.md) et son ASN doit être défini sur 65515.
>

![Diagramme montrant ExpressRoute et la passerelle VPN configurés avec Route Server.](./media/expressroute-vpn-support/expressroute-and-vpn-with-route-server.png)

## <a name="next-steps"></a>Étapes suivantes

- Apprenez-en davantage sur [Azure Route Server](route-server-faq.md).
- Découvrez comment [configurer Azure Route Server](quickstart-configure-route-server-powershell.md).
- Apprenez-en davantage sur la [coexistence d’Azure ExpressRoute et de VPN Azure](../expressroute/expressroute-howto-coexist-resource-manager.md).
