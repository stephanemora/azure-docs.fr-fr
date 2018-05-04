---
title: 'Azure DDoS Protection : bonnes pratiques et architecture de référence | Microsoft Docs'
description: Découvrez comment utiliser les données de journalisation pour obtenir des informations détaillées sur votre application.
services: security
author: barclayn
manager: mbaldwin
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/20/2018
ms.author: barclayn
ms.openlocfilehash: 4b2d785f5b9095a2decfc65ec46808ff6f65c38e
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2018
---
# <a name="azure-ddos-protection-best-practices-and-reference-architecture"></a>Azure DDoS Protection : bonnes pratiques et architecture de référence

Ce document est destiné aux décideurs informatiques et aux équipes de sécurité. Il demande un certain degré de familiarité avec Azure, la mise en réseau et la sécurité.

La conception d’applications capables de résister à des attaques par déni de service distribué (DDoS) nécessite un travail de planification et la prise en compte de divers modes d’échec. Ce document répertorie les bonnes pratiques à suivre pour concevoir des applications résistantes aux attaques DDoS dans Azure.

## <a name="types-of-attacks"></a>Types d’attaques

DDoS est un type d’attaque visant à épuiser les ressources d’une application. Son objectif est d’affecter la disponibilité de l’application et sa capacité à gérer des demandes légitimes. Ces attaques de plus en plus sophistiquées gagnent en importance et en impact. Les attaques DDoS peuvent être ciblées sur n’importe quel point de terminaison qui est publiquement accessible via Internet.

Azure fournit une protection contre les attaques DDoS. Cette protection est intégrée à la plateforme Azure par défaut sans frais additionnels. Outre la protection DDoS de base fournie dans la plateforme, une nouvelle offre nommée « [Azure DDoS Protection Standard](https://azure.microsoft.com/services/ddos-protection/) » propose des fonctionnalités avancées d’atténuation DDoS contre les attaques réseau et est automatiquement réglée pour protéger des ressources Azure spécifiques. Vous pouvez facilement activer cette protection durant la création de réseaux virtuels. Vous pouvez également l’activer après la création initiale ; aucun changement au niveau de l’application ou de la ressource ne s’impose.

![](media/azure-ddos-best-practices/image1.png)

Les attaques DDoS peuvent être réparties en trois grandes catégories.

### <a name="volumetric-attacks"></a>Attaques volumétriques

Les attaques volumétriques sont les attaques DDoS les plus courantes. Les attaques volumétriques sont des agressions par force brute qui ciblent les couches réseau et transport. Elles tentent d’épuiser les ressources comme les liaisons réseau. Ces attaques exploitent souvent plusieurs systèmes infectés pour saturer la couche réseau d’une quantité considérable de trafic apparemment légitime. Différents protocoles de couche réseau sont utilisés dans ce type d’attaque : ICMP (Internet Control Message Protocol), UDP (User Datagram Protocol), TCP (Transmission Control Protocol), etc.

Parmi les attaques DDoS les plus couramment utilisées au niveau de la couche réseau, citons la saturation TCP SYN, l’écho ICMP, la saturation UDP, DNS et l’amplification NTP. Ces attaques peuvent être utilisées non seulement pour perturber le service, mais aussi comme écran de fumée pour s’infiltrer dans un réseau de manière plus ciblée et plus néfaste. [L’exploit Memcached](https://www.wired.com/story/github-ddos-memcached/) qui a récemment affecté GitHub est un exemple d’attaque volumétrique. Cette attaque a pris pour cible le port UDP 11211 et a généré un volume de 1,35 To/s.

### <a name="protocol-attacks"></a>Attaques de protocole

Les attaques de protocole ciblent les protocoles d’application. Elles tentent d’épuiser toutes les ressources disponibles dans les périphériques d’infrastructure comme les pare-feu, les serveurs d’applications et les équilibreurs de charge. Les attaques de protocole utilisent des paquets mal formés ou contenant des anomalies de protocole. Voici comment elles opèrent : après l’envoi d’un grand nombre de requêtes ouvertes, les serveurs et autres périphériques de communication répondent et attendent un paquet en réponse. La cible tente de répondre aux requêtes ouvertes, provoquant tôt ou tard le plantage du système ciblé.

L’exemple le plus courant d’une attaque DDoS basée sur le protocole est la saturation TCP SYN. Dans ce genre d’attaque, des requêtes TCP SYN sont successivement dirigées vers une cible pour éventuellement la surcharger. L’objectif est de bloquer la cible. L’attaque menée contre Dyn en 2016, en plus de viser la couche application, comprenait des saturations TCP SYN ciblant le port 53 des serveurs DNS de Dyn.

### <a name="resource-attacks"></a>Attaques sur les ressources

Les attaques sur les ressources ciblent la couche application. Elles déclenchent des processus back-end dans le but de surcharger le système cible. Les attaques sur les ressources génèrent un trafic d’apparence normale, mais qui achemine des requêtes nécessitant une utilisation intensive du processeur au serveur. En comparaison, le volume de trafic nécessaire pour épuiser des ressources est inférieur à celui d’autres types d’attaques. Le trafic dans une attaque de ressource est indiscernable du trafic légitime, ce qui rend sa détection difficile. Les attaques sur les ressources concernent le plus souvent les services DNS et HTTP/HTTPS.

## <a name="shared-responsibility-in-the-cloud"></a>Responsabilité partagée dans le cloud

La mise en place d’une stratégie de défense approfondie est nécessaire pour lutter contre des attaques de plus en plus variées et sophistiquées. La sécurité est une responsabilité partagée entre le client et Microsoft. C’est ce que Microsoft appelle un [modèle de responsabilité partagée](https://azure.microsoft.com/blog/microsoft-incident-response-and-shared-responsibility-for-cloud-computing/). La figure ci-dessous illustre la répartition des responsabilités.

![](media/azure-ddos-best-practices/image2.png)

Grâce à nos bonnes pratiques, les clients Azure peuvent créer des applications distribuées à l’échelle mondiale qui sont conçues et testées pour l’échec.

## <a name="fundamental-best-practices"></a>Bonnes pratiques de base

Les attaques DDoS sont en hausse. La section suivante fournit des conseils normatifs pour générer des services résistants aux attaques DDoS sur Azure.

### <a name="design-for-security"></a>Conception dans l’optique de la sécurité

Les clients doivent faire de la sécurité leur priorité tout au long du cycle de vie d’une application, de la conception et de l’implémentation au déploiement et aux opérations. Les applications peuvent contenir des bogues qui laissent passer un volume relativement faible de requêtes conçues pour utiliser une quantité anormale de ressources, provoquant une panne de service. Pour protéger un service fonctionnant sur Microsoft Azure, les clients doivent bien comprendre l’architecture de leur application et respecter les [cinq piliers de la qualité logicielle](https://docs.microsoft.com/azure/architecture/guide/pillars).
Les clients doivent avoir connaissance des volumes de trafic habituels, du modèle de connectivité entre l’application et d’autres applications, et des points de terminaison de service exposés à l’Internet public.

De plus, il est extrêmement important qu’ils conçoivent une application suffisamment résiliente pour surmonter une attaque ciblée par déni de service. La plateforme Azure intègre des fonctionnalités de sécurité et de confidentialité, à commencer par [SDL (Security Development Lifecycle)](https://www.microsoft.com/sdl/default.aspx). SDL tient compte de la sécurité à chaque phase de développement et vérifie qu’Azure est continuellement mis à jour pour le rendre encore plus sécurisé.

### <a name="design-for-scalability"></a>Conception dans l’optique de la scalabilité

Capacité d’un système à traiter une charge accrue. Les clients doivent concevoir leurs applications de telle sorte qu’elles puissent être [mises à l’échelle horizontalement](https://docs.microsoft.com/azure/architecture/guide/design-principles/scale-out) pour répondre à la demande d’une charge amplifiée, en particulier dans le cadre d’une attaque DDoS. Si votre application repose sur une seule instance unique d’un service, cela crée un point de défaillance unique. L’approvisionnement de plusieurs instances améliore à la fois la résilience et l’extensibilité.

Pour [Azure App Service](../app-service/app-service-value-prop-what-is.md), sélectionnez un [plan App Service](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) qui offre plusieurs instances. Pour les Azure Cloud Services, configurez chacun de vos rôles de manière à utiliser [plusieurs instances](../cloud-services/cloud-services-choose-me.md). Pour [Machines virtuelles Azure](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-about/?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), vérifiez que votre architecture de machines virtuelles inclut plusieurs machines virtuelles et que chacune d’elles figure dans un [groupe à haute disponibilité](../virtual-machines/virtual-machines-windows-manage-availability.md). Nous vous recommandons d’utiliser des [groupes de machines virtuelles identiques](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) pour les fonctionnalités de mise à l’échelle automatique.

### <a name="defense-in-depth"></a>Défense en profondeur

L’idée derrière la défense en profondeur est de gérer les risques avec des stratégies de protection variées. Le fait de superposer des défenses dans une application réduit les chances de réussite d’une attaque. Nous recommandons aux clients d’implémenter des conceptions sécurisées pour leurs applications par le biais des fonctionnalités intégrées à la plateforme Azure.

Par exemple, plus la taille ou la surface d’exposition de l’application est importante, plus le risque d’attaque est élevé. Il est donc recommandé de réduire la surface d’exposition par le biais de listes vertes pour fermer l’espace d’adressage IP et les ports d’écoute exposés qui ne sont pas utilisés sur les équilibreurs de charge ([ALB](../load-balancer/load-balancer-get-started-internet-portal.md)/[App Gateway](../application-gateway/application-gateway-create-probe-portal.md)) ou par l’intermédiaire de [groupes de sécurité réseau (NSG)](../virtual-network/virtual-networks-nsg.md).
Vous pouvez utiliser des [balises de service](/virtual-network/security-overview.md) et des [groupes de sécurité d’application](/virtual-network/security-overview.md) pour simplifier la création de règles de sécurité et configurer la sécurité réseau comme prolongement naturel de la structure d’une application.

Les clients doivent déployer les services Azure dans un [réseau virtuel](../virtual-network/virtual-networks-overview.md) dans la mesure du possible. Les ressources de service peuvent ainsi communiquer par le biais d’adresses IP privées. Le trafic du service Azure à partir d’un réseau virtuel utilise des adresses IP publiques comme adresses IP source par défaut. Le fait d’utiliser des [points de terminaison de service](../virtual-network/virtual-network-service-endpoints-overview.md) force le trafic de service à utiliser des adresses privées de réseau virtuel comme adresses IP source lors de l’accès au service Azure à partir d’un réseau virtuel.

Il arrive fréquemment que les ressources locales d’un client soient attaquées en même temps que ses ressources dans Azure. Si vous connectez un environnement local à Azure, nous vous recommandons de réduire l’exposition des ressources locales à l’Internet public. Pour bénéficier des fonctionnalités de mise à l’échelle et de protection DDoS avancée d’Azure, déployez vos entités publiques connues dans Azure. Ces entités accessibles publiquement étant souvent la cible d’attaques DDoS, le fait de les placer dans Azure réduit l’impact sur vos ressources locales.

## <a name="azure-ddos-protection"></a>Protection DDoS dans Azure

Azure propose deux offres de service DDoS qui assurent une protection contre les attaques réseau (couche 3 et 4) : la protection DDoS de base (DDoS Protection Basic) et la protection DDoS standard (DDoS Protection Standard). 

### <a name="azure-ddos-basic-protection"></a>Azure DDoS Protection Basic

La protection de base est intégrée à Azure par défaut sans frais additionnels. De par son échelle et sa capacité, le réseau déployé à l’échelle mondiale d’Azure assure une défense contre les attaques courantes de la couche réseau. Cette défense est par ailleurs renforcée par le monitoring continu du trafic et l’atténuation en temps réel. DDoS Protection Basic ne nécessite aucun changement de la part de l’utilisateur au niveau de configuration ou de l’application. Tous les services Azure, notamment les services PaaS comme Azure DNS, sont protégés par DDoS Protection Basic.

![](media/azure-ddos-best-practices/image3.png)

Azure DDoS Protection Basic comprend des composants logiciels et matériels. Un plan de contrôle logiciel détermine le type de trafic acheminé vers des appliances matérielles qui analysent et suppriment le trafic d’attaque, le moment où l’acheminent a lieu ainsi que l’emplacement. Le plan de contrôle prend ces décisions en fonction de la *stratégie* de protection DDoS en vigueur à l’échelle de l’infrastructure. Cette stratégie est définie de façon statique et appliquée universellement à tous les clients Azure.

Par exemple, la stratégie DDoS Protection spécifie le volume de trafic à partir duquel la protection doit être *déclenchée* (autrement dit, le trafic du locataire doit être routé par le biais des appliances de nettoyage) et la façon dont les appliances de nettoyage doivent *atténuer* l’attaque.

Le service Azure DDoS Protection Basic a pour but de protéger l’infrastructure et la plateforme Azure. Il atténue le trafic quand celui-ci dépasse un débit important susceptible d’impacter plusieurs clients dans un environnement multi-locataire. Il ne prend en charge ni les alertes ni les stratégies personnalisées par client.

### <a name="azure-ddos-standard-protection"></a>Azure DDoS Protection Standard

La protection standard fournit des fonctionnalités améliorées d’atténuation des risques liés à DDoS et s’adapte automatiquement de manière à protéger vos ressources Azure spécifiques dans un réseau virtuel. La protection est simple à activer sur n’importe quel réseau virtuel, nouveau ou existant, et ne nécessite aucun changement au niveau de l’application ou des ressources. Elle offre plusieurs avantages par rapport au service de base, notamment la journalisation, la création d’alertes et la télémétrie. Les principales caractéristiques du service Azure DDoS Protection Standard sont présentées ci-dessous.

#### <a name="adaptive-realtime-tuning"></a>Réglage adaptatif en temps réel

Le service Azure DDoS Protection Basic contribue à protéger les clients, mais uniquement dans le but de ne pas impacter d’autres clients. Par exemple, si un service est provisionné pour un volume de trafic entrant légitime inférieur au *taux de déclenchement* de la stratégie de protection DDoS à l’échelle de l’infrastructure, une attaque DDoS sur les ressources du client peut passer inaperçue. De manière plus générale, la complexité des récentes attaques (comme les attaques DDoS multivectorielles) et les comportements spécifiques aux applications des locataires demandent des stratégies de protection personnalisées par client.
Pour cela, deux insights sont utilisés :

1. Apprentissage automatique des modèles de trafic de couche 3/4 par client (par adresse IP)
2. Réduction des faux positifs dans la mesure où l’échelle d’Azure permet d’absorber une quantité importante de trafic

![](media/azure-ddos-best-practices/image5.png)

#### <a name="ddos-protection-telemetry-monitoring--alerting"></a>Protection DDoS : télémétrie, monitoring et génération d’alertes

DDoS Protection Standard expose des données de télémétrie riches par le biais [d’Azure Monitor](/monitoring-and-diagnostics/monitoring-overview-azure-monitor.md) pendant la durée d’une attaque DDoS. La génération d’alertes peut être configurée pour l’une des métriques Azure Monitor exposées par DDoS Protection. La journalisation peut être intégrée à Splunk (Azure Event Hubs), Azure Log Analytics et Stockage Azure pour analyse avancée par le biais de l’interface de diagnostic d’Azure Monitor.

##### <a name="ddos-mitigation-policies"></a>Stratégies d’atténuation des risques liés à DDoS

Dans le portail Azure, cliquez sur **Surveillance** \> **Métriques**. Dans l’écran **Métriques**, sélectionnez le groupe de ressources, le type de ressource Adresse IP publique et votre adresse IP publique Azure. Les métriques DDoS sont visibles dans le volet des métriques disponibles.

DDoS Protection Standard applique **trois stratégies de prévention réglées automatiquement (TCP SYN, TCP et UDP)** pour chaque adresse IP publique de la ressource protégée, dans le réseau virtuel sur lequel la protection DDoS est activée. Vous pouvez afficher les seuils de stratégie en sélectionnant la métrique « **Paquets entrants pour déclencher l’atténuation DDoS** ».

![](media/azure-ddos-best-practices/image7.png)

Les seuils de stratégie sont configurés automatiquement par le biais de notre système de profilage du trafic réseau basé sur l’apprentissage automatique. L’atténuation des risques liés à DDoS pour une adresse IP n’a lieu que si le seuil de stratégie est dépassé.

##### <a name="under-ddos-attack"></a>Attaque DDoS en cours

Si l’adresse IP publique est attaquée, la valeur de la métrique « sous attaque DDoS ou non » passe à 1 à mesure que nous effectuons les opérations d’atténuation sur le trafic de l’attaque.

![](media/azure-ddos-best-practices/image8.png)

Nous vous recommandons de configurer une alerte sur cette métrique afin d’être averti en cas d’atténuation DDoS active sur votre adresse IP publique.

Pour plus d’informations, consultez [Gérer Azure DDoS Protection Standard à l’aide du portail Azure](../virtual-network/ddos-protection-manage-portal.md).

#### <a name="resource-attacks"></a>Attaques sur les ressources

Pour lutter spécifiquement contre les attaques sur les ressources au niveau de la couche application et renforcer la sécurité des applications web, les clients doivent configurer le pare-feu d’applications web (WAF). WAF inspecte le trafic web entrant pour bloquer les injections SQL, les scripts intersites, les attaques DDoS et autres attaques au niveau de la couche 7. [WAF est une fonctionnalité d’Application Gateway](../application-gateway/application-gateway-web-application-firewall-overview.md) fournie par Azure pour protéger de manière centralisée vos applications web contre les vulnérabilités et exploits courants. Les partenaires Azure proposent plusieurs offres WAF. Pour trouver celle la mieux adaptée à vos besoins, visitez la [Place de marché Azure](https://azuremarketplace.microsoft.com/marketplace/apps?search=WAF&page=1).

Même les pare-feu d’application web sont vulnérables aux attaques volumétriques et d’épuisement d’état. Nous vous recommandons fortement d’activer la protection DDoS Standard sur le réseau virtuel WAF pour vous protéger contre les attaques volumétriques et de protocole. Pour plus d’informations, consultez la section sur l’architecture de référence.

### <a name="protection-planning"></a>Planification de la protection

La planification et la préparation sont des étapes cruciales pour comprendre comment un système se comporte en cas d’attaque DDoS. Ces étapes vous aident également à concevoir un plan de gestion des incidents.

Les clients doivent vérifier que DDoS Protection Standard est activé sur le réseau virtuel des points de terminaison accessibles sur Internet. La configuration d’alertes DDoS vous permet d’être en permanence attentif aux attaques potentielles sur votre infrastructure. Les clients doivent monitorer leurs applications de manière indépendante. Ils doivent comprendre le comportement normal de l’application. Des mesures doivent être prises si l’application ne se comporte pas comme prévu durant une attaque DDoS.

#### <a name="ddos-attacks-orchestration"></a>Orchestration d’attaques DDoS

Nous vous conseillons de tester vos hypothèses sur la façon dont vos services répondent à une attaque, et ce avant même qu’elle ne se produise, au moyen de simulations périodiques. Ces tests permettent de vérifier que vos services ou applications continuent à fonctionner comme prévu et que l’expérience des utilisateurs finaux n’est pas interrompue. Identifiez les lacunes du point de vue de la technologie et du processus, et incorporez-les dans la stratégie de réponse DDoS. Il est généralement recommandé d’effectuer ces tests dans des environnements de préproduction ou durant les heures creuses afin de minimiser l’impact sur l’environnement de production.

Nous travaillons en collaboration avec [BreakingPoint Cloud](https://www.ixiacom.com/products/breakingpoint-cloud) pour créer une interface à l’aide de laquelle les clients Azure peuvent générer du trafic sur les points de terminaison publics dotés de DDoS Protection à des fins de simulation. La simulation [BreakPoint Cloud](https://www.ixiacom.com/products/breakingpoint-cloud) vous permet d’effectuer les tâches suivantes :

- Valider la façon dont Microsoft Azure DDoS Protection protège vos ressources Azure contre les attaques DDoS

- Optimiser votre processus de réponse aux incidents en cas d’attaque DDoS

- Documenter la conformité DDoS

- Former des équipes de sécurité réseau

La cybersécurité est une bataille sans relâche qui nécessite des innovations constantes en matière de défense. La protection Standard DDoS d’Azure est une solution de pointe qui permet aux clients d’atténuer de manière efficace les attaques DDoS de plus en plus complexes.

## <a name="components-of-a-ddos-response-strategy"></a>Composants d’une stratégie de réponse DDoS

Dans la plupart des cas, une attaque DDoS ciblant vos ressources Azure nécessite une intervention minimale de la part de l’utilisateur final. Néanmoins, le fait d’incorporer l’atténuation des risques liés à DDoS à la stratégie de réponse aux incidents de l’organisation garantit un impact minimal sur l’activité.

### <a name="microsoft-threat-intelligence"></a>Microsoft Threat Intelligence

Microsoft dispose d’un réseau complet de renseignements sur les menaces. Celui-ci s’appuie sur les connaissances d’une communauté de spécialistes de la sécurité prenant en charge les services en ligne de Microsoft, sur les partenaires Microsoft et sur les relations au sein de la communauté dédiée à la sécurité d’Internet. En tant que fournisseur d’infrastructures de premier plan, Microsoft reçoit des avertissements précoces sur les menaces et les évalue à la lumière des renseignements communiqués par d’autres services en ligne Microsoft et la clientèle mondiale de Microsoft. Tous les renseignements sur les menaces collectés par Microsoft sont incorporés dans les produits Azure DDoS Protection.

Par ailleurs, le service DCU (Digital Crimes Unit) de Microsoft lance des stratégies offensives contre les botnets, une source courante de commande et de contrôle pour les attaques DDoS.

### <a name="perform-a-risk-evaluation-of-your-azure-resources"></a>Effectuer une évaluation des risques de vos ressources Azure

Il est impératif de comprendre en permanence la portée des risques liés à une attaque DDoS. Les clients doivent donc se poser régulièrement les questions suivantes : parmi les nouvelles ressources Azure disponibles publiquement, quelles sont celles qui doivent être protégées ? Existe-t-il un point de défaillance unique dans le service ? Comment faire pour isoler les services de manière à limiter l’impact d’une attaque tout en les rendant accessibles aux clients valides ? Y a-t-il des réseaux virtuels dans lesquels le service DDoS Protection Standard n’est pas activé alors qu’il devrait l’être ? Mes services sont-ils actifs/actifs avec basculement dans plusieurs régions ?

### <a name="customer-ddos-response-team"></a>Équipe de réponse aux attaques DDoS du client

La création d’une équipe de réponse DDoS est essentielle pour garantir une réponse rapide et efficace en cas d’attaque. Les clients doivent identifier dans votre organisation plusieurs contacts qui seront en charge de la planification et de l’exécution. L’équipe de réponse DDoS doit avoir une connaissance approfondie du service Azure DDoS Protection Standard. Elle doit pouvoir non seulement identifier et atténuer une attaque, mais aussi collaborer avec différents clients internes et externes, notamment l’équipe de support Microsoft en cas de besoin.

Microsoft vous recommande d’incorporer des exercices de planification et de simulation faisant appel à l’équipe de réponse DDoS, notamment des tests de mise à l’échelle, dans le cadre normal de la planification de la disponibilité et de la continuité de votre service. 

### <a name="during-an-attack"></a>Durant une attaque

Azure DDoS Protection Standard identifie et atténue les attaques DDoS sans aucune intervention de l’utilisateur. Pour être averti en cas d’atténuation active pour une adresse IP publique protégée, vous pouvez [configurer une alerte](../virtual-network/ddos-protection-manage-portal.md) sur la métrique « sous attaque DDoS ou non ». Vous pouvez passer en revue les alertes et en créer de nouvelles pour les autres métriques DDoS afin de comprendre l’échelle des attaques, le trafic abandonné, etc.

#### <a name="when-to-contact-microsoft-support"></a>Quand contacter le support Microsoft

- Si, au cours d’une attaque DDoS, vous constatez que les performances de la ressource protégée sont fortement dégradées ou que la ressource n’est pas disponible.

- Si vous pensez que le service DDoS Protection ne se comporte pas comme prévu. Le service DDoS Protection lance uniquement une procédure d’atténuation si les critères ci-dessous sont remplis :

    - La valeur de la métrique « Stratégie pour déclencher l’atténuation DDoS (TCP/TCP SYN/UDP) » est inférieure au trafic reçu sur la ressource IP publique protégée.

- Si vous savez qu’un événement viral planifié va entraîner une augmentation importante de votre trafic réseau.

- Si un acteur menace de lancer une attaque DDoS sur vos ressources.

Pour les problèmes affectant des ressources d’entreprise critiques, créez un [ticket de support](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) avec un niveau de gravité A.

### <a name="post-attack-steps"></a>Étapes après l’attaque

Il est toujours bon d’effectuer un compte-rendu après une attaque et de réajuster la stratégie de réponse DDoS au besoin. Points importants à prendre en compte :

- Le manque de scalabilité de l’architecture a-t-il perturbé le service ou l’expérience utilisateur final ?

- Quels sont les services ou les applications qui ont souffert le plus ?

- Quel a été le degré d’efficacité de la stratégie de réponse DDoS et comment peut-il être amélioré ?

Si vous pensez que vous êtes faites l’objet d’une attaque DDoS, transmettez le problème aux canaux de support Azure habituels.

## <a name="ddos-protection-reference-architectures"></a>Architectures de référence de la protection DDoS

DDoS Protection Standard est conçu [pour les services déployés dans un réseau virtuel](../virtual-network/virtual-network-for-azure-services.md). Pour les autres services, le service par défaut DDoS Protection Basic s’applique. Les architectures de référence spécifiques présentées ci-dessous sont organisées par scénarios, avec regroupement des modèles d’architecture.

### <a name="virtual-machine-windowslinux-workloads"></a>Charges de travail de machines virtuelles (Windows/Linux)

#### <a name="application-running-on-load-balanced-vms"></a>Application exécutée sur des machines virtuelles à charge équilibrée

Cette architecture de référence présente un ensemble de pratiques éprouvées pour exécuter plusieurs machines virtuelles Windows dans un groupe identique derrière un équilibreur de charge, afin d’améliorer la disponibilité et l’extensibilité. Cette architecture convient à n’importe quelle charge de travail sans état, par exemple un serveur web.

![](media/azure-ddos-best-practices/image9.png)

Dans cette architecture, une charge de travail est répartie entre plusieurs instances de machine virtuelle. Il existe une seule adresse IP publique, et le trafic Internet est réparti entre les machines virtuelles à l’aide d’un équilibreur de charge. DDoS Protection Standard est activé sur le réseau virtuel de l’équilibreur de charge Azure (Internet) associé à l’adresse IP publique.

L’équilibreur de charge distribue les demandes Internet entrantes aux instances de machine virtuelle. Les groupes de machines virtuelles identiques permettent d’augmenter ou de réduire le nombre de machines virtuelles manuellement ou automatiquement en fonction de règles prédéfinies. Ceci est important si la ressource fait l’objet d’une attaque DDoS. Pour plus d’informations sur cette architecture de référence, consultez [cet article](https://docs.microsoft.com/azure/architecture/reference-architectures/virtual-machines-windows/multi-vm).

#### <a name="applications-running-on-windows-n-tier"></a>Applications exécutées sur une architecture Windows multiniveau

Il existe de nombreuses façons d’implémenter une architecture multiniveau. Le diagramme illustre une application web à 3 niveaux standard. Cette architecture repose sur celle décrite dans [Exécuter des machines virtuelles à charge équilibrée pour la scalabilité et la disponibilité](https://docs.microsoft.com/azure/architecture/reference-architectures/virtual-machines-windows/multi-vm). Les niveaux Web et Business utilisent des machines virtuelles à charge équilibrée.

![](media/azure-ddos-best-practices/image10.png)

Dans l’architecture ci-dessus, DDoS Protection Standard est activé sur le réseau virtuel. Toutes les adresses IP publiques dans le réseau virtuel bénéficient d’une protection DDoS au niveau des couches 3 et 4. Pour protéger la couche 7, il est nécessaire de déployer Application Gateway dans la référence (SKU) WAF. Pour plus d’informations sur cette architecture de référence, consultez [cet article](https://docs.microsoft.com/azure/architecture/reference-architectures/virtual-machines-windows/n-tier).

#### <a name="paas-web-application"></a>Application web PaaS

Cette architecture de référence montre l’exécution d’une application Azure App Service dans une seule région. Cette architecture présente un ensemble de pratiques éprouvées pour une application web utilisant [Azure App Service](https://azure.microsoft.com/documentation/services/app-service/) et [Azure SQL Database](https://azure.microsoft.com/documentation/services/sql-database/).
La région de secours est configurée pour les scénarios de basculement.

![](media/azure-ddos-best-practices/image11.png)

Traffic Manager achemine les requêtes entrantes à Application Gateway dans l’une des régions. Pendant le fonctionnement normal, il achemine les requêtes à Application Gateway dans la région active. Si cette région n’est plus disponible, Traffic Manager bascule sur Application Gateway dans la région de secours.

Tout le trafic provenant d’Internet à destination de l’application web est acheminé à [l’adresse IP publique d’Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-app-overview) par le biais de Traffic Manager. Dans ce scénario, App Service (Web Apps) n’est pas directement accessible de l’extérieur et est protégé par Application Gateway. Il est recommandé de configurer la référence (SKU) WAF Application Gateway (mode prévention) pour mettre en place une protection contre les attaques de couche 7 (HTTP/HTTPS/Web Socket). Par ailleurs, les applications web sont configurées pour [accepter uniquement le trafic provenant de l’adresse IP publique d’Application Gateway](https://azure.microsoft.com/blog/ip-and-domain-restrictions-for-windows-azure-web-sites/).

Pour plus d’informations sur cette architecture de référence, consultez [cet article](https://docs.microsoft.com/azure/architecture/reference-architectures/app-service-web-app/multi-region).

### <a name="mitigation-for-non-web-paas-services"></a>Atténuation pour les services PaaS non web

#### <a name="hdinsight-on-azure"></a>HDInsight sur Azure

Cette architecture de référence montre la configuration de DDoS Protection Standard pour un [cluster HDInsight](https://docs.microsoft.com/azure/hdinsight/) sur Azure. Vous devez vérifier que le cluster HDInsight est lié à un réseau virtuel et que DDoS Protection est activé sur ce réseau virtuel.

![](media/azure-ddos-best-practices/image12.png)

![](media/azure-ddos-best-practices/image13.png)

Dans cette architecture, le trafic destiné au cluster HDInsight en provenance d’internet est acheminé à l’adresse IP publique associée à l’équilibreur de charge de la passerelle HDInsight. L’équilibreur de charge de la passerelle envoie ensuite directement le trafic aux nœuds principaux ou aux nœuds worker. Si DDoS Protection Standard est activé sur le réseau virtuel HDInsight, toutes les adresses IP publiques dans le réseau virtuel bénéficient d’une protection DDoS au niveau des couches 3 et 4. L’architecture de référence ci-dessus peut être combinée avec les architectures de référence multiniveau/multirégion.

Pour plus d’informations sur cette architecture de référence, consultez la documentation [Étendre HDInsight à l’aide d’un réseau virtuel Azure](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network?toc=%2fazure%2fvirtual-network%2ftoc.json).

### <a name="azure-api-management"></a>Gestion des API Azure

Cette architecture de référence protège le point de terminaison public de la ressource [Gestion des API](../api-management/api-management-key-concepts.md) qui publie des API pour les clients externes à l’organisation. Le service Gestion des API (APIM) doit être déployé dans un réseau virtuel externe pour activer la protection DDoS.

![](media/azure-ddos-best-practices/image15.png)

Après configuration du réseau virtuel externe, la passerelle Gestion des API et le portail des développeurs sont accessibles à partir de l’Internet public par le biais d’un équilibreur de charge externe. Dans cette architecture, DDoS Protection Standard est activé sur le réseau virtuel APIM externe. Le trafic provenant d’Internet est acheminé à l’adresse IP publique d’APIM, qui bénéficie d’une protection contre les attaques réseau sur les couches 3 et 4. Pour vous protéger contre les attaques de couche 7 (HTTP/HTTPS), vous pouvez configurer une passerelle Application Gateway en mode WAF.

La liste des services supplémentaires qui sont déployés dans un réseau virtuel et qui peuvent être configurés pour DDoS Protection Standard est disponible [ici](../virtual-network/virtual-network-for-azure-services.md). DDoS Protection Standard prend uniquement en charge les ressources Azure Resource Manager. *Le déploiement d’ASE (Application Service Environment) injecté dans un réseau virtuel avec une adresse IP publique n’est pas pris en charge en mode natif.* Pour plus d’informations sur la protection d’ASE, consultez cette section.

## <a name="next-steps"></a>Étapes suivantes

* [Azure DDoS Protection : page produit](https://azure.microsoft.com/services/ddos-protection/)

* [Blog sur Azure DDoS Protection](http://aka.ms/ddosblog)

* [Documentation sur Azure DDoS Protection](../virtual-network/ddos-protection-overview.md)
