---
title: Contrôle du trafic de sortie dans Azure Australie
description: Éléments clés du contrôle du trafic de sortie dans Azure pour répondre aux exigences du secteur public Australien pour les passerelles Internet sécurisées
author: galey801
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: grgale
ms.openlocfilehash: 7922846d161b7a0cbda32101b6bbb40a1741b323
ms.sourcegitcommit: 15f7b641a67f3d6cf4fb4b4c11eaee18cf335923
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68602087"
---
# <a name="controlling-egress-traffic-in-azure-australia"></a>Contrôle du trafic de sortie dans Azure Australie

Un aspect fondamental de la sécurisation des systèmes de Tâches de configuration initiales est le contrôle du trafic réseau. La restriction de la communication au sel trafic nécessaire pour qu’un système fonctionne réduit le risque de compromission. La visibilité et le contrôle des systèmes externes avec lesquels vos applications et services communiquent vous aident à détecter des systèmes compromis ainsi que des exfiltrations de données tentées ou réussies. Cet article fournit des informations sur le fonctionnement du trafic réseau sortant (de sortie) dans Azure, ainsi que des recommandations relatives à l’implémentation de contrôles de sécurité réseau pour un système connecté à Internet conforme aux préconisations à l’adresse du grand public de l’Australian Cyber Security Centre (ACSC), reprises dans son Manuel de sécurité des informations (Information Security Manual, ISM).

## <a name="requirements"></a>Configuration requise

Les exigences de sécurité globales pour les systèmes du Commonwealth sont définies dans l’ISM. Pour aider les entités du Commonwealth à implémenter la sécurité réseau, l’ACSC a publié le document _ACSC Protect: Implementing Network Segmentation and Segregation_ et, pour aider à la sécurisation des systèmes dans des environnements cloud, elle a publié le document _Cloud Computing Security for Tenants_.

Les documents d’ACSC décrivent le contexte d’implémentation de la sécurité réseau et du contrôle du trafic, et fournissent des recommandations pratiques en matière de conception et de configuration de réseau.

Les conditions clés suivantes pour le contrôle du trafic de sortie dans Azure sont identifiées dans les documents de l’ACSC.

Description|Source
--------------- |------------------
**Implémentez une segmentation et une ségrégation de réseau**. Par exemple, utilisez une architecture à plusieurs niveaux, intégrant des pare-feu basés sur un hôte et des contrôles d’accès au réseau afin de limiter les connectivités réseau entrante et sortante aux ports et protocoles strictement indispensables.| [Cloud computing pour locataires](https://acsc.gov.au/publications/protect/cloud-security-tenants.htm)
**Implémentez une connectivité réseau fiable, offrant une bande passante élevée et une faible latence** entre le locataire (incluant les utilisateurs distants de celui-ci) et le service cloud pour répondre aux exigences de disponibilité du locataire  | [ACSC Protect: Implementing Network Segmentation and Segregation](https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm)
**Appliquez les technologies au-delà de la couche réseau**. Chaque hôte et réseau doit être segmenté et séparé, si possible, au niveau le plus bas pouvant être géré de manière pratique. Dans la plupart des cas, cela s’applique de la couche de liaison de données à la couche application incluse. Cependant, dans des environnements sensibles, une isolation physique peut être appropriée. Les mesures basées sur un hôte et à l’échelle d’un réseau doivent être déployées de manière complémentaire et contrôlées de façon centralisée. L’implémentation d’un pare-feu ou d’une appliance de sécurité en tant qu’unique mesure de sécurité ne suffit pas. |[ACSC Protect: Implementing Network Segmentation and Segregation](https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm)
**Utilisez les principes du moindre privilège et du besoin de savoir**. Si un hôte, un service ou un réseau n’a pas besoin de communiquer avec un autre hôte, service ou réseau, il ne devrait pas être autorisé à le faire. Si un hôte, un service ou un réseau doit communiquer uniquement avec un autre hôte, service ou réseau sur un port ou un protocole spécifique, il devrait être limité à ces port et protocole. L’adoption de ces principes pour un réseau vient en complément de la minimisation des privilèges d’utilisateur et augmente considérablement la posture de sécurité globale de l’environnement. |[ACSC Protect: Implementing Network Segmentation and Segregation](https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm)
**Séparez les hôtes et les réseaux en fonction de leur sensibilité ou de leur importance pour l’exploitation**. Cela peut inclure l’utilisation de composants matériels ou de plateformes variables en fonction des classifications de sécurité, des domaines de sécurité ou des conditions de disponibilité et d’intégrité des hôtes ou réseaux. Plus spécifiquement, séparez les réseaux de gestion et envisagez d’isoler physiquement les réseaux de gestion hors bande pour les environnements sensibles. |[ACSC Protect: Implementing Network Segmentation and Segregation](https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm)
**Identifiez, authentifiez et autorisez l’accès de toutes les entités à toutes les autres entités**. Tous les utilisateurs, hôtes et services doivent avoir accès à tous les autres utilisateurs, hôtes et services, mais uniquement à ceux qui sont indispensables pour l’exécution de leurs tâches ou fonctions spécifiées. Tous les services hérités ou locaux qui contournent ou dégradent les services d’identification, d’authentification et d’autorisation doivent être désactivés autant que possible, et leur utilisation doit être surveillée de près. |[ACSC Protect: Implementing Network Segmentation and Segregation](https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm)
**Implémentez une liste verte du trafic réseau plutôt qu’une liste rouge**. Autorisez l’accès uniquement pour le trafic réseau approprié (trafic identifié, authentifié et autorisé), au lieu de refuser l’accès pour le trafic réseau inapproprié (par exemple, en bloquant une adresse ou un service spécifiques). En termes de stratégie de sécurité, les listes vertes sont supérieures aux listes rouges, et améliorent considérablement votre capacité à détecter et à évaluer des intrusions potentielles. |[ACSC Protect: Implementing Network Segmentation and Segregation](https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm)
**Définissez une liste verte de sites autorisés et bloquez tous les sites non répertoriés** afin de contrer efficacement l’une des techniques d’injection et d’exfiltration de données les plus couramment utilisées par les personnes mal intentionnées. Si les utilisateurs ont légitimement besoin d’accéder à de nombreux sites web ou à une liste de sites en constante et rapide évolution, considérez les coûts liés à une telle implémentation. Une liste verte, même relativement permissive, offre une meilleure sécurité que des listes rouges ou un accès totalement ouvert, tout en réduisant les coûts d’implémentation. Un exemple de liste verte permissive pourrait être celui d’une liste autorisant l’ensemble du sous-domaine australien (« *.au »), ou les 1 000 premiers sites du classement Alexa (après filtrage des domaines DDNS (Dynamic Domain Name System) et autres domaines inappropriés).| [Manuel de sécurité des informations (ISM) pour le secteur public australien](https://www.cyber.gov.au/ism)
|

Cet article fournit des informations et des recommandations concernant le contrôle du trafic réseau sortant de votre environnement Azure. Il couvre les systèmes déployés dans Azure à l’aide des technologies IaaS et PaaS.

L’article sur le [trafic d’entrée de la passerelle](gateway-ingress-traffic.md) traite du trafic réseau entrant dans votre environnement Azure et complète le présent article de manière à couvrir entièrement de contrôle du réseau.

## <a name="architecture"></a>Architecture

Pour contrôler correctement le trafic de sortie, lorsque vous concevez et implémentez la sécurité réseau, vous devez commencer par comprendre le fonctionnement du trafic réseau de sortie au sein d’Azure, tant IaaS et PaaS. Cette section fournit une présentation du traitement du trafic sortant généré par une ressource hébergée dans Azure, ainsi que des contrôles de sécurité disponibles pour limiter et contrôler ce trafic.

### <a name="architecture-components"></a>Composants de l’architecture

Le diagramme architectural présenté ici décrit les chemins possibles que le trafic réseau peut emprunter lors de la sortie d’un système déployé dans un sous-réseau de réseau virtuel. Le trafic au sein d’un réseau virtuel est géré et régi au niveau d’un sous-réseau, avec des règles de routage et de sécurité qui s’appliquent à ses ressources. Les composants liés au trafic de sortie sont divisés en systèmes, itinéraires effectifs, types de tronçons suivants, contrôles de sécurité et sortie PaaS.

![Architecture](media/egress-traffic.png)

### <a name="systems"></a>Systèmes

Les systèmes sont les ressources Azure et composants associés qui génèrent du trafic sortant au sein d’un sous-réseau IP faisant partie d’un réseau virtuel.

| Composant | Description |
| --- | ---|
|Réseau virtuel (VNet) | Un réseau virtuel est une ressource fondamentale dans Azure, qui fournit une plateforme et un périmètre pour le déploiement de ressources et la communication. Le réseau virtuel existe dans une région Azure et définit l’espace d’adressage IP ainsi que les limites de routage pour les ressources qui y sont intégrées, telles que les machines virtuelles.|
|Subnet | Un sous-réseau est une plage d’adresses IP créée au sein d’un réseau virtuel. Il est possible de créer plusieurs sous-réseaux au sein d’un réseau virtuel afin de segmenter celui-ci.|
|Interface réseau| Une interface réseau est une ressource qui existe dans Azure. Elle est connectée à une machine virtuelle et reçoit une adresse IP routable non-Internet privée du sous-réseau auquel elle est associée. Cette adresse IP est affectée de façon dynamique ou statique via Azure Resource Manager.|
|Adresses IP publiques| Une adresse IP publique est une ressource qui réserve l’une des adresses IP publiques routables via Internet appartenant à Microsoft de la région spécifiée en vue de son utilisation dans le réseau virtuel. Elle peut être associée à une interface réseau ou à une ressource PaaS spécifiques, ce qui permet à la ressource de communiquer avec Internet, ExpressRoute et d’autres systèmes PaaS.|
|

### <a name="routes"></a>Itinéraires

Le chemin que le trafic de sortie emprunte dépend des itinéraires effectifs vers cette ressource, qui sont déterminés par la combinaison des itinéraires appris de toutes les sources possibles et de l’application de la logique de routage Azure.

| Composant | Description |
|--- | ---|
|Itinéraires système| Azure crée des itinéraires système automatiquement et assigne les itinéraires pour chaque sous-réseau dans un réseau virtuel. Il n’est pas possible de créer ou supprimer des itinéraires système, mais il est possible d’en personnaliser certains. Azure crée des itinéraires système par défaut pour chaque sous-réseau et ajoute des itinéraires par défaut facultatifs supplémentaires à des sous-réseaux spécifiques ou à chaque sous-réseau en cas d’utilisation de fonctionnalités Azure spécifiques.|
|Points de terminaison de service| Les points de terminaison de service fournissent une connexion de sortie privée directe d’un sous-réseau vers une fonctionnalité PaaS spécifique. Les points de terminaison de service qui sont disponibles uniquement pour un sous-ensemble de fonctionnalités PaaS offrent des performances et une sécurité accrues pour les ressources d’un réseau virtuel accédant à PaaS.|
|Tables de routage| Une table de routage est une ressource dans Azure qui peut être créée pour spécifier des itinéraires définis par l’utilisateur (UDR), capable de compléter ou de remplacer des itinéraires système ou des itinéraires appris via le protocole BGP (Border Gateway Protocol). Chaque UDR spécifie un réseau, un masque de réseau et un tronçon suivant. Une table de routage peut être associée à un ou plusieurs sous-réseaux, mais un sous-réseau ne peut être associé qu’à zéro ou une table de routage.|
|Protocole de passerelle frontière (BGP)| Le protocole BGP est un protocole de routage système inter-autonome. Un système autonome est un réseau ou un groupe de réseaux administrés en commun avec des stratégies de routage communes. Le protocole BGP permet l’échange d’informations de routage entre systèmes autonomes. Il peut être intégré dans des réseaux virtuels via des passerelles de réseau virtuel.|
|

### <a name="next-hop-types-defined"></a>Types de tronçons suivants définis

Chaque itinéraire dans Azure comprend la plage réseau et le masque de sous-réseau associé, ainsi que le tronçon suivant qui détermine la façon dont le trafic est traité.

Type de tronçon suivant | Description
---- | ----
**Réseau virtuel** | Achemine le trafic entre des plages d’adresses au sein de l’espace d’adressage d’un réseau virtuel. Azure crée un itinéraire avec un préfixe d’adresse qui correspond à chaque plage d’adresses définie dans l’espace d’adressage d’un réseau virtuel. Si plusieurs plages d’adresses sont définies dans l’espace d’adressage de réseau virtuel, Azure crée un itinéraire individuel pour chaque plage d’adresses. Azure achemine automatiquement le trafic entre sous-réseaux au sein d’un réseau virtuel en utilisant les itinéraires créés pour chaque plage d’adresses.
**VNet Peering** | Quand un peering (ou apparairage) de réseau virtuel est créé entre deux réseaux virtuels, un itinéraire est ajouté pour chaque plage d’adresses de chaque réseau virtuel au réseau virtuel auquel il est associé. Le trafic est routé entre les réseaux virtuels appairés de la même façon que les sous-réseaux au sein d’un réseau virtuel.
**Passerelle de réseau virtuel** | Un ou plusieurs itinéraires avec passerelle de réseau virtuel répertoriés comme le type de tronçon suivant sont ajoutés quand une passerelle de réseau virtuel est ajoutée à un réseau virtuel. Les itinéraires inclus sont ceux qui sont configurés dans la ressource de passerelle de réseau local et tous les itinéraires appris via le protocole BGP.
**Appliance virtuelle** | Une appliance virtuelle exécute généralement une application réseau telle qu’un pare-feu. L’appliance virtuelle permet un traitement supplémentaire du trafic, tel qu’un filtrage, une inspection ou une traduction d’adresses. Chaque itinéraire avec le type de tronçon d’appliance virtuelle doit également spécifier une adresse IP de tronçon suivant.
**VirtualNetworkServiceEndpoint** | Les adresses IP publiques d’un service spécifique sont ajoutées en tant qu’itinéraires vers un sous-réseau avec un tronçon suivant de VirtualNetworkServiceEndpoint quand un point de terminaison de service est activé. Des points de terminaison de service sont activés pour des services individuels sur des sous-réseaux individuels au sein d’un réseau virtuel. Les adresses IP publiques des services Azure changent régulièrement. Azure gère automatiquement les adresses dans la table de routage en cas de changement d’adresses.
**Internet** | Le trafic avec un tronçon suivant d’Internet quitte le réseau virtuel et est automatiquement traduit en adresse IP publique, soit à partir d’un pool dynamique disponible dans la région Azure associée, ou à l’aide d’une adresse IP publique configurée pour cette ressource. Si l’adresse de destination correspond à l’un des services d’Azure, le trafic est routé directement vers le service via le réseau principal d’Azure plutôt que via Internet. Le trafic entre les services Azure ne traverse pas le réseau Internet, quelle que soit la région Azure où le réseau virtuel existe ou la région Azure dans laquelle une instance du service Azure est déployée.
**Aucun** | Tout trafic dont le tronçon suivant est Aucun est supprimé. Azure crée des itinéraires système par défaut pour les préfixes d’adresse réservés avec Aucun pour type de tronçon suivant. Des itinéraires dont le tronçon suivant est None peuvent également être ajoutés à l’aide de tables de routage pour empêcher le routage du trafic vers des réseaux spécifiques.
|

### <a name="security-controls"></a>Contrôles de sécurité

Contrôle | Description
----- | -----
**Groupes de sécurité réseau (NSG)** | Les groupes de sécurité réseau contrôlent le trafic en direction et sortant des ressources de réseau virtuel dans Azure. Les groupes de sécurité réseau appliquent des règles pour les flux de trafic autorisés ou refusés, qui incluent le trafic tant dans Azure qu’entre Azure et des réseaux externes tels que des réseaux locaux ou Internet. Les groupes de sécurité réseau sont appliqués aux sous-réseaux au sein d’un réseau virtuel ou à des interfaces réseau individuelles.
**Pare-feu Azure** | Un Pare-feu Azure est un service managé de sécurité réseau basé sur le cloud, qui protège les ressources de réseau virtuel Azure. Il s’agit d’un service de pare-feu entièrement avec état, pourvu d’une haute disponibilité intégrée et de l’extensibilité du cloud sans limites. Le Pare-feu Azure peut être configuré avec des règles de filtrage réseau traditionnelles basées sur les adresses IP, les protocoles et les ports, mais il prend également en charge le filtrage basé sur les noms de domaine complets (FQDN), les étiquettes de service et l’intelligence des menaces intégrée.
**Appliance virtuelle réseau** | Les appliances virtuelles réseau sont des médias de machines virtuelles qui peuvent fournir à Azure des fonctions de mise en réseau, de sécurité et autres. Les appliances virtuelles réseau prennent en charge des services et fonctionnalités réseau sous la forme de machines virtuelles dans des déploiements et réseaux virtuels. Vous pouvez les utiliser pour répondre à des exigences spécifiques, opérer une intégration avec des outils de gestion et d’exploitation, ou assurer la cohérence avec des produits existants. Azure prend en charge un vaste éventail d’appliances virtuelles réseau tierces, dont des pare-feu d’applications web (WAF), pare-feu, passerelles/routeurs, contrôleurs de livraison d’applications (ADC) et optimiseurs de réseau étendu.
**Stratégies de points de terminaison de service (préversion)** | Les stratégies de points de terminaison de service de réseau virtuel permettent de filtrer le trafic réseau virtuel vers les services Azure, en autorisant uniquement des ressources de service Azure spécifiques sur les points de terminaison de service. Les stratégies de point de terminaison fournissent un contrôle d’accès granulaire pour le trafic de réseau virtuel vers les services Azure.
**Azure Policy** | Azure Policy est un service dans Azure pour la création, l’attribution et la gestion de stratégies. Ces stratégies utilisent des règles pour contrôler les types de ressources qui peuvent être déployées et la configuration de celles-ci. Des stratégies peuvent être utilisées pour appliquer la conformité en empêchant le déploiement de ressources ne répondant pas à certaines exigences, ou pour surveiller l’état de conformité.
|

### <a name="paas-egress"></a>Sortie PaaS

La majorité des ressources PaaS ne génèrent pas de trafic sortant, mais répondent à des demandes entrantes (d’Application Gateway, de Stockage, de SQL Database, etc.) ou relaient des données d’autres ressources (telles que Service Bus et Azure Relay). Les flux de communication réseau entre les ressources PaaS, par exemple d’App Services vers le Stockage ou des bases de données SQL Database, sont contrôlés et contenus par Azure, et sécurisés à l’aide de contrôles de configuration des identités et d’autres ressources plutôt que par une segmentation ou une séparation de réseau.

Les ressources PaaS déployées dans un réseau virtuel reçoivent des adresses IP dédiées et sont soumises à des contrôles de routage et groupes de sécurité réseau de la même façon que d’autres ressources du réseau virtuel. Les ressources PaaS qui n’existent pas au sein d’un réseau virtuel utilisent un pool d’adresses IP qui sont partagées entre toutes les instances de la ressource, qui sont publiées par le biais de la documentation de Microsoft ou peuvent être déterminées via Azure Resource Manager.

## <a name="general-guidance"></a>Conseils d'ordre général

Pour concevoir et créer des solutions sécurisées dans Azure, il est essentiel de comprendre et de contrôler le trafic réseau afin que seules des communications identifiées et autorisées puissent se produire. L’objectif de ce guide et des conseils relatifs à des composants spécifiques dans les sections ultérieures est de décrire les outils et les services qui peuvent être utilisés pour appliquer les principes décrits dans [ACSC Protect: Implementing Network Segmentation and Segregation](https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm) aux charges de travail Azure. Cela comprend la description de la manière de créer une architecture virtuelle pour sécuriser des ressources quand il n’est pas possible d’appliquer les mêmes contrôles physiques et de réseau traditionnels que dans un environnement local.

### <a name="guidance"></a>Conseils

* Limiter le nombre de points de sortie pour les réseaux virtuels
* Remplacer l’itinéraire par défaut du système pour tous les sous-réseaux qui n’ont pas besoin d’une communication sortante directe vers Internet
* Concevoir et implémenter une architecture réseau complète pour identifier et contrôler tous les points d’entrée et de sortie des ressources Azure
* Envisager d’utiliser une conception de réseau Hub and spoke pour les réseaux virtuels, comme décrit dans la documentation sur le Centre de données virtuel Microsoft
* Utiliser des produits avec des fonctionnalités de sécurité intégrées pour les connexions sortantes vers Internet (par exemple, Pare-feu Azure, appliances virtuelles réseau ou proxys web)
* Utiliser des contrôles d’identité tels que l’accès en fonction du rôle, l’accès conditionnel et l’authentification multifacteur pour limiter des privilèges de configuration réseau
* Implémenter des verrous pour empêcher la modification ou la suppression d’éléments clés de la configuration réseau
* Déployer PaaS dans une configuration intégrée de réseau virtuel pour augmenter la ségrégation et le contrôle
* Implémenter ExpressRoute pour la connectivité avec les réseaux locaux
* Implémenter des VPN pour l’intégration avec des réseaux externes
* Utiliser Azure Policy pour limiter les régions et ressources à celles qui sont strictement nécessaires pour la fonctionnalité du système
* Utiliser Azure Policy pour appliquer une configuration de sécurité à des ressources
* Tirez parti de Network Watcher et d’Azure Monitor pour la journalisation, l’audit et la visibilité du trafic réseau au sein d’Azure

### <a name="resources"></a>Ressources

Item | Lien
-----------| ---------
_Documents relatifs à conformité à la réglementation et aux politiques australiennes incluant des préconisations à l’adresse du grand public_ | [https://aka.ms/au-irap](https://aka.ms/au-irap)
_Centre de données virtuel Azure_ | [https://docs.microsoft.com/azure/architecture/vdc/networking-virtual-datacenter](https://docs.microsoft.com/azure/architecture/vdc/networking-virtual-datacenter)
_Segmentation de réseau de l’ACSC_ | [https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm](https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm)
_Sécurité du cloud pour les locataires de l’ACSC_ | [https://acsc.gov.au/publications/protect/cloud-security-tenants.htm](https://acsc.gov.au/publications/protect/cloud-security-tenants.htm)
_Manuel sur la sécurité des informations de l’ACSC_ | [https://acsc.gov.au/infosec/ism/index.htm](https://acsc.gov.au/infosec/ism/index.htm)
|

## <a name="component-guidance"></a>Guide sur les composants

Cette section fournit des conseils supplémentaires sur les composants individuels, qui sont pertinents pour le trafic de sortie pour les systèmes déployés dans Azure. Chaque section décrit l’intention du composant spécifique avec des liens vers de la documentation et des conseils de configuration qui peuvent être utilisés pour faciliter les activités de conception et de création.

### <a name="systems-security"></a>Sécurité des systèmes

Toutes les communications vers des ressources au sein d’Azure passent par l’infrastructure réseau gérée par Microsoft, qui fournit des fonctionnalités de connectivité et de sécurité. Une série de protections sont automatiquement mises en place par Microsoft pour protéger la plateforme et l’infrastructure réseau Azure, et des fonctionnalités supplémentaires sont disponibles en tant que services dans Azure pour contrôler le trafic réseau et établir la segmentation et la séparation de réseau.

### <a name="virtual-network-vnet"></a>Réseau virtuel (VNet)

Les réseaux virtuels sont les blocs de construction fondamentaux de la mise en réseau dans Azure. Les réseaux virtuels définissent un espace d’adressage IP et une limite de routage à utiliser dans divers systèmes. Les réseaux virtuels sont divisés en sous-réseaux et tous les sous-réseaux au sein d’un réseau virtuel disposent d’un itinéraire réseau direct entre eux. En utilisant des passerelles de réseau virtuel (ExpressRoute ou VPN), les systèmes au sein d’un réseau virtuel peuvent s’intégrer avec des environnements locaux et externes. Et grâce à la traduction d’adresses réseau (NAT) et à l’allocation d’adresses IP publiques assurées par Azure, les systèmes peuvent se connecter à Internet ou à d’autres régions et services Azure. La compréhension des réseaux virtuels, ainsi que des paramètres de configuration et du routage associés sont essentiels pour comprendre et contrôler le trafic réseau de sortie.

Les réseaux virtuels constituant l’espace d’adressage de base et la limite de routage dans Azure, ils peuvent être utilisés comme des blocs de construction de base pour la segmentation et la séparation de réseau.

| Ressource | Lien |
| --- | --- |
| *Vue d’ensemble des réseaux virtuels* | [https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) |
| *Guide pratique pour la planification de réseaux virtuels*  | [https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm) |
| *Guide de démarrage rapide pour la création de réseau virtuel* | [https://docs.microsoft.com/azure/virtual-network/quick-create-portal](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)
|

### <a name="subnet"></a>Subnet

Les sous-réseaux sont des composants essentiels pour la segmentation et la séparation de réseau dans Azure. Des sous-réseaux peuvent être utilisés pour séparer des systèmes. Un sous-réseau est la ressource au sein d’un réseau virtuel où les groupes de sécurité réseau, les tables de routage et les points de terminaison de service sont appliqués. Les sous-réseaux peuvent être utilisés comme adresses source et de destination pour les règles de pare-feu et les listes de contrôle d’accès.

Les sous-réseaux au sein d’un réseau virtuel doivent être planifiés pour répondre aux besoins des charges de travail et des systèmes. Les personnes impliquées dans la conception ou l’implémentation de sous-réseaux doivent se référer aux recommandations de l’ACSC concernant la segmentation de réseau afin de déterminer comment les systèmes doivent être regroupés au sein d’un sous-réseau.

|Ressource|Lien|
|---|---|
|*Ajouter, modifier ou supprimer un sous-réseau de réseau virtuel* | [https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet)
|

### <a name="network-interface"></a>interface réseau

Les interfaces réseau sont la source de tout le trafic de sortie d’une machine virtuelle. Les interfaces réseau permettent la configuration de l’adressage IP et peuvent être utilisées pour appliquer des groupes de sécurité réseau ou pour router le trafic via une appliance virtuelle réseau. Les interfaces réseau des machines virtuelles doivent être planifiées et configurées de manière appropriée pour les objectifs globaux de segmentation et de séparation de réseau.

|Ressource|Lien|
|---|---|
|*Créer, modifier ou supprimer une interface réseau* | [https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface) |
|*Adressage IP d’interface réseau*               | [https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#private-ip-addresses](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#private-ip-addresses)
|

### <a name="vnet-integrated-paas"></a>PaaS intégrée à un réseau virtuel

Une PaaS peut fournir une fonctionnalité et une disponibilité accrues, ainsi que réduire la charge de gestion, mais doit être sécurisée de manière appropriée. Pour augmenter le contrôle, appliquer une segmentation de réseau ou fournir un point de sortie sécurisé pour les applications et services, de nombreuses fonctionnalités de PaaS peuvent être intégrées avec un réseau virtuel.

En utilisant une PaaS comme partie intégrante d’une architecture de système ou d’application, Microsoft fournit plusieurs mécanismes permettant de déployer une PaaS dans un réseau virtuel. La méthodologie de déploiement peut aider à limiter l’accès tout en offrant une connectivité et une intégration avec les systèmes et applications internes. Il peut s’agir, par exemple, d’environnements App Service ou d’instances managées SQL.

Lors du déploiement de PaaS dans un réseau virtuel où des contrôles de routage et de groupe de sécurité réseau ont été implémentés, il est essentiel de comprendre les exigences de communication spécifiques de la ressource, notamment l’accès à la gestion à partir des services Microsoft et le chemin qu’empruntera le trafic des communications en réponse aux demandes entrantes de ces services.

| Ressource  | Lien  |
| --- | --- |
| *Intégration d’un réseau virtuel pour les services Azure* | [https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services) |
| *Guide pratique pour l’intégration de votre application avec un réseau virtuel Azure* | [https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet)
|

### <a name="public-ip"></a>Adresse IP publique

Des adresses IP publiques sont utilisées lors de la communication à l’extérieur d’un réseau virtuel. Cela comprend les ressources PaaS et tous les itinéraires avec un tronçon suivant d’Internet. Les entités du Commonwealth doivent planifier avec soin l’allocation d’adresses IP publiques et les affecter uniquement à des ressources soumises à une exigence authentique. Une pratique de conception générale consiste à allouer des adresses IP publiques aux points de sortie contrôlés pour le réseau virtuel, tels que le Pare-feu Azure, la passerelle VPN ou les appliances virtuelles réseau.

|Ressource|Lien|
|---|---|
|*Vue d’ensemble des adresses IP publiques*  | [https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#public-ip-addresses](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#public-ip-addresses) |
|*Créer, modifier ou supprimer une adresse IP publique* | [https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address)
|

## <a name="effective-routes"></a>Itinéraires effectifs

Les itinéraires effectifs sont l’ensemble des itinéraires déterminés par la combinaison d’itinéraires système, de points de terminaison de service, de tables de routage et de BGP, ainsi que par l’application de la logique de routage Azure. Lorsque le trafic sortant est envoyé à partir d’un sous-réseau, Azure choisit un itinéraire sur la base de l’adresse IP de destination, à l’aide de l’algorithme de correspondance de préfixe le plus long. Si plusieurs itinéraires contiennent le même préfixe d’adresse, Azure choisit le type d’itinéraire en se basant sur l’ordre de priorité suivant :

1. Itinéraire défini par l’utilisateur
2. Itinéraire BGP
3. Itinéraire du système

Il est important de noter que les itinéraires système pour le trafic lié au réseau virtuel, aux peerings de réseaux virtuels ou aux points de terminaison de service de réseau virtuel, sont les itinéraires sélectionnés par défaut, même si les itinéraires BGP sont plus spécifiques.

Les personnes impliquées dans la conception ou l’implémentation de topologies de routage dans Azure doivent comprendre comment Azure route le trafic, et développer une architecture qui équilibre les fonctionnalités nécessaires des systèmes avec la sécurité et la visibilité requises. Il convient de veiller à planifier l’environnement de manière appropriée afin d’éviter des interventions excessives et des exceptions aux comportements de routage, car cela augmente la complexité et peut rendre la résolution des problèmes et la détection d’erreurs plus difficiles et laborieuses.

| Ressource | Lien  |
| --- | --- |
| *Afficher les itinéraires effectifs* | <https://docs.microsoft.com/azure/virtual-network/manage-route-table#view-effective-routes> |
|

### <a name="system-routes"></a>Itinéraires système

Pour [les itinéraires système](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#system-routes), les personnes impliquées dans la conception ou l’implémentation de réseaux virtuels doivent comprendre les itinéraires système par défaut et les options disponibles pour compléter ou remplacer ces itinéraires.

### <a name="service-endpoints"></a>Points de terminaison de service

L’activation des [points de terminaison de service](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) sur un sous-réseau fournit un chemin de communication direct vers la ressource PaaS associée. Cela peut améliorer les performances et la sécurité en restreignant le chemin de communication disponible à ce service uniquement. L’utilisation de points de terminaison de service introduit un chemin d’exfiltration potentielle de données, car la configuration par défaut permet d’accéder à toutes les instances du service PaaS plutôt qu’aux instances spécifiques requises pour une application ou un système.

Les entités du Commonwealth doivent évaluer le risque associé à la fourniture d’un accès direct à la ressource PaaS, y compris la probabilité que le chemin d’accès soit utilisé de façon inappropriée ainsi que la conséquence d’une telle utilisation.

Pour réduire les risques potentiels associés aux points de terminaison de service, implémentez autant que possible des stratégies de point de terminaison de service ou envisagez d’activer des points de terminaison de service sur un Pare-feu Azure ou un sous-réseau d’appliance virtuelle réseau, ainsi que de router le trafic de sous-réseaux via ceux-ci quand un filtrage, une surveillance ou une inspection supplémentaires sont applicables.

|Ressource|Lien|
|---|---|
|*Didacticiel : Restreindre l’accès réseau aux ressources PaaS avec des points de terminaison de service de réseau virtuel en utilisant le portail Azure* |[https://docs.microsoft.com/azure/virtual-network/tutorial-restrict-network-access-to-resources](https://docs.microsoft.com/azure/virtual-network/tutorial-restrict-network-access-to-resources)|
|

### <a name="route-tables"></a>Tables de routage

Les tables de routage fournissent un mécanisme configuré par l’administrateur pour le contrôle du trafic réseau dans Azure. Des tables de routage peuvent être utilisées pour transférer le trafic via un pare-feu ou une appliance virtuelle réseau Azure, se connecter directement à des ressources externes ou remplacer des itinéraires système Azure. Des tables de routage peuvent également être utilisées pour empêcher que des réseaux appris par le biais d’une passerelle de réseau virtuel soient mis à la disposition des ressources d’un sous-réseau en désactivant la propagation d’itinéraire de passerelle de réseau virtuel.

|Ressource|Lien|
|---|---|
|*Concepts de routage – Itinéraires personnalisés* |[https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#custom-routes](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#custom-routes)|
|*Didacticiel : Router le trafic réseau* |[https://docs.microsoft.com/azure/virtual-network/tutorial-create-route-table-portal](https://docs.microsoft.com/azure/virtual-network/tutorial-create-route-table-portal)|
|

### <a name="border-gateway-protocol-bgp"></a>Protocole de passerelle frontière (BGP)

Les passerelles de réseau virtuel peuvent utiliser le protocole BGP pour échanger de façon dynamique des informations de routage avec des réseaux locaux ou d’autres réseaux externes. Le protocole BGP s’applique à un réseau virtuel quand il est configuré par le biais d’une passerelle réseau virtuel ExpressRoute via un peering privé ExpressRoute et quand il est activé sur une passerelle VPN Azure.

Les personnes impliquées dans la conception ou l’implémentation de réseaux virtuels et de passerelles de réseau virtuel dans Azure doivent prendre le temps de comprendre le comportement et les options de configuration disponibles pour le protocole BGP dans Azure.

|Ressource|Lien|
|---|---|
|*Concepts de routage : protocole BGP* | [https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#next-hop-types-across-azure-tools](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#next-hop-types-across-azure-tools)|
|*Configuration requise pour le routage ExpressRoute* | [https://docs.microsoft.com/azure/expressroute/expressroute-routing](https://docs.microsoft.com/azure/expressroute/expressroute-routing)|
|*À propos du protocole BGP avec une passerelle VPN Azure* |[https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-overview](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-overview)|
|*Didacticiel : Configurer un réseau VPN de site à site via un peering Microsoft ExpressRoute* |[https://docs.microsoft.com/azure/expressroute/site-to-site-vpn-over-microsoft-peering](https://docs.microsoft.com/azure/expressroute/site-to-site-vpn-over-microsoft-peering)|
|

## <a name="next-hop-types"></a>Types de tronçons suivants

### <a name="virtual-network"></a>Réseau virtuel

Les itinéraires avec un tronçon suivant de réseau virtuel sont ajoutés automatiquement en tant qu’itinéraires système, mais peuvent également être ajoutés à des itinéraires définis par l’utilisateur pour rediriger le trafic vers le réseau virtuel dans les instances où l’itinéraire système a été remplacé.

### <a name="vnet-peering"></a>Peering de réseaux virtuels

Un peering de réseaux virtuels permet la communication entre deux réseaux virtuels disparates. La configuration de peering de réseaux virtuels doit être activée sur chaque réseau virtuel, mais il n’est pas nécessaire que ceux-ci se trouvent dans la même région ou le même abonnement, ou soient associés au même locataire Azure Active Directory (Azure AD).

Lors de la configuration d’un peering de réseaux virtuels, il est essentiel que les individus impliqués dans la conception ou l’implémentation de celui-ci comprennent les quatre paramètres de configuration associés et la façon dont ils s’appliquent à chaque côté du peering :

1. **Autoriser l’accès au réseau virtuel** : Sélectionnez **Activé** (par défaut) pour activer la communication entre les deux réseaux virtuels. L’activation de la communication entre les réseaux virtuels permet aux ressources connectées à ceux-ci de communiquer entre elles avec les mêmes bande passante et latence que si elles étaient connectées au même réseau virtuel.
2. **Autoriser le trafic transféré :** Activez cette case pour autoriser le trafic *transféré* par un réseau (trafic ne provenant pas du réseau virtuel) à circuler vers ce réseau virtuel via un peering. Ce paramètre est essentiel à l’implémentation d’une topologie de réseau Hub and Spoke.
3. **Autoriser le transit par passerelle :** Activez cette case pour autoriser le réseau virtuel appairé à utiliser la passerelle de réseau virtuel attachée à ce réseau virtuel. L’option *Autoriser le transit par passerelle* est activée sur le réseau virtuel avec la ressource de passerelle de réseau virtuel, mais s’applique uniquement si l’option *Utiliser des passerelles distantes* est activée sur l’autre réseau virtuel.
4. **Utiliser des passerelles distantes :** Activez cette case pour autoriser le trafic en provenance de ce réseau virtuel à passer par une passerelle de réseau virtuel attachée au réseau virtuel avec lequel vous effectuez le peering. L’option *Utiliser des passerelles distantes* est activée sur le réseau virtuel sans passerelle de réseau virtuel, et ne s’applique que si l’option *Autoriser le transit par passerelle* est activée sur l’autre réseau virtuel.

|Ressource|Lien|
|---|---|
| Concepts : Peering de réseau virtuel                   | [https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) |
| Créer, modifier ou supprimer un peering de réseau virtuel | [https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering)|
|

### <a name="virtual-network-gateway"></a>Passerelle de réseau virtuel

Les passerelles de réseau virtuel offrent un mécanisme d’intégration des réseaux virtuels avec des réseaux externes, tels que des environnements locaux, des environnements partenaires et d’autres déploiements cloud. Les deux types de passerelles de réseau virtuel sont ExpressRoute et VPN.

#### <a name="expressroute-gateway"></a>Passerelle ExpressRoute

Les passerelles ExpressRoute fournissent un point de sortie du réseau virtuel vers un environnement local, et doivent être déployées pour répondre aux exigences de sécurité, de disponibilité, de coût et de performances. Les passerelles ExpressRoute fournissent une bande passante réseau définie et occasionnent des coûts d’utilisation après le déploiement. Les réseaux virtuels ne peuvent avoir qu’une seule passerelle ExpressRoute, mais ils peuvent être connectés à plusieurs circuits ExpressRoute et être exploités par plusieurs réseaux virtuels via un peering de réseaux virtuels permettant de partager la bande passante et la connectivité. Veillez à configurer le routage entre les environnements locaux et les réseaux virtuels à l’aide de passerelles ExpressRoute pour garantir une connectivité de bout en bout à l’aide de points de sortie de réseau connus et contrôlés. Les entités du Commonwealth utilisant une passerelle ExpressRoute sur un peering privé ExpressRoute doivent également déployer des appliances virtuelles réseau afin d’établir une connectivité VPN à l’environnement local pour la conformité aux préconisations à l’adresse du grand public de l’ACSC.

Il est important de noter que les passerelles ExpressRoute imposent des restrictions sur les plages d’adresses, les communautés et d’autres éléments de configuration spécifiques échangés via le protocole BGP.

| Ressource  | Lien  |
|---|---|
| Vue d’ensemble de la passerelle ExpressRoute | [https://docs.microsoft.com/azure/expressroute/expressroute-about-virtual-network-gateways](https://docs.microsoft.com/azure/expressroute/expressroute-about-virtual-network-gateways) |
| Configurer une passerelle de réseau virtuel pour ExpressRoute | [https://docs.microsoft.com/azure/expressroute/expressroute-howto-add-gateway-portal-resource-manager](https://docs.microsoft.com/azure/expressroute/expressroute-howto-add-gateway-portal-resource-manager)
|

#### <a name="vpn-gateway"></a>Passerelle VPN

Une passerelle VPN Azure fournit un point réseau de sortie du réseau virtuel vers un réseau externe pour une connectivité de site à site sécurisée. Les passerelles VPN fournissent une bande passante réseau définie et occasionnent des coûts d’utilisation après déploiement. Les entités du Commonwealth utilisant une passerelle VPN doivent s’assurer que celle-ci est configurée conformément aux préconisations à l’adresse du grand public de l’ACSC. Les réseaux virtuels ne peuvent avoir qu’une seule passerelle VPN, mais celle-ci peut être configurée avec plusieurs tunnels et exploitée par plusieurs réseaux virtuels via un peering de réseaux virtuels permettant à plusieurs réseaux virtuels de partager la bande passante et la connectivité. Des passerelles VPN peuvent être établies sur Internet ou sur ExpressRoute via le peering Microsoft.

| Ressource  | Lien |
| --- | --- |
| Vue d’ensemble de la passerelle VPN| [https://docs.microsoft.com/azure/vpn-gateway](https://docs.microsoft.com/azure/vpn-gateway)|
| Planification et conception de la passerelle VPN | [https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design)|
| Passerelle VPN Azure dans Azure Australie | [Passerelle VPN Azure dans Azure Australie](vpn-gateway.md)
|

### <a name="next-hop-of-virtual-appliance"></a>Tronçon suivant d’appliance virtuelle

Le tronçon suivant d’une appliance virtuelle offre la possibilité de traiter le trafic réseau en dehors de la topologie de routage et de réseau Azure appliquée aux réseaux virtuels. Des appliances virtuelles peuvent appliquer des règles de sécurité, traduire des adresses, établir des VPN, proxyser le trafic, ainsi qu’assurer diverses autres fonctionnalités. Le tronçon suivant d’une appliance virtuelle est appliqué par le biais d’itinéraires définis par l’utilisateur dans une table de routage, et peut être utilisé pour diriger le trafic vers un Pare-feu Azure, une appliance virtuelle réseau individuelle ou Azure Load Balancer, offrant ainsi la disponibilité à plusieurs appliances virtuelles réseau. Pour utiliser une appliance virtuelle pour le routage, les interfaces réseau associées doivent être activées pour le transfert IP.

| Ressource  | Lien |
| --- | ---|
| Concepts de routage : itinéraires personnalisés | [https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#custom-routes](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#custom-routes) |
| Activer et désactiver le transfert IP | [https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface#enable-or-disable-ip-forwarding](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface#enable-or-disable-ip-forwarding)
|

### <a name="next-hop-of-virtualnetworkserviceendpoint"></a>Tronçon suivant VirtualNetworkServiceEndpoint

Des itinéraires avec un type de tronçon suivant VirtualNetworkServiceEndpoint sont ajoutés uniquement quand un point de terminaison de service est configuré sur un sous-réseau et ne peut pas être configuré manuellement par le biais de tables de routage.

### <a name="next-hop-of-internet"></a>Tronçon suivant d’Internet

Un tronçon suivant d’Internet est utilisé pour atteindre toutes les ressources utilisant une adresse IP publique, ce qui inclut Internet ainsi que les services PaaS et Azure dans les régions Azure. Un tronçon suivant d’Internet ne nécessite pas d’itinéraire par défaut (0.0.0.0/0) couvrant tous les réseaux, mais il peut être utilisé pour activer des chemins de routage vers des services publics spécifiques. Le tronçon suivant d’Internet doit être utilisé pour ajouter des itinéraires aux services autorisés et aux capacités requises pour la fonctionnalité du système, telles que les adresses de gestion Microsoft.

Voici quelques exemples de services qui peuvent être ajoutés à l’aide du tronçon suivant d’Internet :

1. Services de gestion de clés pour l’activation de Windows
2. Gestion d’App Service Environment

|Ressource|Lien|
|---|---|
| Connexions sortantes dans Azure | [https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) |
| Utiliser des itinéraires personnalisés Azure pour permettre l’activation d’un serveur gestionnaire de clés (KMS) | [https://docs.microsoft.com/bs-latn-ba/azure/virtual-machines/troubleshooting/custom-routes-enable-kms-activation](https://docs.microsoft.com/bs-latn-ba/azure/virtual-machines/troubleshooting/custom-routes-enable-kms-activation) |
| Verrouiller un environnement App Service  | [https://docs.microsoft.com/azure/app-service/environment/firewall-integration](https://docs.microsoft.com/azure/app-service/environment/firewall-integration) |
|

### <a name="next-hop-of-none"></a>Tronçon suivant Aucun

Un tronçon suivant None peut être utilisé pour empêcher toute communication vers un réseau spécifique. Contrairement à un groupe de sécurité réseau qui contrôle si le trafic est autorisé à emprunter un chemin réseau disponible, l’utilisation d’un tronçon suivant None supprime complètement ce chemin. Soyez vigilant lorsque vous créez des itinéraires avec un tronçon suivant None, en particulier lors de leur application à un itinéraire par défaut 0.0.0.0/0, car cela peut avoir des conséquences indésirables et rendre la résolution des problèmes système complexe et laborieuse.

## <a name="security"></a>Sécurité

L’implémentation de contrôles de segmentation et de séparation de réseau sur des fonctionnalités IaaS et PaaS est obtenue par une sécurisation des fonctionnalités elles-mêmes et par l’implémentation de chemins de communication contrôlés à partir des systèmes appelés à communiquer avec les fonctionnalités.

La conception et la création de solutions dans Azure sont un processus de création d’une architecture logique permettant de comprendre, de contrôler et de surveiller les ressources réseau partout où Azure est présent. Cette architecture logique est un logiciel défini au sein de la plateforme Azure, qui remplace une topologie de réseau physique telle qu’implémentée dans les environnements réseau traditionnels.

L’architecture logique créée doit fournir la fonctionnalité nécessaire à son usage, ainsi que la visibilité et le contrôle nécessaires pour la sécurité et l’intégrité.

L’obtention de ce résultat repose sur l’implémentation des outils de segmentation et de séparation de réseau nécessaires, ainsi que sur la protection et l’application de la topologie de réseau et l’implémentation de ces outils.

### <a name="network-security-groups-nsgs"></a>Groupes de sécurité réseau (NSG)

Les groupes de sécurité réseau sont utilisés pour spécifier le trafic entrant et sortant autorisé pour un sous-réseau ou une interface réseau spécifique. Lors de la configuration de groupes de sécurité réseau, les entités du Commonwealth doivent utiliser une approche de liste verte dans laquelle des règles sont configurées pour autoriser le trafic nécessaire, avec une règle par défaut configurée pour refuser tout trafic ne correspondant pas à une instruction d’autorisation spécifique. Lors de la planification et de la configuration de groupes de sécurité réseau, vous devez veiller à ce que tout le trafic entrant et sortant nécessaire soit capturé de manière appropriée. Cela inclut l’identification et la compréhension de toutes les plages d’adresses IP privées utilisées dans les réseaux virtuels et l’environnement local, ainsi que de services Microsoft spécifiques tels qu’Azure Load Balancer et les exigences de gestion de PaaS. Les personnes impliquées dans la conception et l’implémentation des groupes de sécurité réseau doivent également comprendre l’utilisation des étiquettes de service et des groupes de sécurité d’application pour créer des règles de sécurité extrêmement précises spécifiques du service et de l’application.

Il est important de noter que la configuration par défaut d’un groupe de sécurité réseau autorise le trafic sortant vers toutes les adresses au sein du réseau virtuel et toutes les adresses IP publiques.

|Ressource|Lien|
|---|---|
|Vue d’ensemble de la sécurité réseau | [https://docs.microsoft.com/azure/virtual-network/security-overview](https://docs.microsoft.com/azure/virtual-network/security-overview)|
|Créer, changer ou supprimer un groupe de sécurité réseau | [https://docs.microsoft.com/azure/virtual-network/manage-network-security-group](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)|
|

### <a name="azure-firewall"></a>Pare-feu Azure

Le Pare-feu Azure peut être utilisé pour créer une topologie de réseau Hub and Spoke et appliquer des contrôles de sécurité réseau centralisés. Le Pare-feu Azure peut être utilisé pour répondre aux exigences nécessaires de l’ISM pour le trafic sortant en implémentant une approche de liste verte où seuls les protocoles, adresses IP, ports et noms de domaine complets requis pour assurer la fonctionnalité du système sont autorisés. Les entités du Commonwealth doivent adopter une approche basée sur les risques pour déterminer si les capacités de sécurité fournies par le Pare-feu Azure sont suffisantes pour répondre à leurs besoins. Pour les scénarios où des capacités de sécurité supplémentaires en plus de celles fournies par le Pare-feu Azure sont requises, les entités du Commonwealth doivent envisager d’implémenter des appliances virtuelles réseau.

|Ressource|Lien|
|---|---|
|*Documentation sur le Pare-feu Azure* | [https://docs.microsoft.com/azure/firewall](https://docs.microsoft.com/azure/firewall)|
|*Didacticiel : Déployer et configurer un Pare-feu Azure dans un réseau hybride à l’aide d’Azure PowerShell* | [https://docs.microsoft.com/azure/firewall/tutorial-hybrid-ps](https://docs.microsoft.com/azure/firewall/tutorial-hybrid-ps)|
|

### <a name="network-virtual-appliances-nvas"></a>Appliances virtuelles réseau

Vous pouvez utiliser des appliances virtuelles réseau pour créer une topologie de réseau Hub and Spoke, fournir des capacités réseau améliorées ou complémentaires ou proposer une alternative aux mécanismes de réseau Azure afin d’offrir un support et une gestion familiers et cohérentes avec les services réseau locaux. Des appliances virtuelles réseau peuvent être déployées pour répondre à des exigences de sécurité spécifiques telles que des scénarios où il est nécessaire de connaître l’identité associée au trafic réseau, au déchiffrement HTTPS, à l’inspection du contenu, au filtrage ou à d’autres fonctionnalités de sécurité. Les appliances virtuelles réseau doit être déployées dans une configuration hautement disponible, et les personnes impliquées dans leur conception ou leur implémentation doivent consulter la documentation appropriée du fournisseur pour obtenir des instructions sur le déploiement dans Azure.

|Ressource|Lien|
|---|---|
|*Déployer des appliances virtuelles réseau hautement disponibles* | [https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha)|
|

### <a name="service-endpoint-policies-preview"></a>Stratégies de points de terminaison de service (préversion)

Configurez des stratégies de point de terminaison de service sur la base de la disponibilité du service et de l’évaluation des risques de sécurité en lien avec la probabilité d’exfiltration de données et l’incidence possible de celle-ci. Des stratégies de point de terminaison de service doivent être envisagées pour le Stockage Azure, et gérées au cas par cas pour d’autres services en fonction du profil de risque associé.

| Ressource | Lien  |
| --- | --- |
| *Vue d’ensemble des stratégies de points de terminaison de service* | [https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-overview](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-overview) |
| *Créer, modifier ou supprimer une stratégie de point de terminaison de service à l’aide du portail Azure* | [https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-portal](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-portal)
|

### <a name="azure-policy"></a>Azure Policy

Azure Policy est un composant clé permettant d’appliquer et de gérer l’intégrité de l’architecture logique de l’environnement Azure. De nombreux services et chemins de trafic réseau de sortie sont disponibles via les services Azure. Il est essentiel que les entités du Commonwealth connaissent les ressources qui existent au sein de leur environnement et les points de sortie de réseau disponibles. Pour s’assurer que des points de sortie de réseau non autorisés ne soient pas créés dans l’environnement Azure, les entités du Commonwealth doivent utiliser Azure Policy pour contrôler les types de ressources qui peuvent être déployées et la configuration de celles-ci. Les exemples pratiques incluent la restriction des ressources à celles dont l’utilisation est autorisée et approuvée, et nécessitant l’ajout de groupes de sécurité réseau aux sous-réseaux.

|Ressource | Lien|
|---|---|
|*Vue d’ensemble d’Azure Policy* | [https://docs.microsoft.com/azure/governance/policy/overview](https://docs.microsoft.com/azure/governance/policy/overview)|
|*Exemple de stratégie de Types de ressources autorisés* | [https://docs.microsoft.com/azure/governance/policy/samples/allowed-resource-types](https://docs.microsoft.com/azure/governance/policy/samples/allowed-resource-types)|
|*Exemple de stratégie pour forcer l’application d’un groupe de sécurité réseau à un sous-réseau*| [https://docs.microsoft.com/azure/governance/policy/samples/nsg-on-subnet](https://docs.microsoft.com/azure/governance/policy/samples/nsg-on-subnet)|
|

## <a name="paas-egress-capabilities"></a>Fonctionnalités de sortie de PaaS

Les capacités de PaaS offrent des opportunités d’augmentation de la fonctionnalité et de simplification de la gestion, mais introduisent de la complexité pour répondre aux exigences de segmentation et de séparation de réseau. Les capacités de PaaS sont généralement configurées avec des adresses IP publiques et accessibles à partir d’Internet.  Si vous utilisez des capacités de PaaS au sein de vos systèmes et solutions, veillez à identifier les flux de communication entre les composants et à créer des règles de sécurité réseau pour autoriser uniquement cette communication. Dans le cadre d’une approche de défense en profondeur de la sécurité, les capacités de PaaS doivent être configurées avec un chiffrement, une authentification et des contrôles et autorisations d’accès appropriés.  

### <a name="public-ip-for-paas"></a>Adresse IP publique pour PaaS

Les adresses IP publiques pour les capacités de PaaS sont allouées en fonction de la région dans laquelle le service est hébergé ou déployé. Une bonne compréhension de l’allocation d’adresses IP publiques et des régions est requise si vous envisagez de créer des règles de sécurité réseau et une topologie de routage appropriées pour la segmentation et la séparation de réseau couvrant les réseaux virtuels Azure, PaaS et la connectivité ExpressRoute et Internet. Azure alloue les adresses IP à partir d’un pool alloué à chaque région Azure. Microsoft met à disposition les adresses utilisées dans chaque région pour le téléchargement, et le met à jour de manière régulière et contrôlée. Les services disponibles dans chaque région changent également fréquemment à mesure que de nouveaux services sont introduits ou que des services sont déployés plus largement. Les entités du Commonwealth doivent consulter ces documents régulièrement et peuvent utiliser une automatisation pour gérer les systèmes de façon appropriée. Vous pouvez obtenir des adresses IP spécifiques pour certains services hébergés dans chaque région en contactant le support Microsoft.

| Ressource | Lien |
| --- | --- |
| *Plages IP de centres de données Microsoft Azure*                   | [https://www.microsoft.com/download/details.aspx?id=41653](https://www.microsoft.com/download/details.aspx?id=41653) |
| *Services Azure par région*                              | [https://azure.microsoft.com/global-infrastructure/services/?regions=non-regional,australia-central,australia-central-2,australia-east,australia-southeast&products=all](https://azure.microsoft.com/global-infrastructure/services/?regions=non-regional,australia-central,australia-central-2,australia-east,australia-southeast&products=all) |
| *Adresses IP entrantes et sortantes dans Azure App Service* | [https://docs.microsoft.com/azure/app-service/overview-inbound-outbound-ips](https://docs.microsoft.com/azure/app-service/overview-inbound-outbound-ips)
|

## <a name="next-steps"></a>Étapes suivantes

Comparez votre architecture et votre conception globales aux [Blueprints PROTÉGÉS publiés pour les applications web IaaS et PaaS](https://aka.ms/au-protected).
