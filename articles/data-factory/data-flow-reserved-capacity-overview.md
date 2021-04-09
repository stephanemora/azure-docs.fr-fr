---
title: Économiser sur les coûts de calcul grâce à une capacité de réserve
description: Découvrez comment acheter une capacité réservée de flux de données Azure Data Factory pour réduire vos coûts de calcul.
ms.topic: conceptual
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.date: 02/05/2021
ms.openlocfilehash: 26a4692603d8e8a80a52ea77bdd56617131cea5d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104593898"
---
# <a name="save-costs-for-resources-with-reserved-capacity---azure-data-factory-data-flows"></a>Réduire les coûts des ressources grâce à une capacité de réserve – Flux de données Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Faites des économies avec les coûts de flux de données Azure Data Factory en souscrivant une réservation des ressources de calcul au lieu d’appliquer le tarif du paiement à l’utilisation. Avec une capacité réservée, vous vous engagez à utiliser le flux de données ADF sur une période d’un an ou de trois ans, afin de bénéficier de remises importantes sur les coûts de calcul. Pour acheter une capacité réservée, vous devez spécifier la région Azure, le type de calcul, le nombre de cœurs et la durée.

Vous n’avez pas besoin d’attribuer la réservation à une fabrique ou un runtime d’intégration spécifique. Les fabriques existantes ou les fabriques nouvellement déployées bénéficient automatiquement de l’avantage. En achetant une réservation, vous vous engagez à payer les coûts de calcul du flux de données pendant une durée d’un an ou de trois ans. Dès que vous achetez une réservation, les frais de calcul qui correspondent aux attributs de la réservation ne sont plus facturés au tarif du paiement à l’utilisation. 

Vous pouvez acheter une [capacité réservée](https://portal.azure.com) en choisissant des réservations avec des [paiements à l’avance ou mensuels](../cost-management-billing/reservations/prepare-buy-reservation.md). Pour acheter une capacité réservée :

- Vous devez disposer du rôle Propriétaire sur au moins un abonnement Entreprise ou individuel avec des tarifs de paiement à l’utilisation.
- Pour les abonnements Entreprise, **Add Reserved Instances** (Ajouter des instances réservées) doit être activé dans le [portal EA](https://ea.azure.com). Si ce paramètre est désactivé, vous devez être administrateur EA de l’abonnement. Capacité réservée.

Pour en savoir plus sur la facturation des achats de réservation pour les clients professionnels et les clients payant à l’utilisation, consultez les articles [Comprendre l’utilisation d’une réservation Azure pour votre inscription Entreprise](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md) et [Comprendre l’utilisation d’une réservation Azure pour votre abonnement avec paiement à l’utilisation](../cost-management-billing/reservations/understand-reserved-instance-usage.md).

> [!NOTE]
> L’achat d’une capacité de réserve ne préalloue ni ne réserve aucune ressource d’infrastructure spécifique (machines virtuelles ou clusters) à votre usage.

## <a name="determine-proper-azure-ir-sizes-needed-before-purchase"></a>Déterminer les tailles Azure IR appropriées nécessaires avant l’achat

La taille de la réservation doit s’appuyer sur le nombre total de calculs utilisés par les flux de données existants ou à déployer prochainement et qui utilisent le même niveau de calcul.

Supposons, par exemple, que vous exécutiez un pipeline toutes les heures en utilisant 32 cœurs avec mémoire optimisée. Supposons également que vous envisagiez de déployer dans les prochains mois un pipeline supplémentaire qui utilise 64 cœurs pour un usage général. Enfin, imaginons que vous savez que ces ressources vous seront nécessaires pendant au moins 1 an. Dans ce cas, entrez le nombre de cœurs nécessaires pour chaque type de calcul pendant 1 heure. Dans le portail Azure, recherchez Réservations. Choisissez Data Factory > Flux de données, puis entrez 32 pour mémoire optimisée et 64 pour usage général.

## <a name="buy-reserved-capacity"></a>Acheter une capacité réservée

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Sélectionnez **Tous les services** > **Réservations**.
3. Sélectionnez **Ajouter**, puis, dans le volet **Acheter des réservations**, choisissez **Flux de données ADF** pour acheter une nouvelle réservation de flux de données ADF.
4. Remplissez les champs obligatoires. Les attributs que vous sélectionnez peuvent prétendre à la remise de capacité réservée. Le nombre de flux de données qui peuvent bénéficier de la remise dépend de l’étendue et de la quantité sélectionnées.
5. Étudiez de plus près le coût de la réservation de capacité dans la section **Coûts**.
6. Sélectionnez **Achat**.
7. Sélectionnez **Afficher cette réservation** pour connaître l’état de votre achat.

## <a name="cancel-exchange-or-refund-reservations"></a>Annuler, échanger ou rembourser des réservations

Vous pouvez annuler, échanger ou rembourser des réservations avec certaines limitations. Pour plus d’informations, consultez [Échanges et remboursements en libre-service pour les réservations Azure](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

## <a name="need-help-contact-us"></a>Vous avez besoin d’aide ? Nous contacter

Si vous avez des questions ou besoin d’aide, [créez une demande de support](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les réservations Azure, consultez les articles suivants :

- [Comprendre la remise sur réservation Azure](data-flow-understand-reservation-charges.md)
