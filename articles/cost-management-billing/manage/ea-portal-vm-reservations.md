---
title: Instances réservées de machine virtuelle Azure EA
description: Cet article résume la façon dont les réservations Azure pour les instances réservées de machine virtuelle peuvent vous aider à faire des économies dans le cadre de l’inscription de votre entreprise.
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 01/02/2020
ms.topic: conceptual
ms.service: cost-management-billing
manager: boalcsva
ms.openlocfilehash: 26c132d42bfc89c9b6e887c8ca02232848513698
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/15/2020
ms.locfileid: "75985681"
---
# <a name="azure-ea-vm-reserved-instances"></a>Instances réservées de machine virtuelle Azure EA

Cet article résume la façon dont les réservations Azure pour les instances réservées de machine virtuelle peuvent vous aider à faire des économies dans le cadre de l’inscription de votre entreprise. Pour plus d’informations sur les réservations, consultez [Qu’est-ce qu’une réservation Azure ?](../reservations/save-compute-costs-reservations.md).

## <a name="reservation-exchanges-and-refunds"></a>Échanges et remboursements de réservations

Vous pouvez échanger une réservation pour une autre réservation du même type. Il est également possible de rembourser une réservation, jusqu’à 50 000 USD par an, si vous n’en avez plus besoin. Le portail Azure peut être utilisé pour échanger ou rembourser une réservation. Pour plus d’informations, consultez [Échanges et remboursements en libre-service pour les réservations Azure](../reservations/exchange-and-refund-azure-reservations.md).

## <a name="reservation-costs-and-usage"></a>Coûts et utilisation d’une réservation

Les clients titulaires d’un contrat Entreprise peuvent consulter les données d’utilisation et de coûts dans le portail Azure et les API REST. Pour examiner les coûts et l’utilisation d’une réservation, vous pouvez :

- Obtenir les données d’achat de réservation.
- Connaître l’abonnement, le groupe de ressources ou la ressource qui a utilisé une réservation.
- Rétrofacturer l’utilisation des réservations.
- Calculer les économies des réservations.
- Obtenir les données de sous-utilisation des réservations.
- Amortir les coûts des réservations.

Pour plus d’informations sur les coûts de réservation et l’utilisation, voir [Obtenir les données d’utilisation et de coûts de la réservation pour les Contrats Entreprise](../reservations/understand-reserved-instance-usage-ea.md).

Pour obtenir des informations sur les tarifs, consultez [Tarification des machines virtuelles Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) ou [Tarification des machines virtuelles Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/).

## <a name="reserved-instances-api-support"></a>Prise en charge des API des instances réservées

Vous pouvez utiliser des API Azure afin d’obtenir par programmation des informations pour votre organisation sur les réservations des logiciels et services Azure. Pour plus d’informations, consultez [API dédiées à l’automatisation de la réservation Azure](../reservations/reservation-apis.md).

## <a name="azure-reserved-virtual-machine-instances"></a>Instances de machines virtuelles réservées Azure

Les instances réservées peuvent réduire les coûts de vos machines virtuelles jusqu’à 72 % par rapport au prix de paiement à l’utilisation sur toutes les machines virtuelles, ou jusqu’à 82 % d’économies lorsqu’elles sont combinées avec l’offre Azure Hybrid Benefit. Il est possible de mieux hiérarchiser vos charges de travail, votre budget et vos prévisions avec un paiement initial pour des périodes d’un ou trois ans. Vous pouvez également échanger ou annuler les réservations en fonction de l’évolution de votre activité.

### <a name="how-to-buy-reserved-virtual-machine-instances"></a>Comment acheter des instances de machines virtuelles réservées

Pour acheter une instance de machine virtuelle réservée Azure, un administrateur d’inscription Azure Enterprise doit activer l’option d’achat _Reserve Instance_ (Réserver une instance) dans la section _Enrollment Detail_ (Détails de l’inscription) de l’onglet _Inscription_ dans [Azure EA Portal](https://ea.azure.com/).

Une fois que l’inscription EA est activée pour ajouter des instances réservées, n’importe quel propriétaire de compte avec un abonnement actif associé à l’inscription EA peut acheter une instance de machine virtuelle réservée dans le [portail Azure](https://aka.ms/reservations). Pour plus d’informations, consultez [Prépayer des machines virtuelles et réaliser des économies avec les instances de machine virtuelle réservées](https://go.microsoft.com/fwlink/?linkid=861721).

### <a name="how-to-view-reserved-instance-purchase-details"></a>Comment afficher les détails d’achat d’une instance réservée

Vous pouvez afficher les détails de votre achat d’instance réservée via le menu _Réservations_ sur le côté gauche du [portail Azure](https://aka.ms/reservations) ou à partir d’[Azure EA Portal](https://ea.azure.com/). Sélectionnez **Rapports** dans le menu de gauche et faites défiler la liste jusqu’à la section _Charges by Services_ (Frais par services) de l’onglet _Résumé de l’utilisation_. Faites défiler jusqu’au bas de la section, vos achats et utilisation d’instance réservée sont répertoriés à la fin, comme indiqué par la désignation « 1 an » ou « 3 ans » en regard du nom du service, par exemple : Standard_DS1_v2 eastus 1 an ou Standard_D2s_v3 eastus2 3 ans.

### <a name="how-can-i-change-the-subscription-associated-with-reserved-instance-or-transfer-my-reserved-instance-benefits-to-a-subscription-under-the-same-account"></a>Comment modifier l’abonnement associé à l’instance réservée ou transférer mes avantages d’instance réservée vers un abonnement sous le même compte ?

À un moment donné, un seul abonnement peut recevoir des avantages d’instance réservée. Vous pouvez modifier l’abonnement qui reçoit les avantages d’instance réservée en procédant comme suit :

- Connectez-vous au [portail Azure](https://aka.ms/reservations).
- Mettez à jour l’étendue d’abonnement appliquée en associant un autre abonnement sous le même compte.

### <a name="how-to-view-reserved-instance-usage-details"></a>Comment afficher les détails d’utilisation d’instance réservée

Vous pouvez afficher le détail d’utilisation de votre instance réservée dans le [portail Azure](https://aka.ms/reservations) ou dans [Azure EA Portal](https://ea.azure.com/) (pour les clients EA qui ont accès en lecture aux informations de facturation) sous _Rapports_ > _Résumé de l’utilisation_ > _Charges by Services_ (Frais par services). Vos instances réservées peuvent être identifiées par des noms de service contenant « Réservation », par exemple : Instances réservées-Machine virtuelles de base ou Réservation machines virtuelles-Windows Server (1 cœur).

Vos détails d’utilisation et fichier CSV de téléchargement de rapport avancé contiennent des informations supplémentaires sur l’utilisation d’instance réservée. Le champ _Infos supplémentaires_ vous aide à identifier l’utilisation d’instance réservée.

Si vous n’avez pas utilisé l’offre Azure Hybrid Benefit pour acheter des instances de machines virtuelles réservées Azure, les instances réservées génèrent deux compteurs (matériel et logiciel). Si vous avez utilisé l’offre Azure Hybrid Benefit pour acheter une instance réservée, vous ne verrez pas le compteur logiciel dans le détail d’utilisation de votre instance réservée.

### <a name="reserved-instance-billing"></a>Facturation d’instance réservée

Pour les clients professionnels, l’engagement financier Azure pour acheter des instances de machines virtuelles réservées Azure. Si votre inscription dispose d’un solde d’engagement financier suffisant pour couvrir l’achat d’instance réservée, le montant est déduit de votre solde d’engagement financier et vous ne recevez pas de facture pour l’achat.

Dans les scénarios où les clients EA Azure ont utilisé la totalité de leur engagement financier, des instances réservées peuvent toujours être achetées, et ces achats sont facturés sur la facture de dépassement suivante. Le dépassement d’instance réservée, le cas échéant, sera inclus sur la facture de dépassement normale.

### <a name="reserved-instance-expiration"></a>Expiration d’instance réservée

Vous recevrez des notifications par e-mail 30 jours avant l’expiration de la réservation. Une fois que la réservation a expiré, les machines virtuelles déployées continuent à fonctionner et sont facturées au tarif du paiement à l’utilisation. Pour plus d’informations, consultez [Offre des instances de machines virtuelles réservées](https://azure.microsoft.com/pricing/reserved-vm-instances/).

## <a name="next-steps"></a>Étapes suivantes
- Pour plus d’informations sur les réservations Azure, consultez [Qu’est-ce qu’une réservation Azure ?](../reservations/save-compute-costs-reservations.md).
- Pour en savoir plus sur les coûts et l’utilisation des réservations pour les entreprises, consultez [Obtenir les données d’utilisation et de coûts de la réservation pour les Contrats Entreprise](../reservations/understand-reserved-instance-usage-ea.md).
- Pour obtenir des informations sur les tarifs, consultez [Tarification des machines virtuelles Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) ou [Tarification des machines virtuelles Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/).
