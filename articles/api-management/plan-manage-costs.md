---
title: Planifier et gérer les coûts pour la gestion des API Azure
description: Découvrez comment planifier et gérer les coûts pour la gestion des API Azure avec l’analyse du coût dans le portail Azure.
author: dlepow
ms.author: apimpm
ms.custom: subject-cost-optimization
ms.service: api-management
ms.topic: how-to
ms.date: 12/15/2020
ms.openlocfilehash: 799ef33e78737bfd2e1c1a1cfa4991f44c8f5ec0
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/20/2021
ms.locfileid: "98599633"
---
# <a name="plan-and-manage-costs-for-api-management"></a>Planifier et gérer les coûts pour la gestion des API

Cet article explique comment planifier et gérer les coûts pour la gestion des API Azure. Vous devez d’abord utiliser la calculatrice de prix Azure pour planifier les coûts de gestion des API avant d’ajouter des ressources pour le service aux coûts estimés. Une fois que vous avez commencé à utiliser des ressources de gestion des API, utilisez les fonctionnalités de Cost Management pour définir des budgets et superviser les coûts. Vous pouvez également passer en revue les coûts prévus et déterminer les tendances des dépenses pour identifier les domaines où vous pourriez agir. 

Les coûts pour la gestion des API ne représentent qu’une partie des coûts mensuels sur votre facture Azure. Cet article explique comment planifier et gérer les coûts de gestion des API. Cependant, vous êtes facturé pour tous les services et ressources Azure utilisés pour votre abonnement Azure, y compris les services tiers.

## <a name="prerequisites"></a>Prérequis

Analyse des coûts dans Cost Management prend en charge la plupart des types de compte Azure, mais pas tous. Pour accéder à la liste complète des types de comptes pris en charge, voir [Comprendre les données de Cost Management](../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn). Pour afficher les données de coût, vous avez au minimum besoin d’un accès en lecture pour un compte Azure. Pour plus d’informations sur l’attribution de l’accès aux données Azure Cost Management, consultez [Assigner l’accès aux données](../cost-management-billing/costs/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="estimate-costs-before-using-api-management"></a>Estimer les coûts avant d’utiliser la gestion des API

Utilisez la [calculatrice de prix Azure](https://azure.microsoft.com/pricing/calculator/) pour estimer les coûts avant d’ajouter la gestion des API. 

1. Recherchez *Gestion des API* ou sélectionnez **Intégration** > **Gestion des API**.
1. Sélectionnez **Affichage** pour ajouter une estimation de coût par défaut pour une instance de service de gestion des API.

> [!NOTE]
> Les coûts indiqués dans cet exemple sont fournis à des fins de démonstration uniquement. Pour obtenir les informations les plus récentes sur la tarification, consultez la page [Tarification de gestion des API](https://azure.microsoft.com/pricing/details/api-management/).

:::image type="content" source="media/plan-manage-costs/pricing-calculator-developer-tier.png" alt-text="Estimer les coûts pour le niveau Développeur":::

* L’estimation du coût par défaut est basée sur une instance du service de gestion des API dans le [niveau de service](api-management-features.md) **Développeur** avec 1 [unité de capacité](api-management-capacity.md). Le niveau Développeur est uniquement pour les cas d’utilisation et les évaluations hors production. Il n’est pas associé à un contrat de niveau de service.

* Pour estimer les coûts des unités de capacité supplémentaires ou d’un autre niveau de service, sélectionnez d’autres options dans les listes déroulantes **Unités** et **Niveau**.

* En fonction de la disponibilité des fonctionnalités et du niveau de service, des frais supplémentaires peuvent s’appliquer à l’utilisation de [passerelles auto-hébergées](self-hosted-gateway-overview.md).

Pour obtenir des détails supplémentaires sur la tarification et les fonctionnalités, consultez :

* [tarification du service Gestion des API](https://azure.microsoft.com/pricing/details/api-management/)
* [Comparaison des fonctionnalités des niveaux de la Gestion des API Azure](api-management-features.md)

### <a name="using-monetary-credit-with-api-management"></a>Utilisation d’un crédit monétaire avec la gestion des API

Vous pouvez payer les frais d’API Management avec votre Acompte Azure (anciennement appelé engagement financier). Vous ne pouvez cependant pas utiliser le crédit Paiement anticipé Azure pour payer des frais pour des produits et services tiers, notamment ceux de la Place de marché Azure.

## <a name="monitor-costs"></a>Superviser les coûts

À mesure que vous utilisez des ressources Azure avec la gestion des API, vous générez des coûts. Les coûts unitaires d’utilisation des ressources Azure varient selon les intervalles de temps (secondes, minutes, heures et jours) ou selon l’utilisation d’unités (octets, mégaoctets, etc.). Dès que l’utilisation de la gestion des API démarre, les coûts sont générés et vous pouvez voir les coûts dans [Analyse des coûts](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

Quand vous utilisez l’analyse des coûts, vous affichez les coûts de gestion des API dans des graphes et des tableaux pour différents intervalles de temps. Par exemple, par jour, par année ou pour le mois en cours ou le mois précédent. Vous pouvez aussi afficher les coûts par rapport aux budgets et aux coûts prévus. Passez à des vues pour des périodes plus longues pour identifier les tendances des dépenses. Ceci vous permet de voir où des dépassements ont pu se produire. Si vous avez créé des budgets, vous pouvez aussi facilement voir à quel moment ils ont été dépassés.

> [!NOTE]
> Les coûts indiqués dans cet exemple sont fournis à des fins de démonstration uniquement. Vos coûts varient en fonction de l’utilisation des ressources et de la tarification actuelle.

Pour afficher les coûts de gestion des API dans l’analyse des coûts :

1. Connectez-vous au [portail Azure](https://azure.microsoft.com).
1. Ouvrez la fenêtre **Gestion des coûts + Facturation**, sélectionnez **Gestion des coûts** dans le menu, puis sélectionnez **Étendue de facturation**. Par exemple, sélectionnez un abonnement dans la liste.
1. Sélectionnez **Gestion des coûts** dans le menu, puis sélectionnez **Analyse des coûts**.
1. Par défaut, le coût mensuel de tous les services est affiché dans le premier graphique en anneau. 

    :::image type="content" source="media/plan-manage-costs/api-management-cost-analysis.png" alt-text="Coûts mensuels pour l’abonnement":::

1. Pour limiter les coûts à un seul service, par exemple la gestion des API, sélectionnez **Ajouter un filtre**, puis sélectionnez **Nom du service**. Sélectionnez **Gestion des API**.

    :::image type="content" source="media/plan-manage-costs/api-management-apim-cost-analysis.png" alt-text="Exemple montrant les coûts cumulés pour la gestion des API":::

Dans l’exemple précédent, vous avez pu voir le coût actuel du service. Les coûts par région Azure (emplacements) et les coûts de gestion des API par groupe de ressources sont également présentés. À partir de là, vous pouvez explorer les coûts par vous-même.

## <a name="create-budgets"></a>Créer des budgets

Vous pouvez créer des [budgets](../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) pour gérer les coûts et des [alertes](../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) permettant d’avertir automatiquement des parties prenantes en cas d’anomalies de dépenses et de risques de dépenses excessives. Les alertes sont basées sur les dépenses par rapport aux seuils de budget et de coût. Les budgets et les alertes sont créés pour les abonnements et les groupes de ressources Azure : ils sont donc utiles dans le cadre d’une stratégie globale de supervision des coûts. 

Vous pouvez créer des budgets avec des filtres pour des ressources ou des services spécifiques dans Azure si vous souhaitez disposer d’une plus grande granularité dans votre analyse. Les filtres vous permettent de vous assurer que vous ne créez pas accidentellement de nouvelles ressources entraînant un surcoût. Pour plus d’informations sur les options de filtre lorsque vous créez un budget, consultez [Options de regroupement et de filtre](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="export-cost-data"></a>Exporter des données de coûts

Vous pouvez également [exporter vos données de coûts](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) vers un compte de stockage. C’est utile quand vous ou d’autres personnes avez besoin d’effectuer des analyses supplémentaires des données concernant les coûts. Par exemple, une équipe Finance peut analyser les données avec Excel ou Power BI. Vous pouvez exporter vos coûts selon une planification quotidienne, hebdomadaire ou mensuelle, et définir une plage de dates personnalisée. L’exportation des données des coûts est la méthode recommandée pour récupérer les jeux de données des coûts.

## <a name="other-ways-to-manage-and-reduce-costs-for-api-management"></a>Autres façons de gérer et de réduire les coûts de gestion des API

### <a name="choose-tier"></a>Choisir un niveau

Passez en revue la [comparaison des fonctionnalités des niveaux de gestion des API Azure](api-management-features.md) pour vous aider à déterminer le niveau de service qui peut être adapté à vos scénarios. Les différents niveaux de service prennent en charge des combinaisons de fonctionnalités et de capacités conçues pour différents cas d’usage, avec des coûts différents. [Mettez à niveau](upgrade-and-scale.md) vers un autre niveau de service à tout moment.

* Le niveau de service **Consommation** fournit une option légère, serverless, qui n’implique aucun coût fixe. Vous êtes facturé en fonction du nombre d’appels d’API au service au-dessus d’un certain seuil. La capacité est également mise à l’échelle automatiquement en fonction de la charge sur le service.
* Les autres niveaux de gestion des API entraînent des coûts mensuels et fournissent un débit et des ensembles de fonctionnalités plus riches pour les charges de travail d’évaluation et de production.

### <a name="scale-using-capacity-units"></a>Mettre à l’échelle à l’aide d’unités de capacité

À l’exception du niveau de service Consommation, la gestion des API prend en charge la mise à l’échelle en ajoutant ou en supprimant des [*unités de capacité*](api-management-capacity.md). À mesure que la charge augmente sur une instance de gestion des API, l’ajout d’unités de capacité peut être plus économique que la mise à niveau vers un niveau de service supérieur. Le nombre maximal d’unités dépend du niveau de service.

Chaque unité de capacité dispose d’une certaine capacité de traitement des requêtes qui dépend du niveau du service. Par exemple, une unité du niveau de base a un débit maximal estimé d’environ 1 000 requêtes par seconde. 

Au fur et à mesure que vous ajoutez ou supprimez des unités, la capacité et les coûts évoluent proportionnellement. Par exemple, deux unités du niveau standard fournissent un débit estimé d’environ 2 000 requêtes par seconde. Le débit réel peut être différent en raison de la taille des requêtes ou des réponses, des modèles de connexion, du nombre de clients envoyant des requêtes et d’autres facteurs.

[Analysez](api-management-howto-use-azure-monitor.md) la métrique Capacité de votre instance de gestion des API pour prendre des décisions concernant la mise à l’échelle ou la mise à niveau d’une instance de gestion des API pour gérer davantage de charge.

## <a name="next-steps"></a>Étapes suivantes

- Découvrez [comment optimiser votre investissement cloud avec Azure Cost Management](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Apprenez-en davantage sur la gestion des coûts avec l’[analyse du coût](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Découvrez comment [éviter des coûts imprévus](../cost-management-billing/manage/getting-started.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Suivez le cours d’apprentissage guidé [Cost Management](https://docs.microsoft.com/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- En savoir plus sur la [capacité](api-management-capacity.md) de gestion des API.
- Consultez les étapes de mise à l’échelle et de mise à niveau de la gestion des API à l’aide du [portail Azure](upgrade-and-scale.md)et découvrez la [mise à l’échelle automatique](api-management-howto-autoscale.md).