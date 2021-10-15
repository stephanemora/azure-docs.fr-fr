---
title: Consignes pour planifier un réseau Azure NetApp Files | Microsoft Docs
description: Fournit des instructions qui peuvent vous aider à concevoir une architecture réseau efficace à l’aide d’Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: ram-kakani
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/04/2021
ms.author: ramakk
ms.openlocfilehash: 09f09b5c50dd04f39f95405df9875d458a258b25
ms.sourcegitcommit: 57b7356981803f933cbf75e2d5285db73383947f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2021
ms.locfileid: "129545960"
---
# <a name="guidelines-for-azure-netapp-files-network-planning"></a>Consignes pour planifier un réseau Azure NetApp Files

La planification de l’architecture réseau est un élément clé dans la conception d’infrastructure d’application. Cet article vous aide à concevoir une architecture réseau efficace pour que vos charges de travail tirent parti des fonctionnalités enrichies d’Azure NetApp Files.

Les volumes Azure NetApp Files sont conçus pour être contenus dans un sous-réseau à but précis appelé [sous-réseau délégué](../virtual-network/virtual-network-manage-subnet.md), au sein de votre réseau virtuel Azure. Par conséquent, vous pouvez accéder aux volumes directement à partir d’Azure via un appairage de réseaux virtuels ou en local via une passerelle de réseau virtuel (ExpressRoute ou passerelle VPN) le cas échéant. Le sous-réseau est dédié à Azure NetApp Files et il n’y a aucune connectivité à Internet. 

## <a name="configurable-network-features"></a>Fonctionnalités réseau configurables  

 La configuration des [**fonctionnalités réseau Standard**](configure-network-features.md) pour Azure NetApp Files est disponible en préversion publique. Après vous être inscrit à cette fonctionnalité avec votre abonnement, vous pouvez créer de nouveaux volumes en choisissant des fonctionnalités réseau *Standard* ou *De base* dans les régions prises en charge. Dans les régions où les fonctionnalités réseau Standard ne sont pas prises en charge, le volume utilise par défaut les fonctionnalités réseau De base.  

* ***Standard***  
    La sélection de ce paramètre permet d’activer des limites d’adresse IP plus élevées et des fonctionnalités de réseau virtuel standard telles que des [groupes de sécurité réseau](../virtual-network/network-security-groups-overview.md) et des [itinéraires définis par l’utilisateur](../virtual-network/virtual-networks-udr-overview.md#user-defined) sur des sous-réseaux délégués, ainsi que des modèles de connectivité supplémentaires comme indiqué dans cet article.

* ***De base***  
    La sélection de ce paramètre permet des modèles de connectivité sélectifs et une échelle IP limitée, comme indiqué dans la section [Considérations](#considerations). Toutes les [contraintes](#constraints) s’appliquent dans ce paramètre. 

## <a name="considerations"></a>Considérations  

Vous devez comprendre quelques considérations lorsque vous prévoyez d’utiliser un réseau Azure NetApp Files.

### <a name="constraints"></a>Contraintes

Le tableau suivant décrit les éléments pris en charge pour chaque configuration de fonctionnalités réseau :

|      Fonctionnalités     |      Fonctionnalités réseau Standard     |      Fonctionnalités réseau De base     |
|---|---|---|
|     Nombre d’adresses IP utilisées dans un réseau virtuel avec Azure NetApp Files (y compris les réseaux virtuels appairés immédiatement)    |     [Limites standard en tant que machines virtuelles](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits)    |     1 000    |
|     Sous-réseaux délégués ANF par réseau virtuel    |     1    |     1    |
|     [Groupes de sécurité réseau](../virtual-network/network-security-groups-overview.md) (NSG) sur les sous-réseaux délégués Azure NetApp Files    |     Oui    |     Non    |
|     [Itinéraires définis par l’utilisateur](../virtual-network/virtual-networks-udr-overview.md#user-defined) (UDR) sur les sous-réseaux délégués Azure NetApp Files    |     Oui    |     Non    |
|     Connectivité aux [points de terminaison privés](../private-link/private-endpoint-overview.md)    |     Non    |     Non    |
|     Connectivité aux [points de terminaison de service](../virtual-network/virtual-network-service-endpoints-overview.md)    |     Non    |     Non    |
|     Stratégies Azure (par exemple, des stratégies personnalisées d’affectation de noms) sur l’interface Azure NetApp Files    |     Non    |     Non    |
|     Équilibreurs de charge pour le trafic d’Azure NetApp Files    |     Non    |     Non    |
|     Réseau virtuel à double pile (IPv4 et IPv6)    |     Non <br> (IPv4 uniquement pris en charge)    |     Non <br> (IPv4 uniquement pris en charge)    |

### <a name="supported-network-topologies"></a>Topologies de réseau prises en charge

Le tableau suivant décrit les topologies de réseau prises en charge par chaque configuration de fonctionnalités réseau d’Azure NetApp Files. 

|      Topologies     |      Fonctionnalités réseau Standard     |      Fonctionnalités réseau De base     |
|---|---|---|
|     Connectivité à un volume dans un réseau local virtuel    |     Oui    |     Oui    |
|     Connectivité à un volume dans un réseau virtuel homologué (même région)    |     Oui    |     Oui    |
|     Connectivité à un volume dans un réseau virtuel appairé (entre régions ou Peering mondial)    |     Non    |     Non    |
|     Connectivité à un volume via la passerelle ExpressRoute    |     Oui    |     Oui    |
|     ExpressRoute (ER) FastPath    |     Oui    |     Non    |
|     Connectivité d’un environnement local à un volume dans un réseau virtuel spoke via la passerelle ExpressRoute et l’appairage de réseaux virtuels avec transit de passerelle    |     Oui    |     Oui    |
|     Connectivité d’un environnement local à un volume dans un réseau virtuel spoke via la passerelle VPN    |     Oui    |     Oui    |
|     Connectivité d’un environnement local à un volume dans un réseau virtuel spoke via la passerelle VPN et l’appairage de réseaux virtuels avec transit de passerelle    |     Oui    |     Oui    |
|     Connectivité sur les passerelles VPN en mode actif/passif    |     Oui    |     Oui    |
|     Connectivité sur les passerelles VPN en mode actif/actif    |     Oui    |     Non    |
|     Connectivité sur les passerelles redondantes interzone en mode actif/actif    |     Non    |     Non    |
|     Connectivité sur Virtual WAN (VWAN)    |     Non    |     Non    |

## <a name="virtual-network-for-azure-netapp-files-volumes"></a>Réseau virtuel pour les volumes Azure NetApp Files

Cette section explique les concepts qui vous aident à la planification du réseau virtuel.

### <a name="azure-virtual-networks"></a>Réseaux virtuels Azure

Avant d’approvisionner un volume Azure NetApp Files, vous devez créer un réseau virtuel Azure (VNet) ou en utiliser un qui existe déjà dans votre abonnement. Le réseau virtuel définit la limite réseau du volume.  Pour plus d’informations sur la création de réseaux virtuels, voir la [documentation du réseau virtuel Azure](../virtual-network/virtual-networks-overview.md).

### <a name="subnets"></a>Sous-réseaux

Les sous-réseaux segmentent le réseau virtuel en espaces d’adressage distincts utilisables par les ressources Azure que vous placez dedans.  Les volumes Azure NetApp Files sont contenus dans un sous-réseau à but précis appelé [sous-réseau délégué](../virtual-network/virtual-network-manage-subnet.md). 

La délégation de sous-réseau donne des autorisations explicites au service Azure NetApp Files pour créer des ressources propres au service dans le sous-réseau.  Il utilise un identificateur unique dans le déploiement du service. Dans ce cas, une interface réseau est créée pour activer la connectivité à Azure NetApp Files.

Si vous utilisez un réseau virtuel, vous pouvez créer un sous-réseau et déléguer le sous-réseau à Azure NetApp Files en suivant les instructions dans [Déléguer un sous-réseau à Azure NetApp Files](azure-netapp-files-delegate-subnet.md). Vous pouvez également déléguer un sous-réseau vide existant qui n’est pas déjà délégué à d’autres services.

Si le réseau virtuel est appairé à un autre réseau virtuel, vous ne pouvez pas développer l’espace d’adressage du réseau virtuel. Pour cette raison, le nouveau sous-réseau délégué doit être créé au sein de l’espace d’adressage du réseau virtuel. Si vous avez besoin d’étendre l’espace d’adressage, vous devez d’abord supprimer le peering.

### <a name="udrs-and-nsgs"></a>Itinéraires définis par l’utilisateur et groupes de sécurité réseau

Les itinéraires définis par l’utilisateur (UDR) et les groupes de sécurité réseau (NSG) sont uniquement pris en charge sur les sous-réseaux délégués Azure NetApp Files qui ont au moins un volume créé avec des fonctionnalités réseau Standard.  

> [!NOTE]
> L’association de NSG au niveau de l’interface réseau n’est pas prise en charge pour les interfaces réseau d’Azure NetApp Files. 

Si le sous-réseau comporte une combinaison de volumes dotés des fonctionnalités réseau Standard et De base (ou pour les volumes existants non inscrits à la fonctionnalité d’évaluation), les UDR et les NSG appliqués aux sous-réseaux délégués ne s’appliqueront qu’aux volumes dotés des fonctionnalités réseau Standard.

Les volumes dotés des fonctionnalités réseau De base ne prennent pas en charge la configuration d’itinéraires définis par l’utilisateur (UDR) sur les sous-réseaux de machines virtuelles sources avec le préfixe d’adresse du sous-réseau délégué et le tronçon suivant en tant qu’appliance virtuelle réseau. Ce paramètre entraîne des problèmes de connectivité.

## <a name="azure-native-environments"></a>Environnements natifs Azure

Le diagramme suivant illustre un environnement natif Azure :

![Environnement de mise en réseau Azure natif](../media/azure-netapp-files/azure-netapp-files-network-azure-native-environment.png)

### <a name="local-vnet"></a>Réseau virtuel local

Un scénario de base consiste à créer ou à se connecter à un volume Azure NetApp Files à partir d’une machine virtuelle (VM) dans le même réseau virtuel. Pour le réseau virtuel 2 dans le diagramme ci-dessus, Volume 1 est créé dans un sous-réseau délégué et peut être monté sur la machine virtuelle 1 dans le sous-réseau par défaut.

### <a name="vnet-peering"></a>Peering de réseaux virtuels

Si vous avez d’autres réseaux virtuels dans la même région qui ont besoin d’accéder aux ressources d’un autre, les réseaux virtuels peuvent être connectés à l’aide du [peering de réseau virtuel](../virtual-network/virtual-network-peering-overview.md) pour activer la connectivité sécurisée via l’infrastructure Azure. 

Prenez pour exemple les réseaux virtuels 2 et 3 dans le diagramme ci-dessus. Si la machine virtuelle 1 doit se connecter à la machine virtuelle 2 ou au volume 2, ou si la machine virtuelle 2 doit se connecter à la machine virtuelle 1 ou au volume 1, vous devez activer le peering de réseaux virtuels entre les réseaux virtuels 2 et 3. 

De plus, prenez pour exemple un scénario où le réseau virtuel 1 est appairé au réseau virtuel 2, et où le réseau virtuel 2 est appairé au réseau virtuel 3 dans la même région. Les ressources du réseau virtuel 1 peuvent se connecter aux ressources du réseau virtuel 2, mais pas aux ressources du réseau virtuel 3, sauf si les réseaux virtuels 1 et 3 sont appairés. 

Dans le diagramme ci-dessus, la machine virtuelle 3 peut se connecter au Volume 1, mais la machine virtuelle 4 ne peut pas se connecter au Volume 2.  Il en est ainsi car les réseaux virtuels spoke ne sont pas appairés, et _le routage de transit n’est pas pris en charge via le peering de réseau virtuel_.

## <a name="hybrid-environments"></a>Environnements hybrides

Le diagramme suivant illustre un environnement hybride : 

![Environnement de mise en réseau hybride](../media/azure-netapp-files/azure-netapp-files-network-hybrid-environment.png)

Dans le scénario hybride, les applications de centres de données locaux ont besoin d’accéder aux ressources dans Azure.  C’est le cas que vous souhaitiez étendre votre centre de données à Azure, ou que vous souhaitiez utiliser les services Azure natifs pour la reprise d’activité. Consultez [Options de planification de la passerelle VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#planningtable) pour plus d’informations sur la façon de connecter plusieurs ressources locales à des ressources dans Azure via un réseau privé virtuel de site à site ou un circuit Express Route.

Dans une topologie hub-and-spoke hybride, le hub du réseau virtuel dans Azure centralise la connectivité à votre réseau local. Les spokes sont des réseaux virtuels appairés avec le hub et qui peuvent être utilisés pour isoler les charges de travail.

Selon la configuration, vous pouvez connecter des ressources locales à des ressources dans le hub et les spokes.

Dans la topologie illustrée ci-dessus, le réseau local est connecté à un hub de réseau virtuel dans Azure et il y a 2 réseaux virtuels spoke dans la même région appairés au hub du réseau virtuel.  Dans ce scénario, les options de connectivité prises en charge pour les volumes Azure NetApp Files sont les suivantes :

* Les ressources locales des machines virtuelles 1 et 2 peuvent se connecter au volume 1 dans le hub via un réseau privé virtuel de site à site ou un circuit Express Route. 
* Les ressources locales des machines virtuelles 1 et 2 peuvent se connecter au volume 2 ou 3 via une passerelle VPN site à site et un appairage régional de réseaux virtuels.
* La machine virtuelle 3 dans le réseau virtuel du hub peut se connecter au volume 2 dans le réseau virtuel 1 du spoke et au volume 3 dans le réseau virtuel 2 du spoke.
* La machine virtuelle 4 dans le réseau virtuel 1 du spoke et la machine virtuelle 5 dans le réseau virtuel 2 du spoke peuvent se connecter au volume 1 dans le réseau virtuel du hub.
* La machine virtuelle 4 dans le réseau virtuel 1 du spoke ne peut pas se connecter au volume 3 du réseau virtuel 2 du spoke. Aussi, la machine virtuelle 5 dans le réseau virtuel 2 du spoke ne peut pas se connecter au volume 2 du réseau virtuel 1 du spoke. Il en est ainsi car les réseaux virtuels spoke ne sont pas appairés, et _le routage de transit n’est pas pris en charge via le peering de réseau virtuel_.
* Dans l’architecture ci-dessus, s’il existe également une passerelle dans le réseau virtuel spoke, la connectivité au volume ANF à partir d’un réseau local se connectant via la passerelle dans le hub sera perdue. Par défaut, la préférence est donnée à la passerelle dans le réseau virtuel spoke et, ainsi, seules les machines qui se connectent via cette passerelle peuvent se connecter au volume ANF.

## <a name="next-steps"></a>Étapes suivantes

* [Déléguer un sous-réseau à Azure NetApp Files](azure-netapp-files-delegate-subnet.md)
* [Configurer les fonctionnalités réseau d’un volume Azure NetApp Files](configure-network-features.md) 
