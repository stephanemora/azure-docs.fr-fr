---
title: Concepts et exigences en matière de sécurité de réseau dans Azure | Microsoft Docs
description: Cet article décrit les notions de base sur les exigences et les concepts de la sécurité réseau et vous explique ce que propose Azure dans chacun de ces domaines.
services: security
documentationcenter: na
author: TomShinder
manager: MBaldwin
editor: TomSh
ms.assetid: bedf411a-0781-47b9-9742-d524cf3dbfc1
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: terrylan
ms.openlocfilehash: fbd589aedb955ee4bd61dc0ec754d8713a98179a
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/20/2018
ms.locfileid: "34365625"
---
# <a name="azure-network-security-overview"></a>Vue d’ensemble de la sécurité du réseau Azure
Azure inclut une infrastructure réseau solide pour prendre en charge les exigences de connectivité de vos applications et services. La connectivité réseau est possible entre les ressources hébergées dans Azure, entre les ressources hébergées sur site et dans Azure, mais aussi vers et à partir d’Internet et d’Azure.

L’objectif de cet article est d’expliquer ce que propose Azure dans le domaine de la sécurité du réseau. Outre les explications de base sur les concepts et les exigences de la sécurité du réseau, vous en saurez plus sur :

* Mise en réseau Azure
* Contrôle d’accès réseau
* Accès à distance sécurisé et connectivité intersite
* Disponibilité
* Résolution de noms
* Architecture (DMZ) du réseau de périmètre
* Surveillance et détection des menaces

## <a name="azure-networking"></a>Mise en réseau Azure
Les machines virtuelles nécessitent une connectivité réseau. Pour cela, les machines virtuelles doivent être connectées à un réseau virtuel Azure. Un réseau virtuel est une construction logique basée sur le réseau physique Azure. Chaque réseau virtuel logique est isolé des autres réseaux virtuels. Cela permet de s’assurer que le trafic réseau dans vos déploiements n’est pas accessible aux autres clients Azure.

En savoir plus :

* [Présentation du réseau virtuel](../virtual-network/virtual-networks-overview.md)


## <a name="network-access-control"></a>Contrôle d’accès réseau
Le contrôle d’accès réseau consiste à limiter la connectivité vers et depuis certains appareils ou sous-réseaux au sein d’un réseau virtuel. L’objectif du contrôle d’accès réseau est de limiter l’accès à vos machines virtuelles et services aux seuls utilisateurs et appareils approuvés. Les contrôles d’accès sont basés sur la décision d’autoriser ou de refuser la connexion vers et depuis votre machine virtuelle ou votre service.

Azure prend en charge plusieurs types de contrôle d’accès réseau, tels que :

* Contrôle de la couche réseau
* Contrôle du routage et tunneling forcé
* Appliances de sécurité de réseau virtuel

### <a name="network-layer-control"></a>Contrôle de la couche réseau
Tout déploiement sécurisé requiert certaines mesures de contrôle d’accès réseau. L’objectif du contrôle d’accès réseau est de restreindre les communications des machines virtuelles aux systèmes nécessaires. Les autres tentatives de communication sont bloquées.

Si vous avez besoin d’un contrôle d’accès au niveau du réseau de base (reposant sur l’adresse IP et les protocoles TCP ou UDP), vous pouvez utiliser des groupes de sécurité réseau (NSG). Un NSG est un pare-feu de filtrage des paquets avec état qui vous permet de contrôler l’accès sur la base d’un algorithme à [5 tuples](https://www.techopedia.com/definition/28190/5-tuple). Les groupes de sécurité réseau n’effectuent pas d’inspection de la couche d’application ni de contrôles d’accès authentifiés.

En savoir plus :

* [Groupes de sécurité réseau](../virtual-network/security-overview.md)

### <a name="route-control-and-forced-tunneling"></a>Contrôle du routage et tunneling forcé
Il est essentiel de pouvoir contrôler le comportement du routage sur vos réseaux virtuels. Si le routage n’est pas configuré correctement, les applications et les services hébergés sur votre machine virtuelle risquent de se connecter à des appareils non autorisés, y compris les systèmes détenus et utilisés par des personnes malveillantes potentielles.

La mise en réseau Azure permet de personnaliser le comportement de routage du trafic réseau sur vos réseaux virtuels. Vous pouvez ainsi modifier les entrées de la table de routage par défaut dans votre réseau virtuel. Le contrôle du comportement de routage vous permet de vous assurer que tout le trafic en provenance d’un appareil ou d’un groupe d’appareils donné entre ou quitte votre réseau virtuel par un point spécifique.

Par exemple, vous pouvez disposer d’une appliance de sécurité de réseau virtuel sur votre réseau virtuel. Vous voulez vous assurer que tout le trafic vers et depuis votre réseau virtuel passe par cette appliance de sécurité virtuelle. Pour ce faire, vous pouvez configurer des [itinéraires définis par l’utilisateur](../virtual-network/virtual-networks-udr-overview.md) (UDR) dans Azure.

Le [tunneling forcé](https://www.petri.com/azure-forced-tunneling) est un mécanisme que vous pouvez utiliser pour empêcher vos services de se connecter aux appareils sur Internet. Par contre, cela n’empêche pas les services d’accepter des connexions entrantes ni d’y répondre. Les serveurs web frontaux doivent pouvoir répondre aux demandes provenant d’hôtes Interne, ce qui explique pourquoi le trafic Internet est autorisé à entrer sur ces serveurs web et pourquoi les serveurs web sont autorisés à y répondre.

Un serveur web frontal ne devrait par contre pas pouvoir initier une requête sortante. Une telle requête pourrait représenter un risque de sécurité car ces connexions peuvent être utilisées pour télécharger des programmes malveillants. Même si vous voulez autoriser ces serveurs frontaux à initier des requêtes sortantes vers Internet, vous pourriez les obliger à passer par les serveurs proxy web locaux. Ainsi, vous pouvez utiliser les fonctionnalités de journalisation et de filtrage des URL.

Pour éviter ce problème, vous pouvez donc utiliser le tunneling forcé. Lorsque vous activez le tunneling forcé, toutes les connexions à Internet passent obligatoirement par votre passerelle locale. Vous pouvez configurer le tunneling forcé en utilisant les UDR.

En savoir plus :

* [Présentation des itinéraires définis par l’utilisateur et du transfert IP](../virtual-network/virtual-networks-udr-overview.md)

### <a name="virtual-network-security-appliances"></a>Appliances de sécurité de réseau virtuel
Les NSG, les UDR et le tunneling forcé vous offrent une sécurité au niveau des couches réseau et transport du [modèle OSI](https://en.wikipedia.org/wiki/OSI_model), mais vous pouvez aussi étendre la sécurité au-delà de la couche réseau.

Vos besoins en matière de sécurité peuvent inclure :

* Des fonctionnalités d’authentification et d’autorisation régissant l’accès à votre application
* La détection et la gestion des intrusions
* Une inspection de la couche d’application pour les protocoles de niveau supérieur
* Un filtrage des URL
* Un logiciel anti-programme malveillant et antivirus au niveau du réseau
* Une protection anti-robot
* Un contrôle d’accès aux applications
* Une protection DDoS supplémentaire (en supplément de la protection DDoS assurée par la structure Azure)

Ces fonctionnalités avancées de sécurité réseau peuvent être mises en œuvre via une solution de partenaire Azure. Pour connaître les dernières solutions de sécurité réseau des partenaires Azure, rendez-vous sur la [Place de marché Azure](https://azure.microsoft.com/marketplace/) et recherchez les mots clés « sécurité » et « sécurité réseau ».

## <a name="secure-remote-access-and-cross-premises-connectivity"></a>Accès à distance sécurisé et connectivité intersite
Imaginons que vous deviez installer, configurer et gérer vos ressources Azure à distance. Vous pouvez également déployer des solutions [informatiques hybrides](http://social.technet.microsoft.com/wiki/contents/articles/18120.hybrid-cloud-infrastructure-design-considerations.aspx) avec des composants hébergés localement et dans le cloud public Azure. Ces scénarios nécessitent un accès à distance sécurisé.

La mise en réseau Azure prend en charge les scénarios d’accès à distance sécurisé suivants :

* Connecter des stations de travail à un réseau virtuel
* Connecter votre réseau local à un réseau virtuel à l’aide d’un VPN
* Connecter votre réseau local à un réseau virtuel à l’aide d’une liaison réseau étendu dédiée
* Connecter des réseaux virtuels entre eux

### <a name="connect-individual-workstations-to-a-virtual-network"></a>Connecter des stations de travail à un réseau virtuel
Vous pouvez permettre à des développeurs ou à des membres du personnel d’exploitation de gérer les machines virtuelles et les services dans Azure. Par exemple, supposons que vous devez accéder à une machine virtuelle sur un réseau virtuel. Mais votre stratégie de sécurité n’autorise pas l’accès RDP ou SSH à distance pour les machines virtuelles individuelles. Dans ce cas, vous pouvez utiliser une connexion VPN de point à site.

La connexion VPN de point à site utilise le protocole [VPN SSTP](https://technet.microsoft.com/library/cc731352.aspx) pour vous permettre de configurer une connexion privée et sécurisée entre l’utilisateur et le réseau virtuel. Une fois la connexion VPN établie, l’utilisateur peut utiliser le protocole RDP ou SSH sur la liaison VPN pour se connecter à une machine virtuelle sur le réseau virtuel. (en supposant que l’utilisateur peut s’authentifier et qu’il est autorisé à se connecter).

En savoir plus :

* [Configurer une connexion point à site à un réseau virtuel à l’aide de PowerShell](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

### <a name="connect-your-on-premises-network-to-a-virtual-network-with-a-vpn"></a>Connecter votre réseau local à un réseau virtuel à l’aide d’un VPN
Imaginons que vous souhaitiez connecter l’ensemble ou une partie de votre réseau d’entreprise à un réseau virtuel. Il s’agit d’un scénario d’informatique hybride courant dans lequel les organisations [étendent leur centre de données local dans Azure](https://gallery.technet.microsoft.com/Datacenter-extension-687b1d84). Dans de nombreux cas, les organisations hébergent une partie du service dans Azure et une partie en local, par exemple lorsque la solution comprend des serveurs web frontaux dans Azure et des bases de données principales locales. Ces types de connexions « intersite » offrent une gestion plus sécurisée des ressources hébergées dans Azure et prend en charge des scénarios tels que l’extension des contrôleurs de domaine Active Directory dans Azure.

Pour ce faire, vous pouvez utiliser un [VPN de site à site](https://www.techopedia.com/definition/30747/site-to-site-vpn). La différence entre un VPN de site à site et un VPN de point à site est que ce dernier connecte un seul appareil à un réseau virtuel. Un VPN de site à site connecte un réseau entier (par exemple, votre réseau local) à un réseau virtuel. Une connexion VPN de site à site vers un réseau virtuel utilise le protocole VPN hautement sécurisé en mode de tunneling IPsec.

En savoir plus :

* [Créer un réseau virtuel de gestionnaire de ressources avec une connexion VPN de site à site à l’aide du portail Azure](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)
* [Planification et conception de la passerelle VPN](../vpn-gateway/vpn-gateway-plan-design.md)

### <a name="connect-your-on-premises-network-to-a-virtual-network-with-a-dedicated-wan-link"></a>Connecter votre réseau local à un réseau virtuel à l’aide d’une liaison réseau étendu dédiée
Les connexions VPN de point à site et de site à site offrent une véritable connectivité intersite. Toutefois, certaines organisations leur reconnaissent les inconvénients suivants :

* Les connexions VPN déplacent les données sur Internet. Les connexions s’exposent ainsi à des risques de sécurité liés au déplacement de données sur un réseau public. En outre, il est impossible de garantir la fiabilité et la disponibilité des connexions Internet.
* La bande passante des connexions VPN aux réseaux virtuels, limitée à environ 200 Mbits/s, est parfois insuffisante pour certaines applications et utilisations.

Les organisations qui ont besoin du plus haut niveau de disponibilité et de sécurité pour leurs connexions intersites utilisent généralement des liaisons réseau étendu dédiées pour se connecter à des sites distants. Azure vous permet d’utiliser une liaison réseau étendu dédiée pour connecter votre réseau local à un réseau virtuel. Azure ExpressRoute rend cela possible.

En savoir plus :

* [Présentation technique d’ExpressRoute](../expressroute/expressroute-introduction.md)

### <a name="connect-virtual-networks-to-each-other"></a>Connecter des réseaux virtuels entre eux
Il est possible d’utiliser plusieurs réseaux virtuels dans vos déploiements. Plusieurs raisons peuvent vous pousser à le faire, comme la volonté de simplifier la gestion ou de renforcer la sécurité. Quelle que soit la raison pour laquelle vous placez des ressources sur différents réseaux virtuels, vous voudrez peut-être, à un moment donné, connecter entre elles les ressources hébergées sur les différents réseaux.

Une option est de connecter les services d’un réseau virtuel aux services d’un autre réseau virtuel en effectuant un « retour de boucle » via Internet. La connexion part d’un réseau virtuel, passe par Internet, puis revient au réseau virtuel de destination. Cette option expose la connexion aux risques de sécurité inhérents à toute communication Internet.

Une meilleure option consiste à créer un VPN de site à site qui se connecte entre deux réseaux virtuels. Cette méthode utilise le même protocole de [mode de tunneling IPsec](https://technet.microsoft.com/library/cc786385.aspx) que la connexion VPN de site à site mentionnée ci-dessus.

L’avantage d’utiliser cette approche est que la connexion VPN est établie via l’infrastructure réseau Azure et non via Internet. Vous bénéficiez ainsi d’un meilleur niveau de sécurité par rapport à une connexion VPN de site à site basée sur Internet.

En savoir plus :

* [Configurer une connexion de réseau virtuel à réseau virtuel à l’aide d’Azure Resource Manager et de PowerShell](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

## <a name="availability"></a>Disponibilité
La disponibilité est au cœur de tout programme de sécurité. Si vos utilisateurs et les systèmes ne peuvent pas accéder à ce dont ils ont besoin via le réseau, le service peut être considéré comme compromis. Azure propose des technologies de mise en réseau qui prennent en charge les mécanismes de haute disponibilité suivants :

* Équilibrage de charge basé sur HTTP
* Équilibrage de charge au niveau du réseau
* Équilibrage de charge global

L’équilibrage de charge est un mécanisme conçu pour répartir équitablement les connexions entre plusieurs appareils. Il a plusieurs objectifs :

* Augmenter la disponibilité. Lorsque vous équilibrez la charge des connexions sur plusieurs appareils, l’indisponibilité d’un ou plusieurs appareils ne compromet pas le service. Les services exécutés sur les appareils toujours en ligne continuent à fournir le contenu du service.
* Améliorer les performances. Lorsque vous équilibrez la charge des connexions sur plusieurs appareils, l’ensemble des appareils prennent en charge le traitement. Les requêtes de traitement et de mémoire pour distribuer le contenu sont ainsi réparties sur plusieurs appareils.

### <a name="http-based-load-balancing"></a>Équilibrage de charge basé sur HTTP
Les organisations qui exécutent des services basés sur le web privilégient souvent un équilibreur de charge basé sur HTTP. Cela permet de garantir des niveaux de haute disponibilité et de performance suffisants pour ces services. Les équilibreurs de charge classiques basés sur le réseau s’appuient sur des protocoles de couche réseau et transport. Les équilibreurs de charge basés sur HTTP, quant à eux, répartissent la charge en fonction des caractéristiques du protocole HTTP.

La passerelle Azure Application Gateway vous permet de mettre en place un équilibrage de charge basé sur HTTP pour vos services basés sur le web. Application Gateway prend en charge :

* L’affinité de session basée sur les cookies. Cette fonctionnalité permet de s’assurer que les connexions établies avec l’un des serveurs situé derrière l’équilibreur de charge restent opérationnelles entre le client et le serveur. La stabilité des transactions est ainsi garantie.
* Le déchargement SSL. Lorsqu’un client se connecte avec l’équilibreur de charge, cette session est chiffrée à l’aide du protocole HTTPS (SSL). Toutefois, afin d’améliorer les performances, vous pouvez utiliser le protocole HTTP (non chiffré) pour la connexion entre l’équilibreur de charge et le serveur web situé derrière l’équilibreur de charge. C’est le principe du « déchargement SSL ». En effet, les serveurs web situés derrière l’équilibreur de charge ne sont pas affectés par la surcharge de traitement inhérente au chiffrement. Les serveurs web peuvent donc traiter les demandes plus rapidement.
* Le routage du contenu basé sur l’URL. Cette fonctionnalité permet à l’équilibreur de charge de répartir les connexions en fonction de l’URL cible. Cela offre une plus grande flexibilité que les solutions qui répartissent la charge en fonction des adresses IP.

En savoir plus :

* [Vue d’ensemble d’Application Gateway](../application-gateway/application-gateway-introduction.md)

### <a name="network-level-load-balancing"></a>Équilibrage de charge au niveau du réseau
Contrairement à l’équilibrage de charge basé sur HTTP, l’équilibrage de charge au niveau du réseau effectue la répartition en fonction des adresses IP et des numéros de port (TCP ou UDP).
Pour bénéficier des avantages de l’équilibrage de charge au niveau du réseau, vous pouvez utiliser l’équilibreur de charge Azure Load Balancer. Voici ses principales caractéristiques :

* Équilibrage de charge au niveau du réseau basé sur les adresses IP et les numéros de port.
* Prise en charge de l’ensemble des protocoles de couche d’application.
* Répartition de la charge sur les instances de rôle de services cloud et de machines virtuelles Azure.
* Peut être utilisé à la fois pour les applications et les machines virtuelles accessibles sur Internet (équilibrage de charge externe) et non accessibles sur Internet (équilibrage de charge interne).
* Surveillance de point de terminaison, utilisée pour déterminer si l’un des services situés derrière l’équilibreur de charge n’est plus disponible.

En savoir plus :

* [Équilibrage de charge accessible sur Internet entre plusieurs services ou machines virtuelles](../load-balancer/load-balancer-internet-overview.md)
* [Présentation de l’équilibrage de charge interne](../load-balancer/load-balancer-internal-overview.md)

### <a name="global-load-balancing"></a>Équilibrage de charge global
Certaines organisations souhaitent bénéficier du plus haut niveau de disponibilité possible. Pour y parvenir, une option consiste à héberger les applications dans des centres de données répartis dans le monde entier. Lorsqu’une application est hébergée dans des centres de données répartis dans le monde entier, celle-ci restera opérationnelle même si une région géopolitique entière devient indisponible.

Cette stratégie d’équilibrage de charge peut également contribuer à améliorer les performances. Vous pouvez diriger les demandes du service vers le centre de données le plus proche de l’appareil effectuant la demande.

Pour bénéficier des avantages de l’équilibrage de charge global, vous pouvez utiliser Azure Traffic Manager.

En savoir plus :

* [Qu’est-ce que Traffic Manager ?](../traffic-manager/traffic-manager-overview.md)


## <a name="name-resolution"></a>Résolution de noms
La résolution de noms est une fonctionnalité essentielle pour tous les services que vous hébergez dans Azure. En effet, une résolution de noms sécurisée permet d’éviter qu’un cybercriminel ne redirige les requêtes de vos sites vers un site malveillant. Une résolution de noms sécurisée est donc requise pour tous vos services hébergés dans le cloud.

Il existe deux types de résolution de noms :

* Résolution de noms interne. Elle est utilisée par les services sur vos réseaux virtuels, vos réseaux locaux ou les deux. Les noms utilisés pour la résolution de noms interne ne sont pas accessibles sur Internet. Pour une sécurité optimale, votre schéma de résolution de noms interne ne doit pas être accessible aux utilisateurs externes.
* Résolution de noms externe. Elle est utilisée par les personnes et les appareils extérieurs à vos réseaux locaux et à vos réseaux virtuels. Les noms sont visibles sur Internet et utilisés pour établir la connexion avec vos services basés dans le cloud.

Pour la résolution de noms interne, vous avez deux options :

* Serveur DNS du réseau virtuel. Lorsque vous créez un réseau virtuel, un serveur DNS est créé pour vous. Ce serveur DNS peut résoudre les noms des machines hébergées sur ce réseau virtuel. Ce serveur DNS n’est pas configurable, est géré par le gestionnaire de structure Azure et peut donc protéger votre solution de résolution de noms.
* Définissez votre propre serveur DNS. Vous avez la possibilité d’ajouter un serveur DNS de votre choix sur votre réseau virtuel. Ce serveur DNS peut être un serveur DNS intégré à Active Directory, ou une solution de serveur DNS dédiée fournie par un partenaire Azure et que vous pouvez obtenir à partir de la Place de marché Microsoft Azure.

En savoir plus :

* [Présentation du réseau virtuel](../virtual-network/virtual-networks-overview.md)
* [Gestion des serveurs DNS utilisés par un réseau virtuel](../virtual-network/manage-virtual-network.md#change-dns-servers)

Pour la résolution de noms externe, vous avez deux options :

* Héberger votre propre serveur DNS externe en local.
* Héberger votre propre serveur DNS externe via un fournisseur de services.

Les grandes organisations préfèrent généralement héberger leurs serveurs DNS en local. Elles peuvent le faire car elles disposent des connaissances en mise en réseau et de la présence mondiale requises.

Dans la plupart des cas, il est préférable de faire appel à un fournisseur de services pour héberger vos services de résolution de noms DNS. Ces fournisseurs de services possèdent les connaissances en mise en réseau et la présence mondiale pour garantir une très haute disponibilité à vos services de résolution de noms. La disponibilité est essentielle pour les services DNS, car si vos services de résolution de noms deviennent indisponibles, personne ne pourra se connecter à vos services accessibles sur Internet.

Azure vous propose une solution DNS externe à haute disponibilité et performante, connue sous le nom d’Azure DNS. Cette solution de résolution de noms externe repose sur l’infrastructure DNS Azure mondiale. Elle vous permet d’héberger votre domaine dans Azure en utilisant les mêmes informations d’identification, API, outils et services de facturation que pour vos autres services Azure. Elle intègre également les contrôles de sécurité performants de la plateforme.

En savoir plus :

* [Vue d’ensemble d’Azure DNS](../dns/dns-overview.md)

## <a name="perimeter-network-architecture"></a>Architecture du réseau de périmètre
De nombreuses organisations utilisent les réseaux de périmètre pour segmenter leurs réseaux et créer une zone de mémoire tampon entre Internet et leurs services. La partie périmètre du réseau est considérée comme une zone de sécurité faible qui n’héberge aucune ressource de valeur. Généralement, les appareils de sécurité du réseau ayant une interface réseau sur le segment du réseau de périmètre sont visibles. Une autre interface réseau est connectée à un réseau comportant des machines virtuelles et des services qui acceptent les connexions entrantes à partir d’Internet.

Vous pouvez concevoir des réseaux de périmètre de différentes manières. En fonction de vos exigences de sécurité du réseau, vous pouvez décider ou non de déployer un réseau de périmètre et choisir le type de réseau de périmètre à utiliser le cas échéant.

En savoir plus :

* [Services cloud et sécurité réseau Microsoft](../best-practices-network-security.md)


## <a name="monitoring-and-threat-detection"></a>Surveillance et détection des menaces

Azure fournit des fonctions pour vous aider dans ce domaine clé avec la détection précoce, la surveillance, ainsi que la collecte et l’examen du trafic réseau.

### <a name="azure-network-watcher"></a>Azure Network Watcher
Azure Network Watcher vous aide à résoudre les problèmes et fournit un nouvel ensemble d’outils pour faciliter l’identification des problèmes de sécurité.

[L’affichage des groupes de sécurité](../network-watcher/network-watcher-security-group-view-overview.md) contribue à l’audit et à la conformité des machines virtuelles. Cette fonctionnalité vous permet d’effectuer des audits de programmation comparant les stratégies de lignes de base définies par votre organisation aux règles en vigueur pour chacune de vos machines virtuelles. Cela peut vous aider à identifier les différences de configuration.

[La capture de paquets](../network-watcher/network-watcher-packet-capture-overview.md) vous permet de capturer le trafic réseau vers et depuis la machine virtuelle. Vous pouvez collecter les statistiques réseau et résoudre les problèmes d’application, ce qui peut être très utile lors des enquêtes sur les intrusions. Vous pouvez également utiliser cette fonctionnalité avec Azure Functions pour démarrer les captures réseau en réponse à des alertes Azure spécifiques.

Pour plus d’informations sur Network Watcher et pour savoir comment commencer à tester certaines fonctionnalités dans vos laboratoires, consultez la [présentation de la surveillance Azure Network Watcher](../network-watcher/network-watcher-monitoring-overview.md).

>[!NOTE]
Pour les notifications les plus récentes sur la disponibilité et l’état de ce service, consultez [la page relative aux mises à jour d’Azure](https://azure.microsoft.com/updates/?product=network-watcher).

### <a name="azure-security-center"></a>Azure Security Center
Azure Security Center vous aide à prévenir, détecter et résoudre les menaces, en vous apportant une visibilité et un contrôle accrus de la sécurité de vos ressources Azure. Il fournit une surveillance de la sécurité et une gestion des stratégies intégrées pour l’ensemble de vos abonnements Azure, vous aidant ainsi à détecter les menaces qui pourraient passer inaperçues. De plus, il est compatible avec un vaste ensemble de solutions de sécurité.

Security Center vous permet d’optimiser et de surveiller la sécurité réseau grâce aux opérations suivantes :

* Mise à disposition de recommandations relatives à la sécurité réseau.
* Surveillance de l’état de votre configuration de la sécurité réseau.
* Envoi d’alertes destinées à vous informer de toute menace touchant le réseau, au niveau des points de terminaison comme à celui du réseau.

En savoir plus :

* [Présentation d’Azure Security Center](../security-center/security-center-intro.md)


### <a name="logging"></a>Journalisation
La journalisation au niveau du réseau est un élément clé de tout scénario de sécurité réseau. Dans Azure, vous pouvez consigner les informations obtenues pour les NSG afin de collecter les données de journalisation au niveau du réseau. La journalisation des groupes de sécurité réseau vous permet de consigner les données des journaux suivants :

* [Journaux d’activité](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md). Utilisez ces journaux pour consulter toutes les opérations envoyées à vos abonnements Azure. Ces journaux sont activés par défaut et peuvent être affichés dans le portail Azure. Ils étaient auparavant nommés « Journaux d’audit » ou « Journaux des opérations ».
* Journaux d’événements. Ces journaux permettent de savoir quelles règles de groupe de sécurité réseau (NSG) ont été appliquées.
* Journaux des compteurs. Ces journaux affichent le nombre de fois où chaque règle NSG a été appliquée pour refuser ou autoriser le trafic.

Vous pouvez également utiliser [Microsoft Power BI](https://powerbi.microsoft.com/what-is-power-bi/), un puissant outil de visualisation de données, pour afficher et analyser ces journaux.

En savoir plus :

* [Analyse de journaux pour les groupes de sécurité réseau (NSG)](../virtual-network/virtual-network-nsg-manage-log.md)
