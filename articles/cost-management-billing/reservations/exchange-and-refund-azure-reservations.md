---
title: Échanges et remboursements en libre-service de réservations Azure
description: Découvrez comment vous pouvez échanger des réservations Azure ou en obtenir le remboursement.
author: yashesvi
manager: yashesvi
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/21/2020
ms.author: banders
ms.openlocfilehash: ea545919436201524a1c77b27e9b187f3b1c3b64
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/22/2020
ms.locfileid: "76314043"
---
# <a name="self-service-exchanges-and-refunds-for-azure-reservations"></a>Échanges et remboursements en libre-service de réservations Azure

Les réservations Azure offrent la flexibilité nécessaire pour répondre à vos besoins en constante évolution. Vous pouvez échanger une réservation pour une autre réservation du même type. Vous pouvez également rembourser une réservation, jusqu’à 50 000 USD par an, si vous n’en avez plus besoin. La limite maximale du remboursement s’applique à toutes les réservations dans le cadre de votre contrat avec Microsoft.

L’échange en libre service et la fonctionnalité d’annulation n’est pas disponible pour les clients Contrat Entreprise de l’administration américaine. Les autres types d’abonnements de l’administration américaine, notamment Paiement à l’utilisation et Fournisseur de solutions cloud, sont pris en charge.

Vous devez disposer d’un accès propriétaire à l’ordre de réservation pour échanger une réservation existante ou en obtenir le remboursement.

## <a name="exchange-an-existing-reserved-instance"></a>Échanger une instance réservée existante

Vous pouvez échanger votre réservation en trois étapes rapides dans le [portail Azure](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade).

1. Sélectionnez les réservations dont vous souhaitez obtenir le remboursement et cliquez sur **Échanger**.  
    ![Image illustrant des réservations à retourner](./media/exchange-and-refund-azure-reservations/exchange-refund-return.png)
2. Sélectionnez le produit de machine virtuelle que vous voulez acheter et tapez une quantité. Assurez-vous que le nouveau total d’achat est supérieur au total du retour. [Déterminez la bonne taille avant d’effectuer l’achat](../../virtual-machines/windows/prepay-reserved-vm-instances.md#determine-the-right-vm-size-before-you-buy).  
    ![Image illustrant le produit de machine virtuelle à acheter avec un échange](./media/exchange-and-refund-azure-reservations/exchange-refund-select-purchase.png)
3. Vérifiez et finalisez la transaction.  
    ![Image illustrant le produit de machine virtuelle à acheter avec un échange, avec finalisation du retour](./media/exchange-and-refund-azure-reservations/exchange-refund-confirm-exchange.png)

Pour obtenir le remboursement d’une réservation, accédez à **Détails de la réservation** et cliquez sur **Remboursement**.

## <a name="how-transactions-are-processed"></a>Mode de traitement des transactions

Tout d’abord, Microsoft annule la réservation existante et rembourse le montant de cette réservation au prorata. S’il existe un échange, le nouvel achat est traité. Microsoft traite les remboursements à l’aide d’une des méthodes suivantes, selon vos type de compte et mode de paiement :

### <a name="enterprise-agreement-customers"></a>Clients titulaires d’un contrat entreprise

Pour les échanges et les remboursements, l’argent est ajouté à l’engagement financier éventuellement utilisé pour l’achat d’origine. Toutes les factures de dépassement depuis les achats d’origine sont rouvertes et réévaluées pour que l’engagement monétaire soit utilisé. Si la période de l’engagement financier au cours de laquelle la réservation a été achetée n’est plus active, le crédit est ajouté à la période actuelle de l’engagement financier rattaché à votre contrat entreprise. Le crédit est valable 90 jours à compter de la date du remboursement. Tout crédit inutilisé expire au bout de 90 jours.

Si l’achat d’origine a été effectué en tant que dépassement, Microsoft émet une note de crédit.

### <a name="pay-as-you-go-invoice-payments-and-csp-program"></a>Paiements des factures à l’utilisation et programme Fournisseur de solutions cloud

La facture d’achat de réservation d’origine est annulée, puis une facture est créée pour le remboursement. Pour les échanges, la nouvelle facture montre le remboursement et le nouvel achat. Le montant du remboursement est ajusté par rapport à l’achat. Dans le cas d’un simple remboursement d’une réservation, Microsoft conserve le montant au prorata, en vue de son ajustement par rapport à un achat de réservation futur.

### <a name="pay-as-you-go-credit-card-customers"></a>Clients qui paient à l’utilisation au moyen d’une carte de crédit

La facture d’origine est annulée, puis une facture est créée. L’argent est crédité sur la carte de crédit qui a été utilisée pour l’achat d’origine. Si vous avez changé de carte, [contactez le support](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="cancel-exchange-and-refund-policies"></a>Stratégies d’annulation, d’échange et de remboursement

Azure dispose des stratégies suivantes pour les annulations, les échanges et les remboursements.

**Stratégies d’échange**

- Vous pouvez retourner plusieurs réservations existantes pour acheter une nouvelle réservation du même type. Vous ne pouvez pas échanger une réservation d’un type contre une réservation d’un autre type. Par exemple, vous ne pouvez pas retourner une réservation de machine virtuelle pour acheter une réservation SQL.
- Seuls les propriétaires de réservation peuvent traiter un échange. [Découvrez comment ajouter ou changer les utilisateurs qui peuvent gérer une réservation](manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation).
- Un échange est traité comme un remboursement et un rachat : différentes transactions sont créées pour l’annulation et le nouvel achat. Le montant de la réservation au prorata est remboursé pour les réservations que vous échangez. Vous êtes facturé entièrement pour le nouvel achat. Le montant de la réservation au prorata est la valeur résiduelle au prorata quotidienne de la réservation qui fait l’objet d’un retour.
- Vous pouvez échanger une réservation ou en obtenir le remboursement même si le contrat entreprise utilisé pour son achat a expiré et a été renouvelé sous la forme d’un nouveau contrat.
- Vous pouvez modifier n’importe quelle propriété d’une réservation telle que la taille, la région, la quantité et la période avec un échange.
- Le nouveau total d’achat doit être égal ou supérieur au montant retourné.
- La nouvelle réservation achetée dans le cadre d’un échange a une nouvelle période qui commence à la date de cet échange.
- Il n’y a aucune pénalité ou limite annuelle pour les échanges.

**Stratégies de remboursement**
- Si vous annulez une réservation, des frais de résiliation anticipée de 12 % sont susceptibles d’être appliqués.
- Le remboursement que vous recevez pour une annulation est le solde au prorata moins les 12 % des frais de résiliation anticipée. Pour annuler, accédez à la réservation dans le portail Azure, puis sélectionnez **Remboursement**.
- Le montant total du remboursement ne peut pas dépasser 50 000 USD sur 12 mois consécutifs.
- Les remboursements sont calculés en fonction du prix le plus bas de votre prix d’achat ou du prix actuel de la réservation.
- Seuls les propriétaires de réservation peuvent traiter un remboursement. [Découvrez comment ajouter ou changer les utilisateurs qui peuvent gérer une réservation](manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation).
- Microsoft se réserve le droit de facturer une pénalité de 12 % pour les retours. La pénalité n’est pas facturée pour le moment, mais sera facturée à l’avenir.

## <a name="exchange-non-premium-storage-for-premium-storage"></a>Échanger un stockage non Premium contre un stockage Premium

Vous pouvez échanger une réservation achetée pour une taille de machine virtuelle qui ne prend pas en charge le stockage Premium au profit d’une taille de machine virtuelle correspondante qui assure cette prise en charge. Par exemple, une machine virtuelle _F1_ contre une machine virtuelle _F1s_. Pour effectuer l’échange, accédez à Détails de la réservation, puis cliquez sur **Échanger**. L’échange ne réinitialise pas la période de l’instance réservée et ne crée pas de transaction.

## <a name="need-help-contact-us"></a>Vous avez besoin d’aide ? Contactez-nous.

Si vous avez des questions ou besoin d’aide, [créez une demande de support](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Étapes suivantes

- Pour découvrir comment gérer une réservation, consultez [Gérer les réservations Azure](manage-reserved-vm-instance.md).
- Pour plus d’informations sur les réservations Azure, consultez les articles suivants :
    - [Qu’est-ce qu’une réservation Azure ?](save-compute-costs-reservations.md)
    - [Gérer les réservations dans Azure](manage-reserved-vm-instance.md)
    - [Comprendre comment la remise de réservation est appliquée](../manage/understand-vm-reservation-charges.md)
    - [Comprendre l’utilisation d’une réservation pour votre abonnement avec paiement à l’utilisation](understand-reserved-instance-usage.md)
    - [Comprendre l’utilisation d’une réservation pour votre Accord de Mise en Œuvre Entreprise](understand-reserved-instance-usage-ea.md)
    - [Coûts des logiciels Windows non inclus dans les réservations](reserved-instance-windows-software-costs.md)
    - [Réservations Azure dans le cadre du programme Fournisseur de solutions Cloud de l’Espace partenaires](/partner-center/azure-reservations)
