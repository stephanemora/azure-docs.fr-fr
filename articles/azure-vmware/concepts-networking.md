---
title: Concepts – Interconnexion de réseaux
description: Découvrez des aspects clés et des cas d’utilisation de mise en réseau et d’interconnexion dans Azure VMware Solution.
ms.topic: conceptual
ms.date: 03/11/2021
ms.openlocfilehash: 4c964151c49e2fea56031dd24bacf4655753a18d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103491807"
---
# <a name="azure-vmware-solution-networking-and-interconnectivity-concepts"></a>Concepts de réseau et d’interconnexion d’Azure VMware Solution

[!INCLUDE [avs-networking-description](includes/azure-vmware-solution-networking-description.md)]

Il existe deux façons d’assurer l’interconnexion dans le cloud privé Azure VMware Solution :

- [**L’interconnexion Azure de base**](#azure-virtual-network-interconnectivity) vous permet de gérer et d’utiliser votre cloud privé avec un seul réseau virtuel dans Azure. Cette implémentation est idéale pour les évaluations ou implémentations d’Azure VMware Solution qui ne nécessitent pas d’accès à partir d’environnements locaux.

- [**L’interconnexion complète entre un environnement local et un cloud privé**](#on-premises-interconnectivity) étend l’implémentation de base d’Azure pour inclure l’interconnexion entre des environnements locaux et des clouds privés Azure VMware Solution.
 
Cet article aborde les concepts clés qui établissent la mise en réseau et l’interconnexion, notamment les exigences et les limites. Cet article vous fournit les informations dont vous avez besoin pour configurer votre réseau afin qu’il fonctionne avec Azure VMware Solution.

## <a name="azure-vmware-solution-private-cloud-use-cases"></a>Cas d’usage des clouds privés Azure VMware Solution

Les cas d’usage des clouds privés Azure VMware Solution incluent :
- nouvelle charge de travail de machine virtuelle VMware dans le cloud ;
- Transfert de la charge de travail de machine virtuelle en rafale vers le cloud (localement vers Azure VMware Solution uniquement)
- Migration de la charge de travail de machine virtuelle vers le cloud (localement vers Azure VMware Solution uniquement)
- Reprise d’activité après sinistre (d’Azure VMware Solution vers Azure VMware Solution ou localement vers Azure VMware Solution)
- Consommation des services Azure

> [!TIP]
> Tous les cas d’usage du service Azure VMware Solution sont possibles avec la connexion de l’environnement local au cloud privé.

## <a name="azure-virtual-network-interconnectivity"></a>Interconnexion de réseau virtuel Azure

Vous pouvez interconnecter votre réseau virtuel Azure avec l’implémentation du cloud privé Azure VMware Solution. Vous pouvez gérer votre cloud privé Azure VMware Solution, utiliser des charges de travail dans votre cloud privé et accéder à d’autres services Azure.

Le diagramme ci-dessous illustre l’interconnexion de réseaux de base établie au moment du déploiement d’un cloud privé. Il montre la mise en réseau logique entre un réseau virtuel dans Azure et un cloud privé. Cette connectivité est établie via un ExpressRoute principal qui fait partie du service Azure VMware Solution. L’interconnexion répond aux principaux cas d’usage suivants :

- Accès entrant à vCenter Server et à NSX-T Manager qui est possible à partir des machines virtuelles de votre abonnement Azure.
- Accès sortant des machines virtuelles du cloud privé aux services Azure.
- Accès entrant des charges de travail s’exécutant dans le cloud privé.


:::image type="content" source="media/concepts/adjacency-overview-drawing-single.png" alt-text="Connexion de base de réseau virtuel à cloud privé" border="false":::

## <a name="on-premises-interconnectivity"></a>Interconnexion locale

Dans le scénario entièrement interconnecté, vous pouvez accéder à Azure VMware Solution à partir de vos réseaux virtuels Azure et de vos environnements locaux. Cette implémentation est une extension de l’implémentation de base décrite dans la section précédente. Un circuit ExpressRoute est nécessaire pour se connecter à partir d’environnements locaux à votre cloud privé Azure VMware Solution dans Azure.

Le diagramme ci-dessous illustre l’interconnexion d’un environnement local à un cloud privé, ce qui permet les cas d’usage suivants :

- VCenter vMotion à chaud/à froid entre un environnement local et Azure VMware Solution.
- Accès pour la gestion d’environnements locaux à un cloud privé Azure VMware Solution.

:::image type="content" source="media/concepts/adjacency-overview-drawing-double.png" alt-text="Connexion complète de réseau virtuel et d’environnement local à cloud privé" border="false":::

Pour une interconnexion complète avec votre cloud privé, vous devez activer ExpressRoute Global Reach, puis demander une clé d’autorisation et un ID de Peering privé pour Global Reach dans le portail Azure. La clé d’autorisation et l’ID de Peering sont utilisés pour établir un service Global Reach entre un circuit ExpressRoute faisant partie de votre abonnement et le circuit ExpressRoute destiné à votre cloud privé. Une fois liés, les deux circuits ExpressRoute acheminent le trafic réseau entre vos environnements locaux et votre cloud privé. Pour plus d’informations sur les procédures, consultez le [tutoriel relatif à la création d’un Peering ExpressRoute Global Reach vers un cloud privé](tutorial-expressroute-global-reach-private-cloud.md).

## <a name="limitations"></a>Limites
[!INCLUDE [azure-vmware-solutions-limits](includes/azure-vmware-solutions-limits.md)]

## <a name="next-steps"></a>Étapes suivantes 

Maintenant que vous avez couvert les concepts de réseau et d’interconnexion d’Azure VMware Solution, vous voudrez peut-être en savoir plus :

- [Concepts de stockage pour Azure VMware Solution](concepts-storage.md).
- [Concepts d’identité d’Azure VMware Solution](concepts-identity.md).
- [Comment activer la ressource Azure VMware Solution](enable-azure-vmware-solution.md).

<!-- LINKS - external -->
[enable Global Reach]: ../expressroute/expressroute-howto-set-global-reach.md

<!-- LINKS - internal -->
[concepts-upgrades]: ./concepts-upgrades.md
[concepts-storage]: ./concepts-storage.md
