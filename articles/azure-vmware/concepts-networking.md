---
title: Concepts – Interconnexion de réseaux
description: Découvrez des aspects clés et des cas d’utilisation de mise en réseau et d’interconnexion dans la solution Azure VMware (AVS)
ms.topic: conceptual
ms.date: 07/23/2020
ms.openlocfilehash: c0416da9c745ccf92970ff39f623a782d5784983
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87062839"
---
# <a name="azure-vmware-solution-avs-preview-networking-and-interconnectivity-concepts"></a>Concepts de mise en réseau et d’interconnexion de la solution Azure VMware (AVS) en préversion

L’interconnexion réseau entre vos clouds privés Azure VMware Solution (AVS) et des environnements locaux ou des réseaux virtuels dans Azure vous permet d’accéder à votre cloud privé et de l’utiliser. Dans cet article, nous allons aborder quelques concepts clés de la mise en réseau et de l’interconnexion.

Une approche utile de l’interconnexion consiste à prendre en compte les deux types d’implémentations de cloud privé AVS :

1. [**L’interconnexion Azure de base**](#azure-virtual-network-interconnectivity) vous permet de gérer et d’utiliser votre cloud privé avec un seul réseau virtuel dans Azure. Cette implémentation est idéale pour les évaluations ou implémentations d’AVS qui ne nécessitent pas d’accès à partir d’environnements locaux.

1. [**L’interconnexion complète entre un environnement local et un cloud privé**](#on-premises-interconnectivity) étend l’implémentation de base d’Azure pour inclure l’interconnexion entre des environnements locaux et des clouds privés AVS.
 
Vous trouverez plus d’informations sur la configuration requise et les deux types d’implémentations de l’interconnexion des clouds privés AVS dans les sections ci-dessous.

## <a name="avs-private-cloud-use-cases"></a>Cas d’usage du cloud privé AVS

Les cas d’usage pour les clouds privés AVS sont les suivants :
- nouvelle charge de travail de machine virtuelle VMware dans le cloud ;
- transfert de la charge de travail de machine virtuelle en rafale vers le cloud (localement vers AVS uniquement) ;
- migration de la charge de travail de machine virtuelle vers le cloud (localement vers AVS uniquement) ;
- récupération d’urgence (AVS vers AVS ou localement vers AVS) ;
- utilisation de services Azure

 Tous les cas d’usage du service AVS sont activés avec une connexion d’environnement local à cloud privé. 

## <a name="virtual-network-and-expressroute-circuit-requirements"></a>Configuration requise pour le réseau virtuel et le circuit ExpressRoute
 
Lorsque vous créez une connexion à partir d’un réseau virtuel de votre abonnement, le circuit ExpressRoute s’établit par Peering et utilise une clé d’autorisation et un ID de Peering que vous demandez dans le portail Azure. Le Peering est une connexion privée de type un-à-un entre votre cloud privé et le réseau virtuel.

> [!NOTE] 
> Le circuit ExpressRoute ne fait pas partie d’un déploiement de cloud privé. Le circuit ExpressRoute local dépasse le cadre de ce document. Si vous avez besoin d’une connexion locale à votre cloud privé, vous pouvez utiliser l’un de vos circuits ExpressRoute existants ou en acheter un dans le portail Azure.

Lors du déploiement d’un cloud privé, vous recevez des adresses IP pour vCenter et NSX-T Manager. Pour accéder à ces interfaces de gestion, vous devez créer des ressources supplémentaires dans un réseau virtuel inclus dans votre abonnement. Les procédures de création de ces ressources et d’établissement de Peering privé ExpressRoute sont accessibles dans les didacticiels.

La mise en réseau logique du cloud privé inclut un environnement NSX-T pré-approvisionné. Une passerelle de niveau 0 et une passerelle de niveau 1 sont pré-approvisionnées pour vous. Vous pouvez créer un segment et l’attacher à la passerelle de niveau 1 existante ou à une nouvelle passerelle de niveau 1 que vous définissez. Les composants de mise en réseau logique de NSX-T fournissent une connexion Est-Ouest entre les charges de travail, ainsi qu’une connexion Nord-Sud aux services Internet et Azure. 

## <a name="routing-and-subnet-requirements"></a>Conditions requises pour le routage et le sous-réseau

Le routage est basé sur le protocole BGP (Border Gateway Protocol), qui est automatiquement approvisionné et activé par défaut pour chaque déploiement de cloud privé. Pour les clouds privés AVS, vous devez planifier des espaces d’adressage réseau de cloud privé avec un minimum de/22 blocs d’adresses réseau CIDR de longueur de préfixe pour les sous-réseaux, comme indiqué dans le tableau ci-dessous. Le bloc d’adresses ne doit pas chevaucher ceux utilisés dans d’autres réseaux virtuels se trouvant dans votre abonnement et des réseaux locaux. Dans ce bloc d’adresses, la gestion, l’approvisionnement et les réseaux vMotion sont approvisionnés automatiquement.

Exemple de bloc d’adresses réseau CIDR `/22` : `10.10.0.0/22`

Les sous-réseaux :

| Utilisation du réseau             | Subnet | Exemple        |
| ------------------------- | ------ | -------------- |
| Gestion de cloud privé  | `/24`  | `10.10.0.0/24` |
| Réseau vMotion           | `/24`  | `10.10.1.0/24` |
| Charges de travail de machine virtuelle              | `/24`  | `10.10.2.0/24` |
| Appairage ExpressRoute      | `/24`  | `10.10.3.8/30` |


## <a name="azure-virtual-network-interconnectivity"></a>Interconnexion de réseau virtuel Azure

Dans l’implémentation de réseau virtuel à cloud privé, vous pouvez gérer votre cloud privé AVS, utiliser des charges de travail dans votre cloud privé et accéder à des services Azure via la connexion ExpressRoute. 

Le diagramme ci-dessous illustre l’interconnexion de réseaux de base établie au moment du déploiement d’un cloud privé. Celui-ci montre la mise en réseau logique basée sur ExpressRoute entre un réseau virtuel dans Azure et un cloud privé. L’interconnexion correspond à trois des principaux cas d’utilisation :
* Accès entrant à vCenter Server et à NSX-T Manager qui est accessible à partir des machines virtuelles de votre abonnement Azure et non à partir de vos systèmes locaux. 
* Accès sortant de machines virtuelles à des services Azure. 
* Accès entrant et utilisation de charges de travail exécutant un cloud privé.

:::image type="content" source="media/concepts/adjacency-overview-drawing-single.png" alt-text="Connexion de base de réseau virtuel à cloud privé" border="false":::

## <a name="on-premises-interconnectivity"></a>Interconnexion locale

Dans l’implémentation de réseau virtuel et environnement local à cloud privé complet, vous pouvez accéder à vos clouds privés AVS à partir d’environnements locaux. Cette implémentation est une extension de l’implémentation de base décrite dans la section précédente. À l’instar de l’implémentation de base, un circuit ExpressRoute est requis, mais, dans ce cas-ci, il est utilisé pour se connecter à votre cloud privé dans Azure à partir d’environnements locaux. 

Le diagramme ci-dessous illustre l’interconnexion d’un environnement local à un cloud privé, ce qui permet les cas d’usage suivants :
* Croisement à chauf/à froid – vCenter-vMotion
* Accès pour gestion d’environnement local à cloud privé

:::image type="content" source="media/concepts/adjacency-overview-drawing-double.png" alt-text="Connexion complète de réseau virtuel et d’environnement local à cloud privé" border="false":::

Pour une interconnexion complète à votre cloud privé, activez ExpressRoute Global Reach, puis demandez une clé d’autorisation et un ID de Peering privé pour Global Reach dans le portail Azure. La clé d’autorisation et l’ID de Peering sont utilisés pour établir un service Global Reach entre un circuit ExpressRoute faisant partie de votre abonnement et le circuit ExpressRoute destiné à votre nouveau cloud privé. Une fois liés, les deux circuits ExpressRoute acheminent le trafic réseau entre vos environnements locaux et votre cloud privé.  Pour connaître les procédures de demande et d’utilisation de la clé d’autorisation et de l’ID de Peering, consultez le [didacticiel relatif à la création d’un Peering ExpressRoute Global Reach vers un cloud privé](tutorial-expressroute-global-reach-private-cloud.md).


## <a name="next-steps"></a>Étapes suivantes 

L’étape suivante consiste à en apprendre davantage sur les [concepts liés au stockage de cloud privé](concepts-storage.md).

<!-- LINKS - external -->
[enable Global Reach]: ../expressroute/expressroute-howto-set-global-reach.md

<!-- LINKS - internal -->
