---
title: Azure Traffic Manager | Microsoft Docs
description: Cet article fournit une vue d’ensemble d’Azure Traffic Manager. Déterminez si Azure Traffic Manager est adapté à votre application pour équilibrer la charge de trafic utilisateur.
services: traffic-manager
documentationcenter: ''
author: kumudd
manager: jeconnoc
editor: ''
ms.service: traffic-manager
customer intent: As an IT admin, I want to learn about Traffic Manager and what I can use it for.
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/25/2018
ms.author: kumud
ms.openlocfilehash: 236137b87351e3c3a95c1103f7464256f41b9159
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/01/2018
ms.locfileid: "39397214"
---
# <a name="what-is-traffic-manager"></a>Qu’est-ce que Traffic Manager ?
Azure Traffic Manager est un équilibreur de charge du trafic DNS qui vous permet de distribuer le trafic de manière optimale aux services dans toutes les régions Azure globales, tout en offrant réactivité et haute disponibilité.

Traffic Manager utilise le système DNS pour diriger les requêtes des clients vers le point de terminaison de service le plus approprié, en fonction de la méthode de routage du trafic et de l’intégrité des points de terminaison. Un point de terminaison est tout service côté Internet hébergé à l’intérieur ou à l’extérieur d’Azure. Traffic Manager fournit un large éventail de [méthodes de routage du trafic](traffic-manager-routing-methods.md) et [d’option de surveillance des points de terminaison](traffic-manager-monitoring.md) pour répondre aux besoins variés des applications et aux divers modèles de basculement automatique. Traffic Manager est résilient aux défaillances, notamment à l’échec d’une région Azure entière.

>[!NOTE]
> Azure offre une suite de solutions d’équilibrage de charge entièrement managées pour vos scénarios. Si vous recherchez une terminaison de protocole TLS (« déchargement SSL ») ou un traitement de couche d’application par requête HTTP/HTTPS, consultez [Application Gateway](../application-gateway/application-gateway-introduction.md). Si vous cherchez équilibrage régional, consultez [Load Balancer](../load-balancer/load-balancer-overview.md). Vos scénarios de bout en bout peuvent tirer parti de la combinaison de ces solutions en fonction de vos besoins.

Les fonctionnalités suivantes sont incluses dans Traffic Manager :

## <a name="increase-application-availability"></a>Amélioration de la disponibilité des applications

Traffic Manager vous permet de garantir une haute disponibilité de vos applications critiques en surveillant vos points de terminaison et en fournissant un basculement automatique en cas de panne d’un point de terminaison.
    
## <a name="improve-application-performance"></a>Amélioration des performances des applications

Azure vous permet d'exécuter des sites web ou des services cloud dans des centres de données situés dans le monde entier. Traffic Manager améliore la réactivité de vos applications en dirigeant le trafic vers le point de terminaison affichant la latence réseau la plus faible pour le client.

## <a name="perform-service-maintenance-without-downtime"></a>Gestion des services sans les interrompre

Vous pouvez effectuer les opérations de maintenance planifiée sur vos applications sans temps d’arrêt. Traffic Manager dirige le trafic vers les autres points de terminaison pendant la maintenance.

## <a name="combine-hybrid-applications"></a>Combinaison d'applications hybrides

Traffic Manager prend en charge des points de terminaison externes non Azure, ce qui permet de l’utiliser dans des déploiements de cloud hybride et locaux, notamment des scénarios tels que le « [burst-to-cloud](https://azure.microsoft.com/overview/what-is-cloud-bursting/) » la « migration vers le cloud » et le « basculement vers le cloud ».

## <a name="distribute-traffic-for-complex-deployments"></a>Distribution du trafic pour des déploiements vastes et complexes

En utilisant les [profils Traffic Manager imbriqués](traffic-manager-nested-profiles.md), il est possible de combiner des méthodes de routage du trafic pour créer des règles flexibles et sophistiquées afin de répondre aux besoins des déploiements plus vastes et plus complexes.

## <a name="pricing"></a>Tarifs

Pour des informations sur les prix, consultez [Tarification Traffic Manager](https://azure.microsoft.com/pricing/details/traffic-manager/).


## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur la [création d’un profil Traffic Manager](traffic-manager-create-profile.md).
- En savoir plus sur le [fonctionnement de Traffic Manager](traffic-manager-how-it-works.md).
- Consulter le [Forum Aux Questions](traffic-manager-FAQs.md) (FAQ) relatif à Traffic Manager.




