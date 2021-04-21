---
title: Vue d'ensemble de BareMetal Infrastructure sur Azure
description: Fournit une vue d'ensemble de BareMetal Infrastructure sur Azure.
ms.custom: references_regions
ms.topic: conceptual
ms.subservice: workloads
ms.date: 04/08/2021
ms.openlocfilehash: 7a4998a096a5c5d9e793c34d5046dce59262a2ae
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107257566"
---
#  <a name="what-is-baremetal-infrastructure-on-azure"></a>Qu'est-ce que BareMetal Infrastructure sur Azure ?

Microsoft Azure offre une infrastructure cloud qui englobe un large éventail de services cloud intégrés permettant de répondre aux besoins de votre entreprise. Mais dans certains cas, vous pouvez être amené à exécuter des services sur des serveurs nus sans couche de virtualisation. Et vous pouvez avoir besoin d'un accès racine, ainsi que d'un contrôle sur le système d'exploitation. Afin de répondre à ce besoin, Azure propose BareMetal Infrastructure pour différentes applications stratégiques et à forte valeur ajoutée.

BareMetal Infrastructure se compose d'instances BareMetal dédiées (instances de calcul), d'un stockage hautes performances et adapté aux applications (NFS, ISCSI et Fibre Channel), ainsi que d'un ensemble de réseaux locaux virtuels (VLAN) spécifiques aux fonctions au sein d'un environnement isolé. Le stockage peut être partagé entre plusieurs instances BareMetal pour bénéficier de fonctionnalités telles que des clusters scale-out ou pour créer des paires haute disponibilité avec STONITH.
 
Cet environnement dispose également de réseaux VLAN spéciaux auxquels vous pouvez accéder si vous exécutez des machines virtuelles sur un ou plusieurs réseaux virtuels (VNet) Azure dans le cadre de votre abonnement Azure. L'ensemble de l'environnement est représenté sous forme de groupe de ressources dans votre abonnement Azure.

BareMetal Infrastructure se décompose en une trentaine de références SKU, avec des serveurs de 2 à 24 sockets et une mémoire allant de 1,5 à 24 To. Un large éventail de références SKU est également disponible avec la mémoire Octane. Azure propose la plus large gamme d'instances nues disponibles dans un cloud hyperscale.

## <a name="why-baremetal-infrastructure"></a>Qu'est-ce que BareMetal Infrastructure ?  

Certaines charges de travail centrales de l'entreprise sont constituées de technologies qui ne sont tout simplement pas conçues pour s'exécuter dans un environnement de cloud virtualisé classique. Elles requièrent une architecture spéciale, du matériel certifié ou des dimensions hors norme. Bien que ces technologies disposent des fonctionnalités de protection des données et de continuité de l'activité les plus sophistiquées, celles-ci ne sont pas conçues pour le cloud virtualisé. Elles sont plus sensibles aux latences et aux voisins bruyants, et elles exigent beaucoup plus de contrôle en termes de gestion des changements et d'activités de maintenance.

BareMetal Infrastructure est conçu, certifié et testé pour une sélection d'applications. Azure, qui a été le premier à proposer de telles solutions, garde une longueur d'avance sur la concurrence en proposant le plus large portefeuille et les systèmes les plus sophistiqués.

BareMetal Infrastructure offre les avantages suivants : 

- Instances dédiées
- Matériel certifié pour les charges de travail spécialisées
    - SAP (reportez-vous à la [Note SAP N°1928533](https://launchpad.support.sap.com/#/notes/1928533))
    - Oracle (reportez-vous au [Document Oracle N°948372.1](https://support.oracle.com/epmos/faces/DocumentDisplay?_afrLoop=52088246571495&id=948372.1&_adf.ctrl-state=kwnkj1hzm_52))
- Matériel nu (aucune virtualisation du calcul)
- Faible latence entre les machines virtuelles d'applications hébergées sur Azure et les instances BareMetal (0,35 ms)
- Tous les disques SSD Flash et NVMe
    - Jusqu'à 1 Po/locataire 
    - IOPS jusqu'à 1,2 million/locataire 
    - Bande passante réseau de 40/100 Go
    - Accessible via NFS, ISCSI et FC
- Alimentation redondante, blocs d'alimentation, cartes réseau, commutateurs TOR, ports, réseaux étendus, stockage et gestion
- Composants échangeables à chaud pour remplacements en cas de panne (sans reconfiguration)
- Fenêtres de maintenance coordonnées par le client
- Captures instantanées, archivage, mise en miroir et clonage prenant en charge les applications


## <a name="sku-availability-in-azure-regions"></a>Disponibilité des références dans les régions Azure

L’infrastructure BareMetal offre plusieurs références SKU certifiées pour les charges de travail spécialisées. Utilisez les références SKU spécifiques aux charges de travail pour répondre à vos besoins.

- Grandes instances (systèmes de deux à quatre sockets)  
- Très grandes instances (systèmes de quatre à vingt sockets) 

BareMetal Infrastructure pour les charges de travail spécialisées est disponible dans les régions Azure suivantes :
- Europe Ouest
- Europe Nord
- Prise en charge des zones Allemagne Centre-Ouest*
- Prise en charge des zones USA Est 2*
- Prise en charge des zones USA Est*
- Prise en charge des zones USA Ouest*
- Prise en charge des zones USA Ouest 2*
- États-Unis - partie centrale méridionale

>[!NOTE]
>Le terme **Prise en charge des zones** fait référence aux zones de disponibilité au sein d'une région où les instances BareMetal peuvent être déployées sur plusieurs zones pour une résilience et une disponibilité élevées. Cette fonctionnalité permet la prise en charge de la mise à l'échelle active/active multisite.

## <a name="managing-baremetal-instances-in-azure"></a>Gestion des instances BareMetal dans Azure 

En fonction de vos besoins, les topologies d'application de BareMetal Infrastructure peuvent être complexes. Vous pouvez déployer plusieurs instances, à un ou plusieurs emplacements, avec un stockage partagé ou dédié, et des connexions LAN et WAN spécialisées. Ainsi, pour BareMetal Infrastructure, Azure propose une capture consultative de ces informations par un CSA/GBB sur un portail d'approvisionnement. 

Au moment de l'approvisionnement de BareMetal Infrastructure, le système d'exploitation, les réseaux, les volumes de stockage, les placements dans les zones et régions, et les connexions WAN entre les sites sont déjà préconfigurés. Vous êtes prêt à enregistrer vos licences de système d'exploitation (BYOL), à configurer le système d'exploitation et à installer la couche Application.

Toutes les ressources BareMetal Infrastructure, ainsi que leur état et leurs attributs, sont visibles sur le portail Azure. Vous pouvez également utiliser les instances, et ouvrir des demandes de service et des tickets de support à partir de là. 

## <a name="operational-model"></a>Modèle opérationnel
BareMetal Infrastructure est conforme aux normes ISO 27001, ISO 27017, SOC 1 et SOC 2. Elle utilise également un modèle BYOL : système d’exploitation, charge de travail spécialisée et applications tierces.  

Dès que vous recevez un accès racine et le contrôle total, vous assumez la responsabilité des éléments suivants :
- Conception et implémentation de solutions de sauvegarde et de récupération, haute disponibilité et récupération d'urgence
- Licences, sécurité et support pour le système d'exploitation et les logiciels tiers

Microsoft est responsable des éléments suivants :
- Fourniture du matériel pour les charges de travail spécialisées 
- Configuration du système d'exploitation

:::image type="content" source="media/concepts-baremetal-infrastructure-overview/baremetal-support-model.png" alt-text="Diagramme du modèle de support de BareMetal Infrastructure" border="false":::

## <a name="baremetal-instance-stamp"></a>Tampon d'instance BareMetal

Le tampon d’instance BareMetal combine les composants suivants :

- **Calcul :** serveurs basés sur la génération de processeurs Intel Xeon qui offrent la capacité de calcul nécessaire et sont certifiés pour la charge de travail spécialisée.

- **Réseau :** structure réseau haut débit unifiée qui relie les composants de calcul, de stockage et de réseau.

- **Stockage :** infrastructure de stockage accessible via une infrastructure de réseau unifiée.

Dans l’infrastructure multilocataire du tampon BareMetal, les clients sont déployés en tant que locataires isolés. Au moment du déploiement du locataire, vous nommez un abonnement Azure au sein de votre inscription Azure. Cet abonnement Azure est celui dans lequel vos instances BareMetal sont facturées.

>[!NOTE]
>Un client déployant une instance BareMetal est isolé dans un locataire. Un locataire est isolé des autres locataires dans la couche de mise en réseau, de stockage et de calcul. Les unités de stockage et de calcul attribuées à différents locataires ne peuvent pas se voir ni communiquer entre elles sur leurs instances BareMetal.

## <a name="operating-system"></a>Système d’exploitation
Pendant l’approvisionnement de l’instance BareMetal, vous pouvez sélectionner le système d’exploitation que vous souhaitez installer sur les ordinateurs. 

>[!NOTE]
>N’oubliez pas que l’infrastructure BareMetal est un modèle BYOL.

Les versions disponibles du système d’exploitation Linux sont les suivantes :
- Red Hat Enterprise Linux (RHEL)
- SLES (SUSE Linux Enterprise Server)

## <a name="storage"></a>Stockage
BareMetal Infrastructure fournit un stockage NFS et un stockage Fibre Channel hautement redondants. L'infrastructure offre une intégration en profondeur pour les charges de travail d'entreprise telles que SAP, SQL, etc. Elle offre également des fonctionnalités de protection et de gestion des données cohérentes avec les applications. Les outils de gestion en libre-service fournissent des fonctionnalités de capture instantanée, de clonage et de réplication granulaire à faible encombrement, ainsi qu'une surveillance au sein d'un volet unique. L'infrastructure offre des fonctionnalités zéro RPO et RTO pour répondre aux besoins de disponibilité des données et de continuité de l'activité. 

L'infrastructure de stockage offre les avantages suivants :
- Jusqu'à 4 liaisons montantes de 100 Go
- Jusqu'à 32 Go de liaisons montantes Fibre Channel
- Tous les disques SSD Flash et NVMe
- Latence ultra faible et débit élevé
- Jusqu'à 4 Po de stockage brut 
- Jusqu'à 11 millions d'IOPS

Les protocoles d'accès aux données suivants sont pris en charge : 
- iSCSI 
- NFS (v3 ou v4) 
- Fibre Channel 
- NVMe over FC  

## <a name="networking"></a>Mise en réseau
L’architecture des services réseau Azure est un composant clé de la réussite du déploiement des charges de travail spécialisées sur les instances BareMetal. Il est probable que certains systèmes informatiques ne trouvent pas encore dans Azure. Azure offre une technologie réseau qui lui permet de se présenter comme un centre de données virtuel pour vos déploiements de logiciels locaux. Les fonctionnalités réseau Azure requises pour les instances BareMetal sont les suivantes :

- Réseaux virtuels Azure connectés au circuit Azure ExpressRoute qui se connecte à vos ressources réseau locales.
- Le circuit ExpressRoute qui se connecte en local à Azure doit disposer d'une bande passante minimale de 1 Gbit/s.
- Active Directory et DNS étendus dans Azure, ou entièrement exécutés dans Azure.

ExpressRoute vous permet d'étendre votre réseau local dans le cloud Microsoft via une connexion privée avec l'aide d'un fournisseur de connectivité. Vous pouvez utiliser **ExpressRoute Local** pour un transfert de données économique entre votre site local et la région Azure de votre choix. Pour étendre la connectivité au-delà des limites géopolitiques, vous pouvez activer **ExpressRoute Premium**. 

Les instances BareMetal sont approvisionnées dans votre plage d'adresses IP de serveur de réseau virtuel Azure.

:::image type="content" source="media/concepts-baremetal-infrastructure-overview/baremetal-infrastructure-diagram.png" alt-text="Schéma architectural d'Azure BareMetal Infrastructure." lightbox="media/concepts-baremetal-infrastructure-overview/baremetal-infrastructure-diagram.png" border="false":::

L’architecture présentée est divisée en trois sections :
- **Gauche :** montre l'infrastructure locale du client qui exécute différentes applications, en se connectant via le serveur partenaire ou un routeur de périphérie local comme Equinix. Pour plus d’informations, consultez [Fournisseurs de connectivité et emplacements : Azure ExpressRoute](../expressroute/expressroute-locations.md).
- **Au centre :** Montre [ExpressRoute](../expressroute/expressroute-introduction.md) approvisionné à l’aide de votre abonnement Azure et qui offre la connectivité au réseau Azure Edge.
- **Droite :** montre Azure IaaS et, dans ce cas, l'utilisation de machines virtuelles pour héberger vos applications, qui sont approvisionnées au sein de votre réseau virtuel Azure.
- **En bas :** Montre l’utilisation de votre passerelle ExpressRoute activée avec [ExpressRoute FastPath](../expressroute/about-fastpath.md) pour la connectivité BareMetal à faible latence.   
   >[!TIP]
   >Pour prendre tout cela en charge, votre passerelle ExpressRoute doit être UltraPerformance. Pour plus d’informations, consultez [À propos des passerelles de réseau virtuel ExpressRoute](../expressroute/expressroute-about-virtual-network-gateways.md).

## <a name="next-steps"></a>Étapes suivantes

L'étape suivante consiste à identifier les instances BareMetal et à interagir avec elles par le biais du portail Azure.

> [!div class="nextstepaction"]
> [Gérer les instances nues via le portail Azure](connect-baremetal-infrastructure.md)
