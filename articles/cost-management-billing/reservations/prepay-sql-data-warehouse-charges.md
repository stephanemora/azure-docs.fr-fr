---
title: Réduire les coûts de SQL Data Warehouse avec une capacité réservée Azure
description: Découvrez comment réduire les coûts de SQL Data Warehouse avec une capacité réservée pour économiser de l’argent.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: banders
ms.openlocfilehash: c428472d5564393e9f9fcdc3103bb1506dc50936
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2020
ms.locfileid: "77200365"
---
# <a name="save-costs-for-sql-data-warehouse-charges-with-reserved-capacity"></a>Réduire les coûts de SQL Data Warehouse avec une capacité réservée

Vous pouvez économiser de l’argent avec Azure SQL Data Warehouse en vous engageant sur une réservation de votre utilisation de cDWU pendant une durée d’un an ou de trois ans. Pour acheter une capacité réservée de SQL Data Warehouse, vous devez choisir la région Azure et la période. Ensuite, ajoutez la référence SKU SQL Data Warehouse à votre panier, puis choisissez la quantité d’unités cDWU que vous voulez acheter.

Lorsque vous achetez une réservation, l’utilisation de SQL Data Warehouse correspondant aux attributs de la réservation n’est plus facturée au tarif de paiement à l’utilisation.

Une réservation ne couvre pas le frais de stockage ou de réseau associés à l’utilisation de SQL Data Warehouse.

Lorsque la capacité réservée arrive à expiration, les instances SQL Data Warehouse continuent de s’exécuter, mais sont facturées au tarif de paiement à l’utilisation. Les réservations ne sont pas renouvelées automatiquement.

Pour plus d’informations sur la tarification, voir l’[offre de capacité réservée SQL Data Warehouse](https://azure.microsoft.com/pricing/details/sql-data-warehouse/gen2/).

Vous pouvez acheter une capacité réservée Azure SQL Data Warehouse sur le [portail Azure](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade). Payez la réservation [à l’avance ou par paiements mensuels](monthly-payments-reservations.md). Pour acheter une capacité réservée :

- Vous devez avoir le rôle propriétaire pour au moins un abonnement Entreprise ou avec paiement à l’utilisation.
- Pour les abonnements Entreprise, l’option **Ajouter des instances réservées** doit être activée sur le [portail EA](https://ea.azure.com/). Si le paramètre est désactivé, vous devez être un administrateur EA.
- Pour le programme Fournisseur de solutions cloud (CSP), seuls des agents d’administration ou de agents commerciaux peuvent acheter une capacité réservée SQL Data Warehouse.

Pour en savoir plus sur la facturation des achats de réservation pour les clients professionnels et les clients payant à l’utilisation, voir les articles [Comprendre l’utilisation d’une réservation Azure pour votre inscription Entreprise](understand-reserved-instance-usage-ea.md) et [Comprendre l’utilisation d’une réservation Azure pour votre abonnement avec paiement à l’utilisation](understand-reserved-instance-usage.md).

## <a name="choose-the-right-size-before-purchase"></a>Choisir la bonne taille avant l’achat

La taille de la réservation SQL Data Warehouse doit être basée sur le total des unités de calcul Data Warehouse (cDWU) que vous consommez. Les achats sont effectués par incréments de 100 cDWU.

Par exemple, supposons que votre consommation totale de SQL Data Warehouse est DW3000c. Vous souhaitez acheter une capacité réservée à hauteur de cette consommation. Vous devez donc acheter 30 unités de capacité réservée cDWU.

## <a name="buy-sql-data-warehouse-reserved-capacity"></a>Acheter de la capacité réservée SQL Data Warehouse

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Sélectionnez **Tous les services** > **Réservations**.
3. Sélectionnez un abonnement. Utilisez la liste Abonnement pour choisir l’abonnement à utiliser pour payer la capacité réservée. Les coûts de la capacité réservée sont facturés conformément au mode de paiement défini pour l’abonnement. Le type d’abonnement doit être Accord Entreprise (numéros de l’offre : MS-AZR-0017P ou MS-AZR-0148P) ou Paiement à l’utilisation (numéros de l’offre : MS-AZR-0003P ou MS-AZR-0023P).
   - Pour un abonnement Entreprise, les frais sont déduits du solde d’engagement monétaire de l’inscription ou facturés comme un dépassement.
   - Pour un abonnement Paiement à l’utilisation, les frais sont facturés sur le mode de paiement par carte de crédit ou par facture défini sur l’abonnement.
4. Sélectionnez une étendue. Utilisez la liste Étendue pour choisir une étendue d’abonnement.
   - **Étendue de groupe de ressources unique** : applique la remise de réservation aux ressources correspondantes incluses dans le groupe de ressources sélectionné uniquement.
   - **Étendue d’abonnement unique** : applique la remise de réservation aux ressources correspondantes incluses dans l’abonnement sélectionné.
   - **Étendue partagée** : applique la remise de réservation aux ressources correspondantes dans les abonnements éligibles inclus dans le contexte de facturation. Pour les clients Contrat Entreprise, le contexte de facturation correspond à l’inscription. Pour les abonnements individuels utilisant les tarifs du paiement à l’utilisation, l’étendue de facturation correspond à tous les abonnements éligibles créés par l’administrateur de compte.
   - Pour les clients Entreprise, le contexte de facturation est défini par l’inscription EA.
   - Pour les clients Paiement à l’utilisation, l’étendue partagée correspond à tous les abonnements Paiement à l’utilisation créés par l’administrateur de compte.
5. Sélectionnez une région éligible pour la capacité réservée.
6. Choisissez une quantité. Entrez le nombre d’unités de calcul Data Warehouse que vous voulez acheter.    
   Par exemple, une quantité de 30 correspond à une réservation de capacité de 3 000 cDWU par heure.
7. Examinez le coût de réservation de capacité réservée SQL Data Warehouse dans la section **Coûts**.
8. Sélectionnez **Achat**.
9. Sélectionnez **Afficher cette réservation** pour voir le statut de votre achat.

## <a name="cancel-exchange-or-refund-reservations"></a>Annuler, échanger ou rembourser des réservations

Vous pouvez annuler, échanger ou rembourser des réservations avec certaines limitations. Pour plus d’informations, consultez [Échanges et remboursements en libre-service pour les réservations Azure](exchange-and-refund-azure-reservations.md).

La remise sur réservation est appliquée automatiquement au nombre d’instances SQL Data Warehouse correspondant à l’étendue et à la région de la capacité réservée SQL Data Warehouse. Vous pouvez mettre à jour l’étendue de la capacité réservée SQL Data Warehouse par le biais du [portail Azure](https://portal.azure.com/), de PowerShell, de CLI ou de l’API.

## <a name="need-help-contact-us"></a>Vous avez besoin d’aide ? Nous contacter

Si vous avez des questions ou besoin d’aide, [créez une demande de support](https://portal.azure.com/).

## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur la manière dont les remises sur réservation s’appliquent à Azure SQL Data Warehouse, voir [Description de l’application de remises de réservation à Azure SQL Data Warehouse](prepay-sql-data-warehouse-charges.md).

- Pour plus d’informations sur les réservations Azure, consultez les articles suivants :
  - [Qu’est-ce qu’une réservation Azure ?](save-compute-costs-reservations.md)
  - [Gérer les réservations Azure](manage-reserved-vm-instance.md)
  - [Comprendre la remise sur réservation Azure](understand-reservation-charges.md)
  - [Comprendre l’utilisation d’une réservation pour votre abonnement avec paiement à l’utilisation](understand-reserved-instance-usage.md)
  - [Comprendre l’utilisation d’une réservation pour votre Accord de Mise en Œuvre Entreprise](understand-reserved-instance-usage-ea.md)
