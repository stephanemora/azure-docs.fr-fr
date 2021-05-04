---
title: Affichage des transactions d’achat et de remboursement Azure Reservations
description: Découvrez comment afficher les transactions d’achat et de remboursement Azure Reservations.
author: yashesvi
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 04/27/2021
ms.author: banders
ms.openlocfilehash: 8366ba41ee302747fd5ede3cddc72814f29d30ec
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108147447"
---
# <a name="view-reservation-purchase-and-refund-transactions"></a>Affichage des transactions d’achat et de remboursement de réservations

Il existe différentes façons d’afficher les transactions d’achat et de remboursement de réservations. Il est possible d’utiliser le Portail Azure, Power BI et les API REST.

## <a name="view-reservation-purchases-in-the-azure-portal"></a>Afficher les achats de réservations dans le portail Azure

Les lecteurs de la facturation Contrat Entreprise et Contrat client Microsoft peuvent afficher les achats cumulés de réservations dans l’analyse des coûts.

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Accédez à **Cost Management + facturation**.
1. Sélectionnez Analyse des coûts dans le menu gauche.
1. Appliquez un filtre pour  **Modèle de tarification** , puis sélectionnez **réservation**.
1. Pour afficher les achats de réservations, appliquez un filtre pour  **Type de frais** , puis sélectionnez **achat**.
1. Définissez  **Granularité**  sur  **Mensuel**.
1. Définissez  **Histogramme (empilé)** comme type de graphique.

:::image type="content" source="./media/view-purchase-refunds/reservation-purchase-cost-analysis.png" alt-text="Capture d’écran montrant les achats de réservations dans l’analyse des coûts" lightbox="./media/view-purchase-refunds/reservation-purchase-cost-analysis.png" :::

## <a name="view-reservation-transactions-in-the-azure-portal"></a>Affichage des transactions de réservation sur le Portail Azure

Un administrateur de facturation d’Accord de Mise en Œuvre Entreprise ou de Contrat client Microsoft peut afficher les transactions de réservation dans Gestion des coûts + Facturation.

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Recherchez **Gestion des coûts + facturation**.
1. Sélectionnez **Transactions de réservation**.
1. Pour filtrer les résultats, sélectionnez **Intervalle de temps**, **Type** ou **Description**.
1. Sélectionnez **Appliquer**.

[![Capture d’écran indiquant les transactions de réservation sur le portail Azure](./media/view-purchase-refunds/azure-portal-reservation-transactions.png)](./media/view-purchase-refunds/azure-portal-reservation-transactions.png#lightbox)

## <a name="view-reservation-transactions-in-power-bi"></a>Affichage des transactions de réservation dans Power BI

Un administrateur de facturation d’Accord de Mise en Œuvre Entreprise ou de Contrat client Microsoft peut afficher les transactions de réservation dans l’application Power BI Cost Management.

1. Téléchargez [l’application Power BI Cost Management](https://appsource.microsoft.com/product/power-bi/costmanagement.azurecostmanagementapp).
1. Accédez au rapport d’achats RI.

[![Exemple montrant les transactions de réservation](./media/view-purchase-refunds/power-bi-reservation-transactions.png)](./media/view-purchase-refunds/power-bi-reservation-transactions.png#lightbox)

Pour plus d’informations, consultez [Application Power BI Azure Cost Management pour les Contrats Entreprise](../costs/analyze-cost-data-azure-cost-management-power-bi-template-app.md).

## <a name="use-apis-to-get-reservation-transactions"></a>Accéder aux transactions de réservation avec les API

Les utilisateurs du Contrat Entreprise (EA) et du Contrat client Microsoft peuvent accéder aux données des transactions de réservation à l’aide de [l’API Transactions de réservation – Lister](/rest/api/consumption/reservationtransactions/list).

## <a name="need-help-contact-us"></a>Vous avez besoin d’aide ? Contactez-nous.

Si vous avez des questions ou besoin d’aide, [créez une demande de support](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Étapes suivantes

- Pour découvrir comment gérer une réservation, consultez [Gérer les réservations Azure](manage-reserved-vm-instance.md).
- Pour plus d’informations sur les réservations Azure, consultez les articles suivants :
  - [Qu’est-ce qu’une réservation Azure ?](save-compute-costs-reservations.md)
  - [Gérer les réservations dans Azure](manage-reserved-vm-instance.md)