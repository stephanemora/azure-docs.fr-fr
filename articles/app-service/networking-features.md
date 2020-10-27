---
title: Fonctionnalités réseau
description: Découvrez les fonctionnalités réseau d’Azure App Service ainsi que les fonctionnalités nécessaires à la sécurité et au fonctionnement de votre réseau.
author: ccompy
ms.assetid: 5c61eed1-1ad1-4191-9f71-906d610ee5b7
ms.topic: article
ms.date: 10/18/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 860b1ac1713ac7afb7db2643d68974b399b5236b
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/19/2020
ms.locfileid: "92207044"
---
# <a name="app-service-networking-features"></a>Fonctionnalités de mise en réseau App Service

Dans Azure App Service, les applications peuvent être déployées de nombreuses façons. Par défaut, les applications hébergées App Service sont directement accessibles via Internet et ne peuvent atteindre que les points de terminaison Internet hébergés. Toutefois, la plupart des applications clientes ont besoin de contrôler le trafic réseau entrant et sortant. Il existe plusieurs fonctionnalités disponibles dans App Service pour répondre à ces besoins. Le défi est de savoir quelle fonctionnalité doit être utilisée pour résoudre un problème donné. Ce document vise à aider les clients à déterminer la fonction à utiliser grâce à différents cas d’usage.

Il existe deux types de déploiement principaux pour Azure App Service. Il y a le service public multilocataire, qui héberge les plans App Service dans les niveaux tarifaires Gratuit, Partagé, De base, Standard, Premium, Premiumv2 et Premiumv3. Il y a également l’environnement Azure App Service Environment (ASE) de locataire unique, qui héberge les plans App Service de référence SKU Isolé directement dans votre réseau virtuel Microsoft Azure. Les fonctionnalités que vous utilisez varient en fonction du déploiement (service multilocataire ou ASE). 

## <a name="multi-tenant-app-service-networking-features"></a>Fonctionnalités de mise en réseau App Service multilocataire 

Azure App Service est un système distribué. Les rôles qui gèrent les requêtes HTTP/HTTPS entrantes sont appelés serveurs frontend. Les rôles qui hébergent la charge de travail du client sont appelés rôles de travail. Tous les rôles d’un déploiement App Service existent dans un réseau multilocataire. Comme il existe de nombreux clients différents dans la même unité d’échelle App Service, vous ne pouvez pas connecter le réseau App Service directement à votre réseau. Au lieu de connecter les réseaux, nous avons besoin de fonctionnalités pour gérer différents aspects de la communication des applications. Les fonctionnalités qui gèrent les demandes VERS votre application ne peuvent pas être utilisées pour résoudre des problèmes lorsque des appels sont effectués DEPUIS votre application. De même, les fonctionnalités qui résolvent des problèmes pour les appels DEPUIS votre application ne peuvent pas être utilisées pour résoudre des problèmes VERS votre application.  

| Fonctionnalités en entrée | Fonctionnalités en sortie |
|---------------------|-------------------|
| Adresse attribuée par l’application | les connexions hybrides |
| Restrictions d’accès | Intégration au réseau virtuel avec passerelle obligatoire |
| Points de terminaison de service | Intégration au réseau virtuel |

Sauf indication contraire, toutes les fonctionnalités peuvent être utilisées ensemble. Vous pouvez combiner les fonctionnalités pour résoudre différents problèmes.

## <a name="use-case-and-features"></a>Cas d’usage et fonctionnalités

Pour tout cas d’usage, il peut y avoir plusieurs façons de résoudre le problème.  La fonctionnalité appropriée à utiliser dépend parfois d’autres critères que le cas d’usage en lui-même. Les cas d’usage en entrée suivants suggèrent comment utiliser des fonctionnalités de mise en réseau App Service pour résoudre des problèmes en lien avec le contrôle du trafic vers votre application. 
 
| Cas d’usage en entrée | Fonctionnalité |
|---------------------|-------------------|
| Prendre en charge les besoins SSL en fonction des adresses IP pour votre application | adresse attribuée par l’application |
| Adresse entrante dédiée, non partagée pour votre application | adresse attribuée par l’application |
| Restreindre l’accès à votre application à partir d’un ensemble d’adresses bien définies | Restrictions d’accès |
| Restreindre l’accès à mon application à partir des ressources dans un réseau virtuel | Points de terminaison de service </br> ASE ILB </br> Instances Private Endpoint |
| Exposer mon application sur une adresse IP privée dans mon réseau virtuel | ASE ILB </br> Instances Private Endpoint </br> adresse IP privée en entrée sur Application Gateway avec des points de terminaison de service |
| Protéger mon application avec un Web Application Firewall (WAF) | Application Gateway + ASE ILB </br> Application Gateway avec des points de terminaison privés </br> Application Gateway avec des points de terminaison de service </br> Azure Front Door avec des restrictions d’accès |
| Équilibrer la charge du trafic vers mes applications dans différentes régions | Azure Front Door avec des restrictions d’accès | 
| Équilibrer la charge du trafic dans la même région | [Application Gateway avec des points de terminaison de service][appgwserviceendpoints] | 

Les cas d’usage en sortie suivants suggèrent comment utiliser les fonctionnalités de mise en réseau App Service pour répondre aux besoins d’accès sortant de votre application. 

| Cas d’usage en sortie | Fonctionnalité |
|---------------------|-------------------|
| Accéder à des ressources dans un réseau virtuel Azure situé dans la même région | Intégration au réseau virtuel </br> ASE |
| Accéder à des ressources dans un réseau virtuel Azure situé dans une région différente | Intégration au réseau virtuel avec passerelle obligatoire </br> ASE et peering de réseau virtuel |
| Accéder à des ressources sécurisées avec des points de terminaison de service | Intégration au réseau virtuel </br> ASE |
| Accéder à des ressources dans un réseau privé non connecté à Azure | les connexions hybrides |
| Accéder à des ressources via des circuits ExpressRoute | Intégration au réseau virtuel </br> ASE | 
| Sécuriser le trafic sortant à partir de votre application web | Intégration au réseau virtuel et groupes de sécurité réseau </br> ASE | 
| Router le trafic sortant à partir de votre application web | Intégration au réseau virtuel et tables de route </br> ASE | 


### <a name="default-networking-behavior"></a>Comportement de mise en réseau par défaut

Les unités d’échelle Azure App Service prennent en charge de nombreux clients dans chaque déploiement. Les plans de référence SKU Gratuit et Partagé hébergent des charges de travail clientes sur les rôles de travail multilocataires. Les plans De base, et supérieurs, hébergent les charges de travail clientes dédiées à un seul plan App Service. Si vous aviez un plan App Service Standard, toutes les applications dans ce plan sont exécutées sur le même rôle de travail. Si vous effectuez un scale-out du rôle de travail, toutes les applications dans ce plan App Service seront répliquées sur un nouveau rôle de travail pour chaque instance dans votre plan App Service. 

#### <a name="outbound-addresses"></a>Adresses sortantes

Les machines virtuelles de travail sont décomposées en grande partie par les plans tarifaires App Service. Les plans Gratuit, Partagé, De base, Standard et Premium utilisent tous le même type de machine virtuelle de travail. Premiumv2 est sur un autre type de machine virtuelle. Premiumv3 est sur un autre type de machine virtuelle encore. À chaque changement dans la famille de machines virtuelles, il existe un ensemble différent d’adresses sortantes. Si vous mettez à l’échelle de Standard vers Premiumv2, vos adresses sortantes changent. Si vous mettez à l’échelle de Premiumv2 vers Premiumv3, vos adresses sortantes changent. Il existe des unités d’échelle plus anciennes qui modifient à la fois les adresses entrantes et sortantes lorsque vous effectuez une mise à l’échelle de Standard vers Premiumv2. Un certain nombre d’adresses sont utilisées pour effectuer des appels sortants. Les adresses sortantes utilisées par votre application pour effectuer des appels sortants sont répertoriées dans les propriétés de votre application. Ces adresses sont partagées par toutes les applications qui s’exécutent sur la même famille de machines virtuelles de travail dans ce déploiement App Service. Si vous souhaitez afficher toutes les adresses possibles que votre application peut utiliser dans cette unité d’échelle, une autre propriété appelée possibleOutboundAddresses les répertorie. 

![Propriétés de l’application](media/networking-features/app-properties.png)

App Service a plusieurs points de terminaison qui sont utilisés pour gérer le service.  Ces adresses sont publiées dans un document distinct et se trouvent également dans la balise de service d’adresse IP AppServiceManagement. La balise AppServiceManagement est utilisée uniquement avec un App Service Environnement dans lequel vous devez autoriser ce trafic. Les adresses App Service entrantes sont suivies dans la balise de service d’adresse IP App Service. Il n’y a aucune balise de service d’adresse IP qui contient les adresses sortantes utilisées par App Service. 

![Schéma des entrées et sorties App Service](media/networking-features/default-behavior.png)

### <a name="app-assigned-address"></a>Adresse attribuée par l’application 

La fonctionnalité d’adresse attribuée par l’application est une ramification de la fonctionnalité SSL basée sur des adresses IP, qui est accessible en configurant SSL avec votre application. Cette fonctionnalité peut être utilisée pour les appels SSL basés sur une adresse IP, mais elle peut également servir à donner à votre application une adresse que juste elle possède. 

![Schéma de la fonctionnalité d’adresse attribuée par l’application](media/networking-features/app-assigned-address.png)

Lorsque vous utilisez une adresse attribuée par l’application, votre trafic traverse toujours les mêmes rôles frontend qui gèrent tout le trafic entrant dans l’unité d’échelle App Service. Néanmoins, l’adresse qui est attribuée à votre application est uniquement utilisée par votre application. Cette fonctionnalité peut notamment être utilisée dans les cas suivants :

* Prendre en charge les besoins SSL en fonction des adresses IP pour votre application
* Définir une adresse dédiée pour votre application qui n’est pas partagée avec un autre élément

Vous pouvez apprendre à définir une adresse sur votre application en suivant le tutoriel [Ajouter un certificat TLS/SSL dans Azure App Service][appassignedaddress]. 

### <a name="access-restrictions"></a>Restrictions d’accès 

La fonctionnalité Restrictions d’accès vous permet de filtrer les requêtes **entrantes** en fonction de l’adresse IP d’origine. L’action de filtrage a lieu sur les rôles front-end situés en amont des déploiements des rôles de travail où vos applications s’exécutent. Étant donné que les rôles frontend sont en amont des rôles de travail, la fonctionnalité Restrictions d’accès peut être considérée comme une protection de vos applications au niveau du réseau. La fonctionnalité vous permet de créer une liste de blocs d’adresses d’autorisation et de refus qui sont évalués par ordre de priorité. Elle est similaire à la fonctionnalité de groupe de sécurité réseau qui existe dans Mise en réseau Azure.  Vous pouvez utiliser cette fonctionnalité dans un ASE ou dans le service multilocataire. Lorsqu’elle est utilisée avec un ASE ILB, vous pouvez restreindre l’accès à partir de blocs d’adresses privées.

![Restrictions d’accès](media/networking-features/access-restrictions.png)

La fonctionnalité Restrictions d’accès est utile dans les scénarios dans lesquels vous souhaitez limiter les adresses IP qui peuvent être utilisées pour accéder à votre application. Cette fonctionnalité peut notamment être utilisée dans les cas suivants :

* Restreindre l’accès à votre application à partir d’un ensemble d’adresses bien définies 
* Restreindre les accès provenant d’un service d’équilibrage de charge, tels qu’Azure Front Door. Si vous souhaitiez verrouiller votre trafic entrant vers Azure Front Door, créez des règles pour autoriser le trafic à partir de 147.243.0.0/16 et 2a01:111:2050::/44. 

![Restrictions d’accès avec Front Door](media/networking-features/access-restrictions-afd.png)

Si vous souhaitez verrouiller l’accès à votre application de sorte qu’elle soit uniquement accessible à partir de ressources dans votre réseau virtuel Azure, vous avez besoin d’une adresse publique statique à l’emplacement où se trouve votre source dans votre réseau virtuel. Si les ressources n’ont pas d’adresse publique, vous devez utiliser la fonctionnalité Points de terminaison de service à la place. Découvrez comment activer cette fonctionnalité avec le didacticiel sur la [configuration de la fonctionnalité Restrictions d’accès][iprestrictions].

### <a name="service-endpoints"></a>Points de terminaison de service

Les points de terminaison de service vous permettent de verrouiller l’accès **entrant** à votre application, de façon que l’adresse source doive provenir d’un ensemble de sous-réseaux que vous sélectionnez. Cette fonctionnalité fonctionne conjointement avec les restrictions d’accès d’adresse IP. Les points de terminaison de service ne sont pas compatibles avec le débogage à distance. Pour utiliser le débogage à distance avec votre application, votre client ne peut pas se trouver dans un sous-réseau dans lequel des points de terminaison de service sont activés. Les points de terminaison de service sont définis dans la même expérience utilisateur que les restrictions d’accès d’adresse IP. Vous pouvez créer une liste d’autorisation/de refus des règles d’accès qui inclut les adresses publiques, ainsi que des sous-réseaux dans vos réseaux virtuels. Cette fonctionnalité prend en charge des scénarios tels que :

![points de terminaison de service](media/networking-features/service-endpoints.png)

* La configuration d’Application Gateway avec votre application pour verrouiller le trafic entrant vers votre application
* La restriction de l’accès de votre application aux ressources de votre réseau virtuel. Cela peut inclure des machines virtuelles, des ASE ou même d’autres applications qui utilisent l’intégration au réseau virtuel 

![points de terminaison de service avec application gateway](media/networking-features/service-endpoints-appgw.png)

Vous trouverez plus d’informations sur la configuration des points de terminaison de service avec votre application dans le didacticiel sur la [configuration de la fonctionnalité Restrictions d’accès de point de terminaison de service][serviceendpoints]

### <a name="private-endpoints"></a>Points de terminaison privés

Private Endpoint est une interface réseau qui vous permet de vous connecter de façon privée et sécurisée à votre application web Azure Private Link. Private Endpoint utilise une adresse IP privée de votre réseau virtuel, plaçant de fait l’application web dans votre réseau virtuel. Cette fonctionnalité s’applique uniquement aux flux **entrants** dans votre application web.
[Utilisation de points de terminaison privés pour une application web Azure][privateendpoints]

Les points de terminaison privés permettent des scénarios tels que :

* Restreindre l’accès à mon application à partir des ressources dans un réseau virtuel 
* Exposer mon application sur une adresse IP privée dans mon réseau virtuel 
* Protéger mon application avec un pare-feu d’applications web 

Les points de terminaison privés empêchent l’exfiltration de données, car la seule chose que vous pouvez atteindre sur le point de terminaison privé est l’application avec laquelle il est configuré. 
 
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

Étant donné que la fonctionnalité permet d’accéder aux ressources locales sans un trou de pare-feu de trafic entrant, elle est populaire auprès des développeurs. Les autres fonctionnalités de mise en réseau App Service sortantes sont en lien avec le réseau virtuel Azure. La fonctionnalité Connexions hybrides ne doit pas nécessairement passer par un réseau virtuel et peut être utilisée pour des besoins de mise en réseau plus larges. Il est important de noter que la fonctionnalité Connexions hybrides App Service n’a que faire de savoir ce que vous utilisez en plus de celle-ci ou de le savoir. Cela signifie que vous pouvez l’utiliser pour accéder à une base de données, à un service web ou à un socket TCP arbitraire sur un mainframe. La fonctionnalité crée essentiellement des tunnels pour les paquets TCP. 

Bien que la fonctionnalité Connexions hybrides soit populaire pour le développement, elle est également utilisée dans de nombreuses applications de production. Elle est très utile pour accéder à un service web ou à une base de données, mais elle n’est pas appropriée pour les situations impliquant la création de nombreuses connexions. 

### <a name="gateway-required-vnet-integration"></a>Intégration au réseau virtuel avec passerelle obligatoire 

La fonctionnalité Passerelle exigeant l’intégration au réseau virtuel App Service permet à votre application d’envoyer des requêtes **sortantes** dans un réseau virtuel Azure. La fonctionnalité fonctionne en connectant l’hôte sur lequel votre application s’exécute à une passerelle de réseau virtuel sur votre réseau virtuel avec une connexion VPN de point à site. Lorsque vous configurez la fonctionnalité, votre application obtient une des adresses de point à site attribuées à chaque instance. Cette fonctionnalité vous permet d’accéder aux ressources dans les réseaux virtuels classiques ou Resource Manager dans une région. 

![Intégration au réseau virtuel avec passerelle obligatoire](media/networking-features/gw-vnet-integration.png)

Cette fonctionnalité résout le problème de l’accès aux ressources dans d’autres réseaux virtuels et peut même être utilisée pour se connecter via un réseau virtuel à d’autres réseaux virtuels, voire à des réseaux locaux. Elle ne fonctionne pas avec des réseaux virtuels connectés à ExpressRoute, mais fonctionne avec des réseaux connectés VPN de site à site. Il est normalement inapproprié d’utiliser cette fonctionnalité à partir d’une application dans un ASE, car l’ASE est déjà dans votre réseau virtuel. Les cas d’usage que cette fonctionnalité traite sont les suivants :

* Accéder aux ressources sur des adresses IP privées dans vos réseaux virtuels Azure 
* Accéder aux ressources locales s’il existe un VPN de site à site 
* Accéder aux ressources dans des réseaux virtuels avec peering 

Lorsque cette fonctionnalité est activée, votre application utilise le serveur DNS avec lequel le réseau virtuel de destination est configuré. Vous trouverez plus d’informations sur cette fonctionnalité dans la documentation sur [l’intégration au réseau virtuel App Service][vnetintegrationp2s]. 

### <a name="vnet-integration"></a>Intégration au réseau virtuel

La fonctionnalité Passerelle exigeant l’intégration au réseau virtuel est utile mais ne résout pas le problème d’accès aux ressources via ExpressRoute. En plus de devoir atteindre les ressources via des connexions ExpressRoute, il est nécessaire que les applications soient en mesure d’effectuer des appels vers les services sécurisés de point de terminaison de service. Pour traiter ces deux besoins supplémentaires, une autre fonctionnalité Intégration au réseau virtuel a été ajoutée. La nouvelle fonctionnalité Intégration au réseau virtuel vous permet de placer le back end de votre application dans un sous-réseau dans un réseau virtuel Resource Manager dans la même région. Cette fonctionnalité n’est pas disponible à partir d’un ASE, qui se trouve déjà dans un réseau virtuel. Cette fonctionnalité permet :

* D’accéder aux ressources dans les réseaux virtuels Resource Manager de la même région
* D’accéder à des ressources sécurisées avec des points de terminaison de service 
* D’accéder à des ressources qui sont accessibles via des connexions ExpressRoute ou VPN
* De sécuriser tout le trafic sortant 
* De forcer le mode tunneling sur tout le trafic sortant. 

![Intégration au réseau virtuel](media/networking-features/vnet-integration.png)

Pour en savoir plus sur cette fonctionnalité, lisez la documentation sur [l’intégration au réseau virtuel App Service][vnetintegration].

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

Une application avec plusieurs niveaux est une application où les applications de back end d’API sont uniquement accessibles à partir du niveau frontend. Deux méthodes de création d’une application multiniveau sont possibles. Toutes deux commencent par utiliser la fonctionnalité Intégration au réseau virtuel pour connecter votre application web frontale à un sous-réseau dans un réseau virtuel. Cela permet à votre application web d’effectuer des appels dans votre réseau virtuel. Lorsque votre application frontale est connectée au réseau virtuel, vous devez choisir comment verrouiller l’accès à votre application API.  Vous pouvez :

* Héberger l’application frontale et l’application API dans le même ASE ILB et exposer l’application frontale sur Internet avec une passerelle applicative
* Héberger l’application frontale dans le service multilocataire et le serveur principal dans un ASE ILB
* Héberger l’application frontale et l’application API dans le service multilocataire

Si vous hébergez l’application frontale et l’application API pour une application multiniveau, vous pouvez :

Exposer votre application API avec des points de terminaison privés dans votre réseau virtuel

![application avec points de terminaison privés à deux niveaux](media/networking-features/multi-tier-app-private-endpoint.png)

Utiliser des points de terminaison de service pour sécuriser le trafic entrant vers votre application API à celui provenant uniquement du sous-réseau utilisé par votre application web frontend

![application sécurisée avec points de terminaison de service](media/networking-features/multi-tier-app.png)

Les compromis entre les deux techniques sont les suivants :

* Avec des points de terminaison de service, vous n’avez qu’à sécuriser le trafic vers votre application API sur le sous-réseau d’intégration. Cela sécurise l’application API, mais vous pouvez toujours avoir une possibilité d’exfiltration de données de votre application frontale vers d’autres applications du plan App Service.
* Avec des points de terminaison privés, vous avez deux sous-réseaux. Cela augmente la complexité. En outre, le point de terminaison privé est une ressource de niveau supérieur et ajoute un nouvel élément à gérer. L’avantage de l’utilisation de points de terminaison privés est que vous n’avez pas de possibilité d’exfiltration de données. 

L’une ou l’autre technique fonctionne avec plusieurs serveurs frontaux. À petite échelle, les points de terminaison de service sont beaucoup plus faciles à utiliser, car il vous suffit d’activer les points de terminaison de service pour l’application API sur le sous-réseau d’intégration frontal. À mesure que vous ajoutez des applications frontales, vous devez ajuster chaque application API pour avoir des points de terminaison de service avec le sous-réseau d’intégration. Avec des points de terminaison privés, vous avez plus de complexité, mais vous n’avez pas rien à modifier sur vos applications API après avoir défini un point de terminaison privé. 

### <a name="line-of-business-applications"></a>applications métier ;

Les applications métiers sont des applications internes qui ne sont normalement pas exposées pour un accès depuis Internet. Ces applications sont appelées à partir de réseaux d’entreprise où l’accès peut être strictement contrôlé. Si vous utilisez un ASE ILB, il est facile d’héberger vos applications métiers. Si vous utilisez le service multilocataire, vous pouvez utiliser des points de terminaison privés ou des points de terminaison de service associés à une Application Gateway. Il existe deux raisons d’utiliser une Application Gateway avec des points de terminaison de service plutôt que des points de terminaison privés :

* Vous avez besoin d’une protection WAF sur vos applications métiers
* Vous souhaitez équilibrer la charge sur plusieurs instances de vos applications métiers

Si ce n’est pas le cas, il est préférable d’utiliser des points de terminaison privés. Avec des points de terminaison privés disponibles dans le plan App Service, vous pouvez exposer vos applications sur des adresses privées de votre réseau virtuel. Le point de terminaison privé que vous placez dans votre réseau virtuel peut être atteint sur les connexions ExpressRoute et VPN. La configuration de points de terminaison privés exposera vos applications sur une adresse privée, mais vous devrez configurer un DNS pour atteindre cette adresse en local. Pour que cela fonctionne, vous devez transférer la zone Azure DNS privée contenant vos points de terminaison privés vers vos serveurs DNS locaux. Les zones Azure DNS privées ne prennent pas en charge le transfert de zone, mais vous pouvez le prendre en charge à l’aide d’un serveur DNS prévu à cet effet. Ce modèle, [Redirecteur DNS](https://azure.microsoft.com/resources/templates/301-dns-forwarder/), facilite le transfert de votre zone Azure DNS privée vers vos serveurs DNS locaux.

## <a name="app-service-ports"></a>Ports App Service

Si vous analysez l’App Service, vous trouverez plusieurs ports exposés pour les connexions entrantes. Il n’existe aucun moyen de bloquer ou de contrôler l’accès à ces ports dans le service multilocataire. Les ports exposés sont les suivants :

| Utilisation | Ports |
|----------|-------------|
|  HTTP/HTTPS  | 80, 443 |
|  Gestion | 454, 455 |
|  FTP/FTPS    | 21, 990, 10001-10020 |
|  Débogage distant de Visual Studio  |  4020, 4022, 4024 |
|  Service Web Deploy | 8172 |
|  Utilisation de l’infrastructure | 7654, 1221 |

<!--Links-->
[appassignedaddress]: https://docs.microsoft.com/azure/app-service/configure-ssl-certificate
[iprestrictions]: https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions
[serviceendpoints]: https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions
[hybridconn]: https://docs.microsoft.com/azure/app-service/app-service-hybrid-connections
[vnetintegrationp2s]: https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet
[vnetintegration]: https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet
[networkinfo]: https://docs.microsoft.com/azure/app-service/environment/network-info
[appgwserviceendpoints]: https://docs.microsoft.com/azure/app-service/networking/app-gateway-with-service-endpoints
[privateendpoints]: https://docs.microsoft.com/azure/app-service/networking/private-endpoint
