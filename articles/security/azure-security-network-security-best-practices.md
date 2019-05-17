---
title: Meilleures pratiques de sécurité réseau - Microsoft Azure
description: Cet article détaille les meilleures pratiques en matière de sécurité réseau, sur la base de capacités Azure intégrées.
services: security
author: TerryLanfear
manager: barbkess
editor: TomShinder
ms.assetid: 7f6aa45f-138f-4fde-a611-aaf7e8fe56d1
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/05/2019
ms.author: TomSh
ms.openlocfilehash: f36658d57fb514ff279c39e9cb1644cf56341ec4
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/16/2019
ms.locfileid: "65761832"
---
# <a name="azure-best-practices-for-network-security"></a>Meilleures pratiques Azure pour la sécurité du réseau
Cet article décrit une collection de meilleures pratiques Azure pour améliorer la sécurité de votre réseau. Ces meilleures pratiques sont issues de notre expérience dans le domaine de la mise en réseau Azure, mais également de celle des clients, comme vous.

Cet article détaille les points suivants pour chaque bonne pratique :

* Nature de la bonne pratique
* Raison pour laquelle activer cette bonne pratique
* Conséquence possible en cas de non-utilisation de la meilleure pratique
* Alternatives possibles à la meilleure pratique
* Comment apprendre à utiliser la bonne pratique

Ces meilleures pratiques sont basées sur un avis consensus et les fonctionnalités de la plateforme Azure et les ensembles, telles qu’elles existent au moment de la que rédaction de cet article. Les opinions et avis évoluent au fil du temps ; cet article sera régulièrement mis à jour de manière à tenir compte de ces changements.

## <a name="use-strong-network-controls"></a>Utilisation des contrôles réseau fort
Vous pouvez connecter des [machines virtuelles Azure](https://azure.microsoft.com/services/virtual-machines/) et des appliances à d’autres appareils en réseau, en les plaçant sur des [réseaux virtuels Azure](https://docs.microsoft.com/azure/virtual-network/). Autrement dit, vous pouvez connecter des cartes d’interface réseau virtuel à un réseau virtuel afin de permettre des communications TCP/IP entre les appareils en réseau. Les machines virtuelles connectées à un réseau virtuel Azure peuvent se connecter à des appareils se trouvant sur le même réseau virtuel, sur des réseaux virtuels différents, sur Internet ou sur vos réseaux locaux.

Lorsque vous planifiez votre réseau et la sécurité de votre réseau, nous vous recommandons de centraliser :

- Gestion des fonctions de réseau de base comme ExpressRoute, réseau virtuel et l’approvisionnement de sous-réseau et l’adressage IP.
- Gouvernance des éléments de sécurité réseau, tels que les fonctions d’appliance virtuelle réseau comme ExpressRoute, réseau virtuel et l’approvisionnement de sous-réseau et l’adressage IP.

Si vous utilisez un ensemble commun d’outils de gestion pour surveiller votre réseau et la sécurité de votre réseau, vous obtenez une visibilité claire dans les deux. Une stratégie de sécurité simple et unifiée, réduit les erreurs, car elle augmente la compréhension humaine et la fiabilité de l’automatisation.

## <a name="logically-segment-subnets"></a>Segmentation logique des sous-réseaux
Réseaux virtuels Azure sont similaires aux réseaux locaux sur votre réseau local. L’idée derrière un réseau virtuel Azure est que vous créez un réseau basé sur un seul espace d’adressage IP, sur lequel vous pouvez placer toutes vos machines virtuelles Azure. Les espaces d’adressage IP privés disponibles se trouvent dans les plages des classes A (10.0.0.0/8), B (172.16.0.0/12) et C (192.168.0.0/16).

Meilleures pratiques pour segmenter logiquement les sous-réseaux :

**Bonne pratique** : N’attribuez pas de règles avec larges plages d’autorisation (par exemple, autoriser 0.0.0.0 et 255.255.255.255).  
**Détail** : Vérifiez les procédures de dépannage décourager ou interdire la configuration de ces types de règles. Ces autoriser prospect de règles à un faux sentiment de sécurité et sont fréquemment trouvées et exploitées par des équipes rouges.

**Bonne pratique** : Segmentez l’espace d’adressage plus volumineux en sous-réseaux.   
**Détail** : Pour créer vos sous-réseaux, utilisez les principes de création de sous-réseau reposant sur [CIDR](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing).

**Bonne pratique** : Créez des contrôles d’accès réseau entre les sous-réseaux. Le routage entre les sous-réseaux se produit automatiquement. Il est donc inutile de configurer manuellement des tables de routage. Par défaut, il n’existe aucun contrôle d’accès réseau entre les sous-réseaux que vous créez sur un réseau virtuel Azure.   
**Détail** : Utilisez un [groupe de sécurité réseau](../virtual-network/virtual-networks-nsg.md) pour vous protéger contre le trafic non sollicité en sous-réseaux Azure. Groupes de sécurité réseau sont des appareils d’inspection des paquets avec état simple qui utilisent l’approche de 5 tuples (adresse IP source, port source, adresse IP de destination, port de destination et protocole de couche 4) pour créer des règles autoriser ou refuser le trafic réseau. Vous pouvez autoriser ou refuser le trafic vers et depuis une ou plusieurs adresses IP, ou entre des sous-réseaux entiers, dans les deux directions.

Lorsque vous utilisez des groupes de sécurité réseau pour le contrôle d’accès réseau entre les sous-réseaux, vous pouvez placer les ressources qui appartiennent à la même zone de sécurité ou d’un rôle dans leurs propres sous-réseaux.

**Bonne pratique** : Évitez de petits réseaux et sous-réseaux virtuels pour garantir la simplicité et flexibilité.   
**Détail** : La plupart des organisations ajouter davantage de ressources qu’initialement prévue et ré-allocation d’adresses est laborieux. À l’aide de sous-réseaux de petite taille enrichit une sécurité limitée, et mappage d’un groupe de sécurité réseau à chaque sous-réseau ajoute une surcharge. Définir des sous-réseaux largement pour vous assurer que vous disposez d’une flexibilité pour la croissance.

**Bonne pratique** : Simplifier la gestion de règle de groupe de sécurité réseau en définissant [groupes de sécurité d’Application](https://docs.microsoft.com/rest/api/virtualnetwork/applicationsecuritygroups).  
**Détail** : Définir un groupe de sécurité d’Application pour les listes d’adresses IP que vous pensez que peut être modifiée dans le futur ou être utilisées sur plusieurs groupes de sécurité réseau. N’oubliez pas de nom de la sécurité des applications groupes clairement par conséquent, d’autres peuvent comprendre leur contenu et l’objectif.

## <a name="adopt-a-zero-trust-approach"></a>Adopter une approche de confiance de zéro
Les réseaux basés sur le périmètre fonctionnent sur l’hypothèse que tous les systèmes au sein d’un réseau peuvent être approuvés. Mais les employés d’aujourd'hui accéder aux ressources de leur organisation à partir de n’importe où sur divers appareils et applications, ce qui rend périmètre des contrôles de sécurité non pertinentes. Accès aux stratégies de contrôle qui vous concentrer uniquement sur qui peuvent accéder à une ressource ne sont pas suffisants. Pour maîtriser l’équilibre entre sécurité et productivité, les administrateurs de sécurité doivent aussi prendre *comment* une ressource est accédée.

Réseaux doivent évoluer de défenses traditionnels, car les réseaux peuvent être vulnérables à des failles : une personne malveillante peut compromettre un point de terminaison unique au sein de la limite de confiance, puis rapidement une brèche dans tout le réseau. [Zéro approbation](https://www.microsoft.com/security/blog/2018/06/14/building-zero-trust-networks-with-microsoft-365/) réseaux éliminent le concept d’approbation selon l’emplacement réseau au sein d’un périmètre. Au lieu de cela, les architectures de confiance de zéro utilisent appareil et utilisateur des revendications de confiance pour réguler l’accès aux données de l’organisation et aux ressources. De nouvelles initiatives, adopter les approches d’approbation de zéro qui valident l’approbation au moment de l’accès.

Meilleures pratiques sont :

**Bonne pratique** : Donner l’accès conditionnel aux ressources en fonction de l’appareil, identité, de garantie, emplacement réseau et bien plus encore.  
**Détail** : [Accès conditionnel Azure AD](../active-directory/conditional-access/overview.md) permet de vous appliquez les contrôles d’accès appropriés en implémentant des décisions de contrôle d’accès automatisée basées sur les conditions requises. Pour plus d’informations, consultez [gérer l’accès à la gestion Azure avec accès conditionnel](../role-based-access-control/conditional-access-azure-management.md).

**Bonne pratique** : Activer l’accès de port uniquement après l’approbation de flux de travail.  
**Détail** : Vous pouvez utiliser [machine virtuelle l’accès juste-à-temps dans Azure Security Center](../security-center/security-center-just-in-time.md) pour verrouiller le trafic entrant vers vos machines virtuelles Azure, ce qui réduit l’exposition aux attaques tout en fournissant un accès facile pour vous connecter à des machines virtuelles si nécessaire.

**Bonne pratique** : Accorder des autorisations temporaires pour effectuer des tâches privilégiées, ce qui empêche les utilisateurs malveillants ou non autorisés d’accéder une fois que les autorisations ont expiré. L’accès est accordé uniquement au moment où les utilisateurs en ont besoin.  
**Détail** : Utilisez l’accès juste-à-temps dans Azure AD Privileged Identity Management ou dans une solution tierce pour accorder des autorisations pour effectuer des tâches privilégiées.

Aucune approbation est la prochaine évolution de sécurité réseau. L’état des cyberattaques lecteurs aux organisations de prendre la mentalité « faille supposée », mais cette approche ne doit pas être limitée. Zéro réseaux de confiance protéger les ressources et les données d’entreprise tout en garantissant que les organisations peuvent créer un espace de travail moderne à l’aide de technologies qui permettent aux employés d’être productifs à tout moment et en tout lieu, en aucune façon.

## <a name="control-routing-behavior"></a>Contrôle du comportement de routage
Lorsque vous placez une machine virtuelle sur un réseau virtuel Azure, celle-ci peut se connecter à n’importe quelle autre machine virtuelle du même réseau virtuel, même si les autres machines virtuelles se trouvent sur des sous-réseaux différents. Ce type de communication est rendu possible par une collection d’itinéraires système qui sont activés par défaut. Grâce à ces itinéraires par défaut, les machines virtuelles placées sur le même réseau virtuel peuvent initier des connexions les unes avec les autres, ainsi qu’avec Internet (pour les communications sortantes vers Internet uniquement).

Même si les itinéraires système par défaut sont utiles dans de nombreux scénarios de déploiement, il se peut que vous souhaitiez personnaliser la configuration du routage pour vos déploiements. Vous pouvez configurer l’adresse du tronçon suivant afin d’atteindre des destinations spécifiques.

Nous vous recommandons de configurer des [itinéraires définis par l’utilisateur](../virtual-network/virtual-networks-udr-overview.md) quand vous déployez une appliance de sécurité pour un réseau virtuel. Ce point est présenté dans une section ultérieure intitulée [Sécurisation de vos ressources critiques du service Azure pour vos réseaux virtuels uniquement](azure-security-network-security-best-practices.md#secure-your-critical-azure-service-resources-to-only-your-virtual-networks).

> [!NOTE]
> Les itinéraires définis par l’utilisateur ne sont pas obligatoires ; les itinéraires système par défaut fonctionnent habituellement.
>
>

## <a name="use-virtual-network-appliances"></a>Utilisation d’appliances de réseau virtuel
Groupes de sécurité réseau et routage défini par l’utilisateur peuvent fournir une certaine mesure de sécurité réseau au niveau des couches réseau et transport de la [modèle OSI](https://en.wikipedia.org/wiki/OSI_model). mais dans certaines situations, vous souhaiterez ou devrez activer la sécurité aux niveaux élevés de la pile. Le cas échéant, nous vous recommandons de déployer les appliances de sécurité de réseau virtuel fournies par les partenaires d’Azure.

Les appliances de sécurité réseau Azure peuvent offrir des niveaux de sécurité supérieurs à ceux des contrôles appliqués au niveau du réseau. Fonctionnalités de sécurité réseau des appliances de sécurité de réseau virtuel :


* Installation de pare-feu
* Détection et prévention des intrusions
* Gestion des vulnérabilités
* Contrôle des applications
* Détection des anomalies basée sur le réseau
* Filtrage web
* Antivirus
* Protection contre les botnets

Pour trouver les appliances de sécurité de réseau virtuel Azure disponibles, accédez à la [Place de marché Microsoft Azure](https://azure.microsoft.com/marketplace/) et recherchez les termes « sécurité » et « sécurité réseau ».

## <a name="deploy-perimeter-networks-for-security-zones"></a>Déploiement des réseaux de périmètre pour les zones de sécurité
Un [réseau de périmètre](https://docs.microsoft.com/azure/best-practices-network-security) (également appelé zone DMZ) est un segment de réseau logique ou physique qui fournit une couche de sécurité supplémentaire entre vos ressources et Internet. Les périphériques de contrôle d’accès réseau spécialisés situés à la périphérie d’un réseau de périmètre autorisent uniquement le trafic souhaité entrant dans votre réseau virtuel.

Les réseaux de périmètre sont utiles, car vous pouvez concentrer la gestion des contrôles d’accès réseau, la surveillance, la journalisation et la création de rapports sur les appareils situés à la périphérie de votre réseau virtuel Azure. Un réseau de périmètre est où vous activez généralement distribuées par déni de service (distribué DDoS) prévention, intrusion détection/prévention des intrusions (IDS/IPS), règles de pare-feu et stratégies, filtrage de contenu web, anti-programme malveillant du réseau et bien plus encore. Les appareils dédiés à la sécurité réseau se trouvent entre Internet et le réseau virtuel Azure, et disposent d’une interface sur les deux réseaux.

Il s’agit de la conception de base d’un réseau de périmètre, mais il existe de nombreuses conceptions différentes, telles que DOS à DOS, triple hébergement et multi-hébergés.

Selon le concept d’approbation zéro mentionné précédemment, nous vous recommandons d’envisager à l’aide d’un réseau de périmètre pour tous les déploiements de haute sécurité pour améliorer le niveau de contrôle d’accès et de sécurité réseau pour vos ressources Azure. Vous pouvez utiliser Azure ou une solution tierce pour fournir une couche supplémentaire de sécurité entre vos ressources et internet :

- Contrôles natifs Azure. [Pare-feu Azure](../firewall/overview.md) et [pare-feu d’applications web dans Application Gateway](../application-gateway/overview.md#web-application-firewall) offrent une sécurité de base avec un pare-feu entièrement dynamique en tant que service, la haute disponibilité intégrée, l’extensibilité du cloud sans restriction, filtrage du nom de domaine complet , prise en charge des ensembles de règles OWASP core et le programme d’installation simple et la configuration.
- Offres de tiers. Recherche le [place de marché Azure](https://azuremarketplace.microsoft.com/) pour le pare-feu de nouvelle génération (NGFW) et d’autres offres de tiers qui fournissent des outils de sécurité courantes et considérablement améliorer le niveau de sécurité réseau. Configuration peut être plus complexe, mais une offre de tiers peut vous permettre d’utiliser les compétences et capacités existantes.

## <a name="avoid-exposure-to-the-internet-with-dedicated-wan-links"></a>Éviter l’exposition à internet avec des liaisons WAN dédiées
De nombreuses organisations ont opté pour l’informatique hybride. Avec l’environnement informatique hybride, certaines des informations de la société sont dans Azure et d’autres restent en local. Dans de nombreux cas, certains composants d’un service sont exécutés dans Azure, tandis que d’autres le sont localement.

Dans un scénario informatique hybride, il existe généralement un certain type de connectivité entre différents locaux. La connectivité entre locaux permet à l’entreprise de relier ses réseaux locaux aux réseaux virtuels Azure. Deux solutions de connectivité entre locaux sont disponibles :

* [VPN de site à site](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md). Il s’agit d’une technologie établie, fiable et approuvée, mais la connexion s’effectue via Internet. Bande passante est limitée à un maximum d’environ 1,25 Gbits/s. VPN de site à site est une option souhaitable dans certains scénarios.
* **Azure ExpressRoute**. Nous vous recommandons d’utiliser [ExpressRoute](../expressroute/expressroute-introduction.md) pour la connectivité entre locaux. ExpressRoute vous permet d’étendre vos réseaux locaux au cloud de Microsoft via une connexion privée assurée par un fournisseur de connectivité. Grâce à ExpressRoute, vous pouvez établir des connexions aux services de cloud Microsoft comme Azure, Office 365 et Dynamics 365. ExpressRoute est un liaison réseau étendu dédiée lien entre votre emplacement local ou un fournisseur d’hébergement Microsoft Exchange. Comme il s’agit d’une connexion de télécommunications, vos données ne transitent pas par internet, donc il n’est pas exposé aux risques potentiels de communications internet.

L’emplacement de votre connexion ExpressRoute peut affecter la capacité de pare-feu, d’évolutivité, fiabilité et une visibilité de trafic réseau. Vous devez identifier l’emplacement où mettre fin à ExpressRoute dans les réseaux existants (local). Vous pouvez :

- Mettre fin à l’extérieur du pare-feu (le paradigme de réseau de périmètre) si vous avez besoin d’une visibilité sur le trafic, si vous avez besoin continuer une pratique existante de l’isolation des centres de données, ou si vous placez uniquement les ressources extranets sur Azure.
- Mettre fin à l’intérieur du pare-feu (le paradigme d’extension de réseau). Il s’agit de la recommandation par défaut. Dans tous les autres cas, nous vous recommandons de traitement Azure comme un centre de données nième.

## <a name="optimize-uptime-and-performance"></a>Optimisation de la durée active et des performances
Si un service est défaillant, les informations sont inaccessibles. Si les performances sont tellement médiocres que les données en sont inutilisables, vous pouvez considérer que ces dernières sont inaccessibles. Du point de vue de la sécurité, vous devez faire tout ce qui est en votre pouvoir pour garantir des performances et une durée de fonctionnement optimales pour vos services.

Pour optimiser la disponibilité et les performances, une méthode bien connue pour son efficacité est l’équilibrage de charge. L’équilibrage de charge est une méthode de répartition du trafic réseau entre les serveurs qui font partie d’un service. Ainsi, si votre service inclut plusieurs serveurs web frontaux, vous pouvez utiliser l’équilibrage de charge pour répartir le trafic entre ces derniers.

La répartition du trafic permet d’augmenter la disponibilité. En effet, si l’un des serveurs web est indisponible, l’équilibreur de charge arrête d’envoyer des données à ce serveur et redirige le trafic vers les serveurs actifs. L’équilibrage de charge améliore également les performances, car la surcharge du processeur, du réseau et de la mémoire associée au traitement des requêtes est répartie sur les différents serveurs avec équilibrage de charge.

Nous vous recommandons de tirer parti aussi souvent que possible de l’équilibrage de charge, selon les besoins de vos services. Voici des scénarios au niveau du réseau virtuel Azure et au niveau global, ainsi que des options d’équilibrage de charge pour chacun.

**Scénario** : Vous disposez d’une application qui :

- Requiert des requêtes provenant d’une même session utilisateur/client pour atteindre la même machine virtuelle principale. Exemples : applications de panier d’achat et serveurs de courrier.
- Accepte uniquement une connexion sécurisée. La communication non chiffrée vers le serveur n’est donc pas une option acceptable.
- Exige le routage ou l’équilibrage de charge sur différents serveurs principaux des multiples requêtes HTTP sur une même connexion TCP de longue durée.

**Option d’équilibrage de charge** : Utilisez [Azure Application Gateway](../application-gateway/application-gateway-introduction.md), un équilibreur de charge de trafic web HTTP. Application Gateway prend en charge le chiffrement SSL de bout en bout et la [terminaison SSL](../application-gateway/application-gateway-introduction.md) au niveau de la passerelle. Les serveurs web peuvent ensuite être libérés du traitement du chiffrement et du déchiffrement, et du trafic du contenu non chiffré vers les serveurs principaux.

**Scénario** : Vous devez équilibrer la charge des connexions entrantes en provenance d’Internet entre vos serveurs situés au sein d’un réseau virtuel Azure. Ce sont les scénarios que vous rencontrez lorsque vous :

- Disposez d’applications sans état qui acceptent les demandes entrantes provenant d’Internet.
- N’exigez pas de sessions permanentes ni de déchargement SSL. Ces sessions correspondent à une méthode utilisée avec l’équilibrage de charge des applications pour obtenir l’affinité de serveur.

**Option d’équilibrage de charge** : Utilisez le portail Azure pour [créer un équilibreur de charge externe](../load-balancer/quickstart-create-basic-load-balancer-portal.md) qui répartit les demandes entrantes sur plusieurs machines virtuelles afin de fournir un niveau de disponibilité plus élevé.

**Scénario** : Vous devez équilibrer la charge des connexions des machines virtuelles qui ne sont pas sur Internet. Dans la plupart des cas, les connexions qui sont acceptées pour l’équilibrage de charge sont initiées par les appareils figurant sur un réseau virtuel Azure, par exemple des instances SQL Server ou des serveurs web internes.   
**Option d’équilibrage de charge** : Utilisez le portail Azure pour [créer un équilibreur de charge interne](../load-balancer/quickstart-create-basic-load-balancer-powershell.md) qui répartit les demandes entrantes sur plusieurs machines virtuelles afin de fournir un niveau de disponibilité plus élevé.

**Scénario** : Vous recherchez un équilibrage de charge global, car vous :

- Disposez d’une solution cloud qui est largement distribuée dans plusieurs régions et qui nécessite le plus haut niveau de durée de fonctionnement (disponibilité) possible.
- Avez besoin du niveau de disponibilité le plus élevé possible pour vous assurer que votre service est disponible même si tout un centre de données ne l’est pas.

**Option d’équilibrage de charge** : Utilisez Azure Traffic Manager. Grâce à ce service, vous pouvez équilibrer la charge des connexions vers vos services en fonction de l’emplacement de l’utilisateur.

Par exemple, si l’utilisateur qui envoie une requête à votre service se trouve dans un pays de l’Union européenne, la connexion est dirigée vers vos services qui se trouvent au sein d’un centre de données de cette zone. Cette étape de l’équilibrage de charge global assuré par Traffic Manager permet d’optimiser les performances, car la connexion au centre de données le plus proche est plus rapide que dans le cas de centres de données éloignés.

## <a name="disable-rdpssh-access-to-virtual-machines"></a>Désactivation de l’accès RDP/SSH aux machines virtuelles Azure
Il est possible d’atteindre les machines virtuelles Azure à l’aide des protocoles [RDP (Remote Desktop Protocol)](https://en.wikipedia.org/wiki/Remote_Desktop_Protocol) et [SSH (Secure Shell)](https://en.wikipedia.org/wiki/Secure_Shell). Ces protocoles permettent de gérer des machines virtuelles à partir d’emplacements distants. Ils sont souvent utilisés par les centres de données informatiques.

Cependant, ils peuvent être sources de problèmes de sécurité quand ils sont utilisés sur Internet. En effet, les attaquants peuvent recourir à des techniques de [force brute](https://en.wikipedia.org/wiki/Brute-force_attack) pour accéder aux machines virtuelles Azure. Lorsqu’ils y parviennent, ils peuvent utiliser votre machine virtuelle comme point de départ pour le piratage d’autres machines sur votre réseau virtuel, voire attaquer des appareils en réseau en dehors d’Azure.

Nous vous recommandons de désactiver l’accès direct des protocoles RDP et SSH à vos machines virtuelles Azure depuis Internet. Cela fait, vous disposez d’autres options vous permettant d’accéder à ces machines virtuelles à des fins de gestion à distance.

**Scénario** : Autorisez un utilisateur unique à se connecter à un réseau virtuel Azure via Internet.   
**Option** : L’expression [VPN de point à site](../vpn-gateway/vpn-gateway-point-to-site-create.md) est synonyme de connexion du client/serveur VPN pour un accès à distance. Une fois la connexion point à site établie, l’utilisateur peut avoir recours au protocole RDP ou SSH pour se connecter aux machines virtuelles situées sur le réseau virtuel Azure auquel cet utilisateur est connecté via le VPN point à site. Cela suppose que l’utilisateur dispose des autorisations requises pour atteindre ces machines virtuelles.

Le VPN point à site est plus sécurisé qu’une connexion RDP ou SSH directe, car l’utilisateur doit s’authentifier deux fois pour pouvoir se connecter à une machine virtuelle. L’utilisateur doit d’abord s’authentifier (et être autorisé) pour établir la connexion VPN point à site. Il doit ensuite s’authentifier (et être autorisé) pour établir la session RDP ou SSH.

**Scénario** : Permettez aux utilisateurs de votre réseau local de se connecter aux machines virtuelles de votre réseau virtuel Azure.   
**Option** : Un [VPN de site à site](../vpn-gateway/vpn-gateway-site-to-site-create.md) connecte un réseau dans son ensemble à un autre réseau, par le biais d’Internet. Vous pouvez utiliser un VPN de site à site pour connecter votre réseau local à un réseau virtuel Azure. Les utilisateurs de votre réseau local se connectent à l’aide du protocole RDP ou SSH via la connexion VPN de site à site. Vous n’avez pas à autoriser un accès RDP ou SSH direct via Internet.

**Scénario** : Pour proposer une fonctionnalité similaire à la connexion VPN de site à site, utilisez une liaison réseau étendu dédiée.   
**Option** : Utilisez [ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/). Ce service fournit des fonctionnalités similaires au VPN de site à site. Les principales différences entre ces deux architectures sont les suivantes :

- La liaison WAN dédiée ne traverse pas Internet.
- Les liaisons WAN dédiées sont généralement plus stables et plus performantes.

## <a name="secure-your-critical-azure-service-resources-to-only-your-virtual-networks"></a>Sécurisation de vos ressources critiques du service Azure pour vos réseaux virtuels uniquement
Utilisez des points de terminaison de service de réseau virtuel pour étendre votre espace d’adressage privé de réseau virtuel et l’identité de votre réseau virtuel aux services Azure, via une connexion directe. Les points de terminaison permettent de sécuriser vos ressources critiques du service Azure pour vos réseaux virtuels uniquement. Le trafic à partir de votre réseau virtuel vers le service Azure reste toujours sur le réseau principal Microsoft Azure.

Les points de terminaison de service fournissent les avantages suivants :

- **Sécurité améliorée de vos ressources de service Azure** : Avec les points de terminaison de service, les ressources de service Azure peuvent être sécurisées pour votre réseau virtuel. La sécurisation des ressources du service pour un réseau virtuel renforce la sécurité grâce à la suppression complète de l’accès Internet public aux ressources et à l’autorisation du trafic seul à partir de votre réseau virtuel.
- **Routage optimal pour le trafic de service Azure à partir de votre réseau virtuel** : Tous les itinéraires dans votre réseau virtuel qui forcent le trafic Internet vers vos appliances locales et/ou virtuelles, aussi appelé tunneling forcé, forcent également le trafic de service Azure à prendre le même itinéraire que le trafic Internet. Les points de terminaison de service fournissent un routage optimal pour le trafic Azure.

  Les points de terminaison acheminent toujours le trafic de service directement à partir de votre réseau virtuel vers le service sur le réseau principal d’Azure. La conservation du trafic sur le réseau principal d’Azure vous permet de continuer l’audit et la surveillance du trafic Internet sortant à partir de vos réseaux virtuels, via le tunneling forcé, sans affecter le trafic de service. Découvrez d’autres informations sur les [itinéraires définis par l’utilisateur et le tunneling forcé](../virtual-network/virtual-networks-udr-overview.md).

- **Une configuration simple et un temps de gestion réduit** : Les adresses IP publiques réservées dans vos réseaux virtuels ne sont désormais plus nécessaires pour sécuriser les ressources Azure via le pare-feu IP. Aucun NAT ou appareil de passerelle n’est requis pour configurer les points de terminaison de service. Les points de terminaison de service peuvent être configurés par un simple clic sur un sous-réseau. La conservation des points de terminaison ne requiert aucun traitement supplémentaire.

Pour en savoir plus sur les points de terminaison de service et sur les services et régions Azure pour lesquels ces points de terminaison sont disponibles, consultez [Points de terminaison de service de réseau virtuel](../virtual-network/virtual-network-service-endpoints-overview.md).

## <a name="next-steps"></a>Étapes suivantes
Consultez l’article [Bonnes pratiques et tendances Azure relatives à la sécurité](security-best-practices-and-patterns.md) pour découvrir d’autres bonnes pratiques en matière de sécurité à appliquer dans le cadre de la conception, du déploiement et de la gestion de vos solutions cloud avec Azure.
