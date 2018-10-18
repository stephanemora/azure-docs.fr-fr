---
title: Gérer les réservations Azure | Microsoft Docs
description: Découvrez comment changer l’étendue de l’abonnement et gérer l’accès pour les réservations Azure.
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
ms.date: 08/08/2018
ms.author: cwatson
ms.openlocfilehash: 0b19bb0d77bb600258596ce369713464641a7d2f
ms.sourcegitcommit: 42405ab963df3101ee2a9b26e54240ffa689f140
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/28/2018
ms.locfileid: "47423236"
---
# <a name="manage-reservations-for-azure-resources"></a>Gérer les réservations pour les ressources Azure

Après avoir acheté une réservation Azure, il se peut que vous deviez l’appliquer à un autre abonnement, modifier la personne autorisés à la gérer, ou en modifier l’étendue. Vous pouvez également diviser une réservation en deux pour appliquer certaines des instances que vous avez achetées à un autre abonnement.

Si vous avez acheté Azure Reserved Virtual Machine Instances, vous pouvez modifier le paramètre d’optimisation de la réservation. La remise sur la réservation peut s’appliquer à des machines virtuelles de la même série, ou vous pouvez réserver de la capacité du centre de données pour une taille de machine virtuelle spécifique.

## <a name="change-the-scope-for-a-reservation"></a>Modifier l’étendue d’une réservation

 Votre remise de réservation s’applique aux machines virtuelles, aux bases de données SQL, à Azure Cosmos DB ou à d’autres ressources qui correspondent à votre réservation et qui s’exécutent dans les limites de l’étendue de la réservation. L’étendue d’une réservation peut être un abonnement unique ou tous les abonnements de votre contexte de facturation. Si vous définissez l’étendue à un seul abonnement, la réservation est mise en correspondance avec les ressources en cours d’exécution dans l’abonnement sélectionné. Si vous définissez l’étendue pour qu’elle soit partagée, Azure met en correspondance la réservation avec les ressources exécutées dans tous les abonnements du contexte de facturation. Le contexte de facturation dépend de l’abonnement utilisé pour acheter la réservation.

Pour mettre à jour l’étendue d’une réservation :

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Sélectionnez **Tous les services** > **Réservations**.
3. Sélectionnez la réservation.
4. Sélectionnez **Paramètres** > **Configuration**.
5. Modifiez l’étendue. 

Si vous passez de l’étendue partagée à une étendue unique, vous ne pouvez sélectionner que les abonnements dont vous êtes le propriétaire. Seuls peuvent être sélectionnés les abonnements présents dans le même contexte de facturation que celui de la réservation.

L’étendue s’applique uniquement aux abonnements MS-AZR-0003P de l’offre avec paiement à l’utilisation, MS-AZR-0017P de l’offre Entreprise ou CSP. Pour les contrats d’entreprise, les abonnements de développement et de test ne peuvent pas bénéficier de la remise de réservation.

## <a name="add-or-change-users-who-can-manage-a-reservation"></a>Ajouter ou modifier les utilisateurs qui peuvent gérer une réservation

Vous pouvez déléguer la gestion d’une réservation en ajoutant des utilisateurs aux rôles de la réservation. Par défaut, la personne qui a acheté la réservation et l’administrateur de compte disposent tous les deux du rôle de propriétaire sur la réservation.

Vous pouvez gérer l’accès aux réservations, indépendamment des abonnements qui bénéficient de la remise de réservation. Lorsque vous accordez des autorisations de gestion pour une réservation, cela ne veut pas dire que vous octroyez des droits sur la gestion de l’abonnement. Et si vous accordez des autorisations de gestion d’un abonnement dans les limites de l’étendue de la réservation, vous n’octroyez aucun droit permettant de gérer la réservation.

Pour déléguer la gestion de l’accès à une réservation :

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Sélectionnez **Tous les services** > **Réservation** pour afficher la liste des réservations auxquelles vous avez accès.
3. Sélectionnez la réservation pour laquelle vous souhaitez déléguer l’accès à d’autres utilisateurs.
4. Sélectionnez **Contrôle d’accès (IAM)**.
5. Sélectionnez **Ajouter** > **Rôle** > **Propriétaire**. Ou si vous souhaitez accorder un accès limité, sélectionnez un autre rôle.
6. Tapez l’adresse e-mail de l’utilisateur à ajouter comme propriétaire.
7. Sélectionnez l’utilisateur, puis **Enregistrer**.

## <a name="split-a-single-reservation-into-two-reservations"></a>Diviser une réservation unique en deux réservations

 Après avoir acheté plusieurs instances de ressource dans une réservation, vous pouvez affecter de instances de cette réservation à d’autres abonnements. Par défaut, toutes les instances ont une étendue : abonnement unique ou partagé. Par exemple, vous avez acheté 10 instances de réservation et spécifié l’étendue de l’abonnement A. Vous pouvez à présent modifier l’étendue de 7 réservations à l’abonnement A, et les 3 instances restantes à l’abonnement B. La division d’une réservation vous permet de répartir les instances pour une gestion de l’étendue précise. Vous pouvez simplifier la répartition sur les abonnements en choisissant une étendue partagée. Toutefois, pour des raisons de gestion de coûts et de budgétisation, vous pouvez affecter des quantités à des abonnements spécifiques.

 Vous pouvez diviser une réservation en deux réservations par l’intermédiaire de PowerShell, de CLI ou de l’API.

### <a name="split-a-reservation-by-using-powershell"></a>Diviser une réservation à l’aide de PowerShell

1. Récupérez l’ID de l’ordre de réservation en exécutant la commande suivante :

    ```powershell
    # Get the reservation orders you have access to
    Get-AzureRmReservationOrder
    ```

2. Récupérez les détails d’une réservation :

    ```powershell
    Get-AzureRmReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId b8be062a-fb0a-46c1-808a-5a844714965a
    ```

3. Divisez la réservation en deux et répartissez les instances :

    ```powershell
    # Split the reservation. The sum of the reservations, the quantity, must equal the total number of instances in the reservation that you're splitting.
    Split-AzureRmReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId b8be062a-fb0a-46c1-808a-5a844714965a -Quantity 3,2
    ```
4. Vous pouvez mettre à jour l’étendue en exécutant la commande suivante :

    ```powershell
    Update-AzureRmReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId 5257501b-d3e8-449d-a1ab-4879b1863aca -AppliedScopeType Single -AppliedScope /subscriptions/15bb3be0-76d5-491c-8078-61fe3468d414
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

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les réservations Azure, consultez les articles suivants :

- [Qu’est-ce qu’une réservation Azure ?](billing-save-compute-costs-reservations.md)
- [Prépayer des machines virtuelles avec des instances de machines virtuelles réservées Azure](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Prépayer des ressources de calcul SQL Database avec une capacité réservée Azure SQL Database](../sql-database/sql-database-reserved-capacity.md)
- [Prépayer des ressources Azure Cosmos DB avec une capacité réservée Azure Cosmos DB](../cosmos-db/cosmos-db-reserved-capacity.md)
- [Prépayer des logiciels SUSE avec des réservations Azure](../virtual-machines/linux/prepay-suse-software-charges.md)
- [Comprendre comment la remise sur réservation de machine virtuelle est appliquée](billing-understand-vm-reservation-charges.md)
- [Comprendre comment la remise sur offre logicielle SUSE Linux Enterprise est appliquée](../billing/billing-understand-suse-reservation-charges.md)
- [Comprendre comment les autres remises sur réservation sont appliquées](billing-understand-reservation-charges.md)
- [Comprendre l’utilisation d’une réservation pour votre abonnement avec paiement à l’utilisation](billing-understand-reserved-instance-usage.md)
- [Comprendre l’utilisation d’une réservation pour votre Accord de Mise en Œuvre Entreprise](billing-understand-reserved-instance-usage-ea.md)
- [Coûts des logiciels Windows non inclus dans les réservations](billing-reserved-instance-windows-software-costs.md)

## <a name="need-help-contact-support"></a>Vous avez besoin d’aide ? Contacter le support technique

Si vous avez d’autres questions, [contactez le support technique](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pour obtenir une prise en charge rapide de votre problème.
