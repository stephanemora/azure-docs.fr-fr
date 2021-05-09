---
title: Rétrofacturation des coûts Azure Reservations
description: Découvrez comment afficher les coûts Azure Reservations pour la rétrofacturation.
author: yashesvi
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 03/10/2021
ms.author: banders
ms.openlocfilehash: 4059318e6b8052f3b0221c87e8a357cfc8679e44
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532759"
---
# <a name="charge-back-azure-reservation-costs"></a>Rétrofacturation des coûts Azure Reservations

Les lecteurs de la facturation Contrat Entreprise et Contrat client Microsoft peuvent afficher les données de coût amorti des réservations. Ils ont la possibilité d’utiliser ces données pour rétrofacturer la valeur monétaire d’un abonnement, d’un groupe de ressources, d’une ressource ou d’une balise à leurs partenaires. Dans les données amorties, le prix effectif est le coût de la réservation horaire au prorata. Le coût correspond au coût total de l’utilisation de la réservation par la ressource ce jour-là.

Les utilisateurs disposant d’un abonnement individuel ont accès aux données de coût amorti dans leur fichier d’utilisation. Lorsqu’une ressource obtient une remise de réservation, la section *Informations complémentaires* du fichier d’utilisation contient les détails de la réservation. Pour plus d’informations, consultez [Téléchargement de l’utilisation sur le Portail Azure](../understand/download-azure-daily-usage.md#download-usage-from-the-azure-portal-csv).

## <a name="see-reservation-usage-data-for-show-back-and-charge-back"></a>Consultez les données d’utilisation des réservations pour la récupération des données de facturation et la rétrofacturation

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Accédez à **Gestion des coûts + Facturation** 
3. Sélectionnez **Analyse des coûts** dans la navigation de gauche 
4. Sous **Coût réel**, sélectionnez la métrique **Coût amorti**.
5. Pour afficher les ressources utilisées par une réservation, appliquez un filtre pour **Réservation**, puis sélectionnez Réservations.
6. Sous **Granularité**, sélectionnez **Mensuelle** ou **Quotidienne**.
7. Sélectionnez **Tableau** comme type de graphe.
8. Définissez l’option **Regrouper par** sur **Ressource**.

[![Exemple indiquant les coûts des ressources de réservation utilisables pour la rétrofacturation](./media/charge-back-usage/amortized-reservation-costs.png)](./media/charge-back-usage/amortized-reservation-costs.png#lightbox)

Voici une vidéo qui montre comment afficher les coûts d’utilisation des réservations au niveau de l’abonnement, du groupe de ressources et de la ressource dans le portail Azure.

 > [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4sQOw] 

## <a name="get-the-data-for-show-back-and-charge-back"></a>Obtenir les données pour la récupération des données de facturation et la rétrofacturation
1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Accédez à **Gestion des coûts + Facturation** 
3. Sélectionnez **Exporter** dans la navigation de gauche. 
4. Cliquez sur le bouton **Ajouter**
5. Sélectionnez le bouton de métrique Coût amorti et configurez votre exportation

Le prix réel (EffectivePrice) pour l’utilisation qui obtient une remise de réservation correspond au coût au prorata de la réservation (au lieu de zéro). Cela vous permet de connaître la valeur monétaire de la consommation de réservation d’un abonnement, d’un groupe de ressources ou d’une ressource, et peut vous aider à refacturer en interne pour l’utilisation de la réservation. Le jeu de données contient également les heures inutilisées de la réservation. 

## <a name="get-azure-consumption-and-reservation-usage-data-using-api"></a>Obtenir les données d’utilisation des réservations et de consommation Azure à l’aide de l’API

Vous pouvez obtenir les données à l’aide de l’API ou les télécharger à partir du portail Azure.

Vous appelez l’[API Détails d’utilisation](/rest/api/consumption/usagedetails/list) pour obtenir les nouvelles données. Pour plus d’informations sur la terminologie, consultez [Termes relatifs à l’utilisation](../understand/understand-usage.md).

Voici un exemple d’appel de l’API Détails d’utilisation :

```
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{enrollmentId}/providers/Microsoft.Billing/billingPeriods/{billingPeriodId}/providers/Microsoft.Consumption/usagedetails?metric={metric}&amp;api-version=2019-05-01&amp;$filter={filter}
```

Pour plus d’informations sur {enrollmentId} et {billingPeriodId}, consultez l’article sur l’API [Détails d’utilisation - Liste](/rest/api/consumption/usagedetails/list).

Les informations contenues dans le tableau suivant sur metric (métrique) et filter (filtre) peuvent aider à résoudre les problèmes courants liés aux réservations.

| **Type de données de l’API** | Action d’appel d’API |
| --- | --- |
| **Tous les frais (utilisation et achats)** | Remplacez {metric} par ActualCost |
| **Utilisation qui a bénéficié d’une remise de réservation** | Remplacez {metric} par ActualCost<br><br>Remplacez {filter} par properties/reservationId%20ne%20 |
| **Utilisation qui n’a pas bénéficié d’une remise de réservation** | Remplacez {metric} par ActualCost<br><br>Remplacez {filter} par properties/reservationId%20eq%20 |
| **Frais amortis (utilisation et achats)** | Remplacez {metric} par AmortizedCost |
| **Rapport sur la réservation inutilisée** | Remplacez {metric} par AmortizedCost<br><br>Remplacez {filter} par properties/ChargeType%20eq%20'UnusedReservation' |
| **Achats de réservation** | Remplacez {metric} par ActualCost<br><br>Remplacez {filter} par properties/ChargeType%20eq%20'Purchase'  |
| **Remboursements** | Remplacez {metric} par ActualCost<br><br>Remplacez {filter} par properties/ChargeType%20eq%20'Refund' |

## <a name="download-the-usage-csv-file-with-new-data"></a>Télécharger le fichier CSV d’utilisation avec les nouvelles données

Si vous êtes administrateur EA, vous pouvez télécharger le fichier CSV qui contient les nouvelles données d’utilisation à partir du portail Azure. Ces données n’étant pas disponibles à partir du portail EA (ea.azure.com), vous devez télécharger le fichier d’utilisation à partir du portail Azure (portal.azure.com) pour afficher les nouvelles données.

Dans le portail Azure, accédez à [Gestion des coûts + facturation](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade/BillingAccounts).

1. Sélectionnez le compte de facturation.
2. Cliquer sur **Utilisation + frais**.
3. Cliquez sur **Télécharger**.  
![Exemple montrant où télécharger le fichier CSV des données d’utilisation dans le portail Azure](./media/understand-reserved-instance-usage-ea/portal-download-csv.png)
4. Dans **Détails de l’utilisation**, sélectionnez **Données d’utilisation amorties**.

Les fichiers CSV que vous téléchargez contiennent les coûts réels et les coûts amortis.

## <a name="need-help-contact-us"></a>Vous avez besoin d’aide ? Contactez-nous.

Si vous avez des questions ou besoin d’aide, [créez une demande de support](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Étapes suivantes
- Pour plus d’informations sur les données d’utilisation des réservations Azure, consultez les articles suivants :
  - [Coûts et utilisation des réservations dans le contrat Entreprise et le contrat client Microsoft](understand-reserved-instance-usage-ea.md)
 
