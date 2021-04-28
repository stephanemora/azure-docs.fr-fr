---
title: Documentation sur l’architecture Azure Networking
description: Découvrez la documentation sur l’architecture de référence disponible pour les services Azure Networking.
services: networking
author: KumudD
ms.service: virtual-network
ms.topic: article
ms.date: 03/30/2021
ms.author: kumud
ms.openlocfilehash: 8ed448f9f4f8dc688271f2ce671b2d89da0f342f
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108146809"
---
# <a name="azure-networking-architecture-documentation"></a>Documentation sur l’architecture Azure Networking

Cet article fournit des informations sur des guides d’architecture qui peuvent vous aider à explorer les différents services de mise en réseau disponibles dans Azure pour la création de vos applications.

## <a name="networking-overview"></a>Vue d’ensemble de la mise en réseau

Le tableau suivant contient des articles qui fournissent une vue d’ensemble de mise en réseau d’un centre de données virtuel et d’une topologie hub-and-spoke dans Azure.

|Intitulé |Description  |
|---------|---------|
|[Centres de données virtuels](/azure/architecture/vdc/networking-virtual-datacenter)   | Fournit une perspective de mise en réseau d’un centre de données virtuel dans Azure.       |
|[Topologie hub-and-spoke](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)  |Fournit une vue d’ensemble de la topologie de réseau hub-and-spoke dans Azure, ainsi que des informations sur les limites d’abonnement et les hubs multiples.          |

## <a name="connect-to-azure-resources"></a>Se connecter aux ressources Azure

Le tableau suivant contient des articles sur les services Azure Networking qui assurent la connectivité entre ressources Azure, la connectivité d’un réseau local à des ressources Azure, et la connectivité entre branches dans Azure.

|Intitulé |Description  |
|---------|---------|
|[Ajouter des espaces d’adressage IP à des réseaux virtuels appairés](/azure/architecture/networking/prefixes/add-ip-space-peered-vnet)     | Fournit des scripts qui facilitent l’ajout d’espaces d’adressage IP à des réseaux virtuels appairés.        |
|[Connecter des serveurs autonomes à l’aide d’une carte réseau Azure](/azure/architecture/hybrid/azure-network-adapter)   | Montre comment connecter un serveur autonome local à des réseaux virtuels Microsoft Azure à l’aide de la carte réseau Azure que vous déployez via Windows Admin Center.        |
|[Choisir entre le peering de réseau virtuel et les passerelles VPN](/azure/architecture/reference-architectures/hybrid-networking/vnet-peering)   | Compare deux façons de connecter des réseaux virtuels dans Azure : l’appairage de réseaux virtuels et les passerelles VPN.        |
|[Connecter un réseau local à Azure](/azure/architecture/reference-architectures/hybrid-networking/)  | Compare les options de connexion d’un réseau local à un réseau virtuel Azure (VNet). Pour chaque option, une architecture de référence plus détaillée est disponible.        |
|[Architecture de connectivité SD-WAN avec Azure Virtual WAN](../../virtual-wan/sd-wan-connectivity-architecture.md)|Décrit les différentes options de connectivité pour l’interconnexion d’un SD-WAN (Software Defined WAN) privé avec un Azure Virtual WAN.|

## <a name="deploy-highly-available-applications"></a>Déployer des applications hautement disponibles

Le tableau suivant contient des articles décrivant comment déployer vos applications pour la haute disponibilité à l’aide d’une combinaison de services Azure Networking.

|Intitulé |Description  |
|---------|---------|
|[Application multiniveau multirégion](/azure/architecture/reference-architectures/n-tier/multi-region-sql-server)  | Décrit une application multiniveau multirégion qui utilise Traffic Manager pour acheminer des requêtes entrantes vers une région primaire. Et si cette région n’est plus disponible, Traffic Manager bascule vers la région secondaire.      |
| [SaaS mutualisé sur Azure](/azure/architecture/example-scenario/multi-saas/multitenant-saas)       |   Utilise une solution mutualisée incluant une combinaison de Front Door et d’Application Gateway.  Front Door permet d’équilibrer la charge du trafic entre les régions et les itinéraires Application Gateway et d’équilibrer la charge du trafic en interne dans l’application vers les différents services qui répondent aux besoins de l’entreprise du client.  |
| [Application web multiniveau développée pour la haute disponibilité et la reprise d’activité ](/azure/architecture/example-scenario/infrastructure/multi-tier-app-disaster-recovery)        |      Déploie des applications multiniveau résilientes conçues pour la haute disponibilité et la récupération d’urgence. Si la région primaire n’est plus disponible, Traffic Manager bascule vers la région secondaire.  |
|[IaaS : Application Web avec base de données relationnelle](/azure/architecture/high-availability/ref-arch-iaas-web-and-db)    |   Décrit comment utiliser des ressources réparties dans plusieurs zones afin de fournir une architecture de haute disponibilité pour l’hébergement d’une application web IaaS (infrastructure as a service) et d’une base de données SQL Server.     |
|[Partage d’emplacement en temps réel avec des services Azure serverless à faible coût](/azure/architecture/example-scenario/signalr/#azure-front-door)       |   Utilise Azure Front Door pour fournir une disponibilité plus élevée à vos applications que le déploiement dans une seule région. Si une interruption de service régionale affecte la région principale, vous pouvez utiliser Front Door pour basculer vers la région secondaire.      |
|[Appliances réseau virtuelles hautement disponible](/azure/architecture/reference-architectures/dmz/nva-ha)     | Montre comment déployer des appliances virtuelles réseau pour la haute disponibilité dans Azure.        |
|[Équilibrage de charge sur plusieurs régions avec Traffic Manager et Application Gateway](/azure/architecture/high-availability/reference-architecture-traffic-manager-application-gateway)     | Explique comment déployer des applications à plusieurs niveaux résilientes dans plusieurs régions Azure, afin d’assurer la disponibilité et d’obtenir une infrastructure de reprise d’activité fiable.        |

## <a name="secure-your-network-resources"></a>Sécuriser vos ressources réseau

Le tableau suivant contient des articles décrivant comment protéger vos ressources réseau à l’aide de services Azure Networking.

|Intitulé |Description  |
|---------|---------|
|[Meilleures pratiques en matière de sécurité réseau](../../security/fundamentals/network-best-practices.md) |Décrit une collection de meilleures pratiques Azure pour améliorer la sécurité de votre réseau.         |
[Guide d’architecture du Pare-feu Azure](/azure/architecture/example-scenario/firewalls/) | Présente une approche structurée de la conception des pare-feu à haute disponibilité dans Azure à l’aide d’appliances virtuelles tierces.        |
|[Implémenter un réseau hybride sécurisé](/azure/architecture/reference-architectures/dmz/secure-vnet-dmz)     | Décrit une architecture implémentant un réseau de périmètre entre le réseau local et un réseau virtuel Azure. Tout le trafic entrant et sortant transite par le pare-feu Azure.        |
|[Sécuriser et gouverner les charges de travail avec une segmentation au niveau réseau](/azure/architecture/reference-architectures/hybrid-networking/network-level-segmentation) | Décrit les trois modèles courants utilisés pour organiser les charges de travail dans Azure dans une perspective de mise en réseau.   Chacun de ces modèles fournit un type différent d’isolement et de connectivité.      |
|[Pare-feu et Application Gateway pour réseaux virtuels](/azure/architecture/example-scenario/gateway/firewall-application-gateway) | Décrit des services de sécurité de réseau virtuel Azure, tels que le Pare-feu Azure et Azure Application Gateway, explique quand utiliser chaque service, et présente des options de conception de réseau qui combinent les deux.      |

## <a name="next-steps"></a>Étapes suivantes

Découvrez ce qu’est un [réseau virtuel Azure](../../virtual-network/virtual-networks-overview.md).