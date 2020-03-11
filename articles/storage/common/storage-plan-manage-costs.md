---
title: Planifier et gérer les coûts du stockage Azure
description: Découvrez comment planifier et gérer les coûts du stockage Azure avec l’analyse du coût dans le portail Azure.
services: storage
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: normesta
ms.subservice: common
ms.custom: subject-cost-optimization
ms.openlocfilehash: aa0b789b31f50c8b1ccf5450700874a02ad4664c
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2020
ms.locfileid: "78304360"
---
# <a name="plan-and-manage-costs-for-azure-storage"></a>Planifier et gérer les coûts du stockage Azure

Cet article explique comment planifier et gérer les coûts du stockage Azure. Vous devez d’abord utiliser la calculatrice de prix Azure pour planifier les coûts de stockage avant d’ajouter des ressources. Quand vous commencez à utiliser des ressources de stockage Azure, utilisez les fonctionnalités de gestion des coûts pour définir des budgets et superviser les coûts. Vous pouvez également passer en revue les coûts prévus et surveiller les tendances des dépenses pour identifier les domaines où vous pourriez agir.

Gardez à l’esprit que les coûts du stockage Azure ne représentent qu’une partie des coûts mensuels sur votre facture Azure. Cet article explique comment planifier et gérer les coûts du stockage Azure. Cependant, vous êtes facturé pour tous les services et ressources Azure utilisés pour votre abonnement Azure, y compris les services tiers. Quand vous êtes familiarisé avec la gestion des coûts du stockage Azure, vous pouvez appliquer des méthodes similaires pour gérer les coûts de tous les services Azure utilisés dans votre abonnement.

## <a name="prerequisites"></a>Prérequis

L’analyse des coûts prend en charge différents types de comptes Azure. Pour accéder à la liste complète des types de comptes pris en charge, voir [Comprendre les données de Cost Management](../../cost-management-billing/costs/understand-cost-mgt-data.md). Pour afficher les données de coût, vous avez au minimum besoin d’un accès en lecture pour votre compte Azure. Pour plus d’informations sur l’attribution de l’accès aux données Azure Cost Management, consultez [Assigner l’accès aux données](../../cost-management-billing/costs/assign-access-acm-data.md).

## <a name="estimate-costs-before-creating-an-azure-storage-account"></a>Estimer les coûts avant de créer un compte de stockage Azure

Utilisez la [calculatrice de prix Azure](https://azure.microsoft.com/pricing/calculator/) pour estimer les coûts avant de créer un compte de stockage Azure et de commencer à transférer des données vers ce compte.

1. Sur la page [Calculatrice de prix Azure](https://azure.microsoft.com/pricing/calculator/), choisissez la vignette **Comptes de stockage**.

2. Faites défiler la page vers le bas et recherchez la section **Comptes de stockage** de votre estimation.

3. Choisissez les options souhaitées dans les listes déroulantes. 

   L’estimation du coût est mise à jour à mesure que vous modifiez les valeurs de ces listes déroulantes. Cette estimation s’affiche dans le coin supérieur et en bas de l’écran d’estimation. 
    
   ![Superviser les coûts avec le volet Analyse des coûts](media/storage-plan-manage-costs/price-calculator-storage-type.png)

   Quand vous modifiez la valeur de la liste déroulante **Type**, d’autres options de cette feuille de calcul changent également. Utilisez les liens de la section **Plus d’informations** pour en savoir plus sur la fonction de chaque option et sur la façon dont ces options affectent le prix des opérations liées au stockage. 

4. Modifiez les options restantes pour voir leur impact sur votre estimation.

## <a name="use-budgets-and-cost-alerts"></a>Utiliser des budgets et des alertes de coût

Vous pouvez créer des [budgets](../../cost-management-billing/costs/tutorial-acm-create-budgets.md) pour gérer les coûts, et des alertes permettant d’avertir automatiquement les parties prenantes en cas d’anomalies de dépenses et de risques de dépenses excessives. Les alertes sont basées sur les dépenses par rapport aux seuils de budget et de coût. Les budgets et les alertes sont créés pour les abonnements et les groupes de ressources Azure : ils sont donc utiles dans le cadre d’une stratégie globale de supervision des coûts. Ils peuvent cependant avoir des fonctionnalités limitées quant à la gestion des coûts de services Azure individuels comme le coût du stockage Azure. En effet, ils sont conçus pour suivre les coûts à un niveau supérieur.

## <a name="monitor-costs"></a>Superviser les coûts

À mesure que vous utilisez des ressources Azure avec le stockage Azure, vous générez des coûts. Les coûts unitaires d’utilisation des ressources varient selon les intervalles de temps (secondes, minutes, heures et jours) ou selon l’utilisation d’unités (octets, mégaoctets, etc.). Des coûts sont générés dès que vous commencez à utiliser le stockage Azure. Vous pouvez voir les coûts dans le volet [Analyse du coût](../../cost-management-billing/costs/quick-acm-cost-analysis.md) du portail Azure.

Quand vous utilisez l’analyse du coût, vous pouvez voir les coûts du stockage Azure dans des graphes et des tableaux pour différents intervalles de temps. Par exemple, par jour, par année ou pour le mois en cours ou le mois précédent. Vous pouvez également voir les coûts par rapport aux budgets et aux coûts prévus. Passez à des vues pour des périodes plus longues pour identifier les tendances des dépenses et déterminer où des dépassements ont pu se produire. Par ailleurs, si vous avez créé des budgets, vous pouvez facilement voir à quel moment ils ont été dépassés.

Pour voir les coûts du stockage Azure dans l’analyse du coût :

1. Connectez-vous au [portail Azure](https://portal.azure.com).

2. Ouvrez la fenêtre **Gestion des coûts + Facturation**, sélectionnez **Gestion des coûts** dans le menu, puis sélectionnez **Analyse des coûts**. Vous pouvez ensuite changer l’étendue pour un abonnement spécifique dans la liste déroulante **Étendue**.

   ![Superviser les coûts avec le volet Analyse des coûts](./media/storage-plan-manage-costs/cost-analysis-pane.png)

4. Pour voir uniquement les coûts du stockage Azure, sélectionnez **Ajouter un filtre**, puis sélectionnez **Nom du service**. Ensuite, choisissez **stockage** dans la liste. 

   Voici un exemple montrant les coûts du stockage Azure uniquement :

   ![Superviser les coûts de stockage avec le volet Analyse du coût](./media/storage-plan-manage-costs/cost-analysis-pane-storage.png)

Dans l’exemple précédent, vous avez pu voir le coût actuel du service. Les coûts par région Azure (emplacements) et par groupe de ressources sont également présentés.  

## <a name="next-steps"></a>Étapes suivantes

Apprenez-en davantage sur la gestion des coûts avec l’[analyse du coût](../../cost-management-billing/costs/quick-acm-cost-analysis.md).

Consultez les articles suivants pour en savoir plus sur le fonctionnement des prix avec le stockage Azure :

- [Vue d’ensemble de la tarification de Stockage Azure](https://azure.microsoft.com/pricing/details/storage/)
- [Optimiser les coûts de stockage d’objets blob avec une capacité réservée](../blobs/storage-blob-reserved-capacity.md)
