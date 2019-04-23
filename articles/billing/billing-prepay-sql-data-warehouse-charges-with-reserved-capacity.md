---
title: Prépayer les frais de SQL Data Warehouse avec capacité réservée Azure | Microsoft Docs
description: Découvrez comment vous pouvez prépayer les frais de SQL Data Warehouse avec capacité réservée pour économiser de l’argent.
services: billing
author: yashesvi
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 04/13/2019
ms.author: banders
ms.openlocfilehash: 3d56ee99f8056139a234477b6eed93d9fcbe2bbb
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2019
ms.locfileid: "60013814"
---
# <a name="prepay-for-sql-data-warehouse-charges-with-reserved-capacity"></a>Prépayer les frais de SQL Data Warehouse avec capacité réservée

Vous pouvez économiser de l’argent avec Azure SQL Data Warehouse par posté sous forme de votre utilisation de cDWU pour une durée de 1 an ou trois ans. Pour acheter une capacité réservée de SQL Data Warehouse, vous devez choisir Azure région et la période. Ensuite, ajoutez la référence de l’entrepôt de données SQL à votre panier et choisir la quantité d’unités cDWU que vous voulez acheter.

Lorsque vous achetez une réservation, SQL Data Warehouse, l’utilisation qui correspond aux attributs de la réservation n’est plus facturée le paiement-sous-vous allez taux.

Une réservation ne couvre pas le stockage ou réseau frais associés à l’utilisation de SQL Data Warehouse.

Lorsque la capacité réservée arrive à expiration, les instances de SQL Data Warehouse continuent à s’exécuter, mais sont facturées au paiement-sous-vous accédez taux. Les réservations ne la renouvelez pas automatiquement.

Pour plus d’informations sur la tarification, consultez la [SQL Data Warehouse réservé offre de capacité](https://azure.microsoft.com/pricing/details/sql-data-warehouse/gen2/).

Vous pouvez acheter des capacités d’Azure SQL Data Warehouse est réservé le [Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade). Pour acheter une capacité réservée :

- Vous devez disposer du rôle de propriétaire au moins une entreprise ou d’abonnement de paiement à l’utilisation.
- Pour les abonnements de l’entreprise, le **ajouter des Instances réservées** option doit être activée dans le [portail EA](https://ea.azure.com/). Si le paramètre est désactivé, vous devez être administrateur EA.
- Pour le programme fournisseur de solutions Cloud (CSP), uniquement les agents d’administration ou les agents de vente peuvent acheter des capacités de SQL Data Warehouse est réservé.

Pour plus d’informations sur comment les clients d’entreprise et paiement à l’utilisation sont facturés pour les achats de réservation, consultez [comprendre l’utilisation de la réservation Azure pour l’inscription de votre entreprise](billing-understand-reserved-instance-usage-ea.md) et [comprendre Azure utilisation de la réservation pour votre abonnement assorti d’un](billing-understand-reserved-instance-usage.md).

## <a name="choose-the-right-size-before-purchase"></a>Choisissez la bonne taille avant l’achat

SQL Data Warehouse, taille de réservation doit être basée sur le total compute data warehouse Unit (cDWU) que vous consommez. Les achats sont effectués en incréments de 100 cDWU.

Par exemple, supposons que votre consommation totale de SQL Data Warehouse est DW3000c. Vous souhaitez acheter une capacité réservée pour l’ensemble de celui-ci. Par conséquent, vous devez acheter 30 unités de capacité de cDWU réservé.

## <a name="buy-sql-data-warehouse-reserved-capacity"></a>Acheter SQL Data Warehouse de capacité réservée

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Sélectionnez **Tous les services** > **Réservations**.
3. Sélectionnez un abonnement. Utilisez la liste des abonnements pour choisir l’abonnement qui est utilisée pour payer la capacité réservée. La méthode de paiement de l’abonnement est facturée le coût de démarrage pour la capacité réservée. Le type d’abonnement doit être Accord Entreprise (numéros de l’offre : MS-AZR-0017P ou MS-AZR-0148P) ou Paiement à l’utilisation (numéros de l’offre : MS-AZR-0003P ou MS-AZR-0023P).
  - Pour un abonnement Entreprise, les frais sont déduits du solde d’engagement monétaire de l’inscription ou facturés comme un dépassement.
  - Pour un abonnement Paiement à l’utilisation, les frais sont facturés sur le mode de paiement par carte de crédit ou par facture défini sur l’abonnement.
4. Sélectionnez une étendue. Utilisez la liste de l’étendue pour choisir une étendue de l’abonnement.
  - Avec le **unique** option, la remise de réservation est appliquée à SQL Data Warehouse est déployé dans l’abonnement sélectionné.
  - Avec le **partagé** option, la remise de réservation est appliquée aux instances en cours d’exécution dans tous les abonnements au sein de votre contexte de facturation.
    - Pour les entreprises, le contexte de facturation correspond à l’inscription EA.
    - Pour les clients Paiement à l’utilisation, l’étendue partagée correspond à tous les abonnements Paiement à l’utilisation créés par l’administrateur de compte.
5. Sélectionnez une région pour choisir une région Azure qui est couverte par la capacité réservée.
6. Choisissez une quantité. Entrez la quantité de 100 unités de l’entrepôt de données (cDWU) que vous voulez acheter.    
  Par exemple, une quantité de 30 vous donnera de réservation de capacité de 3 000 cDWU toutes les heures.
7. Révision de l’entrepôt de données SQL réservé coût de réservation de capacité dans le **coûts** section.
8. Sélectionnez **Achat**.
9. Sélectionnez **afficher cette réservation** pour afficher l’état de votre achat.

## <a name="cancellations-and-exchanges"></a>Annulations et échanges

Si vous avez besoin pour annuler votre SQL Data Warehouse capacité réservée, il peut y avoir un frais de résiliation anticipée de 12 %. Les remboursements sont basés sur le tarif le plus bas de votre prix d’achat ou le prix actuel de la réservation. Remboursements sont limités à 50,000.00 $ par an. La restitution que vous recevez est le solde restant calculé au prorata moins les frais de résiliation anticipée de 12 %. Pour demander une annulation, accédez à la réservation dans le Portail Azure et sélectionnez **Remboursement** pour créer une demande de support.

Si vous devez modifier votre capacité de SQL Data Warehouse est réservé à une autre région ou à terme, vous pouvez l’échanger de réservation d’une autre qui est de valeur égale ou supérieure. La date de début du terme de la nouvelle réservation ne couvre pas la réservation échangée. L’un ou trois ans terme démarre lorsque vous créez la nouvelle réservation. Pour demander un échange, ouvrez la réservation dans le portail Azure, puis sélectionnez **Exchange** pour créer une demande de support.

Pour plus d’informations sur comment exchange ou un remboursement des réservations, consultez [échanges de réservation et les remboursements](billing-azure-reservations-self-service-exchange-and-refund.md).

La remise de réservation est automatiquement appliquée au nombre d’instances de SQL Data Warehouse qui correspondent à l’étendue de capacité réservée de SQL Data Warehouse et la région. Vous pouvez mettre à jour l’étendue de la capacité réservée de SQL Data Warehouse avec le [Azure portal](https://portal.azure.com/), PowerShell, CLI ou via l’API.

## <a name="need-help-contact-us"></a>Vous avez besoin d’aide ? Nous contacter

Si vous avez des questions ou besoin d’aide, [créez une demande de support](https://portal.azure.com/).

## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur comment les remises de réservation s’appliquent à Azure SQL Data Warehouse, consultez [comment les remises de réservation s’appliquent à Azure SQL Data Warehouse](billing-prepay-sql-data-warehouse-charges-with-reserved-capacity.md).

- Pour plus d’informations sur les réservations Azure, consultez les articles suivants :
  - [Qu’est-ce qu’une réservation Azure ?](billing-save-compute-costs-reservations.md)
  - [Gérer les réservations Azure](billing-manage-reserved-vm-instance.md)
  - [Comprendre la remise sur réservation Azure](billing-understand-reservation-charges.md)
  - [Comprendre l’utilisation d’une réservation pour votre abonnement avec paiement à l’utilisation](billing-understand-reserved-instance-usage.md)
  - [Comprendre l’utilisation d’une réservation pour votre Accord de Mise en Œuvre Entreprise](billing-understand-reserved-instance-usage-ea.md)
