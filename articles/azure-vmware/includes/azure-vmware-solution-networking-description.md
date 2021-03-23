---
title: Connectivité et réseau Azure VMware Solution
description: Description de la connectivité et du réseau Azure VMware Solution.
ms.topic: include
ms.date: 03/13/2021
ms.openlocfilehash: 96dd93f1db5dc3ddcbb883313e19c6aed8a256da
ms.sourcegitcommit: afb9e9d0b0c7e37166b9d1de6b71cd0e2fb9abf5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/14/2021
ms.locfileid: "103462578"
---
<!-- Used in introduction.md and concepts-networking.md -->

Azure VMware Solution offre un environnement de cloud privé, qui est accessible à partir de ressources locales et Azure. Des services comme Azure ExpressRoute, des connexions VPN ou Azure Virtual WAN offrent la connectivité. Ces services requièrent des plages d’adresses réseau et des ports de pare-feu spécifiques pour leur activation.

Lors du déploiement d’un cloud privé, des réseaux privés pour la gestion, l’approvisionnement et vMotion sont créés. Utilisez ces réseaux privés pour accéder à vCenter et NSX-T Manager et à vMotion des machines virtuelles, ou pour le déploiement.  

ExpressRoute Global Reach est utilisé pour connecter les clouds privés à votre environnement local. La connexion requiert un réseau virtuel avec un circuit ExpressRoute local dans votre abonnement.

Les machines virtuelles déployées sur le cloud privé sont accessibles à Internet via la fonctionnalité d’adresse IP publique d’Azure Virtual WAN.  Par défaut, l’accès à Internet est désactivé pour les nouveaux clouds privés. Pour plus d’informations, consultez [Comment utiliser la fonctionnalité d’adresse IP publique dans Azure VMware Solution](../public-ip-usage.md).

