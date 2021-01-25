---
title: Planifier la gestion des coûts pour Azure Cognitive Services
description: Découvrez comment planifier et gérer les coûts pour Azure Cognitive Services avec l’analyse du coût dans le portail Azure.
author: erhopf
ms.author: erhopf
ms.custom: subject-cost-optimization
ms.service: cognitive-services
ms.topic: how-to
ms.date: 12/15/2020
ms.openlocfilehash: 925a092eb67fa459213a37df0fc6b3f7a1b8a0fb
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/20/2021
ms.locfileid: "98602356"
---
# <a name="plan-and-manage-costs-for-azure-cognitive-services"></a>Planifier et gérer les coûts pour Azure Cognitive Services

Cet article explique comment planifier et gérer les coûts pour Azure Cognitive Services. Vous devez d’abord utiliser la calculatrice de prix Azure pour planifier les coûts Cognitive Services avant d’ajouter des ressources pour le service aux coûts estimés. Ensuite, lorsque vous ajoutez les ressources Azure, passez en revue les coûts estimés. Une fois que vous avez commencé à utiliser des ressources Cognitive Services (par exemple, Speech, Vision par ordinateur, LUIS, Analyse de texte, Translator, etc.), utilisez les fonctionnalités de Cost Management pour définir des budgets et surveiller les coûts. Vous pouvez également passer en revue les coûts prévus et déterminer les tendances des dépenses pour identifier les domaines où vous pourriez agir. Les coûts pour Cognitive Services ne représentent qu’une partie des coûts mensuels sur votre facture Azure. Cet article explique comment planifier et gérer les coûts pour Cognitive Services. Cependant, vous êtes facturé pour tous les services et ressources Azure utilisés pour votre abonnement Azure, y compris les services tiers.

## <a name="prerequisites"></a>Prérequis

Analyse des coûts dans Cost Management prend en charge la plupart des types de compte Azure, mais pas tous. Pour accéder à la liste complète des types de comptes pris en charge, voir [Comprendre les données de Cost Management](../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn). Pour afficher les données de coût, vous avez au minimum besoin d’un accès en lecture pour un compte Azure. Pour plus d’informations sur l’attribution de l’accès aux données Azure Cost Management, consultez [Assigner l’accès aux données](../cost-management/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

<!--Note for Azure service writer: If you have other prerequisites for your service, insert them here -->

## <a name="estimate-costs-before-using-cognitive-services"></a>Estimer les coûts avant d’utiliser Cognitive Services

Utilisez la [calculatrice de prix Azure](https://azure.microsoft.com/pricing/calculator/) pour estimer les coûts avant d’ajouter Cognitive Services.

:::image type="content" source="media/cognitive-services-pricing-calculator.png" alt-text="Calculatrice de prix Azure pour Cognitive Services" border="true":::

À mesure que vous ajoutez de nouvelles ressources dans votre espace de travail, revenez à cette calculatrice et ajoutez la même ressource ici pour mettre à jour vos estimations de coûts.

Pour plus d’informations, consultez [Tarifs Azure Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/).

## <a name="understand-the-full-billing-model-for-cognitive-services"></a>Comprendre l’ensemble du modèle de facturation pour Cognitive Services

Cognitive Services s’exécute sur l’infrastructure Azure qui [accumule les coûts](https://azure.microsoft.com/pricing/details/cognitive-services/) lorsque vous déployez la nouvelle ressource. Il est important de comprendre qu’une infrastructure supplémentaire peut accumuler des frais. Vous devez gérer ce coût lorsque vous apportez des modifications aux ressources déployées. 

### <a name="costs-that-typically-accrue-with-cognitive-services"></a>Coûts qui s’accumulent généralement avec Cognitive Services

En général, une fois que vous avez déployé une ressource Azure, les coûts sont déterminés par votre niveau tarifaire et les appels d’API que vous passez à votre point de terminaison. Si le service que vous utilisez a un niveau d’engagement, dépasser les appels alloués dans votre niveau peut entraîner des frais de dépassement.

Des coûts supplémentaires peuvent s’additionner lors de l’utilisation de ces services :

#### <a name="qna-maker"></a>QnA Maker

Lorsque vous créez des ressources pour QnA Maker, des ressources pour d’autres services Azure peuvent aussi être créées. Ils comprennent :

- [Azure App Service (pour l’exécution)](https://azure.microsoft.com/pricing/details/app-service/)
- [Recherche cognitive Azure (pour les données)](https://azure.microsoft.com/pricing/details/search/)
 
### <a name="costs-that-might-accrue-after-resource-deletion"></a>Coûts qui peuvent s’additionner après la suppression des ressources

#### <a name="qna-maker"></a>QnA Maker

Une fois que vous avez supprimé les ressources QnA Maker, les ressources suivantes risquent d’être toujours là. Les coûts continueront de s’accumuler jusqu’à ce que vous supprimiez ces ressources.

- [Azure App Service (pour l’exécution)](https://azure.microsoft.com/pricing/details/app-service/)
- [Recherche cognitive Azure (pour les données)](https://azure.microsoft.com/pricing/details/search/)

### <a name="using-azure-prepayment-credit-with-cognitive-services"></a>Utilisation du crédit Paiement anticipé Azure avec Cognitive Services

Vous pouvez payer les frais Cognitive Services avec votre crédit Paiement anticipé Azure (anciennement appelé engagement financier). Vous ne pouvez cependant pas utiliser le crédit Paiement anticipé Azure pour payer des frais pour des produits et services tiers, y compris ceux de la Place de marché Azure.

## <a name="create-budgets"></a>Créer des budgets

Vous pouvez créer des [budgets](../cost-management/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) pour gérer les coûts et des [alertes](../cost-management/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) permettant d’avertir automatiquement des parties prenantes en cas d’anomalies de dépenses et de risques de dépenses excessives. Les alertes sont basées sur les dépenses par rapport aux seuils de budget et de coût. Les budgets et les alertes sont créés pour les abonnements et les groupes de ressources Azure : ils sont donc utiles dans le cadre d’une stratégie globale de supervision des coûts. 

Vous pouvez créer des budgets avec des filtres pour des ressources ou des services spécifiques dans Azure si vous souhaitez disposer d’une plus grande granularité dans votre analyse. Les filtres vous permettent de vous assurer que vous ne créez pas accidentellement de nouvelles ressources entraînant un surcoût. Pour plus d’informations sur les options de filtre lorsque vous créez un budget, consultez [Options de regroupement et de filtre](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="export-cost-data"></a>Exporter des données de coûts

Vous pouvez également [exporter vos données de coûts](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) vers un compte de stockage. C’est utile quand vous ou d’autres personnes avez besoin d’effectuer des analyses supplémentaires des données concernant les coûts. Par exemple, les équipes financières peuvent analyser les données avec Excel ou Power BI. Vous pouvez exporter vos coûts selon une planification quotidienne, hebdomadaire ou mensuelle, et définir une plage de dates personnalisée. L’exportation des données des coûts est la méthode recommandée pour récupérer les jeux de données des coûts.

<!--
## Other ways to manage and reduce costs for Cognitive Services

Work with Dean to complete this section in 2021.

-->

## <a name="next-steps"></a>Étapes suivantes

- Découvrez [comment optimiser votre investissement cloud avec Azure Cost Management](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Apprenez-en davantage sur la gestion des coûts avec l’[analyse du coût](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Découvrez comment [éviter des coûts imprévus](../cost-management-billing/manage/getting-started.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Suivez le cours d’apprentissage guidé [Cost Management](https://docs.microsoft.com/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
