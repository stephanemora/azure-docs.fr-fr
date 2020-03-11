---
title: Standard Load Balancer et zones de disponibilité Azure
titleSuffix: Azure Load Balancer
description: Dans ce parcours d’apprentissage, familiarisez-vous avec Azure Standard Load Balancer et les Zones de disponibilité.
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/07/2019
ms.author: allensu
ms.openlocfilehash: 5a65982c5c13eb4e4273efcfd8d14910b0f35572
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/29/2020
ms.locfileid: "78197145"
---
# <a name="standard-load-balancer-and-availability-zones"></a>Référence Standard de Load Balancer et zones de disponibilité

Azure Standard Load Balancer prend en charge les scénarios des [zones de disponibilité](../availability-zones/az-overview.md). Vous pouvez utiliser Standard Load Balancer pour optimiser la disponibilité de votre scénario de bout en bout en alignant les ressources sur des zones et en les distribuant parmi les zones.  Consultez les [zones de disponibilité](../availability-zones/az-overview.md) pour obtenir des informations sur les zones de disponibilité, les régions qui les prennent actuellement en charge et d’autres concepts et produits connexes. Les zones de disponibilité associées à Standard Load Balancer constituent un ensemble de fonctionnalités vaste et flexible capable de créer de nombreux scénarios différents.  Consultez ce document pour comprendre ces [concepts](#concepts) et obtenir des [conseils de conception](#design) de scénarios.

## <a name="concepts"></a> Concepts des zones de disponibilité appliqués à Load Balancer

Une ressource Load Balancer elle-même est régionale mais jamais zonale. La granularité de ce que vous pouvez configurer est restreinte par chaque configuration de définition de pool principal, frontal et de règle.
Dans le contexte des zones de disponibilité, le comportement et les propriétés d’une règle d’équilibreur de charge sont décrits comme redondants interzone ou zonaux.  Ces adjectifs décrivent la zonalité d’une propriété.  Dans le contexte de Load Balancer, la redondance interzone implique toujours *plusieurs zones* tandis que l’adjectif zonal implique une isolation du service dans une *zone unique*.
À la fois public et interne, Load Balancer prend en charge les scénarios zonaux et redondants interzone. Le trafic peut être dirigé vers les zones en fonction des besoins (*équilibrage de charge entre les zones*). 

### <a name="frontend"></a>Serveur frontal

Un front-end d’équilibreur de charge est une configuration IP front-end qui fait référence à une ressource d’adresse IP publique ou à une adresse IP privée au sein du sous-réseau d’une ressource de réseau virtuel.  Il forme le point de terminaison à charge équilibrée où votre service est exposé.
Une ressource d’équilibreur de charge peut contenir règles avec des front-ends zonaux et redondants interzone en même temps. Quand une ressource IP publique ou une adresse IP privée est garantie dans une zone, la zonalité (ou son manque) n’est pas mutable.  Pour modifier ou omettre la zonalité d’un front-end d’adresse IP publique ou privée, vous devez recréer l’adresse IP publique dans la zone appropriée.  Les zones de disponibilité ne modifient pas les contraintes pour plusieurs front-ends, examinent [plusieurs front-ends pour l’équilibreur de charge](load-balancer-multivip-overview.md) afin d’obtenir les détails pour cette fonctionnalité.

#### <a name="zone-redundant"></a>Redondant interzone 

Dans une région avec des zones de disponibilité, un front-end Standard Load Balancer peut être redondant interzone.  La redondance interzone signifie que tous les flux entrants et sortants sont servis simultanément par plusieurs zones de disponibilité dans une région à l’aide d’une adresse IP unique. Les schémas de redondance DNS ne sont pas nécessaires. Une adresse IP de front-end unique peut survivre à des échecs de zone et être utilisée pour atteindre tous les membres du pool principal (non impactés) indépendamment de la zone. Une ou plusieurs zones de disponibilité peuvent échouer sans empêcher le chemin de données de survivre dans la mesure où une seule zone de la région reste saine. L’adresse IP unique du front-end est servie simultanément par plusieurs déploiements d’infrastructure indépendants dans plusieurs zones de disponibilité.  Il ne s’agit pas d’un chemin de données sans réponse, car toute nouvelle tentative ou tout rétablissement réussissent dans les autres zones non impactées par les défaillances de zone.   

#### <a name="optional-zone-isolation"></a>Isolation de la zone facultative

Vous pouvez choisir de garantir un frontend dans une seule zone, ce qui donne un *frontend zonal*.  Cela signifie que tout flux entrant ou sortant est servi par une seule zone dans une région.  Votre frontend connaît le même sort que l’intégrité de la zone.  Le chemin de données n’est pas affecté par les défaillances des zones autres que celles dans lesquelles il a été garanti. Vous pouvez utiliser des frontends zonaux pour exposer une adresse IP par zone de disponibilité.  

En outre, vous pouvez consommer des front-ends zonaux directement pour les points de terminaison à charge équilibrée dans chaque zone. Vous pouvez également procéder ainsi pour exposer des points de terminaison à charge équilibrée par zone afin de surveiller individuellement chaque zone.  Ou, pour les points de terminaison publics, vous pouvez les intégrer à un produit d’équilibrage de charge DNS comme [Traffic Manager](../traffic-manager/traffic-manager-overview.md) et utiliser un nom DNS unique. Le client effectue ensuite la résolution vers ce nom DNS pour plusieurs adresses IP zonales.  

Pour fusionner ces concepts (le même backend à la fois redondant interzone et zonal), passez en revue [Frontends multiples pour Azure Load Balancer](load-balancer-multivip-overview.md).

Pour un front-end Load Balancer public, vous ajoutez un paramètre *zones* à la ressource IP publique référencée par la configuration IP frontale utilisée par la règle respective.

Pour un frontend Load Balancer interne, ajoutez un paramètre *zones* à la configuration IP de frontend Load Balancer interne. Le frontend zonal amène Load Balancer à garantir une adresse IP d’un sous-réseau dans une zone spécifique.

### <a name="cross-zone-load-balancing"></a>Équilibrage de charge entre les zones

L’équilibrage de charge entre les zones est la capacité de Load Balancer à atteindre un point de terminaison backend dans n’importe quelle zone. Cette capacité est indépendante du frontend et de sa zonalité.  Toute règle d’équilibrage de charge peut cibler une instance back-end dans n’importe quelle zone de disponibilité ou instance régionale.

Vous devez prendre soin de construire votre scénario d’une manière qui exprime une notion de zones de disponibilité. Par exemple, vous devez garantir votre déploiement de machines virtuelles au sein d’une zone unique ou de plusieurs zones, et aligner les ressources zonales frontales et principales dans la même zone.  Si vous franchissez des zones de disponibilité avec uniquement des ressources zonales, le scénario fonctionne mais peut ne pas avoir un mode de défaillance clair en ce qui concerne les zones de disponibilité. 

### <a name="backend"></a>Backend

Load Balancer fonctionne avec des instances de machines virtuelles.  Celles-ci peuvent être autonomes, des groupes à haute disponibilité ou des groupes de machines virtuelles identiques.  Toute instance de machine virtuelle dans un réseau virtuel unique peut faire partie du pool principal, qu’elle ait été ou non garantie dans une zone ou quelle que soit la zone dans laquelle elle a été garantie.

Pour aligner et garantir votre front-end et votre back-end avec une zone unique, placez uniquement les machines virtuelles au sein de la même zone dans le pool principal respectif.

Pour traiter des machines virtuelles réparties dans plusieurs zones, placez-les simplement à partir de plusieurs zones dans le même pool principal.  Quand vous utilisez des groupes de machines virtuelles identiques, vous pouvez en placer un ou plusieurs dans le même pool backend.  Et chacun d’eux peut se trouver dans une seule ou plusieurs zones.

### <a name="outbound-connections"></a>Connexions sortantes

Les mêmes propriétés zonales et redondantes interzone s’appliquent aux [connexions sortantes](load-balancer-outbound-connections.md).  Une adresse IP publique redondante interzone utilisée pour les connexions sortantes est servie par toutes les zones. Une adresse IP publique zonale est servie uniquement par la zone dans laquelle elle est garantie.  Les allocations de port SNAT des connexions sortantes survivent aux défaillances de zone et votre scénario continuera à fournir une connectivité SNAT sortante si elle n’est pas impactée par les défaillances de zone.  Cela peut nécessiter des transmissions ou le rétablissement des connexions pour les scénarios redondants interzone si un flux a été servi par une zone impactée.  Les flux dans les zones autres que les zones impactées ne sont pas affectés.

L’algorithme de pré-allocation de port SNAT est le même avec ou sans zones de disponibilité.

### <a name="health-probes"></a>Sondes d’intégrité

Vos définitions de sonde d’intégrité existantes restent telles qu’elles sont sans zones de disponibilité.  Toutefois, nous avons développé le modèle d’intégrité au niveau de l’infrastructure. 

Quand vous utilisez des front-ends redondants interzone, Load Balancer développe son modèle d’intégrité interne pour sonder indépendamment l’accessibilité d’une machine virtuelle à partir de chaque zone de disponibilité et arrêter les chemins entre les zones susceptibles d’avoir été en échec sans intervention du client.  Si un chemin donné n’est pas disponible à partir de l’infrastructure Load Balancer d’une zone vers une machine virtuelle située dans une autre zone, Load Balancer peut détecter et éviter cet échec. Les autres zones qui peuvent accéder à cette machine virtuelle peuvent continuer de servir la machine virtuelle à partir de leurs frontends respectifs.  Par conséquent, il est possible que pendant des événements d’échec, chaque zone ait des distributions légèrement différentes des nouveaux flux pendant qu’elle protège l’intégrité globale de votre service de bout en bout.

## <a name="design"></a> Remarques relatives à la conception

Load Balancer est volontairement flexible dans le contexte des zones de disponibilité. Vous pouvez choisir de vous aligner sur des zones ou d’être redondant interzone pour chaque règle.  Une disponibilité accrue peut s’obtenir au prix d’une complexité accrue. C’est pourquoi vous devez concevoir la disponibilité à des fins de performances optimales.  Examinons certains points importants liés à la conception.

### <a name="automatic-zone-redundancy"></a>Redondance dans une zone automatique

Avec Load Balancer, il est simple d’avoir une adresse IP unique comme frontend redondant interzone. Une adresse IP redondante interzone peut servir en toute sécurité une ressource zonale dans toute zone et survivre à un ou plusieurs échecs de zone tant qu’une seule zone reste saine au sein de la région. Inversement, un frontend zonal est une réduction du service à une seule zone et partage son sort avec la zone concernée.

La redondance dans une zone n’implique pas de chemin de données ni de plan de contrôle sans réponse ; il s’agit expressément d’un plan de données. Les flux redondants interzone peuvent utiliser toutes les zones et les flux d’un client utilise toutes les zones saines dans une région. En cas d’échec de zone, les flux de trafic qui utilisent les zones saines à ce moment-là ne sont pas impactés.  Les flux de trafic qui font appel à une zone au moment de la défaillance de cette dernière peuvent être affectés, mais les applications peuvent récupérer. Ces flux peuvent se poursuivre dans les zones saines restantes dans la région après retransmission ou rétablissement, une fois qu’Azure a convergé autour de l’échec de zone.

### <a name="xzonedesign"></a> Limites entre les zones

Il est important de comprendre que dès lors qu’un service de bout en bout traverse des zones, vous partagez le sort non pas d’une seule zone mais éventuellement de plusieurs.  Par conséquent, votre service de bout en bout n’a peut-être pas obtenu de disponibilité sur des déploiements non zonaux.

Évitez d’introduire involontairement des dépendances entre les zones qui invalident les gains de disponibilité quand vous utilisez des zones de disponibilité.  Quand votre application comprend plusieurs composants que vous souhaitez résistants aux échecs de zone, veillez à garantir la survie de suffisamment de composants critiques en cas d’échec de zone.  Par exemple, un seul composant critique de votre application peut affecter toute votre application s’il se trouve dans une seule zone autre que les zones survivantes.  De plus, prévoyez également la restauration de la zone et la façon dont votre application converge. Vous devez comprendre comment votre application raisonne par rapport aux défaillances de certaines parties de l’application. Intéressons-nous à quelques points importants et utilisons-les pour poser les questions qui surviennent quand vous réfléchissez à votre scénario spécifique.

- Si votre application comporte deux composants, comme une adresse IP et une machine virtuelle avec disque managé, et qu’ils sont garantis dans la zone 1 et la zone 2, quand la zone 1 est en situation d’échec, votre service de bout en bout ne survit pas.  Ne franchissez pas de zones avec des scénarios zonaux, sauf si vous comprenez pleinement que vous créez un mode d’échec potentiellement dangereux.  Ce scénario est autorisé pour assurer la flexibilité.

- Si votre application possède deux composants, tels qu’une adresse IP et une machine virtuelle avec disque managé, et qu’il est garanti qu’ils sont respectivement redondant interzone et dans la zone 1, votre service de bout en bout survit à une défaillance de la zone 2, de la zone 3 ou des deux, sauf en cas d’échec de la zone 1.  Toutefois, vous perdez une certaine capacité à raisonner sur l’intégrité de votre service si tout ce que vous observez, c’est l’accessibilité du frontend.  Envisagez de développer un modèle d’intégrité et de capacité plus étendu.  Vous pouvez utiliser les concepts de redondance interzone et de caractéristique zonale ensemble pour développer un insight et une facilité de gestion.

- Si votre application comporte deux composants, comme un frontend Load Balancer redondant interzone et un groupe de machines virtuelles identiques entre trois zones, vos ressources incluses dans les zones non impactées par l’échec sont disponibles mais la capacité de votre service de bout en bout peut être dégradée pendant l’échec de zone. Du point de vue de l’infrastructure, votre déploiement peut survivre à un ou plusieurs échecs de zone. Ainsi, les questions suivantes se posent :
  - Est-ce que vous comprenez comment votre application raisonne sur ces échecs et cette capacité détériorée ?
  - Avez-vous besoin de mesures de protection pour votre service afin de forcer un basculement vers une paire de régions si nécessaire ?
  - Comment allez-vous surveiller, détecter et atténuer un tel scénario ? Vous pouvez peut-être utiliser les diagnostics de la référence Standard de Load Balancer pour renforcer la surveillance des performances de votre service de bout en bout. Examinez ce qui est disponible et ce qui peut nécessiter une augmentation pour vous faire une idée exhaustive.

- Les zones permettent de comprendre et contenir les échecs plus facilement.  Toutefois, un échec de zone n’est pas différent des autres échecs quand il s’agit de concepts comme les délais d’attente, les nouvelles tentatives et les algorithmes d’interruption. Même si Azure Load Balancer fournit des chemins redondants interzone pour tenter une récupération rapide, au niveau du paquet en temps réel, les retransmissions ou rétablissements peuvent se produire dès le début d’un échec et il est important de comprendre comment votre application gère ces échecs. Votre schéma d’équilibrage de charge survit, mais vous avez besoin de prévoir ce qui suit :
  - Lorsqu’une zone est en échec, votre service de bout en bout le comprend-il et en cas de perte de l’état, comment allez-vous récupérer ?
  - Quand une zone est rétablie, votre application comprend-elle comment converger en toute sécurité ?

Passez en revue les [modèles de conception cloud Azure](https://docs.microsoft.com/azure/architecture/patterns/) pour améliorer la résilience de votre application aux scénarios de défaillance.

## <a name="next-steps"></a>Étapes suivantes
- En savoir plus sur les [zones de disponibilité](../availability-zones/az-overview.md)
- En savoir plus sur la [référence Standard de Load Balancer](load-balancer-standard-overview.md)
- Découvrir comment [équilibrer la charge des machines virtuelles dans une zone à l’aide de Load Balancer standard avec un serveur frontal zonal](load-balancer-standard-public-zonal-cli.md)
- Découvrir comment [équilibrer la charge des machines virtuelles dans des zones à l’aide de Load Balancer standard avec un serveur frontal redondant interzone](load-balancer-standard-public-zone-redundant-cli.md)
- Découvrez plus d'informations sur les [modèles de conception cloud Azure](https://docs.microsoft.com/azure/architecture/patterns/) pour améliorer la résilience de votre application aux scénarios de défaillance.
