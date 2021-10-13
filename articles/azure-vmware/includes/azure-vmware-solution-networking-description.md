---
title: Connectivité et réseau Azure VMware Solution
description: Description de la connectivité et du réseau Azure VMware Solution.
ms.topic: include
ms.date: 08/10/2021
author: suzizuber
ms.author: v-szuber
ms.service: azure-vmware
ms.openlocfilehash: 0ee52f437027690787ca5dc95eda2705b796b604
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/06/2021
ms.locfileid: "129638644"
---
<!-- Used in introduction.md and concepts-networking.md -->

Azure VMware Solution offre un environnement de cloud privé, qui est accessible à partir de ressources locales et Azure. Des services tels qu’Azure ExpressRoute, Azure Virtual WAN ou les connexions VPN fournissent la connectivité nécessaire. Toutefois, ces services nécessitent des plages d’adresses réseau et des ports de pare-feu spécifiques pour leur activation.

Lors du déploiement d’un cloud privé, des réseaux privés pour la gestion, l’approvisionnement et vMotion sont créés. Ces réseaux privés vous permettent d’accéder à vCenter et NSX-T Manager ainsi qu’à la migration vMotion ou au déploiement des machines virtuelles.

[!INCLUDE [expressroute-global-reach](expressroute-global-reach.md)]

Les machines virtuelles déployées sur le cloud privé sont accessibles à Internet via la fonctionnalité d’[adresse IP publique d’Azure Virtual WAN](../enable-public-internet-access.md). Pour les nouveaux clouds privés, l’accès à Internet est désactivé par défaut. 



