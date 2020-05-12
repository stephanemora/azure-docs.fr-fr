---
title: Réduire les coûts de service grâce à Azure Advisor
description: Le conseiller Azure permet d’optimiser le coût de vos déploiements Azure.
ms.topic: article
ms.date: 01/29/2019
ms.openlocfilehash: 13e7b1d7c6b0fe342020c40e1bb4abeba97d18bb
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/05/2020
ms.locfileid: "82788091"
---
# <a name="reduce-service-costs-using-azure-advisor"></a>Réduire les coûts de service grâce à Azure Advisor

Le conseiller vous aide à optimiser et à réduire votre dépense Azure globale en identifiant les ressources inactives et sous-utilisées. Vous pouvez obtenir des recommandations en matière de coûts dans l’onglet **Coût** du tableau de bord Advisor.

## <a name="optimize-virtual-machine-spend-by-resizing-or-shutting-down-underutilized-instances"></a>Optimiser le coût de la machine virtuelle en redimensionnant ou en arrêtant les instances sous-utilisées 

Alors que certains scénarios d’application peuvent par définition entraîner une faible utilisation, vous pouvez souvent faire des économies grâce à la gestion de la taille de vos machines virtuelles et de leur nombre. Les Modèles d’évaluation avancée d’Advisor envisagent l’arrêt d’une machine virtuelle lorsqu’un P95ème de la valeur maximale d’utilisation de l’UC est inférieur à 3 % et que l’utilisation du réseau est inférieure à 2 % sur une période de 7 jours. La taille des machines virtuelles est considérée comme adéquate lorsqu'il est possible d'intégrer la charge actuelle dans une référence SKU plus petite (au sein de la même famille de références SKU) ou dans un plus petit nombre d'instances, de manière à ce que la charge actuelle ne dépasse pas 80 % d'utilisation pour les charges de travail non orientées utilisateur et 40 % pour les charges de travail orientées utilisateur. Le type de charge de travail est ici déterminé en analysant les caractéristiques d'utilisation de l'UC de la charge de travail.

Les actions recommandées sont l'arrêt ou le redimensionnement, en fonction des ressources recommandées. Advisor vous fournit une estimation des économies réalisables avec l'une ou l'autre des actions recommandées (redimensionnement ou arrêt). En outre, pour l'action recommandée de redimensionnement, Advisor fournit des informations sur les références SKU actuelles et cibles. 

Si vous souhaitez être plus « agressif » dans l’identification des machines virtuelles sous-utilisées, vous pouvez ajuster la règle d’utilisation du processeur par abonnement.

## <a name="reduce-costs-by-eliminating-unprovisioned-expressroute-circuits"></a>Réduire les coûts en éliminant les circuits ExpressRoute non approvisionnés

Advisor identifie les circuits ExpressRoute qui présentent l’état de fournisseur *Non approvisionné* depuis plus d’un mois et recommande la suppression de ces circuits si vous ne prévoyez pas de les approvisionner avec votre fournisseur de connectivité.

## <a name="reduce-costs-by-deleting-or-reconfiguring-idle-virtual-network-gateways"></a>Réduire les coûts en supprimant ou en reconfigurant les passerelles de réseau virtuel inactives

Advisor identifie les passerelles de réseau virtuel qui sont restées inactives pendant plus de 90 jours. Dans la mesure où ces passerelles sont facturées par heure, vous devez envisager de reconfigurer ou de les supprimer si vous ne souhaitez plus les utiliser. 

## <a name="buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs"></a>Acheter des instances de machine virtuelle réservées pour économiser sur les coûts du paiement à l’utilisation

Advisor examinera votre utilisation des machines virtuelles sur les 30 derniers jours pour déterminer si vous pouvez faire des économies en achetant une réservation Azure. Advisor vous montrera les régions et les tailles où vous pouvez potentiellement réaliser le plus d’économies et vous présentera une estimation des économies générées grâce à l’achat de réservations. Avec les réservations Azure, vous pouvez acheter au préalable les coûts de base de vos machines virtuelles. Des remises seront automatiquement appliquées aux machines virtuelles nouvelles ou existantes ayant la même taille et la même région que vos réservations. [En savoir plus sur Azure Reserved VM Instances.](https://azure.microsoft.com/pricing/reserved-vm-instances/)

Advisor vous informera également des instances réservées dont vous disposez et qui arriveront à expiration au cours des 30 prochains jours. Il vous recommandera d'acheter de nouvelles instances réservées pour vous éviter les tarifs du paiement à l'utilisation.

## <a name="delete-unassociated-public-ip-addresses-to-save-money"></a>Supprimer les adresses IP publiques non associées pour réaliser des économies

Advisor identifie les adresses IP publiques non associées à des ressources Azure telles que les équilibreurs de charge ou machines virtuelles. Ces adresses IP publiques sont fournies pour une somme modique. Si vous n'envisagez pas de les utiliser, vous pouvez les supprimer pour réaliser des économies.

## <a name="delete-azure-data-factory-pipelines-that-are-failing"></a>Supprimer les pipelines Azure Data Factory en échec

Azure Advisor détectera les pipelines Azure Data Factory en échec de manière répétée et vous recommandera de corriger les problèmes ou de supprimer les pipelines en échec dont vous n'avez plus besoin. Vous serez facturé pour ces pipelines même s'ils sont en échec et ne vous sont dès lors d'aucune utilité. 

## <a name="use-standard-snapshots-for-managed-disks"></a>Utiliser des instantanés Standard pour les disques managés
Pour économiser jusqu'à 60 %, nous vous recommandons de stocker vos instantanés dans Stockage Standard, quel que soit le type de stockage du disque parent. Il s’agit de l’option par défaut pour les instantanés de disques managés. Azure Advisor identifiera les instantanés stockés dans Stockage Premium et vous recommandera de les migrer de Stockage Premium vers Stockage Standard. [En savoir plus sur la tarification des disques managés](https://aka.ms/aa_manageddisksnapshot_learnmore)

## <a name="utilize-lifecycle-management"></a>Utiliser la gestion du cycle de vie
Azure Advisor utilisera l’intelligence pour ce qui concerne la taille totale, les transactions et le nombre d’objets du stockage Blob Azure, afin de détecter si un ou plusieurs de vos comptes de stockage sont les plus adaptés pour permettre à la gestion du cycle de vie de hiérarchiser les données. Vous serez invité à créer des règles de gestion du cycle de vie pour hiérarchiser automatiquement vos données dans les niveaux Froid ou Archive, afin d’optimiser vos coûts de stockage tout en conservant vos données dans le stockage d’objets blob Azure pour la compatibilité des applications.

## <a name="create-an-ephemeral-os-disk-recommendation"></a>Créer une recommandation de disque de système d’exploitation éphémère
Avec un [disque de système d’exploitation éphémère](https://docs.microsoft.com/azure/virtual-machines/windows/ephemeral-os-disks), les clients bénéficient des avantages suivants : Réduction du coût de stockage pour le disque de système d’exploitation. Diminution de la latence de lecture/écriture sur le disque de système d’exploitation. Opération de réinitialisation de machine virtuelle plus rapide du fait de la restauration du système d’exploitation (et du disque temporaire) à son état d’origine. Il est préférable d’utiliser le disque de système d’exploitation éphémère pour les machines virtuelles IaaS à courte durée de vie ou les machines virtuelles supportant des charges de travail sans état. Advisor fournit des recommandations pour les ressources qui peuvent tirer parti des avantages que présente le disque de système d’exploitation éphémère. 

## <a name="how-to-access-cost-recommendations-in-azure-advisor"></a>Accès aux recommandations de coût dans Azure Advisor

1. Connectez-vous au [portail Azure](https://portal.azure.com).

1. Recherchez et sélectionnez [**Advisor**](https://aka.ms/azureadvisordashboard) à partir de n’importe quelle page.

1. Dans le tableau de bord **Advisor**, sélectionnez l’onglet **Coût**.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les recommandations d’Advisor, consultez les ressources suivantes :
* [Présentation du conseiller](advisor-overview.md)
* [Bien démarrer](advisor-get-started.md)
* [Recommandations du conseiller en matière de performances](advisor-performance-recommendations.md)
* [Recommandations du conseiller en matière de haute disponibilité](advisor-high-availability-recommendations.md)
* [Recommandations du conseiller en matière de sécurité](advisor-security-recommendations.md)
* [Recommandations d’excellence opérationnelle Advisor](advisor-operational-excellence-recommendations.md)
