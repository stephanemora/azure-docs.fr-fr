---
title: Planifier et gérer les coûts pour Azure Cosmos DB
description: Découvrez comment planifier et gérer les coûts pour Azure Cosmos DB avec l’analyse des coûts dans le portail Azure.
author: SnehaGunda
ms.author: sngun
ms.custom: subject-cost-optimization
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/19/2020
ms.openlocfilehash: 278603887fe7d47b4be52b04f9f0864be1a1b75b
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/23/2020
ms.locfileid: "92482245"
---
# <a name="plan-and-manage-costs-for-azure-cosmos-db"></a>Planifier et gérer les coûts pour Azure Cosmos DB

Cet article explique comment planifier et gérer les coûts pour Azure Cosmos DB.

- estimer le coût des ressources avant de les créer ;
- passer en revue les coûts estimés quand vous commencez à utiliser vos ressources ;
- utilisez les fonctionnalités de gestion des coûts pour établir des budgets et surveiller les coûts ;
- passer en revue les coûts attendus et identifier les tendances des dépenses afin de mettre en évidence les domaines dans lesquels vous pourriez agir.

Il est important de comprendre que les coûts pour Azure Cosmos DB ne représentent qu’une partie des coûts mensuels sur votre facture Azure. Si vous utilisez d’autres services Azure, vous êtes facturé pour tous les services et ressources Azure utilisés dans votre abonnement Azure, y compris les services tiers. Cet article explique comment planifier et gérer les coûts pour Azure Cosmos DB. Une fois que vous êtes familiarisé avec la gestion des coûts pour Azure Cosmos DB, vous pouvez appliquer des méthodes similaires pour gérer les coûts de tous les services Azure utilisés dans votre abonnement.

## <a name="prerequisites"></a>Prérequis

L’analyse des coûts prend en charge différents types de comptes Azure. Pour accéder à la liste complète des types de comptes pris en charge, voir [Comprendre les données de Cost Management](../cost-management-billing/costs/understand-cost-mgt-data.md). Pour afficher les données de coût, vous avez au minimum besoin d’un accès en lecture pour votre compte Azure. Pour plus d’informations sur l’attribution de l’accès aux données Azure Cost Management, consultez [Assigner l’accès aux données](../cost-management-billing/costs/assign-access-acm-data.md).

## <a name="provisioned-throughput-or-serverless"></a>Mode débit approvisionné ou serverless

Azure Cosmos DB prend en charge deux types de modes de capacité : [débit approvisionné](set-throughput.md) et [serverless](serverless.md). La façon dont vous êtes facturé pour votre utilisation d’Azure Cosmos DB variant beaucoup entre ces deux modes, il est important de choisir celui qui est le mieux adapté à votre charge de travail. Pour obtenir des conseils et des recommandations en lien avec ce choix, consultez l’article [Comment choisir entre le mode débit approvisionné et le mode serverless](throughput-serverless.md).

## <a name="estimating-provisioned-throughput-costs-with-capacity-calculator"></a>Estimation des coûts du mode débit approvisionné avec la calculatrice de capacité

Si vous envisagez d’utiliser Azure Cosmos DB en mode débit approvisionné, utilisez la [calculatrice de capacité d’Azure Cosmos DB](https://cosmos.azure.com/capacitycalculator/) pour estimer les coûts avant de créer les ressources dans un compte Azure Cosmos. La calculatrice de capacité permet d’estimer le débit nécessaire et le coût de votre charge de travail. Pour optimiser les coûts et les performances, il est essentiel de configurer vos bases de données et vos conteneurs Azure Cosmos avec la quantité appropriée de débit provisionné, ou [Unités de requête par seconde (RU/s)](request-units.md), pour votre charge de travail. Vous devez entrer des informations comme le type d’API, le nombre de régions, la taille des éléments, les demandes de lecture/écriture par seconde et la quantité totale des données stockées pour obtenir une estimation des coûts. Pour découvrir plus d’informations sur la calculatrice de capacité, consultez l’article [Estimer](estimate-ru-with-capacity-planner.md).

La capture d’écran suivante montre le débit et l’estimation des coûts à l’aide de la calculatrice de capacité :

:::image type="content" source="./media/plan-manage-costs/capacity-calculator-cost-estimate.png" alt-text="Estimation des coûts dans la calculatrice de capacité d’Azure Cosmos DB":::

## <a name="estimating-serverless-costs"></a>Estimation des coûts du mode serverless

Si vous envisagez d’utiliser Azure Cosmos DB en mode serverless, vous devez estimer le nombre d’[unités de requête](request-units.md) et les Go de stockage que vous pouvez consommer sur une base mensuelle. Vous pouvez estimer la quantité requise d’unités de requête en évaluant le nombre d’opérations de base de données qui seraient émises par mois, puis multiplier cette quantité par le coût par RU. Le tableau suivant répertorie les frais de RU estimés pour les opérations de base de données courantes :

| Opération | Coût estimé | Notes |
| --- | --- | --- |
| Créer un élément | 5 unités de requête | Coût moyen pour un élément de 1 Ko avec moins de 5 propriétés à indexer |
| Mettre à jour un élément | 10 unités de requête | Coût moyen pour un élément de 1 Ko avec moins de 5 propriétés à indexer |
| Lire un élément individuel par son ID et sa clé de partition (lecture ponctuelle) | 1 unité de requête | Coût moyen pour un élément de 1 Ko |
| Supprimer un élément | 5 unités de requête | |
| Exécuter une requête | 10 unités de requête | Coût moyen d’une requête tirant pleinement parti de l’[indexation](index-overview.md) et retournant 100 résultats ou moins |

> [!IMPORTANT] 
> Soyez attentif aux Notes dans le tableau ci-dessus. Pour une estimation plus précise des coûts réels de vos opérations, vous pouvez utiliser l’[émulateur Azure Cosmos](local-emulator.md) et [mesurer le coût de RU exact de vos opérations](find-request-unit-charge.md). Bien que l’émulateur Azure Cosmos ne prenne pas en charge le mode sans serveur, il rapporte un tarif de RU standard pour les opérations de base de données, et peut être utilisé pour cette estimation.

Une fois que vous avez calculé le nombre total d’unités de requête et les Go de stockage que vous êtes susceptible de consommer sur un mois, la formule suivante retourne votre estimation de coût : **([Nombre d’unités de requête] / 1 000 000 * 0,25 USD) + ([Go de stockage] * 0,25 USD)** .

> [!NOTE]
> Les coûts indiqués dans l’exemple précédent sont fournis à des fins de démonstration uniquement. Pour obtenir les informations les plus récentes sur la tarification, consultez la [page de tarification](https://azure.microsoft.com/pricing/details/cosmos-db/).

## <a name="review-estimated-costs-from-the-azure-portal"></a>Passer en revue les coûts estimés dans le portail Azure

Quand vous commencez à utiliser des ressources Azure Cosmos DB du portail Azure, vous pouvez voir les coûts estimés. Procédez comme suit pour passer en revue les estimations des coûts :

1. Connectez-vous au portail Azure et accédez à votre compte Azure Cosmos.
1. Accédez à la section **Vue d’ensemble** .
1. Consultez le graphique **Coût** en bas. Ce graphique montre une estimation du coût actuel sur une période configurable :
1. Créez un conteneur, comme un conteneur de graphiques.
1. Entrez le débit nécessaire pour votre charge de travail, par exemple, 400 RU/s. Une fois que vous avez entré la valeur du débit, vous pouvez voir l’estimation de prix comme le montre la capture d’écran suivante :

   :::image type="content" source="./media/plan-manage-costs/cost-estimate-portal.png" alt-text="Estimation des coûts dans la calculatrice de capacité d’Azure Cosmos DB":::

## <a name="use-budgets-and-cost-alerts"></a>Utiliser des budgets et des alertes de coût

Vous pouvez créer des [budgets](../cost-management-billing/costs/tutorial-acm-create-budgets.md) pour gérer les coûts, et des alertes permettant d’avertir automatiquement les parties prenantes en cas d’anomalies de dépenses et de risques de dépenses excessives. Les alertes sont basées sur les dépenses par rapport aux seuils de budget et de coût. Les budgets et les alertes sont créés pour les abonnements et les groupes de ressources Azure : ils sont donc utiles dans le cadre d’une stratégie globale de supervision des coûts. Ils peuvent cependant avoir des fonctionnalités limitées pour gérer les coûts de services Azure individuels, comme le coût d’Azure Cosmos DB, car ils sont conçus pour suivre les coûts à un niveau supérieur.

Si votre abonnement Azure a une limite de dépense, Azure vous empêche de dépenser au-delà que le montant de votre crédit. À mesure que vous créez et utilisez des ressources Azure, vos crédits sont utilisés. Quand vous atteignez votre limite de crédit, les ressources que vous avez déployées sont désactivées pour le reste de cette période de facturation. Vous ne pouvez pas changer votre limite de crédit, mais vous pouvez la supprimer. Pour plus d’informations sur les limites de dépense, consultez [Limite de dépense Azure](../cost-management-billing/manage/spending-limit.md).

## <a name="monitor-costs"></a>Superviser les coûts

À mesure que vous utilisez des ressources avec Azure Cosmos DB, vous générez des coûts. Les coûts unitaires d’utilisation des ressources varient selon les intervalles de temps (secondes, minutes, heures et jours) ou selon l’utilisation d’unités de requête. Dès que vous commencez à utiliser Azure Cosmos DB, des coûts sont générés, que vous pouvez voir dans le volet [Analyse des coûts](../cost-management-billing/costs/quick-acm-cost-analysis.md) du portail Azure.

Quand vous utilisez l’analyse des coûts, vous pouvez voir les coûts d’Azure Cosmos DB dans des graphes et des tableaux pour différents intervalles de temps. Voici quelques exemples montrant les coûts par jour, actuels, pour le mois précédent et pour l’année. Vous pouvez également voir les coûts par rapport aux budgets et aux coûts prévus. Passez à des vues pour des périodes plus longues pour identifier les tendances des dépenses et déterminer où des dépassements ont pu se produire. Si vous avez créé des budgets, vous pouvez également voir facilement où ils ont été dépassés. Pour voir les coûts d’Azure Cosmos DB dans l’analyse des coûts :

1. Connectez-vous au [portail Azure](https://portal.azure.com).

1. Ouvrez la fenêtre **Gestion des coûts + Facturation** , sélectionnez **Gestion des coûts** dans le menu, puis sélectionnez **Analyse des coûts** . Vous pouvez ensuite changer l’étendue pour un abonnement spécifique dans la liste déroulante **Étendue** .

1. Par défaut, le coût de tous les services est affiché dans le premier graphique en anneau. Dans le graphique, sélectionnez la zone intitulée « Azure Cosmos DB ».

1. Pour limiter les coûts à un seul service, par exemple Azure Cosmos DB, sélectionnez **Ajouter un filtre** , puis sélectionnez **Nom du service** . Choisissez ensuite **Azure Cosmos DB** dans la liste. Voici un exemple montrant les coûts seulement pour Azure Cosmos DB :
 
   :::image type="content" source="./media/plan-manage-costs/cost-analysis-pane.png" alt-text="Estimation des coûts dans la calculatrice de capacité d’Azure Cosmos DB":::

Dans l’exemple précédent, vous voyez le coût actuel d’Azure Cosmos DB pour le mois de février. Les graphiques contiennent également les coûts d’Azure Cosmos DB par emplacement et par groupe de ressources.

## <a name="next-steps"></a>Étapes suivantes

Consultez les articles suivants pour en savoir plus sur le fonctionnement des prix dans Azure Cosmos DB :

* [Modèle de prix dans Azure Cosmos DB](how-pricing-works.md)
* [Optimiser le coût du débit approvisionné dans Azure Cosmos DB](optimize-cost-throughput.md)
* [Optimiser le coût de requête dans Azure Cosmos DB](./optimize-cost-reads-writes.md)
* [Optimiser les coûts de stockage dans Azure Cosmos DB](optimize-cost-storage.md)