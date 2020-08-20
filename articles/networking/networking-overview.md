---
title: Vue d’ensemble des services de mise en réseau Azure
description: 'Découvrez les services de mise en réseau dans Azure : services de connectivité, services de protection des applications, services de distribution des applications et de surveillance réseau.'
services: networking
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.workload: infrastructure-services
ms.date: 03/12/2020
ms.author: kumud
ms.openlocfilehash: 1110199a8c02b29d70f0c12e1ed1a6341c44e403
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/10/2020
ms.locfileid: "88037250"
---
# <a name="azure-networking-services-overview"></a>Vue d’ensemble des services de mise en réseau Azure

Les services de mise en réseau dans Azure fournissent un éventail de fonctionnalités de mise en réseau qui peuvent être utilisées ensemble ou séparément. Cliquez sur une des fonctionnalités principales suivantes pour en savoir plus à leur sujet :
- [**Services de connectivité**](#connect) : Connectez les ressources Azure et les ressources locales à l’aide d’un de ces services de mise en réseau dans Azure – réseau virtuel (VNet), Virtual WAN, ExpressRoute, passerelle VPN, passerelle NAT de réseau virtuel, Azure DNS, Peering Service et Azure Bastion – ou d’une combinaison de ces derniers.
- [**Services de protection des applications**](#protect) : Protégez vos applications à l’aide d’un ou plusieurs de ces services de mise en réseau dans Azure – Private Link, protection DDoS, pare-feu, groupes de sécurité réseau, pare-feu d’applications Web et points de terminaison de réseau virtuel.
- [**Services de distribution d’applications**](#deliver) : Fournissez des applications dans le réseau Azure à l’aide d’un ou plusieurs de ces services de mise en réseau dans Azure – Content Delivery Network (CDN), Azure Front Door Service, Traffic Manager, Application Gateway, Internet Analyzer et Load Balancer.
- [**Surveillance du réseau**](#monitor) : Surveillez vos ressources réseau à l’aide d’un ou plusieurs de ces services de mise en réseau dans Azure – Network Watcher, ExpressRoute Monitor, Azure Monitor ou point d’accès terminal de réseau virtuel (TAP).

## <a name="connectivity-services"></a><a name="connect"></a>Services de connectivité
 
Cette section décrit les services qui assurent la connectivité entre les ressources Azure, la connectivité à partir d’un réseau local vers des ressources Azure et la connectivité d’une branche à l’autre dans Azure : réseau virtuel, Virtual WAN, ExpressRoute, passerelle VPN, passerelle NAT de réseau virtuel, Azure DNS, Azure Peering Service et Azure Bastion.

|Service|Pourquoi l'utiliser ?|Scénarios|
|---|---|---|
|[Réseau virtuel](#vnet)|Permet à de nombreuses ressources, telles que les machines virtuelles de communiquer en toute sécurité entre elles, avec Internet et avec les réseaux locaux.| <p>[Filtrer le trafic réseau](../virtual-network/tutorial-filter-network-traffic.md)</p> <p>[Router le trafic réseau](../virtual-network/tutorial-create-route-table-portal.md)</p> <p>[Restreindre l’accès réseau aux ressources](../virtual-network/tutorial-restrict-network-access-to-resources.md)</p> <p>[Connecter des réseaux virtuels](../virtual-network/tutorial-connect-virtual-networks-portal.md)</p>|
|[ExpressRoute](#expressroute)|Vous permet d’étendre vos réseaux locaux au cloud de Microsoft via une connexion privée assurée par un fournisseur de connectivité.|<p>[Création et modification d’un circuit ExpressRoute](../expressroute/expressroute-howto-circuit-portal-resource-manager.md)</p> <p>[Créer et modifier le Peering pour un circuit ExpressRoute](../expressroute/expressroute-howto-routing-portal-resource-manager.md)</p> <p>[Liaison d’un réseau virtuel à un circuit ExpressRoute](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md)</p> <p>[Configurer et gérer des filtres de routage pour les circuits ExpressRoute](../expressroute/how-to-routefilter-portal.md)</p>|
|[Passerelle VPN](#vpngateway)|Envoie du trafic chiffré entre un réseau virtuel Azure et un emplacement local via l’Internet public.|<p>[Connexions de site à site](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)</p> <p>[Connexions de réseau virtuel à réseau virtuel](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)</p> <p>[Connexions point à site](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md)</p>|
|[Virtual WAN](#virtualwan)|Optimise et automatise la connectivité des branches vers et via Azure. Les régions Azure servent de hubs auxquels vous pouvez connecter vos branches.|<p>[Connexions de site à site](../virtual-wan/virtual-wan-site-to-site-portal.md), [Connexions ExpressRoute](../virtual-wan/virtual-wan-expressroute-portal.md)</p>|
|[DNS Azure](#dns)|Héberge le domaine DNS qui fournit une résolution de noms à l’aide de l’infrastructure Microsoft Azure.|<p>[Héberger votre domaine dans Azure DNS](../dns/dns-delegate-domain-azure-dns.md)</p><p>[Créer des enregistrements DNS pour une application web](../dns/dns-web-sites-custom-domain.md)</p> <p>[Créer un enregistrement d’alias pour Traffic Manager](../dns/tutorial-alias-tm.md)</p> <p>[Créer un enregistrement d’alias pour l’adresse IP publique](../dns/tutorial-alias-pip.md)</p> <p>[Créer un enregistrement d’alias pour l’enregistrement de la ressource de zone](../dns/tutorial-alias-rr.md)</p>|
|[Azure Bastion](#bastion)|Configurez une connectivité RDP/SSH sécurisée et fluide à vos machines virtuelles directement dans le portail Azure via TLS. Lorsque vous vous connectez via Azure Bastion, vos machines virtuelles n’ont pas besoin d’une adresse IP publique.|<p>[Créer un hôte Azure Bastion](../bastion/bastion-create-host-portal.md)</p><p>[Se connecter à une machine virtuelle Linux avec SSH](../bastion/bastion-connect-vm-ssh.md)</p><p>[Se connecter à une machine virtuelle Windows avec RDP](../bastion/bastion-connect-vm-rdp.md)</p>|
|[Passerelle NAT de réseau virtuel](#nat)|Créez une passerelle NAT pour fournir une connectivité sortante à une machine virtuelle.|<p>[Créer une passerelle NAT](../virtual-network/quickstart-create-nat-gateway-portal.md)</p>|
|[Azure Peering Service (préversion)](#azurepeeringservice)|Collaborez avec des fournisseurs de services pour un routage fiable et optimal vers le cloud Microsoft sur le réseau public.|<p>[Inscrire Azure Peering Service](../peering-service/azure-portal.md)</p>|
||||


### <a name="virtual-network"></a><a name="vnet"></a>Réseau virtuel

Le réseau virtuel Azure (VNet) est le bloc de construction fondamental pour votre réseau privé dans Azure. Vous pouvez utiliser des réseaux virtuels pour :
- **Communiquer entre les ressources Azure** : vous pouvez déployer des machines virtuelles et plusieurs autres types de ressources Azure sur un réseau virtuel, comme des environnements Azure App Service, Azure Kubernetes Service et des groupes de machines virtuelles identiques Azure. Pour obtenir la liste complète des ressources Azure que vous pouvez déployer sur un réseau virtuel, consultez [Intégration des services de réseau virtuel](../virtual-network/virtual-network-for-azure-services.md).
- **Communiquer mutuellement** : Vous pouvez connecter des réseaux virtuels entre eux, ce qui permet aux ressources de ces réseaux virtuels de communiquer entre eux à l’aide d’un peering. Les réseaux virtuels que vous connectez peuvent être situés dans des régions Azure identiques ou différentes. Pour en savoir plus, consultez [Peering de réseaux virtuels](../virtual-network/virtual-network-peering-overview.md).
- **Communiquer vers Internet** : Par défaut, toutes les ressources d’un réseau virtuel peuvent communiquer en sortie vers Internet. Vous pouvez effectuer des communications entrantes vers une ressource en lui assignant une adresse IP publique ou un équilibreur de charge publique. Vous pouvez également utiliser des [adresses IP publique](../virtual-network/virtual-network-public-ip-address.md) ou [Load Balancer](../load-balancer/load-balancer-overview.md) pour gérer vos connexions sortantes.
- **Communiquer avec des réseaux locaux** : Vous pouvez connecter vos ordinateurs et réseaux locaux à un réseau virtuel à l’aide la [passerelle VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) ou d’[ExpressRoute](../expressroute/expressroute-introduction.md).

Pour plus d’informations, consultez [Présentation du réseau virtuel Azure](../virtual-network/virtual-networks-overview.md).

### <a name="expressroute"></a><a name="expressroute"></a>ExpressRoute
ExpressRoute vous permet d’étendre vos réseaux locaux dans le cloud Microsoft via une connexion privée assurée par un fournisseur de connectivité. Cette connexion est privée. Toutefois, le trafic ne passe pas par Internet. Grâce à ExpressRoute, vous pouvez établir des connexions aux services de cloud computing Microsoft, comme Microsoft Azure, Office 365 et Dynamics 365.  Pour plus d’informations, consultez [Présentation d’ExpressRoute](../expressroute/expressroute-introduction.md).

![Azure ExpressRoute](./media/networking-overview/expressroute-connection-overview.png)

### <a name="vpn-gateway"></a><a name="vpngateway"></a>Passerelle VPN
La passerelle VPN vous aide à créer des connexions chiffrées intersites vers votre réseau virtuel à partir d’emplacements locaux, ou à créer des connexions chiffrées entre des réseaux virtuels. Différentes configurations sont disponibles pour les connexions de passerelle VPN, notamment de site à site, de point à site ou de réseau virtuel à réseau virtuel.
Le diagramme suivant illustre plusieurs connexions VPN de site à site au même réseau virtuel.

![Connexions de passerelle VPN Azure de site à site](./media/networking-overview/vpngateway-multisite-connection-diagram.png)

Pour plus d’informations sur les différents types de connexions VPN, consultez [Passerelle VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md).

### <a name="virtual-wan"></a><a name="virtualwan"></a>Virtual WAN
Azure Virtual WAN est un service réseau qui offre une connectivité de branche optimisée et automatisée via Azure. Les régions Azure servent de hubs auxquels vous pouvez connecter vos branches. Vous pouvez exploiter la dorsale principale d’Azure pour également connecter des branches et profiter d’une connectivité de branche à réseau virtuel. Azure Virtual WAN regroupe plusieurs services de connectivité cloud Azure comme le VPN site à site, ExpressRoute et le VPN utilisateur point à site dans une même interface opérationnelle. La connectivité aux réseaux virtuels Azure est établie à l’aide de connexions de réseau virtuel. Pour plus d’informations, consultez [Présentation du réseau WAN virtuel](../virtual-wan/virtual-wan-about.md).

![Diagramme WAN virtuel](./media/networking-overview/virtualwan1.png)

### <a name="azure-dns"></a><a name="dns"></a>Azure DNS
Azure DNS est un service d’hébergement pour les domaines DNS qui offre une résolution de noms à l’aide de l’infrastructure Microsoft Azure. En hébergeant vos domaines dans Azure, vous pouvez gérer vos enregistrements DNS à l’aide des mêmes informations d’identification, les mêmes API, les mêmes outils et la même facturation que vos autres services Azure. Pour plus d’informations, consultez [Présentation d’Azure DNS](../dns/dns-overview.md).

### <a name="azure-bastion"></a><a name="bastion"></a>Azure Bastion
Le service Azure Bastion est un nouveau service PaaS complètement managé par la plateforme que vous provisionnez au sein de votre réseau virtuel. Il fournit une connectivité RDP/SSH sécurisée et fluide à vos machines virtuelles, directement dans le portail Azure via TLS. Lorsque vous vous connectez via Azure Bastion, vos machines virtuelles n’ont pas besoin d’une adresse IP publique. Pour plus d’informations, consultez [Présentation d’Azure Bastion](../bastion/bastion-overview.md).

![Architecture d’Azure Bastion](./media/networking-overview/architecture.png)

### <a name="virtual-network-nat-gateway"></a><a name="nat"></a>Passerelle NAT de réseau virtuel
Le service NAT (traduction d’adresses réseau) de Réseau virtuel simplifie la connectivité Internet sortante uniquement pour les réseaux virtuels. Quand il est configuré sur un sous-réseau, toute la connectivité sortante utilise vos adresses IP publiques statiques spécifiées. Une connectivité sortante est possible sans équilibreur de charge ni adresses IP publiques directement attachées aux machines virtuelles. Pour plus d’informations, consultez [Qu’est-ce qu’une passerelle NAT de réseau virtuel ?](../virtual-network/nat-overview.md) 

![Passerelle NAT de réseau virtuel](./media/networking-overview/flow-map.png)

### <a name="azure-peering-service"></a><a name="azurepeeringservice"></a> Azure Peering Service
Azure Peering Service améliore la connectivité du client aux services cloud de Microsoft tels qu’Office 365, Dynamics 365, les services SaaS, Azure ou les services Microsoft accessibles via le réseau Internet public. Pour plus d’informations, consultez [Qu’est-ce qu’Azure Peering Service ?](../peering-service/about.md).

## <a name="application-protection-services"></a><a name="protect"></a>Services de protection des applications

Cette section décrit les services de protection qui protègent vos ressources réseau : protègent vos applications à l’aide d’un de ces services de mise en réseau dans Azure – Private Link, protection DDoS, pare-feu, groupes de sécurité réseau, pare-feu d’applications Web et points de terminaison de réseaux virtuels – ou d’une combinaison de ces derniers.

|Service|Pourquoi l'utiliser ?|Scénario|
|---|---|---|
|[Protection DDOS](#ddosprotection) |Haute disponibilité pour vos applications avec protection contre les frais excessifs de trafic IP|[Gérer Azure DDoS Protection](../virtual-network/manage-ddos-protection.md)|
|[Pare-feu d’applications web](#waf)|<p>[Pare-feu d'applications web Azure avec Application Gateway](../web-application-firewall/ag/ag-overview.md) fournit une protection régionale aux entités dans un espace d’adressage public et privé.</p><p>[Pare-feu d'applications web Azure avec Front Door](../web-application-firewall/afds/afds-overview.md) offre une protection à la périphérie du réseau vers les points de terminaison publics.</p>|<p>[Configurer les règles de protection bot](../frontdoor/waf-front-door-policy-configure-bot-protection.md)</p> <p>[Configurer un code de réponse personnalisé](../frontdoor/waf-front-door-configure-custom-response-code.md)</p> <p>[Configurer les règles de restriction IP](../frontdoor/waf-front-door-configure-ip-restriction.md)</p> <p>[Configurer une règle de limite de débit](../frontdoor/waf-front-door-rate-limit-powershell.md)</p> |
|[Pare-feu Azure](#firewall)|Pare-feu Azure est un service de sécurité réseau informatique géré qui protège vos ressources Réseau virtuel Azure. Il s’agit d’un service de pare-feu avec état intégral, doté d’une haute disponibilité intégrée et d’une scalabilité illimitée dans le cloud.|<p>[Déployer un pare-feu Azure dans un réseau virtuel](../firewall/tutorial-firewall-deploy-portal.md)</p> <p>[- Déployer un pare-feu Azure dans un réseau hybride](../firewall/tutorial-hybrid-ps.md)</p> <p>[Filtrer le trafic entrant avec le pare-feu DNAT](../firewall/tutorial-firewall-dnat.md)</p>|
|[Groupes de sécurité réseau](#nsg)|Contrôle complet du nœud de terminaison granulaire distribué sur la machine virtuelle/le sous-réseau pour tous les flux de trafic réseau|[Filtrer le trafic réseau avec des groupes de sécurité réseau](../virtual-network/tutorial-filter-network-traffic.md)|
|[Points de terminaison de service de réseau virtuel](#serviceendpoints)|Permettent de restreindre l’accès réseau à certaines ressources du service Azure en n’autorisant leur accès qu’à partir d’un sous-réseau du réseau virtuel.|[Restreindre l’accès réseau aux ressources PaaS](../virtual-network/tutorial-restrict-network-access-to-resources-powershell.md)|
[Liaison privée](#privatelink)|Vous permet d’accéder aux services Azure PaaS (par exemple Stockage Azure et SQL Database) ainsi qu’aux services de partenaires ou de clients hébergés par Azure sur un point de terminaison privé dans votre réseau virtuel.|<p>[Créer un point de terminaison privé](../private-link/create-private-endpoint-portal.md)</p><p>[Créer un service Private Link](../private-link/create-private-link-service-portal.md)</p>|
|||
### <a name="ddos-protection"></a><a name="ddosprotection"></a>DDoS Protection 
[Azure DDoS Protection](../virtual-network/manage-ddos-protection.md) fournit des mesures contre les menaces DDoS les plus sophistiquées. Le service fournit des fonctionnalités améliorées d’atténuation des attaques DDoS pour votre application et les ressources déployées dans vos réseaux virtuels. Par ailleurs, les clients qui utilisent Azure DDoS Protection ont accès à la prise en charge de la réponse rapide DDoS pour faire appel à des experts DDoS pendant une attaque active.

![Protection DDOS](./media/networking-overview/ddos-protection.png)

### <a name="web-application-firewall"></a><a name="waf"></a>Pare-feu d’applications web

Le [pare-feu d’applications web Azure](../web-application-firewall/overview.md) (WAF) offre une protection de vos applications web contre les codes malveillants exploitant une faille de sécurité et les vulnérabilités telles que les injections de code SQL et le script de site à site. Le pare-feu d'applications web Azure fournit une protection prête à l’emploi contre les 10 principales vulnérabilités établies par OWASP par le biais de règles managées. Les clients peuvent également configurer des règles personnalisées qu’ils gèrent pour offrir une protection supplémentaire basée sur la plage d’adresses IP sources et sur des attributs de requêtes tels que les en-têtes, les cookies, les champs de données de formulaires ou les paramètres de chaînes de requête.

Les clients peuvent choisir de déployer le [pare-feu d'applications web Azure avec Application Gateway](../application-gateway/waf-overview.md), qui offre une protection régionale aux entités dans un espace d’adressage public et privé. Ils peuvent également choisir de déployer le [pare-feu d'applications web Azure avec Front Door](../frontdoor/waf-overview.md), qui offre une protection à la périphérie du réseau vers les points de terminaison publics.

![Pare-feu d’applications web](./media/networking-overview/waf-overview.png)


### <a name="azure-firewall"></a><a name="firewall"></a>Pare-feu Azure
Pare-feu Azure est un service de sécurité réseau informatique géré qui protège vos ressources Réseau virtuel Azure. Avec le pare-feu Azure, vous pouvez créer, appliquer et consigner des stratégies de connectivité réseau et d’application de façon centralisée entre les abonnements et les réseaux virtuels. Pare-feu Azure utilise une adresse IP publique statique pour vos ressources de réseau virtuel, ce qui permet aux pare-feu situés à l’extérieur d’identifier le trafic provenant de votre réseau virtuel. 

Pour plus d’informations sur le pare-feu Azure, consultez la [documentation du pare-feu Azure](../firewall/overview.md).

![Présentation du pare-feu](./media/networking-overview/firewall-threat.png)

### <a name="network-security-groups"></a><a name="nsg"></a>Groupes de sécurité réseau
Vous pouvez filtrer le trafic réseau depuis et vers les ressources Azure dans un réseau virtuel Azure avec un groupe de sécurité réseau. Pour plus d’informations, consultez [Vue d’ensemble de la sécurité](../virtual-network/security-overview.md).

### <a name="service-endpoints"></a><a name="serviceendpoints"></a>Points de terminaison de service
Les points de terminaison de service de réseau virtuel étendent votre espace d’adressage privé de réseau virtuel et l’identité de votre réseau virtuel aux services Azure, via une connexion directe. Les points de terminaison permettent de sécuriser vos ressources critiques du service Azure pour vos réseaux virtuels uniquement. Le trafic à partir de votre réseau virtuel vers le service Azure reste toujours sur le réseau principal de Microsoft Azure. Pour plus d’informations, consultez [Points de terminaison de service de réseau virtuel](../virtual-network/virtual-network-service-endpoints-overview.md).

![Points de terminaison de service de réseau virtuel](./media/networking-overview/vnet-service-endpoints-overview.png)

### <a name="azure-private-link"></a><a name="privatelink"></a>Azure Private Link
[Azure Private Link](../private-link/private-link-overview.md) vous permet d’accéder aux services Azure PaaS (par exemple Stockage Azure et SQL Database) ainsi qu’aux services de partenaires ou de clients hébergés par Azure sur un point de terminaison privé dans votre réseau virtuel.
Le trafic entre votre réseau virtuel et le service transite par le réseau principal de Microsoft. L’exposition de votre service à l’Internet public n’est plus nécessaire. Vous pouvez créer votre propre service de liaison privée dans votre réseau virtuel et le distribuer à vos clients.

![Vue d’ensemble du point de terminaison privé](./media/networking-overview/private-endpoint.png)


## <a name="application-delivery-services"></a><a name="deliver"></a>Services de livraison d’applications

Cette section décrit les services de mise en réseau dans Azure qui permettent de fournir des applications : Content Delivery Network, Azure Front Door Service, Traffic Manager, Load Balancer et Application Gateway.

|Service|Pourquoi l'utiliser ?|Scénario|
|---|---|---|
|[Content Delivery Network](#cdn)|Fournit du contenu à bande passante élevée aux utilisateurs. Les CDN stockent le contenu en cache sur des serveurs Edge dans un point de présente (POP) proches des utilisateurs finaux afin de réduire la latence.|<p>[Ajouter CDN à une application web](../cdn/cdn-add-to-web-app.md)</p> <p>[- Accéder aux objets blob de stockage à l’aide d’un domaine personnalisé Azure CDN via HTTPS](..//cdn/cdn-storage-custom-domain-https.md)</p> <p>[Ajouter un domaine personnalisé à votre point de terminaison Azure CDN](../cdn/cdn-map-content-to-custom-domain.md)</p> <p>[Configurer HTTPS sur un domaine personnalisé Azure CDN](../cdn/cdn-custom-ssl.md?tabs=option-1-default-enable-https-with-a-cdn-managed-certificate)</p>|
|[Azure Front Door Service](#frontdoor)|Vous permet de définir, de gérer et de superviser le routage global de votre trafic web en privilégiant l’optimisation des performances et le basculement instantané global à des fins de haute disponibilité.|<p>[Ajouter un domaine personnalisé à votre Azure Front Door Service](../frontdoor/front-door-custom-domain.md)</p> <p>[Configurer HTTPS sur un domaine personnalisé Front Door](../frontdoor/front-door-custom-domain-https.md)</p><p>[Configurer la stratégie de géofiltrage du pare-feu d'applications web](../frontdoor/front-door-tutorial-geo-filtering.md)|
|[Traffic Manager](#trafficmanager)|Distribue le trafic basé sur DNS aux services dans les régions Azure du monde entier, tout en offrant une disponibilité et une adaptabilité élevées|<p> [Acheminer le trafic pour une latence faible](../traffic-manager/tutorial-traffic-manager-improve-website-response.md)</p><p>[Acheminer le trafic vers un point de terminaison prioritaire](../traffic-manager/traffic-manager-configure-priority-routing-method.md)</p><p> [Contrôler le trafic avec des points de terminaison pondérés](../traffic-manager/tutorial-traffic-manager-weighted-endpoint-routing.md)</p><p>[Acheminer le trafic en fonction de l’emplacement géographique du point de terminaison](../traffic-manager/traffic-manager-configure-geographic-routing-method.md)</p> <p> [Acheminer le trafic en fonction du sous-réseau de l’utilisateur](../traffic-manager/tutorial-traffic-manager-subnet-routing.md)</p>|
|[Équilibreur de charge](#loadbalancer)|Fournit un équilibrage de la charge régional en acheminant le trafic entre les zones de disponibilité et dans vos réseaux virtuels. Fournit un équilibrage de la charge interne en acheminant le trafic entre vos ressources pour créer votre application régionale.|<p> [Équilibrer la charge de trafic internet vers des machines virtuelles](../load-balancer/tutorial-load-balancer-standard-manage-portal.md)</p> <p>[Équilibrer la charge du trafic entre les machines virtuelles à l’intérieur d’un réseau virtuel](../load-balancer/tutorial-load-balancer-standard-internal-portal.md)<p>[Déplacer le trafic vers un port spécifique sur des machines virtuelles spécifiques](../load-balancer/tutorial-load-balancer-port-forwarding-portal.md)</p><p> [Configurer des règles d’équilibrage de charge et des règles de trafic sortant](../load-balancer/configure-load-balancer-outbound-cli.md)</p>|
|[Application Gateway](#applicationgateway)|Azure Application Gateway est un équilibreur de charge du trafic web qui vous permet de gérer le trafic vers vos applications web.|<p>[Diriger le trafic web avec Azure Application Gateway](../application-gateway/quick-create-portal.md)</p><p>[Tutoriel : Configurer une passerelle d’application avec un arrêt TLS à l’aide du portail Azure](../application-gateway/create-ssl-portal.md)</p><p>[Créer une passerelle d’application avec réacheminement par chemin d’URL](../application-gateway/create-url-route-portal.md) </p>|
|

### <a name="content-delivery-network"></a><a name="cdn"></a>Content Delivery Network
Le réseau de diffusion de contenu (CDN) Azure offre aux développeurs une solution globale pour la distribution rapide de contenu haut débit aux utilisateurs en mettant en cache leur contenu sur des nœuds physiques disposés stratégiquement dans le monde entier. Pour plus d’informations sur le CDN Azure, voir la page [Azure Content Delivery Network](../cdn/cdn-overview.md)

![Azure CDN](./media/networking-overview/cdn-overview.png)

### <a name="azure-front-door-service"></a><a name="frontdoor"></a>Service Azure Front Door
Azure Front Door Service vous permet de définir, de gérer et de superviser le routage global de votre trafic web en privilégiant l’optimisation des performances et le basculement instantané global à des fins de haute disponibilité. Avec Front Door, vous pouvez transformer vos applications grand public et professionnelles multirégions en applications, API et contenus modernes fiables, personnalisés et hautes performances bénéficiant avec Azure d’une audience mondiale. Pour plus d’informations, consultez [Azure Front Door](../frontdoor/front-door-overview.md).


### <a name="traffic-manager"></a><a name="trafficmanager"></a>Traffic Manager

Azure Traffic Manager est un équilibreur de charge du trafic DNS qui vous permet de distribuer le trafic de manière optimale aux services dans toutes les régions Azure globales, tout en offrant réactivité et haute disponibilité. Traffic Manager fournit un éventail de méthodes de routage du trafic telles que la priorité, la pondération, les performances, la répartition géographique, la valeur multiple ou le sous-réseau pour distribuer le trafic. Pour plus d’informations sur les différentes méthodes de routage du trafic, consultez [Méthodes de routage de Traffic Manager](../traffic-manager/traffic-manager-routing-methods.md).

Le diagramme suivant montre le routage basé sur la priorité des points de terminaison avec Traffic Manager :

![Méthode de routage du trafic « Priorité » d’Azure Traffic Manager](./media/networking-overview/priority.png)

Pour plus d’informations sur Traffic Manager, consultez [Présentation d’Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md)

### <a name="load-balancer"></a><a name="loadbalancer"></a>Load Balancer
Azure Load Balancer fournit un équilibrage de charge de couche 4 hautement performant et à faible latence pour tous les protocoles UDP et TCP. Il gère les connexions entrantes et sortantes. Vous pouvez configurer des points de terminaison avec équilibrage de charge interne et public. Vous pouvez définir des règles de mappage des connexions entrantes aux destinations du pool principal, en utilisant des options d’analyse d’intégrité TCP et HTTP de façon à gérer la disponibilité du service. Pour en savoir plus sur l’équilibrage de charge, lisez l’article [Présentation de l’équilibreur de charge](../load-balancer/load-balancer-overview.md).

L’illustration suivante montre une application multiniveau sur Internet qui utilise à la fois l’équilibrage de charge interne et externe :

![Exemple avec Load Balancer](./media/networking-overview/load-balancer.png)


### <a name="application-gateway"></a><a name="applicationgateway"></a>Application Gateway
Azure Application Gateway est un équilibreur de charge du trafic web qui vous permet de gérer le trafic vers vos applications web. C’est un contrôleur de livraison d’applications (ADC) sous forme de service qui offre à vos applications plusieurs fonctionnalités d’équilibreur de charge de couche 7. Pour plus d’informations, consultez [Nouveautés d’Azure Application Gateway](../application-gateway/overview.md).

Le diagramme suivant montre le routage d’URL basé sur le chemin avec Application Gateway.

![Exemple avec Application Gateway](./media/networking-overview/figure1-720.png)

## <a name="network-monitoring-services"></a><a name="monitor"></a>Services de surveillance de réseau
Cette section décrit les services de mise en réseau dans Azure qui permettent de surveiller vos ressources réseau : Network Watcher, ExpressRoute Monitor, Azure Monitor et un TAP de réseau virtuel.

|Service|Pourquoi l'utiliser ?|Scénario|
|---|---|---|
|[Network Watcher](#networkwatcher)|Aide à surveiller et à résoudre les problèmes de connectivité, à diagnostiquer les problèmes de VPN, de NSG et de routage, à capturer des paquets sur votre machine virtuelle, à automatiser le déclenchement d’outils de diagnostic à l’aide d’Azure Functions et de Logic Apps|<p>[Diagnostiquer un problème de filtre de trafic sur une machine virtuelle](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md)</p><p>[Diagnostiquer un problème de routage sur une machine virtuelle](../network-watcher/diagnose-vm-network-routing-problem.md)</p><p>[Surveiller les communications entre machines virtuelles](../network-watcher/connection-monitor.md)</p><p>[Diagnostiquer des problèmes de communication entre réseaux](../network-watcher/diagnose-communication-problem-between-networks.md)</p><p>[Consigner le trafic réseau vers et à partir d’une machine virtuelle](../network-watcher/network-watcher-nsg-flow-logging-portal.md)</p>|
|[Moniteur ExpressRoute](#expressroutemonitor)|Fournit une surveillance en temps réel des performances, de la disponibilité et de l’utilisation du réseau, aide à la détection automatique de la topologie du réseau, permet une isolation plus rapide des pannes, détecte les problèmes réseau temporaires et aide à analyser les performances réseau historiques, prend en charge plusieurs abonnements|<p>[Configurer Network Performance Monitor pour ExpressRoute](../expressroute/how-to-npm.md)</p><p>[Surveillance, indicateurs de performance et alertes ExpressRoute](../expressroute/expressroute-monitoring-metrics-alerts.md)</p>|
|[Azure Monitor](#azuremonitor)|Vous aide à comprendre le fonctionnement de vos applications et identifie de façon proactive les problèmes qui les affectent et les ressources dont elles dépendent.|<p>[Indicateurs de performance et alertes Traffic Manager](../traffic-manager/traffic-manager-metrics-alerts.md)</p><p>[Azure surveille les diagnostics pour Standard Load Balancer](../load-balancer/load-balancer-standard-diagnostics.md)</p><p>[Surveiller les journaux et les indicateurs de performance du pare-feu Azure](../firewall/tutorial-diagnostics.md)</p><p>[Supervision et journalisation du pare-feu d’applications web Azure](../frontdoor/waf-front-door-monitor.md)</p>|
|[TAP de réseau virtuel](#vnettap)|Fournit la diffusion en continu du trafic du réseau d’ordinateurs virtuels vers le collecteur de paquets, active les solutions de gestion des performances du réseau et des applications ainsi que les outils d’analyse de sécurité|[Créer une ressource TAP de réseau virtuel](../virtual-network/tutorial-tap-virtual-network-cli.md)|
|

### <a name="network-watcher"></a><a name="networkwatcher"></a>Network Watcher
Azure Network Watcher offre des outils permettant d’effectuer un monitoring et des diagnostics, d’afficher les métriques et d’activer et de désactiver les journaux d’activité pour les ressources se trouvant sur un réseau virtuel Azure. Pour plus d’informations, consultez [Présentation de Network Watcher](../network-watcher/network-watcher-monitoring-overview.md?toc=%2fazure%2fnetworking%2ftoc.json).
### <a name="expressroute-monitor"></a><a name="expressroutemonitor"></a>Moniteur ExpressRoute
Pour en savoir plus sur l’affichage des métriques de circuit ExpressRoute, les journaux de ressources et les alertes, consultez [Supervision, métriques et alertes ExpressRoute](../expressroute/expressroute-monitoring-metrics-alerts.md?toc=%2fazure%2fnetworking%2ftoc.json).
### <a name="azure-monitor"></a><a name="azuremonitor"></a>Azure Monitor
Azure Monitor optimise la disponibilité et les performances de vos applications en fournissant une solution complète pour collecter, analyser et agir sur les données de télémétrie de vos environnements cloud et locaux. Il vous aide à comprendre le fonctionnement de vos applications et identifie de façon proactive les problèmes qui les affectent et les ressources dont elles dépendent. Pour plus d’informations, consultez [Vue d’ensemble d’Azure Monitor](../azure-monitor/overview.md?toc=%2fazure%2fnetworking%2ftoc.json).
### <a name="virtual-network-tap"></a><a name="vnettap"></a>TAP de réseau virtuel
Le TAP (point d’accès terminal) de réseau virtuel Azure vous permet de diffuser en continu votre trafic réseau de machine virtuelle vers un collecteur de paquets réseau ou un outil analytique. Le collecteur ou l’outil analytique est fourni par une [appliance virtuelle réseau](https://azure.microsoft.com/solutions/network-appliances/) partenaire. 

L’image suivante illustre le fonctionnement d’un TAP de réseau virtuel. 

![Fonctionnement d’un TAP de réseau virtuel](./media/networking-overview/virtual-network-tap-architecture.png)

Pour plus d’informations, consultez [Présentation du TAP de réseau virtuel](../virtual-network/virtual-network-tap-overview.md).

## <a name="next-steps"></a>Étapes suivantes

- Créez votre premier réseau virtuel et connectez-y plusieurs machines virtuelles en effectuant les étapes décrites dans l’article [Créer votre premier réseau virtuel](../virtual-network/quick-create-portal.md?toc=%2fazure%2fnetworking%2ftoc.json).
- Connectez votre ordinateur à un réseau virtuel en effectuant les étapes décrites dans l’article [Configurer une connexion de point à site](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fnetworking%2ftoc.json).
- Équilibrez la charge de trafic Internet sur les serveurs publics en effectuant les étapes décrites dans l’article [Créer un équilibrage de charge accessible sur Internet](../load-balancer/load-balancer-get-started-internet-portal.md?toc=%2fazure%2fnetworking%2ftoc.json).
 
 
   
