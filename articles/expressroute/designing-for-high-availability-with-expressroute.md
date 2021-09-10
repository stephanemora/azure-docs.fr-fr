---
title: 'Azure ExpressRoute : Conception pour la haute disponibilité'
description: Cette page fournit des conseils sur l’architecture pour la haute disponibilité lors de l’utilisation d’Azure ExpressRoute.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: article
ms.date: 06/28/2019
ms.author: duau
ms.openlocfilehash: 5dcf58dce7b87862c2f01ad76db8aff66366ee4b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122524518"
---
# <a name="designing-for-high-availability-with-expressroute"></a>Conception pour une haute disponibilité avec ExpressRoute

ExpressRoute est conçu pour la haute disponibilité afin de fournir à l’opérateur une connectivité de réseau privé de qualité aux ressources Microsoft. En d’autres termes, il n’existe aucun point de défaillance unique dans le chemin d’accès ExpressRoute au sein du réseau de Microsoft. Pour optimiser la disponibilité, le client et le segment de fournisseur de service de votre circuit ExpressRoute doivent également être conçus pour la haute disponibilité. Dans cet article, nous allons tout d’abord passer en revue les considérations relatives à l’architecture réseau pour la conception d’une connectivité réseau robuste à l’aide d’ExpressRoute. Nous analyserons ensuite les fonctionnalités qui vous aident à améliorer la haute disponibilité de votre circuit ExpressRoute.

>[!NOTE]
>Les concepts décrits dans cet article s’appliquent tout autant lorsqu’un circuit ExpressRoute est créé sous Virtual WAN ou à l’extérieur de celui-ci.
>

## <a name="architecture-considerations"></a>Considérations relatives à l’architecture

Le schéma suivant montre la méthode de connexion recommandée à l’aide d’un circuit ExpressRoute afin d’optimiser la disponibilité d’un circuit ExpressRoute.

 [![1]][1]

Pour une haute disponibilité, il est primordial de maintenir la redondance du circuit ExpressRoute dans le réseau de bout en bout. En d’autres termes, vous devez conserver la redondance au sein de votre réseau local et ne devez pas la compromettre au sein de votre réseau de fournisseur de service. Pour conserver la redondance à son minimum, il faut éviter des points de défaillance réseau uniques. Vous pouvez davantage améliorer la haute disponibilité en disposant d’une alimentation et d’un refroidissement redondants pour vos appareils réseau.

### <a name="first-mile-physical-layer-design-considerations"></a>Considérations relatives à la conception de couche physique pour le premier mile

 Si vous arrêtez les connexions primaires et secondaires d’un circuit ExpressRoute sur le même Customer Premises Equipment (CPE), vous compromettez la haute disponibilité au sein de votre réseau local. De plus, si vous configurez les connexions primaires et secondaires via le même port qu’un CPE (en arrêtant les deux connexions sous différentes interfaces ou en les fusionnant au sein d’un réseau partenaire), vous forcez le partenaire à compromettre la haute disponibilité sur leur segment réseau également. C’est ce qu’illustre le diagramme suivant.

[![2]][2]

En revanche, si vous arrêtez les connexions primaires et secondaires d’un circuit ExpressRoute dans différents emplacements géographiques, il se peut que vous compromettiez les performances réseau de la connectivité. Si le trafic est activement équilibré entre les connexions primaires et secondaires arrêtées dans différents emplacements géographiques, une différence potentielle considérable dans la latence du réseau entre les deux chemins entraînerait des sous-performances réseau. 

Pour des considérations relatives à la conception de la géoredondance, consultez [Conception pour une reprise d’activité après sinistre][DR].

### <a name="active-active-connections"></a>Connexions entre deux passerelles actives

Le réseau Microsoft est configuré pour opérer les connexions primaires et secondaires des circuits ExpressRoute en mode actif/actif. Toutefois, via vos annonces d’itinéraire, vous pouvez forcer les connexions redondantes d’un circuit ExpressRoute à opérer en mode actif/passif. L’annonce d’itinéraires plus spécifiques et l’ajout du chemin du système autonome BGP sont des techniques courantes utilisées pour préférer un chemin à un autre.

Pour améliorer la haute disponibilité, il est recommandé d’opérer les deux connexions d’un circuit ExpressRoute en mode actif/actif. Si vous autorisez les connexions à opérer en mode actif/actif, le réseau Microsoft équilibre les charges du trafic sur les connexions selon le flux.

L’exécution des connexions primaires et secondaires d’un circuit ExpressRoute en mode actif/passif risque de causer l’échec des deux connexions, après un autre échec dans le chemin actif. Les causes courantes d’échec sur le basculement sont le manque de gestion active de la connexion passive et les itinéraires obsolètes d’annonces de connexions passives.

Aussi, l’exécution des connexions primaires et secondaires d’un circuit ExpressRoute en mode actif/actif entraîne l’échec et le reroutage d’environ la moitié des flux, après l’échec de la connexion ExpressRoute. Ainsi, le mode actif/actif améliorera grandement le MTTR (temps moyen de récupération).

> [!NOTE]
> Au cours d’une activité de maintenance ou en cas d’événements non planifiés ayant un impact sur l’une de ces connexions, Microsoft préfère utiliser l’ajout d’un préfixe au chemin AS pour purger le trafic vers la connexion saine. Vous devez vérifier que le trafic peut être acheminé sur le chemin sain quand l’ajout d’un préfixe au chemin est configuré par Microsoft et que les publications d’itinéraires nécessaires sont configurées de manière appropriée pour éviter toute interruption de service. 
> 

### <a name="nat-for-microsoft-peering"></a>NAT pour le peering Microsoft 

Le peering Microsoft est conçue pour la communication entre les points de terminaison publics. Souvent, les points de terminaison privés locaux deviennent des NAT avec une IP publique sur le réseau du client ou du partenaire avant qu’ils ne communiquent via le peering Microsoft. En supposant que vous utilisez tous deux les connexions primaires et secondaires en mode actif/actif, où et comment vous utilisez le NAT affecte votre temps de récupération après la défaillance d’une des connexions ExpressRoute. Deux options NAT sont illustrées dans la figure suivante :

[![3]][3]

#### <a name="option-1"></a>Option 1 :

La traduction d’adresses réseau (NAT) est appliquée après avoir fractionné le trafic entre les connexions primaires et secondaires du circuit ExpressRoute. Pour répondre aux exigences avec état de la NAT, des pools NAT indépendants sont utilisés pour les appareils primaires et secondaires. Le trafic de retour arrivera sur le même périphérique par lequel le flux est sorti.

Si la connexion ExpressRoute échoue, la capacité à atteindre le pool NAT correspondant est alors interrompue. C’est pourquoi tous les flux réseau interrompus doivent être rétablis soit par TCP, soit par la couche Application après le délai d’attente de la fenêtre correspondante. Pendant la défaillance, Azure ne peut pas atteindre les serveurs locaux à l’aide de la NAT correspondante jusqu’à ce que la connectivité soit restaurée pour les connexions primaires ou secondaires du circuit ExpressRoute.

#### <a name="option-2"></a>Option n°2 :

Un pool NAT commun est utilisé avant de fractionner le trafic entre les connexions primaires et secondaires du circuit ExpressRoute. Il est important de préciser que l’utilisation d’un pool NAT commun avant fractionnement du trafic ne signifie pas qu’il y aura un point de défaillance unique, ce qui compromettrait la haute disponibilité.

Le pool NAT est accessible même si la connexion primaire ou secondaire échoue. C’est la raison pour laquelle la couche réseau elle-même peut réacheminer les paquets et accélérer la récupération après une défaillance. 

> [!NOTE]
> * Si vous utilisez l’option NAT 1 (des pools NAT indépendants pour les connexions ExpressRoute primaires et secondaires), et que vous mappez un port d’une adresse IP d’un des pools NAT à un serveur local, le serveur n’est plus accessible via le circuit ExpressRoute en cas d’échec de la connexion correspondante.
> * Mettre fin à des connexions BGP ExpressRoute sur les appareils avec état peut entraîner des problèmes de basculement pendant les maintenances planifiées ou non planifiées par Microsoft ou votre fournisseur ExpressRoute. Vous devez tester votre configuration pour vérifier que votre trafic basculera correctement et, quand cela est possible, arrêter les sessions BGP sur les appareils sans état.

## <a name="fine-tuning-features-for-private-peering"></a>Réglage des fonctionnalités du peering privé

Dans cette section, nous allons passer en revue des fonctionnalités facultatives (selon votre déploiement Azure et votre susceptibilité au temps de récupération moyen) qui visent à améliorer la haute disponibilité de votre circuit ExpressRoute. Plus précisément, nous allons voir un déploiement de passerelles de réseau virtuel ExpressRoute tenant compte de la zone, et la détection d’envoi bidirectionnel (BFD).

### <a name="availability-zone-aware-expressroute-virtual-network-gateways"></a>Passerelles de réseau virtuel ExpressRoute tenant compte de la zone de disponibilité

Une zone de disponibilité dans une région Azure est une combinaison d’un domaine d’erreur et d’un domaine de mise à jour. Si vous optez pour le déploiement Azure IaaS redondant interzone, il se peut que vous vouliez configurer des passerelles de réseau virtuel redondantes interzone qui termine le peering privé ExpressRoute. Pour en apprendre plus, consultez [À propos des passerelles de réseau virtuel redondantes interzone dans les zones de disponibilité Azure][zone redundant vgw]. Pour configurer une passerelle de réseau virtuel redondante interzone, consultez [Créer une passerelle de réseau virtuel redondante interzone dans les zones de disponibilité Azure][conf zone redundant vgw].

### <a name="improving-failure-detection-time"></a>Amélioration du temps de détection des défaillances

ExpressRoute prend en charge la détection d’envoi bidirectionnel via le peering privé. La détection d’envoi bidirectionnel réduit le temps de détection des défaillances sur le réseau de couche 2 entre Microsoft Enterprise Edge (MSEE) et leurs voisins BGP en local de 3 minutes environ à moins d’une seconde. Un temps de détection des défaillances plus rapide aide à accélérer la récupération. Pour en savoir plus, consultez [Configurer la détection d’envoi bidirectionnel sur ExpressRoute][BFD].

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, nous avons abordé la conception pour la haute disponibilité d’une connectivité de circuit ExpressRoute. Un point de peering du circuit ExpressRoute est épinglé à un emplacement géographique et par conséquent, peut être affecté par une défaillance grave affectant l’emplacement complet. 

Pour des considérations relatives à la conception de connectivité de réseau géoredondante de la dorsale principale de Microsoft qui peut résister à des défaillances catastrophiques, impactant une région entière, consultez [Conception d’une récupération après sinistre avec le peering privé ExpressRoute][DR].

<!--Image References-->
[1]: ./media/designing-for-high-availability-with-expressroute/exr-reco.png "Méthode recommandée pour se connecter via ExpressRoute"
[2]: ./media/designing-for-high-availability-with-expressroute/suboptimal-lastmile-connectivity.png "Connectivité inefficace du dernier mile"
[3]: ./media/designing-for-high-availability-with-expressroute/nat-options.png "Options NAT"


<!--Link References-->
[zone redundant vgw]: ../vpn-gateway/about-zone-redundant-vnet-gateways.md
[conf zone redundant vgw]: ../vpn-gateway/create-zone-redundant-vnet-gateway.md
[Configure Global Reach]: ./expressroute-howto-set-global-reach.md
[BFD]: ./expressroute-bfd.md
[DR]: ./designing-for-disaster-recovery-with-expressroute-privatepeering.md
