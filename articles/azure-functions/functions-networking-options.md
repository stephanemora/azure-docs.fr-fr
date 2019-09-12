---
title: Options de mise en réseau d’Azure Functions
description: Vue d’ensemble de toutes les options de mise en réseau disponibles dans Azure Functions
services: functions
author: alexkarcher-msft
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 4/11/2019
ms.author: alkarche
ms.openlocfilehash: ca7985ee302b35f8e7b39c46c229c7b0b263ffce
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/30/2019
ms.locfileid: "70170654"
---
# <a name="azure-functions-networking-options"></a>Options de mise en réseau d’Azure Functions

Cet article décrit les fonctionnalités de mise en réseau disponibles dans les options d’hébergement pour Azure Functions. Toutes les options de mise en réseau suivantes permettent d’accéder aux ressources sans utiliser d’adresses routables Internet ou de restreindre l’accès à Internet à une Function App. 

Les modèles d’hébergement offrent différents niveaux d’isolement réseau. Le choix du modèle qui convient vous aidera à répondre à vos besoins d’isolement réseau.

Vous pouvez héberger des applications de fonction de deux façons :

* Il existe un ensemble d’options de plan qui s’exécutent sur une infrastructure multilocataire, avec différents niveaux de connectivité réseau virtuel et des options de mise à l’échelle :
    * Le [plan Consommation](functions-scale.md#consumption-plan), qui s’adapte dynamiquement en réponse à la charge et offre des options d’isolement réseau minimales.
    * Le [plan Premium](functions-scale.md#premium-plan), qui s’adapte dynamiquement tout en offrant un isolement réseau plus complet.
    * Le [plan App Service](functions-scale.md#app-service-plan) d’Azure, qui fonctionne sur une échelle fixe et offre un isolement réseau similaire au plan Premium.
* Vous pouvez exécuter des fonctions dans un [Azure App Service Environment](../app-service/environment/intro.md). Cette méthode déploie votre fonction dans votre réseau virtuel et offre un contrôle et un isolement réseau complets.

## <a name="matrix-of-networking-features"></a>Matrice de fonctionnalités de mise en réseau

|                |[Plan Consommation](functions-scale.md#consumption-plan)|[Plan Premium (préversion)](functions-scale.md#premium-plan)|[Plan App Service](functions-scale.md#app-service-plan)|[Environnement App Service](../app-service/environment/intro.md)|
|----------------|-----------|----------------|---------|-----------------------|  
|[Restrictions d'adresses IP entrantes et accès privé aux sites](#inbound-ip-restrictions)|✅ Oui|✅ Oui|✅ Oui|✅ Oui|
|[Intégration du réseau virtuel](#virtual-network-integration)|❌Non|✅ Oui (Zones géographiques)|✅ Oui (Zones géographiques et Passerelle)|✅ Oui|
|[Déclencheurs de réseau virtuel (non HTTP)](#virtual-network-triggers-non-http)|❌Non| ❌Non|✅ Oui|✅ Oui|
|[connexions hybrides](#hybrid-connections)|❌Non|❌Non|✅ Oui|✅ Oui|
|[Restrictions d’adresse IP sortantes](#outbound-ip-restrictions)|❌Non| ❌Non|❌Non|✅ Oui|


## <a name="inbound-ip-restrictions"></a>Restrictions d’adresse IP entrantes

Vous pouvez utiliser des restrictions d’adresse IP pour définir la liste des adresses IP classées par ordre de priorité qui sont autorisées ou non à accéder à votre application. La liste peut inclure des adresses IPv4 et IPv6. Lorsqu’il y a une ou plusieurs entrées, une règle implicite « Tout refuser » se trouve à la fin de la liste. Les restrictions d’adresse IP fonctionnent avec toutes les options d’hébergement de fonction.

> [!NOTE]
> Une fois les restrictions réseau en place, vous ne pouvez utiliser l'éditeur du portail qu'à partir de votre réseau virtuel ou après avoir ajouté à la liste verte l'adresse IP de la machine que vous utilisez pour accéder au Portail Azure. Néanmoins, vous pouvez aussi accéder aux fonctionnalités à partir de l'onglet **Fonctionnalités de la plateforme** de n'importe quel ordinateur.

Pour en savoir plus, consultez [Restrictions d’accès statique Azure App Service](../app-service/app-service-ip-restrictions.md).

## <a name="private-site-access"></a>Accès aux sites privés

L’accès aux sites privés fait référence au fait de rendre votre application accessible uniquement à partir d’un réseau privé, par exemple à partir d’un réseau virtuel Azure. 
* L’accès aux sites privés est disponible dans les plans [Premium](./functions-premium-plan.md), [Consommation](functions-scale.md#consumption-plan) et [App Service](functions-scale.md#app-service-plan) quand des **points de terminaison de service** sont configurés. 
    * Les points de terminaison de service peuvent être configurés pour chaque application, sous Fonctionnalités de la plateforme > Mise en réseau > Configurer des restrictions d’accès > Ajouter une règle. Les réseaux virtuels peuvent maintenant être sélectionnés en tant que « type » d’une règle.
    * Pour plus d’informations, consultez [Points de terminaison de service de réseau virtuel](../virtual-network/virtual-network-service-endpoints-overview.md)
        * N'oubliez pas qu'avec les points de terminaison de service, votre fonction dispose toujours d'un accès sortant complet à Internet, même si l'intégration au réseau virtuel est configurée.
* L'accès aux sites privés est également disponible via une instance d'Azure App Service Environment configurée avec un équilibreur de charge interne (ILB). Pour plus d’informations, consultez [Créer et utiliser un équilibreur de charge interne avec un Azure App Service Environment](../app-service/environment/create-ilb-ase.md).

## <a name="virtual-network-integration"></a>Intégration du réseau virtuel

L’intégration de réseau virtuel permet à votre Function App d’accéder aux ressources au sein d’un réseau virtuel. Cette fonctionnalité est disponible dans le plan Premium et le plan App Service. Si votre application se trouve dans un Azure App Service Environment, elle se trouve déjà dans un réseau virtuel et il n’est pas nécessaire d’utiliser l’intégration de réseau virtuel pour accéder aux ressources de ce même réseau virtuel.

Vous pouvez utiliser l’intégration de réseau virtuel pour permettre l’accès depuis des applications à des bases de données et à des services web qui s’exécutent dans votre réseau virtuel. Avec l’intégration de réseau virtuel, vous n’avez pas besoin d’exposer un point de terminaison public pour les applications sur votre machine virtuelle. Vous pouvez utiliser des adresses routables non Internet privées à la place.

La fonctionnalité d'intégration au réseau virtuel se présente sous deux formes :

1. L'intégration au réseau virtuel régional permet une intégration aux réseaux virtuels de la même région. Sous cette forme, la fonctionnalité nécessite un sous-réseau dans un réseau virtuel de la même région. Bien que cette fonctionnalité soit toujours en préversion, elle est prise en charge pour les charges de travail de production des applications Windows. Il existe cependant certaines restrictions qui sont mentionnées ci-dessous.
2. L'intégration au réseau virtuel avec passerelle obligatoire permet une intégration à des réseaux virtuels situés dans des régions distantes ou à des réseaux virtuels classiques. Cette version de la fonctionnalité nécessite le déploiement d'une passerelle de réseau virtuel dans votre réseau virtuel. Il s’agit de la fonctionnalité VPN point à site et elle est uniquement prise en charge par les applications Windows.

Une application ne peut utiliser qu'une seule forme de la fonctionnalité d'intégration au réseau virtuel à la fois. Il convient donc de déterminer quelle fonctionnalité utiliser. Les deux peuvent être utilisées dans des cas divers et variés. Il existe néanmoins des facteurs de différenciation clairs :

| Problème  | Solution | 
|----------|----------|
| Nécessité d'accéder à une adresse RFC 1918 (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16) dans la même région | Intégration au réseau virtuel régional |
| Nécessité d’accéder aux ressources d’un réseau virtuel classique ou d’un réseau virtuel situé dans une autre région | Intégration au réseau virtuel avec passerelle obligatoire |
| Nécessité d'accéder à des points de terminaison RFC 1918 via ExpressRoute | Intégration au réseau virtuel régional |
| Nécessité d'accéder à des ressources via des points de terminaison de service | Intégration au réseau virtuel régional |

Aucune des fonctionnalités ne vous permet d'accéder à des adresses non compatibles avec RFC 1918 via ExpressRoute. Pour cela, vous devez pour le moment utiliser un environnement ASE.

L'utilisation de l'intégration au réseau virtuel régional n'a pas pour effet de connecter votre réseau virtuel à des ressources locales ou de configurer des points de terminaison de service. Il s'agit d'une configuration de mise en réseau distincte. L'intégration au réseau virtuel régional permet simplement à votre application de passer des appels via ces types de connexion.

Quelle que soit la version utilisée, l'intégration au réseau virtuel permet à votre application de fonction d'accéder aux ressources de votre réseau virtuel, mais ne lui accorde pas d'accès aux sites privés à partir du réseau virtuel. L’accès au site privé fait référence au fait de rendre votre application accessible uniquement à partir d’un réseau privé, par exemple à partir d’un réseau virtuel Azure. L'intégration au réseau virtuel sert uniquement à passer des appels sortants de votre application vers votre réseau virtuel. 

La fonctionnalité d’intégration au réseau virtuel :

* requiert un plan App Service Standard, Premium ou PremiumV2 ;
* prend en charge les protocoles TCP et UDP ;
* fonctionne avec les applications App Service et les applications de fonction.

L’intégration au réseau virtuel ne prend pas en charge certaines choses, notamment :

* montage d’un lecteur ;
* intégration AD ; 
* NetBios ;

Dans Functions, l'intégration au réseau virtuel utilise une infrastructure partagée avec les applications Web App Service. Pour en savoir plus sur les deux types d'intégration au réseau virtuel, consultez :
* [Intégration au réseau virtuel régional](../app-service/web-sites-integrate-with-vnet.md#regional-vnet-integration)
* [Intégration au réseau virtuel avec passerelle obligatoire](../app-service/web-sites-integrate-with-vnet.md#gateway-required-vnet-integration)

Pour en savoir plus sur l'utilisation de l'intégration au réseau virtuel, consultez [Intégrer une application de fonction à un réseau virtuel Azure](functions-create-vnet.md).

### <a name="restricting-your-storage-account-to-a-virtual-network"></a>Restriction de votre compte de stockage à un réseau virtuel

> [!note] 
> Une fois que vous avez configuré des restrictions d’accès sur votre compte de stockage, il peut s’écouler jusqu’à 12 heures avant qu’il ne soit disponible. Pendant ce temps, votre application est complètement hors connexion.

Afin de fournir un niveau de sécurité plus élevé, vous pouvez limiter le compte de stockage de votre application à un réseau virtuel. Vous devez ensuite intégrer votre site à ce réseau virtuel pour accéder à votre compte de stockage. Cette configuration est prise en charge sur tous les plans qui prennent en charge l’intégration du réseau virtuel.

## <a name="virtual-network-triggers-non-http"></a>Déclencheurs de réseau virtuel (non HTTP)

Actuellement, pour pouvoir utiliser des déclencheurs Function autres que HTTP à partir d'un réseau virtuel, vous devez exécuter votre application de fonction dans le cadre d'un plan App Service ou dans un environnement App Service Environment.

Par exemple, si vous configurez Azure Cosmos DB de manière à accepter uniquement le trafic en provenance d'un réseau virtuel, vous devez déployer votre application de fonction dans le cadre d'un plan App Service avec intégration à ce réseau virtuel pour configurer les déclencheurs Azure Cosmos DB à partir de cette ressource. Pendant la phase de préversion, la configuration de l'intégration au réseau virtuel ne permettra pas au plan Premium d'utiliser des déclencheurs à partir de cette ressource Azure Cosmos DB.

Consultez la [liste de tous les déclencheurs non HTTP](./functions-triggers-bindings.md#supported-bindings) pour déterminer ce qui est pris en charge.

## <a name="hybrid-connections"></a>les connexions hybrides

[Connexions hybrides](../service-bus-relay/relay-hybrid-connections-protocol.md) est une fonctionnalité d’Azure Relay que vous pouvez utiliser pour accéder aux ressources d’application dans d’autres réseaux. Elles permettent d’accéder depuis votre application à un point de terminaison d’application. Vous ne pouvez pas l’utiliser pour accéder à votre application. Les connexions hybrides sont disponibles pour les fonctions exécutées dans [plan App Service](functions-scale.md#app-service-plan) et un [Azure App Service Environment](../app-service/environment/intro.md).

Utilisée dans Azure Functions, chaque connexion hybride correspond à une combinaison d’hôte et de port TCP unique. Cela signifie que le point de terminaison de connexion hybride peut se trouver sur un quelconque système d’exploitation et toute application tant que vous accédez à un port d’écoute TCP. La fonctionnalité Connexions hybrides ne détecte pas et ne prend pas en compte le protocole d’application ou les ressources auxquels vous accédez. Elle fournit simplement un accès réseau.

Pour en savoir plus, consultez la [documentation App Service pour les connexions hybrides](../app-service/app-service-hybrid-connections.md), qui prend en charge les fonctions dans un plan App Service.

## <a name="outbound-ip-restrictions"></a>Restrictions d’adresse IP sortantes

Les restrictions d’adresse IP sortantes sont uniquement disponibles pour les fonctions déployées sur un Azure App Service Environment. Vous pouvez configurer des restrictions sortantes pour le réseau virtuel sur lequel votre Azure App Service Environment est déployé.

Lors de l'intégration d'une application Function App à un réseau virtuel dans le cadre d'un plan Premium ou App Service, l'application est toujours en mesure de passer des appels sortants vers Internet.

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur la mise en réseau et Azure Functions : 

* [Tutoriel pour bien démarrer avec l’intégration de réseau virtuel](./functions-create-vnet.md)
* [Lire les questions fréquentes (FAQ) sur la mise en réseau de Functions](./functions-networking-faq.md)
* [En savoir plus sur l’intégration de réseau virtuel avec App Service/Functions](../app-service/web-sites-integrate-with-vnet.md)
* [En savoir plus sur les réseaux virtuels dans Azure](../virtual-network/virtual-networks-overview.md)
* [Activer davantage de fonctionnalités de réseau et plus de contrôle avec Azure App Service Environment](../app-service/environment/intro.md)
* [Se connecter à des ressources locales sans modifier le pare-feu à l’aide des connexions hybrides](../app-service/app-service-hybrid-connections.md)
