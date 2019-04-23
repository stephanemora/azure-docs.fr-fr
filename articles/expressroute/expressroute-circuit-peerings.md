---
title: Circuits Azure ExpressRoute et peering | Microsoft Docs
description: Cette page fournit une vue d’ensemble des circuits ExpressRoute et des domaines de routage/peering.
services: expressroute
author: mialdrid
ms.service: expressroute
ms.topic: conceptual
ms.date: 11/05/2018
ms.author: mialdridm
ms.custom: seodec18
ms.openlocfilehash: 35cee297156cf64deeef8c9c6b514ec8176f9ca5
ms.sourcegitcommit: c884e2b3746d4d5f0c5c1090e51d2056456a1317
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2019
ms.locfileid: "60149966"
---
# <a name="expressroute-circuits-and-peering"></a>Circuits ExpressRoute et peering

Les circuits ExpressRoute vous permettent de connecter votre infrastructure locale à Microsoft via un fournisseur de connectivité. Cet article vous aide à comprendre les circuits ExpressRoute et les domaines de routage/le peering. La figure suivante affiche une représentation logique de la connectivité entre votre WAN et Microsoft.

![](./media/expressroute-circuit-peerings/expressroute-basic.png)

> [!IMPORTANT]
> L’homologation publique Azure a été déconseillée, car elle n'est pas disponible pour les nouveaux circuits ExpressRoute. Les nouveaux circuits prennent en charge l’homologation Microsoft et l’homologation privée.  
>

## <a name="circuits"></a>Circuits ExpressRoute
Un circuit ExpressRoute représente une connexion logique entre votre infrastructure locale et des services de cloud computing Microsoft via un fournisseur de connexion. Vous pouvez commander plusieurs circuits ExpressRoute. Chaque circuit peut se trouver dans une région identique ou différente des autres, et peut être connecté à votre site via des fournisseurs de connectivité.

Les circuits ExpressRoute ne sont mappés à aucune entité physique. Un circuit est identifié par un GUID standard appelé clé de service (s-key). La clé de service est la seule information échangée entre Microsoft, le fournisseur de connectivité, et vous. La clé de service n'est pas secrète pour des raisons de sécurité. Il existe un mappage 1:1 entre un circuit ExpressRoute et la clé de service.

Les nouveaux circuits ExpressRoute peuvent inclure deux peerings indépendants : Le peering privé et le peering Microsoft. En revanche, les circuits ExpressRoute existants peuvent contenir trois peerings : public Azure, privé Azure et Microsoft. Chaque homologation est une paire de sessions BGP indépendantes, chacune configurée de manière redondante pour offrir une disponibilité optimale. Il existe un mappage 1:N (1 <= N <= 3) entre un circuit ExpressRoute et des domaines de routage. Un circuit ExpressRoute peut avoir une, deux ou trois homologations activées par circuit ExpressRoute.

Chaque circuit offre une bande passante fixe (50 Mbps, 100 Mbps, 200 Mbps, 500 Mbps, 1 Gbps, 10 Gbps) et est mappé à un fournisseur de connectivité et un emplacement d’homologation. La bande passante sélectionnée est partagée par toutes les homologations de circuit.

### <a name="quotas"></a>Quotas, limites et limitations
Des limites et quotas par défaut s'appliquent à chaque circuit ExpressRoute. Consultez la page [Limites, quotas et contraintes applicables à l’abonnement et au service Azure](../azure-subscription-service-limits.md) pour obtenir des informations actualisées sur les quotas.

## <a name="routingdomains"></a>Peering ExpressRoute
Un circuit ExpressRoute a plusieurs domaines de routage/peerings qui lui sont associés : public Azure, privé Azure et Microsoft. Chaque peering est configuré de manière identique sur une paire de routeurs (en configuration actif-actif ou de partage de la charge) pour la haute disponibilité. Les services Azure sont classés en *Public Azure* et *Privé Azure* pour représenter les schémas d’adressage IP.

![](./media/expressroute-circuit-peerings/expressroute-peerings.png)

### <a name="privatepeering"></a>Homologation privée Azure
Les services de calcul Azure, à savoir les machines virtuelles (IaaS) et les services cloud (PaaS) déployés au sein d’un réseau virtuel peuvent être connectés via le domaine d’homologation privée. Celui-ci est considéré comme une extension de confiance de votre réseau de base dans Microsoft Azure. Vous pouvez configurer une connectivité bidirectionnelle entre votre réseau de base et les réseaux virtuels Azure. Cette homologation vous permet de vous connecter aux machines virtuelles et services cloud directement sur leurs adresses IP privées.  

Vous pouvez connecter plusieurs réseaux virtuels au domaine d’homologation privée. Pour plus d’informations sur les limitations, consultez le [Forum Aux Questions](expressroute-faqs.md) . Vous pouvez consulter la page [Limites, quotas et contraintes applicables à l’abonnement et au service Azure](../azure-subscription-service-limits.md) pour obtenir des informations actualisées sur les limites.  Reportez-vous à la page [Routage](expressroute-routing.md) pour plus d'informations sur la configuration du routage.

### <a name="microsoftpeering"></a>Homologation Microsoft

[!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

La connectivité aux services en ligne Microsoft (Office 365, Dynamics 365 et les services Azure PaaS) s’effectue via l’homologation Microsoft. Nous activons la connectivité bidirectionnelle entre votre réseau étendu et les services cloud Microsoft via le domaine de routage d’homologation Microsoft. Vous devez vous connecter aux services cloud Microsoft uniquement via des adresses IP publiques qui sont détenues par vous ou votre fournisseur de connectivité. Vous devez également respecter toutes les règles définies. Pour plus d’informations, consultez la page [Composants requis ExpressRoute](expressroute-prerequisites.md).

Pour plus d’informations sur les services pris en charge, les coûts et les détails de configuration, consultez le [Forum Aux Questions](expressroute-faqs.md) . Pour plus d’informations sur la liste des fournisseurs de connectivité offrant une prise en charge de l’homologation Microsoft, consultez la page [Emplacements ExpressRoute](expressroute-locations.md)

### <a name="publicpeering"></a>Peering public Azure (déprécié pour les nouveaux circuits)

> [!Note]
> L’homologation publique Azure a 1 adresse IP NAT sont associée à chaque session BGP. Pour plus de 2 adresses IP NAT, passez à [l’homologation Microsoft](https://docs.microsoft.com/en-us/azure/expressroute/how-to-move-peering), où vous pouvez configurer vos propres allocations NAT, ainsi utiliser des filtres de routage pour les publications de préfixe sélectif. 
>

Les services tels qu’Azure Storage, les bases de données SQL et Sites web sont proposés sur des adresses IP publiques. En privé, vous pouvez vous connecter à des services hébergés sur des adresses IP publiques (y compris les adresses IP virtuelles de vos services cloud) via le domaine de routage d’homologation publique. Vous pouvez connecter le domaine d’homologation publique à votre zone DMZ et vous connecter à tous les services Azure sur leurs adresses IP publiques à partir de votre réseau étendu, sans avoir à vous connecter via Internet.

La connectivité est toujours initiée de votre réseau étendu vers les services Microsoft Azure. Ces derniers ne sont pas en mesure d’initier des connexions à votre réseau via ce domaine de routage. Une fois l’homologation publique activée, vous êtes en mesure de vous connecter à tous les services Azure. Nous ne vous permettons pas de sélectionner les services pour lesquels nous publions les itinéraires.

Vous pouvez définir des filtres d’itinéraires personnalisés au sein de votre réseau pour utiliser uniquement les itinéraires dont vous avez besoin. Reportez-vous à la page [Routage](expressroute-routing.md) pour plus d'informations sur la configuration du routage.

Pour plus d’informations sur les services pris en charge à travers le domaine de routage de l’homologation publique, consultez la [FAQ](expressroute-faqs.md).

## <a name="peeringcompare"></a>Comparaison de peerings
Le tableau suivant compare les trois types de peering :

|  | **Homologation privée** | **Homologation Microsoft** |  **Peering public** (déprécié pour les nouveaux circuits) |
| --- | --- | --- | --- |
| **Nb max. de préfixes pris en charge par homologation** |4 000 par défaut, 10 000 avec ExpressRoute Premium |200 |200 |
| **Plages d’adresses IP prises en charge** |Toute adresse IP valide au sein de votre réseau étendu. |Adresses IP publiques détenues par vous ou par votre fournisseur de connectivité. |Adresses IP publiques détenues par vous ou par votre fournisseur de connectivité. |
| **Exigences en matière de numéros AS** |Numéros AS publics et privés Vous devez être propriétaire du numéro AS public si vous choisissez d’en utiliser un. |Numéros AS publics et privés Cependant, vous devez prouver la propriété des adresses IP publiques. |Numéros AS publics et privés Cependant, vous devez prouver la propriété des adresses IP publiques. |
| **Protocoles IP pris en charge**| IPv4 |  IPv4, IPv6 | IPv4 |
| **Adresses IP de l’interface de routage** |RFC1918 et adresses IP publiques |Adresses IP publiques enregistrées auprès de vous dans les registres de routage. |Adresses IP publiques enregistrées auprès de vous dans les registres de routage. |
| **Prise en charge du hachage MD5** |Oui |OUI |Oui |

Vous pouvez activer un ou plusieurs domaines de routage dans le cadre de votre circuit ExpressRoute. Vous pouvez choisir de placer tous les domaines de routage sur le même VPN si vous souhaitez les combiner dans un domaine de routage unique. Vous pouvez également les placer dans différents domaines de routage comme indiqué dans le schéma. Nous vous recommandons de connecter l’homologation privée directement à votre réseau principal, et les homologations publiques et Microsoft à votre zone DMZ.

Chaque homologation requiert des sessions BGP distinctes (une paire pour chaque type d’homologation). Les paires de session BGP fournissent un lien hautement disponible. Si vous vous connectez via des fournisseurs de connectivité de couche 2, il vous incombe de configurer et de gérer le routage. Pour en savoir plus, passez en revue les [workflows](expressroute-workflows.md) d’ExpressRoute.

## <a name="health"></a>Intégrité ExpressRoute
Les circuits ExpressRoute peuvent faire l’objet d’une surveillance en matière de disponibilité, de connectivité aux réseaux virtuels et d’utilisation de la bande passante à l’aide de [Network Performance Monitor](https://docs.microsoft.com/azure/networking/network-monitoring-overview) (NPM).

NPM supervise l’intégrité du peering privé Azure et du peering Microsoft. Pour plus d’informations, consultez notre [billet](https://azure.microsoft.com/blog/monitoring-of-azure-expressroute-in-preview/).

## <a name="next-steps"></a>Étapes suivantes
* Recherchez un fournisseur de services. Consultez la rubrique [Emplacements et fournisseurs de services ExpressRoute](expressroute-locations.md).
* Assurez-vous que toutes les conditions préalables sont remplies. Consultez la page [Configuration requise pour ExpressRoute](expressroute-prerequisites.md).
* Configurez votre connexion ExpressRoute.
  * [Créer et gérer des circuits ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md)
  * [Configurer l’acheminement (homologation) pour les circuits ExpressRoute](expressroute-howto-routing-portal-resource-manager.md)
