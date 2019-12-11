---
title: Fonctionnalités réseau
description: Découvrez les fonctionnalités réseau d’Azure App Service ainsi que les fonctionnalités nécessaires à la sécurité et au fonctionnement de votre réseau.
author: ccompy
ms.assetid: 5c61eed1-1ad1-4191-9f71-906d610ee5b7
ms.topic: article
ms.date: 05/28/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 6395d62947cda47c3779f15445db08b7515d055d
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/02/2019
ms.locfileid: "74672332"
---
# <a name="app-service-networking-features"></a>Fonctionnalités de mise en réseau App Service

Dans Azure App Service, les applications peuvent être déployées de nombreuses façons. Par défaut, les applications hébergées App Service sont directement accessibles via Internet et ne peuvent atteindre que les points de terminaison Internet hébergés. Toutefois, la plupart des applications clientes ont besoin de contrôler le trafic réseau entrant et sortant. Il existe plusieurs fonctionnalités disponibles dans App Service pour répondre à ces besoins. Le défi est de savoir quelle fonctionnalité doit être utilisée pour résoudre un problème donné. Ce document vise à aider les clients à déterminer la fonction à utiliser grâce à différents cas d’usage.

Il existe deux types de déploiement principaux pour Azure App Service. Il y a le service public multilocataire, qui héberge les plans App Service dans les références SKU de tarification Gratuit, De base, Standard, Premium et Premiumv2. Il y a également l’environnement Azure App Service Environment (ASE) de locataire unique, qui héberge les plans App Service de référence SKU Isolé directement dans votre réseau virtuel Microsoft Azure. Les fonctionnalités que vous utilisez varient en fonction du déploiement (service multilocataire ou ASE). 

## <a name="multi-tenant-app-service-networking-features"></a>Fonctionnalités de mise en réseau App Service multilocataire 

Azure App Service est un système distribué. Les rôles qui gèrent les requêtes HTTP/HTTPS entrantes sont appelés serveurs frontend. Les rôles qui hébergent la charge de travail du client sont appelés rôles de travail. Tous les rôles d’un déploiement App Service existent dans un réseau multilocataire. Comme il existe de nombreux clients différents dans la même unité d’échelle App Service, vous ne pouvez pas connecter le réseau App Service directement à votre réseau. Au lieu de connecter les réseaux, nous avons besoin de fonctionnalités pour gérer différents aspects de la communication des applications. Les fonctionnalités qui gèrent les demandes VERS votre application ne peuvent pas être utilisées pour résoudre des problèmes lorsque des appels sont effectués DEPUIS votre application. De même, les fonctionnalités qui résolvent des problèmes pour les appels DEPUIS votre application ne peuvent pas être utilisées pour résoudre des problèmes VERS votre application.  

| Fonctionnalités en entrée | Fonctionnalités en sortie |
|---------------------|-------------------|
| Adresse attribuée par l’application | les connexions hybrides |
| Restrictions d’accès | Passerelle exigeant l’intégration au réseau virtuel |
| Points de terminaison de service | Intégration au réseau virtuel (préversion) |

Sauf indication contraire, toutes les fonctionnalités peuvent être utilisées ensemble. Vous pouvez combiner les fonctionnalités pour résoudre différents problèmes.

## <a name="use-case-and-features"></a>Cas d’usage et fonctionnalités

Pour tout cas d’usage, il peut y avoir plusieurs façons de résoudre le problème.  La fonctionnalité appropriée à utiliser dépend parfois d’autres critères que le cas d’usage en lui-même. Les cas d’usage en entrée suivants suggèrent comment utiliser des fonctionnalités de mise en réseau App Service pour résoudre des problèmes en lien avec le contrôle du trafic vers votre application. 
 
| Cas d’usage en entrée | Fonctionnalité |
|---------------------|-------------------|
| Prendre en charge les besoins SSL en fonction des adresses IP pour votre application | adresse attribuée par l’application |
| Adresse entrante dédiée, non partagée pour votre application | adresse attribuée par l’application |
| Restreindre l’accès à votre application à partir d’un ensemble d’adresses bien définies | Restrictions d’accès |
| Exposer mon application sur des adresses IP privées dans mon réseau virtuel | ASE ILB </br> Application Gateway avec des points de terminaison de service |
| Restreindre l’accès à mon application à partir des ressources dans un réseau virtuel | Points de terminaison de service </br> ASE ILB |
| Exposer mon application sur une adresse IP privée dans mon réseau virtuel | ASE ILB </br> adresse IP privée en entrée sur Application Gateway avec des points de terminaison de service |
| Protéger mon application avec un pare-feu d’applications web | Application Gateway + ASE ILB </br> Application Gateway avec des points de terminaison de service </br> Azure Front Door avec des restrictions d’accès |
| Équilibrer la charge du trafic vers mes applications dans différentes régions | Azure Front Door avec des restrictions d’accès | 
| Équilibrer la charge du trafic dans la même région | Application Gateway avec des points de terminaison de service | 

Les cas d’usage en sortie suivants suggèrent comment utiliser les fonctionnalités de mise en réseau App Service pour répondre aux besoins d’accès sortant de votre application. 

| Cas d’usage en sortie | Fonctionnalité |
|---------------------|-------------------|
| Accéder à des ressources dans un réseau virtuel Azure situé dans la même région | Intégration au réseau virtuel </br> ASE |
| Accéder à des ressources dans un réseau virtuel Azure situé dans une région différente | Passerelle exigeant l’intégration au réseau virtuel </br> ASE et peering de réseau virtuel |
| Accéder à des ressources sécurisées avec des points de terminaison de service | Intégration au réseau virtuel </br> ASE |
| Accéder à des ressources dans un réseau privé non connecté à Azure | les connexions hybrides |
| Accéder à des ressources via des circuits ExpressRoute | Intégration au réseau virtuel (restreinte pour les adresses RFC 1918 pour l’instant) </br> ASE | 


### <a name="default-networking-behavior"></a>Comportement de mise en réseau par défaut

Les unités d’échelle Azure App Service prennent en charge de nombreux clients dans chaque déploiement. Les plans de référence SKU Gratuit et Partagé hébergent des charges de travail clientes sur les rôles de travail multilocataires. Les plans De base, et supérieurs, hébergent les charges de travail clientes dédiées à un seul plan App Service. Si vous aviez un plan App Service Standard, toutes les applications dans ce plan sont exécutées sur le même rôle de travail. Si vous effectuez un scale out du rôle de travail, toutes les applications dans ce plan App Service seront répliquées sur un nouveau rôle de travail pour chaque instance dans votre plan App Service. Les rôles de travail qui sont utilisés pour Premiumv2 sont différents des rôles de travail utilisés pour les autres plans. Chaque déploiement App Service a une adresse IP qui est utilisée pour tout le trafic entrant vers les applications dans ce déploiement App Service. Toutefois, il existe entre 4 et 11 adresses qui sont utilisées pour effectuer des appels sortants. Ces adresses sont partagées par toutes les applications dans ce déploiement App Service. Les adresses sortantes diffèrent en fonction des divers types de rôles de travail. Cela signifie que les adresses utilisées par les plans App Service Gratuit, Partagé, De base, Standard et Premium sont différentes de celles utilisées pour les appels sortants des plans App Service Premiumv2. Si vous observez les propriétés de votre application, vous pouvez voir les adresses entrantes et sortantes qui sont utilisées par votre application. Si vous avez besoin de verrouiller une dépendance avec une liste de contrôle d’accès d’adresses IP, utilisez possibleOutboundAddresses. 

![Propriétés de l’application](media/networking-features/app-properties.png)

App Service a plusieurs points de terminaison qui sont utilisés pour gérer le service.  Ces adresses sont publiées dans un document distinct et se trouvent également dans la balise de service d’adresse IP AppServiceManagement. La balise AppServiceManagement est utilisée uniquement avec un App Service environnement (ASE) dans lequel vous devez autoriser ce trafic. Les adresses App Service entrantes sont suivies dans la balise de service d’adresse IP App Service. Il n’y a aucune balise de service d’adresse IP qui contient les adresses sortantes utilisées par App Service. 

![Schéma des entrées et sorties App Service](media/networking-features/default-behavior.png)

### <a name="app-assigned-address"></a>Adresse attribuée par l’application 

La fonctionnalité d’adresse attribuée par l’application est une ramification de la fonctionnalité SSL basée sur des adresses IP, qui est accessible en configurant SSL avec votre application. Cette fonctionnalité peut être utilisée pour les appels SSL basés sur une adresse IP, mais elle peut également servir à donner à votre application une adresse que juste elle possède. 

![Schéma de la fonctionnalité d’adresse attribuée par l’application](media/networking-features/app-assigned-address.png)

Lorsque vous utilisez une adresse attribuée par l’application, votre trafic traverse toujours les mêmes rôles frontend qui gèrent tout le trafic entrant dans l’unité d’échelle App Service. Néanmoins, l’adresse qui est attribuée à votre application est uniquement utilisée par votre application. Cette fonctionnalité peut notamment être utilisée dans les cas suivants :

* Prendre en charge les besoins SSL en fonction des adresses IP pour votre application
* Définir une adresse dédiée pour votre application qui n’est pas partagée avec un autre élément

Vous pouvez apprendre à définir une adresse sur votre application en suivant le tutoriel sur [la configuration SSL basée sur des adresses IP][appassignedaddress]. 

### <a name="access-restrictions"></a>Restrictions d’accès 

La fonctionnalité Restrictions d’accès vous permet de filtrer les requêtes **entrantes** en fonction de l’adresse IP d’origine. L’action de filtrage a lieu sur les rôles front-end situés en amont des déploiements des rôles de travail où vos applications s’exécutent. Étant donné que les rôles frontend sont en amont des rôles de travail, la fonctionnalité Restrictions d’accès peut être considérée comme une protection de vos applications au niveau du réseau. La fonctionnalité vous permet de créer une liste de blocs d’adresses d’autorisation et de refus qui sont évalués par ordre de priorité. Elle est similaire à la fonctionnalité de groupe de sécurité réseau qui existe dans Mise en réseau Azure.  Vous pouvez utiliser cette fonctionnalité dans un ASE ou dans le service multilocataire. Lorsqu’elle est utilisée avec un ASE ILB, vous pouvez restreindre l’accès à partir de blocs d’adresses privées.

![Restrictions d’accès](media/networking-features/access-restrictions.png)

La fonctionnalité Restrictions d’accès est utile dans les scénarios dans lesquels vous souhaitez limiter les adresses IP qui peuvent être utilisées pour accéder à votre application. Cette fonctionnalité peut notamment être utilisée dans les cas suivants :

* Restreindre l’accès à votre application à partir d’un ensemble d’adresses bien définies 
* Restreindre les accès provenant d’un service d’équilibrage de charge, tels qu’Azure Front Door. Si vous souhaitiez verrouiller votre trafic entrant vers Azure Front Door, créez des règles pour autoriser le trafic à partir de 147.243.0.0/16 et 2a01:111:2050::/44. 

![Restrictions d’accès avec Front Door](media/networking-features/access-restrictions-afd.png)

Si vous souhaitez verrouiller l’accès à votre application de sorte qu’elle soit uniquement accessible à partir de ressources dans votre réseau virtuel Azure, vous avez besoin d’une adresse publique statique à l’emplacement où se trouve votre source dans votre réseau virtuel. Si les ressources n’ont pas d’adresse publique, vous devez utiliser la fonctionnalité Points de terminaison de service à la place. Découvrez comment activer cette fonctionnalité avec le didacticiel sur la [configuration de la fonctionnalité Restrictions d’accès][iprestrictions].

### <a name="service-endpoints"></a>Points de terminaison de service

La fonctionnalité Points de terminaison de service vous permet de verrouiller l’accès **entrant** à votre application, de façon que l’adresse source doit provenir d’un ensemble de sous-réseaux que vous sélectionnez. Cette fonctionnalité fonctionne conjointement avec les restrictions d’accès d’adresse IP. Les points de terminaison de service sont définis dans la même expérience utilisateur que les restrictions d’accès d’adresse IP. Vous pouvez créer une liste d’autorisation/de refus des règles d’accès qui inclut les adresses publiques, ainsi que des sous-réseaux dans vos réseaux virtuels. Cette fonctionnalité prend en charge des scénarios tels que :

![points de terminaison de service](media/networking-features/service-endpoints.png)

* La configuration d’Application Gateway avec votre application pour verrouiller le trafic entrant vers votre application
* La restriction de l’accès de votre application aux ressources de votre réseau virtuel. Cela peut inclure des machines virtuelles, des ASE ou même d’autres applications qui utilisent l’intégration au réseau virtuel 

![points de terminaison de service avec application gateway](media/networking-features/service-endpoints-appgw.png)

Vous trouverez plus d’informations sur la configuration des points de terminaison de service avec votre application dans le didacticiel sur la [configuration de la fonctionnalité Restrictions d’accès de point de terminaison de service][serviceendpoints]
 
### <a name="hybrid-connections"></a>les connexions hybrides

La fonctionnalité Connexions hybrides App Service permet à vos applications d’effectuer des appels **sortants** vers des points de terminaison TCP spécifiés. Le point de terminaison peut être en local, dans un réseau virtuel ou dans n’importe quel emplacement qui autorise le trafic sortant vers Azure sur le port 443. La fonctionnalité nécessite l’installation d’un agent de relais appelé Hybrid Connection Manager (HCM) sur un ordinateur hôte Windows Server 2012 ou plus récent. HCM doit être en mesure d’atteindre Azure Relay sur le port 443. HCM peut être téléchargé à partir de l’interface utilisateur Connexions hybrides App Service dans le portail. 

![Flux réseau Connexions hybrides](media/networking-features/hybrid-connections.png)

La fonctionnalité Connexions hybrides App Service repose sur la fonctionnalité Connexions hybrides Azure Relay. App Service utilise une forme spécialisée de la fonctionnalité qui prend uniquement en charge les appels sortants de votre application vers un port et un hôte TCP. Cet hôte et ce port doivent uniquement effectuer la résolution sur l’hôte sur lequel HCM est installé. Lorsque l’application, dans App Service, effectue une recherche DNS sur l’hôte et le port définis dans votre connexion hybride, le trafic est redirigé automatiquement pour traverser la connexion hybride et sortir de HCM. Pour en savoir plus sur les connexions hybrides, consultez la documentation sur les [connexions hybrides App Service][hybridconn]

Cette fonctionnalité est couramment utilisée pour :

* Accéder aux ressources dans des réseaux privés qui ne sont pas connectés à Azure avec une connexion VPN ou ExpressRoute
* Prendre en charge les opérations lift and shift des applications locales vers App Service sans avoir à déplacer également les bases de données prise en charge  
* Fournir un accès sécurisé à un seul hôte et un port par connexion hybride. La plupart des fonctionnalités de mise en réseau ouvrent l’accès à un réseau et avec Connexions hybrides, vous pouvez uniquement avoir les hôte et port uniques que vous pouvez atteindre.
* Aborder les scénarios non couverts par les autres méthodes de connectivité sortante
* Effectuer le développement dans App Service où les applications peuvent facilement tirer parti des ressources locales 

Étant donné que la fonctionnalité permet d’accéder aux ressources locales sans un trou de pare-feu de trafic entrant, elle est populaire auprès des développeurs. Les autres fonctionnalités de mise en réseau App Service sortantes sont en lien très étroit avec le réseau virtuel Azure. La fonctionnalité Connexions hybrides ne doit pas nécessairement passer par un réseau virtuel et peut être utilisée pour des besoins de mise en réseau plus larges. Il est important de noter que la fonctionnalité Connexions hybrides App Service n’a que faire de savoir ce que vous utilisez en plus de celle-ci ou de le savoir. Cela signifie que vous pouvez l’utiliser pour accéder à une base de données, à un service web ou à un socket TCP arbitraire sur un mainframe. La fonctionnalité crée essentiellement des tunnels pour les paquets TCP. 

Bien que la fonctionnalité Connexions hybrides soit populaire pour le développement, elle est également utilisée dans de nombreuses applications de production. Elle est très utile pour accéder à un service web ou à une base de données, mais elle n’est pas appropriée pour les situations impliquant la création de nombreuses connexions. 

### <a name="gateway-required-vnet-integration"></a>Passerelle exigeant l’intégration au réseau virtuel 

La fonctionnalité Passerelle exigeant l’intégration au réseau virtuel App Service permet à votre application d’envoyer des requêtes **sortantes** dans un réseau virtuel Azure. La fonctionnalité fonctionne en connectant l’hôte sur lequel votre application s’exécute à une passerelle de réseau virtuel sur votre réseau virtuel avec une connexion VPN de point à site. Lorsque vous configurez la fonctionnalité, votre application obtient une des adresses de point à site attribuées à chaque instance. Cette fonctionnalité vous permet d’accéder aux ressources dans les réseaux virtuels classiques ou Resource Manager dans une région. 

![Passerelle exigeant l’intégration au réseau virtuel](media/networking-features/gw-vnet-integration.png)

Cette fonctionnalité résout le problème de l’accès aux ressources dans d’autres réseaux virtuels et peut même être utilisée pour se connecter via un réseau virtuel à d’autres réseaux virtuels, voire à des réseaux locaux. Elle ne fonctionne pas avec des réseaux virtuels connectés à ExpressRoute, mais fonctionne avec des réseaux connectés VPN de site à site. Il est normalement inapproprié d’utiliser cette fonctionnalité à partir d’une application dans un ASE, car l’ASE est déjà dans votre réseau virtuel. Les cas d’usage que cette fonctionnalité traite sont les suivants :

* Accéder aux ressources sur des adresses IP privées dans vos réseaux virtuels Azure 
* Accéder aux ressources locales s’il existe un VPN de site à site 
* Accéder aux ressources dans des réseaux virtuels avec peering 

Lorsque cette fonctionnalité est activée, votre application utilise le serveur DNS avec lequel le réseau virtuel de destination est configuré. Vous trouverez plus d’informations sur cette fonctionnalité dans la documentation sur [l’intégration au réseau virtuel App Service][vnetintegrationp2s]. 

### <a name="vnet-integration"></a>Intégration au réseau virtuel

La fonctionnalité Passerelle exigeant l’intégration au réseau virtuel est très utile mais ne résout pas le problème d’accès aux ressources via ExpressRoute. En plus de devoir atteindre les ressources via des connexions ExpressRoute, il est nécessaire que les applications soient en mesure d’effectuer des appels vers les services sécurisés de point de terminaison de service. Pour traiter ces deux besoins supplémentaires, une autre fonctionnalité Intégration au réseau virtuel a été ajoutée. La nouvelle fonctionnalité Intégration au réseau virtuel vous permet de placer le back end de votre application dans un sous-réseau dans un réseau virtuel Resource Manager dans la même région. Cette fonctionnalité n’est pas disponible à partir d’un ASE, qui se trouve déjà dans un réseau virtuel. Cette fonctionnalité permet :

* D’accéder aux ressources dans les réseaux virtuels Resource Manager de la même région
* D’accéder à des ressources sécurisées avec des points de terminaison de service 
* D’accéder à des ressources qui sont accessibles via des connexions ExpressRoute ou VPN

![Intégration au réseau virtuel](media/networking-features/vnet-integration.png)

Cette fonctionnalité est en préversion et ne doit pas être utilisée pour les charges de travail en production. Pour en savoir plus sur cette fonctionnalité, lisez la documentation sur [l’intégration au réseau virtuel App Service][vnetintegration].

## <a name="app-service-environment"></a>Environnement App Service 

Un ASE est un déploiement avec locataire unique d’Azure App Service, qui s’exécute sur votre réseau virtuel. Vous pouvez utiliser l’ASE dans les cas suivants :

* Accéder aux ressources dans votre réseau virtuel
* Accéder aux ressources via ExpressRoute
* Exposer vos applications avec une adresse privée dans votre réseau virtuel 
* Accéder aux ressources via des points de terminaison de service 

Avec un ASE, vous n’avez pas besoin d’utiliser des fonctionnalités telles que l’intégration au réseau virtuel ou les points de terminaison de service, car l’ASE est déjà dans votre réseau virtuel. Si vous souhaitez accéder aux ressources telles que SQL ou Stockage sur les points de terminaison de service, activez les points de terminaison de service sur le sous-réseau ASE. Si vous souhaitez accéder aux ressources dans le réseau virtuel, aucune configuration supplémentaire n’est requise.  Si vous souhaitez accéder aux ressources sur ExpressRoute, vous êtes déjà dans le réseau virtuel et n’avez pas besoin de configurer quoi que ce soit sur l’ASE ou les applications qu’il contient. 

Étant donné que les applications dans un ASE ILB peuvent être exposées sur une adresse IP privée, vous pouvez facilement ajouter des appareils de pare-feu d’applications web pour exposer uniquement les applications que vous souhaitez sur Internet tout en sécurisant le reste. L’ASE est tout à fait adapté pour le déploiement simple d’applications à plusieurs niveaux. 

Néanmoins, certaines actions ne peuvent pas encore être réalisées depuis le service multilocataire alors qu’elles sont possibles via un ASE. Celles-ci comprennent :

* L’exposition de vos applications sur une adresse IP privée
* La sécurisation de l’ensemble du trafic sortant avec les contrôles réseau qui ne font pas partie de votre application 
* L’hébergement de vos applications dans un service de locataire unique 
* La montée en puissance vers beaucoup plus d’instances que dans le service multilocataire 
* Le chargement de certificats clients d’autorité de certification privés pour une utilisation par vos applications avec des points de terminaison sécurisés d’autorité de certification privés 
* L’application forcée du protocole TLS 1.1 sur toutes les applications hébergées dans le système sans possibilité de le désactiver au niveau de l’application 
* La proposition d’une adresse sortante dédiée pour toutes les applications dans votre ASE qui n’est pas partagée avec des clients 

![ASE dans un réseau virtuel](media/networking-features/app-service-environment.png)

L’ASE offre la meilleure solution en ce qui concerne l’hébergement d’applications dédiées et isolées, mais présente également quelques problèmes de gestion. Voici quelques points à prendre en compte avant d’utiliser un ASE opérationnel :
 
 * Un ASE s’exécute au sein de votre réseau virtuel, mais a des dépendances en dehors du réseau virtuel. Ces dépendances doivent être autorisées. Apprenez-en davantage dans [Networking considerations for an App Service Environment][networkinfo] (Considérations relatives à la mise en réseau pour un App Service Environment)
 * Un ASE n’évolue pas immédiatement comme le service multilocataire. Vous devez anticiper les besoins de mise à l’échelle, plutôt que d’effectuer une mise à l’échelle quand il est trop tard. 
 * Les frais initiaux d’un ASE sont plus élevés. Afin de tirer le meilleur parti de votre ASE, vous devez planifier l’ajout de nombreuses charges de travail dans un ASE plutôt que de l’utiliser pour quelques tâches uniquement
 * Les applications dans un ASE ne peuvent pas restreindre l’accès à certaines applications dans un ASE et pas dans d’autres.
 * L’ASE se trouve dans un sous-réseau et des règles de mise en réseau s’appliquent à tout le trafic vers et depuis cet environnement ASE. Si vous souhaitez attribuer des règles de trafic entrant pour une seule application, utilisez la fonctionnalité Restrictions d’accès. 

## <a name="combining-features"></a>Combinaison de fonctionnalités 

Les fonctionnalités indiquées pour le service multilocataire peuvent être utilisées ensemble pour traiter des cas d’usage plus complexes. Deux des cas d’usage les plus courants sont décrits ici, mais ils servent uniquement d’exemples. En comprenant le fonctionnement des différentes fonctionnalités, vous pouvez traiter pratiquement tous les besoins architecturaux de votre système.

### <a name="inject-app-into-a-vnet"></a>Injecter une application dans un réseau virtuel

Une requête courante est de savoir comment ajouter votre application dans un réseau virtuel. Ajouter votre application dans un réseau virtuel signifie que les points de terminaison entrants et sortants d’une application se trouvent au sein d’un réseau virtuel. L’ASE offre la meilleure solution pour résoudre ce problème, mais vous pouvez obtenir la majeure partie de ce qui est nécessaire dans le service multilocataire en combinant des fonctionnalités. Par exemple, vous pouvez héberger des applications Intranet uniquement avec des adresses entrantes et sortantes privées en :

* Créant une instance Application Gateway avec une adresse entrante et sortante privée
* Sécurisant le trafic entrant vers votre application avec des points de terminaison de service 
* Utilisant la nouvelle fonctionnalité Intégration au réseau virtuel de façon que le back end de votre application se trouve dans votre réseau virtuel 

Ce style de déploiement ne vous donnerait pas d’adresse dédiée pour le trafic sortant vers Internet ou ne vous donnerait pas la possibilité de verrouiller tout le trafic sortant depuis votre application.  Ce style de déploiement vous permettrait d’obtenir autant d’éléments que ceux que vous pourriez avoir avec un ASE. 

### <a name="create-multi-tier-applications"></a>Créer des applications avec plusieurs niveaux

Une application avec plusieurs niveaux est une application où les applications de back end d’API sont uniquement accessibles à partir du niveau frontend. Pour créer une application à plusieurs niveaux, vous pouvez :

* Utiliser la fonctionnalité Intégration au réseau virtuel pour connecter le back end de votre application web frontend à un sous-réseau dans un réseau virtuel
* Utiliser des points de terminaison de service pour sécuriser le trafic entrant vers votre application API à celui provenant uniquement du sous-réseau utilisé par votre application web frontend

![application à plusieurs niveaux](media/networking-features/multi-tier-app.png)

Vous pouvez avoir plusieurs applications frontend qui utilisent la même application API à l’aide de la fonctionnalité Intégration au réseau virtuel à partir d’autres applications frontend et points de terminaison de service de l’application API avec leurs sous-réseaux.  

<!--Links-->
[appassignedaddress]: https://docs.microsoft.com/azure/app-service/configure-ssl-certificate
[iprestrictions]: https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions
[serviceendpoints]: https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions
[hybridconn]: https://docs.microsoft.com/azure/app-service/app-service-hybrid-connections
[vnetintegrationp2s]: https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet
[vnetintegration]: https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet
[networkinfo]: https://docs.microsoft.com/azure/app-service/environment/network-info
