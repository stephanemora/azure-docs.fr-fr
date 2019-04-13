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
ms.openlocfilehash: a4ae2d8bad50a4103da6afaa0bee5cbb75c877aa
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2019
ms.locfileid: "59545503"
---
# <a name="azure-functions-networking-options"></a>Options de mise en réseau d’Azure Functions

Ce document décrit la suite de fonctionnalités de mise en réseau disponibles sur les options d’hébergement Azure Functions. Toutes les options de mise en réseau suivantes de fournissent certains possibilité d’accéder aux ressources sans utiliser des adresses routables non-Internet ou restreindre l’accès à internet à une application de fonction. Tous les modèles d’hébergement ont différents niveaux d’isolement réseau disponible, et choisir celui qui convient vous permettra de répondre à vos besoins d’isolation réseau.

Applications de fonction peuvent être hébergée de plusieurs façons différentes.

* Il existe un ensemble d’options de plan qui s’exécutent sur l’infrastructure de l’architecture mutualisée, avec différents niveaux de connectivité de réseau virtuel et les options de mise à l’échelle.
    1. Le plan de consommation, qui s’adapte dynamiquement en réponse à charger et offre des options d’isolement réseau minimales.
    1. Le Plan de Premium, qui s’adapte dynamiquement, tout en offrant l’isolement réseau plus complète.
    1. Le Plan App Service, qui fonctionne à une échelle fixe et offre une isolation de réseau similaire au plan Premium.
* Fonctions également peuvent être exécutées que sur un App Service environnement (ASE) qui déploie votre fonction dans votre réseau virtuel et offre une isolation et contrôle de l’intégralité du réseau.

## <a name="networking-feature-matrix"></a>Matrice des fonctionnalités de mise en réseau

|                |[Plan de consommation](functions-scale.md#consumption-plan)|⚠ [Plan Premium](functions-scale.md##premium-plan-public-preview)|[Plan App Service](functions-scale.md#app-service-plan)|[Environnement App Service](../app-service/environment/intro.md)|
|----------------|-----------|----------------|---------|-----------------------|  
|[**Restrictions d’adresse IP entrante**](#inbound-ip-restrictions)|✅Yes|✅Yes|✅Yes|✅Yes|
|[**Intégration au réseau virtuel**](#vnet-integration)|❌No|❌No|✅Yes|✅Yes|
|[**Intégration de réseau virtuel d’aperçu (Express Route & points de terminaison de Service)**](#preview-vnet-integration)|❌No|⚠Oui|⚠Oui|✅Yes|
|[**Connexions hybrides**](#hybrid-connections)|❌No|❌No|✅Yes|✅Yes|
|[**Accès à un Site privé**](#private-site-access)|❌No| ❌No|❌No|✅Yes|

⚠ Fonctionnalité en version préliminaire, pas à des fins de production

## <a name="inbound-ip-restrictions"></a>Restrictions d’adresse IP entrantes

Les restrictions d’adresse IP permettent de définir la liste des adresses IP qui sont autorisées à accéder à votre application. Dans cette liste, les adresses IP sont classées par ordre de priorité (Autoriser/Refuser). Les adresses autorisées peuvent inclure des adresses IPv4 et IPv6. Lorsqu’il y a une ou plusieurs entrées, il existe une règle implicite « Tout refuser » qui se trouve à la fin de la liste. La fonctionnalité de Restrictions d’adresse IP fonctionne avec la fonction toutes les options d’hébergement.

> [!NOTE]
> Pour être en mesure d’utiliser l’Éditeur du portail Azure, le portail doit être en mesure d’accéder directement à votre application de fonction en cours d’exécution et l’appareil que vous utilisez pour accéder au portail doit avoir son dans la liste verte IP. Avec les restrictions de réseau en place, vous pouvez toujours accéder à toutes les fonctionnalités de la **fonctionnalités de la plateforme** onglet.

[En savoir plus ici](https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions)

## <a name="vnet-integration"></a>Intégration au réseau virtuel

Intégration au réseau virtuel permet à votre application de fonction accéder aux ressources à l’intérieur d’un réseau virtuel. Intégration au réseau virtuel est disponible dans le plan Premium et le plan App Service. Si votre application se trouve dans un environnement App Service, il est déjà dans un réseau virtuel et ne nécessite pas l’utilisation de la fonctionnalité d’intégration de réseau virtuel pour accéder aux ressources dans le même réseau virtuel.

Intégration au réseau virtuel permet de votre function app l’accès aux ressources dans votre réseau virtuel, mais n’accorde pas [accès privé aux sites](#private-site-access) à votre application de fonction à partir du réseau virtuel.

L’intégration au réseau virtuel est souvent utilisée pour permettre l’accès depuis des applications à des bases de données et à des services web qui s’exécutent dans votre réseau virtuel. Avec l’intégration au réseau virtuel, vous n’avez pas besoin d’exposer un point de terminaison public pour les applications sur votre machine virtuelle, mais vous pouvez utiliser à la place des adresses privées routables non-Internet.

La version généralement disponible de l’intégration au réseau virtuel s’appuie sur une passerelle VPN pour se connecter des applications de fonction à un réseau virtuel. Il est disponible dans les fonctions hébergées dans un plan app service. Pour savoir comment configurer cette fonctionnalité, consultez le [document de Service de l’application pour la même fonctionnalité](../app-service/web-sites-integrate-with-vnet.md#enabling-vnet-integration).

### <a name="preview-vnet-integration"></a>Intégration au réseau virtuel de version préliminaire

Il existe une nouvelle version de la fonctionnalité d’intégration au réseau virtuel qui est en préversion. Elle ne dépend pas du réseau virtuel point à site, et prend également en charge l’accès aux ressources via ExpressRoute ou via des points de terminaison de service. Cette fonctionnalité est disponible dans le plan Premium et dans les plans App Service mis à l’échelle à PremiumV2.

La nouvelle version de l’intégration au réseau virtuel, ce qui est actuellement en version préliminaire, offre les avantages suivants :

* Aucune passerelle n’est nécessaire pour utiliser la nouvelle fonctionnalité d’intégration au réseau virtuel
* Vous pouvez accéder à des ressources via des connexions ExpressRoute sans configuration supplémentaire au-delà de l’intégration du réseau virtuel connecté à ExpressRoute.
* Vous pouvez utiliser le point de terminaison de Service des ressources à partir de l’exécution des fonctions sécurisées. Pour cela, activez des points de terminaison de service sur le sous-réseau utilisé pour l’intégration au réseau virtuel.
* Vous ne pouvez pas configurer des déclencheurs pour utiliser le point de terminaison de Service à l’aide de la nouvelle fonctionnalité d’intégration au réseau virtuel des ressources sécurisées. 
* L’application de fonction et le réseau virtuel doivent être dans la même région.
* La nouvelle fonctionnalité nécessite un sous-réseau non utilisé dans votre réseau virtuel Resource Manager.
* Charges de travail de production ne sont pas pris en charge sur la nouvelle version de l’intégration au réseau virtuel s’il est disponible en version préliminaire.
* Les tables de routage et l’appairage global ne sont pas encore disponibles avec la nouvelle intégration au réseau virtuel.
* Une adresse est utilisée pour chaque instance d’application de fonction potentiels. Étant donné que la taille du sous-réseau ne peut pas être modifié après l’affectation, utilisez un sous-réseau qui peut facilement prendre en charge la taille de votre mise à l’échelle maximale. Par exemple, pour prendre en charge un plan Premium qui peut être mis à l’échelle des 80 instances, nous recommandons un `/25` sous-réseau qui fournit les adresses d’hôte 126.

Pour en savoir plus sur l’utilisation de l’intégration de réseau virtuel en version préliminaire, consultez [intégrer une application de fonction à un réseau virtuel Azure](functions-create-vnet.md).

## <a name="hybrid-connections"></a>Connexions hybrides

[Connexions hybrides](../service-bus-relay/relay-hybrid-connections-protocol.md) est une fonctionnalité d’Azure Relay qui peut être utilisé pour accéder aux ressources d’application dans d’autres réseaux. Elles permettent d’accéder depuis votre application à un point de terminaison d’application. Il ne peut pas être utilisé pour accéder à votre application. Les connexions hybrides sont disponibles pour les fonctions en cours d’exécution un [plan App Service](functions-scale.md#app-service-plan) et un [environnement App Service](../app-service/environment/intro.md).

Utilisée dans les fonctions, chaque connexion hybride correspond à une combinaison d’hôte et le port TCP unique. Cela signifie que le point de terminaison de connexion hybride peut se trouver sur un quelconque système d’exploitation et toute application à condition que vous accédiez à un port d’écoute TCP. La fonctionnalité Connexions hybrides ne détectent pas et ne prennent pas en compte le protocole d’application ou les ressources auxquels vous accédez. Elles fournissent simplement un accès réseau.

Pour plus d’informations, consultez le [documentation App Service pour les connexions hybrides](../app-service/app-service-hybrid-connections.md), qui prend en charge les fonctions et les applications Web.

## <a name="private-site-access"></a>Accès aux sites privés

L’accès au site privé fait référence au fait de rendre votre application accessible uniquement à partir d’un réseau privé, par exemple à partir d’un réseau virtuel Azure. L’accès privé aux sites est disponible uniquement via un ASE configuré avec un équilibreur de charge interne (ILB). Pour plus d’informations sur l’utilisation d’un environnement ASE, consultez [création et utilisation d’un environnement ASE](../app-service/environment/create-ilb-ase.md).

Il existe plusieurs façons d’accéder aux ressources de réseau virtuel dans les autres options d’hébergement, mais un ASE est la seule façon d’autoriser les déclencheurs pour une fonction doit être effectuée via un réseau virtuel.

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur la mise en réseau et les fonctions : 

* [Suivez notre didacticiel de l’intégration démarrage réseau virtuel](./functions-create-vnet.md)
* [Lire les fonctions de mise en réseau FAQ ici](./functions-networking-faq.md)
* [En savoir plus sur l’intégration au réseau virtuel avec App Service/Functions](../app-service/web-sites-integrate-with-vnet.md)
* [En savoir plus sur les réseaux virtuels dans Azure](../virtual-network/virtual-networks-overview.md)
* [Activer plusieurs fonctionnalités mise en réseau et le contrôle avec les environnements App Service](../app-service/environment/intro.md)
* [Se connecter à des ressources locales sans modifier le pare-feu à l’aide des connexions hybrides](../app-service/app-service-hybrid-connections.md)
