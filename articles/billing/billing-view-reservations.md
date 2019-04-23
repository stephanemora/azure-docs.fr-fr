---
title: Afficher les réservations pour les ressources Azure| Microsoft Docs
description: Découvrez comment afficher les réservations Azure dans le portail Azure.
documentationcenter: ''
author: yashesvi
manager: yashar
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/13/2019
ms.author: banders
ms.openlocfilehash: 872837c774368820527b12778b1a7dd4ddc5c7af
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2019
ms.locfileid: "59995521"
---
# <a name="view-azure-reservations-in-the-azure-portal"></a>Afficher les réservations Azure dans le portail Azure

Selon votre type d’abonnement et les autorisations, il existe deux manières d’afficher les réservations pour Azure.

## <a name="view-purchased-reservations"></a>Afficher les réservations achetées

Par défaut, lorsque vous achetez une réservation, l’administrateur de compte et vous pouvez afficher la réservation. Le compte administrateur et obtiennent automatiquement le rôle de propriétaire de la commande de réservation et de la réservation. Pour autoriser d’autres personnes afficher la réservation, vous devez les ajouter en tant qu’un **propriétaire** ou **lecteur** sur la commande de réservation ou de la réservation.

Pour plus d’informations, consultez la rubrique [Ajouter ou modifier les utilisateurs qui peuvent gérer une réservation](billing-manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation).

Pour afficher une réservation en tant que propriétaire ou lecteur, procédez comme suit :

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Effectuez une recherche sur **réservations**.
    ![Capture d’écran montrant la recherche du portail Azure](./media/billing-view-reservation/portal-reservation-search.png)  
3. La liste affiche toutes les réservations où vous avez le rôle de propriétaire ou de lecteur. Chaque réservation affiche le dernier pourcentage d’utilisation connus.  
    ![Exemple montrant une liste des réservations](./media/billing-view-reservation/view-reservations.png)
4. Sélectionnez une réservation et voir la tendance d’utilisation pour les cinq derniers jours.  
    ![Tendance d’utilisation d’exemple montrant réservation](./media/billing-view-reservation/reservation-utilization.png)
5. Vous pouvez également obtenir le [l’utilisation de réservation](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) à l’aide de l’API d’utilisation d’Instance réservée et avec le [pack de contenu Power BI de Microsoft Azure consommation Insights](/power-bi/service-connect-to-azure-consumption-insights).

Si vous avez besoin de modifier la portée d’une réservation, de la fractionner ou de changer les utilisateurs autorisés à la gérer, consultez la rubrique [Gérer les réservations Azure](billing-manage-reserved-vm-instance.md).

## <a name="view-reservation-transactions-for-enterprise-enrollments"></a>Afficher les transactions de réservation pour les inscriptions Entreprise

 Si vous avez une inscription Entreprise reposant sur un partenaire, affichez les réservations en accédant à **Rapports** dans le portail EA. Pour les autres inscriptions Entreprise, vous pouvez afficher les réservations dans le portail EA et dans le portail Azure. Vous devez être administrateur EA pour afficher les transactions de réservation.

Pour afficher les transactions de réservation dans le portail Azure, procédez comme suit :

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
1. Effectuez une recherche sur **Gestion des coûts + facturation**.

    ![Capture d’écran montrant la recherche dans le portail Azure](./media/billing-view-reservation/portal-cm-billing-search.png)

1. Sélectionnez **Transactions de réservation**.
1. Pour filtrer les résultats, sélectionnez **Intervalle de temps**, **Type** ou **Description**.
1. Sélectionnez **Appliquer**.

    ![Capture d’écran montrant les résultats de transactions de réservation](./media/billing-view-reservation/portal-billing-reservation-transaction-results.png)

Pour obtenir les données à l’aide d’une API, consultez la rubrique [Get Reserved Instance transaction charges for enterprise customers](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges) (Obtenir les frais de transaction des instances réservées pour les clients Entreprise).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les réservations Azure, consultez les articles suivants :

- [Quelles sont les réservations pour Azure ?](billing-save-compute-costs-reservations.md)
- [Gérer les réservations pour Azure](billing-manage-reserved-vm-instance.md)

Acheter un plan de service :

- [Prépayer une capacité réservée Cosmos DB](../cosmos-db/cosmos-db-reserved-capacity.md)
- [Prépayer des ressources de calcul SQL Database avec une capacité réservée Azure SQL Database](../sql-database/sql-database-reserved-capacity.md)
- [Prépayer des machines virtuelles avec des instances de machines virtuelles réservées Azure](../virtual-machines/windows/prepay-reserved-vm-instances.md)

Acheter un plan de logiciels :

- [Prépayer les abonnements de logiciels de Red Hat à partir des réservations d’Azure](../virtual-machines/linux/prepay-rhel-software-charges.md)
- [Prépayer des logiciels SUSE avec des réservations Azure](../virtual-machines/linux/prepay-suse-software-charges.md)

Comprendre l’utilisation de :

- [Comprendre l’utilisation d’une réservation pour votre abonnement avec paiement à l’utilisation](billing-understand-reserved-instance-usage.md)
- [Comprendre l’utilisation d’une réservation pour votre Accord de Mise en Œuvre Entreprise](billing-understand-reserved-instance-usage-ea.md)
- [Comprendre l’utilisation d’une réservations pour les abonnements CSP](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-us"></a>Vous avez besoin d’aide ? Nous contacter

Si vous avez des questions ou besoin d’aide, [créer une demande de support](https://go.microsoft.com/fwlink/?linkid=2083458).
