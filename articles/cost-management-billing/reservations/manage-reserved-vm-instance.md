---
title: Gérer les réservations Azure
description: Découvrez comment gérer les réservations Azure.
ms.service: cost-management-billing
author: bandersmsft
ms.reviewer: yashesvi
ms.topic: conceptual
ms.date: 02/18/2020
ms.author: banders
ms.openlocfilehash: 2dadcac3c776feed5ee521284f6b244a050e83e9
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2020
ms.locfileid: "79237936"
---
# <a name="manage-reservations-for-azure-resources"></a>Gérer les réservations pour les ressources Azure

Après avoir acheté une réservation Azure, il se peut que vous deviez l’appliquer à un autre abonnement, modifier la personne autorisés à la gérer, ou en modifier l’étendue. Vous pouvez également diviser une réservation en deux pour appliquer certaines des instances que vous avez achetées à un autre abonnement.

Si vous avez acheté des instances de machine virtuelle réservées d’Azure, vous pouvez modifier le paramètre d’optimisation de la réservation. La remise sur la réservation peut s’appliquer à des machines virtuelles de la même série, ou vous pouvez réserver de la capacité du centre de données pour une taille de machine virtuelle spécifique. Et, vous devez essayer d’optimiser les réservations afin qu’elles soient entièrement utilisées.


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="reservation-order-and-reservation"></a>Ordre de réservation et réservation

Lorsque vous achetez une réservation, deux objets sont créés : l’**ordre de réservation** et la **réservation**.

Au moment de l’achat, un ordre de réservation contient une réservation. Les actions telles que le fractionnement, la fusion, le remboursement partiel ou l’échange créent de nouvelles réservations dans l’**ordre de réservation**.

Pour afficher un ordre de réservation, accédez à la section **Réservations**, sélectionnez la réservation, puis cliquez sur l’**ID d’ordre de réservation**.

![Exemple de détails d’ordre de réservation indiquant l’ID d’ordre de réservation ](./media/manage-reserved-vm-instance/reservation-order-details.png)

Une réservation hérite des autorisations de son ordre de réservation.

## <a name="change-the-reservation-scope"></a>Modifier l’étendue de réservation

 Votre remise de réservation s’applique aux machines virtuelles, aux bases de données SQL, à Azure Cosmos DB ou à d’autres ressources qui correspondent à votre réservation et qui s’exécutent dans les limites de l’étendue de la réservation. Le contexte de facturation dépend de l’abonnement utilisé pour acheter la réservation.

Pour mettre à jour l’étendue d’une réservation :

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Sélectionnez **Tous les services** > **Réservations**.
3. Sélectionnez la réservation.
4. Sélectionnez **Paramètres** > **Configuration**.
5. Modifiez l’étendue.

Si vous passez de l’étendue partagée à une étendue unique, vous ne pouvez sélectionner que les abonnements dont vous êtes le propriétaire. Seuls peuvent être sélectionnés les abonnements présents dans le même contexte de facturation que celui de la réservation.

L’étendue s’applique uniquement aux abonnements individuels MS-AZR-0003P ou MS-AZR-0023P de l’offre avec paiement à l’utilisation, MS-AZR-0017P ou MS-AZR-0148P de l’offre Entreprise ou CSP.

## <a name="add-or-change-users-who-can-manage-a-reservation"></a>Ajouter ou modifier les utilisateurs qui peuvent gérer une réservation

Vous pouvez déléguer la gestion des réservations en ajoutant des utilisateurs aux rôles de l’ordre de réservation ou de la réservation. Par défaut, la personne qui a acheté l’ordre de réservation et l’administrateur de compte disposent tous les deux du rôle de propriétaire sur la réservation et l’ordre de réservation.

Vous pouvez gérer l’accès aux ordres de réservations et réservations, indépendamment des abonnements qui bénéficient de la remise de réservation. Lorsque vous accordez des autorisations de gestion pour une réservation ou un ordre de réservation, cela ne veut pas dire que vous octroyez des autorisations pour la gestion de l’abonnement. De même, si vous accordez des autorisations de gestion d’un abonnement dans les limites de l’étendue de la réservation, vous n’octroyez aucun droit permettant de gérer la réservation ou l’ordre de réservation.

Pour effectuer un échange ou un remboursement, l’utilisateur doit avoir accès à l’ordre de réservation. Lorsque vous accordez des autorisations à une personne, il convient d’accorder des autorisations pour l’ordre de réservation, pas la réservation.


Pour déléguer la gestion de l’accès à une réservation :

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Sélectionnez **Tous les services** > **Réservation** pour afficher la liste des réservations auxquelles vous avez accès.
3. Sélectionnez la réservation pour laquelle vous souhaitez déléguer l’accès à d’autres utilisateurs.
4. Sélectionnez **Contrôle d’accès (IAM)** .
5. Sélectionnez **Ajouter une attribution de rôle** > **Rôle** > **Propriétaire**. Ou si vous souhaitez accorder un accès limité, sélectionnez un autre rôle.
6. Tapez l’adresse e-mail de l’utilisateur à ajouter comme propriétaire.
7. Sélectionnez l’utilisateur, puis **Enregistrer**.

## <a name="split-a-single-reservation-into-two-reservations"></a>Diviser une réservation unique en deux réservations

 Après avoir acheté plusieurs instances de ressource dans une réservation, vous pouvez affecter de instances de cette réservation à d’autres abonnements. Par défaut, toutes les instances ont une étendue : abonnement unique ou partagé. Par exemple, vous avez acheté 10 instances de réservation et spécifié l’étendue de l’abonnement A. Vous pouvez à présent modifier l’étendue de 7 réservations à l’abonnement A, et les 3 instances restantes à l’abonnement B. La division d’une réservation vous permet de répartir les instances pour une gestion de l’étendue précise. Vous pouvez simplifier la répartition sur les abonnements en choisissant une étendue partagée. Toutefois, pour des raisons de gestion de coûts et de budgétisation, vous pouvez affecter des quantités à des abonnements spécifiques.

 Vous pouvez diviser une réservation en deux réservations par l’intermédiaire de PowerShell, de CLI ou de l’API.

### <a name="split-a-reservation-by-using-powershell"></a>Diviser une réservation à l’aide de PowerShell

1. Récupérez l’ID de l’ordre de réservation en exécutant la commande suivante :

    ```powershell
    # Get the reservation orders you have access to
    Get-AzReservationOrder
    ```

2. Récupérez les détails d’une réservation :

    ```powershell
    Get-AzReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId b8be062a-fb0a-46c1-808a-5a844714965a
    ```

3. Divisez la réservation en deux et répartissez les instances :

    ```powershell
    # Split the reservation. The sum of the reservations, the quantity, must equal the total number of instances in the reservation that you're splitting.
    Split-AzReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId b8be062a-fb0a-46c1-808a-5a844714965a -Quantity 3,2
    ```
4. Vous pouvez mettre à jour l’étendue en exécutant la commande suivante :

    ```powershell
    Update-AzReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId 5257501b-d3e8-449d-a1ab-4879b1863aca -AppliedScopeType Single -AppliedScope /subscriptions/15bb3be0-76d5-491c-8078-61fe3468d414
    ```

## <a name="cancel-exchange-or-refund-reservations"></a>Annuler, échanger ou rembourser des réservations

Vous pouvez annuler, échanger ou rembourser des réservations avec certaines limitations. Pour plus d’informations, consultez [Échanges et remboursements en libre-service pour les réservations Azure](exchange-and-refund-azure-reservations.md).

## <a name="change-optimize-setting-for-reserved-vm-instances"></a>Modifier le paramètre d’optimisation pour des instances de machine virtuelle réservées

 Lorsque vous achetez une instance de machine virtuelle réservée, vous choisissez sa flexibilité de taille ou sa priorité de capacité. La flexibilité de taille d’instance applique la remise sur réservation aux autres machines virtuelles du même [groupe de tailles de machine virtuelle](https://aka.ms/RIVMGroups). La priorité de capacité priorise la capacité de centre de données pour vos déploiements. Cette option offre une assurance supplémentaire quand à votre capacité à lancer les instances de machine virtuelle quand vous en avez besoin.

Par défaut, quand l’étendue de la réservation est partagée, la flexibilité de taille de l’instance est activée. La capacité de centre de données n’est pas priorisée pour les déploiements de machine virtuelle.

Pour les réservations dont l’étendue est unique, vous pouvez optimiser la réservation pour la priorité de capacité au lieu de la flexibilité de taille d’instance de machine virtuelle.

Pour mettre à jour le paramètre d’optimisation de la réservation :

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Sélectionnez **Tous les services** > **Réservations**.
3. Sélectionnez la réservation.
4. Sélectionnez **Paramètres** > **Configuration**.  
  ![Exemple montrant l’élément Configuration](./media/manage-reserved-vm-instance/add-product03.png)
5. Modifiez le paramètre **Optimiser pour**.  
  ![Exemple montrant le paramètre Optimiser pour](./media/manage-reserved-vm-instance/instance-size-flexibility-option.png)

## <a name="optimize-reservation-use"></a>Optimiser l’utilisation de la réservation

Les économies de réservation Azure résultent uniquement d’une utilisation soutenue des ressources. Quand vous effectuez un achat de réservation, vous payez pour ce qui correspond essentiellement à 100 % de l’utilisation des ressources possibles sur une période d’un an ou de trois ans. Essayez d’optimiser votre réservation pour en tirer le meilleur parti et réaliser des économies. Les sections suivantes expliquent comment surveiller une réservation et optimiser son utilisation.

### <a name="view-reservation-use-in-the-azure-portal"></a>Afficher l’utilisation de réservation dans le Portail Azure

L’un des moyens d’afficher l’utilisation des réservations est le Portail Azure.

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Sélectionnez **Réservations de**  > [**tous les services**](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade) et notez l’**utilisation (%)** pour une réservation.  
  ![Image représentant la liste des réservations](./media/manage-reserved-vm-instance/reservation-list.png)
3. Sélectionnez une réservation.
4. Passez en revue la tendance d’utilisation des réservations dans le temps.  
  ![Image représentant l’utilisation de réservation ](./media/manage-reserved-vm-instance/reservation-utilization-trend.png)

### <a name="view-reservation-use-with-api"></a>Afficher l’utilisation de réservation avec l’API

Si vous êtes client Contrat Entreprise (EA), vous pouvez afficher par programmation la manière dont les réservations sont utilisées dans votre organisation. Vous bénéficiez d’une réservation inutilisée par le biais des données d’utilisation. Lorsque vous examinez les frais de réservation, gardez à l’esprit que les données sont réparties entre le coût réel et les coûts amortis. Le coût réel fournit les données à rapprocher avec votre facture mensuelle. Il comporte également des détails sur l’application de réservation et le coût d’achat des réservations. Le coût amorti est semblable au coût réel, sauf que le prix effectif de l’utilisation de la réservation est calculé au prorata. Les heures de réservation inutilisées sont affichées dans les données de coût amorties. Pour plus d’informations sur les données d’utilisation pour les clients EA, consultez [Obtenir les données d’utilisation et de coûts de la réservation pour les Contrats Entreprise](understand-reserved-instance-usage-ea.md).

Pour les autres abonnements, utilisez l’API [de résumés de réservations – Liste par commande de réservation et réservation](/rest/api/consumption/reservationssummaries/listbyreservationorderandreservation).

### <a name="optimize-your-reservation"></a>Optimiser votre réservation

Si vous constatez que les réservations de votre organisation sont sous-utilisées :

- Assurez-vous que les machines virtuelles créées par votre organisation correspondent à la taille de machine virtuelle qui se trouve sur la réservation.
- Assurez-vous que la flexibilité de taille d’instance est activée. Pour plus d’informations, consultez [Gérer les réservations - Modifier le paramètre d’optimisation pour des instances de machine virtuelle réservées](#change-optimize-setting-for-reserved-vm-instances).
- Modifiez l’étendue de réservation en _partage_ afin qu’elle s’applique plus largement. Pour plus d’informations, consultez [Modifier l’étendue d’une réservation](#change-the-reservation-scope).
- Envisagez d’échanger la quantité inutilisée. Pour plus d’informations, consultez [Annulations et échanges](#cancel-exchange-or-refund-reservations).


## <a name="need-help-contact-us"></a>Vous avez besoin d’aide ? Contactez-nous.

Si vous avez des questions ou besoin d’aide, [créez une demande de support](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les réservations Azure, consultez les articles suivants :

- [Qu’est-ce qu’une réservation Azure ?](save-compute-costs-reservations.md)

Acheter un plan de service :
- [Prépayer des machines virtuelles avec des instances de machines virtuelles réservées Azure](../../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Prépayer des ressources de calcul SQL Database avec une capacité réservée Azure SQL Database](../../sql-database/sql-database-reserved-capacity.md)
- [Prépayer des ressources Azure Cosmos DB avec une capacité réservée Azure Cosmos DB](../../cosmos-db/cosmos-db-reserved-capacity.md)

Acheter un abonnement logiciel :
- [Prépayer des abonnements logiciels Red Hat dans Réservations Azure](../../virtual-machines/linux/prepay-rhel-software-charges.md)
- [Prépayer des logiciels SUSE avec des réservations Azure](../../virtual-machines/linux/prepay-suse-software-charges.md)

Comprendre la remise et l’utilisation :
- [Comprendre comment la remise sur réservation de machine virtuelle est appliquée](../manage/understand-vm-reservation-charges.md)
- [Comprendre comment la remise sur abonnement logiciel Red Hat Enterprise Linux est appliquée](understand-rhel-reservation-charges.md)
- [Comprendre comment la remise sur offre logicielle SUSE Linux Enterprise est appliquée](understand-suse-reservation-charges.md)
- [Comprendre comment les autres remises sur réservation sont appliquées](understand-reservation-charges.md)
- [Comprendre l’utilisation d’une réservation pour votre abonnement avec paiement à l’utilisation](understand-reserved-instance-usage.md)
- [Comprendre l’utilisation d’une réservation pour votre Accord de Mise en Œuvre Entreprise](understand-reserved-instance-usage-ea.md)
- [Coûts des logiciels Windows non inclus dans les réservations](reserved-instance-windows-software-costs.md)
