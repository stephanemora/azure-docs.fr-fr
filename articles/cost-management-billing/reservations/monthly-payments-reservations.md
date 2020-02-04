---
title: Acheter des réservations Azure avec paiements initiaux ou mensuels
description: Découvrez comment acheter des réservations Azure avec paiements initiaux ou mensuels.
services: billing
author: bandersmsft
manager: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 11/01/2019
ms.author: banders
ms.openlocfilehash: cf9bb7f3b9661d5957ad569fce7112fe16659761
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/31/2020
ms.locfileid: "75986873"
---
# <a name="purchase-reservations-with-monthly-payments"></a>Acheter des réservations avec paiements mensuels

Jusqu’à présent, les réservations Azure nécessitaient un paiement initial. Vous pouvez désormais payer les réservations en effectuant des paiements mensuels. Contrairement à un achat initial dans lequel vous payez le montant total, l’option de paiement mensuel répartit uniformément le coût total de la réservation sur les différents mois de la période. Une même réservation avec paiement initial et avec paiements mensuels a le même coût total : vous ne payez pas de frais supplémentaires si vous optez pour le paiement mensuel.

Le montant de votre paiement mensuel peut varier en fonction du taux de change du mois en cours pour votre devise locale.

Les paiements mensuels sont disponibles pour ce qui suit :

- Machines virtuelles
- Stockage Azure
- SQL Database
- SQL Data Warehouse
- Cosmos DB
- Frais de timbre App Service

Achetez des réservations dans le [portail Azure](https://portal.azure.com/?Microsoft_Azure_Reservations_EnableMultiCart=true&amp;paymentPlan=true#blade/Microsoft_Azure_Reservations/CreateBlade).

![Exemple montrant l’achat d’une réservation](./media/monthly-payments-reservations/purchase-reservation.png)

Quand vous procédez à l’achat d’une réservation, vous pouvez afficher l’échéancier de paiement. Cliquez sur **Voir l’échéancier complet de paiement**.

![Exemple d’échéancier de paiement d’une réservation](./media/monthly-payments-reservations/prepurchase-schedule.png)

Pour afficher l’échéancier de paiement après l’achat, sélectionnez une réservation, cliquez sur l’**ID d’ordre de réservation**, puis cliquez sur l’onglet **Paiements**.

## <a name="view-payments-made"></a>Afficher les paiements effectués

Vous pouvez afficher les paiements effectués à l’aide des API, des données d’utilisation et de l’analyse des coûts. Pour les réservations avec paiement mensuel, la fréquence a pour valeur **recurring** (périodique) dans les données d’utilisation et l’API de facturation des réservations. Pour les réservations avec paiement initial, la valeur est **onetime** (paiement en une seule fois).

L’analyse des coûts affiche les achats mensuels dans la vue par défaut. Appliquez le filtre **purchase** (achat) pour **Charge type** (Type de frais) et **recurring** (périodique) pour **Frequency** (Fréquence) afin de voir tous les achats. Pour afficher uniquement les réservations, appliquez un filtre pour **Reservation**.

![Exemple montrant les coûts d’achat de réservation dans l’analyse des coûts](./media/monthly-payments-reservations/cost-analysis.png)

## <a name="switch-to-monthly-payments-at-renewal"></a>Passer aux paiements mensuels au moment du renouvellement

Quand vous renouvelez une réservation, vous pouvez opter pour la facturation mensuelle.

## <a name="exchange-and-refunds"></a>Échange et remboursements

Comme les autres réservations, vous pouvez demander le remboursement ou l’échange des réservations achetées avec facturation mensuelle. Vous pouvez envoyer une demande de support afin de démarrer un échange ou un remboursement pour une réservation achetée avec facturation mensuelle.

Quand vous échangez une réservation qui est payée mensuellement, le coût total de la durée de vie du nouvel achat doit être supérieur aux paiements restants qui sont annulés pour la réservation retournée. Il n’y a pas d’autres limites ou frais pour les échanges. Vous pouvez échanger une réservation acquittée avec paiement initial pour acheter une nouvelle réservation avec facturation mensuelle. Toutefois, la valeur de la durée de vie de la nouvelle réservation doit être supérieure à la valeur au prorata de la réservation retournée.

Si vous annulez une réservation qui est payée mensuellement, Microsoft peut appliquer des frais d’annulation aux futurs paiements validés qui sont annulés. Les paiements validés restants entrent dans la limite de remboursement de 50 000 USD.

Pour plus d’informations sur les échanges et les remboursements, consultez [Échanges et remboursements en libre-service de réservations Azure](exchange-and-refund-azure-reservations.md).

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur les réservations, consultez [Qu’est-ce qu’une réservation Azure ?](save-compute-costs-reservations.md)
- Pour en savoir plus sur les tâches que vous devez effectuer avant d’acheter une réservation, consultez [Déterminer la taille de machine virtuelle adaptée avant l’achat](../../virtual-machines/windows/prepay-reserved-vm-instances.md#determine-the-right-vm-size-before-you-buy).
