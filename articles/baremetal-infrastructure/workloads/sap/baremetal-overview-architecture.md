---
title: Vue d’ensemble de la préversion de BareMetal Infrastructure dans Azure
description: Vue d’ensemble de l’infrastructure BareMetal dans Azure.
ms.custom: references_regions
ms.topic: conceptual
ms.date: 1/4/2021
ms.openlocfilehash: eb4dc129719dc410f7101598e3d72e68f17809c1
ms.sourcegitcommit: aeba98c7b85ad435b631d40cbe1f9419727d5884
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2021
ms.locfileid: "97860986"
---
#  <a name="what-is-baremetal-infrastructure-preview-on-azure"></a>Présentation de la préversion de BareMetal Infrastructure dans Azure

L’infrastructure Azure BareMetal offre une solution sécurisée pour la migration des charges de travail personnalisées de l’entreprise. Les instances BareMetal sont des matériels hôtes/serveurs non partagés qui vous sont attribués. Cela déverrouille le déplacement de votre solution locale avec charges de travail spécialisées nécessitant du matériel certifié, des licences et des contrats de support. Azure gère la surveillance et la maintenance de l’infrastructure pour vous, tandis que la surveillance des applications et la surveillance du système d’exploitation invité sont de votre ressort.

L’infrastructure BareMetal permet de moderniser votre infrastructure tout en préservant vos investissements et l’architecture existante. Avec l’infrastructure BareMetal, vous pouvez intégrer des charges de travail spécialisées à Azure, ce qui vous permet d’accéder à des services Azure et de les intégrer avec une faible latence.

## <a name="sku-availability-in-azure-regions"></a>Disponibilité des références dans les régions Azure
L’infrastructure BareMetal pour les charges de travail spécialisées et à usage général est disponible, au départ dans quatre régions basées sur les tampons de révision 4.2 (Rev 4.2) :
- Europe Ouest
- Europe Nord
- USA Est 2
- États-Unis - partie centrale méridionale

>[!NOTE]
>**Rev 4.2** est la dernière infrastructure BareMetal rebaptisée qui utilise l’architecture Rev 4 existante.  Rev 4 assure une plus grande proximité des hôtes de machine virtuelle Azure. Elle présente des améliorations significatives de latence réseau entre les machines virtuelles Azure et les unités d’instance BareMetal déployées dans les tampons ou lignes Rev 4.  Vous pouvez accéder à vos instances BareMetal et les gérer via le portail Azure. 

## <a name="support"></a>Support
BareMetal Infrastructure est conforme aux normes ISO 27001, ISO 27017, SOC 1 et SOC 2.  Elle utilise également un modèle BYOL : système d’exploitation, charge de travail spécialisée et applications tierces.  

Dès que vous recevez un accès racine et le contrôle total, vous assumez la responsabilité des éléments suivants :
- Conception et implémentation de solutions de sauvegarde et de récupération, haute disponibilité et récupération d’urgence
- Licences, sécurité et support pour le système d’exploitation et les logiciels tiers

Microsoft est responsable des éléments suivants :
- Fourniture du matériel pour les charges de travail spécialisées 
- Configuration du système d’exploitation

:::image type="content" source="media/baremetal-support-model.png" alt-text="Modèle de support de BareMetal Infrastructure" border="false":::

## <a name="compute"></a>Compute
L’infrastructure BareMetal offre plusieurs références SKU pour les charges de travail spécialisées. Les références SKU disponibles vont des petits systèmes à deux sockets aux systèmes à 24 Sockets. Utilisez les références SKU spécifiques à votre charge de travail.

Le tampon d’instance BareMetal combine les composants suivants :

- **Calcul** : serveurs basés sur différentes générations de processeurs Intel Xeon qui offrent la capacité de calcul nécessaire et sont certifiés pour la charge de travail spécialisée.

- **Réseau :** structure réseau haut débit unifiée qui relie les composants de calcul, de stockage et de réseau.

- **Stockage :** infrastructure de stockage accessible via une infrastructure de réseau unifiée.

Dans l’infrastructure multilocataire du tampon BareMetal, les clients sont déployés en tant que locataires isolés. Au moment du déploiement du locataire, vous nommez un abonnement Azure au sein de votre inscription Azure. Cet abonnement Azure est celui dans lequel les instances BareMetal sont facturées.

>[!NOTE]
>Un client déployé dans l’instance BareMetal est isolé dans un locataire. Un locataire est isolé des autres locataires dans la couche de mise en réseau, de stockage et de calcul. Les unités de stockage et de calcul assignées aux différents locataires ne peuvent pas se voir les unes les autres ni communiquer sur les instances BareMetal.

## <a name="os"></a>Système d’exploitation
Pendant l’approvisionnement de l’instance BareMetal, vous pouvez sélectionner le système d’exploitation que vous souhaitez installer sur les ordinateurs. 

>[!NOTE]
>N’oubliez pas que l’infrastructure BareMetal est un modèle BYOL.

Les versions disponibles du système d’exploitation Linux sont les suivantes :
- Red Hat Enterprise Linux (RHEL) 7.6
- SLES (SUSE Linux Enterprise Server)
   - SLES 12 SP2
   - SLES 12 SP3
   - SLES 12 SP4
   - SLES 12 SP5
   - SLES 15 SP1

## <a name="storage"></a>Stockage
Les instances BareMetal basées sur un type de référence SKU spécifique sont fournies avec un stockage NFS prédéfini pour le type de charge de travail spécifique. Lorsque vous approvisionnez BareMetal, vous pouvez approvisionner davantage de stockage en fonction de la croissance estimée en soumettant une demande de support. Tout le stockage est fourni avec un disque tout Flash dans la révision 4.2 avec prise en charge de NFSv3 et NFSv4. La révision 4.5 NVMe SSD plus récente sera disponible. Pour plus d’informations sur le dimensionnement du stockage, consultez la section [Type de charge de travail BareMetal](../../../virtual-machines/workloads/sap/get-started.md).

>[!NOTE]
>Le stockage utilisé pour BareMetal répond aux exigences [de la publication 140-2 des normes FIPS (Federal Information Processing Standard)](/microsoft-365/compliance/offering-fips-140-2), qui propose par défaut le chiffrement au repos. Les données sont stockées de manière sécurisée sur les disques.

## <a name="networking"></a>Mise en réseau
L’architecture des services réseau Azure est un composant clé de la réussite du déploiement des charges de travail spécialisées sur les instances BareMetal. Il est probable que certains systèmes informatiques ne trouvent pas encore dans Azure. Azure offre une technologie réseau qui lui permet de se présenter comme un centre de données virtuel pour vos déploiements de logiciels locaux. Les fonctionnalités réseau Azure requises pour les instances BareMetal sont les suivantes :

- Les réseaux virtuels Azure sont connectés au circuit ExpressRoute qui se connecte à vos ressources réseau locales.
- Un circuit ExpressRoute se connectant en local à Azure doit avoir une bande passante minimale de 1 Gbit/s.
- Active Directory et DNS étendus dans Azure ou entièrement exécutés dans Azure.

L’utilisation d’ExpressRoute vous permet d’étendre votre réseau local dans le cloud Microsoft via une connexion privée avec l’aide d’un fournisseur de connectivité. Vous pouvez activer **ExpressRoute Premium** pour étendre la connectivité au-delà des limites géopolitiques ou utiliser **ExpressRoute Local** pour un transfert de données économique avec l’emplacement proche de la région Azure de votre choix.

Les instances BareMetal sont approvisionnées dans votre plage d’adresses IP de serveur de réseau virtuel Azure.

:::image type="content" source="media/baremetal-infrastructure-portal/baremetal-infrastructure-diagram.png" alt-text="Schéma de l’infrastructure Azure BareMetal Infrastructure" lightbox="media/baremetal-infrastructure-portal/baremetal-infrastructure-diagram.png" border="false":::

L’architecture présentée est divisée en trois sections :
- **Gauche :** montre l’infrastructure locale du client qui exécute différentes applications en se connectant via le serveur partenaire ou le routeur de périphérie local comme Equinix. Pour plus d’informations, consultez [Fournisseurs de connectivité et emplacements : Azure ExpressRoute](../../../expressroute/expressroute-locations.md).
- **Centre :** montre [ExpressRoute](../../../expressroute/expressroute-introduction.md) approvisionné à l’aide de votre abonnement Azure et qui offre la connectivité au réseau de périmètre Azure.
- **Droite :** montre Azure IaaS et, dans ce cas, l’utilisation de machines virtuelles pour héberger vos applications, qui sont approvisionnées au sein de votre réseau virtuel Azure.
- **Bas :** montre l’utilisation de votre passerelle ExpressRoute activée avec [ExpressRoute FastPath](../../../expressroute/about-fastpath.md) pour la connectivité BareMetal à faible latence.   
   >[!TIP]
   >Pour prendre tout cela en charge, votre passerelle ExpressRoute doit être UltraPerformance.  Pour plus d’informations, consultez [À propos des passerelles de réseau virtuel ExpressRoute](../../../expressroute/expressroute-about-virtual-network-gateways.md).

## <a name="next-steps"></a>Étapes suivantes

L’étape suivante consiste à identifier et à interagir avec les unités d’instance BareMetal par le biais du portail Azure.

> [!div class="nextstepaction"]
> [Gérer des instances nues via le portail Azure](baremetal-infrastructure-portal.md)