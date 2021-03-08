---
title: Questions fréquentes (FAQ) sur Azure Private Link
description: Découvrez Azure Private Link.
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: conceptual
ms.date: 10/05/2019
ms.author: allensu
ms.openlocfilehash: 4e81d8f88a7c01b6d302bcdaa88559159bed04ea
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101709405"
---
# <a name="azure-private-link-frequently-asked-questions-faq"></a>Questions fréquentes (FAQ) sur Azure Private Link

## <a name="private-link"></a>Private Link

### <a name="what-is-azure-private-endpoint-and-azure-private-link-service"></a>Que sont Azure Private Endpoint et Azure Private Link ?

- **[Azure Private Endpoint](private-endpoint-overview.md)**  : Azure Private Endpoint est une interface réseau qui vous permet de vous connecter de façon privée et sécurisée à un service basé sur Azure Private Link. Vous pouvez utiliser des points de terminaison privés pour vous connecter à un service Azure PaaS prenant en charge Private Link ou à votre propre service Private Link.
- **[Service Azure Private Link](private-link-service-overview.md)**  : Azure Private Link est un service créé par un fournisseur de services. Le service Private Link peut être attaché à la configuration d’adresse IP front-end d’un équilibreur de charge Standard. 

### <a name="how-is-traffic-being-sent-when-using-private-link"></a>Comment le trafic est envoyé lors de l’utilisation de Private Link ?
Le trafic est envoyé dans le cadre d’une connexion privée à l’aide de l’infrastructure principale Microsoft. Il ne passe pas par Internet. Azure Private Link ne stocke pas les données client.
 
### <a name="what-is-the-difference-between-service-endpoints-and-private-endpoints"></a>Quelle est la différence entre des points de terminaison de service et des points de terminaison privés ?
- Les points de terminaison privés accordent un accès réseau à des ressources spécifiques derrière un service donné fournissant une segmentation granulaire. Le trafic peut atteindre la ressource du service à partir d’un emplacement local sans utiliser de points de terminaison publics.
- Un point de terminaison de service reste une adresse IP routable publiquement.  Un point de terminaison privé est une adresse IP privée dans l’espace d’adressage du réseau virtuel sur lequel le point de terminaison privé est configuré.

### <a name="what-is-the-relationship-between-private-link-service-and-private-endpoint"></a>Quel type de relation existe entre Private Endpoint et le service Private Link ?
Plusieurs types de ressources Private Link prennent en charge l’accès via un point de terminaison privé. Les ressources incluent les services Azure PaaS et votre propre service Private Link. Il s’agit d’une relation un-à-plusieurs. 

Un service Azure Private Link reçoit des connexions provenant de plusieurs points de terminaison privés. Un point de terminaison privé se connecte à un service Azure Private Link.    

### <a name="do-i-need-to-disable-network-policies-for-private-link"></a>Dois-je désactiver les stratégies réseau pour Private Link
Oui. Le point de terminaison privé et le service Private Link doivent désactiver les stratégies réseau pour fonctionner correctement. Tous deux ont des propriétés indépendantes les unes des autres.

## <a name="private-endpoint"></a>Point de terminaison privé 
 
### <a name="can-i-create-multiple-private-endpoints-in-same-vnet-can-they-connect-to-different-services"></a>Est-il possible de créer plusieurs points de terminaison privés dans un même réseau virtuel ? Peuvent-ils se connecter à des services différents ? 
Oui. Il est possible d’avoir plusieurs points de terminaison privés dans un même réseau virtuel, et ceux-ci peuvent se connecter à des services différents.  
 
### <a name="do-i-require-a-dedicated-subnet-for-private-endpoints"></a>Ai-je besoin d’un sous-réseau dédié pour les points de terminaison privés ? 
Non. Vous n’avez pas besoin d’un sous-réseau dédié pour les points de terminaison privés. Vous pouvez choisir l’adresse IP d’un point de terminaison privé sur n’importe quel sous-réseau du réseau virtuel où est déployé votre service.  
 
### <a name="can-a-private-endpoint-connect-to-private-link-services-across-azure-active-directory-tenants"></a>Un point de terminaison privé peut-il se connecter aux services Private Link sur des locataires Azure Active Directory différents ? 
Oui. Les points de terminaison privés peuvent se connecter à des services Private Link ou à un service Azure PaaS se trouvant sur des locataires Azure Active Directory différents. Les points de terminaison privés qui se connectent à plusieurs locataires nécessitent une approbation manuelle des demandes. 
 
### <a name="can-private-endpoint-connect-to-azure-paas-resources-across-azure-regions"></a>Private Endpoint peut-il se connecter à des ressources Azure PaaS situées dans une région Azure différente ?
Oui. Les points de terminaison privés peuvent se connecter à des ressources Azure PaaS situées dans une région Azure différente.

### <a name="can-i-modify-my-private-endpoint-network-interface-nic-"></a>Puis-je modifier mon interface réseau (NIC) de point de terminaison privé ?
Lors de la création d’un point de terminaison privé, une carte NIC en lecture seule est assignée. Non modifiable, elle sera conservée pendant tout le cycle de vie du point de terminaison privé.

## <a name="private-link-service"></a>Service Liaison privée
 
### <a name="what-are-the-pre-requisites-for-creating-a-private-link-service"></a>Quels sont les prérequis à la création d’un service Private Link ? 
Vos back-ends de service doivent se trouver sur un réseau virtuel et derrière un Standard Load Balancer.
 
### <a name="how-can-i-scale-my-private-link-service"></a>Comment mettre à l’échelle mon service Private Link ? 
Vous pouvez mettre à l’échelle votre service Private Link de différentes manières : 
- Ajoutez des machines virtuelles back-end au pool derrière votre équilibreur de charge Standard. 
- Ajoutez une adresse IP au service Private Link. Vous pouvez utiliser jusqu’à 8 adresses IP par service Private Link.  
- Ajoutez un nouveau service Private Link à Standard Load Balancer. Vous pouvez utiliser jusqu’à 8 services Private Link par équilibreur de charge.   

### <a name="what-is-natnetwork-address-translation-ip-configuration-used-in-private-link-service-how-can-i-scale-in-terms-of-available-ports-and-connections"></a>Que représente la configuration IP NAT (Network Address Translation) IP utilisée dans le service Private Link ? Comment effectuer un scale-in en termes de ports et de connexions disponibles ? 

La configuration IP NAT s’assure qu’il n’y a aucun conflit IP entre l’espace d’adressage source (côté client) et de destination (fournisseur de services) en fournissant la traduction d’adresses réseau (NAT) source sur le trafic Private Link côté destination (côté fournisseur de services). L’adresse IP NAT s’affichera en tant qu’adresse IP source pour tous les paquets reçus par votre service, et en tant qu’adresse IP de destination pour tous les paquets envoyés par votre service.  L’adresse IP NAT peut être choisie à partir de n’importe quel sous-réseau du réseau virtuel d’un fournisseur de services. 

Chaque adresse IP NAT fournit 64 000 connexions TCP (64 000 ports) par machine virtuelle derrière l’équilibreur de charge standard. Pour mettre à l’échelle et ajouter d’autres connexions, vous pouvez ajouter de nouvelles adresses IP NAT, ou ajouter d’autres machines virtuelles derrière l’équilibreur de charge standard. Cela permet de mettre à l’échelle la disponibilité du port et d’autoriser plus de connexions. Les connexions seront réparties entre les adresses IP NAT et les machines virtuelles situées derrière l’équilibreur de charge standard.

### <a name="can-i-connect-my-service-to-multiple-private-endpoints"></a>Est-il possible de connecter son service à plusieurs points de terminaison privés ?
Oui. Un service Private Link peut recevoir des connexions provenant de plusieurs points de terminaison privés. En revanche, un point de terminaison privé ne peut se connecter qu’à un seul service Private Link.  
 
### <a name="how-should-i-control-the-exposure-of-my-private-link-service"></a>Comment contrôler l’exposition de mon service Private Link ?
Vous pouvez contrôler son exposition en configurant la visibilité dans le service Private Link. La visibilité comprend trois paramètres :

- **Aucune** - Seuls les abonnements avec accès Azure RBAC peuvent localiser le service. 
- **Restreinte** - Seuls les abonnements figurant sur la liste verte et disposant d’un accès Azure RBAC peuvent localiser le service. 
- **Tout le monde** - Tout le monde peut localiser le service. 
 
### <a name="can-i-create-a-private-link-service-with-basic-load-balancer"></a>Est-il possible de créer un service Azure Private Link avec un équilibreur de charge de base ? 
Non. Il n’est pas possible de créer un service Azure Private Link avec un équilibreur de charge de base.
 
### <a name="is-a-dedicated-subnet-required-for-private-link-service"></a>Est-il nécessaire d’avoir un sous-réseau dédié pour un service Private Link ? 
Non. Le service Private Link ne nécessite pas de sous-réseau dédié. Vous pouvez choisir n’importe quel sous-réseau du réseau virtuel où est déployé votre service.   

### <a name="im-a-service-provider-using-azure-private-link-do-i-need-to-make-sure-all-my-customers-have-unique-ip-space-and-dont-overlap-with-my-ip-space"></a>Je suis fournisseur de services et j’utilise Azure Private Link. Dois-je m’assurer que tous mes clients disposent de leur propre espace d’adressage IP afin qu’ils ne chevauchent pas le mien ? 
Non. Azure Private Link s’en charge à votre place. Vous n’êtes pas obligé d’avoir un espace d’adressage qui ne chevauche pas celui de vos clients. 

##  <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur [Azure Private Link](private-link-overview.md)
