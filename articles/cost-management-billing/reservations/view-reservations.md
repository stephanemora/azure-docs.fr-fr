---
title: Afficher les réservations pour les ressources Azure| Microsoft Docs
description: Découvrez comment afficher les réservations Azure dans le portail Azure.
documentationcenter: ''
author: yashesvi
manager: yashar
editor: ''
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: 26d130023a4ed7b4131af3963ae7afdb33e79bd7
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/15/2020
ms.locfileid: "75986637"
---
# <a name="view-azure-reservations-in-the-azure-portal"></a>Afficher les réservations Azure dans le portail Azure

Selon votre type d’abonnement et vos autorisations, il existe deux manières d’afficher les réservations pour Azure.

## <a name="view-purchased-reservations"></a>Afficher les réservations achetées

Par défaut, lorsque vous achetez une réservation, l’administrateur de compte et vous pouvez afficher la réservation. L’administrateur de compte et vous obtenez automatiquement le rôle de propriétaire sur la réservation et l’ordre de réservation. Pour autoriser d’autres personnes à afficher la réservation, vous devez les ajouter en tant que **propriétaire** ou **lecteur** sur celle-ci.

Pour plus d’informations, consultez la rubrique [Ajouter ou modifier les utilisateurs qui peuvent gérer une réservation](manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation).

Pour afficher une réservation en tant que propriétaire ou lecteur, procédez comme suit :

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Recherchez **Réservations**.
    ![Capture d’écran montrant une recherche dans le portail Azure](./media/view-reservations/portal-reservation-search.png)  
3. La liste répertorie toutes les réservations sur lesquelles vous avez le rôle propriétaire ou lecteur. Chaque réservation affiche le dernier pourcentage d’utilisation connu.  
    ![Exemple montrant une liste des réservations](./media/view-reservations/view-reservations.png)
4. Sélectionnez une réservation pour voir la tendance d’utilisation sur les cinq derniers jours.  
    ![Exemple montrant une tendance d’utilisation de réservation](./media/view-reservations/reservation-utilization.png)
5. Vous pouvez également obtenir l’[utilisation de la réservation](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) à l’aide de l’API d’utilisation Instance réservée et avec le [pack de contenu Power BI Microsoft Azure Consumption Insights](/power-bi/service-connect-to-azure-consumption-insights).

Si vous avez besoin de modifier la portée d’une réservation, de la fractionner ou de changer les utilisateurs autorisés à la gérer, consultez la rubrique [Gérer les réservations Azure](manage-reserved-vm-instance.md).

## <a name="view-reservation-transactions-for-enterprise-enrollments"></a>Afficher les transactions de réservation pour les inscriptions Entreprise

 Si vous avez une inscription Entreprise reposant sur un partenaire, affichez les réservations en accédant à **Rapports** dans le portail EA. Pour les autres inscriptions Entreprise, vous pouvez afficher les réservations dans le portail EA et dans le portail Azure. Vous devez être administrateur EA pour afficher les transactions de réservation.

Pour afficher les transactions de réservation dans le portail Azure, procédez comme suit :

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Recherchez **Gestion des coûts + facturation**.

    ![Capture d’écran montrant la recherche dans le portail Azure](./media/view-reservations/portal-cm-billing-search.png)

1. Sélectionnez **Transactions de réservation**.
1. Pour filtrer les résultats, sélectionnez **Intervalle de temps**, **Type** ou **Description**.
1. Sélectionnez **Appliquer**.

    ![Capture d’écran montrant les résultats de transactions de réservation](./media/view-reservations/portal-billing-reservation-transaction-results.png)

Pour obtenir les données à l’aide d’une API, consultez la rubrique [Get Reserved Instance transaction charges for enterprise customers](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges) (Obtenir les frais de transaction des instances réservées pour les clients Entreprise).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les réservations Azure, consultez les articles suivants :

- [Qu’est-ce qu’une réservation Azure ?](save-compute-costs-reservations.md)
- [Gérer les réservations pour Azure](manage-reserved-vm-instance.md)

Acheter un plan de service :

- [Prépayer une capacité réservée Cosmos DB](../../cosmos-db/cosmos-db-reserved-capacity.md)
- [Prépayer des ressources de calcul SQL Database avec une capacité réservée Azure SQL Database](../../sql-database/sql-database-reserved-capacity.md)
- [Prépayer des machines virtuelles avec des instances de machines virtuelles réservées Azure](../../virtual-machines/windows/prepay-reserved-vm-instances.md)

Acheter un abonnement logiciel :

- [Prépayer des abonnements logiciels Red Hat dans Réservations Azure](../../virtual-machines/linux/prepay-rhel-software-charges.md)
- [Prépayer des logiciels SUSE avec des réservations Azure](../../virtual-machines/linux/prepay-suse-software-charges.md)

Comprendre l’utilisation :

- [Comprendre l’utilisation d’une réservation pour votre abonnement avec paiement à l’utilisation](understand-reserved-instance-usage.md)
- [Comprendre l’utilisation d’une réservation pour votre Accord de Mise en Œuvre Entreprise](understand-reserved-instance-usage-ea.md)
- [Comprendre l’utilisation d’une réservations pour les abonnements CSP](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-us"></a>Vous avez besoin d’aide ? Nous contacter

Si vous avez des questions ou besoin d’aide, [créez une demande de support](https://go.microsoft.com/fwlink/?linkid=2083458).
