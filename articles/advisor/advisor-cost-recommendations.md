---
title: Réduire les coûts de service grâce à Azure Advisor | Microsoft Docs
description: Le conseiller Azure permet d’optimiser le coût de vos déploiements Azure.
services: advisor
documentationcenter: NA
author: kasparks
ms.service: advisor
ms.topic: article
ms.date: 01/29/2019
ms.author: kasparks
ms.openlocfilehash: c76c7bdb398184cc297831c9395063e7bf0f6bdc
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/31/2019
ms.locfileid: "55492536"
---
# <a name="reduce-service-costs-using-azure-advisor"></a>Réduire les coûts de service grâce à Azure Advisor

Le conseiller vous aide à optimiser et à réduire votre dépense Azure globale en identifiant les ressources inactives et sous-utilisées. Vous pouvez obtenir des recommandations en matière de coûts dans l’onglet **Coût** du tableau de bord Advisor.

## <a name="optimize-virtual-machine-spend-by-resizing-or-shutting-down-underutilized-instances"></a>Optimiser le coût de la machine virtuelle en redimensionnant ou en arrêtant les instances sous-utilisées 

Alors que certains scénarios d’application peuvent par définition entraîner une faible utilisation, vous pouvez souvent faire des économies grâce à la gestion de la taille de vos machines virtuelles et de leur nombre. Advisor surveille l’utilisation de votre machine virtuelle pendant 14 jours et identifie les machines virtuelles faiblement utilisées. Les machines virtuelles dont l’utilisation du processeur est inférieure ou égale à 5 % et celle du réseau est inférieure ou égale à 7 Mo pendant quatre jours ou plus sont considérées comme des machines virtuelles faiblement utilisées.

Advisor vous présente une estimation du coût de l’exécution de votre machine virtuelle, pour que vous puissiez choisir de l’arrêter ou de la redimensionner.

Si vous souhaitez être plus « agressif » dans l’identification des machines virtuelles sous-utilisées, vous pouvez ajuster la règle d’utilisation moyenne du processeur par abonnement.

## <a name="reduce-costs-by-eliminating-unprovisioned-expressroute-circuits"></a>Réduire les coûts en éliminant les circuits ExpressRoute non approvisionnés

Advisor identifie les circuits ExpressRoute qui présentent l’état de fournisseur *Non approvisionné* depuis plus d’un mois et recommande la suppression de ces circuits si vous ne prévoyez pas de les approvisionner avec votre fournisseur de connectivité.

## <a name="reduce-costs-by-deleting-or-reconfiguring-idle-virtual-network-gateways"></a>Réduire les coûts en supprimant ou en reconfigurant les passerelles de réseau virtuel inactives

Advisor identifie les passerelles de réseau virtuel qui sont restées inactives pendant plus de 90 jours. Dans la mesure où ces passerelles sont facturées par heure, vous devez envisager de reconfigurer ou de les supprimer si vous ne souhaitez plus les utiliser. 

## <a name="buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs"></a>Acheter des instances de machine virtuelle réservées pour économiser sur les coûts du paiement à l’utilisation

Advisor examinera votre utilisation des machines virtuelles sur les 30 derniers jours pour déterminer si vous pouvez faire des économies en achetant une réservation Azure. Advisor vous montrera les régions et les tailles où vous pouvez potentiellement réaliser le plus d’économies et vous présentera une estimation des économies générées grâce à l’achat de réservations. 

Avec les réservations Azure, vous pouvez acheter au préalable les coûts de base de vos machines virtuelles. Des remises seront automatiquement appliquées aux machines virtuelles nouvelles ou existantes ayant la même taille et la même région que vos réservations. [En savoir plus sur Azure Reserved VM Instances.](https://azure.microsoft.com/pricing/reserved-vm-instances/)

## <a name="how-to-access-cost-recommendations-in-azure-advisor"></a>Accès aux recommandations de coût dans Azure Advisor

1. Connectez-vous au [portail Azure](https://portal.azure.com), puis ouvrez [Advisor](https://aka.ms/azureadvisordashboard).

2.  Dans le tableau de bord Advisor, cliquez sur l’onglet **Coût**.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les recommandations d’Advisor, consultez les ressources suivantes :
* [Présentation du conseiller](advisor-overview.md)
* [Prise en main](advisor-get-started.md)
* [Recommandations du conseiller en matière de performances](advisor-cost-recommendations.md)
* [Recommandations du conseiller en matière de haute disponibilité](advisor-cost-recommendations.md)
* [Recommandations du conseiller en matière de sécurité](advisor-cost-recommendations.md)
