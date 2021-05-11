---
title: Utilisation d’ExpressRoute pour les services Microsoft 365 | Microsoft Docs
description: Ce document parle objectivement de l’utilisation d’un circuit ExpressRoute pour les services SaaS Microsoft 365.
documentationcenter: na
services: expressroute
author: rambk
manager: tracsman
ms.service: expressroute
ms.topic: article
ms.workload: infrastructure-services
ms.date: 4/29/2021
ms.author: rambala
ms.openlocfilehash: f7ccad395dd473e2520bbcaefac8e34be6fb2443
ms.sourcegitcommit: 52491b361b1cd51c4785c91e6f4acb2f3c76f0d5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2021
ms.locfileid: "108322272"
---
# <a name="using-expressroute-for-routing-microsoft-365-traffic"></a>Utilisation d’ExpressRoute pour le routage du trafic Microsoft 365

Un circuit ExpressRoute fournit une connectivité privée avec le réseau principal de Microsoft. 
* Il offre un *peering privé* pour se connecter aux points de terminaison privés de votre déploiement IaaS dans les régions Azure. 
* De même, il offre un *peering Microsoft* pour se connecter aux points de terminaison publics des services IaaS, PaaS et SaaS du réseau Microsoft. 

Pour plus d’informations sur ExpressRoute, consultez l’article [Présentation d’ExpressRoute][ExR-Intro].


La possibilité d’utiliser ou non ExpressRoute pour le routage du trafic SaaS Microsoft 365 fait souvent débat. 

* Le principal argument est qu’en offrant le peering Microsoft, ExpressRoute vous permet d’accéder à la plupart des points de terminaison publics du réseau Microsoft. De fait, en utilisant un *filtre de routes*, vous pouvez sélectionner les préfixes des services Microsoft 365 qui doivent être publiés sur votre réseau local via le peering Microsoft. La publication de ces routes permet le routage du trafic des services Microsoft 365 via le circuit ExpressRoute. 
* Le contre-argument est que Microsoft 365 est un service distribué. Il a été conçu pour permettre aux clients du monde entier de se connecter au service à l’aide d’Internet. Il est donc recommandé de ne pas utiliser ExpressRoute pour Microsoft 365.

Cet article vise à : 
* apporter un raisonnement technique pour étayer les arguments et 
* à indiquer de manière objective dans quels cas utiliser ExpressRoute pour le routage du trafic Microsoft 365 et dans quels cas ne pas l’utiliser.

## <a name="network-requirements-of-microsoft-365-traffic"></a>Configuration réseau requise du trafic Microsoft 365
Le service Microsoft 365 comporte souvent du trafic en temps réel, par exemple des appels vocaux et vidéo, des réunions en ligne et la collaboration en temps réel. Ce trafic en temps réel impose des conditions de performances réseau strictes en termes de latence et d’instabilité. Dans certaines limites de latence réseau, l’instabilité peut être gérée efficacement en utilisant une mémoire tampon au niveau de l’appareil client. La latence réseau est une fonction de la distance physique que doit parcourir le trafic, de la bande passante de liaison et de la latence du traitement réseau. 

## <a name="network-optimization-features-of-microsoft-365"></a>Fonctionnalités d’optimisation réseau de Microsoft 365 

Microsoft s’efforce d’optimiser les performances réseau de toutes les applications cloud, aussi bien sur le plan de l’architecture que sur celui des fonctionnalités. Pour commencer, Microsoft est propriétaire de l’un des plus grands réseaux mondiaux, qui est optimisé pour remplir l’objectif principal, à savoir offrir les meilleures performances réseau. S’agissant d’un réseau à définition logicielle, le réseau Microsoft est de type « cold potato ». Cela signifie qu’il attire le trafic et le fait sortir le plus près possible de l’appareil client/réseau client. De plus, le réseau Microsoft offre une redondance et une disponibilité de haut niveau. Pour plus d’informations sur l’optimisation de l’architecture, consultez [How Microsoft builds its fast and reliable global network][MGN].

Pour répondre aux exigences de latence réseau strictes, Microsoft 365 raccourcit la longueur des routes par les procédés suivants :
* routage dynamique de la connexion de l’utilisateur final vers le point d’entrée Microsoft 365 le plus proche, puis 
* à partir du point d’entrée, routage efficace au sein du réseau mondial Microsoft vers le centre de données Microsoft 365 le plus proche (et autorisé).

Les points d’entrée Microsoft 365 sont gérés par Azure Front Door (AFD). AFD est un service largement distribué présent sur le réseau de périmètre mondial Microsoft qui permet de créer des applications SaaS rapides, sécurisées et hautement scalables. Pour mieux comprendre en quoi AFD améliore les performances des applications web, consultez [Qu’est-ce qu’Azure Front Door][AFD]. Au moment de choisir le centre de données Microsoft 365 le plus proche, Microsoft tient compte de la réglementation relative à la souveraineté des données de la région géopolitique.

## <a name="what-is-geo-pinning-connections"></a>En quoi consistent les connexions géo-épinglées ?

Entre un client-serveur, quand vous forcez le trafic à transiter par un ou plusieurs appareils réseau situés dans un lieu géographique, vous géo-épinglez les connexions réseau. Une architecture réseau traditionnelle, qui repose sur le principe de conception sous-jacent selon lequel l’emplacement des clients-serveurs est statique, a généralement recours au géo-épinglage des connexions.
Par exemple, quand vous forcez les connexions Internet de votre entreprise à traverser votre réseau d’entreprise et à sortir par un emplacement central (généralement en transitant par un ensemble de serveurs proxy ou de pare-feu), vous géo-épinglez les connexions Internet.  

De la même manière, dans une architecture d’application SaaS, si vous forcez le trafic à transiter par un centre de données intermédiaire (par exemple, la sécurité du cloud) d’une région ou par un ou plusieurs appareils réseau intermédiaires (par exemple, ExpressRoute) à un emplacement spécifique, vous géo-épinglez les connexions SaaS.

## <a name="when-not-to-use-expressroute-for-microsoft-365"></a>Dans quels cas ne pas utiliser ExpressRoute pour Microsoft 365 ?

En raison de sa capacité à raccourcir de façon dynamique la longueur des routes et à choisir de façon dynamique le centre de données serveur le plus proche de l’emplacement des clients, Microsoft 365 est considéré comme ayant été conçu pour Internet. De plus, un certain trafic Microsoft 365 est routé uniquement via Internet.
Quand vos clients SaaS sont largement distribués dans une région ou dans le monde entier et que vous géo-épinglez les connexions à un emplacement déterminé, vous imposez aux clients plus éloignés de l’emplacement géo-épinglé une latence réseau plus élevée. Une plus grande latence réseau se traduit par des performances réseau non optimales et par des performances d’application médiocres.

Par conséquent, dans les scénarios où vous avez des clients SaaS largement distribués ou des clients hautement mobiles, vous n’avez aucun intérêt à géo-épingler les connexions, notamment en forçant le trafic à transiter par un circuit ExpressRoute à un emplacement de peering déterminé.


## <a name="when-to-use-expressroute-for-microsoft-365"></a>Dans quels cas utiliser ExpressRoute pour Microsoft 365 ?

Voici quelques-unes des raisons qui peuvent justifier l’utilisation d’ExpressRoute pour le routage du trafic Microsoft 365 :
* Vos clients SaaS sont concentrés dans une géolocalisation et le meilleur moyen de se connecter au réseau mondial Microsoft est de passer par des circuits ExpressRoute.
* Vos clients SaaS sont concentrés dans plusieurs endroits dans le monde et chaque endroit dispose de son propre circuit ExpressRoute qui lui confère une connectivité optimale au réseau mondial Microsoft.
* Une législation vous impose de router le trafic cloud via des connexions privées.
* Vous êtes tenu de router l’ensemble du trafic SaaS vers un emplacement centralisé géo-épinglé (qu’il s’agisse d’un centre de données privé ou public), et le meilleur moyen de connecter l’emplacement centralisé au réseau mondial Microsoft est de passer par ExpressRoute.
* Pour certains de vos clients SaaS statiques, seul ExpressRoute offre une connectivité optimale ; pour les autres clients, vous utilisez Internet.

Lorsque vous utilisez ExpressRoute, vous pouvez appliquer le filtre de routes associé au peering Microsoft d’ExpressRoute pour router uniquement une partie des services Microsoft 365 et/ou des services PaaS Azure via le circuit ExpressRoute. Pour plus d’informations, consultez [Tutoriel : Configurer des filtres de routage pour le peering Microsoft][ExRRF].

## <a name="next-steps"></a>Étapes suivantes

* Pour comprendre le flux d’appels Microsoft Teams et la façon d’optimiser la connectivité réseau dans différents scénarios, notamment lorsque vous utilisez ExpressRoute pour obtenir de meilleurs résultats, consultez [Flux d’appels Microsoft Teams][Teams].
* Si vous souhaitez effectuer des tests pour comprendre les problèmes de connectivité Microsoft 365 pour différents emplacements de bureau, consultez [Test de connectivité réseau Microsoft 365][Microsoft 365-Test].
* Pour établir une base de référence et un historique de performances afin de mieux détecter les problèmes de performances émergents de Microsoft 365, consultez [Optimisation des performances d’Office 365 à l’aide de bases de référence et d’un historique de performances][Microsoft 365perf].

<!--Link References-->
[ExR-Intro]: https://docs.microsoft.com/azure/expressroute/expressroute-introduction 
[CreatePeering]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-routing-portal-resource-manager
[MGN]: https://azure.microsoft.com/blog/how-microsoft-builds-its-fast-and-reliable-global-network/
[AFD]: https://docs.microsoft.com/azure/frontdoor/front-door-overview
[ExRRF]: https://docs.microsoft.com/azure/expressroute/how-to-routefilter-portal
[Teams]: https://docs.microsoft.com/microsoftteams/microsoft-teams-online-call-flows
[Microsoft 365-Test]: https://connectivity.office.com/
[Microsoft 365perf]: https://docs.microsoft.com/microsoft-365/enterprise/performance-tuning-using-baselines-and-history?view=o365-worldwide


