---
title: Azure Traffic Manager | Microsoft Docs
description: Cet article fournit une vue d’ensemble d’Azure Traffic Manager. Déterminez s’il représente un choix adapté à l’équilibrage de charge du trafic utilisateur pour votre application.
services: traffic-manager
author: duongau
ms.service: traffic-manager
customer intent: As an IT admin, I want to learn about Traffic Manager and what I can use it for.
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/19/2021
ms.author: duau
ms.openlocfilehash: 8c8897218b153c8584c89abab98934268ccd555d
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102182160"
---
# <a name="what-is-traffic-manager"></a>Qu’est-ce que Traffic Manager ?
Azure Traffic Manager est un équilibreur de charge de trafic DNS. Ce service vous permet de distribuer le trafic à vos applications publiques dans les régions Azure globales. Traffic Manager fournit également à vos points de terminaison publics une haute disponibilité et une réactivité rapide.

Traffic Manager utilise le système DNS pour diriger les requêtes des clients vers le point de terminaison de service approprié, en fonction de la méthode de routage du trafic. Traffic Manager fournit également une supervision de l’intégrité pour chaque point de terminaison. Le point de terminaison peut être tout service côté Internet hébergé dans ou en dehors d’Azure. Traffic Manager fournit un large éventail de [méthodes de routage du trafic](traffic-manager-routing-methods.md) et [d’option de surveillance des points de terminaison](traffic-manager-monitoring.md) pour répondre aux besoins variés des applications et aux divers modèles de basculement automatique. Traffic Manager est résilient aux défaillances, notamment à l’échec d’une région Azure entière.

>[!NOTE]
> Azure offre une suite de solutions d’équilibrage de charge entièrement managées pour vos scénarios. 
> * Si vous souhaitez équilibrer la charge entre vos serveurs dans une région au niveau de la couche Application, consultez [Application Gateway](../application-gateway/overview.md).
> * Si vous avez besoin d’optimiser le routage global de votre trafic web et d’optimiser les performances et la fiabilité des utilisateurs finaux grâce à un basculement global rapide, consultez [Front Door](../frontdoor/front-door-overview.md).
> * Pour effectuer l’équilibrage de charge de la couche Réseau, consultez [Load Balancer](../load-balancer/load-balancer-overview.md). 
> 
> Vos scénarios de bout en bout peuvent tirer parti de la combinaison de ces solutions en fonction de vos besoins.
> Pour obtenir une comparaison des options d’équilibrage de charge Azure, consultez [Vue d’ensemble des options d’équilibrage de charge dans Azure](/azure/architecture/guide/technology-choices/load-balancing-overview).

Traffic Manager offre les fonctionnalités suivantes :

## <a name="increase-application-availability"></a>Amélioration de la disponibilité des applications

Traffic Manager vous permet de garantir une haute disponibilité de vos applications critiques en surveillant vos points de terminaison et en fournissant un basculement automatique en cas de panne d’un point de terminaison.
    
## <a name="improve-application-performance"></a>Amélioration des performances des applications

Azure vous permet d’exécuter des sites web et des services cloud dans des centres de données situés dans le monde entier. Traffic Manager peut améliorer la réactivité de votre site web en dirigeant le trafic vers le point de terminaison ayant la latence la plus faible.

## <a name="service-maintenance-without-downtime"></a>Maintenance des services sans temps d’arrêt

Vous pouvez effectuer une maintenance planifiée sur vos applications sans temps d’arrêt. Traffic Manager peut diriger le trafic vers d’autres points de terminaison pendant la maintenance.

## <a name="combine-hybrid-applications"></a>Combinaison d'applications hybrides

Traffic Manager prend en charge des points de terminaison externes non Azure, ce qui permet de l’utiliser dans des déploiements de cloud hybride et locaux, notamment des scénarios tels que le « [burst-to-cloud](https://azure.microsoft.com/overview/what-is-cloud-bursting/) » la « migration vers le cloud » et le « basculement vers le cloud ».

## <a name="distribute-traffic-for-complex-deployments"></a>Distribution du trafic pour des déploiements vastes et complexes

Grâce aux [profils Traffic Manager imbriqués](traffic-manager-nested-profiles.md), il est possible de combiner plusieurs méthodes de routage du trafic pour créer des règles flexibles et sophistiquées afin de répondre aux besoins des déploiements plus vastes et plus complexes.

## <a name="pricing"></a>Tarifs

Pour des informations sur les prix, consultez [Tarification Traffic Manager](https://azure.microsoft.com/pricing/details/traffic-manager/).


## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur la [création d’un profil Traffic Manager](./quickstart-create-traffic-manager-profile.md).
- En savoir plus sur le [fonctionnement de Traffic Manager](traffic-manager-how-it-works.md).
- Consulter le [Forum Aux Questions](traffic-manager-FAQs.md) (FAQ) relatif à Traffic Manager.