---
title: Connectivité et réseau Azure VMware Solution
description: Description de la connectivité et du réseau Azure VMware Solution.
ms.topic: include
ms.date: 08/10/2021
author: shortpatti
ms.author: v-patsho
ms.service: azure-vmware
ms.openlocfilehash: d758d42ff1e5c77ddd1e17811e2eea24717878aa
ms.sourcegitcommit: e7d500f8cef40ab3409736acd0893cad02e24fc0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122070719"
---
<!-- Used in introduction.md and concepts-networking.md -->

Azure VMware Solution offre un environnement de cloud privé, qui est accessible à partir de ressources locales et Azure. Des services tels qu’Azure ExpressRoute, Azure Virtual WAN ou les connexions VPN fournissent la connectivité nécessaire. Toutefois, ces services nécessitent des plages d’adresses réseau et des ports de pare-feu spécifiques pour leur activation.

Lors du déploiement d’un cloud privé, des réseaux privés pour la gestion, l’approvisionnement et vMotion sont créés. Ces réseaux privés vous permettent d’accéder à vCenter et NSX-T Manager ainsi qu’à la migration vMotion ou au déploiement des machines virtuelles.

[!INCLUDE [expressroute-global-reach](expressroute-global-reach.md)]

Les machines virtuelles déployées sur le cloud privé sont accessibles à Internet via la fonctionnalité d’[adresse IP publique d’Azure Virtual WAN](../enable-public-internet-access.md). Pour les nouveaux clouds privés, l’accès à Internet est désactivé par défaut. 



