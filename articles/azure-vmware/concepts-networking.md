---
title: Concepts – Interconnexion de réseaux
description: Découvrez des aspects clés et des cas d’utilisation de mise en réseau et d’interconnexion dans Azure VMware Solution.
ms.topic: conceptual
ms.date: 07/23/2020
ms.openlocfilehash: 3420f6aa61ced7632175f3e12edda9de72639517
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/22/2020
ms.locfileid: "88750564"
---
# <a name="azure-vmware-solution-preview-networking-and-interconnectivity-concepts"></a>Concepts de mise en réseau et d’interconnexion d’Azure VMware Solution en préversion

Azure VMware Solution offre un environnement de cloud privé VMware, qui est accessible aux utilisateurs et aux applications à partir de ressources ou d’environnements locaux et Azure. Des services tels qu’Azure ExpressRoute et les connexions VPN fournissent la connectivité. Ces services requièrent des plages d’adresses réseau et des ports de pare-feu spécifiques pour leur activation.  

Lors du déploiement d’un cloud privé, des réseaux privés pour la gestion, l’approvisionnement et vMotion sont créés. Ils sont utilisés pour accéder à vCenter et à NSX-T Manager, ainsi que pour le déploiement ou la migration vMotion de machines virtuelles. Tous les réseaux privés sont accessibles à partir d’un réseau virtuel dans Azure ou d’environnements locaux. ExpressRoute Global Reach est utilisé pour connecter des clouds privés à des environnements locaux, et cette connexion nécessite un réseau virtuel avec un circuit ExpressRoute dans votre abonnement.

En outre, lors du déploiement d’un cloud privé, l’accès à Internet et aux services Azure est approvisionné et fourni afin que les machines virtuelles sur les réseaux de production puissent les consommer.  Par défaut, l’accès à Internet est désactivé pour les nouveaux clouds privés, et peut être activé ou désactivé à tout moment.

Une approche utile de l’interconnexion consiste à prendre en compte les deux types d’implémentations de cloud privé AVS :

1. [**L’interconnexion Azure de base**](#azure-virtual-network-interconnectivity) vous permet de gérer et d’utiliser votre cloud privé avec un seul réseau virtuel dans Azure. Cette implémentation est idéale pour les évaluations ou implémentations d’AVS qui ne nécessitent pas d’accès à partir d’environnements locaux.

1. [**L’interconnexion complète entre un environnement local et un cloud privé**](#on-premises-interconnectivity) étend l’implémentation de base d’Azure pour inclure l’interconnexion entre des environnements locaux et des clouds privés AVS.
 
Cet article aborde quelques concepts clés qui établissent la mise en réseau et l’interconnexion, notamment les exigences et les limitations. Il fournit également des informations supplémentaires sur les deux types d’implémentations d’interconnexion de cloud privé AVS. Cet article vous fournit les informations dont vous avez besoin pour configurer votre réseau afin qu’il fonctionne correctement avec AVS.

## <a name="avs-private-cloud-use-cases"></a>Cas d’usage du cloud privé AVS

Les cas d’usage pour les clouds privés AVS sont les suivants :
- nouvelle charge de travail de machine virtuelle VMware dans le cloud ;
- transfert de la charge de travail de machine virtuelle en rafale vers le cloud (localement vers AVS uniquement) ;
- migration de la charge de travail de machine virtuelle vers le cloud (localement vers AVS uniquement) ;
- récupération d’urgence (d’AVS vers AVS ou d’emplacement locale vers AVS) ;
- Consommation des services Azure

> [!TIP]
> Tous les cas d’usage du service AVS sont activés avec une connexion d’environnement local à cloud privé.

## <a name="azure-virtual-network-interconnectivity"></a>Interconnexion de réseau virtuel Azure

Dans l’implémentation de réseau virtuel à cloud privé, vous pouvez gérer votre cloud privé Azure VMware Solution, utiliser des charges de travail dans votre cloud privé et accéder à des services Azure via la connexion ExpressRoute. 

Le diagramme ci-dessous illustre l’interconnexion de réseaux de base établie au moment du déploiement d’un cloud privé. Celui-ci montre la mise en réseau logique basée sur ExpressRoute entre un réseau virtuel dans Azure et un cloud privé. L’interconnexion correspond à trois des principaux cas d’utilisation :
* Accès entrant à vCenter Server et à NSX-T Manager qui est accessible à partir des machines virtuelles de votre abonnement Azure et non à partir de vos systèmes locaux. 
* Accès sortant de machines virtuelles à des services Azure. 
* Accès entrant et utilisation de charges de travail exécutant un cloud privé.

:::image type="content" source="media/concepts/adjacency-overview-drawing-single.png" alt-text="Connexion de base de réseau virtuel à cloud privé" border="false":::

## <a name="on-premises-interconnectivity"></a>Interconnexion locale

Dans l’implémentation de réseau virtuel et environnement local à cloud privé complet, vous pouvez accéder à vos clouds privés Azure VMware Solution à partir d’environnements locaux. Cette implémentation est une extension de l’implémentation de base décrite dans la section précédente. À l’instar de l’implémentation de base, un circuit ExpressRoute est requis, mais, dans ce cas-ci, il est utilisé pour se connecter à votre cloud privé dans Azure à partir d’environnements locaux. 

Le diagramme ci-dessous illustre l’interconnexion d’un environnement local à un cloud privé, ce qui permet les cas d’usage suivants :
* Croisement à chauf/à froid – vCenter-vMotion
* Accès pour la gestion de cloud privé d’un emplacement local à Azure VMware Solution

:::image type="content" source="media/concepts/adjacency-overview-drawing-double.png" alt-text="Connexion complète de réseau virtuel et d’environnement local à cloud privé" border="false":::

Pour une interconnexion complète à votre cloud privé, activez ExpressRoute Global Reach, puis demandez une clé d’autorisation et un ID de Peering privé pour Global Reach dans le portail Azure. La clé d’autorisation et l’ID de Peering sont utilisés pour établir un service Global Reach entre un circuit ExpressRoute faisant partie de votre abonnement et le circuit ExpressRoute destiné à votre nouveau cloud privé. Une fois liés, les deux circuits ExpressRoute acheminent le trafic réseau entre vos environnements locaux et votre cloud privé.  Pour connaître les procédures de demande et d’utilisation de la clé d’autorisation et de l’ID de Peering, consultez le [didacticiel relatif à la création d’un Peering ExpressRoute Global Reach vers un cloud privé](tutorial-expressroute-global-reach-private-cloud.md).

## <a name="next-steps"></a>Étapes suivantes 

- Apprenez-en davantage sur les [considérations et la configuration requise pour la connectivité réseau](tutorial-network-checklist.md). 
- Découvrez [les concepts de stockage de cloud privé](concepts-storage.md).


<!-- LINKS - external -->
[enable Global Reach]: ../expressroute/expressroute-howto-set-global-reach.md

<!-- LINKS - internal -->

