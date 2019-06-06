---
title: Mise en réseau des fonctionnalités de déploiement - Azure App Service | Microsoft Docs
description: Comment utiliser le Service d’application différentes fonctionnalités de mise en réseau
author: ccompy
manager: stefsch
editor: ''
services: app-service\web
documentationcenter: ''
ms.assetid: 5c61eed1-1ad1-4191-9f71-906d610ee5b7
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 05/28/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 666430a11fb95871eb601b2a38eb7b97ad16119f
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66498948"
---
# <a name="app-service-networking-features"></a>Fonctionnalités de mise en réseau App Service

Applications dans Azure App Service peuvent être déployées de plusieurs façons. Par défaut, App Service applications hébergées sont directement accessible via internet et ne peuvent atteindre les points de terminaison internet hébergé. Vous devez toutefois contrôler le trafic réseau entrant et sortant de nombreuses applications de client. Il existe plusieurs fonctionnalités disponibles dans le Service d’application pour répondre à ces besoins. Le défi est de savoir quelle fonctionnalité doit être utilisée pour résoudre un problème donné. Ce document vise à aider les clients à déterminer quelle fonction doit être utilisée en fonction de certains exemples de cas d’utilisation.

Il existe deux types de déploiement principal pour Azure App Service. Il est le service public mutualisé, qui héberge les plans App Service dans le gratuit, partagé, basique, Standard, Premium et Premiumv2 références SKU de tarification. Puis il est le seul locataire App Service environnement (ASE), qui héberge des plans de référence (SKU) App Service isolé directement dans votre réseau virtuel (VNet) Azure. Les fonctionnalités que vous utilisez varie si vous êtes dans le service de l’architecture mutualisé ou dans un environnement ASE. 

## <a name="multi-tenant-app-service-networking-features"></a>Fonctionnalités de mise en réseau mutualisées App Service 

Azure App Service est un système distribué. Les rôles qui gèrent les demandes HTTP/HTTPS entrantes sont appelés serveurs frontaux. Les rôles qui hébergent la charge de travail du client sont appelés des travailleurs. Tous les rôles dans un déploiement d’App Service existent dans un réseau de l’architecture mutualisé. Comme il existe de nombreux clients différents dans la même unité d’échelle App Service, vous ne pouvez pas connecter le réseau de App Service directement à votre réseau. Au lieu de se connecter les réseaux, nous avons besoin de fonctionnalités pour gérer différents aspects de la communication entre applications. Les fonctionnalités qui gèrent les demandes à votre application ne peut pas être utilisées pour résoudre des problèmes lors des appels à partir de votre application. De même, les fonctionnalités qui résolvent des problèmes pour les appels à partir de votre application ne peut pas être utilisées pour résoudre des problèmes à votre application.  

| Fonctionnalités de trafic entrantes | Fonctionnalités sortantes |
|---------------------|-------------------|
| Adresse d’affectées par l’application | les connexions hybrides |
| Restrictions d’accès | Passerelle requis intégration au réseau virtuel |
| Points de terminaison de service | Intégration au réseau virtuel (version préliminaire) |

Sauf indication contraire, toutes les fonctionnalités peuvent être utilisés ensemble. Vous pouvez combiner les fonctionnalités pour résoudre vos problèmes différents.

## <a name="use-case-and-features"></a>Cas d’usage et des fonctionnalités

Pour toute étude de cas, il peut y avoir quelques façons de résoudre le problème.  La fonctionnalité à utiliser est parfois en raison des raisons qui échappent simplement le cas d’usage lui-même. Les cas d’usage entrants suivants montrent l’utilisation des fonctionnalités réseau de App Service pour résoudre des problèmes sur le contrôle du trafic vers votre application. 
 
| Cas d’utilisation de trafic entrant | Fonctionnalité |
|---------------------|-------------------|
| Prise en charge SSL basé sur IP a besoin pour votre application | adresse d’affectées par l’application |
| Non partagé, adresse entrant dédiée pour votre application | adresse d’affectées par l’application |
| Restreindre l’accès à votre application à partir d’un ensemble d’adresses bien définis | Restrictions d’accès |
| Exposer mon application sur des adresses IP privées dans mon réseau virtuel | ASE ILB </br> Passerelle d’application avec les points de terminaison de service |
| Restreindre l’accès à mon application à partir de ressources dans un réseau virtuel | Points de terminaison de service </br> ASE ILB |
| Exposer mon application sur une adresse IP privée dans mon réseau virtuel | ASE ILB </br> adresse IP privée pour entrant sur une passerelle d’Application avec les points de terminaison de service |
| Protéger mon application avec un pare-feu WAF | Passerelle d’application + ASE d’équilibrage de charge interne </br> Passerelle d’application avec les points de terminaison de service </br> Azure porte d’entrée avec les Restrictions d’accès |
| Équilibrer le trafic à mes applications dans différentes régions | Azure porte d’entrée avec les Restrictions d’accès | 
| Équilibrer la charge du trafic dans la même région | Passerelle d’application avec les points de terminaison de service | 

Exemples d’utilisation sortante suggèrent comment utiliser App Service fonctionnalités réseau pour répondre à des besoins de votre application un accès sortant. 

| Cas d’utilisation sortante | Fonctionnalité |
|---------------------|-------------------|
| Accéder aux ressources dans un réseau virtuel Azure dans la même région | Intégration au réseau virtuel </br> ASE |
| Accéder aux ressources dans un réseau virtuel Azure dans une autre région | Passerelle requis intégration au réseau virtuel </br> ASE et homologation |
| Accéder aux ressources sécurisées avec les points de terminaison de service | Intégration au réseau virtuel </br> ASE |
| Accéder aux ressources dans un réseau privé ne pas connecté à Azure | les connexions hybrides |
| Accéder aux ressources via les circuits ExpressRoute | Intégration au réseau virtuel (restreint pour les adresses RFC 1918 pour l’instant) </br> ASE | 


### <a name="default-networking-behavior"></a>Comportement de mise en réseau par défaut

Les unités d’échelle Azure App Service prend en charge de nombreux clients dans chaque déploiement. Les plans gratuit et partagé référence (SKU) hébergent des charges de travail client sur les travailleurs de l’architecture mutualisées. La base et versions ultérieures de charges de travail client de hôte plans qui sont dédiés à un seul plan App Service (ASP). Si vous aviez un plan App Service Standard, toutes les applications dans ce plan seront exécuté sur le même processus de travail. Si vous faites évoluer le processus de travail, toutes les applications dans cet ASP. seront répliquées sur un nouveau traitement pour chaque instance dans vos pages ASP. Les workers qui sont utilisés pour Premiumv2 sont différents des travailleurs utilisés pour les autres plans. Chaque déploiement d’App Service a une adresse IP qui est utilisée pour tout le trafic entrant aux applications dans ce déploiement d’App Service. Il existe toutefois n’importe où à partir de 4 à 11 adresses permet d’effectuer des appels sortants. Ces adresses sont partagées par toutes les applications dans ce déploiement d’App Service. Les adresses sortantes sont différentes selon les types de travail différent. Cela signifie que les adresses utilisées par gratuit, partagé, base, Standard et Premium ASP sont différents que les adresses utilisées pour les appels sortants à partir de l’ASP Premiumv2. Si vous recherchez dans les propriétés de votre application, vous pouvez voir les adresses entrantes et sortantes qui sont utilisés par votre application. Si vous avez besoin verrouiller une dépendance avec une ACL IP, utilisez le possibleOutboundAddresses. 

![Propriétés de l’application](media/networking-features/app-properties.png)

App Service a un nombre de points de terminaison sont utilisés pour gérer le service.  Ces adresses sont publiées dans un document distinct et sont également dans la balise de service AppServiceManagement IP. La balise AppServiceManagement est utilisée uniquement avec un App Service environnement (ASE) où vous devez autoriser ce trafic. Le Service d’application adresses entrants sont suivies dans la balise de service AppService IP. Il n’existe aucune balise de service IP qui contient les adresses sortantes utilisés par App Service. 

![Diagramme de trafic entrant et sortant de App Service](media/networking-features/default-behavior.png)

### <a name="app-assigned-address"></a>Adresse d’affectées par l’application 

La fonctionnalité d’adresse attribuée application est une ramification de la fonctionnalité SSL basé sur IP et est accessible par la configuration de SSL avec votre application. Cette fonctionnalité peut être utilisée pour les appels SSL basé sur IP, mais il peut également servir à donner à votre application à une adresse accessible uniquement. 

![Diagramme de l’adresse d’affectées par l’application](media/networking-features/app-assigned-address.png)

Lorsque vous utilisez une application adresse attribuée, votre trafic traverse toujours les mêmes rôles de serveur frontal qui gèrent tout le trafic entrant dans l’unité d’échelle App Service. L’adresse qui est affecté à votre application, cependant, est uniquement utilisée par votre application. Les cas d’utilisation de cette fonctionnalité sont :

* Prise en charge SSL basé sur IP a besoin pour votre application
* Définir une adresse dédiée pour votre application n’est pas partagée avec un autre élément

Vous pouvez apprendre à définir une adresse sur votre application avec le didacticiel sur [SSL basé sur IP configuration][appassignedaddress]. 

### <a name="access-restrictions"></a>Restrictions d’accès 

Les Restrictions d’accès fonctionnalité vous permet de filtrer **entrant** demandes en fonction de l’adresse IP d’origine. L’action de filtrage a lieu sur les rôles frontaux situés en amont à partir de lancers de travail où vos applications sont en cours d’exécution. Étant donné que les rôles frontaux sont en amont à partir de travailleurs, la fonctionnalité de Restrictions d’accès peut être considérée comme niveau de protection réseau pour vos applications. La fonctionnalité vous permet de générer une liste d’autorisation et refus des blocs d’adresses qui sont évaluées dans l’ordre de priorité. Il est similaire à la fonctionnalité de groupe de sécurité réseau (NSG) qui existe dans la mise en réseau Azure.  Vous pouvez utiliser cette fonctionnalité dans le service de l’architecture mutualisé ou dans un environnement ASE. Lorsqu’il est utilisé avec un ASE ILB, vous pouvez restreindre l’accès à partir de blocs d’adresses privées.

![Restrictions d’accès](media/networking-features/access-restrictions.png)

La fonctionnalité de Restrictions d’accès permet de scénarios dans lesquels vous souhaitez limiter les adresses IP qui peuvent être utilisées pour accéder à votre application. L’utilisation cas pour cette fonctionnalité sont notamment :

* Restreindre l’accès à votre application à partir d’un ensemble d’adresses bien définis 
* Restreindre l’accès à provenant d’un service d’équilibrage de charge, tels que Azure porte d’entrée. Si vous souhaitez verrouiller le trafic entrant vers Azure porte d’entrée, créer des règles pour autoriser le trafic à partir de 147.243.0.0/16 et 2a01:111:2050 :: / 44. 

![Restrictions d’accès avec la porte d’entrée](media/networking-features/access-restrictions-afd.png)

Si vous souhaitez verrouiller l’accès à votre application afin qu’ils peuvent uniquement être accessibles à partir des ressources dans votre réseau virtuel (VNet) Azure, vous avez besoin d’une adresse publique statique sur tout ce que contient votre source de votre réseau virtuel. Si les ressources n’ont pas une adresse publique, vous devez utiliser la fonctionnalité de points de terminaison de Service à la place. Découvrez comment activer cette fonctionnalité avec le didacticiel sur [configuration des Restrictions d’accès][iprestrictions].

### <a name="service-endpoints"></a>Points de terminaison de service

Points de terminaison de service vous permet de verrouiller **entrant** accéder à votre application, telles que l’adresse source doit provenir d’un ensemble de sous-réseaux que vous sélectionnez. Cette fonctionnalité fonctionne conjointement avec les Restrictions d’accès IP. Points de terminaison de service sont définies dans la même expérience utilisateur en tant que les Restrictions d’accès IP. Vous pouvez créer une liste Autoriser ou refuser des règles d’accès qui inclut les adresses publiques, ainsi que des sous-réseaux dans vos réseaux virtuels. Cette fonctionnalité prend en charge des scénarios tels que :

![points de terminaison de service](media/networking-features/service-endpoints.png)

* Configuration d’une passerelle d’Application avec votre application pour verrouiller le trafic entrant à votre application
* Article l’accès à votre application aux ressources de votre réseau virtuel. Cela peut inclure des machines virtuelles, ASE ou même d’autres applications qui utilisent l’intégration au réseau virtuel 

![points de terminaison de service avec application gateway](media/networking-features/service-endpoints-appgw.png)

Vous trouverez plus d’informations sur la configuration des points de terminaison de service avec votre application dans le didacticiel sur [configuration des Restrictions d’accès de point de terminaison de Service][serviceendpoints]
 
### <a name="hybrid-connections"></a>les connexions hybrides

Connexions hybrides App Service permet à vos applications rendre **sortant** appels à des points de terminaison TCP spécifiés. Le point de terminaison peut être en local, dans un réseau virtuel ou n’importe où, qui autorise le trafic sortant vers Azure sur le port 443. La fonctionnalité nécessite l’installation d’un agent de relais appelé le Gestionnaire de connexion hybride (GCH) sur un ordinateur hôte plus récente ou Windows Server 2012. HCM doit être en mesure d’atteindre Azure Relay sur le port 443. Le GCH peut être téléchargé à partir de l’interface de connexions utilisateur App Service hybride dans le portail. 

![Flux de réseau de connexions hybrides](media/networking-features/hybrid-connections.png)

La fonctionnalité Connexions hybrides App Service repose sur la fonctionnalité Connexions hybrides Azure Relay. App Service utilise une forme spécialisée de la fonctionnalité qui prend uniquement en charge les appels sortants à partir de votre application à un hôte TCP et un port. Cet ordinateur hôte et le port suffit résolu sur l’hôte où le HCM est installé. Lors de l’application, dans App Service, effectue une recherche DNS sur l’hôte et le port défini dans votre connexion hybride, le trafic est redirigé automatiquement pour accéder via la connexion hybride et out le Gestionnaire de connexions hybrides. Pour en savoir plus sur les connexions hybrides, lisez la documentation sur [connexions hybrides App Service][hybridconn]

Cette fonctionnalité est couramment utilisée pour :

* Accéder aux ressources dans des réseaux privés qui ne sont pas connectés à Azure avec une connexion VPN ou ExpressRoute
* Prise en charge de lift- and -shift des applications locales vers App Service sans avoir à déplacer également les bases de données prise en charge  
* Fournir en toute sécurité l’accès à un seul hôte et le port par connexion hybride. La plupart des fonctionnalités de mise en réseau pour accéder à un réseau et avec les connexions hybrides vous ne le seul hôte et le port que vous pouvez le contacter.
* Couvrent des scénarios non couverts par d’autres méthodes de connectivité sortante
* Le développement s’effectue dans App Service où les applications peuvent facilement tirer parti des ressources locales 

Étant donné que la fonctionnalité permet d’accéder aux ressources locales sans un trou de pare-feu de trafic entrant, il est populaire auprès des développeurs. Les autres sortantes fonctionnalités App Service mise en réseau sont très virtuel mise en réseau Azure liées. Connexions hybrides n’a pas de dépendance sur passer par un réseau virtuel et peut être utilisé pour une plus grande variété de besoins en matière de mise en réseau. Il est important de noter que la fonctionnalité Connexions hybrides App Service ne pas en charge ou savoir ce que vous faites sur celui-ci. Qui est à dire que vous pouvez l’utiliser pour accéder à une base de données, un service web ou un socket TCP arbitraire sur un macroordinateur. La fonctionnalité de tunnels essentiellement des paquets TCP. 

Bien que les connexions hybrides est populaire pour le développement, il est également utilisé dans nombreuses applications de production également. Il est très utile pour accéder à un service web ou une base de données, mais n’est pas appropriée pour les situations impliquant un nombre de connexions excellent en cours de création. 

### <a name="gateway-required-vnet-integration"></a>Passerelle requis intégration au réseau virtuel 

La passerelle requis la fonctionnalité d’intégration de réseau virtuel de App Service permet à votre application se **sortant** demandes dans un réseau virtuel Azure. La fonctionnalité fonctionne en vous connectant à l’hôte de que votre application s’exécute sur une passerelle de réseau virtuel sur votre réseau virtuel avec une connexion de point-to-site VPN. Lorsque vous configurez la fonctionnalité, votre application obtient une des adresses de point-to-site affectés à chaque instance. Cette fonctionnalité vous permet d’accéder aux ressources classiques ou VNets Resource Manager dans n’importe quelle région. 

![Passerelle requis intégration au réseau virtuel](media/networking-features/gw-vnet-integration.png)

Cette fonctionnalité résout le problème de l’accès aux ressources dans d’autres réseaux virtuels et peut même être utilisée pour se connecter via un réseau virtuel à d’autres réseaux virtuels ou même localement. Il ne fonctionne pas avec ExpressRoute connecté réseaux connectés de réseaux virtuels mais pas avec VPN de Site à site. Il est normalement inapproprié utiliser cette fonctionnalité à partir d’une application dans un environnement de Service d’application (ASE), car l’ASE est déjà dans votre réseau virtuel. Cas d’usage qui résout cette fonctionnalité sont :

* L’accès aux ressources sur les adresses IP privées dans vos réseaux virtuels Azure 
* L’accès aux ressources sur site s’il existe un VPN de site à site 
* L’accès aux ressources dans des réseaux virtuels homologués 

Lorsque cette fonctionnalité est activée, votre application utilisera le serveur DNS configuré avec le réseau virtuel de destination. Vous trouverez plus d’informations sur cette fonctionnalité dans la documentation sur [intégration au réseau virtuel de Service application][vnetintegrationp2s]. 

### <a name="vnet-integration"></a>Intégration au réseau virtuel

La passerelle requis la fonctionnalité d’intégration de réseau virtuel est très utile, mais toujours ne résout pas l’accès aux ressources sur ExpressRoute. En plus de devoir atteindre via les connexions ExpressRoute, il est nécessaire pour les applications pouvoir effectuer des appels au point de terminaison de service sécurisé des services. Pour résoudre les deux de ces besoins supplémentaires, une autre fonctionnalité d’intégration au réseau virtuel a été ajoutée. La nouvelle fonctionnalité d’intégration au réseau virtuel vous permet de placer le serveur principal de votre application dans un sous-réseau dans un VNet Resource Manager dans la même région. Cette fonctionnalité n’est pas disponible à partir d’un environnement App Service, qui se trouve déjà dans un réseau virtuel. Cette fonctionnalité active :

* L’accès aux ressources dans VNets Resource Manager dans la même région
* L’accès aux ressources qui sont sécurisés avec des points de terminaison de service 
* L’accès aux ressources qui sont accessibles via des connexions ExpressRoute ou VPN

![Intégration au réseau virtuel](media/networking-features/vnet-integration.png)

Cette fonctionnalité est disponible en version préliminaire et ne doit pas être utilisée pour les charges de travail de production. Pour en savoir plus sur cette fonctionnalité, lisez la documentation sur [intégration au réseau virtuel de Service application][vnetintegration].

## <a name="app-service-environment"></a>Environnement App Service 

Un environnement de Service d’application (ASE) est un déploiement de client unique d’Azure App Service qui s’exécute dans votre réseau virtuel. L’environnement app service permet de cas d’utilisation tels que :

* Accéder aux ressources dans votre réseau virtuel
* Accéder aux ressources sur ExpressRoute
* Exposer vos applications avec une adresse privée dans votre réseau virtuel 
* Accéder aux ressources entre les points de terminaison de service 

Avec un environnement ASE, vous n’avez pas besoin d’utiliser des fonctionnalités telles que l’intégration au réseau virtuel ou des points de terminaison de service, car l’ASE est déjà dans votre réseau virtuel. Si vous souhaitez accéder aux ressources telles que SQL ou de stockage sur les points de terminaison de service, activez les points de terminaison de service sur le sous-réseau ASE. Si vous souhaitez accéder aux ressources dans le réseau virtuel, aucune configuration supplémentaire n’est requis.  Si vous souhaitez accéder aux ressources sur ExpressRoute, vous êtes déjà dans le réseau virtuel et n’avez pas besoin de configurer quoi que ce soit sur l’ASE ou les applications qu’il contient. 

Étant donné que les applications dans un environnement ASE peuvent être exposées sur une adresse IP privée, vous pouvez facilement ajouter des appareils WAF pour exposer les applications que vous souhaitez à internet et sécurisez le reste. Il se prête à facilitent le développement d’applications multiniveaux. 

Voici quelques éléments qui qui n’est pas encore possible à partir du service de l’architecture mutualisé qui proviennent d’un environnement app service. Ceux incluent des éléments tels que :

* Exposer vos applications sur une adresse IP privée
* Sécuriser le trafic sortant avec les contrôles de réseau qui ne font pas partie de votre application 
* Héberger vos applications dans un service de client unique 
* Monter en puissance au nombre d’instances plus sont possibles dans le service mutualisé 
* Charger des certificats de client autorité de certification privées pour une utilisation par vos applications avec l’autorité de certification privée sécurisée des points de terminaison 
* Forcer le TLS 1.1 sur toutes les applications hébergées dans le système sans possibilité des désactiver au niveau de l’application 
* Fournir une adresse sortante dédiée pour toutes les applications dans votre environnement ASE qui n’est pas partagé avec tous les clients 

![ASE dans un réseau virtuel](media/networking-features/app-service-environment.png)

L’environnement app service fournit la meilleure option requise pour l’hébergement d’application isolée et dédiée, mais n’est fourni avec des défis de gestion. Quelques points à prendre en compte avant d’utiliser un ASE opérationnels sont :
 
 * Un environnement app service s’exécute au sein de votre réseau virtuel mais a des dépendances en dehors du réseau virtuel. Ces dépendances doivent être autorisés. En savoir plus dans [considérations relatives à la mise en réseau pour un environnement App Service][networkinfo]
 * Un ASE n’évolue pas immédiatement telles que le service de l’architecture mutualisé. Vous devez anticiper les besoins de mise à l’échelle, plutôt que de manière réactive mise à l’échelle. 
 * Un ASE a une plus élevée en amont coût associé. Afin d’obtenir le meilleur parti de votre ASE, vous devez planifier sur le placement de nombreuses charges de travail dans un environnement app service plutôt qu’est utilisé pour les petites efforts ont
 * Les applications dans un environnement ASE ne peuvent pas restreindre l’accès à certaines applications dans un environnement app service et d’autres non.
 * L’ASE se trouve dans un sous-réseau et les règles de mise en réseau s’appliquent à tout le trafic vers et à partir de cet environnement ASE. Si vous souhaitez affecter des règles de trafic entrant pour qu’une seule application, utilisez des Restrictions d’accès. 

## <a name="combining-features"></a>Combinaison de fonctionnalités 

Les fonctionnalités indiquées pour le service de l’architecture mutualisé peuvent être utilisées ensemble pour résoudre les cas d’usage plus élaborés. Deux des cas d’usage plus courants sont décrits ici, mais ils servent uniquement d’exemples. En comprenant comment les différentes fonctionnalités, vous pouvez résoudre presque tous les besoins de votre architecture système.

### <a name="inject-app-into-a-vnet"></a>Injecter des applications dans un réseau virtuel

Une demande courante est sur la manière de mettre votre application dans un réseau virtuel. Placement de votre application dans un réseau virtuel signifie que les points de terminaison entrants et sortants pour une application au sein d’un réseau virtuel. L’ASE fournit la meilleure solution pour résoudre ce problème, mais vous pouvez obtenir la majeure partie de ce qui est nécessaire avec dans le service de l’architecture mutualisé en combinant des fonctionnalités. Par exemple, vous pouvez héberger des applications intranet uniquement avec des adresses privées de trafic entrants et sortants par :

* Création d’une passerelle d’Application avec des adresses privées de trafic entrant et sortant
* Sécurisation du trafic entrant vers votre application avec les points de terminaison de service 
* Utiliser la nouvelle intégration de réseau virtuel pour le serveur principal de votre application est dans votre réseau virtuel 

Ce style de déploiement ne serait pas vous donner une adresse dédiée pour le trafic sortant à internet ou vous donnent la possibilité de verrouiller tout le trafic sortant à partir de votre application.  Ce style de déploiement permet d’obtenir une plus grande partie de ce que vous obtiendriez uniquement sinon avec un ASE. 

### <a name="create-multi-tier-applications"></a>Créer des applications à plusieurs niveaux

Une application multiniveau est une application où les applications de serveur principal d’API sont uniquement accessible à partir du niveau frontal. Pour créer une application multiniveau, vous pouvez :

* Utiliser l’intégration de réseau virtuel pour connecter le serveur principal de votre application web frontale avec un sous-réseau dans un réseau virtuel
* Utiliser des points de terminaison de service pour sécuriser le trafic entrant vers votre application API à uniquement en provenance du sous-réseau utilisé par votre application web frontale

![application multiniveau](media/networking-features/multi-tier-app.png)

Vous pouvez avoir plusieurs applications front-end d’utiliser la même application API à l’aide d’intégration au réseau virtuel à partir d’autres applications frontales et des points de terminaison de service à partir de l’application API avec leurs sous-réseaux.  

<!--Links-->
[appassignedaddress]: https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-ssl#bind-your-ssl-certificate
[iprestrictions]: https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions
[serviceendpoints]: https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions
[hybridconn]: https://docs.microsoft.com/azure/app-service/app-service-hybrid-connections
[vnetintegrationp2s]: https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet
[vnetintegration]: https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet
[networkinfo]: https://docs.microsoft.com/azure/app-service/environment/network-info
