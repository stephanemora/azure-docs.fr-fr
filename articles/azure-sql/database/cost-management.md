---
title: Prévoir et gérer les coûts
description: Découvrez comment planifier et gérer les coûts pour Azure SQL Database avec l’analyse du coût dans le portail Azure.
author: MashaMSFT
ms.author: mathoma
ms.custom: subject-cost-optimization
ms.service: sql-database
ms.subservice: service-overview
ms.topic: how-to
ms.date: 01/15/2021
ms.openlocfilehash: e43d9b85bc50772ad75e974e11d0e7332e271b29
ms.sourcegitcommit: c385af80989f6555ef3dadc17117a78764f83963
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2021
ms.locfileid: "111408908"
---
# <a name="plan-and-manage-costs-for-azure-sql-database"></a>Planifier et gérer les coûts d’Azure SQL Database

Cet article explique comment planifier et gérer les coûts pour Azure SQL Database. Tout d’abord, utilisez la calculatrice de tarification Azure pour ajouter des ressources Azure et passez en revue les coûts estimés. Une fois que vous avez commencé à utiliser des ressources Azure SQL Database, utilisez les fonctionnalités de Cost Management pour définir des budgets et superviser les coûts. Vous pouvez également passer en revue les coûts prévus et déterminer les tendances des dépenses pour identifier les domaines où vous pourriez agir. Les coûts pour Azure SQL Database ne représentent qu’une partie des coûts mensuels sur votre facture Azure. Cet article explique comment planifier et gérer les coûts d’Azure SQL Database. Tous les services et ressources Azure utilisés pour votre abonnement Azure, y compris les services tiers, vous sont toutefois facturés.


## <a name="prerequisites"></a>Prérequis

L’analyse des coûts prend en charge la plupart des types de compte Azure, mais pas tous. Pour accéder à la liste complète des types de comptes pris en charge, voir [Comprendre les données de Cost Management](../../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn). Pour afficher les données de coût, vous avez au minimum besoin d’un accès en lecture pour un compte Azure. 

Pour plus d’informations sur l’attribution de l’accès aux données Azure Cost Management, consultez [Assigner l’accès aux données](../../cost-management-billing/costs/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).


## <a name="sql-database-initial-cost-considerations"></a>Considérations relatives au coût initial de SQL Database

Lorsque vous travaillez avec Azure SQL Database, vous devez prendre en compte plusieurs caractéristiques permettant de réaliser des économies :


### <a name="vcore-or-dtu-purchasing-models"></a>Modèles d’achat vCore ou DTU 

Azure SQL Database prend en charge deux modèles d’achat : vCore et DTU. La façon dont vous êtes facturé varie selon les modèles d’achat. Il est donc important de comprendre le modèle qui convient le mieux à votre charge de travail lorsque vous planifiez et considérez les coûts. Pour plus d’informations sur les modèles d’achat vCore et DTU, consultez [Choisir entre les modèles d’achat vCore et DTU](purchasing-models.md).


### <a name="provisioned-or-serverless"></a>Approvisionné ou serverless

Dans le modèle d’achat vCore, Azure SQL Database prend également en charge deux types de niveaux de calcul : débit approvisionné et serverless. La façon dont vous êtes facturé pour chaque niveau de calcul varie. Il est donc important de comprendre ce qui fonctionne le mieux pour votre charge de travail lorsque vous planifiez et considérez les coûts. Pour plus d’informations, consultez [Vue d’ensemble du modèle vCore – Niveaux de calcul](service-tiers-sql-database-vcore.md#compute-tiers).

Dans le niveau de calcul approvisionné du modèle d’achat vCore, vous pouvez échanger vos licences actuelles contre des tarifs réduits. Pour plus d’informations, consultez [Azure Hybrid Benefit (AHB)](../azure-hybrid-benefit.md).

### <a name="elastic-pools"></a>Pools élastiques

Pour les environnements comportant plusieurs bases de données dont les demandes d’utilisation sont variables et imprévisibles, les pools élastiques peuvent permettre de réaliser des économies par rapport à l’approvisionnement d’une même quantité de singletons. Pour plus d’informations, consultez [Pools élastiques](elastic-pool-overview.md).

## <a name="estimate-azure-sql-database-costs"></a>Estimation des coûts d’Azure SQL Database

Utilisez la [calculatrice de tarification Azure](https://azure.microsoft.com/pricing/calculator/) pour estimer les coûts des différentes configurations d’Azure SQL Database. Les informations et les prix figurant dans l’image suivante sont donnés à titre d’exemple uniquement :

:::image type="content" source="media/cost-management/pricing-calc.png" alt-text="Exemple de calculatrice de tarification Azure SQL Database":::

Vous pouvez également estimer l’incidence des différentes options de stratégie de rétention sur le coût. Les informations et les prix figurant dans l’image suivante sont donnés à titre d’exemple uniquement :

:::image type="content" source="media/cost-management/backup-storage.png" alt-text="Exemple de calculatrice de tarification Azure SQL Database pour le stockage":::


## <a name="understand-the-full-billing-model-for-azure-sql-database"></a>Comprendre le modèle de facturation complet pour Azure SQL Database

Azure SQL Database s’exécute sur l’infrastructure Azure qui accumule les coûts avec Azure SQL Database lorsque vous déployez la nouvelle ressource. Il est important de comprendre qu’une infrastructure supplémentaire peut accumuler des frais. Vous devez gérer ce coût lorsque vous apportez des modifications aux ressources déployées. 


Azure SQL Database (à l’exception du niveau de calcul serverless) est facturé à un taux horaire prévisible. Si la base de données SQL est active pendant moins d’une heure, vous êtes facturé pour chaque heure où la base de données existe en utilisant le niveau de service le plus élevé qui a été sélectionné et le stockage et les E/S approvisionnés qui sont appliqués pendant cette heure, indépendamment de l’utilisation ou du fait que la base de données était active pendant moins d’une heure.


### <a name="using-monetary-credit-with-azure-sql-database"></a>Utilisation de Monetary Credit avec Azure SQL Database

Vous pouvez payer les frais Azure SQL Database avec votre crédit Paiement anticipé Azure (anciennement appelé « engagement financier »). Vous ne pouvez cependant pas utiliser le crédit Paiement anticipé Azure pour payer des frais pour des produits et services tiers, notamment ceux de Place de marché Azure.

## <a name="review-estimated-costs-in-the-azure-portal"></a>Vérifiez l’estimation du coût dans le portail Azure

Au cours du processus de création d’une base de données Azure SQL, vous pouvez voir les coûts estimés lors de la configuration du niveau de calcul. 

Pour accéder à cet écran, sélectionnez **Configurer la base de données** sous l’onglet **Concepts de base** de la page **Créer une base de données SQL**. Les informations et les prix figurant dans l’image suivante sont donnés à titre d’exemple uniquement :

  :::image type="content" source="media/cost-management/cost-estimate.png" alt-text="Exemple d’estimation des coûts dans le portail Azure":::



Si votre abonnement Azure a une limite de dépense, Azure vous empêche de dépenser au-delà que le montant de votre crédit. À mesure que vous créez et utilisez des ressources Azure, vos crédits sont utilisés. Quand vous atteignez votre limite de crédit, les ressources que vous avez déployées sont désactivées pour le reste de cette période de facturation. Vous ne pouvez pas changer votre limite de crédit, mais vous pouvez la supprimer. Pour plus d’informations sur les limites de dépense, consultez [Limite de dépense Azure](../../cost-management-billing/manage/spending-limit.md).

## <a name="monitor-costs"></a>Superviser les coûts

Lorsque vous commencez à utiliser Azure SQL Database, vous pouvez voir les coûts estimés dans le portail. Procédez comme suit pour passer en revue les estimations des coûts :

1. Connectez-vous au portail Azure et accédez au groupe de ressources de votre base de données Azure SQL. Vous pouvez localiser le groupe de ressources en accédant à votre base de données et en sélectionnant **Groupe de ressources** dans la section **Vue d’ensemble**.
1. Dans le menu, sélectionnez **Analyse des coûts**.
1. Affichez **Coûts cumulés** et définissez le graphique en bas sous **Nom de service**. Ce graphique montre une estimation de vos coûts actuels pour SQL Database. Pour réduire les coûts de la page entière à Azure SQL Database, sélectionnez **Ajouter un filtre** puis sélectionnez **Azure SQL Database**. Les informations et les prix figurant dans l’image suivante sont donnés à titre d’exemple uniquement :

   :::image type="content" source="media/cost-management/cost-analysis.png" alt-text="Exemple montrant les coûts cumulés pour le portail Azure":::

À partir de là, vous pouvez explorer les coûts par vous-même. Pour plus d’informations sur les différents paramètres d’analyse des coûts, consultez [Démarrer l’analyse des coûts](../../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="create-budgets"></a>Créer des budgets

<!-- Note to Azure service writer: Modify the following as needed for your service. -->

Vous pouvez créer des [budgets](../../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) pour gérer les coûts et des [alertes](../../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) permettant d’avertir automatiquement des parties prenantes en cas d’anomalies de dépenses et de risques de dépenses excessives. Les alertes sont basées sur les dépenses par rapport aux seuils de budget et de coût. Les budgets et les alertes sont créés pour les abonnements et les groupes de ressources Azure : ils sont donc utiles dans le cadre d’une stratégie globale de supervision des coûts. 

Vous pouvez créer des budgets avec des filtres pour des ressources ou des services spécifiques dans Azure si vous souhaitez disposer d’une plus grande granularité dans votre analyse. Les filtres vous permettent de vous assurer que vous ne créez pas accidentellement de nouvelles ressources entraînant un surcoût. Pour plus d’informations sur les options de filtre lorsque vous créez un budget, consultez [Options de regroupement et de filtre](../../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="export-cost-data"></a>Exporter des données de coûts

Vous pouvez également [exporter vos données de coûts](../../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) vers un compte de stockage. C’est utile quand vous ou d’autres personnes avez besoin d’effectuer des analyses supplémentaires des données concernant les coûts. Par exemple, une équipe Finance peut analyser les données avec Excel ou Power BI. Vous pouvez exporter vos coûts selon une planification quotidienne, hebdomadaire ou mensuelle, et définir une plage de dates personnalisée. L’exportation des données des coûts est la méthode recommandée pour récupérer les jeux de données des coûts.


## <a name="other-ways-to-manage-and-reduce-costs-for-azure-sql-database"></a>Autres façons de gérer et de réduire les coûts d’Azure SQL Database

Azure SQL Database vous permet également d’effectuer un scale-up ou un scale-down des ressources pour contrôler les coûts en fonction des besoins de votre application. Pour plus d’informations, consultez [Mettre à l’échelle de façon dynamique les ressources de base de données](scale-resources.md).

Économisez de l’argent en vous engageant à réserver des ressources de calcul pendant un à trois ans. Pour plus d’informations, consultez [Réduire les coûts des ressources grâce à une capacité de réserve](reserved-capacity-overview.md).


## <a name="next-steps"></a>Étapes suivantes

- Découvrez [comment optimiser votre investissement cloud avec Azure Cost Management](../../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Apprenez-en davantage sur la gestion des coûts avec l’[analyse du coût](../../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Découvrez comment [éviter des coûts imprévus](../../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Suivez le cours d’apprentissage guidé [Cost Management](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).