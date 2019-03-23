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
ms.date: 11/12/2018
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 768179f8569eac14166bcbb0a888e1cdbe41d497
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/22/2019
ms.locfileid: "58369698"
---
# <a name="integrate-your-app-with-an-azure-virtual-network"></a>Intégrer une application à un réseau Azure Virtual Network
Ce document décrit la fonctionnalité d’intégration au réseau virtuel d’Azure App Service et explique comment la configurer avec des applications dans [Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714). Les [réseaux virtuels Azure][VNETOverview] vous permettent de placer un grand nombre de vos ressources Azure dans un réseau routable non-Internet. Ces réseaux peuvent ensuite être connectés à vos réseaux locaux avec les technologies VPN. 

Azure App Service se présente sous deux formes. 

1. Les systèmes multilocataires qui prennent en charge l’ensemble des plans de tarification, excepté « Isolé »
2. L’environnement App Service, qui se déploie sur votre réseau virtuel. 

Ce document décrit la fonctionnalité d’intégration au réseau virtuel, qui est destinée à être utilisée dans le service App Service multilocataire.  Si votre application se trouve dans l’[environnement App Service][ASEintro], elle est déjà dans un réseau virtuel et ne nécessite pas l’utilisation de la fonctionnalité d’intégration au réseau virtuel pour accéder aux ressources du même réseau virtuel.

L’intégration au réseau virtuel permet à votre application web d’accéder aux ressources de votre réseau virtuel, mais n’accorde pas d’accès privé à votre application web à partir du réseau virtuel. L’accès au site privé fait référence au fait de rendre votre application accessible uniquement à partir d’un réseau privé, par exemple à partir d’un réseau virtuel Azure. L’accès privé aux sites est disponible uniquement via un ASE configuré avec un équilibreur de charge interne (ILB). Pour en savoir plus sur l’utilisation d’un ILB ASE, commencez par lire l’article : [Création et utilisation d’un ASE ILB][ILBASE]. 

L’intégration au réseau virtuel est souvent utilisée pour permettre l’accès depuis des applications à des bases de données et à des services web qui s’exécutent dans votre réseau virtuel. Avec l’intégration au réseau virtuel, vous n’avez pas besoin d’exposer un point de terminaison public pour les applications sur votre machine virtuelle, mais vous pouvez utiliser à la place des adresses privées routables non-Internet. 

La fonctionnalité d’intégration au réseau virtuel :

* nécessite un plan de tarification Standard, Premium ou PremiumV2 
* fonctionne avec le réseau virtuel classique ou Gestionnaire des ressources 
* prend en charge les protocoles TCP et UDP ;
* fonctionne avec les applications API, web, mobiles et les applications de fonction
* permet à une application de se connecter à un seul réseau virtuel à la fois
* permet d’intégrer jusqu’à cinq réseaux virtuels dans un plan App Service 
* permet l’utilisation du même réseau virtuel par plusieurs applications d’un plan App Service
* nécessite une passerelle de réseau virtuel configurée avec le VPN de point à site
* prend en charge un contrat SLA de 99,9 % grâce au contrat SLA sur la passerelle de réseau virtuel

L’intégration au réseau virtuel ne prend pas en charge certaines choses, notamment :

* montage d’un lecteur ;
* intégration AD ; 
* NetBios ;
* accès privé aux sites.
* accès à des ressources via ExpressRoute 
* accès à des ressources via des point de terminaison de service 

### <a name="getting-started"></a>Prise en main
Voici quelques informations à garder à l’esprit avant de connecter votre application web à un réseau virtuel.

* Un réseau virtuel cible doit prendre en charge la connexion VPN de point à site avec une passerelle de routage avant de pouvoir être connecté à une application. 
* Le réseau virtuel doit faire partie du même abonnement que votre plan App Service (ASP, App Service Plan).
* Les applications intégrées à un réseau virtuel utilisent le serveur DNS spécifié pour ce réseau virtuel.

## <a name="enabling-vnet-integration"></a>Activation de l’intégration au réseau virtuel

Il existe une nouvelle version de la fonctionnalité d’intégration au réseau virtuel qui est en préversion. Elle ne dépend pas du réseau virtuel point à site, et prend également en charge l’accès aux ressources via ExpressRoute ou via des points de terminaison de service. Pour plus d’informations sur la nouvelle fonctionnalité en préversion, accédez à la fin de ce document. 

### <a name="set-up-a-gateway-in-your-vnet"></a>Configurer une passerelle de votre réseau virtuel ###

Si vous avez déjà une passerelle configurée avec les adresses de point à site, vous pouvez passer à la configuration de l’intégration au réseau virtuel avec votre application.  
Pour créer une passerelle :

1. [Créez un sous-réseau de passerelle.][creategatewaysubnet] dans votre réseau virtuel.  

1. [Créez la passerelle VPN][creategateway]. Sélectionnez un type de VPN basé sur les routes.

1. [Définissez les adresses de point à site][setp2saddresses]. Si la passerelle n’est pas dans la référence (SKU) de base, c’est que IKEV2 doit être désactivé dans la configuration de point à site et que SSTP doit être sélectionné. L’espace d’adressage doit être dans un des blocs d’adresses suivants :

* 10.0.0.0/8 : ceci signifie une plage d’adresses IP de 10.0.0.0 à 10.255.255.255
* 172.16.0.0/12 : ceci signifie une plage d’adresses IP de 172.16.0.0 à 172.31.255.255 
* 192.168.0.0/16 : ceci signifie une plage d’adresses IP de 192.168.0.0 à 192.168.255.255

Si vous êtes uniquement créer la passerelle pour utiliser avec l’intégration de réseau virtuel de App Service, vous n’avez pas besoin de télécharger un certificat. La création de la passerelle peut prendre 30 minutes. Vous ne serez pas en mesure d’intégrer votre application à votre réseau virtuel tant que la passerelle n’est pas provisionnée. 

### <a name="configure-vnet-integration-with-your-app"></a>Configurer l’intégration au réseau virtuel à votre application ###

Pour activer l’intégration au réseau virtuel sur votre application : 

1. Accédez à votre application dans le portail Azure, ouvrez les paramètres de l’application et sélectionnez Réseau > Intégration au réseau virtuel. Mettez à l’échelle votre plan App Service vers une référence (SKU) Standard ou supérieure pour pouvoir configurer l’intégration au réseau virtuel. 
 ![Interface utilisateur de l’intégration au réseau virtuel][1]

1. Sélectionnez **Ajouter un réseau virtuel**. 
 ![Ajouter une intégration au réseau virtuel][2]

1. Sélectionnez votre réseau virtuel. 
  ![Sélectionner votre réseau virtuel][8]
  
Votre application est redémarrée après cette dernière étape.  

## <a name="how-the-system-works"></a>Fonctionnement du système
La fonctionnalité d’intégration au réseau virtuel utilise la technologie des réseaux virtuels point à site. Les applications dans Azure App Service sont hébergées sur un système multilocataire qui empêche le provisionnement direct d’une application dans un réseau virtuel. La technologie de point à site limite l’accès réseau à la seule machine virtuelle hébergeant l’application. Les applications sont limitées au seul envoi du trafic vers Internet, via des connexions hybrides ou via l’intégration au réseau virtuel. 

![Fonctionnement de l’intégration au réseau virtuel][3]

## <a name="managing-the-vnet-integrations"></a>Gestion des intégrations au réseau virtuel
La possibilité de se connecter à un réseau virtuel et de s’en déconnecter se situe au niveau de l’application. Les opérations qui peuvent affecter l’intégration au réseau virtuel entre plusieurs applications s’effectuent au niveau du plan App Service. À partir de l’UID de l’application, vous pouvez obtenir des détails sur votre réseau virtuel. Les mêmes informations apparaissent également au niveau du plan App Service. 

 ![Détails du réseau virtuel][4]

Sur la page État de la fonctionnalité réseau, vous pouvez voir si votre application est connectée à votre réseau virtuel. Si votre passerelle de réseau virtuel est à l’arrêt pour une raison quelconque, votre état indique « non connecté ». 

Les informations maintenant à votre disposition dans l’interface utilisateur d’intégration au réseau virtuel au niveau de l’application sont identiques aux informations détaillées que vous obtenez à partir de l’ASP. Il s’agit des informations suivantes :

* Nom du réseau virtuel : liens vers l’interface utilisateur du réseau virtuel
* Emplacement : reflète l’emplacement de votre réseau virtuel. L’intégration à un réseau virtuel dans un autre emplacement peut entraîner des problèmes de latence pour votre application. 
* État du certificat : indique si vos certificats sont synchronisés entre votre plan App Service et votre réseau virtuel.
* État de la passerelle - si vos passerelles sont en panne pour une raison quelconque, votre application ne peut pas accéder aux ressources du réseau virtuel. 
* Espace d’adressage du réseau virtuel : montre l’espace d’adressage IP pour votre réseau virtuel. 
* Espace d’adressage de point à site : montre l’espace d’adressage IP de point à site de votre réseau virtuel. Lors des appels dans votre réseau virtuel, l’adresse FROM de votre application est une de ces adresses. 
* Espace d’adressage de site à site : vous pouvez utiliser des réseaux virtuels de site à site pour connecter votre réseau virtuel à vos ressources locales ou à un autre réseau virtuel. Les plages d’adresses IP définies avec cette connexion VPN sont montrées ici.
* Serveurs DNS : montre les serveurs DNS configurés avec votre réseau virtuel.
* Adresses IP routées vers le réseau virtuel : montre les blocs d’adresses routées utilisées pour piloter le trafic dans votre réseau virtuel 

La seule opération que vous pouvez effectuer dans la vue d’application de votre interface d’intégration au réseau virtuel consiste à déconnecter votre application du réseau virtuel auquel elle est actuellement connectée. Pour déconnecter votre application d’un réseau virtuel, sélectionnez **Déconnecter**. Votre application est redémarrée quand vous vous déconnectez d’un réseau virtuel. La déconnexion ne change pas votre réseau virtuel. Le réseau virtuel et sa configuration, notamment les passerelles, demeurent inchangés. Si vous souhaitez ensuite supprimer votre réseau virtuel, vous devez d’abord supprimer les ressources qu’il contient, y compris les passerelles. 

Pour atteindre l’interface utilisateur de l’intégration au réseau virtuel de votre plan App Service (ASP), ouvrez votre interface utilisateur ASP et sélectionnez **Réseau**.  Sous Intégration au réseau virtuel, sélectionnez **Cliquez ici pour configurer** pour ouvrir l’interface utilisateur de l’état de la fonctionnalité réseau.

![Informations sur l’intégration au réseau virtuel ASP][5]

L’interface utilisateur de l’intégration au réseau virtuel ASP vous montre tous les réseaux virtuels qui sont utilisés par les applications dans votre ASP. Vous pouvez avoir jusqu’à 5 réseaux virtuels auxquels un nombre quelconque d’applications peuvent être connectées dans votre plan App Service. Vous ne pouvez configurer qu’une seule intégration pour chaque application. Pour voir des détails supplémentaires sur chaque réseau virtuel, cliquez sur le réseau virtuel qui vous intéresse. Vous pouvez effectuer ici deux actions.

* **Synchroniser le réseau** L’opération de synchronisation du réseau permet de garantir que vos certificats et les informations du réseau sont synchronisés. Si vous ajoutez ou que vous changez le DNS du réseau virtuel, vous devez effectuer une opération **Synchroniser le réseau**. Cette opération redémarre toutes les applications utilisant ce réseau virtuel.
* **Ajouter des routes** : l’ajout de routes pilote le trafic sortant dans votre réseau virtuel.

**Routage** : les routes définies dans votre réseau virtuel sont utilisées pour diriger le trafic dans votre réseau virtuel à partir de votre application. Si vous devez envoyer du trafic sortant supplémentaire dans le réseau virtuel, vous pouvez ajouter ces blocs d’adresses ici.   

**Certificats** : quand l’intégration au réseau virtuel est activée, un échange de certificats est nécessaire pour garantir la sécurité de la connexion. En plus des certificats, la configuration DNS, les routes et d’autres éléments similaires décrivent le réseau.
Si des certificats ou des informations du réseau sont modifiés, vous devez cliquer sur « Synchroniser le réseau ». Quand vous cliquez sur « Synchroniser le réseau », la connectivité entre votre application et votre réseau virtuel est brièvement interrompue. Votre application n’est pas redémarrée, et la perte de connectivité peut altérer le fonctionnement correct de votre site. 

## <a name="accessing-on-premises-resources"></a>Accès aux ressources sur site
Les applications peuvent accéder aux ressources locales en s’intégrant à des réseaux virtuels qui ont des connexions site à site. Pour accéder à des ressources locales, vous devez mettre à jour les routes de votre passerelle VPN locale avec vos blocs d’adresses de point à site. Lors de la configuration initiale de ce VPN site à site, les scripts utilisés pour le configurer doivent définir les routes correctement. Si vous ajoutez des adresses de point à site après avoir créé votre VPN de site à site, vous devez mettre à jour les routes manuellement. La procédure détaillée dépend de la passerelle et n’est pas décrite ici. En outre, vous ne pouvez pas configurer BGP avec une connexion VPN de site à site.

> [!NOTE]
> La fonctionnalité d’intégration au réseau virtuel n’intègre pas une application à un réseau virtuel qui a une passerelle ExpressRoute. Même si la passerelle ExpressRoute est configurée en [mode de coexistence][VPNERCoex], l’intégration au réseau virtuel ne fonctionne pas. Si vous avez besoin d’accéder aux ressources via une connexion ExpressRoute, vous pouvez utiliser un [App Service Environment][ASE] s’exécutant dans votre réseau virtuel. 
> 
> 

## <a name="peering"></a>Homologation
Vous pouvez utiliser l’intégration au réseau virtuel pour accéder à des ressources dans des réseaux virtuels qui sont appairés au réseau virtuel auquel vous êtes connecté. Pour configurer l’appairage afin qu’il fonctionne avec votre application :

1. Ajoutez une connexion d’appairage sur le réseau virtuel auquel votre application se connecte. Lors de l’ajout de la connexion d’appairage, activez **Autoriser l’accès au réseau virtuel** et cochez **Autoriser le trafic transféré** et **Autoriser le transit par passerelle**.
1. Ajoutez une connexion d’appairage sur le réseau virtuel à appairer au réseau virtuel auquel vous êtes connecté. Lors de l’ajout de la connexion d’appairage sur le réseau virtuel de destination, activez **Autoriser l’accès au réseau virtuel** et cochez **Autoriser le trafic transféré** et **Autoriser les passerelles distantes**.
1. Accédez au plan App Service > Réseau > Interface utilisateur de l’intégration au réseau virtuel dans le portail.  Sélectionnez le réseau virtuel auquel votre application se connecte. Dans la section Routage, ajoutez la plage d’adresses du réseau virtuel qui est appairé au réseau virtuel auquel votre application est connectée.  


## <a name="pricing-details"></a>Détails de la tarification
Trois types de coûts sont associés à l’utilisation de la fonctionnalité d’intégration au réseau virtuel :

* exigences liées au niveau tarifaire de l’ASP ;
* coût de transfert des données ;
* coûts de la passerelle VPN.

Vos applications doivent se trouver dans un plan App Service Standard, Premium ou PremiumV2. Pour en savoir plus sur les coûts, consultez l’article : [Tarification App Service][ASPricing]. 

Les sorties de données engendrent des coûts, même si le réseau virtuel est dans le même centre de données. Ces coûts sont décrits dans [Détails de tarification des transferts de données][DataPricing]. 

La passerelle de réseau virtuel nécessaire pour la connexion VPN de point à site engendre un coût. Les détails se trouvent sur la page [Tarification des passerelles VPN][VNETPricing].

## <a name="troubleshooting"></a>Résolution de problèmes
Même si cette fonctionnalité est facile à configurer, il se peut que vous rencontriez certains problèmes. Si vous rencontrez des difficultés pour accéder au point de terminaison souhaité, certains utilitaires vous permettent de tester la connectivité à partir de la console de l’application. Vous pouvez utiliser deux consoles : la console Kudu et la console du portail Azure. Pour accéder à la console Kudu à partir de votre application, accédez à Outils -> Kudu. Vous pouvez également ouvrir la page [nom_site].scm.azurewebsites.net, puis accéder à l’onglet Console de débogage. Pour accéder à la console hébergée par le portail Azure, à partir de votre application, accédez à outils -> Console. 

#### <a name="tools"></a>Outils
Les outils **ping**, **nslookup** et **tracert** ne fonctionnent pas dans la console en raison de contraintes de sécurité. Deux outils distincts ont été ajoutés pour les remplacer. Pour tester les fonctionnalités DNS, nous avons ajouté un outil nommé nameresolver.exe. La syntaxe est :

    nameresolver.exe hostname [optional: DNS Server]

Vous pouvez utiliser **nameresolver** pour vérifier les noms d’hôte dont dépend votre application. De cette façon, vous pouvez tester si des éléments de votre serveur DNS sont mal configurés ou si vous n’avez pas accès à votre serveur DNS.

L’outil suivant vous permet de tester la connectivité TCP avec une combinaison hôte/port. Il s’agit de l’outil **tcpping**, dont la syntaxe est la suivante :

    tcpping.exe hostname [optional: port]

L’utilitaire **tcpping** vous indique si vous pouvez atteindre un hôte et un port spécifiques. Il peut afficher un succès si une application à écoute sur la combinaison d’hôte et de port, et si l’accès réseau à de votre application à l’hôte et au port est disponible.

#### <a name="debugging-access-to-vnet-hosted-resources"></a>Débogage de l’accès aux ressources hébergées sur un réseau virtuel
Plusieurs choses peuvent empêcher votre application d’atteindre un hôte et un port spécifiques. La plupart du temps, il s’agit de l’une des trois raisons suivantes :

* **Un pare-feu se trouve sur le trajet.** Si vous utilisez un pare-feu sur le trajet, vous dépassez le délai d’expiration de TCP. Dans ce cas, il est de 21 secondes. Utilisez l’outil **tcpping** pour tester la connectivité. Les délais d’expiration TCP peuvent avoir de nombreuses autres origines, mais commencez par vérifier ce point. 
* **DNS n’est pas accessible.** Le délai d’expiration du DNS est de trois secondes par serveur DNS. Si vous avez deux serveurs DNS, le délai d’expiration est de 6 secondes. Utilisez nameresolver pour vérifier que le DNS fonctionne correctement. Rappelez-vous que vous ne pouvez pas utiliser nslookup, car il n’utilise pas le DNS avec lequel votre réseau virtuel est configuré.
* **La plage d’adresses de point à site n’est pas valide.** La plage d’adresses IP de point à site doit se trouver dans les plages d’IP privées RFC 1918 (10.0.0.0-10.255.255.255 / 172.16.0.0-172.31.255.255 / 192.168.0.0-192.168.255.255). Si la plage utilise des adresses IP en dehors de cette plage, cela ne fonctionnera pas. 

Si ces réponses ne résolvent pas votre problème, commencez par répondre aux questions simples ci-dessous : 

* Le portail indique-t-il que la passerelle fonctionne ?
* Les certificats s’affichent comme étant synchronisés ?
* Un utilisateur a-t-il modifié la configuration du réseau sans effectuer de « Synchronisation réseau » dans les ASP affectés ? 

Si votre passerelle est en panne, rétablissez-la. Si vos certificats ne sont pas synchronisés, accédez à la vue ASP de votre interface d’intégration au réseau virtuel et appuyez sur « Synchronisation réseau ». Si vous pensez qu’une modification apportée à votre configuration de réseau virtuel n’a pas été synchronisée avec vos ASP, cliquez sur « Synchroniser le réseau ».  Une opération « Synchroniser le réseau » redémarre toutes les applications de l’ASP qui sont intégrées à ce réseau virtuel. 

Si tous ces aspects sont corrects, vous devez approfondir vos recherches :

* Existe-t-il d’autres applications utilisant l’intégration de réseau virtuel pour accéder à des ressources dans le même réseau virtuel ? 
* Pouvez-vous ouvrir la console de l’application et utiliser tcpping pour accéder à d’autres ressources de votre réseau virtuel ? 

Si l’un de ces aspects est vérifié, votre intégration au réseau virtuel est correcte, et le problème se situe ailleurs. La situation est alors plus complexe car il n’existe aucun moyen simple de comprendre pourquoi vous ne pouvez pas atteindre une combinaison hôte:port. Voici quelques-unes des causes possibles :

* Un pare-feu activé sur l’hôte empêche l’accès au port de l’application à partir de votre plage d’adresses IP de point à site. Des sous-réseaux croisés requièrent souvent un accès public.
* Votre hôte cible est hors-service.
* Votre application est arrêtée.
* L’IP ou le nom d’hôte est incorrect.
* Votre application est à l’écoute sur un port autre que celui que vous envisagiez. Vous pouvez faire correspondre votre ID de processus avec le port d’écoute en utilisant « netstat -aon » sur l’hôte du point de terminaison. 
* Les groupes de sécurité de votre réseau sont configurés de telle sorte qu’ils empêchent l’accès à l’hôte et au port de votre application à partir de votre plage d’adresses IP de point à site.

N’oubliez pas que vous ne connaissez pas l’adresse IP de la plage d’adresses IP de point à site qui sera utilisée par votre application. Vous devez donc autoriser l’accès à partir de la plage entière. 

Étapes de débogage supplémentaires :

* Connectez-vous à une machine virtuelle de votre réseau virtuel et essayez d’atteindre la combinaison hôte:port de vos ressources. Pour tester l’accès à TCP, utilisez la commande PowerShell **test-netconnection**. La syntaxe est :

      test-netconnection hostname [optional: -Port]

* Démarrez une application sur une machine virtuelle et testez l’accès à cet hôte et au port à partir de la console de votre application.

#### <a name="on-premises-resources"></a>Ressources locales ####

Si votre application ne peut pas accéder à une ressource locale, vérifiez si vous pouvez atteindre la ressource à partir de votre réseau virtuel. Utilisez la commande PowerShell **test-netconnection** pour vérifier l’accès à TCP. Si votre machine virtuelle ne peut pas accéder à votre ressource locale, vérifiez que votre connexion VPN de site à site fonctionne. Si c’est le cas, vérifiez les éléments indiqués précédemment, ainsi que la configuration et l’état de la passerelle locale. 

Si votre machine virtuelle hébergée sur le réseau virtuel peut atteindre votre système local, mais que votre application ne le peut pas, la raison en est probablement une des suivantes :

* vos itinéraires ne sont pas configurés avec vos plages IP de point à site dans votre passerelle locale
* vos groupes de sécurité réseau bloquent l’accès de votre plage IP de point à site
* vos pare-feux locaux bloquent le trafic provenant de votre plage IP de point à site


## <a name="powershell-automation"></a>Automation PowerShell

Vous pouvez intégrer App Service à un réseau virtuel Azure à l’aide de PowerShell. Pour obtenir un script prêt à l’exécution, consultez [Connect an app in Azure App Service to an Azure Virtual Network](https://gallery.technet.microsoft.com/scriptcenter/Connect-an-app-in-Azure-ab7527e3).

## <a name="hybrid-connections-and-app-service-environments"></a>Connexions hybrides et environnements App Service
Trois fonctionnalités permettent d’accéder aux ressources hébergées sur le réseau virtuel. Il s'agit de :

* Intégration au réseau virtuel
* les connexions hybrides
* Environnements App Service

Pour les connexions hybrides, vous devez installer l’agent de relais Gestionnaire de connexion hybride (HCM, Hybrid Connection Manager) sur votre réseau. HCM doit pouvoir se connecter à Azure et à votre application. Les connexions hybrides ne nécessitent pas un point de terminaison accessible par des connexions Internet entrantes pour votre réseau distant, comme c’est le cas pour une connexion VPN. HCM s’exécute uniquement sous Windows. Vous pouvez exécuter jusqu’à cinq instances pour bénéficier d’une haute disponibilité. Cependant, les connexions hybrides prennent en charge uniquement le protocole TCP, et chaque point de terminaison de connexion hybride doit correspondre à une combinaison hôte:port spécifique. 

Avec un environnement App Service, vous pouvez exécuter une instance avec un seul locataire d’Azure App Service dans votre réseau virtuel. Si vos applications se trouvent dans un environnement App Service, vos applications peuvent accéder à des ressources de votre réseau virtuel sans étapes supplémentaires. Avec un environnement App Service vos applications s’exécutent sur des travailleurs plus puissantes et peuvent évoluer jusqu'à 100 instances ASP. Les environnements App Service fonctionnent avec toutes les fonctionnalités réseau, y compris ExpressRoute et les points de terminaison de service.  

Même si parfois ces deux fonctionnalités se chevauchent, l’une ne remplace pas l’autre. Le choix de la fonctionnalité à utiliser dépend de vos besoins. Par exemple : 

* Si vous êtes développeur, et que vous voulez exécuter un site dans Azure qui peut accéder à la base de données sur votre station de travail locale, la solution la plus simple consiste à utiliser des connexions hybrides. 
* Si vous êtes une organisation de grande envergure et que vous souhaitez placer un grand nombre de propriétés web dans le cloud public et les gérer dans votre propre réseau, il est préférable de recourir à un environnement App Service. 
* Si vous avez plusieurs applications qui doivent accéder aux ressources de votre réseau virtuel, choisissez l’option d’intégration au réseau virtuel. 

Quand votre réseau virtuel est déjà connecté à votre réseau local, l’utilisation de l’intégration au réseau virtuel ou d’un environnement App Service est un moyen simple de consommer des ressources locales. Si votre réseau virtuel n’est pas connecté à votre réseau local, la configuration d’un réseau VPN de site à site avec votre réseau virtuel est beaucoup plus complexe que l’installation de HCM. 

Outre les différences fonctionnelles, il existe également des différences de tarification. L’environnement App Service est un service Premium offrant des possibilités de configuration de réseau inégalées et des fonctionnalités remarquables. L’intégration au réseau virtuel peut être utilisée avec des plans App Service Standard ou Premium. Elle est idéale pour la consommation sécurisée des ressources dans votre réseau virtuel à partir du système App Service mutualisé. Actuellement, les connexions hybrides dépendent d’un compte BizTalk dont les niveaux de tarification sont variables : une solution gratuite est proposée, puis le tarif augmente progressivement selon le nombre de connexions requises. Cependant, si vous devez utiliser de nombreux réseaux, les connexions hybrides représentent la solution idéale : elles vous permettent d’accéder aux ressources de plus de 100 réseaux distincts. 

## <a name="new-vnet-integration"></a>Nouvelle intégration au réseau virtuel ##

Il existe une nouvelle version de la fonctionnalité d’intégration au réseau virtuel qui ne dépend pas de la technologie VPN de point à site. Contrairement à la fonctionnalité qui existait auparavant, la nouvelle fonctionnalité en préversion fonctionne avec ExpressRoute et avec les points de terminaison de service. 

La nouvelle fonctionnalité est uniquement disponible à partir d’unités d’échelle Azure App Service plus récentes. Si vous pouvez effectuer une mise à l’échelle vers PremiumV2, vous vous trouvez dans une unité d’échelle App Service plus récente. L’interface utilisateur de l’intégration au réseau virtuel dans le portail vous indique si votre application peut utiliser la nouvelle fonctionnalité d’intégration au réseau virtuel. 

La nouvelle version est une préversion et elle a les caractéristiques suivantes.

* Aucune passerelle n’est nécessaire pour utiliser la nouvelle fonctionnalité d’intégration au réseau virtuel
* Vous pouvez accéder à des ressources via des connexions ExpressRoute sans configuration supplémentaire au-delà de l’intégration du réseau virtuel connecté à ExpressRoute.
* L’application et le réseau virtuel doivent être dans la même région.
* La nouvelle fonctionnalité nécessite un sous-réseau non utilisé dans votre réseau virtuel Resource Manager.
* Votre plan App Service doit être un plan Standard, Premium ou PremiumV2.
* Les charges de travail de production ne sont pas prises en charge sur la nouvelle fonctionnalité tant qu’elle est en préversion.
* Votre application doit être dans un déploiement Azure App Service qui est capable de passer à Premium v2.
* La nouvelle fonctionnalité d’intégration au réseau virtuel ne fonctionne pas pour les applications dans un environnement App Service.
* Vous ne pouvez pas supprimer un réseau virtuel avec une application intégrée.  
* Les tables de routage et l’appairage global ne sont pas encore disponibles avec la nouvelle intégration au réseau virtuel.  
* Une adresse est utilisée pour chaque instance du plan App Service. Comme vous ne pouvez pas changer la taille du sous-réseau après l’avoir affectée, utilisez un sous-réseau qui peut couvrir plus que votre taille d’échelle maximale. La taille /27 avec 32 adresses est la taille recommandée car elle permet de gérer un plan App Service mis à l’échelle avec 20 instances.
* Vous pouvez consommer des ressources sécurisées de point de terminaison de service en utilisant la nouvelle fonctionnalité d’intégration au réseau virtuel. Pour cela, activez des points de terminaison de service sur le sous-réseau utilisé pour l’intégration au réseau virtuel.

Pour utiliser la nouvelle fonctionnalité :

1. Accédez à l’interface utilisateur Réseau dans le portail. Si votre application peut utiliser la nouvelle fonctionnalité, vous voyez cette possibilité pour la préversion.  

   ![Sélectionner la nouvelle préversion de l’intégration au réseau virtuel][6]

1. Sélectionnez **Ajouter un réseau virtuel (préversion)**.  

1. Sélectionnez le réseau virtuel Resource Manager auquel vous voulez vous intégrer, puis créez un sous-réseau ou choisissez un sous-réseau vide préexistant. L’intégration prend moins d’une minute. Lors de l’intégration, votre application est redémarrée.  Une fois l’intégration terminée, vous voyez des détails sur le réseau virtuel auquel vous êtes intégré et une bannière en haut qui vous indique que la fonctionnalité est en préversion.

   ![Sélectionner le réseau virtuel et le sous-réseau][7]

Pour permettre l’utilisation par votre application du serveur DNS avec lequel votre réseau virtuel est configuré, créez un paramètre d’application pour votre application, où le nom est WEBSITE_DNS_SERVER et où la valeur est l’adresse IP du serveur.  Si vous avez un serveur DNS secondaire, créez un autre paramètre d’application, où le nom est WEBSITE_DNS_ALT_SERVER et où la valeur est l’adresse IP du serveur. 

Pour déconnecter votre application du réseau virtuel, sélectionnez **Déconnecter**. Ceci redémarre votre application web. 

La nouvelle fonctionnalité d’intégration au réseau virtuel vous permet d’utiliser des points de terminaison de service.  Pour utiliser des points de terminaison de service avec votre application, utilisez la nouvelle intégration au réseau virtuel pour vous connecter à un réseau virtuel sélectionné, puis configurez des points de terminaison de service sur le sous-réseau que vous avez utilisé pour l’intégration. 

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
