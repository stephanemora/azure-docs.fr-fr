---
title: Gérer les instances de machines virtuelles réservées Azure | Microsoft Docs
description: Découvrez comment modifier l’étendue de l’abonnement et gérer l’accès à Azure Reserved VM Instances.
services: billing
documentationcenter: ''
author: vikramdesai01
manager: vikramdesai01
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/09/2018
ms.author: vikdesai
ms.openlocfilehash: ddb9d46dc2689b0dbcd8734e276916f7cd9d2728
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37064415"
---
# <a name="manage-reserved-instances-in-azure"></a>Gérer des instances réservées dans Azure

Après avoir acheté une instance de machine virtuelle réservée Azure, vous pouvez appliquer l’instance réservée à un autre abonnement que celui spécifié lors de l’achat. Si vos machines virtuelles correspondantes s’exécutent sur plusieurs abonnements, vous pouvez aussi changer l’étendue de l’instance réservée et la rendre partagée. Pour maximiser l’économie liée à l’instance réservée, vérifiez que le nombre d’instances achetées correspond aux attributs et au nombre de machines virtuelles qui sont en cours d’exécution. Pour en savoir plus sur les instances réservées Azure, consultez [Réaliser des économies en prépayant des machines virtuelles Azure](https://go.microsoft.com/fwlink/?linkid=862121).

## <a name="change-the-scope-for-a-reserved-instance"></a>Changer l’étendue d’une instance réservée
 La remise liée à votre instance réservée s’applique aux machines virtuelles qui correspondent à votre instance réservée et qui s’exécutent dans l’étendue de l’instance réservée. L’étendue d’une instance réservée peut être un seul abonnement ou tous les abonnements de votre contexte de facturation. Si vous définissez l’étendue sur un seul abonnement, l’instance réservée est mise en correspondance avec les machines virtuelles exécutées dans l’abonnement sélectionné. Si vous définissez l’étendue pour qu’elle soit partagée, Azure met en correspondance l’instance réservée avec les machines virtuelles qui s’exécutent dans tous les abonnements du contexte de facturation. Le contexte de facturation dépend de l’abonnement utilisé pour acheter l’instance réservée. Pour plus d’informations, consultez [Prépayer les machines virtuelles avec des instances réservées](https://go.microsoft.com/fwlink/?linkid=861721).

Pour mettre à jour l’étendue d’une instance réservée : 
1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Sélectionnez **Tous les services** > **Réservations**.
3. Sélectionnez l’instance réservée.
4. Sélectionnez **Paramètres** > **Configuration**.
5. Modifiez l’étendue. Si vous passez de l’étendue partagée à une étendue unique, vous ne pouvez sélectionner que les abonnements dont vous êtes le propriétaire. Seuls peuvent être sélectionnés les abonnements du même contexte de facturation que celui de l’instance réservée. Le contexte de facturation est déterminé par l’abonnement que vous avez sélectionné au moment de l’achat de l’instance réservée. L’étendue s’applique uniquement aux abonnements MS-AZR-0003P de l’offre avec paiement à l’utilisation, et MS-AZR-0017P de l’offre Entreprise. Pour les contrats d’entreprise, les abonnements de développement et de test ne peuvent pas bénéficier de la remise liée aux instances réservées.

## <a name="split-a-single-reserved-instance-into-two-reserved-instances"></a>Fractionner une instance réservée en deux instances réservées
 Après avoir acheté plusieurs instances, vous pouvez affecter des instances d’une instance réservée à différents abonnements. Par défaut, toutes les instances (quantité spécifiée lors de l’achat) ont une étendue, qu’elle soit sur un abonnement unique ou partagée sur plusieurs abonnements. Par exemple, vous avez acheté 10 machines virtuelles Standard D2 et spécifié l’étendue sur l’abonnement A. Vous souhaitez à présent modifier l’étendue pour sept instances réservées sur l’abonnement A, et les trois instances restantes sur l’abonnement B. Le fractionnement d’une instance réservée vous permet de répartir les instances pour une gestion plus fine de l’étendue. Vous pouvez simplifier la répartition sur les abonnements en choisissant une étendue partagée. Toutefois, pour des raisons de gestion de coûts et de budgétisation, vous pouvez affecter des quantités à des abonnements spécifiques.

 Vous pouvez fractionner une instance réservée en deux instances réservées via PowerShell, CLI ou l’API.

### <a name="split-a-reserved-instance-by-using-powershell"></a>Fractionner une instance réservée avec PowerShell
1. Récupérez l’ID de commande de l’instance réservée en exécutant la commande suivante :

    ```powershell
    # Get the reserved instance orders you have access to
    Get-AzureRmReservationOrder
    ```
2. Récupérez les détails d’une instance réservée :

    ```powershell
    Get-AzureRmReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId b8be062a-fb0a-46c1-808a-5a844714965a
    ```
3. Fractionnez l’instance réservée en deux et répartissez les instances :

    ```powershell
    # Split the reserved instance. The sum of the reserved instances, the quantity, must equal the total number of instances in the reserved instance that you're splitting.
    Split-AzureRmReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId b8be062a-fb0a-46c1-808a-5a844714965a -Quantity 3,2
    ```
1. Vous pouvez mettre à jour l’étendue en exécutant la commande suivante :

    ```powershell
    Update-AzureRmReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId 5257501b-d3e8-449d-a1ab-4879b1863aca -AppliedScopeType Single -AppliedScope /subscriptions/15bb3be0-76d5-491c-8078-61fe3468d414
    ```

## <a name="add-or-change-users-who-can-manage-a-reserved-instance"></a>Ajouter ou changer les utilisateurs qui peuvent gérer une instance réservée
Vous pouvez déléguer la gestion d’une instance réservée en ajoutant des utilisateurs aux rôles sur l’instance réservée. Par défaut, la personne qui a acheté l’instance réservée et l’administrateur de compte disposent tous les deux du rôle Propriétaire sur l’instance réservée. 

Vous pouvez gérer l’accès aux instances réservées indépendamment des abonnements qui bénéficient de la remise liée aux instances réservées. Quand vous accordez des autorisations de gestion pour une instance réservée, cela ne veut pas dire que vous octroyez des droits sur la gestion de l’abonnement. Et si vous accordez des autorisations de gestion d’un abonnement dans les limites de l’étendue de l’instance réservée, vous n’octroyez aucun droit permettant de gérer l’instance réservée.
 
Pour déléguer la gestion de l’accès à une instance réservée : 
1.  Connectez-vous au [portail Azure](https://portal.azure.com).
2.  Sélectionnez **Tous les services** > **Réservation** pour afficher la liste des instances réservées auxquelles vous avez accès.
3.  Sélectionnez l’instance réservée pour laquelle vous voulez déléguer l’accès à d’autres utilisateurs.
4.  Sélectionnez **Contrôle d’accès (IAM)** dans le menu.
5.  Sélectionnez **Ajouter** > **Rôle** > **Propriétaire** (ou un rôle différent si vous souhaitez accorder un accès limité). 
6. Entrez l’adresse e-mail de l’utilisateur à ajouter en tant que propriétaire. 
7. Sélectionnez l’utilisateur, puis **Enregistrer**.

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur les instances réservées Azure, consultez les articles suivants :

- [Présentation des instances de machines virtuelles réservées Azure](billing-save-compute-costs-reservations.md)
- [Prépayer des machines virtuelles avec des instances de machines virtuelles réservées Azure](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Comprendre comment la remise liée à une instance réservée est appliquée](billing-understand-vm-reservation-charges.md)
- [Comprendre l’utilisation de l’instance réservée sur votre abonnement avec paiement à l’utilisation](billing-understand-reserved-instance-usage.md)
- [Comprendre l’utilisation de l’instance réservée pour votre Accord de Mise en Œuvre Entreprise](billing-understand-reserved-instance-usage-ea.md)
- [Coûts des logiciels Windows non inclus dans les instances réservées](billing-reserved-instance-windows-software-costs.md)

## <a name="need-help-contact-support"></a>Vous avez besoin d’aide ? Contacter le support technique

Si vous avez d’autres questions, [contactez le support technique](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pour obtenir une prise en charge rapide de votre problème.
