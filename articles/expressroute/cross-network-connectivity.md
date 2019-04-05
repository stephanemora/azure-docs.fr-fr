---
title: Connectivité de réseau Azure | Microsoft Docs
description: Cette page décrit un scénario d’application pour la connectivité réseau et de la solution basée sur les fonctionnalités de mise en réseau Azure.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: expressroute
ms.topic: article
ms.workload: infrastructure-services
ms.date: 04/03/2019
ms.author: rambala
ms.openlocfilehash: 4923c7f2048b7368af6314d5e2288216115bc3bc
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/05/2019
ms.locfileid: "59052656"
---
# <a name="cross-network-connectivity"></a>Connectivité de réseau

Fabrikam Inc. a une présence physique et un déploiement Azure importants dans la région USA Est. Fabrikam a une connectivité back-end entre ses déploiements locaux et Azure par le biais d’ExpressRoute. De même, Contoso Ltd. a une présence et le déploiement d’Azure dans l’ouest des États-Unis. Contoso a une connectivité back-end entre ses déploiements locaux et Azure par le biais d’ExpressRoute.  

Fabrikam Inc. acquiert Contoso Ltd. Suite à la fusion, Fabrikam souhaite interconnecter les réseaux. La figure suivante illustre le scénario :

 [![1]][1]

Les flèches en pointillés au milieu de la figure ci-dessus indiquent les interconnexions réseau souhaité. Plus précisément, il existe trois types de connexions croisées souhaitées : Fabrikam et des VNets de Contoso croisé se connectent (1), 2) Cross régional en local et les réseaux virtuels entre différents se connecte (c'est-à-dire, connexion de réseau local de Fabrikam à Contoso VNet et connexion de réseau local de Contoso à Fabrikam VNet) et (3) Fabrikam et Contoso réseau local entre se connecter. 

Le tableau suivant montre la table de routage de l’homologation privée de l’ExpressRoute de Contoso Ltd., avant la fusion.

[![2]][2]

Le tableau suivant présente les itinéraires effectifs d’une machine virtuelle dans l’abonnement Contoso, avant la fusion. Par la table, la machine virtuelle sur le réseau virtuel est informée de l’espace d’adressage de réseau virtuel et le réseau Contoso local, en dehors de celles par défaut. 

[![4]][4]

Le tableau suivant montre la table de routage de l’homologation privée de l’ExpressRoute de Fabrikam Inc., avant la fusion.

[![3]][3]

Le tableau suivant présente les itinéraires effectifs d’une machine virtuelle dans l’abonnement de Fabrikam, avant la fusion. Par la table, la machine virtuelle sur le réseau virtuel est informée de l’espace d’adressage de réseau virtuel et le réseau local de Fabrikam, en dehors de celles par défaut.

[![5]][5]

Dans cet article, nous allons examiner étape par étape et expliquent comment obtenir les interconnexions souhaitées en utilisant les fonctionnalités de réseau Azure suivantes :

* [Appairage de réseaux virtuels][Virtual network peering] 
* [Connexion ExpressRoute de réseau virtuel][connection]
* [Portée mondiale][Global Reach] 

## <a name="cross-connecting-vnets"></a>Cross-connexion de réseaux virtuels

Homologation de réseaux virtuels (VNet peering) fournit les meilleurs et les meilleures performances réseau lors de la connexion de deux réseaux virtuels. Homologation de réseau virtuel prend en charge l’homologation deux réseaux virtuels à la fois dans la même région Azure (communément appelé homologation) et deux régions Azure différentes (communément appelé Global VNet peering). 

Nous allons configurer Global VNet peering entre les réseaux virtuels dans Contoso et Fabrikam les abonnements Azure. Pour savoir comment créer le réseau virtuel, l’homologation entre deux réseaux virtuels, consultez [créer une homologation de réseau virtuel] [ Configure VNet peering] article.

L’illustration suivante montre l’architecture réseau après avoir configuré l’homologation globale.

[![6]][6]

Le tableau suivant présente les itinéraires connus à l’abonnement Contoso machine virtuelle. Faites attention à la dernière entrée de la table. Cette entrée est le résultat des interconnexions de réseaux virtuels.

[![7]][7]

Le tableau suivant présente les itinéraires connus à l’abonnement de Fabrikam machine virtuelle. Faites attention à la dernière entrée de la table. Cette entrée est le résultat des interconnexions de réseaux virtuels.

[![8]][8]

Homologation directement lie les deux réseaux virtuels (voir, il n’y a aucun tronçon suivant pour *VNetGlobalPeering* entrée dans les deux tables ci-dessus)

## <a name="cross-connecting-vnets-to-the-on-premises-networks"></a>Cross-connexion de réseaux virtuels aux réseaux locaux

Nous pouvons nous connecter un circuit ExpressRoute à plusieurs réseaux virtuels. Consultez [abonnement et les limites de service] [ Subscription limits] pour le nombre maximal de réseaux virtuels qui peuvent être connectées à un circuit ExpressRoute. 

Restons en contact Fabrikam circuit ExpressRoute à l’abonnement Contoso réseau virtuel et même circuit ExpressRoute de Contoso à Fabrikam abonnement réseau virtuel pour activer la connectivité croisée entre réseaux virtuels et les réseaux locaux. Pour vous connecter à un réseau virtuel à un circuit ExpressRoute dans un autre abonnement, nous avons besoin créer et utiliser une autorisation.  Consultez l’article : [Connecter un réseau virtuel à un circuit ExpressRoute][Connect-ER-VNet].

L’illustration suivante montre l’architecture réseau après avoir configuré le circuit Cross-connectivité aux réseaux virtuels.

[![9]][9]

Le tableau suivant montre la table de routage de l’homologation privée de l’ExpressRoute de Contoso Ltd., après interconnexions de réseaux virtuels aux réseaux locaux via ExpressRoute. Voir que la table de routage présente les itinéraires qui appartiennent à deux réseaux virtuels.

[![10]][10]

Le tableau suivant montre la table de routage de l’homologation privée d’ExpressRoute de Fabrikam Inc., après interconnexions de réseaux virtuels aux réseaux locaux via ExpressRoute. Voir que la table de routage présente les itinéraires qui appartiennent à deux réseaux virtuels.

[![11]][11]

Le tableau suivant présente les itinéraires connus à l’abonnement Contoso machine virtuelle. Faites attention à *passerelle de réseau virtuel* entrées de la table. La machine virtuelle voit les itinéraires pour les réseaux locaux.

[![12]][12]

Le tableau suivant présente les itinéraires connus à l’abonnement de Fabrikam machine virtuelle. Faites attention à *passerelle de réseau virtuel* entrées de la table. La machine virtuelle voit les itinéraires pour les réseaux locaux.

[![13]][13]

>[!NOTE]
>Dans une les abonnements Fabrikam et/ou de Contoso, que vous pouvez également avoir réseaux virtuels spokes au hub respectif réseau virtuel (une conception hub- and -spoke n’est pas illustrée dans les diagrammes d’architecture dans cet article). Les interconnexions entre les passerelles de réseau virtuel hub à ExpressRoute seront également permettre la communication entre les concentrateurs est et ouest et de rayons.
>

## <a name="cross-connecting-on-premises-networks"></a>Cross qui se connecte à des réseaux locaux

Portée mondiale ExpressRoute fournit la connectivité entre des réseaux locaux qui sont connectés à des circuits ExpressRoute distincts. Nous allons configurer portée mondiale entre Contoso et Fabrikam ExpressRoute circuits. Étant donné que les circuits ExpressRoute se trouvent dans différents abonnements, nous avons besoin créer et utiliser une autorisation. Consultez [configurer ExpressRoute Global atteindre] [ Configure Global Reach] article pour obtenir des conseils étape par étape.

L’illustration suivante montre l’architecture réseau après avoir configuré la portée mondiale.

[![14]][14]

Le tableau suivant montre la table de routage de l’homologation privée de l’ExpressRoute de Contoso Ltd., après avoir configuré la portée mondiale. Voir que la table de routage présente les itinéraires qui appartiennent à deux réseaux locaux. 

[![15]][15]

Le tableau suivant montre la table de routage de l’homologation privée d’ExpressRoute de Fabrikam Inc., après avoir configuré la portée mondiale. Voir que la table de routage présente les itinéraires qui appartiennent à deux réseaux locaux.

[![16]][16]

## <a name="next-steps"></a>Étapes suivantes

Consultez [FAQ virtual network][VNet-FAQ], pour toute question supplémentaire sur le réseau virtuel et l’homologation de réseau virtuel. Consultez [Forum aux questions ExpressRoute] [ ER-FAQ] pour d’autres questions sur ExpressRoute et virtuels de connectivité réseau.

Global Reach est déployé pays par pays. Pour vérifier si Global Reach est disponible dans les pays qui vous intéressent, consultez [ExpressRoute Global Reach][Global Reach].

<!--Image References-->
[1]: ./media/cross-network-connectivity/premergerscenario.png "Scénario d’application"
[2]: ./media/cross-network-connectivity/contosoexr-rt-premerger.png "table de routage de Contoso ExpressRoute avant la fusion"
[3]: ./media/cross-network-connectivity/fabrikamexr-rt-premerger.png "table de routage de Fabrikam ExpressRoute avant la fusion"
[4]: ./media/cross-network-connectivity/contosovm-routes-premerger.png "Contoso VM achemine avant la fusion"
[5]: ./media/cross-network-connectivity/fabrikamvm-routes-premerger.png "Fabrikam VM achemine avant la fusion"
[6]: ./media/cross-network-connectivity/vnet-peering.png "l’Architecture après l’homologation de réseau virtuel"
[7]: ./media/cross-network-connectivity/contosovm-routes-peering.png "Contoso VM achemine après homologation"
[8]: ./media/cross-network-connectivity/fabrikamvm-routes-peering.png "Fabrikam VM achemine après homologation"
[9]: ./media/cross-network-connectivity/exr-x-connect.png "the Architecture après ExpressRoutes une connexion croisée"
[10]: ./media/cross-network-connectivity/contosoexr-rt-xconnect.png "table de routage de Contoso ExpressRoute après la connexion ExR et réseaux virtuels"
[11]: ./media/cross-network-connectivity/fabrikamexr-rt-xconnect.png "table de routage de Fabrikam ExpressRoute après la connexion ExR et réseaux virtuels"
[12]: ./media/cross-network-connectivity/contosovm-routes-xconnect.png "qui se connecte ExR et les réseaux virtuels dans plusieurs itinéraires Contoso VM après"
[13]: ./media/cross-network-connectivity/fabrikamvm-routes-xconnect.png "qui se connecte ExR et les réseaux virtuels dans plusieurs itinéraires Fabrikam VM après"
[14]: ./media/cross-network-connectivity/globalreach.png "the Architecture après avoir configuré la portée mondiale"
[15]: ./media/cross-network-connectivity/contosoexr-rt-gr.png "table de routage de Contoso ExpressRoute après la portée mondiale"
[16]: ./media/cross-network-connectivity/fabrikamexr-rt-gr.png "table de routage de Fabrikam ExpressRoute après la portée mondiale"

<!--Link References-->
[Virtual network peering]: https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview
[connection]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-portal-resource-manager
[Global Reach]: https://docs.microsoft.com/azure/expressroute/expressroute-global-reach
[Configure VNet peering]: https://docs.microsoft.com/azure/virtual-network/create-peering-different-subscriptions
[Configure Global Reach]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-set-global-reach
[Subscription limits]: https://docs.microsoft.com/azure/azure-subscription-service-limits#networking-limits
[Connect-ER-VNet]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-portal-resource-manager
[ER-FAQ]: https://docs.microsoft.com/azure/expressroute/expressroute-faqs
[VNet-FAQ]: https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq