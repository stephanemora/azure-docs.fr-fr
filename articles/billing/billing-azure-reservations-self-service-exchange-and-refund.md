---
title: Échanges de libre-service et les remboursements pour les réservations d’Azure | Microsoft Docs
description: Découvrez comment vous pouvez échanger ou de la restitution des réservations d’Azure.
services: billing
documentationcenter: ''
author: yashesvi
manager: yashesvi
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/5/2019
ms.author: banders
ms.openlocfilehash: aa1a218fbf0bc7eacac65b50e4ee1f86791e2b3b
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/08/2019
ms.locfileid: "59281979"
---
# <a name="self-service-exchanges-and-refunds-for-azure-reservations"></a>Échanges de libre-service et les remboursements pour les réservations d’Azure

Réservations d’Azure offrent la flexibilité pour répondre à vos besoins en constante évolution. Vous pouvez échanger une réservation pour une autre réservation du même type. Vous pouvez également rembourser une réservation, jusqu'à 50 000 USD par an, si vous n’avez plus besoin.

Libre service exchange et annuler la fonctionnalité n’est pas disponible pour les clients US Government accord d’entreprise. Autres types d’abonnement US Government, y compris le paiement à l’utilisation et CSP sont pris en charge.

## <a name="exchange-an-existing-reserved-instance"></a>Échanger une instance réservée existante

Vous pouvez échanger votre réservation avec trois étapes rapides dans le [Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade).

1. Sélectionnez les réservations que vous souhaitez de remboursement et cliquez sur **Exchange**.  
    ![Image d’un exemple montrant des réservations à retourner](./media/billing-azure-reservations-self-service-exchange-and-refund/exchange-refund-return.png)
2. Sélectionnez le produit de la machine virtuelle que vous souhaitez acheter et tapez une quantité. Assurez-vous que le nouveau total d’achat est supérieur au total de retour. [Déterminer la taille appropriée avant d’acheter](../virtual-machines/windows/prepay-reserved-vm-instances.md#determine-the-right-vm-size-before-you-buy).  
    ![Image montrant le produit de la machine virtuelle avec un échange de l’exemple](./media/billing-azure-reservations-self-service-exchange-and-refund/exchange-refund-select-purchase.png)
3. Passez en revue et terminer la transaction.  
    ![Image montrant le produit de la machine virtuelle avec un échange, la valeur de retour à la fin de l’exemple](./media/billing-azure-reservations-self-service-exchange-and-refund/exchange-refund-confirm-exchange.png)

Pour une réservation de remboursement, accédez à **détails de la réservation** et cliquez sur **remboursement**.

## <a name="how-return-and-exchange-transactions-are-processed"></a>Comment retrouver et traitement des transactions d’exchange

Tout d’abord, Microsoft annule la réservation existante et remboursements la quantité au prorata pour cette réservation. S’il existe un échange, l’achat de nouveau est traitée. Microsoft traite les remboursements à l’aide d’une des méthodes suivantes, selon votre type de compte et de paiement :

### <a name="enterprise-agreement-customers"></a>Clients contrat entreprise

Money est ajouté à l’engagement monétaire pour les échanges et remboursements si l’achat d’origine a été effectuée à l’aide d’une. Toutes les factures dépassements dans la mesure où les achats d’origine sont rouverts et rerated pour vous assurer que l’engagement monétaire est utilisé. Si le terme engagement financier à l’aide de que la réservation a été achetée n’est plus active, le crédit est ajouté à votre terme d’engagement financier contrat entreprise actuel.

Si l’achat d’origine a été effectuée en tant que dépassement, Microsoft publie un avoir.

### <a name="pay-as-you-go-invoice-payment-customers-and-cloud-solution-provider-program"></a>Clients de paiement de facture de paiement à l’utilisation et le programme de fournisseur de solutions de Cloud

La facture d’achat de réservation d’origine est annulée, et ensuite une facture est créée pour la restitution. Pour les échanges, la nouvelle facture montre le remboursement et l’achat de nouveau. Le montant du remboursement est ajusté par rapport à l’achat. Si vous remboursé uniquement une réservation, la quantité au prorata reste avec Microsoft, puis elle est ajustée par rapport à un achat de réservation futures.

### <a name="pay-as-you-go-credit-card-customers"></a>Clients de carte de crédit de paiement à l’utilisation

La facture d’origine est annulée et une facture est créée. L’argent est crédité sur la carte de crédit a été utilisée pour l’achat d’origine. Si vous avez modifié votre carte, [contactez le support technique](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="exchange-policies"></a>Stratégies Exchange

- Vous pouvez retourner plusieurs réservations existantes pour acheter une nouvelle réservation du même type. Vous ne pouvez pas échanger des réservations d’un type pour un autre. Par exemple, vous ne peut pas retourner une réservation de machine virtuelle pour acheter une réservation de SQL.
- Seuls les propriétaires de réservation peuvent traiter un échange. [Découvrez comment ajouter ou modifier des utilisateurs qui peuvent gérer une réservation](https://docs.microsoft.com/azure/billing/billing-manage-reserved-vm-instance#add-or-change-users-who-can-manage-a-reservation).
- Un échange est traité comme un remboursement et un rachat : transactions différentes sont créées pour l’annulation et l’achat de nouveau. La quantité de réservation au prorata est remboursée pour les réservations que vous comparez. Vous êtes facturé entièrement pour l’achat de nouveau. La quantité de réservation au prorata est la valeur quotidienne du résiduelle au prorata de la réservation qui est retournée.
- Vous pouvez échanger ou les réservations de remboursement même si le contrat d’entreprise utilisée pour acheter la réservation a expiré et a été renouvelé comme un nouveau contrat.
- Vous pouvez modifier n’importe quelle propriété de réservation telles que la taille, région, la quantité et le terme avec un échange.
- Le nouveau total achat doit égal ou être supérieure à la quantité retournée.
- La nouvelle réservation achetée en tant que partie intégrante d’exchange a un nouveau terme à partir du moment d’exchange.
- Aucune pénalité ou est limites annuelles pour les échanges.

## <a name="refund-policies"></a>Stratégies de remboursement

- Le montant de votre remboursement total ne peut pas dépasser 50 000 $ dans une fenêtre cumulative de 12 mois.
- Seuls les propriétaires de réservation peuvent traiter un remboursement. [Découvrez comment ajouter ou modifier des utilisateurs qui peuvent gérer une réservation](billing-manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation).
- Microsoft réserve le droit de facturer une pénalité de 12 % pour n’importe quel retourne, bien que la pénalité n’est pas actuellement facturée.

## <a name="exchange-a-non-premium-storage-vm-reservation-for-a-premium-storage-reservation"></a>Échanger une réservation de machine virtuelle pour une réservation de stockage premium de stockage non premium

Vous pouvez échanger une réservation achetée pour une taille de machine virtuelle qui ne prennent pas en charge le stockage premium à une taille de machine virtuelle correspondante qui effectue. Par exemple, un _F1_ pour un _F1s_. Pour effectuer l’échange, accédez aux détails de la réservation, puis cliquez sur **Exchange**. L’échange ne réinitialiser la durée de l’instance réservée ou créer une nouvelle transaction.

## <a name="need-help-contact-us"></a>Vous avez besoin d’aide ? Contactez-nous.

Si vous avez des questions ou besoin d’aide, [créez une demande de support](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Étapes suivantes

- Pour découvrir comment gérer une réservation, consultez [Gérer les réservations Azure](billing-manage-reserved-vm-instance.md).
- Pour plus d’informations sur les réservations Azure, consultez les articles suivants :
    - [Qu’est-ce qu’une réservation Azure ?](billing-save-compute-costs-reservations.md)
    - [Gérer les réservations dans Azure](billing-manage-reserved-vm-instance.md)
    - [Comprendre comment la remise de réservation est appliquée](billing-understand-vm-reservation-charges.md)
    - [Comprendre l’utilisation de la réservation pour votre abonnement de paiement à l’utilisation](billing-understand-reserved-instance-usage.md)
    - [Comprendre l’utilisation de la réservation pour l’inscription de votre entreprise](billing-understand-reserved-instance-usage-ea.md)
    - [Coûts des logiciels Windows non inclus avec les réservations](billing-reserved-instance-windows-software-costs.md)
    - [Réservations d’Azure dans le programme partenaire centre Cloud Solution Provider (CSP)](/partner-center/azure-reservations)
