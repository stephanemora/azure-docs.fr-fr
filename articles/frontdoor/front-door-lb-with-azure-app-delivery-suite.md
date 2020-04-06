---
title: Azure Front Door - Équilibrage de charge avec la suite de livraison d’application Azure | Microsoft Docs
description: Cet article explique comment Azure recommande l’équilibrage de charge avec sa suite de livraison d’application.
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: 9f8d1959549eaddfb4a2c9ea271094db0073c788
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471708"
---
# <a name="load-balancing-with-azures-application-delivery-suite"></a>Équilibrage de charge avec la suite de livraison d’application Azure

## <a name="introduction"></a>Introduction
Microsoft Azure propose plusieurs services globaux et régionaux destinés à gérer la distribution et l’équilibrage de la charge de votre trafic réseau : Traffic Manager, Front Door, Application Gateway et Load Balancer.  Associée aux nombreuses régions Azure et à son architecture zonale, l’utilisation conjointe de ces services vous permet de créer des applications robustes, scalables et à hautes performances.

![Suite de livraison d’application ][1]
 
Ces services sont répartis en deux catégories :
1. Les **services d’équilibrage de charge globaux** tels que Traffic Manager et Front Door distribuent le trafic de vos utilisateurs finaux sur vos back-ends régionaux, entre les clouds ou même vos services locaux hybrides. L’équilibrage de charge global achemine le trafic vers votre backend de service le plus proche et réagit aux changements de fiabilité ou de performances des services afin de maintenir des performances continues et maximales pour vos utilisateurs. 
2. Les **services d’équilibrage de charge régionaux** tels que Standard Load Balancer ou Application Gateway permettent de distribuer le trafic des réseaux virtuels sur vos machines virtuelles ou points de terminaison de service zonaux dans une région.

La combinaison de services globaux et régionaux dans votre application offre un moyen performant, fiable et sécurisé de bout en bout de router le trafic entre vos utilisateurs et vos services IaaS, PaaS ou locaux. Vous trouverez une description de chacun de ces services dans la section suivante.

## <a name="global-load-balancing"></a>Équilibrage de charge global
**Traffic Manager** assure un équilibrage de charge DNS global. Il examine les requêtes DNS entrantes et répond avec un backend intègre, conformément à la stratégie de routage choisie par le client. Les différentes méthodes de routage disponibles sont les suivantes :
- Le routage basé sur les performances permet de rediriger le demandeur vers le backend le plus proche en termes de latence.
- Le routage par priorité permet de rediriger l’ensemble du trafic vers un back-end, les autres back-ends jouant le rôle de back-ends de secours.
- Le routage du trafic en tourniquet (round robin) pondéré permet de répartir le trafic en fonction de la pondération associée à chaque backend.
- Le routage géographique pour s’assurer que les demandeurs situés dans des zones géographiques spécifiques sont dirigés vers les backends mappés à ces régions (par exemple, toutes les requêtes d’Espagne doivent être dirigées vers la région Azure France Centre)
- Le routage de sous-réseau, qui vous permet de mapper des plages d’adresses IP à des backends afin que les requêtes entrantes en provenance de ces adresses IP soient envoyées au backend spécifié (par exemple, tous les utilisateurs qui se connectent à partir de la plage d’adresses IP du siège de votre société doivent obtenir un contenu web différent des utilisateurs ordinaires).

Le client se connecte directement à ce backend. Azure Traffic Manager détecte quand un backend est défectueux, et il redirige les clients vers une autre instance intègre. Pour plus d’informations sur ce service, consultez la [documentation Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md).

**Azure Front Door** fournit une accélération de site web dynamique, notamment l’équilibrage de charge HTTP global.  Il examine les requêtes HTTP entrantes et les achemine vers le backend ou la région la plus proche pour le nom d’hôte, le chemin d’URL et les règles configurées spécifiées.  
Front Door met fin aux requêtes HTTP à la périphérie du réseau Microsoft, et détecte de manière active les changements d’intégrité ou de latence d’application ou d’infrastructure.  Front Door achemine ensuite toujours le trafic vers le backend (sain) le plus rapide et disponible. Pour en savoir plus sur ce service, consultez les détails sur l’[architecture de routage](front-door-routing-architecture.md) et sur les [méthodes de routage du trafic](front-door-routing-methods.md) de Front Door.

## <a name="regional-load-balancing"></a>Équilibrage de charge régional
Application Gateway propose Application Delivery Controller (ADC) en tant que service, mettant ainsi plusieurs fonctionnalités d’équilibrage de charge de couche 7 à la disposition de votre application. Il permet aux clients d’optimiser la productivité de la batterie de serveurs web en déchargeant une terminaison SSL gourmande en ressources du processeur vers la passerelle Application Gateway. Parmi les autres fonctionnalités de routage de couche 7, citons la distribution en tourniquet (round robin) du trafic entrant, l’affinité de session basée sur les cookies, le routage basé sur le chemin des URL et la possibilité d’héberger plusieurs sites web derrière une seule passerelle Application Gateway. Cette dernière peut être configurée en tant que passerelle accessible sur Internet, passerelle interne uniquement ou une combinaison des deux. La passerelle Application Gateway est une solution Azure entièrement gérée, très évolutive et hautement disponible. Elle fournit un ensemble complet de fonctionnalités de diagnostics et de journalisation, pour une meilleure gérabilité.
Load Balancer fait partie intégrante de la pile Azure SDN, et fournit des services d’équilibrage de charge de couche 4 hautement performants et à faible latence pour tous les protocoles UDP et TCP. Il gère les connexions entrantes et sortantes. Vous pouvez configurer des points de terminaison publics et internes avec équilibrage de charge, et définir des règles de mappage des connexions entrantes aux destinations du pool principal, en utilisant des options d’analyse d’intégrité TCP et HTTP de façon à gérer la disponibilité du service.


## <a name="choosing-a-global-load-balancer"></a>Choix d’un équilibreur de charge global
Quand vous devez choisir qui de Traffic Manager ou d’Azure Front Door utiliser comme équilibreur de charge global pour le routage global, vous devez examiner ce qui est similaire et ce qui est différent dans les deux services.   Ces deux services fournissent ce qui suit :
- **Géoredondance multiple :** si une région connaît une défaillance, le trafic est acheminé vers la région la plus proche sans interruption et sans aucune intervention du propriétaire de l’application.
- **Routage vers la région plus proche :** le trafic est acheminé automatiquement vers la région la plus proche.

</br>Le tableau suivant décrit les différences entre Traffic Manager et Azure Front Door :</br>

| Traffic Manager | Azure Front Door |
| --------------- | ------------------------ |
|**N'importe quel protocole :** Traffic Manager fonctionnant au niveau de la couche DNS, vous pouvez acheminer tous les types de trafic réseau : HTTP, TCP, UDP, etc. | **Accélération HTTP :** avec Front Door, le trafic est proxysé à la périphérie du réseau Microsoft.  Pour cette raison, la latence et le débit des requêtes HTTP(S) sont améliorées, ce qui réduit la latence pour la négociation SSL et l’utilisation de connexions à chaud d’AFD vers votre application.|
|**Routage local :** avec le routage au niveau d’une couche DNS, le trafic va toujours de point à point.  Le routage de votre filiale vers votre centre de données local peut prendre un chemin direct, même sur votre propre réseau à l’aide de Traffic Manager. | **Extensibilité indépendante :** comme Front Door fonctionne avec la requête HTTP, les requêtes pour différents chemins d’URL peuvent être routées vers différents pools de backends / services régionaux (microservices) en fonction de règles et de l’intégrité de chaque microservice d’application.|
|**Format de facturation :** la facturation basée sur DNS est mise à l’échelle en fonction du nombre d’utilisateurs et, pour les services à davantage d’utilisateurs, se stabilise afin de réduire le coût. |**Sécurité incluse :** Front Door active des règles telles que la limitation du débit et les listes ACL d’adresses IP pour vous permettre de protéger vos backends avant que le trafic n’atteigne votre application. 

</br>En raison des avantages offerts par Front Door en termes de performances, d’opérabilité et de sécurité pour les charges de travail HTTP, nous recommandons aux clients d’utiliser Front Door pour leurs charges de travail HTTP.    Traffic Manager et Front Door peuvent être utilisés en parallèle afin de servir tout le trafic pour votre application. 

## <a name="building-with-azures-application-delivery-suite"></a>Création avec la suite de livraison d’application Azure 
Nous vous recommandons de faire en sorte que tous les services, sites web et API soient géographiquement redondants et distribuent le trafic aux utilisateurs à partir de l’emplacement le plus proche (latence la plus faible) dans la mesure du possible.  La combinaison des services offerts par Traffic Manager, Front Door, Application Gateway et Load Balancer vous permet de créer des services redondants dans une zone et du point de vue géographique, afin d’optimiser les performances, la scalabilité et la fiabilité.

Dans le diagramme suivant, nous décrivons un exemple de service qui utilise une combinaison de tous ces services pour créer un service web global.   Dans ce cas, l’architecte a utilisé Traffic Manager afin d’acheminer le trafic vers des backends globaux pour la livraison de fichiers et d’objets, et il a utilisé Front Door pour acheminer globalement les chemins d’URL qui correspondent au modèle /store/* vers le service qu’il a migré vers App Service tout en acheminant toutes les autres requêtes vers des Application Gateways régionales.

Dans la région, pour son service IaaS, le développeur de l’application a décidé que les URL correspondant au modèle /images /* seraient desservies par un pool dédié de machines virtuelles différentes du reste de la batterie de serveurs web.

Par ailleurs, le pool de machines virtuelles par défaut affichant le contenu dynamique doit communiquer avec une base de données principale hébergée sur un cluster à haute disponibilité. Le déploiement est entièrement approvisionné via Azure Resource Manager.

Le diagramme suivant illustre l’architecture de ce scénario :

![Architecture détaillée de la suite de livraison d’application][2] 

> [!NOTE]
> Cet exemple est l’une des innombrables configurations possibles offertes par les services d’équilibrage de charge Azure. Les services Traffic Manager, Front Door, Application Gateway et Load Balancer peuvent être combinés et associés pour répondre au mieux à vos besoins d’équilibrage de charge. Par exemple, s’il n’est pas nécessaire de faire appel au déchargement SSL ou au traitement de la couche 7, Load Balancer peut être utilisé à la place d’Application Gateway.


## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [créer une porte d’entrée](quickstart-create-front-door.md).
- Découvrez [comment fonctionne Front Door](front-door-routing-architecture.md).

<!--Image references-->
[1]: ./media/front-door-lb-with-azure-app-delivery-suite/application-delivery-figure1.png
[2]: ./media/front-door-lb-with-azure-app-delivery-suite/application-delivery-figure2.png
