---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 10/21/2020
ms.author: ccompy
ms.openlocfilehash: 821746856cb37781c8f6a2e58659ce7db43e1479
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105609483"
---
L’utilisation de l’intégration au réseau virtuel régional permet à votre application d’accéder aux :

* Ressources d’un réseau virtuel dans la même région que votre application.
* Ressources de réseaux virtuels appairés au réseau virtuel auquel votre application est intégrée.
* Services sécurisés des points de terminaison de service.
* Ressources sur des connexions Azure ExpressRoute.
* Ressources dans le réseau virtuel auquel vous êtes intégré.
* Ressources sur des connexions appairées, notamment des connexions Azure ExpressRoute.
* Instances Private Endpoint 

Lorsque vous utilisez l’intégration au réseau virtuel avec des réseaux virtuels d’une même région, vous pouvez utiliser les fonctionnalités de même en réseau Azure suivantes :

* **Groupes de sécurité réseau (NSG)**  : Vous pouvez bloquer le trafic sortant avec un groupe de sécurité réseau placé sur votre sous-réseau d’intégration. Les règles de trafic entrant ne s’appliquent pas, car vous ne pouvez pas utiliser l’intégration au réseau virtuel pour fournir un accès entrant à votre application.
* **Tables de routage (Routes définies par l’utilisateur)**  : Vous pouvez placer une table de routage sur le sous-réseau d’intégration pour envoyer le trafic sortant où vous voulez.

Par défaut, votre application route seulement le trafic RFC1918 vers votre réseau virtuel. Si vous voulez router tout le trafic sortant vers votre réseau virtuel, suivez les étapes suivantes pour ajouter le paramètre `WEBSITE_VNET_ROUTE_ALL` à votre application : 

1. Accédez à l’interface utilisateur **Configuration** dans votre portail d’application. Sélectionnez **Nouveau paramètre d’application**.
1. Entrez `WEBSITE_VNET_ROUTE_ALL` dans la zone **Nom** et `1` dans la zone **Valeur**.

   ![Fournir le paramètre d’application][4]

1. Sélectionnez **OK**.
1. Sélectionnez **Enregistrer**.

> [!NOTE]
> Lorsque vous routez tout le trafic sortant vers votre réseau virtuel, il est soumis aux groupes de sécurité réseau et aux routes définies par l’utilisateur appliqués à votre sous-réseau d’intégration. Lorsque `WEBSITE_VNET_ROUTE_ALL` est défini sur `1`, le trafic sortant est toujours envoyé à partir des adresses répertoriées dans les propriétés de votre application, sauf si vous fournissez des itinéraires qui dirigent le trafic ailleurs.
> 
> L’intégration au réseau virtuel régional n’est pas en mesure d’utiliser le port 25.

Il existe certaines limitations concernant l’utilisation de l’intégration au réseau virtuel avec les réseaux virtuels d’une même région :

* Vous ne pouvez pas accéder à des ressources via des connexions d’appairage mondiales.
* Cette fonctionnalité est disponible à partir de toutes les unités d’échelle App Service dans Premium v2 et Premium v3. Elle est également disponible dans Standard, mais uniquement à partir d’unités d’échelle App Service plus récentes. Si vous utilisez une unité d’échelle plus ancienne, vous ne pouvez utiliser la fonctionnalité qu’à partir d’un plan App Service Premium v2. Si vous souhaitez vous assurer de pouvoir utiliser la fonctionnalité dans un plan App Service Standard, créez votre application dans un plan App Service Premium v3. Ces plans ne sont pris en charge que sur les unités d’échelle les plus récentes. Vous pouvez effectuer un scale-down par la suite si vous le souhaitez.  
* Le sous-réseau d’intégration peut être utilisé par un seul plan App Service.
* La fonctionnalité ne peut pas être utilisée par des applications de plan Isolé qui se trouvent dans un environnement App Service.
* La fonctionnalité nécessite un sous-réseau inutilisé /28 ou d’une taille supérieure dans un réseau virtuel Azure Resource Manager.
* L’application et le réseau virtuel doivent être dans la même région.
* Vous ne pouvez pas supprimer un réseau virtuel avec une application intégrée. Supprimez l’intégration avant de supprimer le réseau virtuel.
* Vous ne pouvez disposer que d’une seule intégration au réseau virtuel régional par plan App Service. Plusieurs applications d’un même plan App Service peuvent utiliser le même réseau virtuel.
* Vous ne pouvez pas changer l’abonnement d’une application ou d’un plan quand une application utilise l’intégration au réseau virtuel régional.
* Votre application ne peut pas résoudre les adresses dans les zones privées Azure DNS sans modification de la configuration.

L’intégration au réseau virtuel dépend d’un sous-réseau dédié. Lorsque vous approvisionnez un sous-réseau, le sous-réseau Azure perd cinq adresses IP dès le début. Une seule adresse du sous-réseau d’intégration est utilisée pour chaque instance de plan. Lorsque vous définissez l’échelle de votre application sur quatre instances, quatre adresses sont utilisées. 

Lorsque vous en augmentez ou diminuez la taille, l’espace d’adressage requis est doublé pendant une brève période de temps. Cela affecte les instances prises en charge réelles et disponibles pour une taille de sous-réseau donnée. Le tableau suivant indique à la fois le nombre maximal d’adresses disponibles par bloc CIDR et l’impact de cette mise à l’échelle horizontale :

| Taille de bloc CIDR | Nombre maximal d’adresses disponibles | Mise à l’échelle horizontale maximale (instances)<sup>*</sup> |
|-----------------|-------------------------|---------------------------------|
| /28             | 11                      | 5                               |
| /27             | 27                      | 13                              |
| /26             | 59                      | 29                              |

<sup>*</sup>Part du principe que vous devrez effectuer un scale-up ou un scale-down en taille ou en SKU à un moment donné. 

Comme la taille du sous-réseau ne peut pas être modifiée après l’affectation, utilisez un sous-réseau suffisamment grand pour s’adapter à l’échelle que votre application est susceptible d’atteindre. Pour éviter tout problème de capacité du sous-réseau, vous devez utiliser un /26 avec 64 adresses.  

Quand vous voulez que vos applications d’un autre plan atteignent un réseau virtuel auquel sont déjà connectées des applications d’un autre plan, sélectionnez un sous-réseau différent de celui utilisé par l’intégration au réseau virtuel préexistante.

La fonctionnalité est entièrement prise en charge pour les applications Windows et Linux, notamment les [conteneurs personnalisés](../articles/app-service/quickstart-custom-container.md). Tous les comportements sont identiques entre les applications Windows et les applications Linux.

### <a name="service-endpoints"></a>Points de terminaison de service

L’intégration au réseau virtuel régional vous permet d’atteindre les services Azure sécurisés avec les points de terminaison de service. Pour accéder à un service sécurisé par point de terminaison de service, vous devez effectuer les opérations suivantes :

1. Configurez l’intégration au réseau virtuel régional avec votre application web pour vous connecter à un sous-réseau spécifique pour l’intégration.
1. Accédez au service de destination et configurez des points de terminaison de service sur le sous-réseau d’intégration.

### <a name="network-security-groups"></a>Groupes de sécurité réseau

Vous pouvez utiliser des groupes de sécurité réseau pour bloquer le trafic entrant et sortant vers des ressources d’un réseau virtuel. Une application utilisant l’intégration au réseau virtuel régional peut utiliser un [groupe de sécurité réseau][VNETnsg] pour bloquer le trafic sortant vers des ressources dans votre réseau virtuel ou sur Internet. Pour bloquer le trafic vers des adresses publiques, le paramètre d’application `WEBSITE_VNET_ROUTE_ALL` doit être défini sur `1`. Les règles de trafic entrant dans un groupe de sécurité réseau ne s’appliquent pas à votre application, car l’intégration au réseau virtuel n’a d’incidence que sur le trafic sortant depuis votre application.

Pour contrôler le trafic entrant vers votre application, utilisez la fonctionnalité Restrictions d’accès. Un groupe de sécurité réseau appliqué à votre sous-réseau d’intégration est actif quelles que soient les routes appliquées à votre sous-réseau d’intégration. Si `WEBSITE_VNET_ROUTE_ALL` est défini sur `1` et que vous n’avez aucune route affectant le trafic des adresses publiques sur votre sous-réseau d’intégration, l’ensemble du trafic sortant est toujours soumis aux groupes de sécurité réseau affectés à votre sous-réseau d’intégration. Lorsque `WEBSITE_VNET_ROUTE_ALL` n’est pas défini, les groupes de sécurité réseau s’appliquent seulement au trafic RFC1918.

### <a name="routes"></a>Itinéraires

Vous pouvez utiliser des tables de routage pour router le trafic sortant depuis votre application vers où vous voulez. Par défaut, les tables de routage affectent seulement votre trafic de destination RFC1918. Lorsque vous définissez `WEBSITE_VNET_ROUTE_ALL` sur `1`, tous vos appels entrants sont affectés. Les routes définies sur votre sous-réseau d’intégration n’affectent pas les réponses aux requêtes d’application entrantes. Les destinations courantes peuvent inclure des pare-feu ou des passerelles.

Si vous souhaitez router tout le trafic sortant localement, vous pouvez utiliser une table de route pour envoyer l’intégralité du trafic sortant vers votre passerelle ExpressRoute. Si vous choisissez de router le trafic vers une passerelle, veillez à définir des routes dans le réseau externe pour renvoyer des réponses.

Les routes BGP (Border Gateway Protocol) affectent également le trafic de votre application. Si vous avez des routes BGP provenant par exemple d’une passerelle ExpressRoute, le trafic sortant de votre application est affecté. Par défaut, les routes BGP affectent seulement votre trafic de destination RFC1918. Lorsque `WEBSITE_VNET_ROUTE_ALL` est défini sur `1`, tout le trafic sortant peut être affecté par vos routes BGP.

### <a name="azure-dns-private-zones"></a>Zones privées Azure DNS 

Une fois que votre application est intégrée à votre réseau virtuel, elle utilise le même serveur DNS que celui avec lequel votre réseau virtuel est configuré. Par défaut, votre application ne fonctionnera pas avec des zones privées Azure DNS. Pour qu’elle fonctionne avec les zones privées Azure DNS, vous devez ajouter les paramètres d’application suivants :

1. `WEBSITE_DNS_SERVER` avec la valeur `168.63.129.16`
1. `WEBSITE_VNET_ROUTE_ALL` avec la valeur `1`

Ces paramètres envoient l’ensemble de vos appels sortants de votre application vers votre réseau virtuel et permet à votre application d’accéder à une zone privée Azure DNS. Avec ces paramètres, votre application peut utiliser Azure DNS en interrogeant la zone privée DNS au niveau du Worker.  

### <a name="private-endpoints"></a>Points de terminaison privés

Si vous souhaitez effectuer des appels vers des [points de terminaison privés][privateendpoints], vous devez vous assurer que vos recherches DNS seront résolues sur le point de terminaison privé. Vous pouvez appliquer ce comportement de l’une des façons suivantes : 

* Intégrer à des zones privées Azure DNS Lorsque votre réseau virtuel n’a pas de serveur DNS personnalisé, cela sera effectué automatiquement.
* Gérer le point de terminaison privé dans le serveur DNS utilisé par votre application. Pour ce faire, vous devez connaître l’adresse du point de terminaison privé, puis pointer le point de terminaison que vous essayez d’atteindre vers cette adresse à l’aide d’un enregistrement A.
* Configurez votre propre serveur DNS pour le transfert vers des zones privées Azure DNS.

<!--Image references-->
[4]: ../includes/media/web-sites-integrate-with-vnet/vnetint-appsetting.png

<!--Links-->
[VNETnsg]: /azure/virtual-network/security-overview/
[privateendpoints]: ../articles/app-service/networking/private-endpoint.md
