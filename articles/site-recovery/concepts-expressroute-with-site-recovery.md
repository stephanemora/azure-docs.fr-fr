---
title: Azure ExpressRoute avec Azure Site Recovery | Microsoft Docs
description: Cet article explique comment utiliser Azure ExpressRoute avec Azure Site Recovery pour la récupération d’urgence et la migration.
services: site-recovery
documentationcenter: ''
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: mayg
ms.openlocfilehash: 13d07546f1d82782e211213816b53373913e80b3
ms.sourcegitcommit: 8e06d67ea248340a83341f920881092fd2a4163c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/16/2018
ms.locfileid: "49353881"
---
# <a name="azure-expressroute-with-azure-site-recovery"></a>Azure ExpressRoute avec Azure Site Recovery

Microsoft Azure ExpressRoute vous permet d’étendre vos réseaux locaux au cloud de Microsoft via une connexion privée assurée par un fournisseur de connectivité. Grâce à ExpressRoute, vous pouvez établir des connexions aux services de cloud computing Microsoft, comme Microsoft Azure, Office 365 et Dynamics 365.

Cet article décrit comment utiliser Azure ExpressRoute avec Azure Site Recovery pour la récupération d’urgence et la migration.

## <a name="expressroute-circuits"></a>Circuits ExpressRoute

Un circuit ExpressRoute représente une connexion logique entre votre infrastructure locale et des services de cloud computing Microsoft via un fournisseur de connexion. Vous pouvez commander plusieurs circuits ExpressRoute. Chaque circuit peut se trouver dans une région identique ou différente des autres, et peut être connecté à votre site via des fournisseurs de connectivité. Pour en savoir plus sur les circuits ExpressRoute, [voir ici](../expressroute/expressroute-circuit-peerings.md).

## <a name="expressroute-routing-domains"></a>Domaines de routage ExpressRoute

Un circuit ExpressRoute dispose de plusieurs domaines de routage qui lui sont associés :
-   [Homologation privée Azure](../expressroute/expressroute-circuit-peerings.md#azure-private-peering) : les services de calcul Azure, à savoir les machines virtuelles (IaaS), et les services cloud (PaaS) déployés au sein d’un réseau virtuel peuvent être connectés via le domaine d’homologation privée. Celui-ci est considéré comme une extension de confiance de votre réseau de base dans Microsoft Azure.
-   [Homologation publique Azure](../expressroute/expressroute-circuit-peerings.md#azure-public-peering) : des services tels que le Stockage, les bases de données SQL et les sites web Azure sont disponibles sur des adresses IP publiques. En privé, vous pouvez vous connecter à des services hébergés sur des adresses IP publiques (y compris les adresses IP virtuelles de vos services cloud) via le domaine de routage d’homologation publique. L’homologation publique est déconseillée pour les créations, et une homologation Microsoft doit être utilisée à la place pour les services Azure PaaS.
-   [Homologation Microsoft](../expressroute/expressroute-circuit-peerings.md#microsoft-peering) : la connexion aux services en ligne Microsoft (services Office 365, Dynamics 365 et Azure PaaS) s’effectue via l’homologation Microsoft. L’homologation Microsoft est le domaine de routage recommandé pour se connecter aux services Azure PaaS.

Pour en savoir plus et comparer les domaines de routage ExpressRoute, [voir ici](../expressroute/expressroute-circuit-peerings.md#routing-domain-comparison).

## <a name="on-premises-to-azure-replication-with-expressroute"></a>Réplication de service local sur Azure avec ExpressRoute

Azure Site Recovery permet une récupération d’urgence et une migration sur Azure de [machines virtuelles Hyper-V](hyper-v-azure-architecture.md), de [machines virtuelles VMware](vmware-azure-architecture.md) et de [serveurs physiques](physical-azure-architecture.md) locaux. Pour tous les scénarios de réplication de service local sur Azure, les données sont envoyées à un compte de stockage Azure et stockées sur celui-ci. Lors d’une réplication, vous ne payez aucuns frais de machine virtuelle. Quand vous exécutez un basculement vers Azure, Site Recovery crée automatiquement des machines virtuelles Azure IaaS.

Azure Site Recovery réplique les données sur un compte de stockage Azure, via un point de terminaison public. Pour utiliser ExpressRoute afin d’effectuer une réplication Site Recovery, vous pouvez utiliser une [homologation publique](../expressroute/expressroute-circuit-peerings.md#azure-public-peering) ou une [homologation Microsoft](../expressroute/expressroute-circuit-peerings.md#microsoft-peering). L’homologation Microsoft est le domaine de routage recommandé pour la réplication. Après que des machines virtuelles ou des serveurs ont basculé vers un réseau virtuel Azure, vous pouvez y accéder à l’aide d’une [homologation privée](../expressroute/expressroute-circuit-peerings.md#azure-private-peering). La réplication n’est pas pris en charge via une homologation privée.

Le scénario combiné est représenté dans le diagramme suivant : ![De local à Azure avec ExpressRoute](./media/concepts-expressroute-with-site-recovery/site-recovery-with-expressroute.png)

## <a name="azure-to-azure-replication-with-expressroute"></a>Réplication d’Azure sur Azure avec ExpressRoute

Azure Site Recovery permet une récupération d’urgence de [machines virtuelles Azure](azure-to-azure-architecture.md). Selon que vos machines virtuelles Azure utilisent ou non [Azure Managed Disks](../virtual-machines/windows/managed-disks-overview.md), les données de réplication sont envoyées à un compte de stockage Azure ou à un disque managé de réplica dans la région Azure cible. Bien que les points de terminaison de réplication soient publics, le trafic de réplication pour la réplication de machines virtuelles Azure, par défaut, ne passe pas par Internet, quelle que soit la région Azure dans laquelle se trouve le réseau virtuel source. Vous pouvez remplacer l’itinéraire système par défaut d’Azure pour le préfixe d’adresse 0.0.0.0/0 par un [itinéraire personnalisé](../virtual-network/virtual-networks-udr-overview.md#custom-routes) et rediriger le trafic des machines virtuelles vers une appliance virtuelle réseau locale, mais cette configuration n’est pas recommandée pour la réplication Site Recovery. Si vous utilisez des itinéraires personnalisés, vous devez [créer un point de terminaison de service de réseau virtuel](azure-to-azure-about-networking.md#create-network-service-endpoint-for-storage) dans votre réseau virtuel pour le « Stockage », afin que le trafic de réplication ne quitte pas la limite Azure.

Pour la récupération d’urgence de machine virtuelle Azure, par défaut, ExpressRoute n’est pas requis pour la réplication. Après le basculement des machines virtuelles vers la région Azure cible, vous pouvez y accéder via une [homologation privée](../expressroute/expressroute-circuit-peerings.md#azure-private-peering).

Si vous utilisez déjà ExpressRoute pour vous connecter à partir de votre centre de données local aux machines virtuelles Azure dans la région source, vous pouvez planifier un rétablissement de la connectivité ExpressRoute dans la région cible du basculement. Vous pouvez utiliser le même circuit ExpressRoute pour vous connecter à la région cible via une nouvelle connexion de réseau virtuel, ou utiliser un circuit et une connexion ExpressRoute distincts pour la récupération d’urgence. Les différents scénarios possibles sont décrits [ici](azure-vm-disaster-recovery-with-expressroute.md#fail-over-azure-vms-when-using-expressroute).

Vous pouvez répliquer des machines virtuelles Azure vers n’importe quelle région Azure au sein du même cluster géographique, comme expliqué [ici](../site-recovery/azure-to-azure-support-matrix.md#region-support). Si la région Azure cible choisie ne figure pas dans la même région géopolitique que la source, il se peut que vous deviez activer ExpressRoute Premium. Pour plus d’informations, voir [Emplacements ExpressRoute](../expressroute/expressroute-locations.md#azure-regions-to-expressroute-locations-within-a-geopolitical-region) et [Tarification ExpressRoute](https://azure.microsoft.com/pricing/details/expressroute/).

## <a name="next-steps"></a>Étapes suivantes
- En savoir plus sur les [circuits ExpressRoute](../expressroute/expressroute-circuit-peerings.md).
- En savoir plus sur les [domaines de routage ExpressRoute](../expressroute/expressroute-circuit-peerings.md#expressroute-routing-domains).
- En savoir plus sur les [emplacements ExpressRoute](../expressroute/expressroute-locations.md).
- En savoir plus sur la récupération d’urgence de [machines virtuelles Azure via ExpressRoute](azure-vm-disaster-recovery-with-expressroute.md).
