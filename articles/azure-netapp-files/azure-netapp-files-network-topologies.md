---
title: Instructions pour les fichiers de NetApp Azure réseau planification | Microsoft Docs
description: Fournit des instructions qui peuvent vous aider à concevoir une architecture réseau efficace à l’aide de fichiers NetApp de Azure.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: b-juche
ms.openlocfilehash: fa2de14ada5d24531dfecc7f2f709a87f39ea6cb
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/17/2019
ms.locfileid: "65826447"
---
# <a name="guidelines-for-azure-netapp-files-network-planning"></a>Consignes pour planifier un réseau Azure NetApp Files

Planification de l’architecture réseau est un élément clé de la conception d’infrastructure d’application. Cet article vous aide à concevoir une architecture réseau efficace pour vos charges de travail tirer parti des fonctionnalités enrichies de fichiers NetApp de Azure.

Volumes de fichiers de NetApp Azure sont conçus pour être contenus dans un sous-réseau objectif spécial appelé [déléguée sous-réseau](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet) au sein de votre réseau virtuel Azure. Par conséquent, vous pouvez accéder aux volumes directement à partir de votre réseau virtuel, à partir de réseaux virtuels homologués dans la même région ou en local via une passerelle de réseau virtuel (ExpressRoute ou passerelle VPN) en fonction des besoins. Le sous-réseau est dédié aux fichiers de NetApp Azure et il n’existe aucune connectivité aux autres services Azure ou à internet.

## <a name="considerations"></a>Considérations  

Vous devez comprendre quelques considérations lorsque vous planifiez de réseau d’Azure Files de NetApp.

### <a name="constraints"></a>Contraintes

Les fonctionnalités ci-dessous sont actuellement pas pris en charge pour les fichiers de NetApp Azure : 

* Groupes de sécurité réseau (NSG) sur le sous-réseau
* Itinéraires définis par l’utilisateur (UDR) avec un tronçon suivant en tant que sous-réseau de fichiers Azure NetApp
* Stratégies Azure (par exemple, des stratégies d’affectation de noms personnalisées) sur l’interface Azure NetApp Files
* Équilibreurs de charge pour le trafic de fichiers NetApp de Azure

Les restrictions suivantes s’appliquent aux fichiers de NetApp Azure :

* Le nombre de machines virtuelles qui peuvent se connecter à un volume (avec un réseau virtuel ou entre des réseaux virtuels homologués) ne peut pas dépasser 1000.
* Dans chaque réseau virtuel Azure, un seul sous-réseau peut être délégué à Azure NetApp Files.


### <a name="supported-network-topologies"></a>Topologies de réseau prises en charge

Le tableau suivant décrit les topologies réseau prises en charge par Azure Files de NetApp.  Elle décrit également les solutions de contournement pour les topologies non prises en charge. 

|    Topologies    |    est pris en charge    |     Solution de contournement    |
|-------------------------------------------------------------------------------------------------------------------------------|--------------------|-----------------------------------------------------------------------------|
|    Connectivité à un volume dans un réseau local virtuel    |    Oui    |         |
|    Connectivité à volume dans un réseau virtuel homologué (même région)    |    Oui    |         |
|    Connectivité à volume dans un réseau virtuel homologué (Cross région ou l’homologation globale)    |    Non    |    Aucun    |
|    Connectivité à un volume via la passerelle ExpressRoute    |    Oui    |         |
|    Connectivité locale vers un volume dans un réseau virtuel spoke via la passerelle ExpressRoute et homologation avec transit de passerelle    |    Non    |    Créer un sous-réseau délégué dans le concentrateur réseau virtuel (réseau virtuel Azure avec la passerelle)    |
|    Connectivité locale vers un volume dans un réseau virtuel spoke via la passerelle VPN    |    Oui    |         |
|    Connectivité locale vers un volume dans un réseau virtuel spoke via la passerelle VPN et homologation avec transit de passerelle    |    Oui    |         |


## <a name="virtual-network-for-azure-netapp-files-volumes"></a>Réseau virtuel pour les volumes de fichiers NetApp de Azure

Cette section explique les concepts qui vous aident à la planification du réseau virtuel.

### <a name="azure-virtual-networks"></a>Réseaux virtuels Azure

Avant de configurer un volume Azure Files de NetApp, vous devez créer un réseau virtuel Azure (VNet) ou utiliser un qui existe déjà dans votre abonnement. Le réseau virtuel définit la limite du réseau du volume.  Pour plus d’informations sur la création de réseaux virtuels, consultez le [documentation réseau virtuel Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview).

### <a name="subnets"></a>Sous-réseaux

Sous-réseaux segment le réseau virtuel en espaces d’adressage distincts qui sont utilisables par les ressources Azure.  Volumes de fichiers de NetApp Azure sont contenus dans un sous-réseau à usage spécial appelé [déléguée sous-réseau](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet). 

Délégation de sous-réseau donne des autorisations explicites pour le service de fichiers NetApp de Azure pour créer des ressources de service spécifique dans le sous-réseau.  Il utilise un identificateur unique dans le déploiement du service. Dans ce cas, une interface réseau est créée pour activer la connectivité à Azure Files de NetApp.

Si vous utilisez un réseau virtuel, vous pouvez créer un sous-réseau et déléguer le sous-réseau pour les fichiers NetApp Azure en suivant les instructions dans [déléguer un sous-réseau pour Azure Files de NetApp](azure-netapp-files-delegate-subnet.md). Vous pouvez également déléguer un sous-réseau vide existant qui n’est pas déjà délégué à d’autres services.

Si le réseau virtuel est homologué avec un autre réseau virtuel, vous ne pouvez pas développer l’espace d’adressage de réseau virtuel. Pour cette raison, le nouveau sous-réseau délégué doit être créé au sein de l’espace d’adressage de réseau virtuel. Si vous avez besoin étendre l’espace d’adressage, vous devez supprimer l’homologation avant de l’étendre l’espace d’adressage.

### <a name="udrs-and-nsgs"></a>UDR et les groupes de sécurité réseau

Groupes de sécurité réseau (NSG) avec un tronçon suivant ne peut pas servir de sous-réseaux déléguées pour Azure Files de NetApp. De même, les itinéraires définis par l’utilisateur (UDR) sont également pas pris en charge. 

Pour résoudre ce problème, vous pouvez appliquer des groupes de sécurité réseau à d’autres sous-réseaux qui autorise ou refuser le trafic vers et depuis le sous-réseau Azure NetApp Files déléguée.  

## <a name="azure-native-environments"></a>Environnements natifs Azure

Le diagramme suivant illustre un environnement Azure native :

![Environnement de mise en réseau Azure natives](../media/azure-netapp-files/azure-netapp-files-network-azure-native-environment.png)

### <a name="local-vnet"></a>Réseau local virtuel

Un scénario de base consiste à créer ou se connecter à un volume Azure NetApp Files à partir d’une machine virtuelle (VM) dans le même réseau virtuel. Pour le réseau virtuel 2 dans le diagramme ci-dessus, Volume 1 est créé dans un sous-réseau délégué et peuvent être montés sur la machine virtuelle 1 dans le sous-réseau par défaut.

### <a name="vnet-peering"></a>Homologation de réseaux virtuels

Si vous avez d’autres réseaux virtuels dans la même région qui ont besoin d’accéder aux ressources de l’autre, les réseaux virtuels peuvent être connectés à l’aide de [homologation](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) pour activer la connectivité sécurisée via l’infrastructure Azure. 

Envisagez de réseau virtuel 2 et 3 du réseau virtuel dans le diagramme ci-dessus. Si 1 de la machine virtuelle doit se connecter à la machine virtuelle 2 et 2 de Volume, ou si la machine virtuelle 2 doit se connecter à la machine virtuelle 1 ou Volume 1, vous devez activer l’homologation de réseau virtuel 2 et 3 du réseau virtuel. 

En outre, envisagez un scénario où réseau virtuel 1 est homologué avec réseau virtuel 2 et vnet2 est appairé avec 3 de réseau virtuel dans la même région. Les ressources de réseau virtuel 1 peuvent se connecter aux ressources de réseau virtuel 2, mais il ne peut pas se connecter aux ressources de réseau virtuel 3, réseau virtuel 1 et 3 du réseau virtuel sont homologués. 

Dans le diagramme ci-dessus, bien que 3 de la machine virtuelle peut se connecter à Volume 1, 4 de la machine virtuelle ne peut pas se connecter à un Volume 2.  La raison est que les réseaux virtuels spokes ne sont pas homologués, et _le routage de transit n’est pas pris en charge via une homologation de réseau virtuel_.

## <a name="hybrid-environments"></a>Environnements hybrides

Le diagramme suivant illustre un environnement hybride : 

![Environnement de mise en réseau hybride](../media/azure-netapp-files/azure-netapp-files-networ-hybrid-environment.png)

Dans le scénario hybride, les applications à partir de centres de données locaux ont besoin d’accéder aux ressources dans Azure.  C’est le cas si vous souhaitez étendre votre centre de données vers Azure, ou que vous souhaitez utiliser les services Azure natifs ou récupération d’urgence. Consultez [passerelle VPN options de planification](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways?toc=%2fazure%2fvirtual-network%2ftoc.json#planningtable) indiquant comment connecter plusieurs ressources locales à des ressources dans Azure via un VPN de site à site ou ExpressRoute.

Dans une topologie hub-and-spoke hybride, le réseau virtuel dans Azure hub agit comme un point central de la connectivité à votre réseau local. Les rayons sont des réseaux virtuels homologués avec le hub, et ils peuvent être utilisés pour isoler les charges de travail.

Selon la configuration. Vous pouvez connecter des ressources du système local aux ressources dans le hub et les rayons.

Dans la topologie illustrée ci-dessus, le réseau local est connecté à un concentrateur réseau virtuel dans Azure, et il existe spoke 2 réseaux virtuels homologués avec le réseau virtuel hub.  Dans ce scénario, les options de connectivité pris en charge pour les volumes de fichiers NetApp de Azure sont les suivantes :

* Ressources sur site machine virtuelle 1 et 2 de la machine virtuelle peuvent se connecter à Volume 1 dans le hub via un VPN de site à site ou ExpressRoute. 
* Ressources locales VM 1 et 2 de la machine virtuelle peuvent se connecter à un Volume 2 ou 3 de Volume.
* 3 de machine virtuelle dans le concentrateur réseau virtuel peut se connecter à volume 2 dans le membre spoke réseau virtuel 1 et 3 de Volume dans le membre spoke 2 de réseau virtuel.
* 4 de machine virtuelle à partir de membre spoke 1 de réseau virtuel et 5 de machine virtuelle à partir de spoke 2 de réseau virtuel peuvent se connecter à un Volume 1, dans le réseau virtuel hub.

4 machines virtuelles dans le membre spoke 1 de réseau virtuel ne peut pas se connecter à Volume 3 dans le membre spoke 2 de réseau virtuel. En outre, 5 de machine virtuelle dans spoke VNet2 ne peut pas se connecter à Volume 2 dans le membre spoke 1 de réseau virtuel. C’est le cas, car les réseaux virtuels spokes ne sont pas homologués et _le routage de transit n’est pas pris en charge via une homologation de réseau virtuel_.

## <a name="next-steps"></a>Étapes suivantes

[Déléguer un sous-réseau à Azure NetApp Files](azure-netapp-files-delegate-subnet.md)
