---
title: Afficher les réservations Azure | Microsoft Docs
description: Découvrez comment afficher les réservations Azure dans le portail Azure.
services: billing
documentationcenter: ''
author: yashesvi
manager: yashar
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/03/2018
ms.author: cwatson
ms.openlocfilehash: 2ae30ca55f3ca03a64438025960ddd807e288216
ms.sourcegitcommit: f58fc4748053a50c34a56314cf99ec56f33fd616
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/04/2018
ms.locfileid: "48272600"
---
# <a name="view-azure-reservations-in-the-azure-portal"></a>Afficher les réservations Azure dans le portail Azure

Selon votre type d’abonnement et vos autorisations, il existe deux manières d’afficher les réservations Azure.

## <a name="view-reservations-as-owner-or-reader"></a>Afficher les réservations en tant que propriétaire ou lecteur

Par défaut, lorsque vous achetez une réservation, l’administrateur de compte et vous pouvez afficher la réservation. L’administrateur de compte et vous obtenez automatiquement le rôle de propriétaire sur la réservation. Pour autoriser d’autres personnes à afficher la réservation, vous devez les ajouter en tant que **propriétaire** ou **lecteur** sur la réservation. Pour plus d’informations, consultez la rubrique [Ajouter ou modifier les utilisateurs qui peuvent gérer une réservation](billing-manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation).
 
Pour afficher une réservation en tant que propriétaire ou lecteur, procédez comme suit :

1. Connectez-vous au [Portail Azure]( http://portal.azure.com).
1. Effectuez une recherche sur **réservations**.

    ![Capture d’écran montrant la recherche dans le portail Azure](./media/billing-view-reservation/portal-reservation-search.png)

1. Vous obtenez une liste des réservations où vous avez le rôle de propriétaire ou de lecteur.

Si vous avez besoin de modifier la portée d’une réservation, de la fractionner ou de changer les utilisateurs autorisés à la gérer, consultez la rubrique [Gérer les réservations Azure](billing-manage-reserved-vm-instance.md).

## <a name="view-reservation-transactions-for-enterprise-enrollments"></a>Afficher les transactions de réservation pour les inscriptions Entreprise

 Si vous avez une inscription Entreprise reposant sur un partenaire, affichez les réservations en accédant à **Rapports** dans le portail EA. Pour les autres inscriptions Entreprise, vous pouvez afficher les réservations dans le portail EA et dans le portail Azure. Vous devez être administrateur EA pour afficher les transactions de réservation.

Pour afficher les transactions de réservation dans le portail Azure, procédez comme suit :

1. Connectez-vous au [Portail Azure]( http://portal.azure.com).
1. Effectuez une recherche sur **Gestion des coûts + facturation**.

    ![Capture d’écran montrant la recherche dans le portail Azure](./media/billing-view-reservation/portal-cm-billing-search.png)

1. Sélectionnez **Transactions de réservation**.
1. Pour filtrer les résultats, sélectionnez **Intervalle de temps**, **Type** ou **Description**.
1. Sélectionnez **Appliquer**.

    ![Capture d’écran montrant les résultats de transactions de réservation](./media/billing-view-reservation/portal-billing-reservation-transaction-results.png)

Pour obtenir les données à l’aide d’une API, consultez la rubrique [Get Reserved Instance transaction charges for enterprise customers](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges) (Obtenir les frais de transaction des instances réservées pour les clients Entreprise).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les réservations Azure, consultez les articles suivants :

- [Qu’est-ce qu’une réservation Azure ?](billing-save-compute-costs-reservations.md)
- [Prépayer une capacité réservée Cosmos DB](../cosmos-db/cosmos-db-reserved-capacity.md)
- [Prépayer des ressources de calcul SQL Database avec une capacité réservée Azure SQL Database](../sql-database/sql-database-reserved-capacity.md)
- [Prépayer des machines virtuelles avec des instances de machines virtuelles réservées Azure](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Gérer les réservations Azure](billing-manage-reserved-vm-instance.md)
- [Comprendre l’utilisation d’une réservation pour votre abonnement avec paiement à l’utilisation](billing-understand-reserved-instance-usage.md)
- [Comprendre l’utilisation d’une réservation pour votre Accord de Mise en Œuvre Entreprise](billing-understand-reserved-instance-usage-ea.md)
- [Comprendre l’utilisation d’une réservations pour les abonnements CSP](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-support"></a>Vous avez besoin d’aide ? Contacter le support technique

Si vous avez d’autres questions, [contactez le support technique](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pour obtenir une prise en charge rapide de votre problème.
