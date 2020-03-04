---
title: Intégrer une application au réseau virtuel Microsoft Azure
description: Intégrez des applications à Azure App Service avec des réseaux virtuels Azure.
author: ccompy
ms.assetid: 90bc6ec6-133d-4d87-a867-fcf77da75f5a
ms.topic: article
ms.date: 02/27/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 76139716fe11536faa0ff792185ba1643801c641
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2020
ms.locfileid: "77649004"
---
# <a name="integrate-your-app-with-an-azure-virtual-network"></a>Intégrer une application à un réseau Azure Virtual Network
Ce document décrit la fonctionnalité d’intégration au réseau virtuel d’Azure App Service et explique comment la configurer avec des applications dans [Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714). Les [réseaux virtuels Azure][VNETOverview] vous permettent de placer un grand nombre de vos ressources Azure dans un réseau routable non-Internet.  

Azure App Service a deux variantes. 

1. Les systèmes multilocataires qui prennent en charge l’ensemble des plans tarifaires, excepté « Isolé »
2. L’environnement ASE (App Service Environment), qui se déploie dans votre réseau virtuel et prend en charge les applications à plan tarifaire Isolé.

Ce document décrit la fonctionnalité d’intégration au réseau virtuel, qui est destinée à être utilisée dans le service App Service multilocataire. Si votre application se trouve dans [Azure App Service Environment][ASEintro], elle est déjà dans un réseau virtuel et ne nécessite pas l’utilisation de la fonctionnalité d’intégration au réseau virtuel pour accéder aux ressources du même réseau virtuel. Pour plus d’informations sur toutes les fonctionnalités de mise en réseau App Service, consultez [Fonctionnalités de mise en réseau App Service](networking-features.md).

L’intégration au réseau virtuel permet à votre application web d’accéder aux ressources de votre réseau virtuel, mais sans pour autant accorder d’accès privé entrant à votre application web depuis le réseau virtuel. L’accès au site privé fait référence au fait de rendre votre application accessible uniquement à partir d’un réseau privé, par exemple à partir d’un réseau virtuel Azure. L'intégration au réseau virtuel sert uniquement à passer des appels sortants de votre application vers votre réseau virtuel. La fonctionnalité d’intégration au réseau virtuel se comporte différemment lorsqu’elle est utilisée avec des réseaux virtuels de la même région, et avec ceux d’autres régions. La fonctionnalité d’intégration au réseau virtuel présente deux variantes.

1. Intégration au réseau virtuel régional : lors de la connexion aux réseaux virtuels Resource Manager de la même région, vous devez disposer d’un sous-réseau dédié dans le réseau virtuel que vous intégrez. 
2. Intégration au réseau virtuel avec passerelle obligatoire : lors de la connexion à des réseaux virtuels d’autres régions, ou à un réseau virtuel classique dans la même région, vous avez besoin d’une passerelle de réseau virtuel provisionnée dans le réseau virtuel cible.

Les fonctionnalités d’intégration au réseau virtuel :

* Demandent un plan tarifaire Standard, Premium, PremiumV2 ou Élastique Premium 
* Prennent en charge les protocoles TCP et UDP
* Fonctionnent avec les applications App Service et les applications de fonction

L’intégration au réseau virtuel ne prend pas en charge certaines choses, notamment :

* montage d’un lecteur ;
* intégration AD ; 
* NetBios ;

L’intégration au réseau virtuel avec passerelle obligatoire fournit uniquement un accès aux ressources du réseau virtuel cible, ou des réseaux connectés au réseau virtuel cible avec un peering ou des réseaux privés virtuels. L’intégration au réseau virtuel avec passerelle obligatoire n’autorise pas l’accès aux ressources disponibles sur les connexions ExpressRoute, ou fonctionne avec des points de terminaison de service. 

Quelle que soit la version utilisée, l’intégration au réseau virtuel permet à votre application web d’accéder aux ressources de votre réseau virtuel, mais n’accorde pas d’accès privé entrant à votre application web à partir du réseau virtuel. L’accès au site privé fait référence au fait de rendre votre application accessible uniquement à partir d’un réseau privé, par exemple à partir d’un réseau virtuel Azure. L'intégration au réseau virtuel sert uniquement à passer des appels sortants de votre application vers votre réseau virtuel. 

## <a name="enable-vnet-integration"></a>Activer une intégration au réseau virtuel 

1. Accédez à l’interface utilisateur Réseau dans le portail App Service. Sous Intégration au réseau virtuel, sélectionnez *« Cliquez ici pour configurer »* . 

1. Sélectionnez **Ajouter un réseau virtuel**.  

   ![Sélectionner l’intégration au réseau virtuel][1]

1. La liste déroulante contient tous les réseaux virtuels Resource Manager de votre abonnement dans la même région, et en dessous, une liste de tous les réseaux virtuels Resource Manager dans toutes les autres régions. Sélectionnez le réseau virtuel avec lequel vous souhaitez effectuer l’intégration.

   ![Sélectionner le réseau virtuel][2]

   * Si le réseau virtuel se trouve dans la même région, créez un sous-réseau ou choisissez un sous-réseau préexistant vide. 

   * Pour sélectionner un réseau virtuel dans une autre région, vous devez disposer d’une passerelle de réseau virtuel provisionnée dont l’option Point à site est activée.

   * Pour une intégration à un réseau virtuel classique, plutôt que de cliquer sur la liste déroulante des réseaux virtuels, sélectionnez **Cliquez ici pour vous connecter à un réseau virtuel classique**. Sélectionnez le réseau virtuel classique souhaité. Le réseau virtuel cible doit déjà être doté d’une passerelle de réseau virtuel provisionnée dont l’option Point à site est activée.

    ![Sélectionner un réseau virtuel classique][3]
    
Lors de l’intégration, votre application est redémarrée.  Dès l’intégration terminée, des informations s’affichent sur le réseau virtuel que vous avez intégré. 

Une fois que votre application est intégrée au réseau virtuel, elle utilise le serveur DNS avec lequel votre réseau virtuel est configuré, sauf s’il est Azure DNS Private Zones. Vous ne pouvez pas actuellement utiliser l’intégration au réseau virtuel avec Azure DNS Private Zones.

## <a name="regional-vnet-integration"></a>Intégration au réseau virtuel régional

L’utilisation de l’intégration au réseau virtuel régional permet à votre application d’accéder aux :

* Ressources du réseau virtuel dans la même région que celle à laquelle vous êtes intégré 
* Ressources des réseaux virtuels appairés à votre réseau virtuel, et qui se trouvent dans la même région
* Services sécurisés du point de terminaison de service
* Ressources via les connexions ExpressRoute
* Ressources du réseau virtuel auquel vous êtes connecté
* Ressources via des connexions appairées, notamment les connexions ExpressRoute
* Points de terminaison privés 

Lorsque vous utilisez l’intégration au réseau virtuel avec des réseaux virtuels d’une même région, vous pouvez utiliser les fonctionnalités Azure Networking suivantes :

* Groupes de sécurité réseau (NSG) : vous pouvez bloquer le trafic sortant avec un groupe de sécurité réseau placé sur votre sous-réseau d’intégration. Les règles de trafic entrant ne s’appliquent pas, car vous ne pouvez pas utiliser l’intégration au réseau virtuel pour fournir un accès entrant à votre application web.
* Tables de route (UDR) : vous pouvez placer une table de route sur le sous-réseau d’intégration pour envoyer le trafic sortant à l’emplacement de votre choix. 

Par défaut, votre application achemine uniquement le trafic RFC1918 vers votre réseau virtuel. Si vous voulez router tout le trafic sortant vers votre réseau virtuel, appliquez le paramètre d’application WEBSITE_VNET_ROUTE_ALL à votre application. Pour configurer le paramètre d’application :

1. Accédez à l’interface utilisateur Configuration dans votre portail d’application. Sélectionnez **Nouveau paramètre d’application**.
1. Tapez **WEBSITE_VNET_ROUTE_ALL** dans le champ Nom, et **1** dans le champ Valeur.

   ![Fournir le paramètre d’application][4]

1. Sélectionnez **OK**.
1. Sélectionnez **Enregistrer**.

Si vous routez tout le trafic sortant vers votre réseau virtuel, il est soumis aux NSG et aux UDR appliqués à votre sous-réseau d’intégration. Lorsque vous acheminez tout le trafic sortant vers votre réseau virtuel, vos adresses sortantes seront toujours les adresses sortantes listées dans les propriétés de votre application, sauf si vous fournissez les routes pour envoyer le trafic ailleurs. 

Il existe certaines limitations concernant l’utilisation de l’intégration au réseau virtuel avec les réseaux virtuels d’une même région :

* Vous ne pouvez pas accéder à des ressources via des connexions de peering mondial.
* La fonctionnalité n’est disponible qu’à partir des unités d’échelle App Service récentes qui prennent en charge les plans App Service PremiumV2.
* Le sous-réseau d’intégration ne peut être utilisé que par un seul plan App Service
* La fonctionnalité ne peut pas être utilisée par des applications de plan Isolé qui se trouvent dans un environnement ASE.
* La fonctionnalité nécessite un sous-réseau inutilisé /27 avec 32 adresses, ou d’une taille supérieure, dans un réseau virtuel Resource Manager.
* L’application et le réseau virtuel doivent être dans la même région.
* Vous ne pouvez pas supprimer un réseau virtuel avec une application intégrée. Supprimez l’intégration avant de supprimer le réseau virtuel. 
* L’intégration ne peut se faire qu’avec des réseaux virtuels du même abonnement que l’application web.
* Vous ne pouvez disposer que d’une seule intégration au réseau virtuel régional par plan App Service. Plusieurs applications d’un même plan App Service peuvent utiliser le même réseau virtuel. 
* Vous ne pouvez pas modifier l'abonnement d'une application ou d'un plan App Service lorsqu'une application utilise l'intégration au réseau virtuel régional.

Une adresse est utilisée pour chaque instance du plan App Service. Si vous mettez votre application à l’échelle vers cinq instances, cinq adresses sont utilisées. Étant donné que la taille du sous-réseau ne peut pas être modifiée après l’affectation, vous devez utiliser un sous-réseau suffisamment grand pour s’adapter à la taille que votre application est susceptible d’atteindre. Une taille de /26 avec 64 adresses est recommandée. Un sous-réseau /26 avec 64 adresses contient un plan App Service Premium avec 30 instances. Lorsque vous modifiez un plan App Service à la hausse ou à la baisse, vous avez brièvement besoin de deux fois plus d'adresses. 

Si vous souhaitez que vos applications d’un autre plan App Service atteignent un réseau virtuel auquel sont déjà connectées des applications d’un autre plan App Service, vous devez sélectionner un sous-réseau différent de celui utilisé par l’intégration VNet préexistante.  

La fonctionnalité est en préversion pour Linux. Le formulaire Linux de la fonctionnalité prend uniquement en charge les appels aux adresses RFC 1918 (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16).

### <a name="web-app-for-containers"></a>Web App pour conteneurs

Si vous utilisez App Service sur Linux avec les images intégrées, l’intégration au réseau virtuel régional fonctionne sans modification supplémentaire. Si vous utilisez Web App pour conteneurs, vous devez modifier votre image docker pour pouvoir utiliser l’intégration au réseau virtuel. Dans votre image docker, utilisez la variable d’environnement PORT comme port d’écoute du serveur web principal, au lieu d’utiliser un numéro de port codé en dur. La variable d’environnement PORT est automatiquement définie par la plateforme App Service au démarrage du conteneur. Si vous utilisez SSH, le démon SSH doit être configuré pour écouter sur le numéro de port spécifié par la variable d’environnement SSH_PORT lors de l’utilisation de l’intégration au réseau virtuel régional.  Il n’existe pas de prise en charge de l’intégration au réseau virtuel avec passerelle obligatoire sur Linux. 

### <a name="service-endpoints"></a>Points de terminaison de service

L’intégration au réseau virtuel régional vous permet d’utiliser des points de terminaison de service.  Pour utiliser des points de terminaison de service avec votre application, servez-vous de l’intégration au réseau virtuel régional pour vous connecter à un réseau virtuel sélectionné, puis configurez des points de terminaison de service sur le sous-réseau que vous avez utilisé pour l’intégration. 

### <a name="network-security-groups"></a>Network Security Group

Les groupes de sécurité réseau (NSG) vous permettent de bloquer les trafics entrant et sortant sur les ressources d’un réseau virtuel. Une application web utilisant l’intégration au réseau virtuel régional peut utiliser le [Groupe de sécurité réseau][VNETnsg] pour bloquer le trafic sortant vers des ressources dans votre réseau virtuel ou Internet. Pour bloquer le trafic vers des adresses publiques, le paramètre d’application WEBSITE_VNET_ROUTE_ALL doit être défini sur 1. Les règles de trafic entrant dans un groupe de sécurité réseau ne s’appliquent pas à votre application, car l’intégration au réseau virtuel n’a d’incidence que sur le trafic sortant depuis votre application. Pour contrôler le trafic entrant vers votre application web, utilisez la fonctionnalité Restrictions d’accès. Un groupe de sécurité réseau appliqué à votre sous-réseau d’intégration sera actif quelles que soient les routes appliquées à votre sous-réseau d’intégration. Si WEBSITE_VNET_ROUTE_ALL était défini sur 1, et que vous n’aviez aucune route affectant le trafic des adresses publiques sur votre sous-réseau d’intégration, l’ensemble du trafic sortant serait toujours soumis aux NSG attribués à votre sous-réseau d’intégration. Si WEBSITE_VNET_ROUTE_ALL n’était pas défini, les NSG s’appliqueraient uniquement au trafic RFC1918.

### <a name="routes"></a>Itinéraires

Les tables de route vous permettent de router le trafic sortant depuis votre application vers l’emplacement de votre choix. Par défaut, les tables de route n’ont d’incidence que sur votre trafic de destination RFC1918.  Si vous affectez la valeur 1 à WEBSITE_VNET_ROUTE_ALL, tous vos appels sortants seront affectés. Les routes définies sur votre sous-réseau d’intégration n’ont pas d’incidence sur les réponses aux requêtes d’application entrantes. Les destinations courantes peuvent inclure des pare-feu ou des passerelles. Si vous souhaitez router tout le trafic sortant localement, vous pouvez utiliser une table de route pour envoyer l’intégralité du trafic sortant vers votre passerelle ExpressRoute. Si vous choisissez de router le trafic vers une passerelle, veillez à définir des routes dans le réseau externe pour renvoyer des réponses.

Les routes Border Gateway Protocol (BGP) affectent également le trafic de votre application. Si vous avez des routes BGP provenant par exemple d’une passerelle ExpressRoute, le trafic sortant de votre application sera affecté. Par défaut, les routes BGP n’affectent que votre trafic de destination RFC1918. Si WEBSITE_VNET_ROUTE_ALL a la valeur 1, tout le trafic sortant peut être affecté par vos routes BGP. 

### <a name="how-regional-vnet-integration-works"></a>Fonctionnement de l’intégration au réseau virtuel régional

Les applications contenues dans App Service sont hébergées dans des rôles de travail. Les plans tarifaires de base et supérieurs sont des plans d’hébergement dédiés dans lesquels aucune autre charge de travail de client ne s’exécute sur les mêmes Workers. L’intégration au réseau virtuel régional opère en montant des interfaces virtuelles avec des adresses du sous-réseau délégué. Comme l’adresse de départ se trouve dans votre réseau virtuel, elle peut accéder à la plupart des éléments contenus dans votre réseau virtuel ou accessibles par celui-ci, comme le ferait une machine virtuelle dans votre réseau virtuel. L’implémentation de la mise en réseau étant différente de l’exécution d’une machine virtuelle dans votre réseau virtuel, certaines fonctionnalités de mise en réseau ne sont pas encore disponibles quand cette fonctionnalité est utilisée.

![Fonctionnement de l’intégration au réseau virtuel régional][5]

Quand l’intégration au réseau virtuel régional est activée, votre application continue de passer des appels sortants vers Internet en empruntant les mêmes canaux que d’habitude. Les adresses sortantes figurant sur le portail des propriétés de l’application sont toujours les adresses qu’utilise votre application. Ce qui change pour votre application, c’est que les appels aux services sécurisés de point de terminaison de service ou aux adresses RFC 1918 accèdent à votre réseau virtuel. Si WEBSITE_VNET_ROUTE_ALL a la valeur 1, tout le trafic sortant peut être envoyé dans votre réseau virtuel. 

La fonctionnalité ne prend en charge qu’une seule interface virtuelle par Worker.  Une interface virtuelle par Worker signifie une intégration au réseau virtuel régional par plan App Service. Toutes les applications relevant d’un même plan App Service peuvent utiliser la même intégration au réseau virtuel, mais si vous avez besoin qu’une application se connecte à un autre réseau virtuel, vous devez créer un autre plan App Service. L’interface virtuelle utilisée n’est pas une ressource à laquelle les clients peuvent accéder directement.

Compte tenu du mode de fonctionnement de cette technologie, le trafic utilisé avec l’intégration au réseau virtuel n’apparaît pas dans les journaux de flux du groupe de sécurité réseau (NSG) ou de Network Watcher.  

## <a name="gateway-required-vnet-integration"></a>Intégration au réseau virtuel avec passerelle obligatoire

L’intégration au réseau virtuel avec passerelle obligatoire prend en charge la connexion à un réseau virtuel d’une autre région ou à un réseau virtuel classique. Intégration au réseau virtuel avec passerelle obligatoire : 

* Permet à une application de se connecter à un seul réseau virtuel à la fois.
* Permet d’intégrer jusqu’à cinq réseaux virtuels dans un plan App Service. 
* Permet à un même réseau virtuel d’être utilisé par plusieurs applications dans le cadre d’un plan App Service sans impact sur le nombre total d’applications pouvant être utilisées par un plan App Service.  Si vous avez six applications qui utilisent le même réseau virtuel dans le cadre d’un plan App Service, cela compte pour un réseau virtuel utilisé. 
* Prend en charge un contrat SLA de 99,9 % du fait du contrat SLA de la passerelle.
* Permet à vos applications d’utiliser le DNS avec lequel le réseau virtuel est configuré.
* Nécessite une passerelle de réseau virtuel basée sur le routage et configurée avec un VPN de point à site SSTP avant de pouvoir être connecté à une application. 

Vous ne pouvez pas utiliser l’intégration au réseau virtuel avec passerelle obligatoire :

* Avec des applications Linux
* Avec un réseau virtuel connecté au moyen d’ExpressRoute 
* Pour accéder à des ressources sécurisées de points de terminaison de service
* Avec une passerelle de coexistence qui prend en charge à la fois les connexions ExpressRoute et les VPN de point à site/site à site

### <a name="set-up-a-gateway-in-your-vnet"></a>Configurer une passerelle de votre réseau virtuel ###

Pour créer une passerelle :

1. [Créez un sous-réseau de passerelle][creategatewaysubnet] dans votre réseau virtuel.  

1. [Créez la passerelle VPN][creategateway]. Sélectionnez un type de VPN basé sur les routes.

1. [Définissez les adresses de point à site][setp2saddresses]. Si la passerelle n’est pas dans la référence SKU de base, c’est que IKEV2 doit être désactivé dans la configuration de point à site, et que SSTP doit être sélectionné. L’espace d’adressage de point à site doit se situer dans les blocs d’adresses RFC 1918, à savoir 10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16.

Si vous créez simplement la passerelle pour l’utiliser avec l’intégration au réseau virtuel App Service, il n’est pas nécessaire de charger un certificat. La création de la passerelle peut prendre 30 minutes. Vous ne serez pas en mesure d’intégrer votre application à votre réseau virtuel tant que la passerelle n’est pas provisionnée. 

### <a name="how-gateway-required-vnet-integration-works"></a>Fonctionnement de l’intégration au réseau virtuel avec passerelle obligatoire

L’intégration au réseau virtuel avec passerelle obligatoire s’appuie sur la technologie VPN de point à site. Les VPN de point à site limitent l’accès réseau à la seule machine virtuelle hébergeant l’application. Les applications sont limitées au seul envoi du trafic vers Internet, via des connexions hybrides ou via l’intégration au réseau virtuel. Lorsque votre application est configurée avec le portail pour utiliser l’intégration au réseau virtuel avec passerelle obligatoire, une négociation complexe est managée en votre nom pour créer et attribuer des certificats sur la passerelle et du côté de l’application. Au final, les Workers utilisés pour héberger vos applications sont en mesure de se connecter directement à la passerelle de réseau virtuel, dans le réseau virtuel sélectionné. 

![Fonctionnement de l’intégration au réseau virtuel avec passerelle obligatoire][6]

### <a name="accessing-on-premises-resources"></a>Accès aux ressources sur site

Les applications peuvent accéder aux ressources locales en s’intégrant à des réseaux virtuels qui ont des connexions site à site. Si vous utilisez l’intégration au réseau virtuel avec passerelle obligatoire, vous devez mettre à jour les routes de votre passerelle VPN locale avec vos blocs d’adresses de point à site. Lors de la configuration initiale de ce VPN site à site, les scripts utilisés pour le configurer doivent définir les routes correctement. Si vous ajoutez des adresses de point à site après avoir créé votre VPN de site à site, vous devez mettre à jour les routes manuellement. La procédure détaillée dépend de la passerelle et n’est pas décrite ici. Vous ne pouvez pas configurer BGP avec une connexion VPN de site à site.

Aucune configuration supplémentaire n’est nécessaire pour permettre à la fonctionnalité d’intégration au réseau virtuel régional d’accéder à votre réseau virtuel et en local. Vous devez simplement connecter votre réseau virtuel en local à l’aide d’ExpressRoute ou d’un VPN de site à site. 

> [!NOTE]
> La fonctionnalité d’intégration au réseau virtuel avec passerelle obligatoire n’intègre pas une application à un réseau virtuel doté d’une passerelle ExpressRoute. Même si la passerelle ExpressRoute est configurée en [mode de coexistence][VPNERCoex], l’intégration au réseau virtuel ne fonctionne pas. Si vous avez besoin d’accéder à des ressources via une connexion ExpressRoute, vous pouvez utiliser la fonctionnalité d’intégration au réseau virtuel régional ou un [environnement ASE (App Service Environment)][ASE], qui s’exécute dans votre réseau virtuel. 
> 
> 

### <a name="peering"></a>Peering

Si vous utilisez le peering avec l’intégration au réseau virtuel régional, aucune configuration supplémentaire n’est nécessaire. 

Si vous utilisez l’intégration au réseau virtuel avec passerelle obligatoire ainsi que le peering, vous devez configurer quelques éléments supplémentaires. Pour configurer le peering afin qu’il fonctionne avec votre application :

1. Ajoutez une connexion de peering sur le réseau virtuel auquel votre application se connecte. Lors de l’ajout de la connexion de peering, activez **Autoriser l’accès au réseau virtuel** et cochez **Autoriser le trafic transféré** et **Autoriser le transit par passerelle**.
1. Ajoutez une connexion de peering sur le réseau virtuel à appairer au réseau virtuel auquel vous êtes connecté. Lors de l’ajout de la connexion de peering sur le réseau virtuel de destination, activez **Autoriser l’accès au réseau virtuel** et cochez **Autoriser le trafic transféré** et **Autoriser les passerelles distantes**.
1. Accédez au plan App Service > Réseau > Interface utilisateur de l’intégration au réseau virtuel dans le portail.  Sélectionnez le réseau virtuel auquel votre application se connecte. Dans la section Routage, ajoutez la plage d’adresses du réseau virtuel qui est appairé au réseau virtuel auquel votre application est connectée.  

## <a name="managing-vnet-integration"></a>Gestion de l’intégration au réseau virtuel 

Les connexions et déconnexions avec un réseau virtuel se font au niveau de l’application. Les opérations qui peuvent affecter l’intégration au réseau virtuel entre plusieurs applications s’effectuent au niveau du plan App Service. Sur le portail, sous application > Mise en réseau > Intégration de réseau virtuel, vous pouvez obtenir des détails sur votre réseau virtuel. Vous pouvez consulter des informations similaires au niveau de l’ASP dans ASP > Networking > Portail Intégration au réseau virtuel.

La seule opération que vous pouvez effectuer dans la vue d’application de votre interface d’intégration au réseau virtuel consiste à déconnecter votre application du réseau virtuel auquel elle est actuellement connectée. Pour déconnecter votre application d’un réseau virtuel, sélectionnez **Déconnecter**. Votre application est redémarrée quand vous vous déconnectez d’un réseau virtuel. La déconnexion ne change pas votre réseau virtuel. Le sous-réseau ou la passerelle ne sont pas supprimés. Donc, si vous souhaitez supprimer votre réseau virtuel, vous devez d’abord déconnecter votre application du réseau virtuel puis supprimer les ressources qu’il contient comme les passerelles. 

L’interface utilisateur de l’intégration au réseau virtuel ASP vous montre toutes les intégrations au réseau virtuel utilisées par les applications dans votre ASP. Pour voir des détails supplémentaires sur chaque réseau virtuel, cliquez sur le réseau virtuel qui vous intéresse. Vous pouvez effectuer deux actions ici pour l’intégration au réseau virtuel avec passerelle obligatoire.

* **Synchroniser le réseau** L’opération de synchronisation du réseau s’adresse uniquement à la fonctionnalité d’intégration au réseau virtuel dépendante de la passerelle. L’exécution d’une opération de synchronisation du réseau est l’assurance que vos certificats et informations réseau sont synchronisés. Si vous ajoutez ou que vous changez le DNS du réseau virtuel, vous devez effectuer une opération **Synchroniser le réseau**. Cette opération redémarre toutes les applications utilisant ce réseau virtuel.
* **Ajouter des routes** : l’ajout de routes pilote le trafic sortant dans votre réseau virtuel. 

**Routage de l’intégration au réseau virtuel avec passerelle obligatoire** : les routes définies dans votre réseau virtuel sont utilisées pour diriger le trafic dans votre réseau virtuel à partir de votre application. Si vous devez envoyer du trafic sortant supplémentaire dans le réseau virtuel, vous pouvez ajouter ces blocs d’adresses ici. Cette fonctionnalité n’opère qu’avec l’intégration au réseau virtuel avec passerelle obligatoire. Les tables de route n’ont pas d’incidence sur le trafic de votre application lors de l’utilisation de l’intégration au réseau virtuel avec passerelle obligatoire, comme c’est le cas pour l’intégration au réseau virtuel régional.

**Certificats de l’intégration au réseau virtuel avec passerelle obligatoire** : quand l’intégration au réseau virtuel avec passerelle obligatoire est activée, un échange de certificats est nécessaire pour garantir la sécurité de la connexion. En plus des certificats, la configuration DNS, les routes et d’autres éléments similaires décrivent le réseau.
Si des certificats ou des informations du réseau sont modifiés, vous devez cliquer sur « Synchroniser le réseau ». Quand vous cliquez sur « Synchroniser le réseau », la connectivité entre votre application et votre réseau virtuel est brièvement interrompue. Votre application n’est pas redémarrée, et la perte de connectivité peut altérer le fonctionnement correct de votre site. 

## <a name="pricing-details"></a>Détails de la tarification
L’utilisation de la fonctionnalité d’intégration au réseau virtuel régional ne s’accompagne d’aucuns frais supplémentaires, au-delà des frais liés au niveau tarifaires ASP.

L’utilisation de la fonctionnalité d’intégration au réseau virtuel avec passerelle obligatoire est associée à trois types de coûts :

* Frais liés au niveau tarifaire ASP : vos applications doivent relever d’un plan App Service Standard, Premium ou PremiumV2. Pour en savoir plus sur les coûts, consultez l’article : [Tarification d’App Service][ASPricing]. 
* Coûts de transfert de données : les sorties de données engendrent des coûts, même si le réseau virtuel se trouve dans le même centre de données. Ces coûts sont décrits dans [Détails de tarification des transferts de données][DataPricing]. 
* Coûts liés à la passerelle VPN : la passerelle de réseau virtuel nécessaire au VPN de point à site engendre des coûts. Les détails se trouvent sur la page [Tarification des passerelles VPN][VNETPricing].

## <a name="troubleshooting"></a>Dépannage
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
* Votre destination est-elle une adresse non RFC1918 alors que la valeur de WEBSITE_VNET_ROUTE_ALL n’est pas 1 ?
* Y a-t-il un groupe de sécurité réseau qui bloque la sortie de votre sous-réseau d’intégration ?
* Si elle transite par ExpressRoute ou un VPN, votre passerelle locale est-elle configurée pour réacheminer le trafic vers Azure ? Si vous pouvez accéder à des points de terminaison dans votre réseau virtuel, mais pas en local, vérifiez vos routes.
* Disposez-vous d’autorisations suffisantes pour définir la délégation sur le sous-réseau d’intégration ? Durant la configuration de l’intégration au réseau virtuel régional, votre sous-réseau d’intégration doit être délégué à Microsoft.Web. L'interface utilisateur de l'intégration au réseau virtuel délègue automatiquement le sous-réseau à Microsoft.Web. Si votre compte ne dispose pas d’autorisations réseau suffisantes pour définir la délégation, vous devez demander à une personne autorisée de configurer les attributs de votre sous-réseau d’intégration de manière à déléguer celui-ci. Pour déléguer manuellement le sous-réseau d'intégration, accédez à l'interface utilisateur du sous-réseau du service Réseau virtuel Azure et définissez la délégation sur Microsoft.Web. 

**Intégration au réseau virtuel avec passerelle obligatoire**
* La plage d’adresses de point à site se trouve-t-elle dans les plages RFC 1918 (10.0.0.0-10.255.255.255 / 172.16.0.0-172.31.255.255 / 192.168.0.0-192.168.255.255) ?
* Le portail indique-t-il que la passerelle fonctionne ? Si votre passerelle est en panne, rétablissez-la.
* Les certificats apparaissent-ils comme étant synchronisés ou soupçonnez-vous une modification de la configuration réseau ?  Si vos certificats ne sont pas synchronisés ou si vous pensez qu’une modification apportée à la configuration de votre réseau virtuel n’a pas été synchronisée avec vos ASP, cliquez sur « Synchroniser le réseau ».
* Si elle transite par un VPN, la passerelle locale est-elle configurée pour réacheminer le trafic vers Azure ? Si vous pouvez accéder à des points de terminaison dans votre réseau virtuel, mais pas en local, vérifiez vos routes.
* Essayez-vous d’utiliser une passerelle de coexistence qui prend à la fois en charge la connectivité point à site et la connectivité ExpressRoute ? Les passerelles de coexistence ne sont pas prises en charge avec l’intégration au réseau virtuel. 

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


## <a name="automation"></a>Automatisation

Il existe une prise en charge de l’interface CLI pour l’intégration au réseau virtuel régional. Pour accéder aux commandes suivantes, veuillez [Installer Azure CLI][installCLI]. 

        az webapp vnet-integration --help

        Group
            az webapp vnet-integration : Methods that list, add, and remove virtual network integrations
            from a webapp.
                This command group is in preview. It may be changed/removed in a future release.
        Commands:
            add    : Add a regional virtual network integration to a webapp.
            list   : List the virtual network integrations on a webapp.
            remove : Remove a regional virtual network integration from webapp.

        az appservice vnet-integration --help

        Group
            az appservice vnet-integration : A method that lists the virtual network integrations used in an
            appservice plan.
                This command group is in preview. It may be changed/removed in a future release.
        Commands:
            list : List the virtual network integrations used in an appservice plan.

Pour l’intégration au réseau virtuel avec passerelle obligatoire, vous pouvez intégrer App Service à un réseau virtuel Azure à l’aide de PowerShell. Pour obtenir un script prêt à l’exécution, consultez [Connect an app in Azure App Service to an Azure Virtual Network](https://gallery.technet.microsoft.com/scriptcenter/Connect-an-app-in-Azure-ab7527e3).


<!--Image references-->
[1]: ./media/web-sites-integrate-with-vnet/vnetint-app.png
[2]: ./media/web-sites-integrate-with-vnet/vnetint-addvnet.png
[3]: ./media/web-sites-integrate-with-vnet/vnetint-classic.png
[4]: ./media/web-sites-integrate-with-vnet/vnetint-appsetting.png
[5]: ./media/web-sites-integrate-with-vnet/vnetint-regionalworks.png
[6]: ./media/web-sites-integrate-with-vnet/vnetint-gwworks.png


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
[VNETnsg]: https://docs.microsoft.com/azure/virtual-network/security-overview/
[VNETRouteTables]: https://docs.microsoft.com/azure/virtual-network/manage-route-table/
[installCLI]: https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest/
