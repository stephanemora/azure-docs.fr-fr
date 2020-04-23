---
title: À propos d’Azure Edge Zone – Préversion
description: En savoir plus sur les offres Edge Computing de Microsoft.
services: vnf-manager
author: ganesr
ms.service: vnf-manager
ms.topic: article
ms.date: 04/02/2020
ms.author: ganesr
ms.openlocfilehash: aaa849633591bfd34a9fca026c820ec2f9137844
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81410833"
---
# <a name="about-azure-edge-zones---preview"></a>À propos d’Azure Edge Zone – Préversion

Azure Edge Zones est une famille d’offres de Microsoft Azure qui permet le traitement des données à proximité de l’utilisateur. Vous pouvez déployer des machines virtuelles, des conteneurs et certains services Azure dans Edge Zone pour répondre aux exigences de faible latence et de débit élevé des applications.

Les scénarios d’utilisation classiques pour les zones périphériques sont les suivants :

- Commande et contrôle en temps réel en robotique
- Analytique et inférence en temps réel avec l’intelligence artificielle et le Machine Learning
- Vision industrielle
- Rendu à distance pour les scénarios VDI et de réalité mixte
- Gaming multijoueur immersif
- Diffusion multimédia en continu et distribution de contenu
- Surveillance et sécurité

Azure Edge Zone est disponible en trois offres discrètes :

- Azure Edge Zones
- Azure Edge Zones avec opérateur
- Azure Private Edge Zones

## <a name="azure-edge-zones"></a><a name="edge-zones"></a>Azure Edge Zones

![Edge Zones](./media/edge-zones-overview/edge-zones.png "Zones périphériques")

Les instances Azure Edge Zone sont des extensions Azure à faible empreinte mémoire qui sont placées dans des centres de population éloignés des régions Azure. Elles prennent en charge les machines virtuelles, les conteneurs et une sélection de services Azure qui permettent d’exécuter des applications soumises à contrainte de latence et gourmandes en débit au plus près des utilisateurs finaux. Elles font partie du réseau mondial de Microsoft et offrent une connectivité sécurisée, fiable et à large bande passante entre les applications qui s’exécutent dans la zone périphérique proche de l’utilisateur ainsi que l’ensemble complet des services Azure s’exécutant dans les régions Azure. Les Azure Edge Zones sont détenues et gérées par Microsoft et vous permettent d’utiliser le même ensemble d’outils et de portail Azure pour gérer et déployer des services dans les zones périphériques.

Les cas d’utilisation classiques sont les suivants :

- Gaming et diffusion de jeux en continu
- Diffusion multimédia en continu et distribution de contenu
- Analytique et inférence en temps réel à l’aide de l’intelligence artificielle et du Machine Learning
- Rendu pour la réalité mixte

Les Azure Edge Zones seront disponibles dans les zones métropolitaines suivantes :

- New York
- Los Angeles, CA
- Miami, FL

Pour plus d’informations, [contactez l’équipe Edge Zones](https://aka.ms/EdgeZones).

## <a name="azure-edge-zones-with-carrier"></a><a name="carrier"></a>Azure Edge Zones avec opérateur

![Edge Zones with Carrier](./media/edge-zones-overview/edge-carrier.png "Edge Zones avec opérateur")

Les instances Azure Edge Zone avec opérateur sont des extensions Azure à faible empreinte mémoire qui sont placées dans les centres de donnés des opérateurs de téléphonie mobile situés dans des centres de population. L’infrastructure Azure Edge Zone avec opérateur est placée à un tronçon du réseau 5G de l’opérateur de téléphonie mobile, offrant une latence inférieure à 10 millisecondes aux applications d’appareils mobiles. L’offre Azure Edge Zone avec opérateur est déployée dans les centres de données des opérateurs de téléphonie mobile et connectée au réseau mondial de Microsoft. Elle offre une connectivité sécurisée, fiable et à large bande passante entre les applications qui s’exécutent à proximité de l’utilisateur ainsi que l’ensemble complet des services Azure s’exécutant dans les régions Azure. Les développeurs peuvent utiliser le même ensemble d’outils habituels pour créer et déployer des services dans les zones périphériques.

Les cas d’utilisation classiques sont les suivants :

- Gaming et diffusion de jeux en continu
- Diffusion multimédia en continu et distribution de contenu
- Analytique et inférence en temps réel à l’aide de l’intelligence artificielle et du Machine Learning
- Rendu pour la réalité mixte
- Véhicules connectés
- Télémédecine

Les zones périphériques seront proposées en partenariat avec les opérateurs suivants :

- AT&T (Atlanta, Dallas et Los Angeles)

## <a name="azure-private-edge-zones"></a><a name="private-edge-zones"></a>Azure Private Edge Zones

![Private Edge Zones](./media/edge-zones-overview/private-edge.png "Private Edge Zones")

Les instances Azure Private Edge Zones sont des extensions Azure à faible empreinte mémoire placées localement. Elles sont basées sur la plateforme [Azure Stack Edge](https://azure.microsoft.com/products/azure-stack/edge/) et permettent un accès à faible latence aux services de calcul et de stockage déployés localement. Ces zones périphériques privées vous permettent également de déployer des fonctions réseau virtualisées (VNF), telles que des cœurs de paquets mobiles, des routeurs, des pare-feu et des appliances SD-WAN, ainsi que des applications d’éditeurs de logiciels indépendants (ISV) comme les [applications managées Azure](https://azure.microsoft.com/services/managed-applications/) côte à côte avec des machines virtuelles et des conteneurs locaux. L’offre Azure Private Edge Zones est proposée avec une solution d’orchestration native Cloud qui vous permet de gérer le cycle de vie des fonctions réseau virtualisées (VNF) et des applications à partir du Portail Azure.

Elle vous permet de développer et de déployer des applications localement à l’aide des mêmes outils habituels utilisés pour créer et déployer des applications dans Azure. Vous pouvez également exécuter des réseaux mobiles privés (LTE privé, 5G privé) et des fonctions de sécurité telles que des pare-feu, étendre vos réseaux locaux sur plusieurs branches et sur Azure à l’aide d’appliances SD-WAN sur les mêmes appliances Private Edge Zone et les gérer à partir d’Azure.

Les cas d’utilisation classiques sont les suivants :

- Commande et contrôle en temps réel en robotique
- Analytique et inférence en temps réel avec l’intelligence artificielle et le Machine Learning
- Vision industrielle
- Rendu à distance pour les scénarios VDI et de réalité mixte
- Surveillance et sécurité

Nous disposons d’un écosystème complet de fournisseurs VNF, d’ISV et de partenaires MSP pour activer des solutions de bout en bout à l’aide d’instances Private Edge Zones. Pour plus d’informations, [contactez l’équipe Private Edge Zones](https://aka.ms/EdgeZonesPartner).

## <a name="private-edge-zones---partners"></a><a name="private-edge-partners"></a>Private Edge Zones – Partenaires

![Private Edge Zone Partners](./media/edge-zones-overview/partners.png "Partenaires Private Edge Zone")

### <a name="virtualized-network-functions-vnfs"></a><a name="vnf"></a>Fonctions réseau virtualisées (VNF)

#### <a name="virtualized-evolved-packet-core-vepc-for-mobile-networks"></a><a name="vEPC"></a>Evolved Packet Core virtualisé (vEPC) pour réseaux mobiles

- [Affirmed Networks](https://www.affirmednetworks.com/)
- [Druid Software](https://www.druidsoftware.com/)
- [Expeto](https://www.expeto.io/)
- [Mavenir](https://mavenir.com/)
- [Metaswitch](https://www.metaswitch.com/)
- [Nokia Digital Automation Cloud](https://www.dac.nokia.com/)

#### <a name="mobile-radio-partners"></a><a name="mobile-radio"></a>Partenaires de radio mobile

- [Commscope Ruckus](https://support.ruckuswireless.com/)

#### <a name="sd-wan-vendors"></a><a name="sdwan-vendors"></a>Fournisseurs SD-WAN

- [NetFoundry](https://netfoundry.io/)
- [NuageNetworks de Nokia](https://www.nuagenetworks.net/)
- [SD-WAN VMware de Velocloud](https://www.velocloud.com/)

#### <a name="router-vendors"></a><a name="router-vendors"></a>Fournisseurs de routeur

- [Arista](https://www.arista.com/)

Pour plus d’informations sur la façon de devenir un partenaire, [contactez l’équipe Private Edge Zones](https://aka.ms/EdgeZonesPartner).

#### <a name="firewall-vendors"></a><a name="firewall-vendors"></a>Fournisseurs de pare-feu

- Palo Alto Networks

### <a name="managed-solutions-providers---mobile-operators-and-global-system-integrators"></a><a name="msp-mobile"></a>Fournisseurs de solutions managées – Opérateurs de téléphonie mobile et intégrateurs de systèmes mondiaux

| Intégrateurs de systèmes mondiaux et opérateurs | Opérateurs de téléphonie mobile |
| --- | --- |
| Amdocs                       | Etisalat             |
| American Tower               | NTT Communications   |
| Century Link                 | Proximus             |
| Expeto                       | Rogers               |
| Federated Wireless           | SK Telecom           |
| Infosys                      | Telefonica           |
| Tech Mahindra                | Telstra              |
|        *                     | Vodafone             |

Pour plus d’informations sur la façon de devenir un partenaire, [contactez l’équipe Private Edge Zones](https://aka.ms/EdgeZonesPartner).

## <a name="private-edge-zones---solutions"></a><a name="solutions-private-edge"></a>Private Edge Zones – Solutions

### <a name="private-mobile-network-on-private-edge-zones"></a><a name="private-mobile-private-edge"></a>Private Mobile Network sur Private Edge Zones

![Private Mobile Network on Private Edge Zones](./media/edge-zones-overview/mobile-networks.png "Private Mobile Network sur Private Edge Zones")

Vous pouvez maintenant déployer un réseau mobile privé sur des zones périphériques privées. Les réseaux mobiles privés permettent d’avoir une latence très faible, une capacité élevée et un réseau sans fil fiable et sécurisé, requis pour les applications stratégiques. Les réseaux mobiles privés peuvent permettre des scénarios tels que la commande et le contrôle des véhicules guidés automatisés (AGV) dans un entrepôt, la communication en temps réel entre les robots dans une usine intelligente et la réalité augmentée et des applications de réalité virtuelle de pointe.

La fonction réseau vEPC (Evolved Packet Core virtualisé) constitue le cerveau d’un réseau mobile privé. Vous pouvez maintenant déployer un vEPC sur Private Edge Zones. Pour obtenir la liste des partenaires vEPC disponibles sur les zones périphériques privées, consultez [ISV vEPC](#vEPC).

Le déploiement d’une solution de réseau mobile privé sur Private Edge Zones requiert d’autres composants tels que des points d’accès mobiles, des cartes SIM et d’autres VNF (p. ex. : des routeurs). L’accès à un spectre sous licence ou sans licence est essentiel à la configuration d’un réseau mobile privé. En outre, vous pouvez avoir besoin d’aide pour la planification RF, la disposition physique, l’installation et le support. Pour obtenir la liste des partenaires, consultez [Partenaires de radio mobile](#mobile-radio).

Microsoft fournit un écosystème de partenaires qui peut vous aider dans tous les aspects de ce processus : de la planification du réseau à la gestion de la configuration à partir d’Azure, en passant par l’achat des appareils requis et la configuration du matériel. Grâce à un ensemble de partenaires agréés et étroitement intégrés à Microsoft, vous avez l’assurance que la solution sera fiable et facile à utiliser. Vous pouvez vous concentrer sur vos scénarios clés, Microsoft et ses partenaires seront là pour vous aider avec le reste.

### <a name="sd-wan-on-private-edge-zones"></a><a name="sdwan-private-edge">SD-WAN sur Private Edge Zones</a>

![SD-WAN on Private Edge Zones](./media/edge-zones-overview/sd-wan.png "SD-WAN sur Private Edge Zones")
 
La technologie SD-WAN vous permet de créer des réseaux étendus (WAN, Wide Area Networks) de niveau entreprise avec une bande passante accrue, un accès haute performance au cloud, l’insertion de services, la fiabilité, la gestion des stratégies et une visibilité étendue du réseau. SD-WAN fournit une connectivité transparente pour les succursales qui est orchestrée à partir de contrôleurs centraux redondants, à un coût de possession réduit.
SD-WAN sur Private Edge Zones vous permet de passer du modèle centré sur les investissements à un modèle SaaS (Software-as-a-service) pour réduire les budgets informatiques. Vous pouvez utiliser l’orchestrateur ou le contrôleur des partenaires SD-WAN de votre choix pour activer de nouveaux services et les propager immédiatement sur l’ensemble du réseau.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, contactez les équipes suivantes :

* [Équipe Edge Zones](https://aka.ms/EdgeZones)
* [Équipe Private Edge Zones : pour devenir partenaire](https://aka.ms/EdgeZonesPartner)
