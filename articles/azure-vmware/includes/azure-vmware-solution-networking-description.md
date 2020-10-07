---
title: Connectivité et mise en réseau d’Azure VMWare Solution
description: Description de la connectivité et de la mise en réseau d’Azure VMWare Solution.
ms.topic: include
ms.date: 09/28/2020
ms.openlocfilehash: 1d43a8a49fc56a6a5b534a89fd1dfc2f5ae2d478
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91574452"
---
<!-- Used in introduction.md and concepts-networking.md -->

Azure VMware Solution offre un environnement de cloud privé, qui est accessible à partir de ressources ou d’environnements locaux et Azure. Des services tels qu’Azure ExpressRoute et les connexions VPN fournissent la connectivité. Ces services requièrent des plages d’adresses réseau et des ports de pare-feu spécifiques pour leur activation.

Lors du déploiement d’un cloud privé, des réseaux privés pour la gestion, l’approvisionnement et vMotion sont créés. Utilisez ces réseaux privés pour accéder à vCenter et NSX-T Manager et à vMotion des machines virtuelles, ou pour le déploiement.  ExpressRoute Global Reach est utilisé pour connecter les clouds privés à votre environnement local. La connexion requiert un réseau virtuel avec un circuit ExpressRoute dans votre abonnement.



>[!NOTE]
>L’accès à Internet et les services Azure sont configurés et fournis pour consommer des machines virtuelles sur des réseaux de production lors du déploiement d’un cloud privé.  Par défaut, l’accès à Internet est désactivé pour les nouveaux clouds privés, et peut être activé ou désactivé à tout moment.