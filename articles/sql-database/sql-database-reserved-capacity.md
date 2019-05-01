---
title: Prépayer les vCores Azure SQL Database pour réaliser des économies | Microsoft Docs
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
manager: craigg
ms.date: 04/26/2019
ms.openlocfilehash: 74a52df4eab8a3e471e541889cde74846af00fb8
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64574188"
---
# <a name="prepay-for-sql-database-compute-resources-with-azure-sql-database-reserved-capacity"></a>Prépayer des ressources de calcul SQL Database avec une capacité réservée Azure SQL Database

Faites des économies avec Azure SQL Database en utilisant le prépaiement de ressources de calcul au lieu d’appliquer le tarif du paiement à l’utilisation. Avec une capacité réservée Azure SQL Database, vous prenez un engagement initial dans SQL Database, sur une période d’un an ou de trois ans, afin de bénéficier de remises importantes sur les coûts de calcul. Pour acheter une capacité réservée SQL Database, vous devez spécifier la région Azure, le type de déploiement, le niveau de performance et le terme.


Vous n'êtes pas tenu d'affecter la réservation à des instances SQL Database spécifiques (bases de données uniques, pools élastiques ou instances gérées). Les instances SQL Database correspondantes, qui sont déjà en cours d’exécution ou récemment déployées, bénéficient automatiquement de cet avantage. En achetant une réservation, vous payez d’avance les coûts de calcul pendant une durée d’un an ou de trois ans. Dès que vous achetez une réservation, les frais de calcul SQL Database qui correspondent aux attributs de la réservation ne sont plus facturés au tarif du paiement à l’utilisation. Une réservation ne couvre pas les frais de logiciels, ni ceux de réseau ou de stockage qui sont associés à l’instance SQL Database. À l’issue de la période de réservation, la remise sur facturation cesse de s’appliquer et les bases de données SQL sont facturées au tarif du paiement à l’utilisation. Les réservations ne se renouvellent pas automatiquement. Pour plus d’informations sur les tarifs, consultez l’[offre de capacité réservée SQL Database](https://azure.microsoft.com/pricing/details/sql-database/managed/).

Vous pouvez acheter une capacité réservée Azure SQL Database sur le [portail Azure](https://portal.azure.com). Pour acheter une capacité réservée SQL Database :

- Vous devez disposer du rôle de propriétaire sur au moins un abonnement Entreprise ou un abonnement Paiement à l’utilisation.
- Pour les abonnements Entreprise, **Add Reserved Instances** (Ajouter des instances réservées) doit être activé dans le [portal EA](https://ea.azure.com). Si ce paramètre est désactivé, vous devez être administrateur EA de l'abonnement.
- Pour ce qui est du programme des fournisseurs de solutions cloud, seuls les agents d’administration ou les agents commerciaux peuvent acheter une capacité réservée SQL Database.

Plus en savoir plus sur la facturation des achats de réservation pour les clients professionnels et les clients bénéficiant du paiement à l’utilisation, consultez les articles [Comprendre l’utilisation d’une réservation Azure pour votre inscription Entreprise](../billing/billing-understand-reserved-instance-usage-ea.md) et [Comprendre l’utilisation d’une réservation Azure pour votre abonnement avec paiement à l’utilisation](../billing/billing-understand-reserved-instance-usage.md).

## <a name="determine-the-right-sql-size-before-purchase"></a>Déterminer la bonne taille de SQL avant l’achat

La taille de la réservation doit s'appuyer sur le nombre total de calculs utilisés par les bases de données uniques, pools élastiques ou instances gérées existants ou à déployer dans une région spécifique et utilisant le même niveau de performance et la même génération de matériel.

Par exemple, supposons que vous exécutez un processeur Gen5, option Usage général ; un pool élastique de 16 vCore et deux processeurs Gen5, option Critique pour l’entreprise ; des bases de données uniques 4 vCore. Admettons également que vous envisagez de déployer au cours du mois suivant un processeur Gen5 supplémentaire, option Usage général ; un pool élastique de 16 vCore et un processeur Gen5, option Critique pour l’entreprise ; un pool élastique de 32 vCore. Enfin, imaginons que vous savez que ces ressources vous seront nécessaires pendant au moins 1 an. Dans ce cas, vous devez acheter une réservation de 32 (2x16) vCores pour une durée d’un an, avec l’option Usage général pour pool élastique/bases de données uniques - Gen5, et une réservation de 40 (2x4 + 32) vCores pour une durée d’un an, avec l’option Critique pour l’entreprise pour pool élastique/bases de données uniques - Gen5.

## <a name="buy-sql-database-reserved-capacity"></a>Acheter une capacité réservée SQL Database

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Sélectionnez **Tous les services** > **Réservations**.
3. Sélectionnez **Ajouter**, puis dans le volet Sélectionner le Type de produit, choisissez **SQL Database** pour acheter une nouvelle réservation de bases de données SQL.
4. Renseignez les champs obligatoires. Les bases de données uniques, pools élastiques ou instances gérées existants ou nouveaux, correspondant aux attributs que vous sélectionnez, peuvent prétendre à la remise de capacité réservée. Le nombre réel de vos instances SQL Database qui obtiennent la remise dépend de l’étendue et de la quantité sélectionnées.

   ![Capture d’écran avant de soumettre l’achat de la capacité réservée SQL Database](./media/sql-database-reserved-vcores/sql-reserved-vcores-purchase.png)

    | Champ      | Description|
    |:------------|:--------------|
    |Nom        |Nom de cette réservation.|
    |Abonnement|Abonnement utilisé pour payer la réservation de capacité réservée SQL Database. Les coûts initiaux de la réservation de capacité réservée SQL Database sont facturés selon le mode de paiement défini sur l’abonnement. Le type d'abonnement doit être un Contrat Entreprise (numéro de l'offre : MS-AZR-0017P ou MS-AZR-0148P) ou Paiement à l’utilisation (numéros de l’offre : MS-AZR-0003P ou MS-AZR-0023P). Pour un abonnement Entreprise, les frais sont déduits du solde d’engagement monétaire de l’inscription ou facturés comme un dépassement. Pour un abonnement Paiement à l’utilisation, les frais sont facturés sur le mode de paiement par carte de crédit ou par facture défini sur l’abonnement.|
    |Étendue       |L’étendue de la réservation vCore peut couvrir un seul abonnement ou plusieurs abonnements (étendue partagée). Si vous sélectionnez : <br/><br/>**Abonnement unique** : la remise de réservation vCore est appliquée aux instances SQL Database incluses dans cet abonnement. <br/><br/>**Abonnement partagé** : la remise de réservation vCore est appliquée aux instances SQL Database en cours d’exécution dans tous les abonnements de votre contexte de facturation. Pour les clients Entreprise, l'étendue partagée correspond à l'inscription et inclut tous les abonnements compris dans l'inscription. Pour les clients Paiement à l’utilisation, l’étendue partagée correspond à tous les abonnements Paiement à l’utilisation créés par l’administrateur de compte.|
    |Région      |Région Azure couverte par la réservation de capacité réservée SQL Database.|
    |Type de déploiement|Type de ressource SQL pour laquelle vous voulez acheter la réservation.|
    |Niveau de performances|Niveau de service pour les instances SQL Database.
    |Terme        |Une année ou trois ans.|
    |Quantité    |Nombre d’instances achetées au sein de la réservation de capacité réservée SQL Database. La quantité correspond au nombre d’instances SQL Database en cours d’exécution, qui peuvent bénéficier de la remise de facturation. Par exemple, si vous exécutez 10 instances SQL Database dans la région USA Est, vous devez spécifier 10 comme quantité, afin d’optimiser l’avantage pour toutes les machines en cours d’exécution. |
    |||

5. Étudiez de plus près le coût de la réservation de capacité réservée SQL Database à la section **Coûts**.
6. Sélectionnez **Achat**.
7. Sélectionnez **Afficher cette réservation** pour connaître l’état de votre achat.

## <a name="cancellations-and-exchanges"></a>Annulations et échanges

Si vous êtes amené à annuler votre réservation de capacité de réserve SQL Database, des frais de résiliation anticipée de 12 % sont susceptibles d’être appliqués. Les remboursements sont basés sur le tarif le plus bas de votre prix d’achat ou le prix actuel de la réservation. Les remboursements sont limités à 50 000 $ par an. Le remboursement que vous recevez correspond au solde restant au prorata moins les frais de résiliation anticipée de 12 %. Pour demander une annulation, accédez à la réservation dans le Portail Azure et sélectionnez **Remboursement** pour créer une demande de support.

Si vous êtes amené à modifier votre réservation de capacité de réserve SQL Database pour une autre région, un autre type de déploiement ou un autre terme, vous pouvez l’échanger contre une autre réservation de valeur égale ou supérieure. La date de début du terme de la nouvelle réservation ne couvre pas la réservation échangée. Le terme de 1 ou 3 ans commence lorsque vous créez la nouvelle réservation. Pour demander un échange, accédez à la réservation dans le Portail Azure et sélectionnez **Échange** pour créer une demande de support.

Pour plus d’informations sur comment exchange ou un remboursement des réservations, consultez [échanges de réservation et les remboursements](../billing/billing-azure-reservations-self-service-exchange-and-refund.md).

## <a name="vcore-size-flexibility"></a>Flexibilité de la taille vCore

La flexibilité de la taille vCore vous permet de vous mettre à l’échelle au sein d’un niveau de performances et d’une région, sans perdre le bénéfice de la capacité réservée. Grâce à la capacité réservée SQL Database, vous avez également la possibilité de déplacer temporairement vos bases de données à chaud entre des pools et des bases de données uniques dans le cadre de vos opérations normales (au sein du même niveau de performances et de la même région), sans perdre le bénéfice de la capacité réservée. En conservant une mémoire tampon non appliquée dans votre réservation, vous pouvez gérer efficacement les pics de performances sans dépasser votre budget.

## <a name="next-steps"></a>Étapes suivantes

La remise de réservation vCore est appliquée automatiquement au nombre d’instances SQL Database qui correspondent à l’étendue et aux attributs de la réservation de capacité réservée SQL Database. Vous pouvez mettre à jour l’étendue de la réservation de capacité réservée SQL Database par le biais du [portail Azure](https://portal.azure.com), de PowerShell, de CLI ou de l’API.

Pour savoir comment gérer la réservation de capacité de réserve SQL Database, consultez [Gérer la capacité de réserve SQL Database](../billing/billing-manage-reserved-vm-instance.md).

Pour plus d’informations sur les réservations Azure, consultez les articles suivants :

- [Qu’est-ce qu’une réservation Azure ?](../billing/billing-save-compute-costs-reservations.md)
- [Gérer les réservations Azure](../billing/billing-manage-reserved-vm-instance.md)
- [Comprendre la remise sur réservation Azure](../billing/billing-understand-reservation-charges.md)
- [Comprendre l’utilisation d’une réservation pour votre abonnement avec paiement à l’utilisation](../billing/billing-understand-reserved-instance-usage.md)
- [Comprendre l’utilisation d’une réservation pour votre Accord de Mise en Œuvre Entreprise](../billing/billing-understand-reserved-instance-usage-ea.md)
- [Réservations Azure dans le cadre du programme Fournisseur de solutions Cloud de l’Espace partenaires](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-us"></a>Vous avez besoin d’aide ? Nous contacter

Si vous avez des questions ou besoin d’aide, [créez une demande de support](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
