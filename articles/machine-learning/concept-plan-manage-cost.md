---
title: Planifier la gestion des coûts
titleSuffix: Azure Machine Learning
description: Planifiez et gérez les coûts d’Azure Machine Learning avec l’analyse des coûts dans le portail Azure. Découvrez d’autres conseils permettant de réduire vos coûts lors de la création de modèles ML.
author: sdgilley
ms.author: sgilley
ms.custom: subject-cost-optimization, devx-track-azurecli
ms.reviewer: nigup
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 06/08/2021
ms.openlocfilehash: 01c985b0554fe5955010c1c8c286f81f8de6d3ee
ms.sourcegitcommit: 190658142b592db528c631a672fdde4692872fd8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/11/2021
ms.locfileid: "112006002"
---
# <a name="plan-to-manage-costs-for-azure-machine-learning"></a>Planifier la gestion des coûts d’Azure Machine Learning

Cet article explique comment planifier et gérer les coûts d’Azure Machine Learning. Tout d’abord, vous devez utiliser la calculatrice de prix Azure pour planifier les coûts avant d’ajouter des ressources. Ensuite, lorsque vous ajoutez les ressources Azure, passez en revue les coûts estimés. 

Une fois que vous avez commencé à utiliser des ressources Azure Machine Learning, utilisez les fonctionnalités de gestion des coûts pour définir des budgets et superviser les coûts. Passez également en revue les coûts prévus et déterminez les tendances des dépenses pour identifier les domaines où vous pourriez agir.

Il est important de comprendre que les coûts pour Azure Machine Learning ne représentent qu’une partie des coûts mensuels sur votre facture Azure. Si vous utilisez d’autres services Azure, tous les services et ressources Azure utilisés dans votre abonnement Azure vous sont facturés, y compris les services tiers. Cet article explique comment planifier et gérer les coûts d’Azure Machine Learning. Quand vous connaîtrez bien la gestion des coûts d’Azure Machine Learning, vous pourrez appliquer des méthodes similaires pour gérer les coûts de tous les services Azure utilisés dans votre abonnement.

Pour plus d’informations sur l’optimisation des coûts, consultez [Gérer et optimiser les coûts dans Azure Machine Learning](how-to-manage-optimize-cost.md).

## <a name="prerequisites"></a>Prérequis

Analyse des coûts dans Cost Management prend en charge la plupart des types de compte Azure, mais pas tous. Pour accéder à la liste complète des types de comptes pris en charge, voir [Comprendre les données de Cost Management](../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn). 

Pour afficher les données de coût, vous avez au minimum besoin d’un accès en lecture pour un compte Azure. Pour plus d’informations sur l’attribution de l’accès aux données Azure Cost Management, consultez [Assigner l’accès aux données](../cost-management-billing/costs/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="estimate-costs-before-using-azure-machine-learning"></a>Estimer les coûts avant d’utiliser Azure Machine Learning

- Utilisez la [calculatrice de prix Azure](https://azure.microsoft.com/pricing/calculator/) pour estimer les coûts avant de créer les ressources dans un espace de travail Azure Machine Learning.
Sur la gauche, sélectionnez **AI + Machine Learning**, puis sélectionnez **Azure Machine Learning** pour commencer.  

La capture d’écran suivante montre l’estimation des coûts effectuée par la calculatrice :

:::image type="content" source="media/concept-plan-manage-cost/capacity-calculator-cost-estimate.png" alt-text="Exemple d’estimation de coûts dans la calculatrice de prix Azure.":::

À mesure que vous ajoutez de nouvelles ressources dans votre espace de travail, revenez à cette calculatrice et ajoutez la même ressource ici pour mettre à jour vos estimations de coûts.

Pour plus d’informations, consultez [Tarifs Azure Machine Learning](https://azure.microsoft.com/pricing/details/machine-learning?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="understand-the-full-billing-model-for-azure-machine-learning"></a>Comprendre le modèle de facturation complet pour Azure Machine Learning

Azure Machine Learning s’exécute sur l’infrastructure Azure qui accumule les coûts avec Azure Machine Learning lorsque vous déployez la nouvelle ressource. Il est important de comprendre qu’une infrastructure supplémentaire peut accumuler des frais. Vous devez gérer ce coût lorsque vous apportez des modifications aux ressources déployées. 






### <a name="costs-that-typically-accrue-with-azure-machine-learning"></a>Coûts qui s’accumulent généralement avec Azure Machine Learning

Lorsque vous créez des ressources pour un espace de travail Azure Machine Learning, des ressources pour d’autres services Azure sont également créées. Il s'agit des éléments suivants :

* Compte de base [Azure Container Registry](https://azure.microsoft.com/pricing/details/container-registry?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
* [Stockage d'objets blob de blocs Azure](https://azure.microsoft.com/pricing/details/storage/blobs?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) (Usage général v1)
* [Key Vault](https://azure.microsoft.com/pricing/details/key-vault?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
* [Application Insights](https://azure.microsoft.com/en-us/pricing/details/monitor?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
 
### <a name="costs-might-accrue-after-resource-deletion"></a>Coûts qui peuvent s’accumuler après la suppression de la ressource

Quand vous supprimez un espace de travail Azure Machine Learning dans le portail Azure ou avec l’interface de ligne de commande Azure, les ressources suivantes continuent d’exister. Les coûts continueront de s’accumuler jusqu’à ce que vous supprimiez ces ressources.

* Azure Container Registry
* Stockage d'objets blob de blocs Azure
* Key Vault
* Application Insights

Pour supprimer l’espace de travail ainsi que ces ressources dépendantes, utilisez le Kit de développement logiciel (SDK) :

```python
ws.delete(delete_dependent_resources=True)
```

Si vous créez le service Azure Kubernetes (AKS) dans votre espace de travail, ou si vous attachez des ressources de calcul à votre espace de travail, vous devez les supprimer séparément dans le [portail Azure](https://portal.azure.com).

### <a name="using-azure-prepayment-credit-with-azure-machine-learning"></a>Utilisation du crédit Paiement anticipé Azure avec Azure Machine Learning

Vous pouvez payer les frais d’Azure Machine Learning avec votre crédit de paiement anticipé Azure. Vous ne pouvez cependant pas utiliser le crédit Paiement anticipé Azure pour payer des frais pour des produits et services tiers, notamment ceux de la Place de marché Azure.

## <a name="review-estimated-costs-in-the-azure-portal"></a>Vérifiez l’estimation du coût dans le portail Azure

<!-- Note for Azure service writer: If your service shows estimated costs when a user is creating resources in the Azure portal, at a minimum, insert this section as a brief walkthrough that steps through creating a Azure Machine Learning resource where the estimated cost is shown to the user, updated for your service. Add a screenshot where the estimated costs or subscription credits are shown.

If your service doesn't show costs as they create a resource or if estimated costs aren't shown to users before they use your service, then omit this section.

For example, you might start with the following (modify for your service):
-->

Vous voyez une estimation des coûts à mesure que vous créez des ressources de calcul pour Azure Machine Learning.

Pour créer une *instance de calcul * et voir le prix estimé :

1. Connectez-vous au [studio Azure Machine Learning](https://ml.azure.com).
1. Sur la gauche, sélectionnez **Calcul**.
1. Dans la barre d’outils supérieure, sélectionnez **+ Nouveau**.
1. Consultez l’estimation du prix indiqué pour chaque taille de machine virtuelle disponible.
1. Finalisez la création de la ressource.


:::image type="content" source="media/concept-plan-manage-cost/create-resource.png" alt-text="Exemple d’estimation des coûts durant la création d’une instance de calcul." lightbox="media/concept-plan-manage-cost/create-resource.png" :::

Si votre abonnement Azure a une limite de dépense, Azure vous empêche de dépenser au-delà que le montant de votre crédit. À mesure que vous créez et utilisez des ressources Azure, vos crédits sont utilisés. Quand vous atteignez votre limite de crédit, les ressources que vous avez déployées sont désactivées pour le reste de cette période de facturation. Vous ne pouvez pas changer votre limite de crédit, mais vous pouvez la supprimer. Pour plus d’informations sur les limites de dépense, consultez [Limite de dépense Azure](../cost-management-billing/manage/spending-limit.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="monitor-costs"></a>Superviser les coûts

À mesure que vous utilisez des ressources Azure avec Azure Machine Learning, vous générez des coûts. Les coûts unitaires d’utilisation des ressources Azure varient selon les intervalles de temps (secondes, minutes, heures et jours) et selon l’utilisation d’unités (octets, mégaoctets, etc.). Dès que l’utilisation d’Azure Machine Learning démarre, des coûts sont générés, que vous pouvez voir dans l’[analyse du coût](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

Quand vous utilisez l’analyse du coût, vous pouvez voir les coûts d’Azure Machine Learning dans des graphes et des tableaux pour différents intervalles de temps. Par exemple, par jour, par année ou pour le mois en cours ou le mois précédent. Vous pouvez aussi afficher les coûts par rapport aux budgets et aux coûts prévus. Passez à des vues pour des périodes plus longues pour identifier les tendances des dépenses. Ceci vous permet de voir où des dépassements ont pu se produire. Si vous avez créé des budgets, vous pouvez aussi facilement voir à quel moment ils ont été dépassés.

Pour voir les coûts d’Azure Machine Learning dans l’analyse du coût :

1. Connectez-vous au portail Azure.
2. Ouvrez l’étendue dans le portail Azure et sélectionnez **Analyse des coûts** dans le menu. Par exemple, accédez à **Abonnements**, sélectionnez un abonnement dans la liste, puis sélectionnez **Analyse du coût** dans le menu. Sélectionnez **Étendue** pour passer à une autre étendue dans l’analyse des coûts.
3. Par défaut, le coût des services est affiché dans le premier graphique en anneau. Dans le graphique, sélectionnez la zone intitulée Azure Machine Learning.

Les coûts mensuels réels s’affichent lors de l’ouverture initiale de l’analyse des coûts. Voici un exemple qui montre tous les coûts d’utilisation mensuelle.

:::image type="content" source="media/concept-plan-manage-cost/all-costs.png" alt-text="Exemple montrant les coûts cumulés pour un abonnement." lightbox="media/concept-plan-manage-cost/all-costs.png" :::


Pour limiter les coûts à un seul service, par exemple Azure Machine Learning, sélectionnez **Ajouter un filtre**, puis **Nom du service**. Sélectionnez ensuite **Azure Machine Learning**.

Voici un exemple montrant les coûts d’Azure Machine Learning uniquement.

:::image type="content" source="media/concept-plan-manage-cost/vm-specific-cost.png" alt-text="Exemple montrant les coûts cumulés pour Nom du service." lightbox="media/concept-plan-manage-cost/vm-specific-cost.png" :::

<!-- Note to Azure service writer: The image shows an example for Azure Storage. Replace the example image with one that shows costs for your service. -->

Dans l’exemple précédent, vous avez pu voir le coût actuel du service. Les coûts par région Azure (localisations) et les coûts d’Azure Machine Learning par groupe de ressources sont également présentés. À partir de là, vous pouvez explorer les coûts par vous-même.
## <a name="create-budgets"></a>Créer des budgets

Vous pouvez créer des [budgets](../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) pour gérer les coûts et des [alertes](../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) permettant d’avertir automatiquement des parties prenantes en cas d’anomalies de dépenses et de risques de dépenses excessives. Les alertes sont basées sur les dépenses par rapport aux seuils de budget et de coût. Les budgets et les alertes sont créés pour les abonnements et les groupes de ressources Azure : ils sont donc utiles dans le cadre d’une stratégie globale de supervision des coûts. 

Vous pouvez créer des budgets avec des filtres pour des ressources ou des services spécifiques dans Azure si vous souhaitez disposer d’une plus grande granularité dans votre analyse. Les filtres vous permettent de vous assurer que vous ne créez pas accidentellement de nouvelles ressources entraînant un surcoût. Pour plus d’informations sur les options de filtre lorsque vous créez un budget, consultez [Options de regroupement et de filtre](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="export-cost-data"></a>Exporter des données de coûts

Vous pouvez également [exporter vos données de coûts](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) vers un compte de stockage. C’est utile quand vous ou d’autres personnes avez besoin d’effectuer des analyses supplémentaires des données concernant les coûts. Par exemple, une équipe Finance peut analyser les données avec Excel ou Power BI. Vous pouvez exporter vos coûts selon une planification quotidienne, hebdomadaire ou mensuelle, et définir une plage de dates personnalisée. L’exportation des données des coûts est la méthode recommandée pour récupérer les jeux de données des coûts.

## <a name="other-ways-to-manage-and-reduce-costs-for-azure-machine-learning"></a>Autres façons de gérer et de réduire les coûts d’Azure Machine Learning

Utilisez les conseils suivants pour vous aider à gérer et à optimiser les coûts de vos ressources de calcul.

- Configurer vos clusters d’entraînement pour la mise à l’échelle automatique
- Définir des quotas sur votre abonnement et vos espaces de travail
- Définir des stratégies de résiliation sur votre exécution d’entraînement
- Utiliser des machines virtuelles de basse priorité
- Utiliser une instance de machine virtuelle réservée Azure
- Entraîner localement
- Paralléliser l’entraînement
- Définir des stratégies de conservation et de suppression des données
- Déployer des ressources dans la même région

Pour plus d’informations, consultez [Gérer et optimiser les coûts dans Azure Machine Learning](how-to-manage-optimize-cost.md).

## <a name="next-steps"></a>Étapes suivantes

- [Gérer et optimiser les coûts dans Azure Machine Learning](how-to-manage-optimize-cost.md).
- [Gérer les budgets, les coûts et le quota pour Azure Machine Learning à l’échelle de l’organisation](/azure/cloud-adoption-framework/ready/azure-best-practices/optimize-ai-machine-learning-cost)
- Découvrez [comment optimiser votre investissement cloud avec Azure Cost Management](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Apprenez-en davantage sur la gestion des coûts avec l’[analyse du coût](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Découvrez comment [éviter des coûts imprévus](../cost-management-billing/understand/analyze-unexpected-charges.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Suivez le cours d’apprentissage guidé [Cost Management](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).