---
title: Azure Load Balancer et zones de disponibilité
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
ms.date: 05/07/2020
ms.author: allensu
ms.openlocfilehash: 06b8e193f48a4e1d3956c8c40ee03dfd11a4a088
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129276016"
---
# <a name="load-balancer-and-availability-zones"></a>Load Balancer et zones de disponibilité

Azure Load Balancer prend en charge les scénarios des zones de disponibilité. Vous pouvez utiliser l’équilibreur de charge standard pour améliorer la disponibilité dans votre scénario en alignant les ressources et en les distribuant sur les zones.  Consultez ce document pour comprendre ces concepts et obtenir des conseils de conception de scénarios

Un équilibreur de charge peut être de type **redondant interzone, zonal** ou **non zonal**. Pour configurer les propriétés liées aux zones (mentionnées ci-dessus) pour votre équilibreur de charge, sélectionnez le type approprié de serveur frontal requis.

## <a name="zone-redundant"></a>Redondant interzone

Dans une région avec des zones de disponibilité, un équilibreur de charge standard peut être redondant interzone. Ce trafic est pris en charge par une adresse IP unique.

Une adresse IP de serveur frontend unique va survivre à une défaillance de zone. L’adresse IP frontend peut être utilisée pour atteindre tous les membres du pool principal (non affectés), quelle que soit la zone. Une ou plusieurs zones de disponibilité peuvent échouer sans empêcher le chemin de données de survivre dans la mesure où une seule zone de la région reste saine.

L’adresse IP frontend est servie simultanément par plusieurs déploiements d’infrastructure indépendants dans plusieurs zones de disponibilité. Toute nouvelle tentative ou tout rétablissement réussissent dans les autres zones non affectée par les défaillances de zone.

<p align="center">
  <img src="./media/az-zonal/zone-redundant-lb-1.svg" alt="Figure depicts a zone-redundant standard load balancer directing traffic in three different zones to three different subnets in a zone redundant configuration." width="512" title="Service NAT de Réseau virtuel">
</p>

*Figure : Équilibreur de charge redondant dans une zone*

## <a name="zonal"></a>Zonal

Vous pouvez choisir de garantir un frontend dans une seule zone, ce qui donne un *zonal*.  Dans ce scénario, tout flux entrant ou sortant est servi par une seule zone dans une région.  Votre frontend connaît le même sort que l’intégrité de la zone.  Le chemin de données n’est pas affecté par les défaillances des zones autres que celles dans lesquelles il a été garanti. Vous pouvez utiliser des frontends zonaux pour exposer une adresse IP par zone de disponibilité.  

En outre, vous pouvez utiliser des frontends zonaux directement pour les points de terminaison à charge équilibrée dans chaque zone. Vous pouvez également utiliser cette configuration pour exposer des points de terminaison à charge équilibrée par zone afin de surveiller individuellement chaque zone. Pour les points de terminaison publics, vous pouvez les intégrer à un produit d’équilibrage de charge DNS comme [Traffic Manager](../traffic-manager/traffic-manager-overview.md) et utiliser un nom DNS unique.


<p align="center">
  <img src="./media/az-zonal/zonal-lb-1.svg" alt="Figure depicts three zonal standard load balancers each directing traffic in a zone to three different subnets in a zonal configuration." width="512" title="Service NAT de Réseau virtuel">
</p>

*Figure : Équilibreur de charge zonal*

Pour un frontend d’équilibreur de charge public, vous ajoutez un paramètre **zones** à l’adresse IP publique. Cette adresse IP publique est référencée par la configuration d’adresse IP frontend utilisée par la règle correspondante.

Pour un équilibreur de charge frontend interne, ajoutez un paramètre **zones** à la configuration IP de l’équilibreur de charge frontend interne. Un frontend zonal garantit une adresse IP dans un sous-réseau d’une zone spécifique.

## <a name="non-zonal"></a>Non zonal

Les équilibreurs de charge peuvent également être créés dans une configuration non zonale à l’aide d’un front-end « sans zone » (adresse IP publique ou préfixe d’adresse IP publique).  Cette option n’offre pas de garantie de redondance. Notez que toutes les adresses IP publiques [mises à niveau](../virtual-network/public-ip-upgrade-portal.md) seront de type « sans zone ».

## <a name="design-considerations"></a><a name="design"></a> Remarques relatives à la conception

Maintenant que vous comprenez les propriétés liées aux zones pour Standard Load Balancer, les considérations de conception suivantes peuvent vous aider à atteindre une haute disponibilité.

### <a name="tolerance-to-zone-failure"></a>Tolérance aux défaillances de zone

- Un équilibreur de charge **redondant interzone** peut servir une ressource zonale dans n’importe quelle zone avec une adresse IP.  L’adresse IP peut survivre à un ou plusieurs défaillances de zone tant qu’au moins une zone reste fonctionnelle dans la région.
- Un frontend **zonal** est une réduction du service à une seule zone et partage son sort avec la zone concernée. Si la zone dans laquelle se trouve votre déploiement est défaillante, votre déploiement ne survivra pas à cet échec.

Nous vous recommandons d’utiliser un équilibreur de charge redondant interzone pour vos charges de travail de production.

### <a name="multiple-frontends"></a>Plusieurs serveurs frontaux

L’utilisation de plusieurs front-ends vous permet d’équilibrer la charge du trafic sur plusieurs ports et/ou adresses IP.  Lors de la conception de votre architecture, il est important de tenir compte de la façon dont la redondance de zone et plusieurs front-ends peuvent interagir.  Notez que si l’objectif est que chaque front-end soit toujours résistant aux défaillances, toutes les adresses IP utilisées comme front-ends doivent être redondantes interzone.   Si un ensemble de front-ends est destiné à être associé à une seule zone, chaque adresse IP de cet ensemble doit être associée à cette zone spécifique.  Il n’est pas obligatoire d’avoir un équilibreur de charge pour chaque zone ; au lieu de cela, chaque front-end zonal (ou ensemble de front-ends zonaux) peut être associé à des machines virtuelles du pool de back-ends qui font partie de cette zone de disponibilité spécifique.

### <a name="transition-between-regional-zonal-models"></a>Transition entre les modèles zonaux régionaux

Dans le cas où une région est augmentée pour avoir des [zones de disponibilité](../availability-zones/az-overview.md), elle reste non zonale. Pour vous assurer que votre architecture peut tirer parti des nouvelles zones, nous vous recommandons de créer de nouvelles adresses IP frontales, et de répliquer les règles et configurations appropriées de façon à utiliser ces nouvelles adresses IP publiques.

### <a name="control-vs-data-plane-implications"></a>Implications au niveau du plan de contrôle et du plan de données

La redondance dans une zone n’implique pas de plan de données ni de plan de contrôle sans réponse. Les flux redondants interzone peuvent utiliser toutes les zones et vos flux utiliser toutes les zones saines dans une région. Dans le cas d’une défaillance de zone, les flux de trafic utilisant les zones saines ne sont pas affectés.

Les flux de trafic qui font appel à une zone au moment de la défaillance de cette dernière peuvent être concernés, mais les applications peuvent récupérer. Le trafic se poursuit dans les zones saines de la région après retransmission, une fois qu’Azure a convergé autour de la défaillance de zone.

Passez en revue les [modèles de conception cloud Azure](/azure/architecture/patterns/) pour améliorer la résilience de votre application aux scénarios de défaillance.

## <a name="limitations"></a>Limites

* Après la création, les zones de la ressource ne peuvent pas être modifiées, mises à jour ou créées.
* Les ressources ne peuvent pas être converties de zonales à redondantes interzones, ou inversement, après la création.

## <a name="next-steps"></a>Étapes suivantes
- En savoir plus sur les [zones de disponibilité](../availability-zones/az-overview.md)
- En savoir plus sur la [référence Standard de Load Balancer](./load-balancer-overview.md)
- Découvrir comment [équilibrer la charge des machines virtuelles dans une zone à l’aide d’un équilibreur de charge standard zonal](./quickstart-load-balancer-standard-public-cli.md)
- Découvrir comment [équilibrer la charge des machines virtuelles dans des zones à l’aide d’un équilibreur de charge standard redondant interzone](./quickstart-load-balancer-standard-public-cli.md)
- Découvrez plus d'informations sur les [modèles de conception cloud Azure](/azure/architecture/patterns/) pour améliorer la résilience de votre application aux scénarios de défaillance.
