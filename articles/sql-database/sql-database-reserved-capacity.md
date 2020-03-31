---
title: Réduire les coûts
description: Découvrez comment acheter une capacité réservée Azure SQL Database pour réduire vos coûts de calcul.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: sstein, carlrab
ms.date: 08/29/2019
ms.openlocfilehash: 0713e1ed98cc87b95cad1f84672148cd062e0b58
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75979987"
---
# <a name="save-costs-for-sql-database-compute-resources-with-azure-sql-database-reserved-capacity"></a>Réduire les coûts des ressources de calcul SQL Database avec une capacité réservée Azure SQL Database

Faites des économies avec Azure SQL Database en procédant à une réservation des ressources de calcul au lieu d’appliquer le tarif du paiement à l’utilisation. Avec une capacité réservée Azure SQL Database, vous prenez un engagement pour utiliser SQL Database sur une période d’un an ou de trois ans, afin de bénéficier de remises importantes sur les coûts de calcul. Pour acheter une capacité réservée SQL Database, vous devez spécifier la région Azure, le type de déploiement, le niveau de performance et le terme.


Vous n'êtes pas tenu d'affecter la réservation à des instances SQL Database spécifiques (bases de données uniques, pools élastiques ou instances gérées). Les instances SQL Database correspondantes, qui sont déjà en cours d’exécution ou récemment déployées, bénéficient automatiquement de cet avantage. En achetant une réservation, vous vous engagez à payer les coûts de calcul pendant une durée d’un an ou de trois ans. Dès que vous achetez une réservation, les frais de calcul SQL Database qui correspondent aux attributs de la réservation ne sont plus facturés au tarif du paiement à l’utilisation. Une réservation ne couvre pas les frais de logiciels, ni ceux de réseau ou de stockage qui sont associés à l’instance SQL Database. À l’issue de la période de réservation, la remise sur facturation cesse de s’appliquer et les bases de données SQL sont facturées au tarif du paiement à l’utilisation. Les réservations ne se renouvellent pas automatiquement. Pour plus d’informations sur les tarifs, consultez l’[offre de capacité réservée SQL Database](https://azure.microsoft.com/pricing/details/sql-database/managed/).

Vous pouvez acheter une capacité réservée Azure SQL Database sur le [portail Azure](https://portal.azure.com). Payez la réservation [à l’avance ou par paiements mensuels](../cost-management-billing/reservations/monthly-payments-reservations.md). Pour acheter une capacité réservée SQL Database :

- Vous devez disposer du rôle Propriétaire sur au moins un abonnement Entreprise ou individuel avec des tarifs de paiement à l’utilisation.
- Pour les abonnements Entreprise, **Add Reserved Instances** (Ajouter des instances réservées) doit être activé dans le [portal EA](https://ea.azure.com). Si ce paramètre est désactivé, vous devez être administrateur EA de l’abonnement.
- Pour ce qui est du programme des fournisseurs de solutions cloud, seuls les agents d’administration ou les agents commerciaux peuvent acheter une capacité réservée SQL Database.

Plus en savoir plus sur la facturation des achats de réservation pour les clients professionnels et les clients bénéficiant du paiement à l’utilisation, consultez les articles [Comprendre l’utilisation d’une réservation Azure pour votre inscription Entreprise](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md) et [Comprendre l’utilisation d’une réservation Azure pour votre abonnement avec paiement à l’utilisation](../cost-management-billing/reservations/understand-reserved-instance-usage.md).

## <a name="determine-the-right-sql-size-before-purchase"></a>Déterminer la bonne taille de SQL avant l’achat

La taille de la réservation doit s'appuyer sur le nombre total de calculs utilisés par les bases de données uniques, pools élastiques ou instances gérées existants ou à déployer dans une région spécifique et utilisant le même niveau de performance et la même génération de matériel.

Par exemple, supposons que vous exécutez un processeur Gen5, option Usage général ; un pool élastique de 16 vCore et deux processeurs Gen5, option Critique pour l’entreprise ; des bases de données uniques 4 vCore. Admettons également que vous envisagez de déployer au cours du mois suivant un processeur Gen5 supplémentaire, option Usage général ; un pool élastique de 16 vCore et un processeur Gen5, option Critique pour l’entreprise ; un pool élastique de 32 vCore. Enfin, imaginons que vous savez que ces ressources vous seront nécessaires pendant au moins 1 an. Dans ce cas, vous devez acheter une réservation de 32 (2x16) vCores pour une durée d’un an, avec l’option Usage général pour pool élastique/bases de données uniques - Gen5, et une réservation de 40 (2x4 + 32) vCores pour une durée d’un an, avec l’option Critique pour l’entreprise pour pool élastique/bases de données uniques - Gen5.

## <a name="buy-sql-database-reserved-capacity"></a>Acheter une capacité réservée SQL Database

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Sélectionnez **Tous les services** > **Réservations**.
3. Sélectionnez **Ajouter**, puis dans le volet Acheter des réservations, choisissez **SQL Database** pour acheter une nouvelle réservation de bases de données SQL.
4. Renseignez les champs obligatoires. Les bases de données uniques, pools élastiques ou instances gérées existants ou nouveaux, correspondant aux attributs que vous sélectionnez, peuvent prétendre à la remise de capacité réservée. Le nombre réel de vos instances SQL Database qui obtiennent la remise dépend de l’étendue et de la quantité sélectionnées.
    ![Capture d’écran avant de soumettre l’achat de la capacité réservée SQL Database](./media/sql-database-reserved-vcores/sql-reserved-vcores-purchase.png)

Le tableau suivant décrit les champs requis.

| Champ      | Description|
|------------|--------------|
|Abonnement|Abonnement utilisé pour payer la réservation de capacité réservée SQL Database. Les coûts initiaux de la réservation de capacité réservée SQL Database sont facturés selon le mode de paiement défini sur l’abonnement. Le type d’abonnement doit être Accord Entreprise (numéros de l’offre : MS-AZR-0017P ou MS-AZR-0148p) ou un accord individuel avec paiement à l’utilisation (numéros de l’offre : MS-AZR-0003P ou MS-AZR-0023P). Pour un abonnement Entreprise, les frais sont déduits du solde d’engagement monétaire de l’inscription ou facturés comme un dépassement. Pour un abonnement individuel avec paiement à l’utilisation, les frais sont facturés sur le mode de paiement par carte de crédit ou par facture sur l’abonnement.|
|Étendue       |L’étendue de la réservation vCore peut couvrir un seul abonnement ou plusieurs abonnements (étendue partagée). Si vous sélectionnez : <br/><br/>**Partagé** : la remise de réservation vCore est appliquée aux instances SQL Database en cours d’exécution dans tous les abonnements de votre contexte de facturation. Pour les clients Entreprise, l'étendue partagée correspond à l'inscription et inclut tous les abonnements compris dans l'inscription. Pour les clients Paiement à l’utilisation, l’étendue partagée correspond à tous les abonnements Paiement à l’utilisation créés par l’administrateur de compte.<br/><br/>**Abonnement unique** : la remise de réservation vCore est appliquée aux instances SQL Database incluses dans cet abonnement. <br/><br/>**Groupe de ressources unique** : la remise de réservation est appliquée aux instances SQL Database incluses dans l’abonnement sélectionné et dans le groupe de ressources sélectionné dans cet abonnement.|
|Région      |Région Azure couverte par la réservation de capacité réservée SQL Database.|
|Type de déploiement|Type de ressource SQL pour laquelle vous voulez acheter la réservation.|
|Niveau de performances|Niveau de service pour les instances SQL Database.
|Terme        |Une année ou trois ans.|
|Quantité    |Quantité de ressources de calcul achetée au sein de la réservation de capacité réservée SQL Database. La quantité correspond au nombre de vCores de la région Azure et du niveau de performance sélectionnés qui est réservé et qui bénéficie de la remise de facturation. Par exemple, si vous exécutez ou que vous envisagez d’exécuter des instances SQL Database avec la capacité de calcul totale de 16 vCores Gen5 dans la région USA Est, vous devez spécifier une quantité de 16 pour optimiser l’avantage pour toutes les instances. |

1. Étudiez de plus près le coût de la réservation de capacité réservée SQL Database à la section **Coûts**.
1. Sélectionnez **Achat**.
1. Sélectionnez **Afficher cette réservation** pour connaître l’état de votre achat.

## <a name="cancel-exchange-or-refund-reservations"></a>Annuler, échanger ou rembourser des réservations

Vous pouvez annuler, échanger ou rembourser des réservations avec certaines limitations. Pour plus d’informations, consultez [Échanges et remboursements en libre-service pour les réservations Azure](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

## <a name="vcore-size-flexibility"></a>Flexibilité de la taille vCore

La flexibilité de la taille vCore vous permet de vous mettre à l’échelle au sein d’un niveau de performances et d’une région, sans perdre le bénéfice de la capacité réservée. Grâce à la capacité réservée SQL Database, vous avez également la possibilité de déplacer temporairement vos bases de données à chaud entre des pools et des bases de données uniques dans le cadre de vos opérations normales (au sein du même niveau de performances et de la même région), sans perdre le bénéfice de la capacité réservée. En conservant une mémoire tampon non appliquée dans votre réservation, vous pouvez gérer efficacement les pics de performances sans dépasser votre budget.

## <a name="limitation"></a>Limitation

Vous ne pouvez pas réserver de bases de données SQL basées sur DTU (De base, Standard ou Premium).

## <a name="need-help-contact-us"></a>Vous avez besoin d’aide ? Nous contacter

Si vous avez des questions ou besoin d’aide, [créez une demande de support](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Étapes suivantes

La remise de réservation vCore est appliquée automatiquement au nombre d’instances SQL Database qui correspondent à l’étendue et aux attributs de la réservation de capacité réservée SQL Database. Vous pouvez mettre à jour l’étendue de la réservation de capacité réservée SQL Database par le biais du [portail Azure](https://portal.azure.com), de PowerShell, de CLI ou de l’API.

Pour savoir comment gérer la réservation de capacité de réserve SQL Database, consultez [Gérer la capacité de réserve SQL Database](../cost-management-billing/reservations/manage-reserved-vm-instance.md).

Pour plus d’informations sur les réservations Azure, consultez les articles suivants :

- [Qu’est-ce qu’une réservation Azure ?](../cost-management-billing/reservations/save-compute-costs-reservations.md)
- [Gérer les réservations Azure](../cost-management-billing/reservations/manage-reserved-vm-instance.md)
- [Comprendre la remise sur réservation Azure](../cost-management-billing/reservations/understand-reservation-charges.md)
- [Comprendre l’utilisation d’une réservation pour votre abonnement avec paiement à l’utilisation](../cost-management-billing/reservations/understand-reserved-instance-usage.md)
- [Comprendre l’utilisation d’une réservation pour votre Accord de Mise en Œuvre Entreprise](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
- [Réservations Azure dans le cadre du programme Fournisseur de solutions Cloud de l’Espace partenaires](https://docs.microsoft.com/partner-center/azure-reservations)
