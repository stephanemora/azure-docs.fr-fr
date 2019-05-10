---
title: Comprendre l’utilisation des réservations Azure pour les contrats entreprise
description: Découvrez comment analyser votre utilisation pour comprendre l’application de la réservation Azure pour votre Accord de Mise en Œuvre Entreprise.
author: bandersmsft
manager: yashar
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/07/2019
ms.author: banders
ms.openlocfilehash: 8d85dd1c21f952261e838c01843e15dafcc0e931
ms.sourcegitcommit: 300cd05584101affac1060c2863200f1ebda76b7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/08/2019
ms.locfileid: "65415785"
---
# <a name="get-enterprise-agreement-reservation-costs-and-usage"></a>L’utilisation et les coûts de réservation de contrat entreprise

Les coûts de réservation et les données d’utilisation sont disponibles pour les clients contrat entreprise dans le portail Azure et l’API REST. Cet article vous aide à :

- Obtenir des données d’achat de réservation
- Obtenir des données d’utilisation incomplète de réservation
- Amortir le coût de réservation
- Facturation interne pour l’utilisation de la réservation
- Calculer les économies de réservation

Frais de place de marché sont consolidés dans les données d’utilisation. Vous permet d’afficher les frais pour la première utilisation de tiers, l’utilisation de la place de marché et achats à partir d’une source de données.

## <a name="reservation-charges-in-azure-usage-data"></a>Frais de réservation dans les données d’utilisation Azure

Données sont divisées en deux jeux de données distincts : _Coût réel_ et _coût amorti_. Comment ces deux jeux de données diffère :

**Coût réel** -fournit des données au rapprochement avec votre facture mensuelle. Ces données ont réservation les coûts d’achat. Il possède zéro EffectivePrice pour l’utilisation qui a reçu la remise de réservation.

**Coût amorti** -la ressource EffectiveCost qui obtient la remise de réservation est le coût au prorata de l’instance réservée. Le jeu de données a également les coûts de réservation inutilisé. La somme du coût de réservation et réservation inutilisée fournit le coût amorti quotidien de la réservation.

Comparaison de deux jeux de données :

| Données | Jeu de données de coût réel | Jeu de données de coût amorti |
| --- | --- | --- |
| Achats de réservation | Disponible dans cette vue.<br>  Pour obtenir ce filtre de données sur ChargeType = &quot;achat&quot;. <br> Référence à l’ID de réservation ou ReservationName savoir quels réservation le coût a trait.  | Non applicable à cette vue. <br> Les coûts d’acquisition ne sont pas fournis dans les données amorties. |
| EffectivePrice | La valeur est égale à zéro pour l’utilisation qui obtient la remise de réservation. | La valeur est le coût horaire au prorata de la réservation pour l’utilisation qui a la remise de réservation. |
| Réservation inutilisée (fournit le nombre d’heures de que la réservation ne sont pas utilisée dans une journée et la valeur monétaire des déchets) | Non applicable dans cette vue. | Disponible dans cette vue.<br> Pour obtenir ces données, filtrer sur ChargeType = &quot;UnusedReservation&quot;.<br>  Référence à l’ID de réservation ou ReservationName savoir quels réservation a été sous-utilisée. Il s’agit de la quantité de la réservation a été perdu dans pour la journée.  |
| UnitPrice (prix de la ressource à partir de votre table de tarification) | Disponible | Disponible |

Autres informations disponibles dans les données d’utilisation Azure a changé :

- Produit et des informations de compteur - Azure ne remplace pas le compteur à l’origine à utiliser avec l’ID de réservation et ReservationName, comme elle le faisait auparavant.
- ID de réservation et ReservationName - ils sont leurs propres champs dans les données. Auparavant, il auparavant disponibles uniquement sous AdditionalInfo.
- ProductOrderId - l’ID de commande de réservation, ajouté en tant que son propre champ.
- ProductOrderName - nom de produit de la réservation achetée.
- Terme - 12 mois ou 36 mois.
- RINormalizationRatio - sous AdditionalInfo. C’est le rapport où la réservation est appliquée à l’enregistrement de l’utilisation. Si la flexibilité de taille d’instance est activée sur votre réservation, puis l’appliquer à d’autres tailles. La valeur indique le rapport que la réservation a été appliquée à l’enregistrement de l’utilisation.

## <a name="get-azure-consumption-and-reservation-usage-data-using-api"></a>Obtenir des données d’utilisation de la consommation et de réservation Azure à l’aide API

Vous pouvez obtenir les données à l’aide de l’API ou téléchargez-le à partir du portail Azure.

Vous appelez le [API détails d’utilisation](/rest/api/consumption/usagedetails/list) avec la version de l’API &quot;2019-04-01-preview&quot; pour obtenir les nouvelles données. Pour plus d’informations sur la terminologie, consultez [conditions d’utilisation](billing-understand-your-usage.md). L’appelant doit être un administrateur d’entreprise pour l’accord d’entreprise à l’aide du [portail EA](https://ea.azure.com). Les administrateurs d’entreprise en lecture seule peut également obtenir les données.

Les données ne sont pas disponibles dans [API de création de rapports pour les clients Enterprise - détails d’utilisation](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail).

Voici un exemple d’appel à l’API :

```
https://consumption.azure.com/providers/Microsoft.Billing/billingAccounts/{enrollmentId}/providers/Microsoft.Billing/billingPeriods/{billingPeriodId}/providers/Microsoft.Consumption/usagedetails?metric={metric}&amp;api-version=2019-04-01-preview&amp;$filter={filter}
```

Pour plus d’informations sur {enrollmentId} et {billingPeriodId}, consultez le [détails d’utilisation – liste](https://docs.microsoft.com/rest/api/consumption/usagedetails/list) article sur l’API.

Les informations contenues dans le tableau suivant sur mesure et le filtre peut aider à résoudre les problèmes courants de réservation.

| **Type de données de l’API** | Action d’appel API |
| --- | --- |
| **Tous les frais (achats et l’utilisation)** | Remplacez {métrique} par ActualCost |
| **Utilisation qui a obtenu la remise de réservation** | Remplacez {métrique} par ActualCost<br>Remplacez {filter} par : properties/reservationId%20ne%20 |
| **Utilisation qui n’a pas obtenu la remise de réservation** | Remplacez {métrique} par ActualCost<br>Remplacez {filter} par : properties/reservationId%20eq%20 |
| **Frais amortis (achats et l’utilisation)** | Remplacez {métrique} par AmortizedCost |
| **Rapport de réservation inutilisées** | Remplacez {métrique} par AmortizedCost<br>Remplacez {filter} par : properties/ChargeType%20eq%20'UnusedReservation' |
| **Achats de réservation** | Remplacez {métrique} avec ActualCostReplace {filter} : properties/ChargeType%20eq%20'Purchase'  |
| **Remboursements** | Remplacez {métrique} par ActualCost<br>Remplacez {filter} par : properties/ChargeType%20eq%20'Refund' |

## <a name="download-the-usage-csv-file-with-new-data"></a>Télécharger le fichier CSV d’utilisation avec de nouvelles données

Si vous êtes un administrateur EA, vous pouvez télécharger le fichier CSV qui contient les nouvelles données d’utilisation à partir du portail Azure. Ces données n’est pas disponibles à partir de la [portail EA](https://ea.azure.com).

Dans le portail Azure, accédez à [gestion des coûts + facturation](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade/BillingAccounts).

1. Sélectionnez le compte de facturation.
2. Cliquez sur **utilisation + frais**.
3. Cliquez sur **Télécharger**.  
![Exemple montrant où télécharger le fichier de données de l’utilisation de volume partagé de cluster dans le portail Azure](./media/billing-understand-reserved-instance-usage-ea/portal-download-csv.png)
4. Dans **télécharger l’utilisation + frais** , sous **détails d’utilisation, Version 2** , sélectionnez **tous les frais (utilisation et achats)** puis cliquez sur Télécharger. Répétez ces étapes pour **amorti frais (utilisation et achats)**.

Les fichiers CSV que vous téléchargez contiennent des coûts réels et les coûts amortis.

## <a name="common-cost-and-usage-tasks"></a>Tâches courantes de coût et d’utilisation

Les sections suivantes sont des tâches courantes que la plupart des gens utilisent pour afficher leurs données d’utilisation et de coût de réservation.

### <a name="get-reservation-purchase-costs"></a>Obtenir des coûts d’achat de réservation

Coûts d’achat de réservation sont disponibles dans les données de coût réel. Filtrer les _ChargeType = bon_. Reportez-vous à ProductOrderID pour déterminer quel ordre de réservation l’achat concerne.

### <a name="get-underutilized-reservation-quantity-and-costs"></a>Obtenir la quantité de réservation sous-utilisés et les coûts

Obtenir des données de coût amorti et filtrer les _ChargeType_ _= UnusedReservation_. Vous obtenez la quantité de réservation inutilisé quotidienne et le coût. Vous pouvez filtrer les données pour une réservation ou une commande de réservation à l’aide _ID de réservation_ et _ProductOrderId_ champs, respectivement. Si une réservation a été utilisé à 100 %, l’enregistrement a une quantité égale à 0.

### <a name="amortize-reservation-costs"></a>Amortir le coût de réservation

Obtenir des données de coût amorti et de filtre pour une commande de réservation à l’aide _ProductOrderID_ pour obtenir les coûts amortis quotidiennes pour une réservation.

### <a name="chargeback-for-a-reservation"></a>Facturation interne pour une réservation

Vous pouvez utiliser de réservation de rétrofacturation à d’autres organisations par abonnement, les groupes de ressources ou les balises. Les données de coût amorti fournissant une valeur monétaire de l’utilisation d’une réservation sur les types de données suivants :

- Ressources (par exemple, une machine virtuelle)
- Groupe de ressources
- Balises
- Abonnement

### <a name="get-the-blended-rate-for-chargeback"></a>Obtenir le taux de combinées pour la facturation interne

Pour déterminer le taux combinée, obtenir les données de coûts amortis et le coût total d’agrégation. Pour les machines virtuelles, vous pouvez utiliser les informations de nom du compteur ou ServiceType à partir de données AdditionalInfo JSON. Diviser le coût total par la quantité utilisée pour obtenir le taux combinée.

### <a name="audit-optimum-reservation-use-for-instance-size-flexibility"></a>Réservation optimale d’audit par exemple utiliser la flexibilité de taille

La quantité de multiples avec le _RINormalizationRatio_, à partir d’AdditionalInfo. Les résultats indiquent le nombre d’heures d’utilisation de la réservation a été appliqué à l’enregistrement de l’utilisation.

### <a name="determine-reservation-savings"></a>Déterminer les économies de réservation

Obtenir les données de coûts Amortized et filtrer les données pour une instance réservée. Ensuite :

1. Obtenir l’estimation des coûts paiement à l’utilisation. Multipliez la _UnitPrice_ valeur _quantité_ valeurs à obtenir estimé des frais de paiement à l’utilisation, si la remise de réservation ne s’applique à l’utilisation.
2. Obtenir les coûts de réservation. Somme du _coût_ valeurs à obtenir la valeur monétaire de ce que vous avez payé pour l’instance réservée. Il inclut les coûts utilisés et non utilisés de la réservation.
3. Soustraire les coûts de réservation à partir de l’estimation des coûts de paiement à l’utilisation pour obtenir l’estimation d’économies.

## <a name="reservation-purchases-and-amortization-in-azure-cost-analysis"></a>Achats de réservation et amortissement dans l’analyse des coûts Azure

Coût de l’instance réservée est disponible dans [mode d’aperçu des coûts Azure analyse](https://preview.portal.azure.com/?feature.canmodifystamps=true&amp;microsoft_azure_costmanagement=stage2&amp;Microsoft_Azure_CostManagement_arm_canary=true&amp;Microsoft_Azure_CostManagement_apiversion=2019-04-01-preview&amp;Microsoft_Azure_CostManagement_amortizedCost=true#blade/Microsoft_Azure_CostManagement/Menu/costanalysis). Par défaut, la vue de données de coût est pour le coût réel. Vous pouvez basculer vers le coût amorti. Voici un exemple.

![Exemple montrant où sélectionner coût amorti dans l’analyse des coûts](./media/billing-understand-reserved-instance-usage-ea/portal-cost-analysis-amortized-view.png)

Appliquer des filtres pour afficher vos frais par un type de réservation ou de frais. Regrouper sur le nom de la réservation pour afficher les coûts ventilés par réservations.

## <a name="need-help-contact-us"></a>Vous avez besoin d’aide ? Contactez-nous.

Si vous avez des questions ou besoin d’aide, [créer une demande de support](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les réservations Azure, consultez les articles suivants :

- [Qu’est-ce qu’une réservation Azure ?](billing-save-compute-costs-reservations.md)
- [Prépayer des machines virtuelles avec des instances de machines virtuelles réservées Azure](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Prépayer des ressources de calcul SQL Database avec une capacité réservée Azure SQL Database](../sql-database/sql-database-reserved-capacity.md)
- [Gérer les réservations Azure](billing-manage-reserved-vm-instance.md)
- [Comprendre comment la remise de réservation est appliquée](billing-understand-vm-reservation-charges.md)
- [Comprendre l’utilisation d’une réservation pour votre abonnement avec paiement à l’utilisation](billing-understand-reserved-instance-usage.md)
- [Coûts des logiciels Windows non inclus dans les réservations](billing-reserved-instance-windows-software-costs.md)
