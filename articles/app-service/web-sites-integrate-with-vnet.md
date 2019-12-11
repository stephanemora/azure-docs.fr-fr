---
title: Intégrer une application au réseau virtuel Microsoft Azure
description: Découvrez comment Azure App Service s’intègre au réseau virtuel Microsoft Azure et comment connecter une application à un réseau virtuel.
author: ccompy
ms.assetid: 90bc6ec6-133d-4d87-a867-fcf77da75f5a
ms.topic: article
ms.date: 08/21/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 71dc37fc000b2f195478e06f7e755fa8df926444
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/02/2019
ms.locfileid: "74688286"
---
# <a name="integrate-your-app-with-an-azure-virtual-network"></a>Intégrer une application à un réseau Azure Virtual Network
Ce document décrit la fonctionnalité d’intégration au réseau virtuel d’Azure App Service et explique comment la configurer avec des applications dans [Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714). Les [réseaux virtuels Azure][VNETOverview] vous permettent de placer un grand nombre de vos ressources Azure dans un réseau routable non-Internet.  

Azure App Service a deux variantes. 

1. Les systèmes multilocataires qui prennent en charge l’ensemble des plans de tarification, excepté « Isolé »
2. L’environnement ASE (App Service Environment), qui se déploie dans votre réseau virtuel et prend en charge les applications à plan tarifaire Isolé.

Ce document passe en revue les deux fonctionnalités d’intégration au réseau virtuel, qui sont destinées à être utilisées dans le service App Service multilocataire. Si votre application se trouve dans [Azure App Service Environment][ASEintro], elle est déjà dans un réseau virtuel et ne nécessite pas l’utilisation de la fonctionnalité d’intégration au réseau virtuel pour accéder aux ressources du même réseau virtuel. Pour plus d’informations sur toutes les fonctionnalités de mise en réseau App Service, consultez [Fonctionnalités de mise en réseau App Service](networking-features.md).

La fonctionnalité d’intégration au réseau virtuel se présente sous deux formes :

1. La première version permet une intégration aux réseaux virtuels d’une même région. Sous cette forme, la fonctionnalité nécessite un sous-réseau dans un réseau virtuel de la même région. Bien que cette fonctionnalité soit toujours en préversion, elle est prise en charge pour les charges de travail de production d’applications Windows. Il existe cependant certaines restrictions qui sont mentionnées ci-dessous.
2. L’autre version permet une intégration à des réseaux virtuels d’autres régions ou à des réseaux virtuels classiques. Cette version de la fonctionnalité nécessite le déploiement d'une passerelle de réseau virtuel dans votre réseau virtuel. Il s’agit de la fonctionnalité VPN point à site et elle est uniquement prise en charge par les applications Windows.

Une application ne peut utiliser qu'une seule forme de la fonctionnalité d'intégration au réseau virtuel à la fois. Il convient donc de déterminer quelle fonctionnalité utiliser. Les deux peuvent être utilisées dans des cas divers et variés. Il existe néanmoins des facteurs de différenciation clairs :

| Problème  | Solution | 
|----------|----------|
| Nécessité d'accéder à une adresse RFC 1918 (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16) dans la même région | Intégration au réseau virtuel régional |
| Nécessité d’accéder aux ressources d’un réseau virtuel classique ou d’un réseau virtuel situé dans une autre région | Intégration au réseau virtuel avec passerelle obligatoire |
| Nécessité d'accéder à des points de terminaison RFC 1918 via ExpressRoute | Intégration au réseau virtuel régional |
| Nécessité d'accéder à des ressources via des points de terminaison de service | Intégration au réseau virtuel régional |

Aucune des fonctionnalités ne vous permet d'accéder à des adresses non compatibles avec RFC 1918 via ExpressRoute. Pour cela, vous devez pour le moment utiliser un environnement ASE.

L'utilisation de l'intégration au réseau virtuel régional n'a pas pour effet de connecter votre réseau virtuel à des ressources locales ou de configurer des points de terminaison de service. Il s'agit d'une configuration de mise en réseau distincte. L’intégration au réseau virtuel régional permet simplement à votre application d’effectuer des appels via ces types de connexion.

Quelle que soit la version utilisée, l’intégration au réseau virtuel permet à votre application web d’accéder aux ressources de votre réseau virtuel, mais n’accorde pas d’accès privé entrant à votre application web à partir du réseau virtuel. L’accès au site privé fait référence au fait de rendre votre application accessible uniquement à partir d’un réseau privé, par exemple à partir d’un réseau virtuel Azure. L'intégration au réseau virtuel sert uniquement à passer des appels sortants de votre application vers votre réseau virtuel. 

La fonctionnalité d’intégration au réseau virtuel :

* nécessite un plan de tarification Standard, Premium ou PremiumV2 
* prend en charge les protocoles TCP et UDP ;
* fonctionne avec les applications App Service et les applications de fonction

L’intégration au réseau virtuel ne prend pas en charge certaines choses, notamment :

* montage d’un lecteur ;
* intégration AD ; 
* NetBios ;

## <a name="regional-vnet-integration"></a>Intégration au réseau virtuel régional 

> [!NOTE]
> Le peering n’est pas encore disponible pour App Service pour Linux.
>

Quand l’intégration au réseau virtuel est utilisée avec des réseaux virtuels de la même région que votre application, il est nécessaire d’utiliser un sous-réseau délégué avec au moins 32 adresses. Le sous-réseau ne peut pas être utilisé à d’autres fins. Les appels sortants effectués à partir de votre application le seront à partir des adresses contenues dans le sous-réseau délégué. Quand vous utilisez cette version de l’intégration au réseau virtuel, les appels sont effectués à partir des adresses contenues dans votre réseau virtuel. Voici ce que peut faire votre application en utilisant les adresses de votre réseau virtuel :

* Effectuer des appels aux services sécurisés de point de terminaison de service
* Accéder à des ressources via des connexions ExpressRoute
* Accéder aux ressources du réseau virtuel auquel vous êtes connecté
* Accéder à des ressources via des connexions appairées incluant des connexions ExpressRoute

Bien que cette fonctionnalité soit disponible en préversion, elle est prise en charge pour les charges de travail de production d’applications Windows avec les limitations suivantes :

* Vous pouvez seulement accédez aux adresses qui se trouvent dans la plage RFC 1918. Il s’agit des adresses qui se trouvent dans les blocs d’adresses 10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16.
* Vous ne pouvez pas accéder à des ressources via des connexions de peering mondial.
* Vous ne pouvez pas définir d’itinéraires pour le trafic en provenance de votre application et à destination de votre réseau virtuel.
* La fonctionnalité n’est disponible qu’à partir des unités d’échelle App Service récentes qui prennent en charge les plans App Service PremiumV2.
* Le sous-réseau d’intégration ne peut être utilisé que par un seul plan App Service
* La fonctionnalité ne peut pas être utilisée par des applications de plan Isolé qui se trouvent dans un environnement ASE.
* La fonctionnalité nécessite un sous-réseau inutilisé /27 avec 32 adresses ou d’une taille supérieure dans votre réseau virtuel Resource Manager.
* L’application et le réseau virtuel doivent être dans la même région.
* Vous ne pouvez pas supprimer un réseau virtuel avec une application intégrée. Vous devez d’abord supprimer l’intégration. 
* Vous ne pouvez disposer que d’une seule intégration au réseau virtuel régional par plan App Service. Plusieurs applications d’un même plan App Service peuvent utiliser le même réseau virtuel. 
* Vous ne pouvez pas modifier l'abonnement d'une application ou d'un plan App Service lorsqu'une application utilise l'intégration au réseau virtuel régional.

Une adresse est utilisée pour chaque instance du plan App Service. Si vous avez défini l'échelle de votre application sur 5 instances, 5 adresses sont utilisées. Étant donné que la taille du sous-réseau ne peut pas être modifiée après l’affectation, vous devez utiliser un sous-réseau suffisamment grand pour s’adapter à la taille que votre application est susceptible d’atteindre. Une taille de /26 avec 64 adresses est recommandée. Une taille de /27 avec 32 adresses prendra en charge un plan App Service Premium de 20 instances si vous n'avez pas modifié la taille du plan App Service. Lorsque vous modifiez un plan App Service à la hausse ou à la baisse, vous avez brièvement besoin de deux fois plus d'adresses. 

Si vous souhaitez que vos applications d’un autre plan App Service atteignent un réseau virtuel auquel sont déjà connectées des applications d’un autre plan App Service, vous devez sélectionner un sous-réseau différent de celui utilisé par l’intégration VNet préexistante.  

La fonctionnalité est en préversion aussi pour Linux. Pour utiliser la fonctionnalité d’intégration au réseau virtuel avec un réseau virtuel Resource Manager dans une même région :

1. Accédez à l’interface utilisateur Réseau dans le portail. La présence de l’option Ajouter un réseau virtuel (préversion) est le signe que votre application peut utiliser la nouvelle fonctionnalité.  

   ![Sélectionner l’intégration au réseau virtuel][6]

1. Sélectionnez **Ajouter un réseau virtuel (préversion)** .  

1. Sélectionnez le réseau virtuel Resource Manager auquel vous voulez vous intégrer, puis créez un sous-réseau ou choisissez un sous-réseau vide préexistant. L’intégration prend moins d’une minute. Lors de l’intégration, votre application est redémarrée.  Une fois l’intégration terminée, vous voyez des détails sur le réseau virtuel auquel vous êtes intégré et une bannière en haut qui vous indique que la fonctionnalité est en préversion.

   ![Sélectionner le réseau virtuel et le sous-réseau][7]

Une fois que votre application est intégrée à votre réseau virtuel, elle utilise le serveur DNS avec lequel votre réseau virtuel est configuré. 

L'intégration au réseau virtuel régional requiert que votre sous-réseau d'intégration soit délégué à Microsoft.Web.  L'interface utilisateur de l'intégration au réseau virtuel délègue automatiquement le sous-réseau à Microsoft.Web. Si votre compte ne dispose pas des autorisations réseau suffisantes, vous devez demander à une personne autorisée de configurer les attributs de votre sous-réseau d'intégration de manière à déléguer celui-ci. Pour déléguer manuellement le sous-réseau d'intégration, accédez à l'interface utilisateur du sous-réseau du service Réseau virtuel Azure et définissez la délégation sur Microsoft.Web.

Pour déconnecter votre application du réseau virtuel, sélectionnez **Déconnecter**. Ceci redémarre votre application web. 


#### <a name="web-app-for-containers"></a>Web App pour conteneurs

Si vous utilisez App Service sur Linux avec les images intégrées, la fonctionnalité d’intégration au réseau virtuel régional fonctionne sans modifications supplémentaires. Si vous utilisez Web App pour conteneurs, vous devez modifier votre image docker pour pouvoir utiliser l’intégration au réseau virtuel. Dans votre image docker, utilisez la variable d’environnement PORT comme port d’écoute du serveur web principal, au lieu d’utiliser un numéro de port codé en dur. La variable d’environnement PORT est automatiquement définie par la plateforme App Service au démarrage du conteneur. Si vous utilisez SSH, le démon SSH doit être configuré pour écouter sur le numéro de port spécifié par la variable d’environnement SSH_PORT lors de l’utilisation de l’intégration au réseau virtuel régional.

### <a name="service-endpoints"></a>Points de terminaison de service

La nouvelle fonctionnalité d’intégration au réseau virtuel vous permet d’utiliser des points de terminaison de service.  Pour utiliser des points de terminaison de service avec votre application, utilisez la nouvelle intégration au réseau virtuel pour vous connecter à un réseau virtuel sélectionné, puis configurez des points de terminaison de service sur le sous-réseau que vous avez utilisé pour l’intégration. 


### <a name="how-vnet-integration-works"></a>Fonctionnement de l’intégration au réseau virtuel

Les applications contenues dans App Service sont hébergées dans des rôles de travail. Les plans tarifaires de base et supérieurs sont des plans d’hébergement dédiés dans lesquels aucune autre charge de travail de client ne s’exécute sur les mêmes Workers. L’intégration au réseau virtuel opère en montant des interfaces virtuelles avec des adresses du sous-réseau délégué. Comme l’adresse de départ se trouve dans votre réseau virtuel, elle a accès à la plupart des éléments contenus dans votre réseau virtuel ou accessibles par celui-ci, comme le ferait une machine virtuelle dans votre réseau virtuel. L’implémentation de la mise en réseau étant différente de l’exécution d’une machine virtuelle dans votre réseau virtuel, certaines fonctionnalités de mise en réseau ne sont pas encore disponibles quand cette fonctionnalité est utilisée.

![Intégration au réseau virtuel](media/web-sites-integrate-with-vnet/vnet-integration.png)

Quand l’intégration au réseau virtuel est activée, votre application continue de passer des appels sortants vers Internet en empruntant les mêmes canaux que d’habitude. Les adresses sortantes figurant sur le portail des propriétés de l’application sont toujours les adresses qu’utilise votre application. Ce qui change pour votre application, c’est que les appels aux services sécurisés de point de terminaison de service ou aux adresses RFC 1918 accèdent à votre réseau virtuel. 

La fonctionnalité ne prend en charge qu’une seule interface virtuelle par Worker.  Une interface virtuelle par Worker signifie une intégration au réseau virtuel régional par plan App Service. Toutes les applications relevant d’un même plan App Service peuvent utiliser la même intégration au réseau virtuel, mais si vous avez besoin qu’une application se connecte à un autre réseau virtuel, vous devez créer un autre plan App Service. L’interface virtuelle utilisée n’est pas une ressource à laquelle les clients peuvent accéder directement.

Compte tenu du mode de fonctionnement de cette technologie, le trafic utilisé avec l’intégration au réseau virtuel n’apparaît pas dans les journaux de flux du groupe de sécurité réseau (NSG) ou de Network Watcher.  

## <a name="gateway-required-vnet-integration"></a>Intégration au réseau virtuel avec passerelle obligatoire 

La fonctionnalité d’intégration au réseau virtuel avec passerelle obligatoire :

* Permet de se connecter à des réseaux virtuels de n’importe quelle région, qu’il s’agisse de réseaux virtuels Resource Manager ou classiques.
* Permet à une application de se connecter à un seul réseau virtuel à la fois.
* Permet d’intégrer jusqu’à cinq réseaux virtuels dans un plan App Service. 
* Permet à un même réseau virtuel d’être utilisé par plusieurs applications dans le cadre d’un plan App Service sans impact sur le nombre total d’applications pouvant être utilisées par un plan App Service.  Si vous avez 6 applications qui utilisent le même réseau virtuel dans le cadre d’un plan App Service, cela compte pour un réseau virtuel utilisé. 
* Nécessite une passerelle de réseau virtuel configurée avec le VPN de point à site.
* Prend en charge un contrat SLA de 99,9 % du fait du contrat SLA de la passerelle.

Ce que cette fonctionnalité ne prend pas en charge :
* Utilisation avec des applications Linux
* Accès aux ressources via ExpressRoute 
* Accès aux ressources via des points de terminaison de service 

### <a name="getting-started"></a>Prise en main

Voici quelques informations à garder à l’esprit avant de connecter votre application web à un réseau virtuel.

* Un réseau virtuel cible doit prendre en charge la connexion VPN de point à site avec une passerelle de routage avant de pouvoir être connecté à une application. 
* Le réseau virtuel doit faire partie du même abonnement que votre plan App Service (ASP, App Service Plan).
* Les applications intégrées à un réseau virtuel utilisent le serveur DNS spécifié pour ce réseau virtuel.

### <a name="set-up-a-gateway-in-your-vnet"></a>Configurer une passerelle de votre réseau virtuel ###

Si vous avez déjà une passerelle configurée avec les adresses de point à site, vous pouvez passer à la configuration de l’intégration au réseau virtuel avec votre application.  
Pour créer une passerelle :

1. [Créez un sous-réseau de passerelle][creategatewaysubnet] dans votre réseau virtuel.  

1. [Créez la passerelle VPN][creategateway]. Sélectionnez un type de VPN basé sur les routes.

1. [Définissez les adresses de point à site][setp2saddresses]. Si la passerelle n’est pas dans la référence (SKU) de base, c’est que IKEV2 doit être désactivé dans la configuration de point à site et que SSTP doit être sélectionné. L’espace d’adressage doit se situer dans les blocs d’adresses RFC 1918, à savoir 10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16.

Si vous créez simplement la passerelle pour l’utiliser avec l’intégration au réseau virtuel App Service, il n’est pas nécessaire de charger un certificat. La création de la passerelle peut prendre 30 minutes. Vous ne serez pas en mesure d’intégrer votre application à votre réseau virtuel tant que la passerelle n’est pas provisionnée. 

### <a name="configure-vnet-integration-with-your-app"></a>Configurer l’intégration au réseau virtuel à votre application 

Pour activer l’intégration au réseau virtuel sur votre application : 

1. Accédez à votre application dans le portail Azure, ouvrez les paramètres de l’application et sélectionnez Réseau > Intégration au réseau virtuel. Votre ASP doit être présent dans une référence SKU Standard ou supérieure pour utiliser l’une ou l’autre des fonctionnalités d’intégration au réseau virtuel. 
 ![Interface utilisateur de l’intégration au réseau virtuel][1]

1. Sélectionnez **Ajouter un réseau virtuel**. 
 ![Ajouter une intégration au réseau virtuel][2]

1. Sélectionnez votre réseau virtuel. 
  ![Sélectionner votre réseau virtuel][8]
  
Votre application est redémarrée après cette dernière étape.  

### <a name="how-the-gateway-required-vnet-integration-feature-works"></a>Mode opératoire de la fonctionnalité d’intégration au réseau virtuel avec passerelle obligatoire

La fonctionnalité d’intégration au réseau virtuel avec passerelle obligatoire s’appuie sur la technologie VPN de point à site. La technologie de point à site limite l’accès réseau à la seule machine virtuelle hébergeant l’application. Les applications sont limitées au seul envoi du trafic vers Internet, via des connexions hybrides ou via l’intégration au réseau virtuel. 

![Fonctionnement de l’intégration au réseau virtuel][3]

## <a name="managing-vnet-integration"></a>Gestion de l’intégration au réseau virtuel
La possibilité de se connecter à un réseau virtuel et de s’en déconnecter se situe au niveau de l’application. Les opérations qui peuvent affecter l’intégration au réseau virtuel entre plusieurs applications s’effectuent au niveau du plan App Service. Sur le portail, sous application > Mise en réseau > Intégration de réseau virtuel, vous pouvez obtenir des détails sur votre réseau virtuel. Des informations similaires sont visibles sur le portail au niveau de l’ASP via ASP > Mise en réseau > Intégration de réseau virtuel, avec notamment la possibilité d’identifier les applications du plan App Service en question qui utilisent une intégration donnée.

 ![Détails du réseau virtuel][4]

Les informations auxquelles vous avez accès dans l’interface utilisateur de l’intégration au réseau virtuel sont les mêmes d’un portail à l’autre (applications et ASP).

* Nom du réseau virtuel : liens vers l’interface utilisateur du réseau virtuel
* Emplacement : reflète l’emplacement de votre réseau virtuel. L’intégration à un réseau virtuel dans un autre emplacement peut entraîner des problèmes de latence pour votre application. 
* État du certificat : indique si vos certificats sont synchronisés entre votre plan App Service et votre réseau virtuel.
* État de la passerelle : si vous utilisez l’intégration au réseau virtuel avec passerelle obligatoire, vous pouvez voir l’état de la passerelle.
* Espace d’adressage du réseau virtuel : montre l’espace d’adressage IP pour votre réseau virtuel. 
* Espace d’adressage de point à site : montre l’espace d’adressage IP de point à site de votre réseau virtuel. Quand vous effectuez des appels dans votre réseau virtuel en utilisant la fonctionnalité avec passerelle obligatoire, l’adresse FROM de votre application est une de ces adresses. 
* Espace d’adressage de site à site : vous pouvez utiliser des réseaux virtuels de site à site pour connecter votre réseau virtuel à vos ressources locales ou à un autre réseau virtuel. Les plages d’adresses IP définies avec cette connexion VPN sont montrées ici.
* Serveurs DNS : montre les serveurs DNS configurés avec votre réseau virtuel.
* Adresses IP routées vers le réseau virtuel : montre les blocs d’adresses routées utilisées pour piloter le trafic dans votre réseau virtuel 

La seule opération que vous pouvez effectuer dans la vue d’application de votre interface d’intégration au réseau virtuel consiste à déconnecter votre application du réseau virtuel auquel elle est actuellement connectée. Pour déconnecter votre application d’un réseau virtuel, sélectionnez **Déconnecter**. Votre application est redémarrée quand vous vous déconnectez d’un réseau virtuel. La déconnexion ne change pas votre réseau virtuel. Le sous-réseau ou la passerelle ne sont pas supprimés. Donc, si vous souhaitez supprimer votre réseau virtuel, vous devez d’abord déconnecter votre application du réseau virtuel puis supprimer les ressources qu’il contient comme les passerelles. 

Pour atteindre l’interface utilisateur de l’intégration au réseau virtuel de votre plan App Service (ASP), ouvrez votre interface utilisateur ASP et sélectionnez **Réseau**.  Sous Intégration au réseau virtuel, sélectionnez **Cliquez ici pour configurer** pour ouvrir l’interface utilisateur de l’état de la fonctionnalité réseau.

![Informations sur l’intégration au réseau virtuel ASP][5]

L’interface utilisateur de l’intégration au réseau virtuel ASP vous montre tous les réseaux virtuels qui sont utilisés par les applications dans votre ASP. Pour voir des détails supplémentaires sur chaque réseau virtuel, cliquez sur le réseau virtuel qui vous intéresse. Vous pouvez effectuer ici deux actions.

* **Synchroniser le réseau** L’opération de synchronisation du réseau s’adresse uniquement à la fonctionnalité d’intégration au réseau virtuel dépendante de la passerelle. L’exécution d’une opération de synchronisation du réseau est l’assurance que vos certificats et informations réseau sont synchronisés. Si vous ajoutez ou que vous changez le DNS du réseau virtuel, vous devez effectuer une opération **Synchroniser le réseau**. Cette opération redémarre toutes les applications utilisant ce réseau virtuel.
* **Ajouter des routes** : l’ajout de routes pilote le trafic sortant dans votre réseau virtuel.

**Routage** : les routes définies dans votre réseau virtuel sont utilisées pour diriger le trafic dans votre réseau virtuel à partir de votre application. Si vous devez envoyer du trafic sortant supplémentaire dans le réseau virtuel, vous pouvez ajouter ces blocs d’adresses ici. Cette fonctionnalité n’opère qu’avec l’intégration au réseau virtuel avec passerelle obligatoire.

**Certificats** : quand l’intégration au réseau virtuel avec passerelle obligatoire est activée, un échange de certificats est nécessaire pour garantir la sécurité de la connexion. En plus des certificats, la configuration DNS, les routes et d’autres éléments similaires décrivent le réseau.
Si des certificats ou des informations du réseau sont modifiés, vous devez cliquer sur « Synchroniser le réseau ». Quand vous cliquez sur « Synchroniser le réseau », la connectivité entre votre application et votre réseau virtuel est brièvement interrompue. Votre application n’est pas redémarrée, et la perte de connectivité peut altérer le fonctionnement correct de votre site. 

## <a name="accessing-on-premises-resources"></a>Accès aux ressources sur site
Les applications peuvent accéder aux ressources locales en s’intégrant à des réseaux virtuels qui ont des connexions site à site. Si vous utilisez l’intégration au réseau virtuel avec passerelle obligatoire, vous devez mettre à jour les routes de votre passerelle VPN locale avec vos blocs d’adresses de point à site. Lors de la configuration initiale de ce VPN site à site, les scripts utilisés pour le configurer doivent définir les routes correctement. Si vous ajoutez des adresses de point à site après avoir créé votre VPN de site à site, vous devez mettre à jour les routes manuellement. La procédure détaillée dépend de la passerelle et n’est pas décrite ici. Vous ne pouvez pas configurer BGP avec une connexion VPN de site à site.

Aucune configuration supplémentaire n’est nécessaire pour permettre à la fonctionnalité d’intégration au réseau virtuel régional d’accéder à votre réseau virtuel et en local. Vous devez simplement connecter votre réseau virtuel en local à l’aide d’ExpressRoute ou d’un VPN de site à site. 

> [!NOTE]
> La fonctionnalité d’intégration au réseau virtuel avec passerelle obligatoire n’intègre pas une application à un réseau virtuel doté d’une passerelle ExpressRoute. Même si la passerelle ExpressRoute est configurée en [mode de coexistence][VPNERCoex], l’intégration au réseau virtuel ne fonctionne pas. Si vous avez besoin d’accéder à des ressources via une connexion ExpressRoute, vous pouvez utiliser la fonctionnalité d’intégration au réseau virtuel régional ou un [environnement ASE (App Service Environment)][ASE], qui s’exécute dans votre réseau virtuel. 
> 
> 

## <a name="peering"></a>Peering
Si vous utilisez le peering avec l’intégration au réseau virtuel régional, aucune configuration supplémentaire n’est nécessaire. 

Si vous utilisez l’intégration au réseau virtuel avec passerelle obligatoire ainsi que le peering, vous devez configurer quelques éléments supplémentaires. Pour configurer le peering afin qu’il fonctionne avec votre application :

1. Ajoutez une connexion de peering sur le réseau virtuel auquel votre application se connecte. Lors de l’ajout de la connexion de peering, activez **Autoriser l’accès au réseau virtuel** et cochez **Autoriser le trafic transféré** et **Autoriser le transit par passerelle**.
1. Ajoutez une connexion de peering sur le réseau virtuel à appairer au réseau virtuel auquel vous êtes connecté. Lors de l’ajout de la connexion de peering sur le réseau virtuel de destination, activez **Autoriser l’accès au réseau virtuel** et cochez **Autoriser le trafic transféré** et **Autoriser les passerelles distantes**.
1. Accédez au plan App Service > Réseau > Interface utilisateur de l’intégration au réseau virtuel dans le portail.  Sélectionnez le réseau virtuel auquel votre application se connecte. Dans la section Routage, ajoutez la plage d’adresses du réseau virtuel qui est appairé au réseau virtuel auquel votre application est connectée.  


## <a name="pricing-details"></a>Détails de la tarification
L’utilisation de la fonctionnalité d’intégration au réseau virtuel régional ne s’accompagne d’aucuns frais supplémentaires, au-delà des frais liés au niveau tarifaires ASP.

L’utilisation de la fonctionnalité d’intégration au réseau virtuel avec passerelle obligatoire est associée à trois types de coûts :

* Frais liés au niveau tarifaire ASP : vos applications doivent relever d’un plan App Service Standard, Premium ou PremiumV2. Pour en savoir plus sur les coûts, consultez l’article : [Tarification d’App Service][ASPricing]. 
* Coûts de transfert de données : les sorties de données engendrent des coûts, même si le réseau virtuel se trouve dans le même centre de données. Ces coûts sont décrits dans [Détails de tarification des transferts de données][DataPricing]. 
* Coûts liés à la passerelle VPN : la passerelle de réseau virtuel nécessaire au VPN de point à site engendre des coûts. Les détails se trouvent sur la page [Tarification des passerelles VPN][VNETPricing].


## <a name="troubleshooting"></a>Résolution de problèmes
Même si cette fonctionnalité est facile à configurer, il se peut que vous rencontriez certains problèmes. Si vous rencontrez des difficultés pour accéder au point de terminaison souhaité, certains utilitaires vous permettent de tester la connectivité à partir de la console de l’application. Vous pouvez utiliser deux consoles : la console Kudu et la console du portail Azure. Pour accéder à la console Kudu à partir de votre application, accédez à Outils -> Kudu. Vous pouvez également accéder à la console Kudo via le site [nom du site].scm.azurewebsites.net. Une fois le site chargé, accédez à l'onglet Console de débogage. Pour accéder à la console hébergée par le portail Azure, à partir de votre application, accédez à outils -> Console. 

#### <a name="tools"></a>Outils
Les outils **ping**, **nslookup** et **tracert** ne fonctionnent pas dans la console en raison de contraintes de sécurité. Deux outils distincts ont été ajoutés pour les remplacer. Pour tester les fonctionnalités DNS, nous avons ajouté un outil nommé nameresolver.exe. La syntaxe est :

    nameresolver.exe hostname [optional: DNS Server]

Vous pouvez utiliser **nameresolver** pour vérifier les noms d’hôte dont dépend votre application. De cette façon, vous pouvez tester si des éléments de votre serveur DNS sont mal configurés ou si vous n’avez pas accès à votre serveur DNS. Vous pouvez identifier le serveur DNS qu’utilisera votre application dans la console en examinant les variables d’environnement WEBSITE_DNS_SERVER et WEBSITE_DNS_ALT_SERVER.

L’outil suivant vous permet de tester la connectivité TCP avec une combinaison hôte/port. Il s’agit de l’outil **tcpping**, dont la syntaxe est la suivante :

    tcpping.exe hostname [optional: port]

L’utilitaire **tcpping** vous indique si vous pouvez atteindre un hôte et un port spécifiques. Il peut afficher un succès si une application à écoute sur la combinaison d’hôte et de port, et si l’accès réseau à de votre application à l’hôte et au port est disponible.

#### <a name="debugging-access-to-vnet-hosted-resources"></a>Débogage de l’accès aux ressources hébergées sur un réseau virtuel
Plusieurs choses peuvent empêcher votre application d’atteindre un hôte et un port spécifiques. La plupart du temps, il s’agit de l’une des trois raisons suivantes :

* **Un pare-feu se trouve sur le trajet.** Si vous utilisez un pare-feu sur le trajet, vous dépassez le délai d’expiration de TCP. Dans ce cas, il est de 21 secondes. Utilisez l’outil **tcpping** pour tester la connectivité. Les délais d’expiration TCP peuvent avoir de nombreuses autres origines, mais commencez par vérifier ce point. 
* **DNS n’est pas accessible.** Le délai d’expiration du DNS est de trois secondes par serveur DNS. Si vous avez deux serveurs DNS, le délai d’expiration est de 6 secondes. Utilisez nameresolver pour vérifier que le DNS fonctionne correctement. Rappelez-vous que vous ne pouvez pas utiliser nslookup, car il n’utilise pas le DNS avec lequel votre réseau virtuel est configuré. S’il n’est pas accessible, il se peut qu’un pare-feu ou un groupe de sécurité réseau (NSG) bloque l’accès au DNS ou que celui-ci est inopérant.

Si ces éléments ne suffisent pas à résoudre vos problèmes, commencez par vous poser les questions suivantes : 

**Intégration au réseau virtuel régional**
* Votre destination est-elle une adresse RFC 1918 ?
* Y a-t-il un groupe de sécurité réseau qui bloque la sortie de votre sous-réseau d’intégration ?
* Si elle transite par ExpressRoute ou un VPN, votre passerelle locale est-elle configurée pour réacheminer le trafic vers Azure ? Si vous pouvez accéder à des points de terminaison dans votre réseau virtuel, mais pas en local, il est judicieux de vérifier ce point.

**Intégration au réseau virtuel avec passerelle obligatoire**
* La plage d’adresses de point à site se trouve-t-elle dans les plages RFC 1918 (10.0.0.0-10.255.255.255 / 172.16.0.0-172.31.255.255 / 192.168.0.0-192.168.255.255) ?
* Le portail indique-t-il que la passerelle fonctionne ? Si votre passerelle est en panne, rétablissez-la.
* Les certificats apparaissent-ils comme étant synchronisés ou soupçonnez-vous une modification de la configuration réseau ?  Si vos certificats ne sont pas synchronisés ou si vous pensez qu’une modification apportée à la configuration de votre réseau virtuel n’a pas été synchronisée avec vos ASP, cliquez sur « Synchroniser le réseau ».
* Si elle transite par ExpressRoute ou un VPN, votre passerelle locale est-elle configurée pour réacheminer le trafic vers Azure ? Si vous pouvez accéder à des points de terminaison dans votre réseau virtuel, mais pas en local, il est judicieux de vérifier ce point.

Le débogage des problèmes de réseau constitue un défi, car cette opération ne vous permet pas de voir ce qui bloque l’accès à une combinaison hôte:port spécifique. Voici quelques-unes des causes possibles :

* Un pare-feu activé sur l’hôte empêche l’accès au port de l’application à partir de votre plage d’adresses IP de point à site. Des sous-réseaux croisés requièrent souvent un accès public.
* Votre hôte cible est hors-service.
* Votre application est arrêtée.
* L’IP ou le nom d’hôte est incorrect.
* Votre application est à l’écoute sur un port autre que celui que vous envisagiez. Vous pouvez faire correspondre votre ID de processus avec le port d’écoute en utilisant « netstat -aon » sur l’hôte du point de terminaison. 
* Les groupes de sécurité de votre réseau sont configurés de telle sorte qu’ils empêchent l’accès à l’hôte et au port de votre application à partir de votre plage d’adresses IP de point à site.

Ne perdez pas de vue que l’adresse que votre application utilisera réellement est une inconnue. Cela peut être n’importe quelle adresse de la plage d’adresses de sous-réseau d’intégration ou de point à site. De ce fait, vous devez autoriser l’accès depuis toutes les adresses de la plage. 

Étapes de débogage supplémentaires :

* Connectez-vous à une machine virtuelle de votre réseau virtuel et essayez d’atteindre la combinaison hôte:port de vos ressources. Pour tester l’accès à TCP, utilisez la commande PowerShell **test-netconnection**. La syntaxe est :

      test-netconnection hostname [optional: -Port]

* Démarrez une application sur une machine virtuelle et testez l’accès à cet hôte et au port à partir de la console de votre application en utilisant **tcpping**.

#### <a name="on-premises-resources"></a>Ressources locales ####

Si votre application ne peut pas accéder à une ressource locale, vérifiez si vous pouvez atteindre la ressource à partir de votre réseau virtuel. Utilisez la commande PowerShell **test-netconnection** pour vérifier l’accès à TCP. Si votre machine virtuelle ne peut pas accéder à votre ressource locale, la connexion à votre VPN ou à ExpressRoute n’est peut-être pas configurée correctement.

Si votre machine virtuelle hébergée sur le réseau virtuel peut atteindre votre système local, mais que votre application ne le peut pas, la raison en est probablement une des suivantes :

* vos routes ne sont pas configurées avec vos plages d’adresses de sous-réseau ou de point à site dans votre passerelle locale
* vos groupes de sécurité réseau bloquent l’accès de votre plage IP de point à site
* vos pare-feux locaux bloquent le trafic provenant de votre plage IP de point à site
* vous tentez d’accéder à une adresse non compatible avec RFC 1918 en utilisant la fonctionnalité d’intégration au réseau virtuel régional


## <a name="powershell-automation"></a>Automation PowerShell

Vous pouvez intégrer App Service à un réseau virtuel Azure à l’aide de PowerShell. Pour obtenir un script prêt à l’exécution, consultez [Connect an app in Azure App Service to an Azure Virtual Network](https://gallery.technet.microsoft.com/scriptcenter/Connect-an-app-in-Azure-ab7527e3).


<!--Image references-->
[1]: ./media/web-sites-integrate-with-vnet/vnetint-app.png
[2]: ./media/web-sites-integrate-with-vnet/vnetint-addvnet.png
[3]: ./media/web-sites-integrate-with-vnet/vnetint-howitworks.png
[4]: ./media/web-sites-integrate-with-vnet/vnetint-details.png
[5]: ./media/web-sites-integrate-with-vnet/vnetint-aspdetails.png
[6]: ./media/web-sites-integrate-with-vnet/vnetint-newvnet.png
[7]: ./media/web-sites-integrate-with-vnet/vnetint-newvnetdetails.png
[8]: ./media/web-sites-integrate-with-vnet/vnetint-selectvnet.png

<!--Links-->
[VNETOverview]: https://azure.microsoft.com/documentation/articles/virtual-networks-overview/ 
[AzurePortal]: https://portal.azure.com/
[ASPricing]: https://azure.microsoft.com/pricing/details/app-service/
[VNETPricing]: https://azure.microsoft.com/pricing/details/vpn-gateway/
[DataPricing]: https://azure.microsoft.com/pricing/details/data-transfers/
[V2VNETP2S]: https://azure.microsoft.com/documentation/articles/vpn-gateway-howto-point-to-site-rm-ps/
[ASEintro]: environment/intro.md
[ILBASE]: environment/create-ilb-ase.md
[V2VNETPortal]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md
[VPNERCoex]: ../expressroute/expressroute-howto-coexist-resource-manager.md
[ASE]: environment/intro.md
[creategatewaysubnet]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#creategw
[creategateway]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal#creategw
[setp2saddresses]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal#addresspool
