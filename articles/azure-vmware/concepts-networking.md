---
title: Concepts – Interconnexion de réseaux
description: Découvrez des aspects clés et des cas d’utilisation de mise en réseau et d’interconnexion dans la solution Azure VMware (AVS)
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: 82e5497c30bf02313e5ff8ad24167af569a153c2
ms.sourcegitcommit: d9cd51c3a7ac46f256db575c1dfe1303b6460d04
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82739953"
---
# <a name="azure-vmware-solution-avs-preview-networking-and-interconnectivity-concepts"></a>Concepts de mise en réseau et d’interconnexion de la solution Azure VMware (AVS) en préversion

L’interconnexion réseau entre vos clouds privés de solution Azure VMware (AVS) et des environnements locaux ou des réseaux virtuels dans Azure vous permet d’accéder à votre cloud privé et de l’utiliser. Cet article décrit quelques concepts clés de la mise en réseau et de l’interconnexion, qui sont à la base de l’interconnexion.

Une approche utile de l’interconnexion consiste à prendre en compte les deux types d’implémentations de cloud privé AVS : les implémentations avec une interconnexion de base exclusivement Azure et les implémentations avec une interconnexion complète d’environnement local à cloud privé.

Les cas d’usage pour les clouds privés AVS sont les suivants :
- nouvelle charge de travail de machine virtuelle VMware dans le cloud ;
- charge de travail de machine virtuelle accédant en rafale au cloud ;
- migration de charge de travail de machine virtuelle vers le cloud.
- récupération d’urgence
- utilisation de services Azure

 Tous les cas d’usage du service AVS sont activés avec une connexion d’environnement local à cloud privé. Le modèle d’interconnexion de base est idéal pour les évaluations ou implémentations d’AVS qui ne nécessitent pas d’accès à partir d’environnements locaux.

Les deux types d’interconnexions de cloud privé AVS sont décrits dans les sections ci-dessous.  L’interconnexion la plus simple est la « connexion de réseau virtuel Azure ». Elle vous permet de gérer et d’utiliser votre cloud privé avec un seul réseau virtuel dans Azure. L’interconnexion décrite dans « Connexion locale » étend la connexion de réseau virtuel pour inclure également l’interconnexion entre environnements locaux et clouds privés AVS.

## <a name="azure-virtual-network-interconnectivity"></a>Interconnexion de réseau virtuel Azure

L’interconnexion de réseaux de base établie au moment du déploiement d’un cloud privé est illustrée dans le diagramme ci-dessous. Celui-ci montre la mise en réseau logique basée sur ExpressRoute entre un réseau virtuel dans Azure et un cloud privé. L’interconnexion correspond à trois des principaux cas d’utilisation :
- Accès entrant aux réseaux de gestion où se trouvent le serveur vCenter et le gestionnaire NSX-T.
    - Accessibilité à partir de machines virtuelles faisant partie de votre abonnement Azure, et non à partir de vos systèmes locaux.
- Accès sortant de machines virtuelles à des services Azure.
- Accès entrant et utilisation de charges de travail exécutant un cloud privé.

![Connexion de base de réseau virtuel à cloud privé](./media/concepts/adjacency-overview-drawing-single.png)

Le circuit ExpressRoute dans ce scénario de connexion de réseau virtuel à cloud privé est établi lorsque vous créez une connexion à partir d’un réseau virtuel faisant partie de votre abonnement au circuit ExpressRoute de votre cloud privé. L’appairage utilise une clé d’autorisation et un ID de circuit que vous demandez via le portail Azure. La connexion ExpressRoute établie par le biais de l’appairage est une connexion privée de type un-à-un entre votre cloud privé et le réseau virtuel. Vous pouvez gérer votre cloud privé, utiliser des charges de travail dans votre cloud privé et accéder à des services Azure via cette connexion ExpressRoute.

Lorsque vous déployez un cloud privé AVS, un espace d’adressage de réseau privé /22 unique est requis. Cet espace d’adressage ne doit pas chevaucher des espaces d’adressage utilisés dans d’autres réseaux virtuels de votre abonnement. Dans cet espace d’adressage, la gestion, l’approvisionnement et les réseaux vMotion sont approvisionnés automatiquement. Le routage est basé sur le protocole BGP et automatiquement approvisionné et activé par défaut pour chaque déploiement de cloud privé.

Lors du déploiement d’un cloud privé, vous recevez les adresses IP du serveur vCenter et du gestionnaire NSX-T. Pour accéder à ces interfaces de gestion, vous allez créer des ressources supplémentaires dans un réseau virtuel inclus dans votre abonnement. Les procédures de création de ces ressources et d’établissement d’appairage privé ExpressRoute sont décrites dans les didacticiels.

Vous concevez la mise en réseau logique de cloud privé et l’implémentez avec NSX-T. Le cloud privé inclut un NSX-T pré-approvisionné. Une passerelle de niveau 0 et une passerelle de niveau 1 sont pré-approvisionnées. Vous pouvez créer un segment et l’attacher à la passerelle de niveau 1 existante ou à une nouvelle passerelle de niveau 1 que vous pouvez définir. Les composants de mise en réseau logique de NSX-T fournissent une connexion Est-Ouest entre les charges de travail, ainsi qu’une connexion Nord-Sud aux services Internet et Azure. 

## <a name="on-premises-interconnectivity"></a>Interconnexion locale

Vous pouvez également connecter des environnements locaux à vos clouds privés AVS. Ce type d’interconnexion est une extension de l’interconnexion de base décrite dans la section précédente.

![connexion complète de réseau virtuel et de cloud privé local](./media/concepts/adjacency-overview-drawing-double.png)

Pour établir une interconnexion complète avec un cloud privé, vous utilisez le portail Azure pour activer ExpressRoute Global Reach entre un circuit ExpressRoute de cloud privé et un circuit ExpressRoute local. Cette configuration étend la connexion de base pour inclure l’accès à des clouds privés à partir d’environnements locaux.

Un circuit ExpressRoute d’environnement local à réseau un virtuel Azure est nécessaire pour se connecter à partir d’environnements locaux à votre cloud privé dans Azure. Ce circuit ExpressRoute est inclus dans votre abonnement et ne fait pas partie d’un déploiement de cloud privé. Le circuit ExpressRoute local dépasse le cadre de ce document. Si vous avez besoin d’une connexion locale à votre cloud privé, vous pouvez utiliser l’un de vos circuits ExpressRoute existants ou en acheter un dans le portail Azure.

Une fois liés à Global Reach, les deux circuits ExpressRoute acheminent le trafic réseau entre vos environnements locaux et votre cloud privé. L’interconnexion d’environnement local à cloud privé est illustrée dans le diagramme précédent. L’interconnexion représentée dans le diagramme permet les cas d’usage suivants :

- Croisement à chauf/à froid – vCenter-vMotion
- Accès pour gestion d’environnement local à cloud privé

Pour permettre une connexion complète, vous pouvez demander une clé d’autorisation et un ID d’appairage privé pour Global Reach dans le portail Azure. Vous utilisez la clé et l’ID pour établir un service Global Reach entre un circuit ExpressRoute faisant partie de votre abonnement et le circuit ExpressRoute destiné à votre nouveau cloud privé. Le [didacticiel pour la création d’un cloud privé](tutorial-create-private-cloud.md) décrit les procédures à suivre pour demander et utiliser la clé et l’ID.

Les exigences de routage de la solution nécessitent que vous planifiiez les espaces d’adressage du réseau de cloud privé de façon à éviter les chevauchements avec d’autres réseaux virtuels et des réseaux locaux. Les clouds privés AVS nécessitent au minimum un bloc d’adresses réseau CIDR `/22` pour les sous-réseaux, comme indiqué ci-dessous. Ce réseau complète vos réseaux locaux. Pour pouvoir se connecter à des environnements locaux et à des réseaux virtuels, ce bloc d’adresses réseau ne doit pas présenter de chevauchement.

Exemple de bloc d’adresses réseau CIDR `/22` : `10.10.0.0/22`

Les sous-réseaux :

| Utilisation du réseau             | Subnet | Exemple        |
| ------------------------- | ------ | -------------- |
| Gestion de cloud privé            | `/24`    | `10.10.0.0/24`   |
| Réseau vMotion       | `/24`    | `10.10.1.0/24`   |
| Charges de travail de machine virtuelle | `/24`   | `10.10.2.0/24`   |
| Appairage ExpressRoute | `/24`    | `10.10.3.8/30`   |

## <a name="next-steps"></a>Étapes suivantes 

L’étape suivante consiste à en apprendre davantage sur les [concepts liés au stockage de cloud privé](concepts-storage.md).

<!-- LINKS - external -->
[enable Global Reach]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-set-global-reach

<!-- LINKS - internal -->

