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
ms.openlocfilehash: a0bb34f8a43199a5d3a18064bce92ef4bec543af
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67050650"
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
|[Restrictions d’adresse IP entrantes](#inbound-ip-restrictions)|✅ Oui|✅ Oui|✅ Oui|✅ Oui|
|[Restrictions d’adresse IP sortantes](#private-site-access)|❌Non| ❌Non|❌Non|✅ Oui|
|[Intégration du réseau virtuel](#virtual-network-integration)|❌Non|❌Non|✅ Oui|✅ Oui|
|[Intégration du réseau virtuel en préversion (Azure ExpressRoute et points de terminaison de service sortants)](#preview-version-of-virtual-network-integration)|❌Non|✅ Oui|✅ Oui|✅ Oui|
|[connexions hybrides](#hybrid-connections)|❌Non|❌Non|✅ Oui|✅ Oui|
|[Accès aux sites privés](#private-site-access)|❌Non| ✅ Oui|✅ Oui|✅ Oui|

## <a name="inbound-ip-restrictions"></a>Restrictions d’adresse IP entrantes

Vous pouvez utiliser des restrictions d’adresse IP pour définir la liste des adresses IP classées par ordre de priorité qui sont autorisées ou non à accéder à votre application. La liste peut inclure des adresses IPv4 et IPv6. Lorsqu’il y a une ou plusieurs entrées, une règle implicite « Tout refuser » se trouve à la fin de la liste. Les restrictions d’adresse IP fonctionnent avec toutes les options d’hébergement de fonction.

> [!NOTE]
> Pour utiliser l’éditeur du portail Azure, le portail doit être en mesure d’accéder directement à votre Function App en cours d’exécution. En outre, l’adresse IP de l’appareil que vous utilisez pour accéder au portail doit être sur la liste verte. Lorsque des restrictions de réseau sont appliquées, vous pouvez aussi accéder aux fonctionnalités dans l’onglet **Fonctionnalités de la plateforme**.

Pour en savoir plus, consultez [Restrictions d’accès statique Azure App Service](../app-service/app-service-ip-restrictions.md).

## <a name="outbound-ip-restrictions"></a>Restrictions d’adresse IP sortantes

Les restrictions d’adresse IP sortantes sont uniquement disponibles pour les fonctions déployées sur un Azure App Service Environment. Vous pouvez configurer des restrictions sortantes pour le réseau virtuel sur lequel votre Azure App Service Environment est déployé.

## <a name="virtual-network-integration"></a>Intégration du réseau virtuel

L’intégration de réseau virtuel permet à votre Function App d’accéder aux ressources au sein d’un réseau virtuel. Cette fonctionnalité est disponible dans le plan Premium et le plan App Service. Si votre application se trouve dans un Azure App Service Environment, elle se trouve déjà dans un réseau virtuel et il n’est pas nécessaire d’utiliser l’intégration de réseau virtuel pour accéder aux ressources de ce même réseau virtuel.

L’intégration de réseau virtuel permet à votre Function App d’accéder aux ressources de votre réseau virtuel, mais n’accorde pas d’[accès aux sites privés](#private-site-access) à votre Function App à partir du réseau virtuel.

Vous pouvez utiliser l’intégration de réseau virtuel pour permettre l’accès depuis des applications à des bases de données et à des services web qui s’exécutent dans votre réseau virtuel. Avec l’intégration de réseau virtuel, vous n’avez pas besoin d’exposer un point de terminaison public pour les applications sur votre machine virtuelle. Vous pouvez utiliser des adresses routables non Internet privées à la place.

La version en disponibilité générale de l’intégration de réseau virtuel s’appuie sur une passerelle VPN pour connecter des applications de fonction à un réseau virtuel. Elle est disponible dans des fonctions hébergées dans un plan App Service. Pour en savoir plus sur la configuration de cette fonctionnalité, consultez [Intégrer une application à un réseau virtuel Azure](../app-service/web-sites-integrate-with-vnet.md).

### <a name="preview-version-of-virtual-network-integration"></a>Préversion de l’intégration de réseau virtuel

Une nouvelle version de la fonctionnalité d’intégration de réseau virtuel est en préversion. Elle ne dépend pas de VPN de point à site. Elle prend en charge l’accès aux ressources via ExpressRoute ou des points de terminaison de service. Elle est disponible dans le plan Premium et dans les plans App Service mis à l’échelle vers PremiumV2.

Voici certaines caractéristiques de cette version :

* Vous n’avez pas besoin de passerelle pour l’utiliser.
* Vous pouvez accéder à des ressources de connexions ExpressRoute sans autre configuration que l’intégration avec le réseau virtuel connecté à ExpressRoute.
* Vous pouvez utiliser des ressources sécurisées de point de terminaison de service à partir de fonctions en cours d’exécution. Pour cela, activez des points de terminaison de service sur le sous-réseau utilisé pour l’intégration de réseau virtuel.
* Vous ne pouvez pas configurer de déclencheurs pour utiliser des ressources sécurisées de point de terminaison de service. 
* La Function App et le réseau virtuel doivent se trouver dans la même région.
* La nouvelle fonctionnalité nécessite un sous-réseau non utilisé dans le réseau virtuel que vous avez déployé via Azure Resource Manager.
* Les charges de travail de production ne sont pas prises en charge tant que la fonctionnalité est en préversion.
* Les tables de routage et de peering mondial ne sont pas encore disponibles avec la fonctionnalité.
* Une adresse est utilisée pour chaque instance potentielle de Function App. Étant donné que vous ne pouvez pas changer la taille du sous-réseau après l’avoir affectée, utilisez un sous-réseau qui peut facilement prendre en charge que votre taille d’échelle maximale. Par exemple, pour prendre en charge un plan Premium qui peut être mis à l’échelle jusqu’à 80 instances, nous recommandons un sous-réseau `/25` qui fournit 126 adresses d’hôte.

Pour en savoir plus sur l’utilisation de la préversion de l’intégration de réseau virtuel, consultez [Intégrer une application de fonction à un réseau virtuel Azure](functions-create-vnet.md).

## <a name="hybrid-connections"></a>les connexions hybrides

[Connexions hybrides](../service-bus-relay/relay-hybrid-connections-protocol.md) est une fonctionnalité d’Azure Relay que vous pouvez utiliser pour accéder aux ressources d’application dans d’autres réseaux. Elles permettent d’accéder depuis votre application à un point de terminaison d’application. Vous ne pouvez pas l’utiliser pour accéder à votre application. Les connexions hybrides sont disponibles pour les fonctions exécutées dans [plan App Service](functions-scale.md#app-service-plan) et un [Azure App Service Environment](../app-service/environment/intro.md).

Utilisée dans Azure Functions, chaque connexion hybride correspond à une combinaison d’hôte et de port TCP unique. Cela signifie que le point de terminaison de connexion hybride peut se trouver sur un quelconque système d’exploitation et toute application tant que vous accédez à un port d’écoute TCP. La fonctionnalité Connexions hybrides ne détecte pas et ne prend pas en compte le protocole d’application ou les ressources auxquels vous accédez. Elle fournit simplement un accès réseau.

Pour en savoir plus, consultez la [documentation App Service pour les connexions hybrides](../app-service/app-service-hybrid-connections.md), qui prend en charge les fonctions dans un plan App Service.

## <a name="private-site-access"></a>Accès aux sites privés

L’accès aux sites privés fait référence au fait de rendre votre application accessible uniquement à partir d’un réseau privé, par exemple à partir d’un réseau virtuel Azure. 
* L’accès aux sites privés est disponible dans les plans Premium et App Service lorsque des **points de terminaison de service** sont configurés. Pour plus d’informations, consultez [Points de terminaison de service de réseau virtuel](../virtual-network/virtual-network-service-endpoints-overview.md)
    * N’oubliez pas qu’avec les points de terminaison de service, votre fonction a toujours un accès complet sortant à Internet, même si l’intégration de réseau virtuel est configurée.
* L’accès aux sites privés est disponible uniquement via un Azure App Service Environment configuré avec un équilibreur de charge interne (ILB). Pour plus d’informations, consultez [Créer et utiliser un équilibreur de charge interne avec un Azure App Service Environment](../app-service/environment/create-ilb-ase.md).

Différentes méthodes permettent d’accéder aux ressources de réseau virtuel dans d’autres options d’hébergement. Toutefois, un Azure App Service Environment est la seule méthode pour autoriser des déclencheurs pour une fonction sur un réseau virtuel.

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur la mise en réseau et Azure Functions : 

* [Tutoriel pour bien démarrer avec l’intégration de réseau virtuel](./functions-create-vnet.md)
* [Lire les questions fréquentes (FAQ) sur la mise en réseau de Functions](./functions-networking-faq.md)
* [En savoir plus sur l’intégration de réseau virtuel avec App Service/Functions](../app-service/web-sites-integrate-with-vnet.md)
* [En savoir plus sur les réseaux virtuels dans Azure](../virtual-network/virtual-networks-overview.md)
* [Activer davantage de fonctionnalités de réseau et plus de contrôle avec Azure App Service Environment](../app-service/environment/intro.md)
* [Se connecter à des ressources locales sans modifier le pare-feu à l’aide des connexions hybrides](../app-service/app-service-hybrid-connections.md)
