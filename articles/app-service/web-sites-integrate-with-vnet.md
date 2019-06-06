---
title: Intégrer une application à un réseau virtuel Azure – Azure App Service
description: Explique comment connecter une application d’Azure App Service à un réseau virtuel Azure nouveau ou existant
services: app-service
documentationcenter: ''
author: ccompy
manager: stefsch
ms.assetid: 90bc6ec6-133d-4d87-a867-fcf77da75f5a
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/28/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: dcb128d8793e3438d87e728bde069d07c72cf97b
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66493061"
---
# <a name="integrate-your-app-with-an-azure-virtual-network"></a>Intégrer une application à un réseau Azure Virtual Network
Ce document décrit la fonctionnalité d’intégration de réseau virtuel Azure App Service et comment configurer des applications dans le [Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714). Les [réseaux virtuels Azure][VNETOverview] vous permettent de placer un grand nombre de vos ressources Azure dans un réseau routable non-Internet.  

Azure App Service se présente sous deux formes. 

1. Les systèmes multilocataires qui prennent en charge l’ensemble des plans de tarification, excepté « Isolé »
2. L’environnement de Service App (ASE), qui déploie sur votre réseau virtuel et prend en charge les applications du plan de tarification isolé

Ce document passe par les deux fonctionnalités d’intégration au réseau virtuel, qui doit être utilisée dans l’architecture mutualisée App Service. Si votre application se trouve dans l’[environnement App Service][ASEintro], elle est déjà dans un réseau virtuel et ne nécessite pas l’utilisation de la fonctionnalité d’intégration au réseau virtuel pour accéder aux ressources du même réseau virtuel. Pour plus d’informations sur toutes les fonctionnalités de mise en réseau App Service, consultez [réseau fonctionnalités App Service](networking-features.md)

Il existe deux formes à la fonctionnalité d’intégration de réseau virtuel

1. Une seule version permet l’intégration avec les réseaux virtuels dans la même région. Cette forme de la fonctionnalité nécessite un sous-réseau dans un réseau virtuel dans la même région
2. L’autre version permet l’intégration avec les réseaux virtuels dans d’autres régions ou avec les réseaux virtuels classiques. Cette version de la fonctionnalité nécessite le déploiement d’une passerelle de réseau virtuel dans votre réseau virtuel.

Une application peut utiliser uniquement d’une forme de la fonctionnalité d’intégration de réseau virtuel à la fois. La question est la fonction qui doit utiliser. Vous pouvez utiliser pour de nombreux éléments. Les facteurs de différenciation clair sont cependant :

| Problème  | Solution | 
|----------|----------|
| Souhaitez atteindre une adresse RFC 1918 (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16) dans la même région | Intégration de réseau virtuel régional |
| Pour accéder à un réseau virtuel classique ou un réseau virtuel dans une autre région | Passerelle requis intégration au réseau virtuel |
| Pour accéder à RFC 1918 de points de terminaison sur ExpressRoute | Intégration de réseau virtuel régional |
| À atteindre des ressources entre les points de terminaison de service | Intégration de réseau virtuel régional |

Aucune de ces fonctionnalités vous permettra d’atteindre les adresses non compatibles avec RFC 1918 sur ExpressRoute. Pour ce faire, vous devez utiliser un environnement app service pour l’instant.

À l’aide de l’intégration de réseau virtuel régional ne connectez votre réseau virtuel en local ou configurer des points de terminaison de service. Qui est distinct de mise en réseau de configuration. L’intégration de réseau virtuel régional permet simplement à votre application effectuer des appels de ces types de connexions.

Quelle que soit la version utilisée, intégration au réseau virtuel permet de votre application web a accès aux ressources dans votre réseau virtuel, mais n’accorde un accès privé entrant à votre application web à partir du réseau virtuel. L’accès au site privé fait référence au fait de rendre votre application accessible uniquement à partir d’un réseau privé, par exemple à partir d’un réseau virtuel Azure. Intégration au réseau virtuel est uniquement pour effectuer des appels sortants à partir de votre application sur votre réseau virtuel. 

La fonctionnalité d’intégration au réseau virtuel :

* nécessite un plan de tarification Standard, Premium ou PremiumV2 
* prend en charge les protocoles TCP et UDP ;
* fonctionne avec les applications App Service et Function App

L’intégration au réseau virtuel ne prend pas en charge certaines choses, notamment :

* montage d’un lecteur ;
* intégration AD ; 
* NetBios ;

## <a name="regional-vnet-integration"></a>Intégration de réseau virtuel régional 

Lors de l’intégration au réseau virtuel est utilisée avec les réseaux virtuels dans la même région que votre application, il requiert l’utilisation d’un sous-réseau délégué avec au moins 32 adresses qu’elle contient. Le sous-réseau ne peut pas être utilisé pour tout autre élément. Les appels sortants effectués à partir de votre application sera à partir des adresses dans le sous-réseau de délégué. Lorsque vous utilisez cette version de l’intégration au réseau virtuel, les appels sont effectués à partir d’adresses de votre réseau virtuel. À l’aide d’adresses de votre réseau virtuel permet à votre application pour :

* effectuer des appels au point de terminaison de service services sécurisés
* accéder aux ressources via les connexions ExpressRoute
* accéder aux ressources dans le réseau virtuel que vous êtes connecté à
* accéder aux ressources via des connexions homologues, y compris les connexions ExpressRoute

Cette fonctionnalité est disponible en version préliminaire, mais il est pris en charge pour les charges de travail de production avec les limitations suivantes :

* Vous pouvez uniquement atteindre les adresses qui se trouvent dans la plage de RFC 1918. Ce sont des adresses 10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16 blocs d’adresses.
* Vous ne pouvez atteindre les ressources entre les connexions d’appairage globales
* Vous ne pouvez pas définir des itinéraires sur le trafic entrant à partir de votre application sur votre réseau virtuel
* la fonctionnalité est uniquement disponible à partir de nouvelles unités d’échelle App Service qui prennent en charge les plans de Service d’application PremiumV2.
* la fonctionnalité ne peut pas être utilisée par les applications de plan isolé qui se trouvent dans un environnement App Service
* la fonctionnalité requiert un sous-réseau inutilisé au moins 32 adresses dans votre VNet Resource Manager.
* L’application et le réseau virtuel doivent être dans la même région
* Une adresse est utilisée pour chaque instance de plan App Service. Comme vous ne pouvez pas changer la taille du sous-réseau après l’avoir affectée, utilisez un sous-réseau qui peut couvrir plus que votre taille d’échelle maximale. La taille /27 avec 32 adresses est la taille recommandée car elle permet de gérer un plan App Service mis à l’échelle avec 20 instances.
* Vous ne pouvez pas supprimer un réseau virtuel avec une application intégrée. Vous devez d’abord supprimer l’intégration 

Pour utiliser la fonctionnalité d’intégration de réseau virtuel avec un VNet Resource Manager dans la même région :

1. Accédez à l’interface utilisateur Réseau dans le portail. Si votre application est en mesure d’utiliser la nouvelle fonctionnalité, vous verrez une option pour ajouter du réseau virtuel (version préliminaire).  

   ![Sélectionnez l’intégration au réseau virtuel][6]

1. Sélectionnez **Ajouter un réseau virtuel (préversion)** .  

1. Sélectionnez le réseau virtuel Resource Manager auquel vous voulez vous intégrer, puis créez un sous-réseau ou choisissez un sous-réseau vide préexistant. L’intégration prend moins d’une minute. Lors de l’intégration, votre application est redémarrée.  Une fois l’intégration terminée, vous voyez des détails sur le réseau virtuel auquel vous êtes intégré et une bannière en haut qui vous indique que la fonctionnalité est en préversion.

   ![Sélectionner le réseau virtuel et le sous-réseau][7]

Une fois que votre application est intégrée à votre réseau virtuel, il utilisera le même serveur DNS configuré avec votre réseau virtuel. 

Pour déconnecter votre application du réseau virtuel, sélectionnez **Déconnecter**. Ceci redémarre votre application web. 

La nouvelle fonctionnalité d’intégration au réseau virtuel vous permet d’utiliser des points de terminaison de service.  Pour utiliser des points de terminaison de service avec votre application, utilisez la nouvelle intégration au réseau virtuel pour vous connecter à un réseau virtuel sélectionné, puis configurez des points de terminaison de service sur le sous-réseau que vous avez utilisé pour l’intégration. 

### <a name="how-vnet-integration-works"></a>Fonctionnement de l’intégration au réseau virtuel

Les applications dans App Service sont hébergées sur les rôles de travail. La base et le plus élevé de plans de tarification sont dédiés de plans d’hébergement lorsqu’il n’y a aucune autres charges de travail clients en cours d’exécution sur les mêmes workers. Intégration au réseau virtuel fonctionne en montant des interfaces virtuelles avec des adresses dans le sous-réseau de délégué. Étant donné que l’à partir d’adresse se trouve dans votre réseau virtuel, il a accès à la plupart des éléments dans ou via votre réseau virtuel comme le ferait une machine virtuelle dans votre réseau virtuel. L’implémentation de mise en réseau est différente de celle d’une machine virtuelle en cours d’exécution dans votre réseau virtuel et qui est pourquoi certaines fonctionnalités de mise en réseau ne sont pas encore disponibles lors de l’utilisation de cette fonctionnalité.

![Intégration au réseau virtuel](media/web-sites-integrate-with-vnet/vnet-integration.png)

Lors de l’intégration au réseau virtuel est activée, votre application sera toujours effectuer des appels sortants à internet via les canaux mêmes comme d’habitude. Les adresses sortantes qui sont répertoriés dans le portail de propriétés d’application sont toujours les adresses utilisées par votre application. Quels sont les modifications de votre application que les appels au point de terminaison de service sécurisés des services ou les adresses RFC 1918 passe sur votre réseau virtuel. 

La fonctionnalité prend uniquement en charge une seule interface virtuelle par travail.  Une interface virtuelle par travailleur signifie une interface virtuelle par plan App Service. Toutes les applications dans le même plan App Service peuvent utiliser la même intégration au réseau virtuel, mais si vous avez besoin pour vous connecter à un réseau virtuel supplémentaire, vous devez créer un autre plan App Service. L’interface virtuel utilisé n’est pas une ressource dont les clients disposent d’un accès direct à.

En raison de la nature du fonctionne de cette technologie, le trafic qui est utilisé avec l’intégration de réseau virtuel n’apparaît pas dans les journaux de flux Network Watcher ou le groupe de sécurité réseau.  

## <a name="gateway-required-vnet-integration"></a>Passerelle requis intégration au réseau virtuel 

La passerelle requis la fonctionnalité d’intégration de réseau virtuel :

* peut être utilisé pour se connecter à des réseaux virtuels dans n’importe quelle région qu’ils soient Resource Manager ou réseaux virtuels classiques
* permet à une application de se connecter à un seul réseau virtuel à la fois
* permet d’intégrer jusqu’à cinq réseaux virtuels dans un plan App Service 
* permet le même réseau virtuel à être utilisés par plusieurs applications dans un Plan App Service sans affecter le nombre total peut être utilisé par un plan App Service.  Si vous avez 6 applications utilisant le même réseau virtuel dans le même plan App Service, qui compte en tant que réseau 1 virtuel est utilisé. 
* nécessite une passerelle de réseau virtuel configurée avec le VPN de point à site
* prend en charge un contrat SLA de 99,9 % grâce au contrat SLA sur la passerelle de réseau virtuel

Cette fonctionnalité ne prend pas en charge :

* accès à des ressources via ExpressRoute 
* accès à des ressources via des point de terminaison de service 

### <a name="getting-started"></a>Prise en main

Voici quelques informations à garder à l’esprit avant de connecter votre application web à un réseau virtuel.

* Un réseau virtuel cible doit prendre en charge la connexion VPN de point à site avec une passerelle de routage avant de pouvoir être connecté à une application. 
* Le réseau virtuel doit faire partie du même abonnement que votre plan App Service (ASP, App Service Plan).
* Les applications intégrées à un réseau virtuel utilisent le serveur DNS spécifié pour ce réseau virtuel.

### <a name="set-up-a-gateway-in-your-vnet"></a>Configurer une passerelle de votre réseau virtuel ###

Si vous avez déjà une passerelle configurée avec les adresses de point à site, vous pouvez passer à la configuration de l’intégration au réseau virtuel avec votre application.  
Pour créer une passerelle :

1. [Créez un sous-réseau de passerelle.][creategatewaysubnet] dans votre réseau virtuel.  

1. [Créez la passerelle VPN][creategateway]. Sélectionnez un type de VPN basé sur les routes.

1. [Définissez les adresses de point à site][setp2saddresses]. Si la passerelle n’est pas dans la référence (SKU) de base, c’est que IKEV2 doit être désactivé dans la configuration de point à site et que SSTP doit être sélectionné. L’espace d’adressage doit être dans les blocs d’adresses RFC 1918 10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16

Si vous êtes uniquement créer la passerelle pour utiliser avec l’intégration de réseau virtuel de App Service, vous n’avez pas besoin de télécharger un certificat. La création de la passerelle peut prendre 30 minutes. Vous ne serez pas en mesure d’intégrer votre application à votre réseau virtuel tant que la passerelle n’est pas provisionnée. 

### <a name="configure-vnet-integration-with-your-app"></a>Configurer l’intégration au réseau virtuel à votre application 

Pour activer l’intégration au réseau virtuel sur votre application : 

1. Accédez à votre application dans le portail Azure, ouvrez les paramètres de l’application et sélectionnez Réseau > Intégration au réseau virtuel. Votre ASP doit être dans une référence SKU Standard ou supérieure pour utiliser cette fonction intégration au réseau virtuel. 
 ![Interface utilisateur de l’intégration au réseau virtuel][1]

1. Sélectionnez **Ajouter un réseau virtuel**. 
 ![Ajouter une intégration au réseau virtuel][2]

1. Sélectionnez votre réseau virtuel. 
  ![Sélectionner votre réseau virtuel][8]
  
Votre application est redémarrée après cette dernière étape.  

### <a name="how-the-gateway-required-vnet-integration-feature-works"></a>Comment la passerelle requis la fonctionnalité d’intégration de réseau virtuel fonctionne

La passerelle requis la fonctionnalité d’intégration de réseau virtuel repose sur la technologie point-to-site VPN. La technologie de point à site limite l’accès réseau à la seule machine virtuelle hébergeant l’application. Les applications sont limitées au seul envoi du trafic vers Internet, via des connexions hybrides ou via l’intégration au réseau virtuel. 

![Fonctionnement de l’intégration au réseau virtuel][3]

## <a name="managing-vnet-integration"></a>Gestion de l’intégration de réseau virtuel
La possibilité de se connecter à un réseau virtuel et de s’en déconnecter se situe au niveau de l’application. Les opérations qui peuvent affecter l’intégration au réseau virtuel entre plusieurs applications s’effectuent au niveau du plan App Service. À partir de l’application > mise en réseau > portail de l’intégration au réseau virtuel, vous pouvez obtenir des détails sur votre réseau virtuel. Vous pouvez voir des informations similaires au niveau ASP dans la page ASP > mise en réseau > portail d’intégration au réseau virtuel, y compris les applications dans ce plan App Service utilisent une intégration donnée.

 ![Détails du réseau virtuel][4]

Les informations à votre disposition pour vous dans l’interface utilisateur d’intégration réseau virtuel est le même entre l’application et les portails ASP.

* Nom du réseau virtuel : liens vers l’interface utilisateur du réseau virtuel
* Emplacement : reflète l’emplacement de votre réseau virtuel. L’intégration à un réseau virtuel dans un autre emplacement peut entraîner des problèmes de latence pour votre application. 
* État du certificat : indique si vos certificats sont synchronisés entre votre plan App Service et votre réseau virtuel.
* État de la passerelle - devez vous être à l’aide de la passerelle requis intégration au réseau virtuel, vous pouvez voir l’état de la passerelle.
* Espace d’adressage du réseau virtuel : montre l’espace d’adressage IP pour votre réseau virtuel. 
* Espace d’adressage de point à site : montre l’espace d’adressage IP de point à site de votre réseau virtuel. Lors des appels vers votre réseau virtuel lors de l’utilisation de la fonctionnalité de passerelle requis, votre application FROM adresse sera une de ces adresses. 
* Espace d’adressage de site à site : vous pouvez utiliser des réseaux virtuels de site à site pour connecter votre réseau virtuel à vos ressources locales ou à un autre réseau virtuel. Les plages d’adresses IP définies avec cette connexion VPN sont montrées ici.
* Serveurs DNS : montre les serveurs DNS configurés avec votre réseau virtuel.
* Adresses IP routées vers le réseau virtuel : montre les blocs d’adresses routées utilisées pour piloter le trafic dans votre réseau virtuel 

La seule opération que vous pouvez effectuer dans la vue d’application de votre interface d’intégration au réseau virtuel consiste à déconnecter votre application du réseau virtuel auquel elle est actuellement connectée. Pour déconnecter votre application d’un réseau virtuel, sélectionnez **Déconnecter**. Votre application est redémarrée quand vous vous déconnectez d’un réseau virtuel. La déconnexion ne change pas votre réseau virtuel. Le sous-réseau ou la passerelle n’est pas supprimé. Si vous souhaitez ensuite supprimer votre réseau virtuel, vous devez tout d’abord vous déconnecter de votre application à partir du réseau virtuel et supprimer les ressources qu’il contient, telles que des passerelles. 

Pour atteindre l’interface utilisateur de l’intégration au réseau virtuel de votre plan App Service (ASP), ouvrez votre interface utilisateur ASP et sélectionnez **Réseau**.  Sous Intégration au réseau virtuel, sélectionnez **Cliquez ici pour configurer** pour ouvrir l’interface utilisateur de l’état de la fonctionnalité réseau.

![Informations sur l’intégration au réseau virtuel ASP][5]

L’interface utilisateur de l’intégration au réseau virtuel ASP vous montre tous les réseaux virtuels qui sont utilisés par les applications dans votre ASP. Pour voir des détails supplémentaires sur chaque réseau virtuel, cliquez sur le réseau virtuel qui vous intéresse. Vous pouvez effectuer ici deux actions.

* **Synchroniser le réseau** L’opération de réseau de synchronisation est uniquement pour la fonctionnalité d’intégration au réseau virtuel dépendante de passerelle. Exécution d’une opération de réseau de synchronisation permet de s’assurer que vos certificats et les informations de réseau sont synchronisés. Si vous ajoutez ou que vous changez le DNS du réseau virtuel, vous devez effectuer une opération **Synchroniser le réseau**. Cette opération redémarre toutes les applications utilisant ce réseau virtuel.
* **Ajouter des routes** : l’ajout de routes pilote le trafic sortant dans votre réseau virtuel.

**Routage** : les routes définies dans votre réseau virtuel sont utilisées pour diriger le trafic dans votre réseau virtuel à partir de votre application. Si vous devez envoyer du trafic sortant supplémentaire dans le réseau virtuel, vous pouvez ajouter ces blocs d’adresses ici. Ne fonctionne que Betacam avec passerelle requis intégration au réseau virtuel.

**Certificats** lorsque la passerelle requis intégration au réseau virtuel est activée, un échange est nécessaire de certificats pour garantir la sécurité de la connexion. En plus des certificats, la configuration DNS, les routes et d’autres éléments similaires décrivent le réseau.
Si des certificats ou des informations du réseau sont modifiés, vous devez cliquer sur « Synchroniser le réseau ». Quand vous cliquez sur « Synchroniser le réseau », la connectivité entre votre application et votre réseau virtuel est brièvement interrompue. Votre application n’est pas redémarrée, et la perte de connectivité peut altérer le fonctionnement correct de votre site. 

## <a name="accessing-on-premises-resources"></a>Accès aux ressources sur site
Les applications peuvent accéder aux ressources locales en s’intégrant à des réseaux virtuels qui ont des connexions site à site. Si vous utilisez la passerelle requis intégration au réseau virtuel, vous devez mettre à jour vos itinéraires de passerelle VPN en local avec vos blocs d’adresses de point-to-site. Lors de la configuration initiale de ce VPN site à site, les scripts utilisés pour le configurer doivent définir les routes correctement. Si vous ajoutez des adresses de point à site après avoir créé votre VPN de site à site, vous devez mettre à jour les routes manuellement. La procédure détaillée dépend de la passerelle et n’est pas décrite ici. Vous ne pouvez avoir BGP configuré avec une connexion VPN de site à site.

Il n’existe aucune configuration supplémentaire requise pour la fonctionnalité d’intégration au réseau virtuel régionale accéder via votre réseau virtuel et au niveau local. Vous devez simplement vous connecter votre réseau virtuel en local à l’aide d’ExpressRoute ou un VPN de site à site. 

> [!NOTE]
> La passerelle requis la fonctionnalité d’intégration de réseau virtuel n’intégrer une application à un réseau virtuel qui a une passerelle ExpressRoute. Même si la passerelle ExpressRoute est configurée en [mode de coexistence][VPNERCoex], l’intégration au réseau virtuel ne fonctionne pas. Si vous avez besoin d’accéder aux ressources via une connexion ExpressRoute, vous pouvez ensuite utiliser la fonctionnalité d’intégration au réseau virtuel régionale ou une [environnement App Service][ASE], qui s’exécute dans votre réseau virtuel. 
> 
> 

## <a name="peering"></a>Homologation
Si vous utilisez l’appairage avec l’intégration de réseau virtuel régional, il est inutile d’effectuer de configuration supplémentaire. 

Si vous utilisez la passerelle requis intégration au réseau virtuel avec l’homologation, vous devez configurer quelques éléments supplémentaires. Pour configurer l’appairage afin qu’il fonctionne avec votre application :

1. Ajoutez une connexion d’appairage sur le réseau virtuel auquel votre application se connecte. Lors de l’ajout de la connexion d’appairage, activez **Autoriser l’accès au réseau virtuel** et cochez **Autoriser le trafic transféré** et **Autoriser le transit par passerelle**.
1. Ajoutez une connexion d’appairage sur le réseau virtuel à appairer au réseau virtuel auquel vous êtes connecté. Lors de l’ajout de la connexion d’appairage sur le réseau virtuel de destination, activez **Autoriser l’accès au réseau virtuel** et cochez **Autoriser le trafic transféré** et **Autoriser les passerelles distantes**.
1. Accédez au plan App Service > Réseau > Interface utilisateur de l’intégration au réseau virtuel dans le portail.  Sélectionnez le réseau virtuel auquel votre application se connecte. Dans la section Routage, ajoutez la plage d’adresses du réseau virtuel qui est appairé au réseau virtuel auquel votre application est connectée.  


## <a name="pricing-details"></a>Détails de la tarification
La fonctionnalité d’intégration au réseau virtuel régionale a sans frais supplémentaires pour une utilisation au-delà de l’ASP que les frais de niveau de tarification.

Il existe trois frais associés à l’utilisation de la fonctionnalité d’intégration au réseau virtuel de passerelle requis :

* Frais niveau tarifaires ASP - vos applications doivent se trouver dans un Standard, Premium ou Plan de PremiumV2 App Service. Pour en savoir plus sur les coûts, consultez l’article : [Tarification App Service][ASPricing]. 
* Coûts de transfert de données - là est un frais pour la sortie de données, même si le réseau virtuel est dans le même centre de données. Ces coûts sont décrits dans [Détails de tarification des transferts de données][DataPricing]. 
* Les coûts de passerelle VPN - là est un coût à la passerelle de réseau virtuel qui est requis pour la connexion VPN point à site. Les détails se trouvent sur la page [Tarification des passerelles VPN][VNETPricing].


## <a name="troubleshooting"></a>Résolution de problèmes
Même si cette fonctionnalité est facile à configurer, il se peut que vous rencontriez certains problèmes. Si vous rencontrez des difficultés pour accéder au point de terminaison souhaité, certains utilitaires vous permettent de tester la connectivité à partir de la console de l’application. Vous pouvez utiliser deux consoles : la console Kudu et la console du portail Azure. Pour accéder à la console Kudu à partir de votre application, accédez à Outils -> Kudu. Vous pouvez également ouvrir la page [nom_site].scm.azurewebsites.net, puis accéder à l’onglet Console de débogage. Pour accéder à la console hébergée par le portail Azure, à partir de votre application, accédez à outils -> Console. 

#### <a name="tools"></a>Outils
Les outils **ping**, **nslookup** et **tracert** ne fonctionnent pas dans la console en raison de contraintes de sécurité. Deux outils distincts ont été ajoutés pour les remplacer. Pour tester les fonctionnalités DNS, nous avons ajouté un outil nommé nameresolver.exe. La syntaxe est :

    nameresolver.exe hostname [optional: DNS Server]

Vous pouvez utiliser **nameresolver** pour vérifier les noms d’hôte dont dépend votre application. De cette façon, vous pouvez tester si des éléments de votre serveur DNS sont mal configurés ou si vous n’avez pas accès à votre serveur DNS. Vous pouvez voir le serveur DNS que votre application va utiliser dans la console en examinant les variables d’environnement WEBSITE_DNS_SERVER et WEBSITE_DNS_ALT_SERVER.

L’outil suivant vous permet de tester la connectivité TCP avec une combinaison hôte/port. Il s’agit de l’outil **tcpping**, dont la syntaxe est la suivante :

    tcpping.exe hostname [optional: port]

L’utilitaire **tcpping** vous indique si vous pouvez atteindre un hôte et un port spécifiques. Il peut afficher un succès si une application à écoute sur la combinaison d’hôte et de port, et si l’accès réseau à de votre application à l’hôte et au port est disponible.

#### <a name="debugging-access-to-vnet-hosted-resources"></a>Débogage de l’accès aux ressources hébergées sur un réseau virtuel
Plusieurs choses peuvent empêcher votre application d’atteindre un hôte et un port spécifiques. La plupart du temps, il s’agit de l’une des trois raisons suivantes :

* **Un pare-feu se trouve sur le trajet.** Si vous utilisez un pare-feu sur le trajet, vous dépassez le délai d’expiration de TCP. Dans ce cas, il est de 21 secondes. Utilisez l’outil **tcpping** pour tester la connectivité. Les délais d’expiration TCP peuvent avoir de nombreuses autres origines, mais commencez par vérifier ce point. 
* **DNS n’est pas accessible.** Le délai d’expiration du DNS est de trois secondes par serveur DNS. Si vous avez deux serveurs DNS, le délai d’expiration est de 6 secondes. Utilisez nameresolver pour vérifier que le DNS fonctionne correctement. Rappelez-vous que vous ne pouvez pas utiliser nslookup, car il n’utilise pas le DNS avec lequel votre réseau virtuel est configuré. Si elle est inaccessible, vous pouvez avoir un pare-feu ou l’accès bloqué par groupe de sécurité réseau à DNS, ou il peut être arrêté.

Si ces éléments ne pas répondre à vos problèmes, commencez par les éléments tels que : 

**Intégration de réseau virtuel régional**
* est votre destination une adresse RFC 1918
* existe-t-il une sortie de blocage de groupe de sécurité réseau à partir de votre sous-réseau de l’intégration
* Si l’intention via un VPN ou ExpressRoute, votre passerelle locale est configuré pour acheminer le trafic sauvegarder sur Azure ? Si vous pouvez atteindre des points de terminaison dans votre réseau virtuel, mais pas en local, il est judicieux de vérifier.

**Passerelle requis intégration au réseau virtuel**
* est la plage d’adresses de point à site dans les plages d’adresses RFC 1918 (10.0.0.0-10.255.255.255 / 172.16.0.0-172.31.255.255 / 192.168.0.0-192.168.255.255) ?
* Le portail indique-t-il que la passerelle fonctionne ? Si votre passerelle est en panne, rétablissez-la.
* Certificats s’affichent comme étant synchronisés ou si vous pensez que la configuration du réseau a été modifiée ?  Si vos certificats ne sont pas synchronisés ou si vous pensez qu’il a été une modification apportée à votre configuration de réseau virtuel qui n’a pas été synchronisée avec vos ASP, puis appuyez sur « Synchronisation réseau ».
* Si l’intention via un VPN ou ExpressRoute, votre passerelle locale est configuré pour acheminer le trafic sauvegarder sur Azure ? Si vous pouvez atteindre des points de terminaison dans votre réseau virtuel, mais pas en local, il est judicieux de vérifier.

Résoudre les problèmes de mise en réseau de constitue un défi, car vous ne pouvez pas voir ce qui bloque l’accès à une combinaison hôte : port spécifique. Voici quelques-unes des causes possibles :

* Un pare-feu activé sur l’hôte empêche l’accès au port de l’application à partir de votre plage d’adresses IP de point à site. Des sous-réseaux croisés requièrent souvent un accès public.
* Votre hôte cible est hors-service.
* Votre application est arrêtée.
* L’IP ou le nom d’hôte est incorrect.
* Votre application est à l’écoute sur un port autre que celui que vous envisagiez. Vous pouvez faire correspondre votre ID de processus avec le port d’écoute en utilisant « netstat -aon » sur l’hôte du point de terminaison. 
* Les groupes de sécurité de votre réseau sont configurés de telle sorte qu’ils empêchent l’accès à l’hôte et au port de votre application à partir de votre plage d’adresses IP de point à site.

N’oubliez pas que vous ignorez quelle adresse de votre application utilise réellement. Il peut être n’importe quelle adresse dans la plage d’adresses intégration sous-réseau ou point-to-site, vous devez autoriser l’accès à partir de la plage d’adresses complète. 

Étapes de débogage supplémentaires :

* Connectez-vous à une machine virtuelle de votre réseau virtuel et essayez d’atteindre la combinaison hôte:port de vos ressources. Pour tester l’accès à TCP, utilisez la commande PowerShell **test-netconnection**. La syntaxe est :

      test-netconnection hostname [optional: -Port]

* Démarrez une application sur une machine virtuelle et testez l’accès à l’hôte et le port à partir de la console à partir de votre application en utilisant **tcpping**

#### <a name="on-premises-resources"></a>Ressources locales ####

Si votre application ne peut pas accéder à une ressource locale, vérifiez si vous pouvez atteindre la ressource à partir de votre réseau virtuel. Utilisez la commande PowerShell **test-netconnection** pour vérifier l’accès à TCP. Si votre machine virtuelle ne peut pas accéder à votre ressource locale, il se peut que votre connexion VPN ou ExpressRoute ne peut pas configurée correctement.

Si votre machine virtuelle hébergée sur le réseau virtuel peut atteindre votre système local, mais que votre application ne le peut pas, la raison en est probablement une des suivantes :

* vos itinéraires ne sont pas configurés avec votre sous-réseau ou pointent vers les plages d’adresses de site dans votre passerelle locale
* vos groupes de sécurité réseau bloquent l’accès de votre plage IP de point à site
* vos pare-feux locaux bloquent le trafic provenant de votre plage IP de point à site
* vous tentez d’atteindre une adresse non compatibles avec RFC 1918 à l’aide de la fonctionnalité d’intégration au réseau virtuel régionale


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
[creategatewaysubnet]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal#gatewaysubnet
[creategateway]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal#creategw
[setp2saddresses]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal#addresspool
