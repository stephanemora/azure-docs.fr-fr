---
title: Planifier la gestion des coûts pour Azure Synapse Analytics
description: Découvrez comment planifier et gérer les coûts pour Azure Synapse Analytics avec l’analyse du coût dans le portail Azure.
author: julieMSFT
ms.author: jrasnick
ms.custom: subject-cost-optimization
ms.service: synapse-analytics
ms.topic: how-to
ms.date: 12/09/2020
ms.openlocfilehash: e62d494989c86b4c0eab9cdbd51b68fc49fffe76
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/18/2020
ms.locfileid: "97681270"
---
# <a name="plan-and-manage-costs-for-azure-synapse-analytics"></a>Planifier et gérer les coûts pour Azure Synapse Analytics

Cet article explique comment planifier et gérer les coûts d’Azure Synapse Analytics. Vous devez d’abord utiliser la calculatrice de prix Azure pour planifier les coûts Azure Synapse avant d’ajouter des ressources pour le service aux coûts estimés. Ensuite, lorsque vous ajoutez les ressources Azure Synapse, passez en revue les coûts estimés.

Une fois que vous avez commencé à utiliser des ressources Azure Synapse, utilisez les fonctionnalités de Cost Management pour définir des budgets et superviser les coûts. Vous pouvez également passer en revue les coûts prévus et déterminer les tendances des dépenses pour identifier les domaines où vous pourriez agir. Les coûts pour Azure Synapse ne représentent qu’une partie des coûts mensuels sur votre facture Azure. Cet article explique comment planifier et gérer les coûts d’Azure Synapse. Tous les services et ressources Azure utilisés pour votre abonnement Azure, y compris les services tiers, vous sont toutefois facturés.

## <a name="prerequisites"></a>Prérequis

Analyse des coûts dans Cost Management prend en charge la plupart des types de compte Azure, mais pas tous. Pour accéder à la liste complète des types de comptes pris en charge, voir [Comprendre les données de Cost Management](https://docs.microsoft.com/azure/cost-management-billing/costs/understand-cost-mgt-data?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn). Pour afficher les données de coût, vous avez au minimum besoin d’un accès en lecture pour un compte Azure. Pour plus d’informations sur l’attribution de l’accès aux données Azure Cost Management, consultez [Assigner l’accès aux données](https://docs.microsoft.com/azure/cost-management/assign-access-acm-data?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="estimate-costs-before-using-azure-synapse-analytics"></a>Estimer les coûts avant d’utiliser Azure Synapse Analytics

Utilisez la [calculatrice de prix Azure](https://azure.microsoft.com/pricing/calculator/) pour estimer les coûts avant d’ajouter Azure Synapse Analytics.

Azure Synapse inclut différentes ressources associées à des options de facturation variées, comme indiqué dans l’estimation de coût ci-dessous. 

![Exemple d’estimation de coûts dans la calculatrice de prix Azure](./media/plan-manage-costs/cost-estimate.png)

## <a name="understand-the-full-billing-model-for-azure-synapse-analytics"></a>Comprendre le modèle de facturation complet pour Azure Synapse Analytics

Azure Synapse s’exécute sur l’infrastructure Azure qui accumule les coûts avec Azure Synapse lorsque vous déployez la nouvelle ressource. Il est important de comprendre qu’une infrastructure supplémentaire peut accumuler des frais. Vous devez gérer ce coût lorsque vous apportez des modifications aux ressources déployées. 

### <a name="costs-that-typically-accrue-with-azure-synapse-analytics"></a>Coûts qui s’accumulent généralement avec Azure Synapse Analytics

Lorsque vous créez des ressources pour Azure Synapse, des ressources pour d’autres services Azure sont également créées. Ils comprennent :

- Data Lake Storage Gen2

 ### <a name="costs-might-accrue-after-resource-deletion"></a>Coûts qui peuvent s’accumuler après la suppression de la ressource

Une fois que vous avez supprimé des ressources Azure Synapse, les ressources suivantes peuvent continuer à exister. Les coûts continueront de s’accumuler jusqu’à ce que vous supprimiez ces ressources.

- Data Lake Storage Gen2

### <a name="using-monetary-credit-with-azure-synapse"></a>Utilisation de Monetary Credit avec Azure Synapse 

Vous pouvez payer les frais d’Azure Synapse avec votre crédit d’engagement financier EA. Vous ne pouvez cependant pas utiliser le crédit d’engagement financier EA pour payer des frais pour des produits et services tiers, y compris ceux de la Place de marché Azure.

## <a name="review-estimated-costs-in-the-azure-portal"></a>Vérifiez l’estimation du coût dans le portail Azure

À mesure que vous créez des ressources pour Azure Synapse Analytics, vous pouvez consulter les coûts estimés. Un pool SQL serverless est créé avec les espaces de travail. Aucun frais n’est facturé pour celui-ci tant que vous n’exécutez pas de requêtes. D’autres ressources, telles que des pools SQL dédiés et des pools Apache Spark serverless, doivent être créées dans l’espace de travail.

Pour créer une ressource de type <ResourceName> et consulter le prix estimé :

1. Accédez au service dans le portail Azure.
2. Créez la ressource.
3. Consultez le prix estimé indiqué dans le résumé.
4. Finalisez la création de la ressource.

![Exemple d’estimation de coûts après la création d’une ressource](./media/plan-manage-costs/create-workspace-cost.png)


Si votre abonnement Azure a une limite de dépense, Azure vous empêche de dépenser au-delà que le montant de votre crédit. À mesure que vous créez et utilisez des ressources Azure, vos crédits sont utilisés. Quand vous atteignez votre limite de crédit, les ressources que vous avez déployées sont désactivées pour le reste de cette période de facturation. Vous ne pouvez pas changer votre limite de crédit, mais vous pouvez la supprimer. Pour plus d’informations sur les limites de dépense, consultez [Limite de dépense Azure](https://docs.microsoft.com/azure/billing/billing-spending-limit).

## <a name="monitor-costs"></a>Superviser les coûts

L’utilisation de ressources Azure Synapse entraîne la facturation de frais. Les coûts unitaires d’utilisation des ressources Azure varient selon les intervalles de temps (secondes, minutes, heures et jours) ou selon l’utilisation d’unités (octets, mégaoctets, etc.). Dès que vous commencez à utiliser des ressources dans Azure Synapse, des frais vous sont facturés. Vous pouvez les consulter dans l’[analyse des coûts](https://docs.microsoft.com/azure/cost-management/quick-acm-cost-analysis?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

Quand vous utilisez l’analyse des coûts, vous affichez les coûts d’Azure Synapse Analytics dans des graphiques et des tableaux pour différents intervalles de temps. Par exemple, par jour, par année ou pour le mois en cours ou le mois précédent. Vous pouvez aussi afficher les coûts par rapport aux budgets et aux coûts prévus. Passez à des vues pour des périodes plus longues pour identifier les tendances des dépenses. Ceci vous permet de voir où des dépassements ont pu se produire. Si vous avez créé des budgets, vous pouvez aussi facilement voir à quel moment ils ont été dépassés.

Pour consulter les coûts d’Azure Synapse dans l’analyse des coûts :

1. Connectez-vous au portail Azure.
2. Ouvrez l’étendue (abonnement ou groupe de ressources) dans le portail Azure, puis sélectionnez **Analyse des coûts** dans le menu. Par exemple, accédez à **Abonnements**, sélectionnez un abonnement dans la liste, puis sélectionnez **Analyse du coût** dans le menu. Sélectionnez **Étendue** pour passer à une autre étendue dans l’analyse des coûts.
3. Par défaut, le coût des services est affiché dans le premier graphique en anneau. Dans le graphique, sélectionnez la zone intitulée Azure Synapse.

Les coûts mensuels réels s’affichent lors de l’ouverture initiale de l’analyse des coûts. Voici un exemple qui montre tous les coûts d’utilisation mensuelle.

![Exemple montrant les coûts cumulés pour un abonnement](./media/plan-manage-costs/actual-monthly-costs.png)

- Pour limiter les coûts à un seul service, par exemple Azure Synapse, sélectionnez **Ajouter un filtre**, puis **Nom du service**. Sélectionnez ensuite **Azure Synapse Analytics**.

Voici un exemple montrant les coûts d’Azure Synapse uniquement.

![Exemple montrant les coûts cumulés pour Nom du service](./media/plan-manage-costs/filtered-monthly-costs.png)

Dans l’exemple précédent, vous avez pu voir le coût actuel du service. Les coûts par région Azure (emplacements) et les coûts Azure Synapse par groupe de ressources sont également présentés. À partir de là, vous pouvez explorer les coûts par vous-même.

## <a name="create-budgets"></a>Créer des budgets

Vous pouvez créer des [budgets](https://docs.microsoft.com/azure/cost-management/tutorial-acm-create-budgets?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) pour gérer les coûts et des [alertes](https://docs.microsoft.com/azure/cost-management/cost-mgt-alerts-monitor-usage-spending?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) permettant d’avertir automatiquement des parties prenantes en cas d’anomalies de dépenses et de risques de dépenses excessives. Les alertes sont basées sur les dépenses par rapport aux seuils de budget et de coût. Les budgets et les alertes sont créés pour les abonnements et les groupes de ressources Azure : ils sont donc utiles dans le cadre d’une stratégie globale de supervision des coûts. 

Vous pouvez créer des budgets avec des filtres pour des ressources ou des services spécifiques dans Azure si vous souhaitez disposer d’une plus grande granularité dans votre analyse. Les filtres vous permettent de vous assurer que vous ne créez pas accidentellement de nouvelles ressources entraînant un surcoût. Pour plus d’informations sur les options de filtre lorsque vous créez un budget, consultez [Options de regroupement et de filtre](https://docs.microsoft.com/azure/cost-management-billing/costs/group-filter?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="export-cost-data"></a>Exporter des données de coûts

Vous pouvez également [exporter vos données de coûts](https://docs.microsoft.com/azure/cost-management-billing/costs/tutorial-export-acm-data?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) vers un compte de stockage. C’est utile quand vous ou d’autres personnes avez besoin d’effectuer des analyses supplémentaires des données concernant les coûts. Par exemple, une équipe Finance peut analyser les données avec Excel ou Power BI. Vous pouvez exporter vos coûts selon une planification quotidienne, hebdomadaire ou mensuelle, et définir une plage de dates personnalisée. L’exportation des données des coûts est la méthode recommandée pour récupérer les jeux de données des coûts.


## <a name="other-ways-to-manage-and-reduce-costs-for-azure-synapse"></a>Autres façons de gérer et de réduire les coûts d’Azure Synapse 

### <a name="serverless-sql-pool"></a>Pool SQL serverless

Pour en savoir plus sur les coûts pour un pool SQL serverless, consultez [Gestion des coûts d’un pool SQL serverless dans Azure Synapse Analytics](./sql/data-processed.md)

### <a name="dedicated-sql-pool"></a>Pool SQL dédié

Vous pouvez contrôler les coûts d’un pool SQL dédié en suspendant la ressource lorsqu’elle n’est pas utilisée. Par exemple, si vous n’utilisez pas la base de données pendant la nuit et les week-ends, vous pouvez la suspendre à ces moments et la reprendre pendant la journée. Pour plus d’informations, consultez [Suspendre et reprendre le calcul dans le pool SQL dédié avec le portail Azure](./sql-data-warehouse/pause-and-resume-compute-portal.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

<!-- ### Serverless Apache Spark pool -->

### <a name="data-integration---pipelines-and-data-flows"></a>Intégration de données - Pipelines et flux de données 

Pour en savoir plus sur les coûts d’intégration des données, consultez [Planifier et gérer les coûts d’Azure Data Factory](../data-factory/plan-manage-costs.md).

## <a name="next-steps"></a>Étapes suivantes

- Découvrez [comment optimiser votre investissement cloud avec Azure Cost Management](https://docs.microsoft.com/azure/cost-management-billing/costs/cost-mgt-best-practices?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Apprenez-en davantage sur la gestion des coûts avec l’[analyse du coût](https://docs.microsoft.com/azure/cost-management-billing/costs/quick-acm-cost-analysis?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Découvrez comment [éviter des coûts imprévus](https://docs.microsoft.com/azure/cost-management-billing/manage/getting-started?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Suivez le cours d’apprentissage guidé [Cost Management](https://docs.microsoft.com/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Apprenez-en davantage sur la planification et la gestion des coûts pour [Azure Machine Learning](../machine-learning/concept-plan-manage-cost.md).