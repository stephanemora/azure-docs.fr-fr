---
title: Intégration à un réseau virtuel de services Azure pour l’isolement réseau
titlesuffix: Azure Virtual Network
description: Cet article décrit différentes méthodes d’intégration d’un service Azure à un réseau virtuel qui vous permet d’accéder en toute sécurité au service Azure.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 12/01/2020
ms.author: kumud
ms.openlocfilehash: 007424969672167d7ca81b2130cda8e0a5da8000
ms.sourcegitcommit: 9ad20581c9fe2c35339acc34d74d0d9cb38eb9aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2021
ms.locfileid: "110539401"
---
# <a name="integrate-azure-services-with-virtual-networks-for-network-isolation"></a>Intégrer des services Azure à des réseaux virtuels pour l’isolement réseau

L’intégration d’un service Azure à un réseau virtuel vous permet de verrouiller l’accès au service à votre infrastructure de réseau virtuel uniquement. L’infrastructure de réseau virtuel comprend également des réseaux virtuels appairés et des réseaux locaux.

L’intégration au réseau virtuel offre aux services Azure les avantages de l’isolement réseau et peut être accomplie à l’aide d’une ou plusieurs des méthodes suivantes :
- [Déploiement d’instances dédiées du service dans un réseau virtuel](virtual-network-for-azure-services.md). Les services sont alors accessibles de manière privée dans le réseau virtuel, et à partir des réseaux locaux.
- Utilisation d’un [point de terminaison privé](../private-link/private-endpoint-overview.md) qui vous permet de vous connecter de façon privée et sécurisée à un service basé sur [Azure Private Link](../private-link/private-link-overview.md). Le point de terminaison privé utilise une adresse IP privée de votre réseau virtuel, plaçant de fait le service dans votre réseau virtuel.
- Accès au service à l’aide de points de terminaison publics en étendant un réseau virtuel au service par le biais de [points de terminaison de service](virtual-network-service-endpoints-overview.md). Les points de terminaison de service permettent de sécuriser les ressources de service au sein du réseau virtuel.
- Utilisation d’[étiquettes de service](service-tags-overview.md) pour autoriser ou refuser le trafic vers vos ressources Azure vers et depuis des points de terminaison IP publics.

## <a name="deploy-dedicated-azure-services-into-virtual-networks"></a>Déployer des services Azure dédiés dans des réseaux virtuels

Quand vous déployez des services Azure dédiés sur un réseau virtuel, vous pouvez communiquer avec les ressources de service de manière privée, par le biais d’adresses IP privées.

![Déployer des services Azure dédiés dans des réseaux virtuels](./media/virtual-network-for-azure-services/deploy-service-into-vnet.png)

Le déploiement d’un service Azure dédié dans votre réseau virtuel offre les capacités suivantes :
- Les ressources situées sur le réseau virtuel peuvent communiquer entre elles de manière privée, par le biais d’adresses IP privées. Exemple : le transfert direct des données entre HDInsight et SQL Server sur une machine virtuelle, au sein d’un réseau virtuel.
- Les ressources locales peuvent accéder aux ressources d’un réseau virtuel à l’aide d’adresses IP privées par le biais d’une connexion VPN site à site (passerelle VPN) ou ExpressRoute.
- Les réseaux virtuels peuvent être appairés pour permettre aux ressources situées sur les réseaux virtuels de communiquer entre elles, par le biais d’adresses IP privées.
- Les instances de service dans un réseau virtuel sont généralement entièrement managées par le service Azure. Cette gestion inclut la surveillance de l’intégrité des ressources et la mise à l’échelle avec une charge.
- Les instances de service sont déployées dans un sous-réseau d’un réseau virtuel. L’accès au réseau entrant ou sortant pour le sous-réseau doit être ouvert par le biais de groupes de sécurité réseau, selon les recommandations fournies par le service.
- Certains services imposent des restrictions sur le sous-réseau dans lequel ils sont déployés. Ces restrictions limitent l’application de stratégies, d’itinéraires, ou la combinaison de machines virtuelles et de ressources du service au sein d’un même sous-réseau. Vérifiez sur chaque service les restrictions spécifiques, car elles peuvent changer au fil du temps. Azure NetApp Files, Dedicated HSM, Azure Container Instances, App Service sont des exemples de ces services.
- Les services peuvent éventuellement nécessiter un sous-réseau délégué comme identificateur explicit qu’un sous-réseau peut héberger un service particulier. Grâce à la délégation, les services obtiennent des autorisations explicites pour créer pour créer des ressources propres au service dans le sous-réseau délégué.
- Consultez un exemple de réponse de l’API REST sur un réseau virtuel avec un sous-réseau délégué. Une liste complète de services qui utilisent le modèle de sous-réseau délégué peut être obtenue via l’API Available Delegations (Délégations disponibles).

Pour obtenir la liste des services qui peuvent être déployés dans un réseau virtuel, consultez [Déployer des services Azure dédiés dans des réseaux virtuels](virtual-network-for-azure-services.md).

## <a name="private-link-and-private-endpoints"></a>Liaison privée et points de terminaison privés

Les points de terminaison privés autorisent l’entrée de trafic à partir de votre réseau virtuel vers une ressource Azure en toute sécurité. Cette liaison privée est établie sans qu’il soit nécessaire d’utiliser des adresses IP publiques. Un point de terminaison privé est une interface réseau spéciale pour un service Azure dans votre réseau virtuel. Lorsque vous créez un point de terminaison privé pour votre ressource, il offre une connectivité sécurisée entre les clients de votre réseau virtuel et votre ressource Azure. Une adresse IP est attribuée au point de terminaison privé à partir de la plage d’adresses IP de votre réseau virtuel. La connexion entre le point de terminaison privé et le service Azure utilise une liaison privée.

La partie droite du diagramme affiche une base de données Azure SQL Database en tant que service PaaS cible. La cible peut être [n’importe quel service qui prend en charge les points de terminaison privés](../private-link/availability.md). Il existe plusieurs instances de SQL Server logique pour plusieurs clients, qui sont toutes accessibles via des adresses IP publiques.

Dans ce cas, une instance de SQL Server logique est exposée avec un point de terminaison privé. Le point de terminaison rend SQL Server accessible via une adresse IP privée dans le réseau virtuel du client. En raison de la modification de la configuration DNS, l’application cliente envoie maintenant son trafic directement à ce point de terminaison privé. Le service cible verra le trafic provenant d’une adresse IP privée du réseau virtuel. 

La liaison privée est représentée par la flèche verte. Une adresse IP publique peut encore _exister_ pour la ressource cible en même temps que le point de terminaison privé. L’adresse IP publique n’est plus utilisée par l’application cliente. Le pare-feu peut désormais interdire tout accès à cette adresse IP publique, ce qui la rend accessible _uniquement_ via des points de terminaison privés. Les connexions à un serveur SQL sans point de terminaison privé du réseau virtuel sont toujours issues d’une adresse IP publique. Ce flux est représenté par la flèche bleue.

![Points de terminaison privés](./media/network-isolation/architecture-private-endpoints.png)

L’application cliente utilise généralement un nom d’hôte DNS pour atteindre le service cible. Aucune modification n’est requise pour l’application. [La résolution DNS dans le réseau virtuel doit être configurée](../private-link/private-endpoint-dns.md) pour résoudre le même nom d’hôte à l’adresse IP privée de la ressource cible au lieu de l’adresse IP publique d’origine. Avec un chemin d’accès privé entre le client et le service cible, le client ne repose pas sur l’adresse IP publique. Le service cible peut désactiver l’accès public.

Cette exposition d’instances individuelles vous permet d'[éviter le vol de données](../private-link/private-endpoint-overview.md#network-security-of-private-endpoints). Un acteur malveillant ne peut pas collecter des informations de la base de données ni les charger dans une autre base de données publique ou un autre compte de stockage. Vous pouvez empêcher l’accès aux adresses IP publiques de _tous_ les services PaaS. Vous pouvez toujours autoriser l’accès aux instances PaaS via leurs points de terminaison privés.

Pour plus d’informations sur Private Link et pour obtenir la liste des services Azure pris en charge, consultez [Qu’est-ce que Private Link ?](../private-link/private-link-overview.md)

## <a name="service-endpoints"></a>Points de terminaison de service

Les points de terminaison de service fournissent une connectivité directe et sécurisée aux services Azure via le réseau principal Azure. Les points de terminaison permettent de sécuriser vos ressources Azure critiques pour vos réseaux virtuels uniquement. Les points de terminaison de service permettent aux adresses IP privées dans le réseau virtuel d’atteindre un service Azure sans avoir besoin d’une adresse IP publique sortante.

Sans point de terminaison de service, il peut être difficile de limiter l’accès à votre réseau virtuel uniquement. L’adresse IP source peut être modifiée ou partagée avec d’autres clients. Par exemple, les services PaaS avec des adresses IP sortantes partagées. Avec les points de terminaison de service, l’adresse IP source que le service cible voit devient une adresse IP privée de votre réseau virtuel. Cette modification du trafic entrant permet d’identifier facilement l’origine et de l’utiliser pour configurer des règles de pare-feu appropriées. Par exemple, en autorisant uniquement le trafic à partir d’un sous-réseau spécifique au sein de ce réseau virtuel.

Avec les points de terminaison de service, les entrées DNS pour les services Azure ne sont pas modifiées et continuent de résoudre les adresses IP publiques attribuées au service Azure.

Dans le diagramme ci-dessous, le côté droit est le même service PaaS cible. Sur la gauche se trouve un réseau virtuel client avec deux sous-réseaux : le sous-réseau A, qui a un point de terminaison de service vers `Microsoft.Sql`, et le sous-réseau B, qui n’a aucun point de terminaison de service défini. 

Lorsqu’une ressource du sous-réseau B tente d’atteindre un serveur SQL Server, elle utilise une adresse IP publique pour les communications sortantes. Ce trafic est représenté par la flèche bleue. Le pare-feu SQL Server doit utiliser cette adresse IP publique pour autoriser ou bloquer le trafic réseau. 

Lorsqu’une ressource du sous-réseau A tente d’atteindre un serveur de base de données, elle est considérée comme une adresse IP privée à partir du réseau virtuel. Ce trafic est représenté par les flèches vertes. Le pare-feu SQL Server peut désormais autoriser ou bloquer spécifiquement le sous-réseau A. Il n’est pas nécessaire de connaître l’adresse IP publique du service source.

![Points de terminaison de service](./media/network-isolation/architecture-service-endpoints.png)

Les points de terminaison de service s’appliquent à **toutes** les instances du service cible. Par exemple, **toutes** les instances de SQL Server de clients Azure, pas seulement l’instance du client.

Pour plus d’informations, consultez [Points de terminaison de service de réseau virtuel](virtual-network-service-endpoints-overview.md).

## <a name="service-tags"></a>Balises de service

Une balise de service représente un groupe de préfixes d’adresses IP d’un service Azure donné. Avec des étiquettes de service, vous pouvez définir des contrôles d’accès réseau sur des [groupes de sécurité réseau](./network-security-groups-overview.md#security-rules) ou le [Pare-feu Azure](../firewall/service-tags.md). Vous pouvez autoriser ou refuser le trafic pour le service. Pour autoriser ou refuser le trafic, spécifiez l’étiquette de service dans le champ source ou de destination d’une règle. 

![Autoriser ou refuser le trafic à l’aide d’étiquettes de service](./media/network-isolation/service-tags.png)

Isoler le réseau et protéger vos ressources Azure d’Internet lorsque vous accédez aux services Azure qui ont des points de terminaison publics. Créez des règles de groupe de sécurité réseau entrant/sortant pour refuser le trafic vers et depuis **Internet** et autoriser le trafic vers/à partir d’**AzureCloud**. Pour en savoir plus sur les étiquettes de service, consultez [Étiquettes de service disponibles](service-tags-overview.md#available-service-tags) pour des services Azure spécifiques.

Pour plus d’informations sur les étiquettes de service et les services Azure qui les prennent en charge, consultez [Vue d’ensemble des étiquettes de service](service-tags-overview.md).

## <a name="compare-private-endpoints-and-service-endpoints"></a>Comparer des points de terminaison privés et des points de terminaison de service

Plutôt que d’examiner uniquement leurs différences, il convient de souligner que les points de terminaison de service et les points de terminaison privés ont des caractéristiques communes.

Ces deux fonctionnalités sont utilisées pour un contrôle plus granulaire du pare-feu sur le service cible. Par exemple, pour limiter l’accès aux bases de données SQL Server ou aux comptes de stockage. Toutefois, l’opération est différente pour les deux, comme indiqué plus en détail dans les sections précédentes.

Les deux approches résolvent le problème d'[Insuffisance de ports SNAT (Network Address Translation) sources](../load-balancer/load-balancer-outbound-connections.md#scenarios). Vous risquez une insuffisance lorsque vous tunnelisez le trafic via une appliance virtuelle réseau ou un service avec des limitations de port SNAT. Lorsque vous utilisez des points de terminaison de service ou des points de terminaison privés, le trafic prend directement un chemin d’accès optimisé vers le service cible. Les deux approches peuvent tirer parti des applications gourmandes en bande passante, car la latence et le coût sont réduits.

Dans les deux cas, vous pouvez toujours vous assurer que le trafic dans le service cible traverse un pare-feu réseau ou appliance virtuelle réseau. Cette procédure est différente pour les deux approches. Lorsque vous utilisez des points de terminaison de service, vous devez configurer le point de terminaison de service sur le sous-réseau du **pare-feu**, plutôt que sur le sous-réseau où le service source est déployé. Lorsque vous utilisez des points de terminaison privés, vous placez un itinéraire défini par l’utilisateur (UDR) pour l’adresse IP du point de terminaison privé sur le sous-réseau **source**. Pas dans le sous-réseau du point de terminaison privé.

| Considération                                                                                                                                    | Points de terminaison de service                                                                                                           | Points de terminaison privés                                                                                                                                                 |
| ------------------------------------------------------------------------------------------------------------------------------------------------ | --------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Portée de la configuration                                                                                                   | Totalité du service (par exemple, _tous_ les serveurs SQL ou les comptes de stockage de _tous_ les clients)                                      | Instance individuelle (par exemple, une instance de SQL Server spécifique ou un compte de stockage que _vous_ possédez)                                                                    |
| Le trafic Azure vers Azure reste sur le réseau principal Azure                                                                                       | Oui                                                                                                                         | Oui                                                                                                                                                               |
| Le service peut désactiver son adresse IP publique                                                                                                        | Non                                                                                                                          | Oui                                                                                                                                                               |
| Le service peut être atteint sans utiliser d’adresse IP publique                                                                                       | Non                                                                                                                          | Oui                                                                                                                                                               |
| Vous pouvez facilement limiter le trafic provenant d’un réseau virtuel Azure                                                                             | Oui (autoriser l’accès à partir de sous-réseaux spécifiques et ou utiliser des groupes de sécurité réseau)                                                                   | Non*                                                                                                                                                               |
| Vous pouvez facilement limiter le trafic de provenance locale (VPN/ExpressRoute)                                                                           | N/A**                                                                                                                       | Non*                                                                                                                                                               |
| Requiert des modifications DNS                                                                                                                             | Non                                                                                                                          | Oui (consultez [Configuration DNS](../private-link/private-endpoint-dns.md))                                                                 |
| Influence le coût de votre solution                                                                                                                | Non                                                                                                                          | Oui (consultez [Tarification de Private Link](https://azure.microsoft.com/pricing/details/private-link/))                                                                       |
| Influence le [contrat SLA composite](/azure/architecture/framework/resiliency/business-metrics#composite-slas) de votre solution | Non                                                                                                                          | Oui (le service de liaison privée dispose d’un [contrat SLA de 99,99 %](https://azure.microsoft.com/support/legal/sla/private-link/))                                                 |

*Tout ce qui a une ligne de visée réseau sur le point de terminaison privé bénéficie d’un accès au niveau du réseau. Cet accès ne peut pas être contrôlé par un groupe de sécurité réseau sur le point de terminaison privé proprement dit.

**Les ressources du service Azure sécurisées pour des réseaux virtuels ne sont pas accessibles à partir des réseaux locaux. Si vous souhaitez autoriser le trafic depuis un réseau local, autorisez également les adresses IP publiques (généralement NAT) à partir de vos circuits locaux ou ExpressRoute. Ces adresses IP peuvent être ajoutées via la configuration du pare-feu IP des ressources du service Azure. Pour plus d’informations, consultez le [FAQ sur les réseaux virtuels](virtual-networks-faq.md#can-an-on-premises-devices-ip-address-that-is-connected-through-azure-virtual-network-gateway-vpn-or-expressroute-gateway-access-azure-paas-service-over-vnet-service-endpoints).

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [intégrer votre application à un réseau Azure](../app-service/web-sites-integrate-with-vnet.md).
- Découvrez comment [restreindre l’accès aux ressources à l’aide d’étiquettes de service](tutorial-restrict-network-access-to-resources.md).
- Découvrez comment [vous connecter en privé à un compte Azure Cosmos à l’aide d’Azure Private Link](../private-link/tutorial-private-endpoint-cosmosdb-portal.md).