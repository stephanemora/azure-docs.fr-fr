---
title: Gérer les instances Azure Reserved Instances - Facturation Azure | Microsoft Docs
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
ms.openlocfilehash: fc473906be9c572e6d6549c85f9faa8fe7566b86
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/18/2018
ms.locfileid: "34303060"
---
# <a name="manage-reserved-instances"></a>Gérer des instances réservées

Après avoir acheté une instance Azure Reserved VM Instance, vous voudrez peut-être appliquer cette instance Reserved Instance à un autre abonnement que celui qui a été spécifié lors de l’achat. Si vos machines virtuelles correspondantes s’exécutent sur plusieurs abonnements, vous avez peut-être envie également de modifier l’étendue de l’instance Reserved Instance pour la partager. Afin d’optimiser cette remise d’instance Reserved Instance, vérifiez que le nombre d’instances achetées correspond aux attributs et au nombre de machines virtuelles qui sont en cours d’exécution. Pour en savoir plus sur les instances Azure Reserved Instances, consultez la page [Instances de machines virtuelles réservées Azure](https://go.microsoft.com/fwlink/?linkid=862121).

## <a name="change-the-scope-for-a-reserved-instance"></a>Modifier l’étendue d’une instance Reserved Instance
 Votre remise d’instance Reserved Instance s’applique aux machines virtuelles qui correspondent à votre instance Reserved Instance et qui s’exécutent dans les limites de l’étendue de l’instance Reserved Instance. L’étendue d’une instance Reserved Instance peut être un abonnement unique ou tous les abonnements de votre contexte de facturation. Si vous définissez l’étendue sur un abonnement unique, l’instance Reserved Instance est mise en correspondance avec les machines virtuelles exécutées dans l’abonnement sélectionné. Si vous définissez l’étendue pour qu’elle soit partagée, Azure met en correspondance l’instance Reserved Instance avec les machines virtuelles exécutées dans tous les abonnements du contexte de facturation. Le contexte de facturation dépend de l’abonnement utilisé pour acheter l’instance Reserved Instance. Pour en savoir plus, consultez [Prépayer les machines virtuelles avec des instances de machines virtuelles réservées](https://go.microsoft.com/fwlink/?linkid=861721).

Pour mettre à jour l’étendue d’une instance Reserved Instance : 
1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Sélectionnez **Tous les services** > **Réservations**.
3. Sélectionnez l’instance Reserved Instance.
4. Sélectionnez **Paramètres** > **Configuration**.
5. Modifiez l’étendue. Si vous passez de l’étendue partagée à une étendue unique, vous ne pouvez sélectionner que les abonnements dont vous êtes le propriétaire. Seuls peuvent être sélectionnés les abonnements présents dans le même contexte de facturation que celui de l’instance Reserved Instance. Le contexte de facturation est déterminé par l’abonnement que vous avez sélectionné au moment de l’achat de l’instance Reserved Instance. L’étendue s’applique uniquement aux abonnements MS-AZR-0003P de l’offre avec paiement à l’utilisation, et MS-AZR-0017P de l’offre Entreprise. Pour les contrats d’entreprise, les abonnements de développement et de test ne peuvent pas bénéficier de la remise d’instance Reserved Instance.

## <a name="split-a-single-reserved-instance-into-two-reserved-instances"></a>Fractionner une instance Reserved Instance en deux instances Reserved Instances
 Après avoir acheté plusieurs instances, vous voudrez peut-être affecter les instances d’une instance Reserved Instance à différents abonnements. Par défaut, toutes les instances (quantité spécifiée lors de l’achat) ont une étendue, qu’elle soit sur un abonnement unique ou partagée sur plusieurs abonnements. Par exemple, vous avez acheté 10 machines virtuelles Standard D2 et spécifié l’étendue sur l’abonnement A. Vous souhaitez à présent modifier l’étendue pour sept instances Reserved Instances sur l’abonnement A, et les trois instances restantes sur l’abonnement B. Le fractionnement d’une instance Reserved Instance vous permet de répartir les instances pour une gestion de l’étendue plus minutieuse. Vous pouvez simplifier la répartition sur les abonnements en choisissant une étendue partagée. Toutefois, pour des raisons de gestion de coûts et de budgétisation, vous pouvez affecter des quantités à des abonnements spécifiques.

 Vous pouvez fractionner une instance Reserved Instance en deux instances Reserved Instances par l’intermédiaire de PowerShell, de CLI ou de l’API.

### <a name="split-a-reserved-instance-by-using-powershell"></a>Fractionner une instance Reserved Instance à l’aide de PowerShell
1. Récupérez l’ID de l’ordre d’instance Reserved Instance en exécutant la commande suivante :

    ```powershell
    # Get the Reserved Instance orders you have access to
    Get-AzureRmReservationOrder
    ```
2. Récupérez les détails d’une instance Reserved Instance :

    ```powershell
    Get-AzureRmReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId b8be062a-fb0a-46c1-808a-5a844714965a
    ```
3. Fractionnez l’instance Reserved Instance en deux et répartissez les instances :

    ```powershell
    # Split the Reserved Instance. The sum of the Reserved Instances, the quantity, must equal the total number of instances in the Reserved Instance that you're splitting.
    Split-AzureRmReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId b8be062a-fb0a-46c1-808a-5a844714965a -Quantity 3,2
    ```
1. Vous pouvez mettre à jour l’étendue en exécutant la commande suivante :

    ```powershell
    Update-AzureRmReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId 5257501b-d3e8-449d-a1ab-4879b1863aca -AppliedScopeType Single -AppliedScope /subscriptions/15bb3be0-76d5-491c-8078-61fe3468d414
    ```

## <a name="add-or-change-users-who-can-manage-a-reserved-instance"></a>Ajouter ou modifier les utilisateurs qui peuvent gérer une instance Reserved Instance
Vous pouvez déléguer la gestion d’une instance Reserved Instance en ajoutant des utilisateurs aux rôles de l’instance Reserved Instance. Par défaut, la personne qui a acheté l’instance Reserved Instance et l’administrateur de compte disposent tous les deux du rôle Propriétaire sur l’instance Reserved Instance. 

Vous pouvez gérer l’accès aux instances Reserved Instances, indépendamment des abonnements qui bénéficient de la remise d’instance Reserved Instance. Lorsque vous accordez des autorisations de gestion pour une instance Reserved Instance, cela ne veut pas dire que vous octroyez des droits sur la gestion de l’abonnement. Et si vous accordez des autorisations de gestion d’un abonnement dans les limites de l’étendue de l’instance Reserved Instance, vous n’octroyez aucun droit permettant de gérer l’instance Reserved Instance.
 
Pour déléguer la gestion de l’accès à une instance Reserved Instance : 
1.  Connectez-vous au [portail Azure](https://portal.azure.com).
2.  Sélectionnez **Tous les services** > **Réservation** pour afficher la liste des instances Reserved Instances auxquelles vous avez accès.
3.  Sélectionnez l’instance Reserved Instance pour laquelle vous souhaitez déléguer l’accès à d’autres utilisateurs.
4.  Sélectionnez **Contrôle d’accès (IAM)** dans le menu.
5.  Sélectionnez **Ajouter** > **Rôle** > **Propriétaire** (ou un rôle différent si vous souhaitez accorder un accès limité). 
6. Entrez l’adresse e-mail de l’utilisateur à ajouter en tant que propriétaire. 
7. Sélectionnez l’utilisateur, puis **Enregistrer**.

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur les instances Azure Reserved Instances, consultez les articles suivants :

- [Réaliser des économies sur les machines virtuelles avec les instances de machine virtuelle réservées](billing-save-compute-costs-reservations.md)
- [Prépayer les machines virtuelles avec des instances de machines virtuelles réservées](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Comprendre comment la remise de l’offre d’instance de machine virtuelle réservée est appliquée](billing-understand-vm-reservation-charges.md)
- [Comprendre l’utilisation de l’offre d’instance réservée sur votre abonnement avec paiement à l’utilisation](billing-understand-reserved-instance-usage.md)
- [Comprendre l’utilisation de l’offre d’instance réservée pour l’inscription de votre entreprise](billing-understand-reserved-instance-usage-ea.md)
- [Coûts des logiciels Windows non inclus dans les instances réservées](billing-reserved-instance-windows-software-costs.md)

## <a name="need-help-contact-support"></a>Vous avez besoin d’aide ? Contacter le support technique

Si vous avez d’autres questions, [contactez le support technique](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pour obtenir une prise en charge rapide de votre problème.
