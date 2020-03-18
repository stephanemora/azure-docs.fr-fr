---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 02/27/2020
ms.author: ccompy
ms.openlocfilehash: c731e2a7d4da1a66aabb50b335d526fbb6a0a302
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78671520"
---
L’utilisation de l’intégration au réseau virtuel régional permet à votre application d’accéder aux :

* Ressources du réseau virtuel dans la même région que celle à laquelle vous êtes intégré 
* Ressources des réseaux virtuels appairés à votre réseau virtuel, et qui se trouvent dans la même région
* Services sécurisés du point de terminaison de service
* Ressources via les connexions ExpressRoute
* Ressources du réseau virtuel auquel vous êtes connecté
* Ressources via des connexions appairées, notamment les connexions ExpressRoute
* Points de terminaison privés 

Lorsque vous utilisez l’intégration au réseau virtuel avec des réseaux virtuels d’une même région, vous pouvez utiliser les fonctionnalités Azure Networking suivantes :

* Groupes de sécurité réseau (NSG) : vous pouvez bloquer le trafic sortant avec un groupe de sécurité réseau placé sur votre sous-réseau d’intégration. Les règles de trafic entrant ne s’appliquent pas, car vous ne pouvez pas utiliser l’intégration au réseau virtuel pour fournir un accès entrant à votre application.
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
* L’intégration ne peut se faire qu’avec des réseaux virtuels figurant dans le même abonnement que l’application
* Vous ne pouvez disposer que d’une seule intégration au réseau virtuel régional par plan App Service. Plusieurs applications d’un même plan App Service peuvent utiliser le même réseau virtuel. 
* Vous ne pouvez pas modifier l’abonnement d’une application ou d’un plan quand une application utilise l’intégration au réseau virtuel régional

Une adresse est utilisée pour chaque instance du plan. Si vous mettez votre application à l’échelle vers cinq instances, cinq adresses sont utilisées. Étant donné que la taille du sous-réseau ne peut pas être modifiée après l’affectation, vous devez utiliser un sous-réseau suffisamment grand pour s’adapter à la taille que votre application est susceptible d’atteindre. Une taille de /26 avec 64 adresses est recommandée. Un sous-réseau /26 avec 64 adresses contient un plan Premium avec 30 instances. Lorsque vous modifiez un plan à la hausse ou à la baisse, vous avez brièvement besoin de deux fois plus d’adresses. 

Si vous souhaitez que vos applications d’un autre plan atteignent un réseau virtuel auquel sont déjà connectées des applications d’un autre plan, vous devez sélectionner un sous-réseau différent de celui utilisé par l’intégration au réseau virtuel préexistante.  

La fonctionnalité est en préversion pour Linux. Le formulaire Linux de la fonctionnalité prend uniquement en charge les appels aux adresses RFC 1918 (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16).

### <a name="web--function-app-for-containers"></a>Web App/Function App pour conteneurs

Si vous hébergez votre application sur Linux avec les images intégrées, l’intégration au réseau virtuel régional fonctionne sans modification supplémentaire. Si vous utilisez Web App/Function App pour conteneurs, vous devez modifier votre image Docker pour pouvoir utiliser l’intégration au réseau virtuel. Dans votre image docker, utilisez la variable d’environnement PORT comme port d’écoute du serveur web principal, au lieu d’utiliser un numéro de port codé en dur. La variable d’environnement PORT est automatiquement définie par la plateforme au démarrage du conteneur. Si vous utilisez SSH, le démon SSH doit être configuré pour écouter sur le numéro de port spécifié par la variable d’environnement SSH_PORT lors de l’utilisation de l’intégration au réseau virtuel régional.  Il n’existe pas de prise en charge de l’intégration au réseau virtuel avec passerelle obligatoire sur Linux. 

### <a name="service-endpoints"></a>Points de terminaison de service

L’intégration au réseau virtuel régional vous permet d’utiliser des points de terminaison de service.  Pour utiliser des points de terminaison de service avec votre application, servez-vous de l’intégration au réseau virtuel régional pour vous connecter à un réseau virtuel sélectionné, puis configurez des points de terminaison de service sur le sous-réseau que vous avez utilisé pour l’intégration. 

### <a name="network-security-groups"></a>Network Security Group

Les groupes de sécurité réseau (NSG) vous permettent de bloquer les trafics entrant et sortant sur les ressources d’un réseau virtuel. Une application utilisant l’intégration au réseau virtuel régional peut utiliser un [Groupe de sécurité réseau][VNETnsg] pour bloquer le trafic sortant vers des ressources dans votre réseau virtuel ou Internet. Pour bloquer le trafic vers des adresses publiques, le paramètre d’application WEBSITE_VNET_ROUTE_ALL doit être défini sur 1. Les règles de trafic entrant dans un groupe de sécurité réseau ne s’appliquent pas à votre application, car l’intégration au réseau virtuel n’a d’incidence que sur le trafic sortant depuis votre application. Pour contrôler le trafic entrant vers votre application, utilisez la fonctionnalité Restrictions d’accès. Un groupe de sécurité réseau appliqué à votre sous-réseau d’intégration sera actif quelles que soient les routes appliquées à votre sous-réseau d’intégration. Si WEBSITE_VNET_ROUTE_ALL était défini sur 1, et que vous n’aviez aucune route affectant le trafic des adresses publiques sur votre sous-réseau d’intégration, l’ensemble du trafic sortant serait toujours soumis aux NSG attribués à votre sous-réseau d’intégration. Si WEBSITE_VNET_ROUTE_ALL n’était pas défini, les NSG s’appliqueraient uniquement au trafic RFC1918.

### <a name="routes"></a>Itinéraires

Les tables de route vous permettent de router le trafic sortant depuis votre application vers l’emplacement de votre choix. Par défaut, les tables de route n’ont d’incidence que sur votre trafic de destination RFC1918.  Si vous affectez la valeur 1 à WEBSITE_VNET_ROUTE_ALL, tous vos appels sortants seront affectés. Les routes définies sur votre sous-réseau d’intégration n’ont pas d’incidence sur les réponses aux requêtes d’application entrantes. Les destinations courantes peuvent inclure des pare-feu ou des passerelles. Si vous souhaitez router tout le trafic sortant localement, vous pouvez utiliser une table de route pour envoyer l’intégralité du trafic sortant vers votre passerelle ExpressRoute. Si vous choisissez de router le trafic vers une passerelle, veillez à définir des routes dans le réseau externe pour renvoyer des réponses.

Les routes Border Gateway Protocol (BGP) affectent également le trafic de votre application. Si vous avez des routes BGP provenant par exemple d’une passerelle ExpressRoute, le trafic sortant de votre application sera affecté. Par défaut, les routes BGP n’affectent que votre trafic de destination RFC1918. Si WEBSITE_VNET_ROUTE_ALL a la valeur 1, tout le trafic sortant peut être affecté par vos routes BGP. 


<!--Image references-->
[4]: ../includes/media/web-sites-integrate-with-vnet/vnetint-appsetting.png

<!--Links-->
[VNETnsg]: https://docs.microsoft.com/azure/virtual-network/security-overview/