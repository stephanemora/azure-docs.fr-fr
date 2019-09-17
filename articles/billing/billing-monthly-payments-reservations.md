---
title: Acheter des réservations Azure avec paiements initiaux ou mensuels
description: Découvrez comment acheter des réservations Azure avec paiements initiaux ou mensuels.
services: billing
author: bandersmsft
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: banders
ms.openlocfilehash: d211334ad2aa760cd63b98c6827fb2512811a1d3
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/11/2019
ms.locfileid: "70806922"
---
# <a name="purchase-reservations-with-monthly-payments"></a>Acheter des réservations avec paiements mensuels

Jusqu’à présent, les réservations Azure nécessitaient un paiement initial. Vous pouvez désormais payer les réservations en effectuant des paiements mensuels. Contrairement à un achat initial dans lequel vous payez le montant total, l’option de paiement mensuel répartit uniformément le coût total de la réservation sur les différents mois de la période. Une même réservation avec paiement initial et avec paiements mensuels a le même coût total : vous ne payez pas de frais supplémentaires si vous optez pour le paiement mensuel.

Le montant de votre paiement mensuel peut varier en fonction du taux de change du mois en cours pour votre devise locale.

Les paiements mensuels sont disponibles pour ce qui suit :

- Machines virtuelles
- Base de données SQL
- SQL Data Warehouse
- Cosmos DB
- Frais de timbre App Service

Achetez des réservations dans le [portail Azure](https://portal.azure.com/?Microsoft_Azure_Reservations_EnableMultiCart=true&amp;paymentPlan=true#blade/Microsoft_Azure_Reservations/CreateBlade).

![Exemple montrant l’achat d’une réservation](./media/billing-monthly-payments-reservations/purchase-reservation.png)

Quand vous procédez à l’achat d’une réservation, vous pouvez afficher l’échéancier de paiement. Cliquez sur **Voir l’échéancier complet de paiement**.

![Exemple d’échéancier de paiement d’une réservation](./media/billing-monthly-payments-reservations/prepurchase-schedule.png)

Pour afficher l’échéancier de paiement après l’achat, sélectionnez une réservation, cliquez sur l’**ID d’ordre de réservation**, puis cliquez sur l’onglet **Paiements**.

## <a name="view-payments-made"></a>Afficher les paiements effectués

Vous pouvez afficher les paiements effectués à l’aide des API, des données d’utilisation et de l’analyse des coûts. Pour les réservations avec paiement mensuel, la fréquence a pour valeur **recurring** (périodique) dans les données d’utilisation et l’API de facturation des réservations. Pour les réservations avec paiement initial, la valeur est **onetime** (paiement en une seule fois).

L’analyse des coûts affiche les achats mensuels dans la vue par défaut. Appliquez le filtre **purchase** (achat) pour **Charge type** (Type de frais) et **recurring** (périodique) pour **Frequency** (Fréquence) afin de voir tous les achats. Pour afficher uniquement les réservations, appliquez un filtre pour **Reservation**.

![Exemple montrant les coûts d’achat de réservation dans l’analyse des coûts](./media/billing-monthly-payments-reservations/cost-analysis.png)

## <a name="switch-to-monthly-payments-at-renewal"></a>Passer aux paiements mensuels au moment du renouvellement

Quand vous renouvelez une réservation, vous pouvez opter pour la facturation mensuelle.

## <a name="exchange-and-refunds"></a>Échange et remboursements

Comme les autres réservations, vous pouvez demander le remboursement ou l’échange des réservations achetées avec facturation mensuelle. Vous pouvez envoyer une demande de support afin de démarrer un échange ou un remboursement pour une réservation achetée avec facturation mensuelle.

Quand vous échangez une réservation qui est payée mensuellement, le coût total de la durée de vie du nouvel achat doit être supérieur aux paiements restants qui sont annulés pour la réservation retournée. Il n’y a pas d’autres limites ou frais pour les échanges. Vous pouvez échanger une réservation acquittée avec paiement initial pour acheter une nouvelle réservation avec facturation mensuelle. Toutefois, la valeur de la durée de vie de la nouvelle réservation doit être supérieure à la valeur au prorata de la réservation retournée.

Si vous annulez une réservation qui est payée mensuellement, Microsoft peut appliquer des frais d’annulation aux futurs paiements validés qui sont annulés. Les paiements validés restants entrent dans la limite de remboursement de 50 000 USD.

Pour plus d’informations sur les échanges et les remboursements, consultez [Échanges et remboursements en libre-service de réservations Azure](billing-azure-reservations-self-service-exchange-and-refund.md).

## <a name="faq"></a>Forum Aux Questions

Q. Azure propose-t-il des « réservations initiales partielles » ?<br>
R. Non. Les coûts des réservations avec paiement initial et paiement mensuel étant identiques, Microsoft ne prend pas en charge les paiements initiaux partiels.

Q. Les paiements mensuels sont-ils disponibles pour le programme Fournisseur de solutions Microsoft Cloud (CSP) ?<br>
R. Oui, les partenaires peuvent acheter des réservations pour leurs clients CSP dans le portail Azure. La possibilité d’acheter des réservations avec facturation mensuelle n’est pas disponible dans l’espace Partenaire.

Q. Je suis client Azure Government ; puis-je acheter une réservation avec paiement mensuel ?<br>
R. Pas actuellement.

Q. Quand puis-je procéder moi-même à un échange ou remboursement dans le portail Azure, au lieu de créer un ticket de support ?<br>
R. Pas actuellement. Les demandes d’échange et de remboursement de réservations avec paiements mensuels sont gérées par le support Azure.

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur les réservations, consultez [Qu’est-ce qu’une réservation Azure ?](billing-save-compute-costs-reservations.md)
- Pour en savoir plus sur les tâches que vous devez effectuer avant d’acheter une réservation, consultez [Déterminer la taille de machine virtuelle adaptée avant l’achat](../virtual-machines/windows/prepay-reserved-vm-instances.md#determine-the-right-vm-size-before-you-buy).
