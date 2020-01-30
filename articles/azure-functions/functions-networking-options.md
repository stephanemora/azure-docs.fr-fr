---
title: Options de mise en réseau d’Azure Functions
description: Vue d’ensemble de toutes les options de mise en réseau disponibles dans Azure Functions.
author: alexkarcher-msft
ms.topic: conceptual
ms.date: 4/11/2019
ms.author: alkarche
ms.openlocfilehash: 79c27d252136281249c217f51019e53987922334
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76846456"
---
# <a name="azure-functions-networking-options"></a>Options de mise en réseau d’Azure Functions

Cet article décrit les fonctionnalités de mise en réseau disponibles dans les options d’hébergement pour Azure Functions. Toutes les options de mise en réseau suivantes vous permettent d’accéder aux ressources sans utiliser d’adresses routables Internet ou de restreindre l’accès à Internet à une Function App.

Les modèles d’hébergement offrent différents niveaux d’isolement réseau. Le choix du modèle qui convient vous aidera à répondre à vos besoins d’isolement réseau.

Vous pouvez héberger des applications de fonction de deux façons :

* Il existe un ensemble d’options de plan qui s’exécutent sur une infrastructure multilocataire, avec différents niveaux de connectivité réseau virtuel et des options de mise à l’échelle :
    * Le [plan Consommation](functions-scale.md#consumption-plan), qui s’adapte dynamiquement en réponse à la charge et offre des options d’isolement réseau minimales.
    * Le [plan Premium](functions-scale.md#premium-plan), qui s’adapte dynamiquement tout en offrant un isolement réseau plus complet.
    * Le [plan App Service](functions-scale.md#app-service-plan) d’Azure, qui fonctionne sur une échelle fixe et offre un isolement réseau similaire au plan Premium.
* Vous pouvez exécuter des fonctions dans un [Azure App Service Environment](../app-service/environment/intro.md). Cette méthode déploie votre fonction dans votre réseau virtuel et offre un contrôle et un isolement réseau complets.

## <a name="matrix-of-networking-features"></a>Matrice de fonctionnalités de mise en réseau

|                |[Plan Consommation](functions-scale.md#consumption-plan)|[Plan Premium](functions-scale.md#premium-plan)|[Plan App Service](functions-scale.md#app-service-plan)|[Environnement App Service](../app-service/environment/intro.md)|
|----------------|-----------|----------------|---------|-----------------------|  
|[Restrictions d'adresses IP entrantes et accès privé aux sites](#inbound-ip-restrictions)|✅Oui|✅Oui|✅Oui|✅Oui|
|[Intégration du réseau virtuel](#virtual-network-integration)|❌Non|✅Oui (Zones géographiques)|✅Oui (Zones géographiques et Passerelle)|✅Oui|
|[Déclencheurs de réseau virtuel (non HTTP)](#virtual-network-triggers-non-http)|❌Non| ✅Oui |✅Oui|✅Oui|
|[Connexions hybrides](#hybrid-connections) (Windows uniquement)|❌Non|✅Oui|✅Oui|✅Oui|
|[Restrictions d’adresse IP sortantes](#outbound-ip-restrictions)|❌Non| ❌Non|❌Non|✅Oui|

## <a name="inbound-ip-restrictions"></a>Restrictions d’adresse IP entrantes

Vous pouvez utiliser des restrictions d’adresse IP pour définir la liste des adresses IP classées par ordre de priorité qui sont autorisées ou non à accéder à votre application. La liste peut inclure des adresses IPv4 et IPv6. Lorsqu’il y a une ou plusieurs entrées, une règle implicite « Tout refuser » se trouve à la fin de la liste. Les restrictions d’adresse IP fonctionnent avec toutes les options d’hébergement de fonction.

> [!NOTE]
> Une fois les restrictions réseau en place, vous ne pouvez utiliser l'éditeur du portail qu'à partir de votre réseau virtuel ou après avoir ajouté l'adresse IP de la machine que vous utilisez pour accéder au Portail Azure sur la liste des destinataires approuvés. Néanmoins, vous pouvez aussi accéder aux fonctionnalités à partir de l'onglet **Fonctionnalités de la plateforme** de n'importe quel ordinateur.

Pour en savoir plus, consultez [Restrictions d’accès statique Azure App Service](../app-service/app-service-ip-restrictions.md).

## <a name="private-site-access"></a>Accès aux sites privés

L’accès aux sites privés fait référence au fait de rendre votre application accessible uniquement à partir d’un réseau privé, par exemple à partir d’un réseau virtuel Azure.

* L’accès aux sites privés est disponible dans les plans [Premium](./functions-premium-plan.md), [Consommation](functions-scale.md#consumption-plan) et [App Service](functions-scale.md#app-service-plan) quand des points de terminaison de service sont configurés.
    * Les points de terminaison de service peuvent être configurés pour chaque application, sous **Fonctionnalités de la plateforme** > **Mise en réseau** > **Configurer des restrictions d’accès** > **Ajouter une règle**. Les réseaux virtuels peuvent maintenant être sélectionnés comme un type de règle.
    * Pour plus d’informations, consultez [Points de terminaison de service de réseau virtuel](../virtual-network/virtual-network-service-endpoints-overview.md).
    * N'oubliez pas qu'avec les points de terminaison de service, votre fonction dispose toujours d'un accès sortant complet à Internet, même si l'intégration au réseau virtuel est configurée.
* L'accès aux sites privés est également disponible via une instance d'Azure App Service Environment configurée avec un équilibreur de charge interne (ILB). Pour plus d’informations, consultez [Créer et utiliser un équilibreur de charge interne avec un Azure App Service Environment](../app-service/environment/create-ilb-ase.md).

## <a name="virtual-network-integration"></a>Intégration du réseau virtuel

L’intégration de réseau virtuel permet à votre Function App d’accéder aux ressources au sein d’un réseau virtuel. Cette fonctionnalité est disponible dans le plan Premium et le plan App Service. Si votre application se trouve dans un Azure App Service Environment, elle se trouve déjà dans un réseau virtuel et il n’est pas nécessaire l’intégration de réseau virtuel pour accéder aux ressources de ce même réseau virtuel.

Vous pouvez utiliser l’intégration de réseau virtuel pour permettre l’accès depuis des applications à des bases de données et à des services web qui s’exécutent dans votre réseau virtuel. Avec l’intégration de réseau virtuel, vous n’avez pas besoin d’exposer un point de terminaison public pour les applications sur votre machine virtuelle. Vous pouvez utiliser des adresses routables non Internet privées à la place.

L’intégration au réseau virtuel se présente sous deux formes :

+ **Intégration du réseau virtuel régional (préversion)**  : Active l’intégration aux réseaux virtuels d’une même région. Ce type d’intégration nécessite un sous-réseau dans un réseau virtuel de la même région. Cette fonctionnalité est toujours en préversion, mais elle est prise en charge pour les applications de fonction s’exécutant sur Windows, avec les avertissements décrits après la table Problème/Solution suivante.
+ **Intégration au réseau virtuel avec passerelle obligatoire** : Permet une intégration à des réseaux virtuels situés dans des régions distantes ou à des réseaux virtuels classiques. Ce type d’intégration nécessite le déploiement d'une passerelle de réseau virtuel dans votre réseau virtuel. Il s’agit d’une fonctionnalité basée sur un VPN point à site, qui est prise en charge uniquement pour les applications de fonction s’exécutant sur Windows.

Une application ne peut utiliser qu’un seul type de fonctionnalité d’intégration de réseau virtuel à la fois. Bien que les deux soient utiles dans de nombreux scénarios, le tableau suivant renseigne sur l’utilisation de chacun :

| Problème  | Solution |
|----------|----------|
| Nécessité d'accéder à une adresse RFC 1918 (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16) dans la même région | Intégration du réseau virtuel régional |
| Vous souhaitez accéder aux ressources d’un réseau virtuel classique ou d’un réseau virtuel dans une autre région | Intégration au réseau virtuel avec passerelle obligatoire |
| Nécessité d'accéder à des points de terminaison RFC 1918 via Azure ExpressRoute | Intégration du réseau virtuel régional |
| Nécessité d'accéder à des ressources via des points de terminaison de service | Intégration du réseau virtuel régional |

Aucune des fonctionnalités ne vous permet d'accéder à des adresses non compatibles avec RFC 1918 via ExpressRoute. Pour ce faire, vous devez utiliser un App Service Environment.

L’utilisation de l’intégration de réseau virtuel régional ne permet pas de connecter votre réseau virtuel à des points de terminaison locaux ou de configurer des points de terminaison de service. Il s'agit d'une configuration de mise en réseau distincte. L'intégration au réseau virtuel régional permet simplement à votre application de passer des appels via ces types de connexion.

Quelle que soit la version utilisée, l'intégration au réseau virtuel permet à votre application de fonction d'accéder aux ressources de votre réseau virtuel, mais ne lui accorde pas d'accès aux sites privés à partir du réseau virtuel. L’accès aux sites privés fait référence au fait de rendre votre application accessible uniquement à partir d’un réseau privé, par exemple à partir d’un réseau virtuel Azure. L'intégration au réseau virtuel sert uniquement à passer des appels sortants de votre application vers votre réseau virtuel.

La fonctionnalité d’intégration au réseau virtuel :

* requiert un plan App Service Standard, Premium ou PremiumV2 ;
* prend en charge les protocoles TCP et UDP ;
* fonctionne avec les applications App Service et les applications de fonction.

L’intégration au réseau virtuel ne prend pas en charge certaines choses, notamment :

* montage d’un lecteur ;
* Intégration Active Directory
* NetBIOS

Dans Azure Functions, l'intégration au réseau virtuel utilise une infrastructure partagée avec les applications Web App Service. Pour en savoir plus sur les deux types d'intégration au réseau virtuel, consultez :

* [Intégration du réseau virtuel régional](../app-service/web-sites-integrate-with-vnet.md#regional-vnet-integration)
* [Intégration au réseau virtuel avec passerelle obligatoire](../app-service/web-sites-integrate-with-vnet.md#gateway-required-vnet-integration)

Pour en savoir plus sur l'utilisation de l'intégration au réseau virtuel, consultez [Intégrer une application de fonction à un réseau virtuel Azure](functions-create-vnet.md).

## <a name="connecting-to-service-endpoint-secured-resources"></a>Connexion à des ressources sécurisées de point de terminaison de service

> [!NOTE]
> Une fois que vous avez configuré des restrictions d’accès sur la ressource en aval, pour l’instant, il peut s’écouler jusqu’à 12 heures avant que les nouveaux points de terminaison de service ne soient disponibles pour votre application de fonction. Pendant ce temps, la ressource sera complètement inaccessible pour votre application.

Afin d’offrir un niveau de sécurité supérieur, vous pouvez restreindre un nombre de services Azure sur un réseau virtuel en utilisant des points de terminaison de service. Vous devez ensuite intégrer votre application de fonction à ce réseau virtuel pour accéder à la ressource. Cette configuration est prise en charge sur tous les plans qui prennent en charge l’intégration du réseau virtuel.

[En savoir plus sur les points de terminaison de service de réseau virtuel.](../virtual-network/virtual-network-service-endpoints-overview.md)

### <a name="restricting-your-storage-account-to-a-virtual-network"></a>Restriction de votre compte de stockage à un réseau virtuel

Quand vous créez une application de fonction, vous devez créer un compte de stockage Azure à usage général qui prend en charge le stockage Blob, File d’attente et Table, ou établir un lien vers un compte de ce type. Actuellement, vous ne pouvez pas utiliser de restrictions de réseau virtuel sur ce compte. La configuration d’un point de terminaison de service de réseau virtuel sur le compte de stockage que vous utilisez pour votre application de fonction entraînera l’arrêt de votre application.

[Apprenez-en davantage sur les exigences liées aux comptes de stockage.](./functions-create-function-app-portal.md#storage-account-requirements)

### <a name="using-key-vault-references"></a>Utiliser des références Key Vault 

Les références Key Vault vous permettent d’utiliser des secrets d’Azure Key Vault dans votre application Azure Functions, sans avoir à modifier le code. Azure Key Vault est un service qui fournit une gestion centralisée des secrets, avec un contrôle total sur les stratégies d’accès et l’historique d’audit.

Actuellement, les références [Key Vault](../app-service/app-service-key-vault-references.md) ne fonctionneront pas si votre Key Vault est sécurisé à l’aide de points de terminaison de service. Pour vous connecter à un Key Vault à l’aide de l’intégration de réseau virtuel, vous devez appeler le coffre de clés dans votre code d’application.

## <a name="virtual-network-triggers-non-http"></a>Déclencheurs de réseau virtuel (non HTTP)

Vous pouvez utiliser des fonctions de déclencheur non-HTTP à partir d’un réseau virtuel de deux manières : 
+ Exécutez votre application de fonction dans un plan Premium et activez la prise en charge des déclencheurs de réseau virtuel.
+ Exécutez votre application de fonction dans un plan ou environnement App Service.

### <a name="premium-plan-with-virtual-network-triggers"></a>Plan Premium avec déclencheurs de réseau virtuel

En cas d'exécution dans un plan Premium, vous pouvez connecter des fonctions de déclencheur non-HTTP vers des services s'exécutant au sein d’un réseau virtuel. Pour ce faire, vous devez activer la prise en charge des déclencheurs de réseau virtuel pour votre application de fonction. Le paramètre de **prise en charge des déclencheurs de réseau virtuel** se trouve dans le [portail Azure](https://portal.azure.com), sous **Paramètres de l’application de fonction**.

![VNETToggle](media/functions-networking-options/virtual-network-trigger-toggle.png)

Vous pouvez également activer les déclencheurs de réseau virtuel à l’aide de la commande Azure CLI suivante :

```azurecli-interactive
az resource update -g <resource_group> -n <function_app_name>/config/web --set properties.functionsRuntimeScaleMonitoringEnabled=1 --resource-type Microsoft.Web/sites
```

Les déclencheurs de réseau virtuel sont pris en charge dans la version 2.x et les versions ultérieures du runtime Functions. Les types de déclencheurs non-HTTP suivants sont pris en charge.

| Extension | Version minimale |
|-----------|---------| 
|[Microsoft.Azure.WebJobs.Extensions.Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage/) | 3.0.10 ou ultérieure |
|[Microsoft.Azure.WebJobs.Extensions.EventHubs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs)| 4.1.0 ou ultérieure|
|[Microsoft.Azure.WebJobs.Extensions.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.ServiceBus)| 3.2.0 ou ultérieure|
|[Microsoft.Azure.WebJobs.Extensions.CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB)| 3.0.5 ou ultérieure|
|[Microsoft.Azure.WebJobs.Extensions.DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask)| 2.0.0 ou ultérieure|

> [!IMPORTANT]
> Lorsque vous activez la prise en charge des déclencheurs de réseau virtuel, seuls les types de déclencheurs ci-dessus sont mis à l’échelle dynamiquement avec votre application. Vous pouvez cependant utiliser les déclencheurs non répertoriés ci-dessus, mais ils ne sont pas mis à l’échelle au-delà de leur nombre d’instances chauffées au préalable. Pour obtenir la liste complète des déclencheurs, consultez [Déclencheurs et liaisons](./functions-triggers-bindings.md#supported-bindings).

### <a name="app-service-plan-and-app-service-environment-with-virtual-network-triggers"></a>Plan App Service et App Service Environment avec déclencheurs de réseau virtuel

Lorsque votre application de fonction s’exécute dans un plan App Service ou App Service Environment, vous pouvez utiliser des fonctions de déclencheur non-HTTP. Pour le bon déclenchement de vos fonctions, vous devez être connecté à un réseau virtuel, avec accès à la ressource définie dans la connexion de déclenchement. 

Supposons, par exemple, que vous souhaitiez configurer Azure Cosmos DB pour accepter le trafic uniquement à partir d’un réseau virtuel. Dans ce cas, vous devez déployer votre application de fonction dans un plan App Service fournissant une intégration de réseau virtuel à ce réseau virtuel. Ainsi, une fonction peut être déclenchée par cette ressource Azure Cosmos DB. 

## <a name="hybrid-connections"></a>les connexions hybrides

[Connexions hybrides](../service-bus-relay/relay-hybrid-connections-protocol.md) est une fonctionnalité d’Azure Relay que vous pouvez utiliser pour accéder aux ressources d’application dans d’autres réseaux. Elles permettent d’accéder depuis votre application à un point de terminaison d’application. Vous ne pouvez pas l’utiliser pour accéder à votre application. Connexions hybrides est disponible pour les fonctions exécutées sur Windows dans tous les plans, sauf le plan Consommation.

Utilisée dans Azure Functions, chaque connexion hybride correspond à une combinaison d’hôte et de port TCP unique. Cela signifie que le point de terminaison de connexion hybride peut se trouver sur un quelconque système d’exploitation et toute application tant que vous accédez à un port d’écoute TCP. La fonctionnalité Connexions hybrides ne détecte pas et ne prend pas en compte le protocole d’application ou les ressources auxquels vous accédez. Elle fournit simplement un accès réseau.

Pour plus d’informations, consultez la [documentation App Service pour les connexions hybrides](../app-service/app-service-hybrid-connections.md). Ces mêmes étapes de configuration prennent en charge Azure Functions.

>[!IMPORTANT]
> Les connexions hybrides sont uniquement prises en charge dans les plans Windows. Linux n'est pas pris en charge.

## <a name="outbound-ip-restrictions"></a>Restrictions d’adresse IP sortantes

Les restrictions d’adresse IP sortantes sont uniquement disponibles pour les fonctions déployées sur un Azure App Service Environment. Vous pouvez configurer des restrictions sortantes pour le réseau virtuel sur lequel votre Azure App Service Environment est déployé.

Lorsque vous intégrez une application de fonction à un réseau virtuel dans le cadre d'un plan Premium ou App Service, l'application est toujours en mesure de passer des appels sortants vers Internet.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur la mise en réseau et Azure Functions :

* [Tutoriel pour bien démarrer avec l’intégration de réseau virtuel](./functions-create-vnet.md)
* [Lire les questions fréquentes (FAQ) sur la mise en réseau de Functions](./functions-networking-faq.md)
* [En savoir plus sur l’intégration de réseau virtuel avec App Service/Functions](../app-service/web-sites-integrate-with-vnet.md)
* [En savoir plus sur les réseaux virtuels dans Azure](../virtual-network/virtual-networks-overview.md)
* [Activer davantage de fonctionnalités de réseau et plus de contrôle avec Azure App Service Environment](../app-service/environment/intro.md)
* [Se connecter à des ressources locales sans modifier le pare-feu à l’aide des connexions hybrides](../app-service/app-service-hybrid-connections.md)
