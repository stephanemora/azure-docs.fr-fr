---
title: Gérer les réservations Azure | Microsoft Docs
description: Découvrez comment changer l’étendue de l’abonnement et gérer l’accès pour les réservations Azure.
ms.service: billing
documentationcenter: ''
author: yashesvi
manager: yashesvi
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/13/2019
ms.author: banders
ms.openlocfilehash: 9a5b200ffb9441b90875c7764786004ff5f1e8a1
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2019
ms.locfileid: "59994948"
---
# <a name="manage-reservations-for-azure-resources"></a>Gérer les réservations pour les ressources Azure

Une fois que vous achetez une réservation pour Azure, vous devrez peut-être appliquer cette réservation à un autre abonnement, modifier qui peut gérer la réservation, ou modifier l’étendue de la réservation. Vous pouvez également diviser une réservation en deux pour appliquer certaines des instances que vous avez achetées à un autre abonnement.

Si vous avez acheté Azure Reserved Virtual Machine Instances, vous pouvez modifier le paramètre d’optimisation de la réservation. La remise sur la réservation peut s’appliquer à des machines virtuelles de la même série, ou vous pouvez réserver de la capacité du centre de données pour une taille de machine virtuelle spécifique.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="reservation-order-and-reservation"></a>Réservation et l’ordre de réservation

Lorsque vous achetez d’une réservation, deux objets sont créés : **Ordre de réservation** et **réservation**.

Au moment de l’achat, un ordre de réservation a une réservation sous celui-ci. Créent des actions telles que le fractionnement, fusion, remboursement partiel ou exchange nouvelles réservations sous la **de réservation**.

Pour afficher une commande de réservation, accédez à **réservations** > sélectionnez la réservation, puis cliquez sur le **ID de commande de réservation**.

![Exemple de détails de commande de réservation indiquant l’ID de commande de réservation ](./media/billing-manage-reserved-vm-instance/reservation-order-details.png)

Une réservation hérite des autorisations de son ordre de réservation.

## <a name="change-the-reservation-scope"></a>Modifier l’étendue de réservation

 Votre remise de réservation s’applique aux machines virtuelles, bases de données SQL, Azure Cosmos DB ou autres ressources qui correspondent à votre réservation et l’exécutent dans l’étendue de réservation. Le contexte de facturation dépend de l’abonnement utilisé pour acheter la réservation.

Pour mettre à jour l’étendue d’une réservation :

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Sélectionnez **Tous les services** > **Réservations**.
3. Sélectionnez la réservation.
4. Sélectionnez **Paramètres** > **Configuration**.
5. Modifiez l’étendue.

Si vous passez de l’étendue partagée à une étendue unique, vous ne pouvez sélectionner que les abonnements dont vous êtes le propriétaire. Seuls peuvent être sélectionnés les abonnements présents dans le même contexte de facturation que celui de la réservation.

L’étendue s’applique uniquement aux abonnements MS-AZR-0003P ou MS-AZR-0023P de l’offre avec paiement à l’utilisation, MS-AZR-0017P ou MS-AZR-0148P de l’offre Entreprise ou CSP.

## <a name="add-or-change-users-who-can-manage-a-reservation"></a>Ajouter ou modifier les utilisateurs qui peuvent gérer une réservation

Vous pouvez déléguer la gestion de la réservation en ajoutant des utilisateurs aux rôles sur l’ordre de réservation ou la réservation. Par défaut, la personne qui place l’ordre de réservation et l’administrateur de compte ont le rôle de propriétaire sur l’ordre de réservation et la réservation.

Vous pouvez gérer l’accès aux commandes de réservations et les réservations indépendamment des abonnements qui bénéficient de la remise de réservation. Lorsque vous accordez des autorisations pour gérer une demande de réservation ou la réservation, il ne les autorisez à gérer l’abonnement. De même, si vous accordez des autorisations pour gérer un abonnement dans l’étendue de la réservation, il ne donne pas les droits pour gérer l’ordre de réservation ou la réservation.

Pour effectuer un échange ou un remboursement, l’utilisateur doit avoir accès à l’ordre de réservation. Lorsqu’il accorde une personne, il convient d’accorder des autorisations à l’ordre de réservation, pas la réservation.


Pour déléguer la gestion de l’accès à une réservation :

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Sélectionnez **Tous les services** > **Réservation** pour afficher la liste des réservations auxquelles vous avez accès.
3. Sélectionnez la réservation pour laquelle vous souhaitez déléguer l’accès à d’autres utilisateurs.
4. Sélectionnez **Contrôle d’accès (IAM)**.
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

## <a name="cancellations-and-exchanges"></a>Annulations et échanges

Selon le type de réservation, vous pourrez peut-être annuler ou échanger une réservation. Pour plus d’informations, consultez les sections relatives aux annulations et échanges dans les rubriques suivantes :

- [Prépayer des machines virtuelles avec des instances de machines virtuelles réservées Azure](..//virtual-machines/windows/prepay-reserved-vm-instances.md#cancellations-and-exchanges)
- [Prépayer des logiciels SUSE avec des réservations Azure](../virtual-machines/linux/prepay-suse-software-charges.md#cancellation-and-exchanges-not-allowed)
- [Prépayer des ressources de calcul SQL Database avec une capacité réservée Azure SQL Database](../sql-database/sql-database-reserved-capacity.md#cancellations-and-exchanges)

## <a name="change-optimize-setting-for-reserved-vm-instances"></a>Modifier le paramètre d’optimisation pour des instances de machine virtuelle réservées

 Lorsque vous achetez une instance de machine virtuelle réservée, vous choisissez sa flexibilité de taille ou sa priorité de capacité. La flexibilité de taille d’instance applique la remise sur réservation aux autres machines virtuelles du même [groupe de tailles de machine virtuelle](https://aka.ms/RIVMGroups). La priorité de capacité priorise la capacité de centre de données pour vos déploiements. Cette option offre une assurance supplémentaire quand à votre capacité à lancer les instances de machine virtuelle quand vous en avez besoin.

Par défaut, quand l’étendue de la réservation est partagée, la flexibilité de taille de l’instance est activée. La capacité de centre de données n’est pas priorisée pour les déploiements de machine virtuelle.

Pour les réservations dont l’étendue est unique, vous pouvez optimiser la réservation pour la priorité de capacité au lieu de la flexibilité de taille d’instance de machine virtuelle.

Pour mettre à jour le paramètre d’optimisation de la réservation :

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Sélectionnez **Tous les services** > **Réservations**.
3. Sélectionnez la réservation.
4. Sélectionnez **Paramètres** > **Configuration**.
5. Modifiez le paramètre **Optimiser pour**.

## <a name="need-help-contact-us"></a>Vous avez besoin d’aide ? Contactez-nous.

Si vous avez des questions ou besoin d’aide, [créer une demande de support](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les réservations Azure, consultez les articles suivants :

- [Quelles sont les réservations pour Azure ?](billing-save-compute-costs-reservations.md)

Acheter un plan de service :
- [Prépayer des machines virtuelles avec des instances de machines virtuelles réservées Azure](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Prépayer des ressources de calcul SQL Database avec une capacité réservée Azure SQL Database](../sql-database/sql-database-reserved-capacity.md)
- [Prépayer des ressources Azure Cosmos DB avec une capacité réservée Azure Cosmos DB](../cosmos-db/cosmos-db-reserved-capacity.md)

Acheter un plan de logiciels :
- [Prépayer les abonnements de logiciels de Red Hat à partir des réservations d’Azure](../virtual-machines/linux/prepay-rhel-software-charges.md)
- [Prépayer des logiciels SUSE avec des réservations Azure](../virtual-machines/linux/prepay-suse-software-charges.md)

Comprendre l’utilisation et remise :
- [Comprendre comment la remise sur réservation de machine virtuelle est appliquée](billing-understand-vm-reservation-charges.md)
- [Comprendre comment la remise de plan de logiciels de Red Hat Enterprise Linux est appliquée](../billing/billing-understand-rhel-reservation-charges.md)
- [Comprendre comment la remise sur offre logicielle SUSE Linux Enterprise est appliquée](../billing/billing-understand-suse-reservation-charges.md)
- [Comprendre comment les autres remises sur réservation sont appliquées](billing-understand-reservation-charges.md)
- [Comprendre l’utilisation d’une réservation pour votre abonnement avec paiement à l’utilisation](billing-understand-reserved-instance-usage.md)
- [Comprendre l’utilisation d’une réservation pour votre Accord de Mise en Œuvre Entreprise](billing-understand-reserved-instance-usage-ea.md)
- [Coûts des logiciels Windows non inclus dans les réservations](billing-reserved-instance-windows-software-costs.md)
