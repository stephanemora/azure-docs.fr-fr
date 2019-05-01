---
title: Options de mise en réseau d’Azure Functions
description: Une vue d’ensemble de toutes les options de mise en réseau disponibles dans Azure Functions
services: functions
author: alexkarcher-msft
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 4/11/2019
ms.author: alkarche
ms.openlocfilehash: 49f89d39b3b917ec6357b241d7c413c2790eca25
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64575606"
---
# <a name="azure-functions-networking-options"></a>Options de mise en réseau d’Azure Functions

Cet article décrit les fonctionnalités de mise en réseau disponibles sur les options d’hébergement pour Azure Functions. Toutes les options de mise en réseau suivantes de fournissent certains possibilité d’accéder aux ressources sans utiliser des adresses routables non-Internet ou restreindre l’accès à internet à une application de fonction. 

Les modèles d’hébergement ont différents niveaux d’isolement réseau disponible. Choisir celui qui convient vous aidera à répondre à vos besoins d’isolation réseau.

Vous pouvez héberger des applications de fonction de deux façons :

* Il existe un ensemble d’options de plan qui s’exécutent sur une infrastructure mutualisée, avec différents niveaux de connectivité de réseau virtuel et les options de mise à l’échelle :
    * Le [plan de consommation](functions-scale.md#consumption-plan), qui s’adapte dynamiquement en réponse à charger et offre des options d’isolement réseau minimales.
    * Le [plan Premium](functions-scale.md#premium-plan-public-preview), qui s’adapte dynamiquement, tout en offrant l’isolement réseau plus complète.
    * Azure [plan App Service](functions-scale.md#app-service-plan), qui fonctionne sur une échelle fixe et offre une isolation de réseau similaire au plan Premium.
* Vous pouvez exécuter des fonctions dans un [environnement App Service](../app-service/environment/intro.md). Cette méthode déploie votre fonction dans votre réseau virtuel et offre une isolation et contrôle de l’intégralité du réseau.

## <a name="matrix-of-networking-features"></a>Matrice des fonctionnalités de mise en réseau

|                |[Plan de consommation](functions-scale.md#consumption-plan)|[Plan Premium (version préliminaire)](functions-scale.md#premium-plan-public-preview)|[Plan App Service](functions-scale.md#app-service-plan)|[Environnement App Service](../app-service/environment/intro.md)|
|----------------|-----------|----------------|---------|-----------------------|  
|[Restrictions d’adresse IP entrantes](#inbound-ip-restrictions)|✅Yes|✅Yes|✅Yes|✅Yes|
|[Restrictions d’adresse IP sortante](#private-site-access)|❌No| ❌No|❌No|✅Yes|
|[Intégration du réseau virtuel](#virtual-network-integration)|❌No|❌No|✅Yes|✅Yes|
|[Afficher un aperçu d’intégration de réseau virtuel (Azure ExpressRoute et points de terminaison de service sortants)](#preview-version-of-virtual-network-integration)|❌No|✅Yes|✅Yes|✅Yes|
|[connexions hybrides](#hybrid-connections)|❌No|❌No|✅Yes|✅Yes|
|[accès privé aux sites](#private-site-access)|❌No| ✅Yes|✅Yes|✅Yes|

## <a name="inbound-ip-restrictions"></a>Restrictions d’adresse IP entrantes

Vous pouvez utiliser les restrictions d’adresse IP pour définir une liste ordonnée de priorité d’adresses IP qui est autorisé/refusé à votre application. La liste peut inclure des adresses IPv4 et IPv6. Lorsqu’il existe une ou plusieurs entrées, implicite « refuser tout » existe à la fin de la liste. Restrictions d’adresse IP fonctionnent avec toutes les options d’hébergement de fonction.

> [!NOTE]
> Pour utiliser l’Éditeur du portail Azure, le portail doit être en mesure d’accéder directement à votre application de fonction en cours d’exécution. En outre, l’appareil que vous utilisez pour accéder au portail doit avoir son dans la liste verte IP. Avec les restrictions de réseau en place, vous pouvez toujours accéder à toutes les fonctionnalités sur le **fonctionnalités de la plateforme** onglet.

Pour plus d’informations, consultez [restrictions d’accès statique Azure App Service](../app-service/app-service-ip-restrictions.md).

## <a name="outbound-ip-restrictions"></a>Restrictions d’adresse IP sortantes

Restrictions d’adresse IP sortantes sont uniquement disponibles pour les fonctions déployées pour un environnement App Service. Vous pouvez configurer des restrictions sortantes pour le réseau virtuel où votre environnement App Service est déployé.

## <a name="virtual-network-integration"></a>Intégration du réseau virtuel

Intégration de réseau virtuel permet à votre application de fonction accéder aux ressources à l’intérieur d’un réseau virtuel. Cette fonctionnalité est disponible dans le plan Premium et le plan App Service. Si votre application se trouve dans un environnement App Service, il est déjà dans un réseau virtuel et ne nécessite pas l’utilisation de l’intégration de réseau virtuel pour accéder aux ressources dans le même réseau virtuel.

Intégration de réseau virtuel permet de votre function app l’accès aux ressources dans votre réseau virtuel, mais n’accorde pas [accès privé aux sites](#private-site-access) à votre application de fonction à partir du réseau virtuel.

Vous pouvez utiliser l’intégration de réseau virtuel pour activer l’accès à partir d’applications pour les bases de données et services web exécutés dans votre réseau virtuel. Avec l’intégration de réseau virtuel, vous n’avez pas besoin d’exposer un point de terminaison public pour les applications sur votre machine virtuelle. Vous pouvez utiliser les adresses routables privés et internet non à la place.

La version généralement disponible de l’intégration de réseau virtuel s’appuie sur une passerelle VPN pour se connecter des applications de fonction à un réseau virtuel. Il est disponible dans les fonctions hébergées dans un plan App Service. Pour savoir comment configurer cette fonctionnalité, consultez [intégrer votre application à un réseau virtuel Azure](../app-service/web-sites-integrate-with-vnet.md#enabling-vnet-integration).

### <a name="preview-version-of-virtual-network-integration"></a>Version préliminaire de l’intégration de réseau virtuel

Une nouvelle version de la fonctionnalité d’intégration de réseau virtuel est en version préliminaire. Il ne dépend pas point-to-site VPN. Il prend en charge l’accès aux ressources via ExpressRoute ou des points de terminaison de service. Il est disponible dans le plan Premium et dans les plans App Service mis à l’échelle à PremiumV2.

Voici certaines caractéristiques de cette version :

* Vous n’avez pas besoin une passerelle à l’utiliser.
* Vous pouvez accéder à des ressources de connexions ExpressRoute sans autre configuration que l’intégration avec le réseau virtuel connecté à ExpressRoute.
* Vous pouvez utiliser les ressources de point de terminaison de service sécurisé à partir de l’exécution des fonctions. Pour ce faire, activez les points de terminaison de service sur le sous-réseau utilisé pour l’intégration de réseau virtuel.
* Vous ne pouvez pas configurer des déclencheurs afin d’utiliser des ressources sécurisées par point de terminaison de service. 
* L’application de fonction et le réseau virtuel doivent être dans la même région.
* La nouvelle fonctionnalité nécessite un sous-réseau non utilisé dans le réseau virtuel que vous avez déployé via Azure Resource Manager.
* Charges de travail de production ne sont pas pris en charge lorsque la fonction est disponible en version préliminaire.
* Tables de routage et l’homologation globale ne sont pas encore disponibles avec la fonctionnalité.
* Une adresse est utilisée pour chaque instance potentiel d’une application de fonction. Étant donné que vous ne pouvez pas modifier la taille du sous-réseau après l’affectation, utilisez un sous-réseau qui peut facilement prendre en charge la taille de votre mise à l’échelle maximale. Par exemple, pour prendre en charge un plan Premium qui peut être mis à l’échelle des 80 instances, nous recommandons un `/25` sous-réseau qui fournit les adresses d’hôte 126.

Pour en savoir plus sur l’utilisation de la version préliminaire de l’intégration de réseau virtuel, consultez [intégrer une application de fonction à un réseau virtuel Azure](functions-create-vnet.md).

## <a name="hybrid-connections"></a>les connexions hybrides

[Connexions hybrides](../service-bus-relay/relay-hybrid-connections-protocol.md) est une fonctionnalité de relais Azure que vous pouvez utiliser pour accéder aux ressources d’application dans d’autres réseaux. Elles permettent d’accéder depuis votre application à un point de terminaison d’application. Vous ne pouvez pas l’utiliser pour accéder à votre application. Les connexions hybrides sont disponibles pour les fonctions en cours d’exécution un [plan App Service](functions-scale.md#app-service-plan) et un [environnement App Service](../app-service/environment/intro.md).

Utilisée dans Azure Functions, chaque connexion hybride correspond à une combinaison d’hôte et le port TCP unique. Cela signifie que le point de terminaison de la connexion hybride peut être n’importe quel système d’exploitation et n’importe quelle application, tant que vous accédez à un port d’écoute TCP. La fonctionnalité Connexions hybrides ne pas savoir ou en compte le protocole d’application, ou les ressources auxquels vous accédez. Il fournit simplement un accès au réseau.

Pour plus d’informations, consultez le [documentation App Service pour les connexions hybrides](../app-service/app-service-hybrid-connections.md), qui prend en charge les fonctions dans un plan App Service.

## <a name="private-site-access"></a>Accès aux sites privés

Accès privé aux sites fait référence pour rendre votre application accessible uniquement à partir d’un réseau privé comme à partir d’un réseau virtuel Azure. 
* Accès privé aux sites est disponible dans les App Service Premium planifier quand **points de terminaison de Service** sont configurés. Pour plus d’informations, consultez [points de terminaison de service réseau virtuel](../virtual-network/virtual-network-service-endpoints-overview.md)
    * N’oubliez pas qu’avec les points de terminaison de Service, votre fonction toujours a un accès complet sortant à internet, même avec l’intégration de réseau virtuel configurée.
* Accès privé aux sites est disponible uniquement avec un environnement App Service configuré avec un équilibreur de charge interne (ILB). Pour plus d’informations, consultez [créer et utiliser un équilibreur de charge interne avec un environnement App Service](../app-service/environment/create-ilb-ase.md).

Il existe de nombreuses façons d’accéder aux ressources de réseau virtuel dans les autres options d’hébergement. Mais un environnement App Service est la seule façon d’autoriser les déclencheurs pour une fonction doit être effectuée via un réseau virtuel.

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur les fonctions de mise en réseau et Azure : 

* [Suivez le didacticiel sur la mise en route avec l’intégration de réseau virtuel](./functions-create-vnet.md)
* [Lire les Forum aux questions sur la mise en réseau de fonctions](./functions-networking-faq.md)
* [En savoir plus sur l’intégration de réseau virtuel avec App Service/fonctions](../app-service/web-sites-integrate-with-vnet.md)
* [En savoir plus sur les réseaux virtuels dans Azure](../virtual-network/virtual-networks-overview.md)
* [Activer plusieurs fonctionnalités mise en réseau et le contrôle avec les environnements App Service](../app-service/environment/intro.md)
* [Se connecter à des ressources locales sans modifications de pare-feu à l’aide de connexions hybrides](../app-service/app-service-hybrid-connections.md)
