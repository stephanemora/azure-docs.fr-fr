---
title: Connectivité et réseau Azure VMware Solution
description: Description de la connectivité et du réseau Azure VMware Solution.
ms.topic: include
ms.date: 05/28/2021
ms.openlocfilehash: 66ea12565aa84661a4019e5685a5544792591b0c
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/02/2021
ms.locfileid: "111350913"
---
<!-- Used in introduction.md and concepts-networking.md -->

Azure VMware Solution offre un environnement de cloud privé, qui est accessible à partir de ressources locales et Azure. Des services comme Azure ExpressRoute, des connexions VPN ou Azure Virtual WAN offrent la connectivité. Ces services requièrent des plages d’adresses réseau et des ports de pare-feu spécifiques pour leur activation.

Lors du déploiement d’un cloud privé, des réseaux privés pour la gestion, l’approvisionnement et vMotion sont créés. Utilisez ces réseaux privés pour accéder à vCenter et NSX-T Manager et à vMotion des machines virtuelles, ou pour le déploiement.  

ExpressRoute Global Reach est utilisé pour connecter les clouds privés à votre environnement local. La connexion requiert un réseau virtuel avec un circuit ExpressRoute local dans votre abonnement.

Les machines virtuelles déployées sur le cloud privé sont accessibles à Internet via la fonctionnalité d’adresse IP publique d’Azure Virtual WAN.  Par défaut, l’accès à Internet est désactivé pour les nouveaux clouds privés. Pour plus d’informations, consultez [Activer l’accès Internet public dans Azure VMware Solution](../enable-public-internet-access.md).



