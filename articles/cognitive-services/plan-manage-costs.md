---
title: Planifier la gestion des coûts pour Azure Cognitive Services
description: Découvrez comment planifier et gérer les coûts pour Azure Cognitive Services avec l’analyse du coût dans le portail Azure.
author: erhopf
ms.author: erhopf
ms.custom: subject-cost-optimization
ms.service: cognitive-services
ms.topic: how-to
ms.date: 12/15/2020
ms.openlocfilehash: 661af89ad223978abbefd71dd8008577475c5875
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/31/2021
ms.locfileid: "99221371"
---
# <a name="plan-and-manage-costs-for-azure-cognitive-services"></a>Planifier et gérer les coûts pour Azure Cognitive Services

Cet article explique comment planifier et gérer les coûts pour Azure Cognitive Services. Vous devez d’abord utiliser la calculatrice de prix Azure pour planifier les coûts Cognitive Services avant d’ajouter des ressources pour le service aux coûts estimés. Ensuite, lorsque vous ajoutez les ressources Azure, passez en revue les coûts estimés. Une fois que vous avez commencé à utiliser des ressources Cognitive Services (par exemple, Speech, Vision par ordinateur, LUIS, Analyse de texte, Translator, etc.), utilisez les fonctionnalités de Cost Management pour définir des budgets et surveiller les coûts. Vous pouvez également passer en revue les coûts prévus et déterminer les tendances des dépenses pour identifier les domaines où vous pourriez agir. Les coûts pour Cognitive Services ne représentent qu’une partie des coûts mensuels sur votre facture Azure. Cet article explique comment planifier et gérer les coûts pour Cognitive Services. Cependant, vous êtes facturé pour tous les services et ressources Azure utilisés pour votre abonnement Azure, y compris les services tiers.

## <a name="prerequisites"></a>Prérequis

Analyse des coûts dans Cost Management prend en charge la plupart des types de compte Azure, mais pas tous. Pour accéder à la liste complète des types de comptes pris en charge, voir [Comprendre les données de Cost Management](../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn). Pour afficher les données de coût, vous avez au minimum besoin d’un accès en lecture pour un compte Azure. Pour plus d’informations sur l’attribution de l’accès aux données Azure Cost Management, consultez [Assigner l’accès aux données](../cost-management-billing/costs/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

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

## <a name="monitor-costs"></a>Superviser les coûts

<!-- Note to Azure service writer: Modify the following as needed for your service. Replace example screenshots with ones taken for your service. If you need assistance capturing screenshots, ask banders for help. -->

À mesure que vous utilisez des ressources Azure avec Cognitive Services, vous générez des coûts. Les coûts unitaires d’utilisation des ressources Azure varient selon les intervalles de temps (secondes, minutes, heures et jours) ou selon l’utilisation d’unités (octets, mégaoctets, etc.). Dès que l’utilisation de Cognitive Services démarre, des coûts sont occasionnés, que vous pouvez voir dans [Analyse des coûts](../cost-management/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

Quand vous effectuez une analyse du coût, vous voyez les coûts de Cognitive Services dans des graphes et des tableaux pour différents intervalles de temps. Par exemple, par jour, par année ou pour le mois en cours ou le mois précédent. Vous pouvez aussi afficher les coûts par rapport aux budgets et aux coûts prévus. Passez à des vues pour des périodes plus longues pour identifier les tendances des dépenses. Ceci vous permet de voir où des dépassements ont pu se produire. Si vous avez créé des budgets, vous pouvez aussi facilement voir à quel moment ils ont été dépassés.

Pour afficher les coûts de Cognitive Services dans l’analyse des coûts :

1. Connectez-vous au portail Azure.
2. Ouvrez l’étendue dans le portail Azure et sélectionnez **Analyse des coûts** dans le menu. Par exemple, accédez à **Abonnements**, sélectionnez un abonnement dans la liste, puis sélectionnez **Analyse du coût** dans le menu. Sélectionnez **Étendue** pour passer à une autre étendue dans l’analyse des coûts.
3. Par défaut, le coût des services est affiché dans le premier graphique en anneau. Dans le graphique, sélectionnez la zone intitulée Cognitive Services.

Les coûts mensuels réels s’affichent lors de l’ouverture initiale de l’analyse des coûts. Voici un exemple qui montre tous les coûts d’utilisation mensuelle.

:::image type="content" source="./media/cost-management/all-costs.png" alt-text="Exemple montrant les coûts cumulés pour un abonnement":::

- Pour limiter les coûts à un seul service, tel Cognitive Services, sélectionnez **Ajouter un filtre**, puis **Nom du service**. Sélectionnez ensuite **Cognitive Services**.

Voici un exemple montrant uniquement les coûts de Cognitive Services.

:::image type="content" source="./media/cost-management/cognitive-services-costs.png" alt-text="Exemple montrant les coûts cumulés pour Cognitive Services":::

Dans l’exemple précédent, vous avez pu voir le coût actuel du service. Les coûts par région Azure (emplacements) et les coûts de Cognitive Services par groupe de ressources sont également présentés. À partir de là, vous pouvez explorer les coûts par vous-même.

## <a name="create-budgets"></a>Créer des budgets

Vous pouvez créer des [budgets](../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) pour gérer les coûts et des [alertes](../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) permettant d’avertir automatiquement des parties prenantes en cas d’anomalies de dépenses et de risques de dépenses excessives. Les alertes sont basées sur les dépenses par rapport aux seuils de budget et de coût. Les budgets et les alertes sont créés pour les abonnements et les groupes de ressources Azure : ils sont donc utiles dans le cadre d’une stratégie globale de supervision des coûts. 

Vous pouvez créer des budgets avec des filtres pour des ressources ou des services spécifiques dans Azure si vous souhaitez disposer d’une plus grande granularité dans votre analyse. Les filtres vous permettent de vous assurer que vous ne créez pas accidentellement de nouvelles ressources entraînant un surcoût. Pour plus d’informations sur les options de filtre lorsque vous créez un budget, consultez [Options de regroupement et de filtre](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="export-cost-data"></a>Exporter des données de coûts

Vous pouvez également [exporter vos données de coûts](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) vers un compte de stockage. C’est utile quand vous ou d’autres personnes avez besoin d’effectuer des analyses supplémentaires des données concernant les coûts. Par exemple, les équipes financières peuvent analyser les données avec Excel ou Power BI. Vous pouvez exporter vos coûts selon une planification quotidienne, hebdomadaire ou mensuelle, et définir une plage de dates personnalisée. L’exportation des données des coûts est la méthode recommandée pour récupérer les jeux de données des coûts.

## <a name="next-steps"></a>Étapes suivantes

- Découvrez [comment optimiser votre investissement cloud avec Azure Cost Management](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Apprenez-en davantage sur la gestion des coûts avec l’[analyse du coût](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Découvrez comment [éviter des coûts imprévus](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Suivez le cours d’apprentissage guidé [Cost Management](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).