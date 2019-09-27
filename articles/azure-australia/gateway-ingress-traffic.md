---
title: Contrôle du trafic d’entrée dans Azure Australie
description: Guide pour le contrôle du trafic d’entrée dans Azure Australie pour répondre aux exigences du secteur public Australien pour les passerelles Internet sécurisées
author: galey801
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: 3885b9fa346047a50a49c7b2f9b96b6a8f95e51f
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/05/2019
ms.locfileid: "68779349"
---
# <a name="controlling-ingress-traffic-in-azure-australia"></a>Contrôle du trafic d’entrée dans Azure Australie

Un élément fondamental de la sécurisation des systèmes de Tâches de configuration initiales est le contrôle du trafic réseau. Pour réduire le risque de compromission, le trafic doit être limité à ce qui est strictement nécessaire pour qu’un système fonctionne.

Ce guide fournit des détails sur le fonctionnement du trafic réseau entrant (d’entrée) dans Azure, et des recommandations pour l’implémentation de contrôles de sécurité réseau pour un système connecté à Internet.

Les contrôles réseau s’alignent sur les préconisations à l’adresse du grand public formulées par l’Australian Cyber Security Center (ACSC) reprises dans son Manuel de sécurité des informations (Information Security Manual, ISM).

## <a name="requirements"></a>Configuration requise

Les exigences de sécurité globales pour les systèmes du Commonwealth sont définies dans l’ISM. Pour aider les entités du Commonwealth à implémenter la sécurité réseau, l’ACSC a publié [ACSC Protect: Implementing Network Segmentation and Segregation](https://www.acsc.gov.au/publications/protect/network_segmentation_segregation.htm) et, pour aider à la sécurisation des systèmes dans des environnements cloud, elle a publié le document [Cloud Computing Security for Tenants](https://www.acsc.gov.au/publications/protect/Cloud_Computing_Security_for_Tenants.pdf).

Ces guides décrivent le contexte d’implémentation de la sécurité réseau et du contrôle du trafic, et fournissent des recommandations pratiques en matière de conception et de configuration de réseau.

Le guide sur la [sécurité du cloud computing pour les locataires de Microsoft Azure](https://aka.ms/au-irap) dans la page australienne du Portail d’approbation de services met en évidence des technologies Microsoft spécifiques qui vous permettent de suivre les conseils des publications de l’ACSC.

Les exigences clés suivantes, identifiées dans les publications de l’ACSC, sont importantes pour le contrôle du trafic d’entrée dans Azure:

|Description|Source|
|---|---|
|**Implémentez une segmentation et une ségrégation de réseau. Par exemple, utilisez une architecture à plusieurs niveaux, intégrant des pare-feu basés sur un hôte et des contrôles d’accès au réseau du fournisseur de solutions Cloud afin de limiter les connectivités réseau de machine virtuelle entrante et sortante aux ports et protocoles strictement indispensables.**| _Cloud computing pour locataires_|
|**Implémentez une connectivité réseau fiable, offrant une bande passante élevée et une faible latence** entre le locataire (incluant les utilisateurs distants de celui-ci) et le service cloud pour répondre aux exigences de disponibilité du locataire  | _Cloud computing pour locataires_|
|**Appliquez les technologies au-delà de la couche réseau**. Chaque hôte et réseau doit être segmenté et séparé, si possible, au niveau le plus bas pouvant être géré de manière pratique. Dans la plupart des cas, la segmentation et la séparation s’appliquent de la couche de liaison de données à la couche application incluse. Cependant, dans des environnements sensibles, une isolation physique peut être appropriée. Les mesures basées sur un hôte et à l’échelle d’un réseau doivent être déployées de manière complémentaire et contrôlées de façon centralisée. L’utilisation d’un pare-feu ou d’une appliance de sécurité en tant qu’unique mesure de sécurité ne suffit pas. |_ACSC Protect: Implementing Network Segmentation and Segregation_|
|**Utilisez les principes du moindre privilège et du besoin de savoir**. Si un hôte, un service ou un réseau n’a pas besoin de communiquer avec un autre hôte, service ou réseau, il ne devrait pas être autorisé à le faire. Si un hôte, un service ou un réseau doit communiquer uniquement avec un autre hôte, service ou réseau à l’aide de port ou protocoles spécifiques, tout autre port ou protocole doit être désactivé. L’adoption de ces principes pour un réseau vient en complément de la minimisation des privilèges d’utilisateur et augmente considérablement la posture de sécurité globale de l’environnement. |_ACSC Protect: Implementing Network Segmentation and Segregation_|
|**Séparez les hôtes et les réseaux en fonction de leur sensibilité ou de leur importance pour l’exploitation**. Vous pouvez obtenir une séparation en utilisant des composants matériels ou plateformes variables en fonction des classifications de sécurité, des domaines de sécurité ou des conditions de disponibilité et d’intégrité des hôtes ou réseaux. Plus spécifiquement, séparez les réseaux de gestion et envisagez d’isoler physiquement les réseaux de gestion hors bande pour les environnements sensibles. |_ACSC Protect: Implementing Network Segmentation and Segregation_|
|**Identifiez, authentifiez et autorisez l’accès de toutes les entités à toutes les autres entités**. Tous les utilisateurs, hôtes et services doivent avoir accès uniquement aux autres utilisateurs, hôtes et services indispensables pour l’exécution de leurs tâches ou fonctions spécifiées. Tous les services hérités ou locaux qui contournent ou dégradent les services d’identification, d’authentification et d’autorisation doivent être désactivés et leur utilisation doit être surveillée de près. |_ACSC Protect: Implementing Network Segmentation and Segregation_|
|**Implémentez une liste verte du trafic réseau plutôt qu’une liste rouge**. Autorisez l’accès uniquement pour le trafic réseau approprié (c’est-à-dire identifié, authentifié et autorisé), au lieu de refuser l’accès pour le trafic réseau inapproprié (par exemple, en bloquant une adresse ou un service spécifiques). En termes de stratégie de sécurité, les listes vertes sont supérieures aux listes rouges, et améliorent considérablement la capacité d’une organisation à détecter et à évaluer des intrusions potentielles. |_ACSC Protect: Implementing Network Segmentation and Segregation_|
|

Cet article fournit des informations et des recommandations sur la façon dont ces exigences peuvent être satisfaites pour des systèmes déployés dans Azure à l’aide de solutions IaaS et PaaS. Nous vous conseillons également lire l’article [Contrôle du trafic de sortie dans Azure Australie](gateway-egress-traffic.md) pour bien comprendre le contrôle du trafic réseau au sein d’Azure.

## <a name="architecture"></a>Architecture

Si vous êtes impliqué dans la conception ou l’implémentation des contrôles de sécurité réseau et de trafic d’entrée, vous devez commencer par comprendre comment le trafic réseau d’entrée fonctionne dans Azure en lien avec les solutions IaaS et PaaS. Cette section fournit une vue d’ensemble des points d’entrée possibles où le trafic réseau pourrait atteindre une ressource hébergée dans Azure, ainsi que des contrôles de sécurité disponibles pour restreindre et contrôler ce trafic. Chacun de ces composants est abordé en détail dans les autres sections de ce guide.

### <a name="architecture-components"></a>Composants de l’architecture

Le diagramme architectural présenté ici décrit les chemins possibles que le trafic réseau peut emprunter pour se connecter à un service hébergé dans Azure. Ces composants sont divisés en Azure, entrée IaaS, entrée PaaS et contrôle de sécurité, selon la fonction qu’ils fournissent pour le trafic d’entrée.

![Architecture](media/ingress-traffic.png)

### <a name="azure-components"></a>Composants Azure

|Composant | Description|
|---|---|
|**Protection DDOS** | Une attaque par déni de service distribué (DDoS) tente d’épuiser les ressources d’une application afin de la rendre indisponible aux utilisateurs légitimes. Les attaques DDoS peuvent être ciblées sur n’importe quel point de terminaison qui est publiquement accessible via Internet. Azure inclut une protection contre les attaques DDoS automatiquement via la plateforme Azure, et fournit des fonctionnalités d’atténuation supplémentaires qui peuvent être activées pour des applications spécifiques afin d’offrir un contrôle plus précis.|
| **Traffic Manager** | Azure Traffic Manager est un équilibreur de charge de trafic basé sur un DNS, qui peut distribuer le trafic de manière optimale aux services dans toutes les régions Azure globales, tout en offrant une réactivité et une disponibilité élevées. Traffic Manager utilise le système DNS pour diriger les requêtes des clients vers le point de terminaison le plus approprié, en fonction de la méthode de routage du trafic et de l’intégrité des points de terminaison.|
| **ExpressRoute** | ExpressRoute est une connexion réseau dédiée à l’utilisation des services de cloud computing Microsoft. Approvisionnée au travers d’un fournisseur de connectivité, elle offre de meilleurs niveaux de fiabilité, de rapidité, de latence et de sécurité que des connexions classiques sur Internet. Un circuit ExpressRoute est la connexion logique entre l’infrastructure locale et les services de cloud computing Microsoft via un fournisseur de connectivité.|
| **Appairage privé ExpressRoute** | Le peering privé ExpressRoute est une connexion entre l’environnement local et des réseaux virtuels Azure privés. Il permet d’accéder aux services Azure, tels que Machines Virtuelles, qui sont déployés au sein d’un réseau virtuel. Les ressources et les réseaux virtuels accessibles via le peering privé sont considérés comme une extension du réseau principal d’une organisation. Un peering privé fournit une connectivité bidirectionnelle entre le réseau local et les réseaux virtuels Azure à l’aide d’adresses IP privées.|
| **Appairage Microsoft ExpressRoute** | Un peering Microsoft ExpressRoute est une connexion entre l’environnement local et des services publics Microsoft et Azure. Cela inclut une connectivité à Office 365, à Dynamics 365 et aux services Azure PaaS. Le peering est établi sur des adresses IP publiques appartenant à l’organisation ou au fournisseur de connectivité. Par défaut, aucun service n’est accessible via le peering Microsoft ExpressRoute et une organisation doit adopter les services requis. Ce processus fournit ensuite une connectivité aux mêmes points de terminaison que ceux disponibles sur Internet.|
|

### <a name="iaas-ingress-components"></a>Composants d’entrée IaaS

|Composant | Description|
|---|---|
|**Interface réseau** | Une interface réseau est une ressource qui existe dans Azure. Elle est connectée à une machine virtuelle et reçoit une adresse IP routable non-Internet privée du sous-réseau auquel elle est associée. Cette adresse IP est affectée de façon dynamique ou statique via Azure Resource Manager.|
|**Sous-réseau** | Un sous-réseau est une plage d’adresses IP créée au sein d’un réseau virtuel. Il est possible de créer plusieurs sous-réseaux au sein d’un réseau virtuel afin de segmenter celui-ci.|
| **Réseau virtuel** | Un réseau virtuel est une ressource fondamentale dans Azure, qui fournit une plateforme et un périmètre pour le déploiement de ressources et la communication. Le réseau virtuel existe dans une région Azure et définit l’espace d’adressage IP ainsi que les limites de routage pour les ressources qui y sont intégrées, telles que les machines virtuelles.|
| **Homologation de réseaux virtuels** | VNet peering est une option de configuration Azure qui permet une communication directe entre deux réseaux virtuels sans avoir besoin de passerelle réseau virtuel. Une fois appairés, les deux réseaux virtuels peuvent communiquer directement et une configuration supplémentaire peut contrôler l’utilisation des passerelles réseau virtuel et d’autres options de transit.|
| **Adresse IP publique** | Une adresse IP publique est une ressource qui réserve l’une des adresses IP publiques routables via Internet appartenant à Microsoft de la région spécifiée en vue de son utilisation dans le réseau virtuel. Elle peut être associée à une interface réseau spécifique, ce qui permet d’accéder à la ressource à partir d’Internet, de ExpressRoute et d’autres systèmes PaaS.|
| **Passerelle ExpressRoute** | Une passerelle ExpressRoute est un objet dans un réseau virtuel, qui assure la connectivité et le routage à partir du réseau virtuel vers des réseaux locaux via un peering privé sur un circuit ExpressRoute.|
| **Passerelle VPN** | Une passerelle VPN est un objet dans un réseau virtuel, qui fournit un tunnel chiffré reliant un réseau virtuel à un réseau externe. Le tunnel chiffré peut être une connexion site à site pour une communication bidirectionnelle avec un environnement local, un autre réseau virtuel ou un environnement cloud, ou une connexion point à site pour une communication avec un point de terminaison unique.|
| **Intégration avec un réseau virtuel PaaS** | De nombreuses fonctionnalités PaaS peuvent être déployées dans un réseau virtuel ou intégrées avec celui-ci. Certaines fonctionnalités PaaS peuvent être entièrement intégrées avec un réseau virtuel et être accessibles via des adresses IP exclusivement privées. D’autres, telles qu’Azure Load Balancer et Azure Application Gateway, peuvent avoir une interface externe avec une adresse IP publique, ainsi qu’une interface interne avec une adresse IP privée à l’intérieur du réseau virtuel. Dans ce cas, le trafic peut entrer dans le réseau virtuel via la fonctionnalité PaaS.|
|

### <a name="paas-ingress-components"></a>Composants d’entrée PaaS

|Composant | Description|
|---|---|
|**Nom d’hôte** | Les fonctionnalités Azure PaaS sont identifiées par un nom d’hôte public unique qui est affecté lors de la création de la ressource. Ce nom d’hôte est ensuite inscrit dans un domaine DNS public où il peut être résolu en adresse IP publique.|
|**Adresse IP publique** | À moins d’être déployées dans une configuration intégrée de réseau virtuel, les fonctionnalités Azure PaaS sont accessibles via une adresse IP publique routable sur Internet. Cette adresse peut être dédiée à des ressources spécifiques, telles qu’un équilibreur de charge public, ou associée à une fonctionnalité spécifique ayant un point d’entrée partagé pour plusieurs instances, telles que le Stockage ou SQL. Cette adresse IP publique est accessible à partir d’Internet, d’ExpressRoute ou d’adresses IP publiques IaaS par le biais du réseau principal Azure.|
|**Points de terminaison de service** | Les points de terminaison de service fournissent une connexion privée directe d’un réseau virtuel vers une fonctionnalité PaaS spécifique. Les points de terminaison de service qui sont disponibles uniquement pour un sous-ensemble de fonctionnalités PaaS offrent des performances et une sécurité accrues pour les ressources d’un réseau virtuel accédant à PaaS.|
|

### <a name="security-controls"></a>Contrôles de sécurité

|Composant | Description|
|---|---|
|**Groupes de sécurité réseau (NSG)** | Les groupes de sécurité réseau contrôlent le trafic en direction et sortant des ressources de réseau virtuel dans Azure. Les groupes de sécurité réseau appliquent des règles pour les flux de trafic autorisés ou refusés, qui incluent le trafic dans et entre Azure et les réseaux externes, tels qu’un réseau local ou Internet. Les groupes de sécurité réseau sont appliqués aux sous-réseaux au sein d’un réseau virtuel ou à des interfaces réseau individuelles.|
|**Pare-feu PaaS** | De nombreuses fonctionnalités PaaS, telles que Stockage et SQL, disposent d’un pare-feu intégré pour contrôler le trafic réseau d’entrée vers la ressource spécifique. Des règles peuvent être créées pour autoriser ou refuser des connexions à partir d’adresses IP spécifiques et/ou de réseaux virtuels.|
|**Authentification PaaS et contrôle d’accès** | Dans le cadre d’une approche multicouche de la sécurité, les fonctionnalités PaaS offrent plusieurs mécanismes d’authentification des utilisateurs et de contrôle des privilèges et accès.|
|**Azure Policy** | Azure Policy est un service dans Azure pour la création, l’attribution et la gestion de stratégies. Ces stratégies utilisent des règles pour contrôler les types de ressources qui peuvent être déployées et la configuration de celles-ci. Des stratégies peuvent être utilisées pour appliquer la conformité en empêchant le déploiement de ressources ne répondant pas à certaines exigences, ou pour surveiller l’état de conformité.|
|

## <a name="general-guidance"></a>Règle générale

Pour concevoir et créer des solutions sécurisées dans Azure, il est essentiel de comprendre et de contrôler le trafic réseau afin que seules des communications identifiées et autorisées puissent se produire. L’objectif de ce guide et des conseils relatifs à des composants spécifiques dans les sections ultérieures est de décrire les outils et les services qui peuvent être utilisés pour appliquer les principes décrits dans _ACSC Protect: Implementing Network Segmentation and Segregation_ dans l’ensemble des charges de travail Azure. Cela comprend la description de la manière de créer une architecture virtuelle pour sécuriser des ressources quand il n’est pas possible d’appliquer les mêmes contrôles physiques et de réseau traditionnels que dans un environnement local.

### <a name="specific-focus-areas"></a>Domaines stratégiques spécifiques

* Limiter le nombre de points d’entrée dans les réseaux virtuels
* Limiter le nombre d’adresses IP publiques
* Envisager d’utiliser une conception de réseau Hub and spoke pour les réseaux virtuels, comme décrit dans la documentation sur le Centre de données virtuel Microsoft
* Utiliser des produits avec des fonctionnalités de sécurité intégrées pour les connexions entrantes à partir d’Internet (par exemple, Application Gateway, passerelle API, appliances virtuelles réseau)
* Limiter les flux de communication vers des capacités PaaS uniquement pour les capacités nécessaires à la fonctionnalité du système
* Déployer PaaS dans une configuration intégrée de réseau virtuel pour augmenter la séparation et le contrôle
* Configurer des systèmes pour utiliser des mécanismes de chiffrement conformes aux préconisations à l’adresse du grand public de l’ACSC et à l’ISM
* Utiliser des protections basées sur l’identité, telles que l’authentification et le contrôle d’accès en fonction du rôle, en plus des contrôles réseau traditionnels
* Implémenter ExpressRoute pour la connectivité avec les réseaux locaux
* Implémenter des VPN pour le trafic administratif et l’intégration avec des réseaux externes
* Utiliser Azure Policy pour limiter les régions et ressources à celles qui sont strictement nécessaires pour la fonctionnalité du système
* Utiliser Azure Policy pour appliquer une configuration de sécurité à des ressources accessibles via Internet

### <a name="additional-resources"></a>Ressources supplémentaires

|Ressource | Lien|
|---|---|
|Documents relatifs à conformité à la réglementation et aux politiques australiennes incluant des préconisations à l’adresse du grand public|[https://aka.ms/au-irap](https://aka.ms/au-irap)|
|Centre de données virtuel Azure|[https://docs.microsoft.com/azure/architecture/vdc/networking-virtual-datacenter](https://docs.microsoft.com/azure/architecture/vdc/networking-virtual-datacenter)|
|Segmentation de réseau de l’ACSC|[https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm](https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm)|
|Sécurité du cloud pour les locataires de l’ACSC| [https://acsc.gov.au/publications/protect/cloud-security-tenants.htm](https://acsc.gov.au/publications/protect/cloud-security-tenants.htm)|
|Manuel sur la sécurité des informations de l’ACSC|[https://acsc.gov.au/infosec/ism/index.htm](https://acsc.gov.au/infosec/ism/index.htm)|

## <a name="component-guidance"></a>Guide sur les composants

Cette section fournit des conseils supplémentaires sur les composants individuels, qui sont pertinents pour le trafic d’entrée pour les systèmes déployés dans Azure. Chaque section décrit l’intention du composant spécifique avec des liens vers de la documentation et des conseils de configuration qui peuvent être utilisés pour faciliter les activités de conception et de création.

## <a name="azure"></a>Azure

Toutes les communications vers des ressources au sein d’Azure passent par l’infrastructure réseau gérée par Microsoft, qui fournit des fonctionnalités de connectivité et de sécurité. Une série de protections sont automatiquement mises en place par Microsoft pour protéger la plateforme et l’infrastructure réseau Azure, et des fonctionnalités supplémentaires sont disponibles en tant que services dans Azure pour contrôler le trafic réseau et établir la segmentation et la séparation de réseau.

### <a name="ddos-protection"></a>Protection DDOS

Les ressources accessibles via Internet sont vulnérables aux attaques par déni de service distribué (DDoS). Pour vous protéger contre ces attaques, Azure fournit des protections DDoS aux niveaux De base et Standard.

La protection De base est automatiquement activée dans la plateforme Azure incluant la surveillance permanente du trafic et la prévention en temps réel d’attaques courantes au niveau du réseau, et fournissant les mêmes défenses que celles utilisées par les services en ligne de Microsoft. La distribution et l’atténuation du trafic d’attaque peuvent être réalisées entre différentes régions à l’échelle du réseau global d’Azure. La protection est assurée pour les adresses IP publiques IPv4 et IPv6 Azure.

Standard : fournit des fonctionnalités d’atténuation supplémentaires par rapport au niveau de service De base destinées spécifiquement aux ressources de réseau virtuel Azure. Les stratégies de protection sont paramétrées par le biais d’algorithmes de surveillance du trafic et d’apprentissage automatique dédiés. La protection est assurée pour les adresses IP publiques IPv4 Azure.

|Ressource|Lien|
|---|---|
|Vue d’ensemble d’Azure DDoS Protection|[https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview)|
|Meilleures pratiques Azure DDoS|[https://docs.microsoft.com/azure/security/fundamentals/ddos-best-practices](https://docs.microsoft.com/azure/security/fundamentals/ddos-best-practices)|
|Gestion d’Azure DDoS Protection|[https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection](https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection)|
|

### <a name="traffic-manager"></a>Traffic Manager

Traffic Manager permet de gérer le trafic d’entrée en contrôlant les points de terminaison d’une application qui reçoivent des connexions. Pour vous protéger contre une perte de disponibilité de systèmes ou d’applications en raison d’une attaque dirigée contre la sécurité informatique, ou pour récupérer des services après une compromission du système, Traffic Manager permet de rediriger le trafic vers des instances d’application disponibles opérationnelles.

|Ressource|Lien|
|---|---|
|Vue d’ensemble de Traffic Manager | [https://docs.microsoft.com/azure/traffic-manager/traffic-manager-overview](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-overview)|
|Guide sur la récupération d’urgence à l’aide d’Azure DNS et Traffic Manager | [https://docs.microsoft.com/azure/networking/disaster-recovery-dns-traffic-manager](https://docs.microsoft.com/azure/networking/disaster-recovery-dns-traffic-manager)|
|

### <a name="expressroute"></a>ExpressRoute

ExpressRoute permet d’établir un chemin privé à partir d’un environnement local vers des systèmes hébergés dans Azure. Cette connexion peut offrir une fiabilité accrue et des performances garanties au travers d’une confidentialité améliorée pour les communications réseau. ExpressRoute permet aux entités du Commonwealth de contrôler le trafic entrant à partir de l’environnement local, et de définir des adresses dédiées spécifiques de l’organisation à utiliser pour les règles entrantes de pare-feu et les listes de contrôle d’accès.

|Ressource | Lien|
|---|---|
|Vue d’ensemble d’ExpressRoute | [https://docs.microsoft.com/azure/expressroute/](https://docs.microsoft.com/azure/expressroute/)|
|Modèles de connectivité ExpressRoute | [https://docs.microsoft.com/azure/expressroute/expressroute-connectivity-models](https://docs.microsoft.com/azure/expressroute/expressroute-connectivity-models)|
|

### <a name="expressroute-private-peering"></a>Peering privé ExpressRoute

Le peering privé fournit un mécanisme permettant d’étendre un environnement local dans Azure en utilisant uniquement des adresses IP privées. Cela permet aux entités du Commonwealth d’intégrer des réseaux virtuels Azure et des plages d’adresses avec des systèmes et services locaux existants. Le peering privé garantit que la communication dans ExpressRoute est exclusivement réservée aux réseaux virtuels autorisés par l’organisation. Si vous utilisez le peering privé, les entités du Commonwealth doivent implémenter des appliances virtuelle réseau plutôt qu’une passerelle VPN Azure pour établir la communication VPN sécurisée avec vos réseaux locaux, conformément aux préconisations à l’adresse du grand public de l’ACSC.

|Ressource | Lien|
|---|---|
|Vue d’ensemble du peering privé ExpressRoute | [https://docs.microsoft.com/azure/expressroute/expressroute-circuit-peerings#routingdomains](https://docs.microsoft.com/azure/expressroute/expressroute-circuit-peerings#routingdomains)|
|Guide pratique du peering privé ExpressRoute | [https://docs.microsoft.com/azure/expressroute/expressroute-howto-routing-portal-resource-manager#private](https://docs.microsoft.com/azure/expressroute/expressroute-howto-routing-portal-resource-manager#private)|
|

### <a name="expressroute-microsoft-peering"></a>Peering Microsoft ExpressRoute

Le peering Microsoft offre une connexion à haut débit et à faible latence aux services publics Microsoft sans devoir traverser Internet. Cela améliore la fiabilité, les performances et la confidentialité des connexions. En utilisant des filtres de routage, les entités du Commonwealth peuvent limiter les communications aux régions Azure dont elles ont besoin, mais cela inclut des services hébergés par d’autres organisations et peut nécessiter des fonctionnalités de filtrage ou d’inspection supplémentaires entre l’environnement local et Microsoft.

Les entités du Commonwealth peuvent utiliser les adresses IP publiques dédiées établies par la relation de peering pour identifier de manière unique l’environnement local à utiliser dans les pare-feu et les listes de contrôle d’accès au sein des fonctionnalités PaaS.

En guise d’alternative, les entités du Commonwealth peuvent utiliser le peering Microsoft ExpressRoute en tant que réseau en sous-couche pour établir la connectivité VPN via une passerelle VPN Azure. Dans ce modèle, il n’y a aucune communication active entre le réseau local interne et les services publics Azure via ExpressRoute, mais la connectivité sécurisée via des réseaux virtuels privés est obtenue conformément aux préconisations à l’adresse du grand public de l’ACSC.

|Ressource | Lien|
|---|---|
|Vue d’ensemble du peering Microsoft ExpressRoute | [https://docs.microsoft.com/azure/expressroute/expressroute-circuit-peerings#routingdomains](https://docs.microsoft.com/azure/expressroute/expressroute-circuit-peerings#routingdomains)|
|Guide pratique du peering Microsoft ExpressRoute | [https://docs.microsoft.com/azure/expressroute/expressroute-howto-routing-portal-resource-manager#msft](https://docs.microsoft.com/azure/expressroute/expressroute-howto-routing-portal-resource-manager#msft)|
|

## <a name="iaas-ingress"></a>Entrée IaaS

Cette section fournit des conseils sur les composants pour le contrôle du trafic d’entrée vers des composants IaaS. IaaS comprend les Machines Virtuelles et d’autres ressources de calcul qui peuvent être déployées et gérées au sein d’un réseau virtuel dans Azure. Pour que le trafic arrive aux systèmes déployés à l’aide d’IaaS, il doit disposer d’un point d’entrée dans le réseau virtuel, qui peut être établi via une adresse IP publique, une passerelle de réseau virtuel ou une relation de peering de réseau virtuel.

### <a name="network-interface"></a>interface réseau

Les interfaces réseau sont les points d’entrée pour tout le trafic vers une machine virtuelle. Les interfaces réseau permettent la configuration de l’adressage IP et peuvent être utilisées pour appliquer des groupes de sécurité réseau ou pour router le trafic via une appliance virtuelle réseau. Les interfaces réseau des machines virtuelles doivent être planifiées et configurées de manière appropriée pour les objectifs globaux de segmentation et de séparation de réseau.

|Ressource | Lien|
|---|---|
|Créer, modifier ou supprimer une interface réseau | [https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface)|
|Adressage IP d’interface réseau | [https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#private-ip-addresses](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#private-ip-addresses)|
|

### <a name="subnet"></a>Subnet

Les sous-réseaux sont des composants essentiels pour la segmentation et la séparation de réseau dans Azure. Des sous-réseaux peuvent également être utilisés pour séparer des systèmes. Des groupes de sécurité réseau peuvent être appliqués à des sous-réseaux afin de restreindre les flux de communication en entrée au strict nécessaire pour assurer la fonctionnalité du système. Des sous-réseaux peuvent être utilisés comme adresses source et de destination pour les règles de pare-feu et les listes de contrôle d’accès, et être configurés pour des points de terminaison de service afin de fournir une connectivité aux fonctionnalités PaaS.

|Ressource | Lien|
|---|---|
|Ajouter, modifier ou supprimer un sous-réseau de réseau virtuel | [https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet)|
|

### <a name="virtual-network-vnet"></a>Réseau virtuel (VNet)

Les réseaux virtuels sont les blocs de construction fondamentaux de la mise en réseau dans Azure. Les réseaux virtuels définissent un espace d’adressage IP et une limite de routage à utiliser dans divers systèmes. Les réseaux virtuels sont divisés en sous-réseaux et tous les sous-réseaux au sein d’un réseau virtuel disposent d’un itinéraire réseau direct entre eux. En utilisant des passerelles de réseau virtuel (ExpressRoute ou VPN), les systèmes au sein d’un réseau virtuel peuvent être rendus accessibles aux environnements locaux et externes. La compréhension des réseaux virtuels, ainsi que des paramètres de configuration et du routage associés sont essentiels pour comprendre et contrôler le trafic réseau d’entrée.

|Ressource | Lien|
|---|---|
|Vue d’ensemble des réseaux virtuels | [https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)|
|Guide pratique pour la planification de réseaux virtuels | [https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm)|
Guide de démarrage rapide pour la création de réseau virtuel | [https://docs.microsoft.com/azure/virtual-network/quick-create-portal](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)|
|

### <a name="vnet-peering"></a>VNET Peering

VNet peering est utilisé pour fournir un chemin de communication direct entre deux réseaux virtuels. Une fois le peering établi, les hôtes d’un réseau virtuel ont un chemin de routage haut débit directement vers des hôtes d’un autre réseau virtuel. Les groupes de sécurité réseau s’appliquent toujours au trafic, car des paramètres de configuration standard et avancée peuvent être utilisés pour définir si la communication via des passerelles réseau virtuel ou à partir d’autres systèmes externes est autorisée.

|Ressource | Lien|
|---|---|
|Vue d’ensemble du peering de réseau virtuel |  [https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)|
|Créer, modifier ou supprimer un peering de réseau virtuel | [https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering)|
|

### <a name="public-ip-on-vnet"></a>Adresse IP publique sur réseau virtuel

Les adresses IP publiques sont utilisées pour fournir un chemin de communication d’entrée vers des services déployés dans un réseau virtuel. Les entités du Commonwealth doivent planifier avec soin l’allocation d’adresses IP publiques et les affecter uniquement à des ressources soumises à une véritable exigence. Dans le cadre d’une pratique de conception générale, des adresses IP publiques doivent être allouées à des ressources intégrant des fonctionnalités de sécurité, telles qu’Application Gateway ou des appliances virtuelles réseau, afin de fournir un point d’entrée public contrôlé sécurisé à un réseau virtuel.

|Ressource | Lien|
|---|---|
|Vue d’ensemble des adresses IP publiques | [https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#public-ip-addresses](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#public-ip-addresses)|
|Créer, modifier ou supprimer une adresse IP publique | [https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address)|
|

### <a name="expressroute-gateway"></a>Passerelle ExpressRoute

Les passerelles ExpressRoute fournissent un point d’entrée à partir de l’environnement local, et doivent être déployées pour répondre aux exigences de sécurité, de disponibilité, de coût et de performances. Les passerelles ExpressRoute fournissent une bande passante réseau définie et occasionnent des coûts d’utilisation après le déploiement. Les réseaux virtuels ne peuvent avoir qu’une seule passerelle ExpressRoute, mais ils peuvent être connectés à plusieurs circuits ExpressRoute et être exploités par plusieurs réseaux virtuels via VNET Peering, permettant à plusieurs réseau virtuels de partager la bande passante et la connectivité. Veillez à configurer le routage entre les environnements locaux et les réseaux virtuels à l’aide de passerelles ExpressRoute pour garantir une connectivité de bout en bout à l’aide de points d’entrée de réseau connus et contrôlés. Les entités du Commonwealth utilisant une passerelle ExpressRoute doivent également déployer des appliances virtuelles réseau afin d’établir une connectivité VPN à l’environnement local pour la conformité aux préconisations à l’adresse du grand public de l’ACSC.

|Ressource | Lien|
|---|---|
|Vue d’ensemble de la passerelle ExpressRoute | [https://docs.microsoft.com/azure/expressroute/expressroute-about-virtual-network-gateways](https://docs.microsoft.com/azure/expressroute/expressroute-about-virtual-network-gateways)|
|Configurer une passerelle de réseau virtuel pour ExpressRoute | [https://docs.microsoft.com/azure/expressroute/expressroute-howto-add-gateway-portal-resource-manager](https://docs.microsoft.com/azure/expressroute/expressroute-howto-add-gateway-portal-resource-manager)|
|

### <a name="vpn-gateway"></a>Passerelle VPN

Une passerelle VPN Azure fournit un point réseau d’entrée à partir d’un réseau externe pour des connexions site à site ou point à site sécurisées. Les passerelles VPN fournissent une bande passante réseau définie et occasionnent des coûts d’utilisation après déploiement. Les entités du Commonwealth utilisant une passerelle VPN doivent s’assurer que celle-ci est configurée conformément aux préconisations à l’adresse du grand public de l’ACSC. Les réseaux virtuels ne peuvent avoir qu’une seule passerelle VPN, mais celle-ci peut être configurée avec plusieurs tunnels et exploitée par plusieurs réseaux virtuels via un peering de réseaux virtuels permettant à plusieurs réseaux virtuels de partager la bande passante et la connectivité. Des passerelles VPN peuvent être établies sur Internet ou sur ExpressRoute via le peering Microsoft.

|Ressource | Lien|
|---|---|
|Vue d’ensemble de la passerelle VPN | [https://docs.microsoft.com/azure/vpn-gateway/](https://docs.microsoft.com/azure/vpn-gateway/)|
|Planification et conception de la passerelle VPN | [https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design)|
|Configuration de passerelle VPN pour les agences gouvernementales australiennes|[Configuration d’IPSec requise pour les agences gouvernementales australiennes](vpn-gateway.md)|
|

### <a name="paas-vnet-integration"></a>Intégration avec un réseau virtuel PaaS

L’exploitation de PaaS peut fournir une fonctionnalité et une disponibilité accrues, ainsi que réduire la charge de gestion, mais doit être sécurisée de manière appropriée. Pour augmenter le contrôle, appliquer une segmentation de réseau ou fournir un point d’entrée sécurisé pour les applications et services, de nombreuses fonctionnalités de PaaS peuvent être intégrées avec un réseau virtuel.

Pour fournir un point d’entrée sécurisé, des fonctionnalités PaaS telles qu’Application Gateway peuvent être configurées avec une interface publique externe et une interface privée interne pour communiquer avec des services d’application. Cela évite de devoir configurer des serveurs d’applications avec des adresses IP publiques et de les exposer à des réseaux externes.

Pour utiliser une PaaS comme partie intégrante d’une architecture de système ou d’application, Microsoft fournit plusieurs mécanismes permettant de déployer une PaaS dans un réseau virtuel. La méthodologie de déploiement restreint l’accès entrant à partir de réseaux externes tels qu’Internet, tout en assurant la connectivité et l’intégration avec les systèmes et applications internes. Il peut s’agir, par exemple, d’environnements App Service ou d’instances managées SQL.

|Ressource | Lien|
|---|---|
|Intégration d’un réseau virtuel pour les services Azure | [https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)|
|Guide pratique pour l’intégration de votre application avec un réseau virtuel Azure | [https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet)|
|

## <a name="paas-ingress"></a>Entrée PaaS

Les capacités de PaaS offrent des opportunités d’augmentation de la capacité et de simplification de la gestion, mais introduisent de la complexité pour répondre aux exigences de segmentation et de séparation de réseau. Les capacités de PaaS sont généralement configurées avec des adresses IP publiques et accessibles à partir d’Internet.  Lors de la création de systèmes utilisant des capacités de PaaS, vous devez veiller à identifier tous les flux de communication nécessaires entre les composants au sein du système et les règles de sécurité réseau créées pour autoriser uniquement cette communication. Dans le cadre d’une approche de défense en profondeur de la sécurité, les capacités de PaaS doivent être configurées avec un chiffrement, une authentification et des contrôles et autorisations d’accès appropriés.  

### <a name="hostname"></a>Nom d’hôte

Les fonctionnalités PaaS sont identifiées de manière unique par des noms d’hôte afin de permettre l’hébergement de plusieurs instances du même service sur la même adresse IP publique. Des noms d’hôte uniques sont spécifiés lors de la création de ressources et existent dans des domaines DNS appartenant à Microsoft. Les noms d’hôte spécifiques pour des services autorisés peuvent être utilisés dans des outils de sécurité offrant des capacités de filtrage au niveau de l’application. Certains services peuvent également être configurés avec des domaines personnalisés selon les besoins.

|Ressource | Lien|
|---|---|
|De nombreux espaces de noms publics utilisés par les services Azure peuvent être obtenus via PowerShell en exécutant la commande Get-AzureRMEnvironment | [https://docs.microsoft.com/powershell/module/azurerm.profile/get-azurermenvironment](https://docs.microsoft.com/powershell/module/azurerm.profile/get-azurermenvironment)|
|Configuration d’un nom de domaine personnalisé pour un service cloud Azure | App Services et d’autres services peuvent avoir des domaines personnalisés [https://docs.microsoft.com/azure/cloud-services/cloud-services-custom-domain-name-portal](https://docs.microsoft.com/azure/cloud-services/cloud-services-custom-domain-name-portal)|
|

### <a name="public-ip-for-paas"></a>Adresse IP publique pour PaaS

Les adresses IP publiques pour les capacités de PaaS sont allouées en fonction de la région dans laquelle le service est hébergé ou déployé. Une bonne compréhension de l’allocation d’adresses IP publiques et des régions est requise pour créer des règles de sécurité réseau et une topologie de routage appropriées pour la segmentation et la séparation de réseau couvrant les réseaux virtuels Azure, PaaS et la connectivité ExpressRoute et Internet. Azure alloue les adresses IP à partir d’un pool alloué à chaque région Azure. Microsoft met à disposition les adresses utilisées dans chaque région pour le téléchargement, et le met à jour de manière régulière et contrôlée. Les services disponibles dans chaque région changent également fréquemment à mesure que de nouveaux services sont introduits ou que des services sont déployés plus largement. Les entités du Commonwealth doivent consulter ces documents régulièrement et peuvent utiliser une automatisation pour gérer les systèmes de façon appropriée. Vous pouvez obtenir des adresses IP spécifiques pour certains services hébergés dans chaque région en contactant le support Microsoft.

|Ressource | Lien|
|---|---|
|Plages IP de centres de données Microsoft Azure | [https://www.microsoft.com/download/details.aspx?id=41653](https://www.microsoft.com/download/details.aspx?id=41653)|
|Services Azure par région | [https://azure.microsoft.com/global-infrastructure/services/?regions=non-regional,australia-central,australia-central-2,australia-east,australia-southeast&products=all](https://azure.microsoft.com/global-infrastructure/services/?regions=non-regional,australia-central,australia-central-2,australia-east,australia-southeast&products=all)|
|

### <a name="service-endpoints"></a>Points de terminaison de service

Des points de terminaison de service de réseau virtuel fournissent une connexion réseau d’entrée privée haut débit pour les sous-réseaux d’un réseau virtuel afin d’utiliser des fonctionnalités PaaS spécifiques. Pour une segmentation et une séparation de réseau complètes de la capacité PaaS, celle-ci doit être configurée pour accepter les connexions uniquement à partir des réseaux virtuels nécessaires. Toutes les fonctionnalités PaaS ne prenant pas en charge une combinaison de règles de pare-feu incluant des points de terminaison de service et des règles d’adresse IP traditionnelles, il convient de bien comprendre le flux des communications requises pour l’administration et la fonctionnalité de l’application, afin que l’implémentation de ces contrôles de sécurité n’affecte pas la disponibilité du service.

|Ressource | Lien|
|---|---|
|Vue d’ensemble des points de terminaison de service | [https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview)
|Didacticiel |[https://docs.microsoft.com/azure/virtual-network/tutorial-restrict-network-access-to-resources](https://docs.microsoft.com/azure/virtual-network/tutorial-restrict-network-access-to-resources)|
|

## <a name="security"></a>Sécurité

L’implémentation de contrôles de segmentation et de séparation de réseau sur des fonctionnalités IaaS et PaaS est obtenue par une sécurisation des fonctionnalités elles-mêmes et par l’implémentation de chemins de communication contrôlés à partir des systèmes appelés à communiquer avec les fonctionnalités.

La conception et la création de solutions dans Azure sont un processus de création d’une architecture logique permettant de comprendre, de contrôler et de surveiller les ressources réseau partout où Azure est présent. Cette architecture logique est un logiciel défini au sein de la plateforme Azure, qui remplace une topologie de réseau physique telle qu’implémentée dans les environnements réseau traditionnels.

L’architecture logique créée doit fournir la fonctionnalité nécessaire à son usage, ainsi que la visibilité et le contrôle nécessaires pour la sécurité et l’intégrité.

L’obtention de ce résultat repose sur l’implémentation des outils de segmentation et de séparation de réseau nécessaires, ainsi que sur la protection et l’application de la topologie de réseau et l’implémentation de ces outils.

Les informations fournies dans ce guide permettent d’identifier les sources de trafic d’entrée qui doivent être autorisées, ainsi que les façons dont le trafic peut être contrôlé ou restreint davantage.

### <a name="network-security-groups-nsgs"></a>Groupes de sécurité réseau (NSG)

Les groupes de sécurité réseau sont utilisés pour spécifier le trafic entrant et sortant autorisé pour un sous-réseau ou une interface réseau spécifique. Lors de la configuration de groupes de sécurité réseau, les entités du Commonwealth doivent utiliser une approche de liste verte dans laquelle des règles sont configurées pour autoriser le trafic nécessaire, avec une règle par défaut configurée pour refuser tout trafic ne correspondant pas à une instruction d’autorisation spécifique. Lors de la planification et de la configuration de groupes de sécurité réseau, vous devez veiller à ce que tout le trafic entrant et sortant nécessaire soit capturé de manière appropriée. Cela inclut l’identification et la compréhension de toutes les plages d’adresses IP privées utilisées dans les réseaux virtuels Azure et l’environnement local, ainsi que de services Microsoft spécifiques tels qu’Azure Load Balancer et les exigences de gestion de PaaS. Les personnes impliquées dans la conception et l’implémentation des groupes de sécurité réseau doivent également comprendre l’utilisation des étiquettes de service et des groupes de sécurité d’application pour créer des règles de sécurité extrêmement précises spécifiques du service et de l’application.

|Ressource | Lien|
|---|---|
|Vue d’ensemble de la sécurité réseau | [https://docs.microsoft.com/azure/virtual-network/security-overview](https://docs.microsoft.com/azure/virtual-network/security-overview)
|Créer, changer ou supprimer un groupe de sécurité réseau | [https://docs.microsoft.com/azure/virtual-network/manage-network-security-group](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)|
|

## <a name="paas-firewall"></a>Pare-feu PaaS

Un pare-feu PaaS est une fonctionnalité de contrôle d’accès réseau qui peut être appliquée à certains services PaaS. Elle permet de configurer un filtrage d’adresses IP ou un filtrage à partir de réseaux virtuels spécifiques afin de limiter le trafic d’entrée vers l’instance PaaS spécifique. Pour les fonctionnalités PaaS qui incluent un pare-feu, des stratégies de contrôle d’accès réseau doivent être configurées pour autoriser uniquement le trafic d’entrée nécessaire en fonction des exigences de l’application.  

|Ressource | Lien|
|---|---|
|Règles de pare-feu IP Azure SQL Database et SQL Data Warehouse | [https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)|
|Sécurité réseau de stockage | [https://docs.microsoft.com/azure/storage/common/storage-network-security](https://docs.microsoft.com/azure/storage/common/storage-network-security)|
|

## <a name="paas-authentication-and-access-control"></a>Authentification PaaS et contrôle d’accès

Selon la capacité PaaS et son objectif, l’utilisation de contrôles réseau pour restreindre l’accès peut ne pas être possible ou pratique. Dans le cadre du modèle de sécurité multiniveau pour PaaS, Azure fournit une série de mécanismes d’authentification et de contrôle d’accès pour restreindre l’accès à un service, même si le trafic réseau est autorisé. Les mécanismes d’authentification standard pour les fonctionnalités PaaS incluent Azure Active Directory, une authentification au niveau de l’application, et des clés ou signatures d’accès partagées. Une fois qu’un utilisateur est identifié de manière sécurisée, des rôles peuvent être utilisés pour contrôler les actions que l’utilisateur peut effectuer. Ces outils peuvent être utilisés en guise d’alternative ou de mesure complémentaire pour restreindre l’accès aux services.

|Ressource | Lien|
|---|---|
|Contrôle et autorisation d’accès aux bases de données SQL Database et SQL Data Warehouse | [https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins](https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins)|
|Autorisation pour les services de stockage Azure | [https://docs.microsoft.com/rest/api/storageservices/authorization-for-the-Azure-Storage-Services](https://docs.microsoft.com/rest/api/storageservices/authorization-for-the-Azure-Storage-Services)|
|

## <a name="azure-policy"></a>Azure Policy

Azure Policy est un composant clé permettant d’appliquer et de gérer l’intégrité de l’architecture logique de l’environnement Azure. Étant donné la diversité des services et les chemins de trafic réseau en entrée disponibles via les services Azure, il est essentiel que les entités du Commonwealth connaissent les ressources qui existent au sein de leur environnement et les points d’entrée réseau disponibles. Pour s’assurer que des points d’entrée de réseau non autorisés ne soient pas créés dans l’environnement Azure, les entités du Commonwealth doivent utiliser Azure Policy pour contrôler les types de ressources qui peuvent être déployées et la configuration de celles-ci. Les exemples pratiques incluent la restriction des ressources à celles dont l’utilisation est autorisée et approuvée, appliquant un chiffrement HTTPS au Stockage, et nécessitant l’ajout de groupes de sécurité réseau aux sous-réseaux.

|Ressource | Lien|
|---|---|
|Vue d’ensemble d’Azure Policy | [https://docs.microsoft.com/azure/governance/policy/overview](https://docs.microsoft.com/azure/governance/policy/overview)|
|Exemple de stratégie de Types de ressources autorisés | [https://docs.microsoft.com/azure/governance/policy/samples/allowed-resource-types](https://docs.microsoft.com/azure/governance/policy/samples/allowed-resource-types)
|Exemple de stratégie pour vérifier l’existence d’un compte de stockage HTTPS|[https://docs.microsoft.com/azure/governance/policy/samples/ensure-https-storage-account](https://docs.microsoft.com/azure/governance/policy/samples/ensure-https-storage-account)_
|Exemple de stratégie pour forcer l’application d’un groupe de sécurité réseau à un sous-réseau| [https://docs.microsoft.com/azure/governance/policy/samples/nsg-on-subnet](https://docs.microsoft.com/azure/governance/policy/samples/nsg-on-subnet)|
|

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la gestion des flux de trafic de votre environnement Azure vers d’autres réseaux à l’aide de vos composants de passerelle dans Azure, voir l’article sur [la gestion et le contrôle du trafic de sortie de la passerelle](gateway-egress-traffic.md).
