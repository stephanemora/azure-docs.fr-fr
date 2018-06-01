---
title: Utilisation d’ExpressRoute avec la récupération d’urgence de machines virtuelles Azure | Microsoft Docs
description: Explique comment utiliser Azure ExpressRoute avec Azure Site Recovery pour la récupération d’urgence de machines virtuelles Azure
services: site-recovery
documentationcenter: ''
author: mayanknayar
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 05/11/2018
ms.author: manayar
ms.openlocfilehash: 44ecbcc51cb53f4d7b68f5c5e24e7d81c5a4208c
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/11/2018
ms.locfileid: "34071585"
---
# <a name="using-expressroute-with-azure-virtual-machine-disaster-recovery"></a>Utilisation d’ExpressRoute avec la récupération d’urgence de machines virtuelles Azure

Microsoft Azure ExpressRoute vous permet d’étendre vos réseaux locaux au cloud de Microsoft via une connexion privée assurée par un fournisseur de connectivité. Cet article décrit comment vous pouvez utiliser ExpressRoute avec Site Recovery pour la récupération d’urgence des machines virtuelles Azure.

## <a name="prerequisites"></a>Prérequis


Avant de commencer, vérifiez que vous connaissez suffisamment les sujets suivants :
-   [Circuits](../expressroute/expressroute-circuit-peerings.md) ExpressRoute
-   [Domaines de routage](../expressroute/expressroute-circuit-peerings.md#expressroute-routing-domains) ExpressRoute
-   [Architecture de réplication](azure-to-azure-architecture.md) des machines virtuelles Azure
-   [Configuration de la réplication](azure-to-azure-tutorial-enable-replication.md) pour les machines virtuelles
-   [Basculement](azure-to-azure-tutorial-failover-failback.md) des machines virtuelles Azure

## <a name="expressroute-and-azure-virtual-machine-replication"></a>Réplication des machines virtuelles Azure et ExpressRoute

Lors de la protection des machines virtuelles Azure avec Site Recovery, les données de réplication sont envoyées à un compte de stockage Azure ou à un réplica de disque managé sur la région Azure cible, en fonction de l’utilisation ou non [d’Azure Managed Disks](../virtual-machines/windows/managed-disks-overview.md) par vos machines virtuelles Azure. Bien que les points de terminaison de réplication soient publics, le trafic de réplication pour la réplication de machines virtuelles Azure, par défaut, ne passe pas par Internet, quelle que soit la région Azure dans laquelle le réseau virtuel source se trouve.

Pour la récupération d’urgence des machines virtuelles Azure, comme les données de réplication ne quittent pas la limite Azure, ExpressRoute n’est pas requis pour la réplication. Après le basculement des machines virtuelles vers la région Azure cible, vous pouvez y accéder à l’aide d’une [homologation privée](../expressroute/expressroute-circuit-peerings.md#azure-private-peering).

## <a name="replicating-azure-deployments"></a>Réplication des déploiements Azure

Dans un [article](site-recovery-retain-ip-azure-vm-failover.md#on-premises-to-azure-connectivity) plus ancien, nous vous expliquions comment effectuer une configuration simple avec un réseau virtuel Azure connecté à un centre de données client local via ExpressRoute. Les déploiements d’entreprise classiques ont des charges de travail réparties sur plusieurs réseaux virtuels Azure et un Hub de connectivité central qui établit une connectivité externe, à la fois à Internet et aux déploiements locaux.

Cet exemple illustre une topologie hub and spoke, plutôt fréquente dans les déploiements d’entreprise :
-   Le déploiement se trouve dans la région **Azure Asie de l’Est** et le centre de données local a une connexion de circuit ExpressRoute via un réseau de périphérie partenaire à Hong Kong.
-   Les applications sont déployées sur les deux réseaux virtuels spoke : **Source VNet1** avec l’espace d’adressage 10.1.0.0/24 et **Source VNet2** avec l’espace d’adressage 10.2.0.0/24.
-   Le réseau virtuel hub, **Source Hub VNet**, avec l’espace d’adressage 10.10.10.0/24 agit en tant qu’opérateur de contrôle. Toutes les communications entre les sous-réseaux passent par le Hub.
-   Le réseau virtuel hub a deux sous-réseaux : **NVA Subnet** avec l’espace d’adressage 10.10.10.0/25 et **Gateway Subnet** avec l’espace d’adressage 10.10.10.128/25.
-   Le sous-réseau **NVA Subnet** a une appliance virtuelle réseau avec l’adresse IP 10.10.10.10.
-   Le sous-réseau **Gateway Subnet** a une passerelle ExpressRoute connectée à une connexion ExpressRoute qui achemine le trafic vers le centre de données client local via un domaine de routage d’homologation privée.
-   Chaque réseau virtuel spoke est connecté au réseau virtuel hub et tout le routage au sein de cette topologie de réseau est contrôlé via les tables de routage Azure (UDR). Tout le trafic sortant d’un réseau virtuel et se dirigeant vers l’autre réseau virtuel ou le centre de données local est routé via l’appliance virtuelle réseau.

![De l’emplacement local vers Azure avant le basculement](./media/azure-vm-disaster-recovery-with-expressroute/site-recovery-with-expressroute-before-failover.png)

### <a name="hub-and-spoke-peering"></a>Homologation hub and spoke

L’homologation spoke vers hub est configurée comme suit :
-   Autoriser l’accès au réseau virtuel : Activé
-   Autoriser le trafic transféré : Activé
-   Autoriser le transit par passerelle : Désactivé
-   Use remote gateways (Utiliser des passerelles à distance) : Activé

 ![Configuration de l’homologation spoke vers hub](./media/azure-vm-disaster-recovery-with-expressroute/spoke-to-hub-peering-configuration.png)

L’homologation hub vers spoke est configurée comme suit :
-   Autoriser l’accès au réseau virtuel : Activé
-   Autoriser le trafic transféré : Activé
-   Autoriser le transit par passerelle : Activé
-   Use remote gateways (Utiliser des passerelles à distance) : Désactivé

 ![Configuration de l’homologation hub vers spoke](./media/azure-vm-disaster-recovery-with-expressroute/hub-to-spoke-peering-configuration.png)

### <a name="enabling-replication-for-the-deployment"></a>Activation de la réplication pour le déploiement

Pour la configuration ci-dessus, commencez par [configurer la récupération d’urgence](azure-to-azure-tutorial-enable-replication.md) pour chaque machine virtuelle à l’aide de Site Recovery. Site Recovery peut créer des réplicas de réseaux virtuels (y compris des sous-réseaux et des sous-réseaux de passerelle) sur la région cible et créer les mappages nécessaires entre les réseaux virtuels sources et cibles. Vous pouvez également créer au préalable les sous-réseaux et réseaux cibles et les utiliser au moment de l’activation de la réplication.

Site Recovery ne réplique pas les tables de routage, les passerelles de réseau virtuel, les connexions de passerelle de réseau virtuel l’homologation de réseau virtuel ou les autres connexions ou ressources réseau. Tous ces éléments et les autres ressources ne font pas partie du [processus de réplication](azure-to-azure-architecture.md#replication-process) et doivent être créés avant le basculement ou au cours de celui-ci et être connectés aux ressources appropriées. Vous pouvez utiliser les puissants [plans de récupération](site-recovery-create-recovery-plans.md) d’Azure Site Recovery pour automatiser la création et la connexion de ressources supplémentaires à l’aide de scripts d’automatisation.

Par défaut, le trafic de réplication ne quitte pas la limite Azure. En règle générale, les déploiements d’appliance virtuelle réseau définissent également un itinéraire par défaut (0.0.0.0/0) qui force le trafic Internet sortant à circuler via l’appliance virtuelle réseau. Dans ce cas, l’appliance peut être limitée si tout le trafic de réplication passe par celle-ci. La même règle s’applique également lors de l’utilisation d’itinéraires par défaut pour le routage de tout le trafic des machines virtuelles Azure vers des déploiements locaux. Nous vous recommandons de [créer un point de terminaison de service réseau](azure-to-azure-about-networking.md#create-network-service-endpoint-for-storage) dans votre réseau virtuel pour « Stockage » afin que le trafic de réplication ne quitte pas la limite Azure.

## <a name="failover-models-with-expressroute"></a>Modèles de basculement avec ExpressRoute

Lorsque des machines virtuelles Azure sont basculées vers une autre région, la connexion ExpressRoute existante sur le réseau virtuel source n’est pas automatiquement transférée sur le réseau virtuel cible dans la région de récupération. Une nouvelle connexion est nécessaire pour connecter ExpressRoute au réseau virtuel cible.

Vous pouvez répliquer des machines virtuelles Azure sur n’importe quelle région Azure au sein du même cluster géographique comme expliqué [ici](azure-to-azure-support-matrix.md#region-support). Dans le cas où la région Azure cible choisie n’est pas dans la même région géopolitique que la source, vous devez activer ExpressRoute Premium si vous utilisez un seul circuit ExpressRoute pour la connectivité à la région source et à la région cible. Pour plus d’informations, consultez la section [Régions Azure vers des emplacements ExpressRoute au sein d’une région géopolitique.](../expressroute/expressroute-locations.md#azure-regions-to-expressroute-locations-within-a-geopolitical-region) et la page [Tarification ExpressRoute](https://azure.microsoft.com/pricing/details/expressroute/).

### <a name="two-expressroute-circuits-in-two-different-expressroute-peering-locations"></a>Deux circuits ExpressRoute dans deux emplacements d’homologation ExpressRoute différents
-   Cette configuration est utile si vous souhaitez assurer une protection en cas de défaillance au niveau du circuit ExpressRoute principal et en cas d’incidents régionaux à grande échelle, qui peuvent également toucher les emplacements d’homologation ExpressRoute et interrompre votre circuit ExpressRoute principal.
-   Normalement, le circuit connecté à l’environnement de production est utilisé en tant que circuit principal, et le circuit secondaire est un circuit de prévention de défaillance avec une bande passante généralement plus faible. La bande passante du circuit secondaire peut être augmentée en cas d’incident, quand le circuit secondaire doit prendre le relais du circuit principal.
-   Avec cette configuration, vous pouvez établir les connexions à partir de votre circuit ExpressRoute secondaire vers le réseau virtuel cible suite au basculement ou avoir les connexions établies et prêtes pour une déclaration d’incident, ce qui réduit votre temps de récupération global. Avec des connexions simultanées aux réseaux virtuels de la région cible et principale, vérifiez que votre routage local utilise le circuit et la connexion secondaire uniquement après le basculement.
-   Les réseaux virtuels sources et cibles des machines virtuelles protégées par Site Recovery peuvent avoir des adresses IP identiques ou différentes lors du basculement, en fonction de vos besoins. Dans les deux cas, les connexions secondaires peuvent être établies avant le basculement.

### <a name="two-expressroute-circuits-in-the-same-expressroute-peering-location"></a>Deux circuits ExpressRoute dans le même emplacement d’homologation ExpressRoute
-   Avec cette configuration, vous pouvez assurer une protection en cas de défaillance au niveau du circuit ExpressRoute principal, mais pas en cas d’incidents régionaux à grande échelle, qui peuvent également toucher les emplacements d’homologation ExpressRoute. Avec cette configuration, les circuits principaux et secondaires peuvent être touchés.
-   Les autres conditions pour les adresses IP et les connexions restent les mêmes que celles indiquées dans la configuration précédente. Vous pouvez avoir des connexions simultanées entre le centre de données local et le réseau virtuel source avec le circuit principal et vers le réseau virtuel cible avec le circuit secondaire. Avec des connexions simultanées aux réseaux virtuels de la région cible et principale, vérifiez que votre routage local utilise le circuit et la connexion secondaire uniquement après le basculement.
-   Vous ne pouvez pas connecter les deux circuits au même réseau virtuel quand les circuits sont créés au même emplacement d’homologation.

### <a name="single-expressroute-circuit"></a>Circuit ExpressRoute unique
-   Cette configuration n’assure pas de protection en cas d’incident régional à grande échelle, qui pourrait toucher l’emplacement d’homologation ExpressRoute.
-   Avec un circuit ExpressRoute unique, vous ne pouvez pas connecter simultanément les réseaux virtuels sources et cibles au circuit si le même espace d’adressage IP est utilisé sur la région cible.
-   Lorsque le même espace d’adressage IP est utilisé sur la région cible, la connexion du côté source doit être interrompue, et la connexion du côté cible doit être établie par la suite. Cette modification de la connexion peut faire l’objet d’un script dans le cadre d’un plan de récupération.
-   En cas de défaillance régionale, si la région primaire n’est pas accessible, l’opération de déconnexion peut échouer. Une telle défaillance peut avoir un impact sur la création de la connexion à la région cible quand le même espace d’adressage IP est utilisé sur le réseau virtuel cible.
-   Si la création de la connexion réussit sur la cible et si la région primaire récupère ultérieurement, vous pouvez être confronté à des pertes de paquets quand deux connexions simultanées essaient de se connecter au même espace d’adressage. Pour éviter les pertes de paquets, la connexion principale doit se terminer immédiatement. Après la restauration automatique des machines virtuelles sur la région primaire, la connexion principale peut de nouveau être établie après l’interruption de la connexion secondaire.
-   Si un espace d’adressage différent est utilisé sur le réseau virtuel cible, vous pouvez vous connecter simultanément aux réseaux virtuels sources et cibles du même circuit ExpressRoute.

## <a name="recovering-azure-deployments"></a>Récupération des déploiements Azure
Imaginez le modèle de basculement avec deux circuits ExpressRoute distincts dans deux emplacements d’homologation différents et la rétention d’adresse IP privée pour les machines virtuelles Azure protégées. La région de récupération Azure cible est Asie du Sud-Est et une connexion de circuit ExpressRoute secondaire est établie via un réseau de périphérie partenaire à Singapour.

Pour automatiser la récupération de la totalité du déploiement, en plus de répliquer des machines virtuelles et des réseaux virtuels, d’autres connexions et ressources réseau doivent également être créées. Pour la topologie de réseau hub and spoke précédente, les étapes supplémentaires suivantes doivent être réalisées au cours de l’opération de [basculement](azure-to-azure-tutorial-failover-failback.md) ou après celle-ci :
1.  Créez la passerelle Azure ExpressRoute dans le réseau virtuel hub de la région cible. La passerelle ExpressRoute est requise pour connecter le réseau virtuel hub cible au circuit ExpressRoute.
2.  Créez la connexion de réseau virtuel à partir du réseau virtuel hub cible vers le circuit ExpressRoute cible.
3.  Configurez les homologations de réseau virtuel entre les réseaux virtuels hub and spoke de la région cible. Les propriétés d’homologation sur la région cible seront identiques à celles de la région source.
4.  Configurez les UDR dans le réseau virtuel hub et les deux réseaux virtuels spoke. Les propriétés des UDR côté cible sont les mêmes que celles côté source lorsque vous utilisez des adresses IP identiques. Avec des adresses IP cibles différentes, les UDR doivent être modifiés en conséquence.

Les étapes ci-dessus peuvent faire l’objet d’un script dans le cadre d’un [plan de récupération](site-recovery-create-recovery-plans.md). En fonction des exigences en matière de temps de récupération et de connectivité de l’application, les étapes ci-dessus peuvent également être réalisées avant le début du basculement.

Après la récupération des machines virtuelles et à la fin des autres étapes de connectivité, l’environnement de récupération se présente comme suit : ![Emplacement local vers Azure avec ExpressRoute après le basculement](./media/azure-vm-disaster-recovery-with-expressroute/site-recovery-with-expressroute-after-failover.png)

Un exemple de topologie simple pour la récupération d’urgence de machines virtuelles Azure avec un seul circuit ExpressRoute, avec la même adresse IP sur les machines virtuelles cibles, est détaillé [ici](site-recovery-retain-ip-azure-vm-failover.md#on-premises-to-azure-connectivity).

## <a name="recovery-time-objective-rto-considerations"></a>Remarques sur les objectifs de délai de récupération
Pour réduire le temps de récupération global de votre déploiement, nous vous recommandons d’approvisionner et de déployer au préalable les [composants réseau](azure-vm-disaster-recovery-with-expressroute.md#enabling-replication-for-the-deployment) supplémentaires de la région cible telles que les passerelles de réseau virtuel. Le déploiement des ressources supplémentaires est assorti d’un temps d’arrêt court qui peut avoir un impact sur le temps de récupération global s’il n’est pas pris en compte lors de la planification.

Nous vous recommandons d’exécuter régulièrement des [simulations de récupération d’urgence](azure-to-azure-tutorial-dr-drill.md) pour les déploiements protégés. Une simulation valide votre stratégie de réplication sans perte de données ou temps d’arrêt et n’affecte pas votre environnement de production. L’exécution d’une simulation permet également d’éviter les problèmes de configuration de dernière minute qui peuvent avoir un impact défavorable sur votre objectif de temps de récupération. Pour le test de basculement, nous vous recommandons d’utiliser un réseau de machines virtuelles Azure distinct plutôt que le réseau par défaut qui était configuré quand vous avez activé la réplication.

Si vous utilisez un seul circuit ExpressRoute, nous vous recommandons d’utiliser un autre espace d’adressage IP pour le réseau virtuel cible afin d’éviter des problèmes d’établissement de connexion pendant les incidents régionaux. Si vous ne pouvez pas utiliser des adresses IP différentes dans votre environnement de production récupéré, le basculement test de la simulation de récupération d’urgence peut être effectué sur un réseau test distinct avec des adresses IP différentes, car vous ne pouvez pas connecter deux réseaux virtuels avec un espace d’adressage IP se chevauchant sur le même circuit ExpressRoute.

## <a name="next-steps"></a>Étapes suivantes
- En savoir plus sur les [circuits ExpressRoute](../expressroute/expressroute-circuit-peerings.md).
- En savoir plus sur les [domaines de routage ExpressRoute](../expressroute/expressroute-circuit-peerings.md#expressroute-routing-domains).
- En savoir plus sur les [emplacements ExpressRoute](../expressroute/expressroute-locations.md).
- En savoir plus sur les [plans de récupération](site-recovery-create-recovery-plans.md) pour automatiser le basculement d’application
