---
title: Résoudre les problèmes liés à l’absence d’abonnements éligibles dans le portail Azure
description: Cet article vous aide à résoudre les problèmes liés au message d’erreur indiquant l’absence d’abonnements éligibles dans le portail Azure lorsque vous essayez d’acheter une réservation.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: reservations
ms.author: banders
ms.reviewer: yashar
ms.topic: troubleshooting
ms.date: 06/27/2021
ms.openlocfilehash: 40002ab69c39481393654629a44a038dfc9d01af
ms.sourcegitcommit: 1c12bbaba1842214c6578d914fa758f521d7d485
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/28/2021
ms.locfileid: "112988928"
---
# <a name="troubleshoot-no-eligible-subscriptions"></a>Résoudre les problèmes liés à l’absence d’abonnements éligibles

Cet article vous aide à résoudre les problèmes liés au message d’erreur *Aucun abonnement éligible* dans le portail Azure lorsque vous essayez d’acheter une réservation.

## <a name="symptoms"></a>Symptômes

1. Connectez-vous au [portail Azure](https://portal.azure.com) et accédez à **Réservations**.
1. Sélectionnez **Ajouter**, puis sélectionnez un service.
1. Le message d’erreur suivant s’affiche :
   ```
    No eligible subscriptions
    
    You do not have any eligible subscriptions to purchase reservations. To purchase a reservation, you should have owner or reservation purchaser permission on at least one subscription of the following type: Pay-as-you-go, CSP, Microsoft Enterprise or Microsoft Customer Agreement.
    ```
1. Dans la zone **Sélectionner le produit que vous voulez acheter**, développez la liste **Abonnement de facturation** pour voir la raison pour laquelle un abonnement spécifique n’est pas éligible pour l’achat d’une instance réservée. L’image suivante montre des exemples expliquant pourquoi une réservation ne peut pas être achetée.  
    :::image type="content" source="./media/troubleshoot-no-eligible-subscriptions/select-product-to-purchase.png" alt-text="Exemple indiquant pourquoi une réservation ne peut pas être achetée" lightbox="./media/troubleshoot-no-eligible-subscriptions/select-product-to-purchase.png" :::

## <a name="cause"></a>Cause

Pour acheter une instance réservée Azure, vous devez disposer d’au moins un abonnement répondant aux conditions suivantes :

- L’abonnement doit être un type d’offre pris en charge. Les types d’offre pris en charge sont : paiement à l’utilisation, fournisseur de solutions cloud (CSP), Microsoft Azure Enterprise ou Contrat client Microsoft.
- Vous devez être propriétaire ou acheteur de réservation sur l’abonnement.

Lorsque vous n’avez pas d’abonnement conforme aux exigences, vous obtenez l’erreur `No eligible subscriptions`.

### <a name="cause-1"></a>Cause 1

L’abonnement doit être un type d’offre pris en charge. Les types d’offre pris en charge sont : paiement à l’utilisation, CSP, Microsoft Azure Enterprise ou contrat client Microsoft. Votre type d’abonnement n’est pas pris en charge. Lorsque vous sélectionnez un abonnement correspondant à un type d’offre qui ne prend pas en charge les réservations, l’erreur suivante s’affiche.

```
Subscription not eligible for purchase

This subscription is not eligible for reservation benefit an cannot be used to purchase a reservation.
```

:::image type="content" source="./media/troubleshoot-no-eligible-subscriptions/subscription-not-eligible.png" alt-text="Exemple illustrant le message d’erreur L’abonnement ne peut pas faire l’objet d’un achat" :::

### <a name="cause-2"></a>Cause 2

Vous devez être propriétaire ou acheteur de réservation sur l’abonnement. Si vous ne disposez pas des autorisations suffisantes, l’erreur suivante s’affiche.

```
You do not have owner or reservation purchaser access on the subscription

You can only purchase reservations using subscriptions on which you have owner or reservation purchaser access.
```

## <a name="solution"></a>Solution

- Si votre offre actuelle ne prend pas en charge les réservations, vous devez créer un nouvel abonnement Azure.
- Si vous n’avez pas accès à une réservation existante, vous pouvez en obtenir l’accès à partir du propriétaire actuel.

### <a name="solution-1"></a>Solution 1

Pour acheter une réservation, vous devez créer un nouvel abonnement Azure qui prend en charge les réservations.

- Si vous disposez d’un essai gratuit Azure, vous pouvez [mettre à niveau votre abonnement](../manage/upgrade-azure-subscription.md).
- Vous pouvez créer un nouvel abonnement Azure avec les [tarifs de paiement à l’utilisation](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/).
- Inscrivez-vous à un [Contrat client Microsoft](https://azure.microsoft.com/pricing/purchase-options/microsoft-customer-agreement/) et créez un nouvel abonnement.
- Achetez un nouvel abonnement avec un [CSP](https://www.microsoft.com/solution-providers/home) et créez un nouvel abonnement.

### <a name="solution-2"></a>Solution 2

Pour obtenir un accès de propriétaire à une réservation, vous devez obtenir l’accès à l’un ou l’autre des éléments suivants :

- L’ordre de réservation avec lequel la réservation a été achetée
- La réservation elle-même

Le propriétaire de l’ordre de réservation ou le propriétaire de la réservation en cours peut vous déléguer l’accès à l’aide des étapes suivantes.

Pour permettre à d’autres personnes de gérer des réservations, vous avez le choix entre deux options :

- Déléguer la gestion de l’accès pour un ordre de réservation individuel en attribuant le rôle Propriétaire à un utilisateur au niveau de l’étendue des ressources de l’ordre de réservation. Si vous souhaitez accorder un accès limité, sélectionnez un autre rôle.  
     Pour connaître les étapes détaillées, consultez [Attribuer des rôles Azure à l’aide du portail Azure](../../role-based-access-control/role-assignments-portal.md).

- Ajouter un utilisateur en tant qu’administrateur de facturation à un Contrat Entreprise ou à un Contrat client Microsoft :
    - Pour un Contrat Entreprise, ajoutez des utilisateurs avec le rôle d’_Administrateur d’entreprise_ qui permet d’afficher et de gérer tous les ordres de réservation qui s’appliquent au Contrat Entreprise. Les utilisateurs détenant le rôle d’_Administrateur d’entreprise (lecture seule)_ peuvent uniquement afficher la réservation. Les administrateurs de service et les propriétaires de compte ne peuvent pas afficher les réservations _à moins_ d’être explicitement ajoutés à celles-ci à l’aide du contrôle d’accès (IAM). Pour plus d’informations, consultez [Gestion des rôles Azure Enterprise](../manage/understand-ea-roles.md).

        _Les administrateurs d’entreprise peuvent prendre possession d’un ordre de réservation et peuvent ajouter d’autres utilisateurs à une réservation à l’aide du contrôle d’accès (IAM)._
    - Pour un Contrat client Microsoft, les utilisateurs détenant le rôle de propriétaire du profil de facturation ou le rôle de contributeur du profil de facturation peuvent gérer l’ensemble des achats de réservation effectués à l’aide du profil de facturation. Les lecteurs de profil de facturation et les gestionnaires de facture peuvent voir toutes les réservations qui sont réglées avec le profil de facturation. Toutefois, ils ne peuvent apporter aucune modification aux réservations.
    Pour plus d’informations, consultez [Rôles et tâches liés au profil de facturation](../manage/understand-mca-roles.md#billing-profile-roles-and-tasks).

Pour plus d’informations, consultez la rubrique [Ajouter ou modifier les utilisateurs qui peuvent gérer une réservation](manage-reserved-vm-instance.md#who-can-manage-a-reservation-by-default).

## <a name="next-steps"></a>Étapes suivantes

- Passez en revue [Ajouter ou modifier les utilisateurs qui peuvent gérer une réservation](manage-reserved-vm-instance.md#who-can-manage-a-reservation-by-default) si vous devez vous voir accorder l’accès par un propriétaire de la réservation.
