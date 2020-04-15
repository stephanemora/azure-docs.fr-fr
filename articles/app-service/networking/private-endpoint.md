---
title: Connexion privée à une application web avec Azure Private Endpoint
description: Connexion privée à une application web à l’aide d’un point de terminaison privé Azure
author: ericgre
ms.assetid: 2dceac28-1ba6-4904-a15d-9e91d5ee162c
ms.topic: article
ms.date: 03/18/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.custom: fasttrack-edit
ms.openlocfilehash: 4d139cfa50afa94621066995314737fac70bbafe
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80756283"
---
# <a name="using-private-endpoints-for-azure-web-app-preview"></a>Utilisation de points de terminaison privés pour application web Azure (préversion)

> [!Note]
> La préversion est disponible dans les régions USA Est et USA Ouest 2 pour toutes les applications web PremiumV2 Windows et Linux, ainsi que toutes les fonctions Premium élastiques. 

Vous pouvez utiliser un point de terminaison privé pour votre application web Azure afin de permettre aux clients situés dans votre réseau privé d’accéder de façon sécurisée à l’application via une liaison privée. Le point de terminaison privé utilise une adresse IP de l’espace d’adressage de votre réseau virtuel Azure. Le trafic entre un client de votre réseau privé et l’application web traverse le réseau virtuel et une liaison privée sur le réseau principal de Microsoft, ce qui élimine son exposition à l’Internet public.

L’utilisation d’un point de terminaison privé pour votre application web vous permet d’effectuer les opérations suivantes :

- Sécuriser votre application web en configurant le point de terminaison privé, ce qui évite l’exposition publique.
- Vous connecter de manière sécurisée à l’application web à partir de réseaux locaux qui se connectent au réseau virtuel à l’aide d’un VPN ou d’un peering privé ExpressRoute.

Si vous avez simplement besoin d’une connexion sécurisée entre votre réseau virtuel et votre application web, un point de terminaison de service est la solution la plus simple. Si vous devez également accéder à l’application web à partir d’un emplacement local via une passerelle Azure, un réseau virtuel appairé au niveau régional ou un réseau virtuel appairé à l’échelle mondiale, le point de terminaison privé est la solution qu’il vous faut.  

Pour plus d’informations, consultez [Points de terminaison de service][serviceendpoint].

## <a name="conceptual-overview"></a>Vue d'ensemble conceptuelle

Un point de terminaison privé est l’interface réseau (NIC) spéciale de votre application web Azure qui se trouve dans un sous-réseau de votre réseau virtuel.
Lorsque vous créez un point de terminaison privé pour votre application web, celui-ci fournit une connectivité sécurisée entre les clients de votre réseau privé et votre application web. Une adresse IP est attribuée au point de terminaison privé à partir de la plage d’adresses IP de votre réseau virtuel.
La connexion entre le point de terminaison privé et l’application Web utilise une [liaison privée][privatelink] sécurisée. Le point de terminaison privé est utilisé uniquement pour les flux entrants de votre application web. Les flux sortants n’utilisent pas ce point de terminaison privé, mais vous pouvez injecter des flux sortants de votre réseau dans un sous-réseau différent via la [fonctionnalité d’intégration au réseau virtuel][vnetintegrationfeature].

Le sous-réseau dans lequel vous connectez le point de terminaison privé peut comporter d’autres ressources. Vous n’avez pas besoin d’un sous-réseau vide dédié.
Vous pouvez également déployer un point de terminaison privé dans une région différente de celle de l’application web. 

> [!Note]
>La fonctionnalité d’intégration au réseau virtuel ne peut pas utiliser le même sous-réseau que le point de terminaison privé. Il s’agit d’une limitation de la fonctionnalité d’intégration au réseau virtuel.

Du point de vue de la sécurité :

- Lorsque vous activez des points de terminaison privés sur votre application web, vous empêchez tout accès public.
- Vous pouvez activer plusieurs points de terminaison privés dans d’autres réseaux virtuels et sous-réseaux, y compris dans des réseaux virtuels d’autres régions.
- L’adresse IP de l’interface réseau du point de terminaison privé doit être dynamique. Toutefois, elle ne changera que si vous supprimez le point de terminaison privé.
- L’interface réseau du point de terminaison privé ne peut pas être associée à un groupe de sécurité réseau.
- Le sous-réseau qui héberge le point de terminaison privé peut être associé à un groupe de sécurité réseau. Toutefois, vous devez désactiver l’application des stratégies réseau pour le point de terminaison privé, comme décrit dans [Désactiver les stratégies réseau pour les points de terminaison privés][disablesecuritype]. Par conséquent, vous ne pouvez pas filtrer par groupe de sécurité réseau l’accès à votre point de terminaison privé.
- Lorsque vous activez un point de terminaison privé sur votre application web, la configuration des [restrictions d’accès][accessrestrictions] de l’application web n’est pas évaluée.
- Vous pouvez réduire le risque d’exfiltration de données à partir du réseau virtuel en supprimant toutes les règles de groupe de sécurité réseau dans lesquelles la destination est étiquetée Internet ou services Azure. Toutefois, l’ajout d’un point de terminaison privé Web App dans votre sous-réseau vous permet d’atteindre n’importe quelle application web qui est hébergée dans le même déploiement et qui est exposée à Internet.

Les journaux HTTP web de votre application web contiennent l’adresse IP source du client. Ceci est implémenté à l’aide du protocole de proxy TCP, en transférant la propriété IP du client à l’application web. Pour plus d’informations, consultez [Obtention d’informations de connexion à l’aide du proxy TCP v2][tcpproxy].


  > [!div class="mx-imgBorder"]
  > ![Vue d’ensemble des points de terminaison privés de l’application web](media/private-endpoint/global-schema-web-app.png)

## <a name="dns"></a>DNS

Cette fonctionnalité étant en préversion, nous ne modifions pas l’entrée DNS pendant la période de préversion. Vous devez gérer vous-même l’entrée DNS sur votre serveur DNS privé ou dans la zone privée d’Azure DNS.
Si vous devez utiliser un nom DNS personnalisé, vous devez ajouter celui-ci dans votre application web. Pendant la période de préversion, le nom personnalisé doit être validé comme n’importe quel nom personnalisé, à l’aide de la résolution DNS publique. Pour plus d’informations, consultez [Validation DNS personnalisée][dnsvalidation].

## <a name="pricing"></a>Tarifs

Pour plus d’informations sur les tarifs, consultez [Tarification Liaison privée Azure][pricing].

## <a name="limitations"></a>Limites

Nous améliorons régulièrement la fonctionnalité de liaison privée et le point de terminaison privé. Pour obtenir des informations à jour sur les limitations, voir [cet article][pllimitations].

## <a name="next-steps"></a>Étapes suivantes

Pour déployer un point de terminaison privé pour votre application web via le portail, voir [Comment se connecter en privé à une application web][howtoguide]




<!--Links-->
[serviceendpoint]: https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview
[privatelink]: https://docs.microsoft.com/azure/private-link/private-link-overview
[vnetintegrationfeature]: https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet
[disablesecuritype]: https://docs.microsoft.com/azure/private-link/disable-private-endpoint-network-policy
[accessrestrictions]: https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions
[tcpproxy]: ../../private-link/private-link-service-overview.md#getting-connection-information-using-tcp-proxy-v2
[dnsvalidation]: https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-domain
[pllimitations]: https://docs.microsoft.com/azure/private-link/private-endpoint-overview#limitations
[pricing]: https://azure.microsoft.com/pricing/details/private-link/
[howtoguide]: https://docs.microsoft.com/azure/private-link/create-private-endpoint-webapp-portal
