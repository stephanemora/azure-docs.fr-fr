---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 04/15/2020
ms.author: ccompy
ms.openlocfilehash: f7208307df51ecefb76f9adaedea59b327cdc19e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81604873"
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

Par défaut, votre application route seulement le trafic RFC1918 vers votre réseau virtuel. Si vous voulez router tout le trafic sortant vers votre réseau virtuel, appliquez le paramètre d’application WEBSITE_VNET_ROUTE_ALL à votre application. Pour configurer le paramètre d’application :

1. Accédez à l’interface utilisateur **Configuration** dans votre portail d’application. Sélectionnez **Nouveau paramètre d’application**.
1. Entrez **WEBSITE_VNET_ROUTE_ALL** dans la zone **Nom** et **1** dans la zone **Valeur**.

   ![Fournir le paramètre d’application][4]

1. Sélectionnez **OK**.
1. Sélectionnez **Enregistrer**.

Si vous routez tout le trafic sortant vers votre réseau virtuel, il est soumis aux groupes de sécurité réseau et aux routes définies par l’utilisateur appliqués à votre sous-réseau d’intégration. Lorsque vous acheminez tout le trafic sortant vers votre réseau virtuel, vos adresses sortantes sont toujours les adresses sortantes listées dans les propriétés de votre application, sauf si vous fournissez les routes pour envoyer le trafic ailleurs.

Il existe certaines limitations concernant l’utilisation de l’intégration au réseau virtuel avec les réseaux virtuels d’une même région :

* Vous ne pouvez pas accéder à des ressources via des connexions d’appairage mondiales.
* La fonctionnalité est disponible seulement à partir des unités d’échelle Azure App Service récentes qui prennent en charge les plans App Service PremiumV2.
* Le sous-réseau d’intégration peut être utilisé par un seul plan App Service.
* La fonctionnalité ne peut pas être utilisée par des applications de plan Isolé qui se trouvent dans un environnement App Service.
* La fonctionnalité nécessite un sous-réseau inutilisé /27 avec 32 adresses ou d’une taille supérieure, dans un réseau virtuel Azure Resource Manager.
* L’application et le réseau virtuel doivent être dans la même région.
* Vous ne pouvez pas supprimer un réseau virtuel avec une application intégrée. Supprimez l’intégration avant de supprimer le réseau virtuel.
* L’intégration ne peut se faire qu’avec des réseaux virtuels figurant dans le même abonnement que l’application.
* Vous ne pouvez disposer que d’une seule intégration au réseau virtuel régional par plan App Service. Plusieurs applications d’un même plan App Service peuvent utiliser le même réseau virtuel.
* Vous ne pouvez pas changer l’abonnement d’une application ou d’un plan quand une application utilise l’intégration au réseau virtuel régional.
* Votre application ne peut pas résoudre les adresses dans Azure DNS Private Zones sans modification de la configuration.

Une adresse est utilisée pour chaque instance du plan. Si vous mettez votre application à l’échelle vers cinq instances, cinq adresses sont utilisées. Comme la taille du sous-réseau ne peut pas être modifiée après l’affectation, vous devez utiliser un sous-réseau suffisamment grand pour s’adapter à la taille que votre application est susceptible d’atteindre. Une taille de /26 avec 64 adresses est recommandée. Un sous-réseau /26 avec 64 adresses contient un plan Premium avec 30 instances. Lorsque vous modifiez un plan à la hausse ou à la baisse, vous avez brièvement besoin de deux fois plus d’adresses.

Si vous voulez que vos applications d’un autre plan atteignent un réseau virtuel auquel sont déjà connectées des applications d’un autre plan, sélectionnez un sous-réseau différent de celui utilisé par l’intégration au réseau virtuel préexistante.

La fonctionnalité est en préversion pour Linux. Le formulaire Linux de la fonctionnalité prend uniquement en charge les appels aux adresses RFC 1918 (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16).

### <a name="web-or-function-app-for-containers"></a>Web App/Function App pour conteneurs

Si vous hébergez votre application sur Linux avec les images intégrées, l’intégration au réseau virtuel régional fonctionne sans modification supplémentaire. Si vous utilisez Web App ou Function App pour conteneurs, vous devez modifier votre image Docker pour utiliser l’intégration au réseau virtuel. Dans votre image docker, utilisez la variable d’environnement PORT comme port d’écoute du serveur web principal au lieu d’utiliser un numéro de port codé en dur. La variable d’environnement PORT est automatiquement définie par la plateforme au démarrage du conteneur. Si vous utilisez SSH, le démon SSH doit être configuré pour écouter sur le numéro de port spécifié par la variable d’environnement SSH_PORT quand vous utilisez l’intégration au réseau virtuel régional. Il n’existe pas de prise en charge de l’intégration au réseau virtuel avec passerelle obligatoire sur Linux.

### <a name="service-endpoints"></a>Points de terminaison de service

L’intégration au réseau virtuel régional vous permet d’utiliser des points de terminaison de service. Pour utiliser des points de terminaison de service avec votre application, servez-vous de l’intégration au réseau virtuel régional pour vous connecter à un réseau virtuel sélectionné, puis configurez des points de terminaison de service avec le service de destination sur le sous-réseau que vous avez utilisé pour l’intégration. Si vous souhaitez ensuite accéder à un service via des points de terminaison de service :

1. configurez l’intégration au réseau virtuel régional à votre application web
1. accédez au service de destination et configurez des points de terminaison de service sur le sous-réseau utilisé pour l’intégration

### <a name="network-security-groups"></a>Groupes de sécurité réseau

Vous pouvez utiliser des groupes de sécurité réseau pour bloquer le trafic entrant et sortant vers des ressources d’un réseau virtuel. Une application utilisant l’intégration au réseau virtuel régional peut utiliser un [groupe de sécurité réseau][VNETnsg] pour bloquer le trafic sortant vers des ressources dans votre réseau virtuel ou sur Internet. Pour bloquer le trafic vers des adresses publiques, le paramètre d’application WEBSITE_VNET_ROUTE_ALL doit être défini sur 1. Les règles de trafic entrant dans un groupe de sécurité réseau ne s’appliquent pas à votre application, car l’intégration au réseau virtuel n’a d’incidence que sur le trafic sortant depuis votre application.

Pour contrôler le trafic entrant vers votre application, utilisez la fonctionnalité Restrictions d’accès. Un groupe de sécurité réseau appliqué à votre sous-réseau d’intégration est actif quelles que soient les routes appliquées à votre sous-réseau d’intégration. Si WEBSITE_VNET_ROUTE_ALL est défini sur 1 et que vous n’avez aucune route affectant le trafic des adresses publiques sur votre sous-réseau d’intégration, l’ensemble du trafic sortant est toujours soumis aux groupes de sécurité réseau affectés à votre sous-réseau d’intégration. Si WEBSITE_VNET_ROUTE_ALL n’est pas défini, les groupes de sécurité réseau s’appliquent seulement au trafic RFC1918.

### <a name="routes"></a>Itinéraires

Vous pouvez utiliser des tables de routage pour router le trafic sortant depuis votre application vers où vous voulez. Par défaut, les tables de routage affectent seulement votre trafic de destination RFC1918. Si définissez WEBSITE_VNET_ROUTE_ALL sur 1, tous vos appels sortants seront affectés. Les routes définies sur votre sous-réseau d’intégration n’affectent pas les réponses aux requêtes d’application entrantes. Les destinations courantes peuvent inclure des pare-feu ou des passerelles.

Si vous souhaitez router tout le trafic sortant localement, vous pouvez utiliser une table de route pour envoyer l’intégralité du trafic sortant vers votre passerelle ExpressRoute. Si vous choisissez de router le trafic vers une passerelle, veillez à définir des routes dans le réseau externe pour renvoyer des réponses.

Les routes BGP (Border Gateway Protocol) affectent également le trafic de votre application. Si vous avez des routes BGP provenant par exemple d’une passerelle ExpressRoute, le trafic sortant de votre application sera affecté. Par défaut, les routes BGP affectent seulement votre trafic de destination RFC1918. Si WEBSITE_VNET_ROUTE_ALL est défini sur 1, tout le trafic sortant peut être affecté par vos routes BGP.

### <a name="azure-dns-private-zones"></a>Azure DNS Private Zones 

Une fois que votre application est intégrée à votre réseau virtuel, elle utilise le même serveur DNS que celui avec lequel votre réseau virtuel est configuré. Par défaut, votre application ne fonctionnera pas avec Azure DNS Private Zones. Pour qu’elle fonctionne avec Azure DNS Private Zones, vous devez ajouter les paramètres d’application suivants :

1. WEBSITE_DNS_SERVER avec la valeur 168.63.129.16 
1. WEBSITE_VNET_ROUTE_ALL avec la valeur 1

Ces paramètres envoient l’ensemble de vos appels sortants de votre application vers votre réseau virtuel, en plus de permettre à votre application d’utiliser Azure DNS Private Zones.

### <a name="private-endpoints"></a>Instances Private Endpoint

Si vous souhaitez effectuer des appels à des [points de terminaison privés][privateendpoints], vous devez soit les intégrer à Azure DNS Private Zones, soit gérer le point de terminaison privé dans le serveur DNS utilisé par votre application. 

<!--Image references-->
[4]: ../includes/media/web-sites-integrate-with-vnet/vnetint-appsetting.png

<!--Links-->
[VNETnsg]: https://docs.microsoft.com/azure/virtual-network/security-overview/
[privateendpoints]: https://docs.microsoft.com/azure/app-service/networking/private-endpoint
