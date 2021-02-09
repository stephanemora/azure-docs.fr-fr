---
title: Prévoir la gestion des coûts d’Azure Logic Apps
description: Découvrez comment planifier et gérer les coûts pour Azure Logic Apps avec l’analyse des coûts dans le portail Azure
ms.service: logic-apps
ms.reviewer: estfan, logicappspm, azla
ms.topic: how-to
ms.custom: subject-cost-optimization
ms.date: 01/29/2021
ms.openlocfilehash: 58e12862cf00b500bced105d67fede8599c2a257
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/30/2021
ms.locfileid: "99179817"
---
# <a name="plan-and-manage-costs-for-azure-logic-apps"></a>Prévoir et gérer les coûts d’Azure Logic Apps

Cet article vous aide à planifier et gérer les coûts d’Azure Logic Apps. Avant de créer ou d’ajouter des ressources à l’aide de ce service, estimez vos coûts à l’aide de la calculatrice de prix Azure. Une fois que vous avez commencé à utiliser des ressources Logic Apps, vous pouvez définir des budgets et superviser les coûts à l’aide d’[Azure Cost Management](https://docs.microsoft.com/azure/cost-management-billing/cost-management-billing-overview?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn). Vous pouvez également passer en revue les coûts prévus et superviser les tendances des dépenses pour identifier les domaines où vous pourriez agir.

Gardez à l’esprit que les coûts de Logic Apps ne représentent qu’une partie des coûts mensuels sur votre facture Azure. Cet article explique comment planifier et gérer les coûts de Logic Apps. Tous les services et ressources Azure utilisés dan votre abonnement Azure, notamment les services tiers, vous sont toutefois facturés. Quand vous êtes familiarisé avec la gestion des coûts de Logic Apps, vous pouvez appliquer des méthodes similaires pour gérer les coûts de tous les services Azure utilisés dans votre abonnement.

## <a name="prerequisites"></a>Prérequis

<!--Note for Azure service writer: This section covers prerequisites for the Cost Management's Cost Analysis feature. Add other prerequisites needed for your service after the Cost Management prerequisites. -->

[Azure Cost Management](https://docs.microsoft.com/azure/cost-management-billing/cost-management-billing-overview?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) prend en charge la plupart des types de comptes Azure. Pour voir tous les types de comptes pris en charge, consultez [Comprendre les données de Cost Management](https://docs.microsoft.com/azure/cost-management-billing/costs/understand-cost-mgt-data?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn). Pour afficher les données de coût, vous avez au minimum besoin d’un accès en lecture pour votre compte Azure.

Pour plus d’informations sur l’attribution de l’accès aux données Azure Cost Management, consultez [Assigner l’accès aux données](https://docs.microsoft.com/azure/cost-management/assign-access-acm-data?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

<!--Note for Azure service writer: If you have other prerequisites for your service, add them here -->

<a name="understand-billing-model"></a>

## <a name="understand-the-billing-model"></a>Comprendre le modèle de facturation

Azure Logic Apps s’exécute sur l’infrastructure Azure qui [accumule les coûts](https://azure.microsoft.com/pricing/details/logic-apps/) lorsque vous déployez de nouvelles ressources. Veillez à bien comprendre le [modèle de facturation pour le service Logic Apps, ainsi que les ressources Azure associées](logic-apps-pricing.md) et à gérer les coûts liés à ces dépendances lorsque vous apportez des modifications aux ressources déployées.

<a name="typical-costs"></a>

### <a name="costs-that-typically-accrue-with-azure-logic-apps"></a>Coûts qui s’accumulent généralement avec Azure Logic Apps

Le service Logic Apps applique différents modèles de tarification, en fonction des ressources que vous créez et utilisez :

* Les ressources d’application logique que vous créez et exécutez dans le service Logic Apps multilocataire utilisent un [modèle de tarification de la consommation](../logic-apps/logic-apps-pricing.md#consumption-pricing).

* Les ressources d’application logique que vous créez et exécutez dans un [environnement ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) utilisent un [modèle de tarification fixe](../logic-apps/logic-apps-pricing.md#fixed-pricing).

Voici d’autres ressources qui entraînent des coûts lorsque vous les créez pour une utilisation avec des applications logiques :

* Un [compte d’intégration](../logic-apps/logic-apps-pricing.md#integration-accounts) est une ressource distincte que vous créez et liez à une application logique pour créer des intégrations B2B. Les comptes d’intégration utilisent un [modèle de tarification fixe](../logic-apps/logic-apps-pricing.md#integration-accounts) où le tarif est basé sur le type de compte d’intégration ou le *niveau* que vous utilisez.

* Un [environnement ISE](../logic-apps/logic-apps-pricing.md#fixed-pricing) est une ressource distincte que vous créez en tant qu’emplacement de déploiement pour les applications logiques qui ont besoin d’un accès direct aux ressources dans un réseau virtuel. Les environnements ISE utilisent un [modèle de tarification fixe](../logic-apps/logic-apps-pricing.md#fixed-pricing) où le taux est basé sur la référence SKU de l’environnement ISE que vous créez, ainsi que d’autres paramètres.

* Un [connecteur personnalisé](../logic-apps/logic-apps-pricing.md#consumption-pricing) est une ressource distincte que vous créez pour une API REST qui n’a aucun connecteur prédéfini que vous pouvez utiliser dans vos applications logiques. Les exécutions de connecteur personnalisé utilisent un [modèle de tarification de la consommation](../logic-apps/logic-apps-pricing.md#consumption-pricing) sauf lorsque vous les utilisez dans un environnement ISE.

* Dans le service Logic Apps multilocataire, [la conservation des données et la consommation du stockage](../logic-apps/logic-apps-pricing.md#data-retention) accumulent les coûts à l’aide d’un [modèle de tarification fixe](../logic-apps/logic-apps-pricing.md#fixed-pricing). Par exemple, les entrées et les sorties de l’historique des exécutions sont conservées dans un stockage en arrière-plan, qui diffère des ressources de stockage que vous créez, gérez et auxquelles vous accédez indépendamment à partir de votre application logique.

  Dans un environnement ISE, la conservation des données et la consommation du stockage n’entraînent pas de coûts.

<a name="costs-after-resource-deletion"></a>

### <a name="costs-might-accrue-after-resource-deletion"></a>Coûts qui peuvent s’accumuler après la suppression de la ressource

<!--Note to Azure service writer: You might need to sync with your product team to identify resources that continue to exist after those ones for your service are deleted. If you're certain that no resources can exist after those for your service are deleted, then omit this section. -->

Une fois que vous avez supprimé une application logique, le service Logic Apps ne crée pas ou n’exécute pas de nouvelles instances de flux de travail. Toutefois, toutes les exécutions en cours et en attente continuent jusqu’à ce qu’elles soient terminées. En fonction du nombre de ces exécutions, ce processus peut prendre un certain temps. Pour plus d’informations, consultez [Gérer des applications logiques](manage-logic-apps-with-azure-portal.md#delete-logic-apps).

Si vous disposez de ces ressources après la suppression d’une application logique, ces ressources continuent d’exister et d’accumuler des coûts jusqu’à ce que vous les supprimiez :

* Les ressources Azure que vous créez et gérez indépendamment de l’application logique qui se connecte à ces ressources, par exemple, les applications de fonction Azure, les Event Hubs, les grilles d’événements, etc.

* Comptes d’intégration

* Environnements de service d’intégration (ISE)

  Si vous [supprimez un environnement ISE](ise-manage-integration-service-environment.md#delete-ise), le réseau virtuel Azure associé, les sous-réseaux et les autres ressources associées continuent d’exister. Après avoir supprimé l’environnement ISE, vous devrez peut-être attendre un certain nombre d’heures avant de pouvoir essayer de supprimer le réseau virtuel ou les sous-réseaux.

### <a name="using-monetary-credit-with-azure-logic-apps"></a>Utilisation de Monetary Credit avec Azure Logic Apps

Vous pouvez payer les frais d’Azure Logic Apps avec votre crédit d’engagement financier EA. Vous ne pouvez cependant pas utiliser le crédit d’engagement financier EA pour payer des frais liés à des produits et services tiers, y compris ceux de la Place de marché Azure.

<a name="estimate-costs"></a>

## <a name="estimate-costs"></a>Estimer les coûts

Avant de créer des ressources avec Azure Logic Apps, estimez vos coûts à l’aide de la [calculatrice de prix Azure](https://azure.microsoft.com/pricing/calculator/). Pour plus d’informations, consultez [Modèle de tarification pour Azure Logic Apps](../logic-apps/logic-apps-pricing.md).

1. Dans la [page de la calculatrice de prix Azure](https://azure.microsoft.com/pricing/calculator/), dans le menu de gauche, sélectionnez **Intégration** > **Azure Logic Apps**.

   ![Capture d’écran montrant la calculatrice de prix Azure avec l’option « Azure Logic Apps » sélectionnée.](./media/plan-manage-costs/add-azure-logic-apps-pricing-calculator.png)

1. Faites défiler la page jusqu’à ce que vous puissiez afficher la calculatrice de prix Azure Logic Apps. Dans les différentes sections pour les ressources Azure qui sont directement liées à Azure Logic Apps, entrez le nombre de ressources que vous prévoyez d’utiliser et le nombre d’intervalles durant lesquels vous pourriez utiliser ces ressources.

   Cette capture d’écran montre un exemple de l’estimation des coûts à l’aide de la calculatrice :

   ![Exemple d’estimation de coûts dans la calculatrice de prix Azure](./media/plan-manage-costs/example-logic-apps-pricing-calculator.png)

1. Pour mettre à jour vos estimations des coûts à mesure que vous créez et utilisez de nouvelles ressources associées, revenez à cette calculatrice et mettez à jour ces ressources ici.

<a name="create-budgets-alerts"></a>

## <a name="create-budgets-and-alerts"></a>Créer des budgets et des alertes

Pour vous aider à gérer de manière proactive les coûts pour votre compte ou abonnement Azure, vous pouvez créer des [budgets](https://docs.microsoft.com/azure/cost-management/tutorial-acm-create-budgets?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) et des [alertes](https://docs.microsoft.com/azure/cost-management/cost-mgt-alerts-monitor-usage-spending?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) à l’aide du service et des fonctionnalités [Azure Cost Management + Billing](https://docs.microsoft.com/azure/cost-management-billing/cost-management-billing-overview?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).  Les budgets et les alertes sont créés pour les abonnements et les groupes de ressources Azure : ils sont donc utiles dans le cadre d’une stratégie globale de supervision des coûts.

En fonction des dépenses par rapport aux seuils de budget et de coût, des alertes informent automatiquement les parties prenantes en cas de problèmes de dépense et des risques liés aux dépassements. Vous pouvez aussi créer des budgets avec des filtres pour des ressources ou des services spécifiques dans Azure si vous souhaitez disposer d’une plus grande granularité dans votre supervision. Les filtres vous permettent de vous assurer que vous ne créez pas accidentellement de nouvelles ressources entraînant un surcoût. Pour plus d’informations sur les options de filtre, consultez [Options de filtre et de groupe](https://docs.microsoft.com/azure/cost-management-billing/costs/group-filter?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

<a name="monitor-costs"></a>

## <a name="monitor-costs"></a>Superviser les coûts

Les coûts unitaires d’utilisation des ressources varient selon les intervalles de temps (secondes, minutes, heures et jours) ou selon l’utilisation d’unités (octets, mégaoctets, etc.). Par exemple, par jour, par année ou pour le mois en cours ou le mois précédent. Passez à des vues pour des périodes plus longues pour identifier les tendances des dépenses. Lorsque vous utilisez les fonctionnalités d’analyse des coûts, vous pouvez afficher les coûts sous forme de graphes et de tables sur différents intervalles de temps. Si vous avez créé des budgets et des prévisions de coût, vous pouvez également trouver facilement où les budgets sont dépassés et où les dépassements ont pu avoir lieu.

Une fois que vous avez commencé à engranger des coûts pour les ressources qui sont créées ou qui commencent dans Azure, vous pouvez passer en revue et superviser ces coûts de l’une des manières suivantes :

* [Superviser les exécutions d’applications logiques et la consommation de stockage](#monitor-billing-metrics) à l’aide d’Azure Monitor

* Exécuter une [analyse des coûts](https://docs.microsoft.com/azure/cost-management/quick-acm-cost-analysis?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) à l’aide d’[Azure Cost Management + Billing](https://docs.microsoft.com/azure/cost-management-billing/cost-management-billing-overview?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)

<a name="monitor-billing-metrics"></a>

### <a name="monitor-logic-app-executions-and-storage-consumption"></a>Superviser les exécutions d’applications logiques et la consommation de stockage

À l’aide d’Azure Monitor, vous pouvez afficher ces métriques pour une application logique spécifique :

* Exécutions d’actions facturables
* Exécutions de déclencheurs facturables
* Utilisation de la facturation pour les exécutions d’opérations natives
* Utilisation de la facturation pour les exécutions de connecteurs Standard
* Utilisation de la facturation pour la consommation du stockage
* Nombre total d’exécutions facturables

<a name="execution-storage-metrics"></a>

#### <a name="view-execution-and-storage-consumption-metrics"></a>Afficher les métriques de consommation liées au stockage et à l’exécution

1. Dans le portail Azure, recherchez et sélectionnez votre application logique. Dans le menu de votre application logique, sous **Supervision**, sélectionnez **Métriques**.

1. Dans le volet de droite, sous **Titre du graphique**, dans la barre des métriques, ouvrez la liste **Métrique**, puis sélectionnez la métrique de votre choix.

   > [!NOTE]
   > La consommation du stockage est mesurée comme le nombre d’unités de stockage (Go) utilisées par votre application logique et est facturée. Les exécutions qui utilisent moins de 500 Mo de stockage peuvent ne pas apparaître dans la vue de supervision, mais elles sont quand même facturées.

   ![Capture d’écran montrant le volet Métriques avec la liste « Métrique » ouverte.](./media/logic-apps-pricing/select-metric.png)

1. Dans l’angle supérieur droit du volet, sélectionnez la période de votre choix.

1. Pour afficher d’autres données de consommation du stockage, notamment les tailles d’entrée et de sortie d’une action dans l’historique des exécutions de votre application logique, [procédez comme suit](#view-input-output-sizes).

<a name="view-input-output-sizes"></a>

#### <a name="view-action-input-and-output-sizes-in-run-history"></a>Afficher les tailles d’entrée et de sortie d’une action dans l’historique des exécutions

1. Dans le portail Azure, recherchez et sélectionnez votre application logique.

1. Dans le menu de votre application logique, sélectionnez **Vue d’ensemble**.

1. Dans le volet à droite, sous **Historique des exécutions**, sélectionnez l’exécution qui dispose des entrées et des sorties que vous voulez afficher.

1. Sous **Exécution de l’application logique**, sélectionnez **Détails de l’exécution**.

1. Dans le volet **Détails de l’exécution de l’application logique**, dans la table des actions qui répertorie la durée et l’état de chaque action, sélectionnez l’action que vous souhaitez afficher.

1. Dans le volet **Action de l’application logique**, recherchez les tailles des entrées et des sorties de cette action. Sous **Lien d’entrées** et **Lien de sorties**, recherchez les liens vers ces entrées et sorties.

   > [!NOTE]
   > Pour les boucles, seules les actions de niveau supérieur affichent des tailles pour leurs entrées et sorties. Pour les actions dans les boucles imbriquées, les entrées et les sorties affichent une taille nulle et aucun lien.

<a name="run-cost-analysis"></a>

### <a name="run-cost-analysis-by-using-azure-cost-management-and-billing"></a>Exécuter une analyse des coûts à l’aide d’Azure Cost Management + Billing

Pour passer en revue les coûts du service Logic Apps en fonction d’une étendue spécifique, par exemple, un abonnement Azure, vous pouvez utiliser les fonctionnalités [d’analyse des coûts](https://docs.microsoft.com/azure/cost-management/quick-acm-cost-analysis?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) dans [Azure Cost Management + Billing](https://docs.microsoft.com/azure/cost-management-billing/cost-management-billing-overview?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

1. Dans le portail Azure, ouvrez l’étendue de votre choix, par exemple votre abonnement Azure. Dans le menu gauche, sous **Cost Management**, sélectionnez **Analyse des coûts**.

   Lorsque vous ouvrez pour la première fois le volet d’analyse des coûts, le graphe du haut affiche les coûts d’utilisation réels et prévus pour tous les services de l’abonnement durant le mois en cours.

   ![Capture d’écran qui montre le portail Azure et le volet d’analyse des coûts avec des exemples de coûts réels et prévus dans un abonnement.](./media/plan-manage-costs/cost-analysis-total-actual-forecasted-costs.png)

   > [!TIP]
   > Pour changer d’étendue, dans le volet **Analyse des coûts**, dans la barre des filtres, sélectionnez le filtre **Étendue**. Dans le volet **Sélectionner une étendue**, basculez vers l’étendue de votre choix.

   En dessous, les graphiques en anneau affichent les coûts actuels par service Azure, par région Azure (emplacement) et par groupe de ressources.

   ![Capture d’écran qui montre le portail Azure et le volet d’analyse des coûts avec des exemples de graphiques en anneau pour les services, les régions et les groupes de ressources.](./media/plan-manage-costs/cost-analysis-donut-charts.png)

1. Pour filtrer le graphique sur une zone spécifique, comme un service ou une ressource, dans la barre des filtres, sélectionnez **Ajouter un filtre**.

1. Dans la liste de gauche, sélectionnez le type de filtre, par exemple **Nom du service**. Dans la liste de droite, sélectionnez le filtre, par exemple **applications logiques**. Quand vous avez terminé, sélectionnez la coche verte.

   ![Capture d’écran qui montre le portail Azure et le volet d’analyse des coûts avec des sélections de filtre.](./media/plan-manage-costs/cost-analysis-add-service-name-filter.png)

   Par exemple, voici le résultat du service Logic Apps :

   ![Capture d’écran qui montre le portail Azure et le volet d’analyse des coûts avec des résultats filtrés sur « applications logiques ».](./media/plan-manage-costs/cost-analysis-total-actual-costs-service.png)

### <a name="export-cost-data"></a>Exporter des données de coûts

Lorsque vous avez besoin d’effectuer des analyses de données supplémentaire sur les coûts, vous pouvez [exporter des données de coût](https://docs.microsoft.com/azure/cost-management-billing/costs/tutorial-export-acm-data?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) vers un compte de stockage. Par exemple, une équipe Finance peut analyser ces données avec Excel ou Power BI. Vous pouvez exporter vos coûts selon une planification quotidienne, hebdomadaire ou mensuelle, et définir une plage de dates personnalisée. L’exportation des données des coûts est la méthode recommandée pour récupérer les jeux de données des coûts.

## <a name="other-ways-to-manage-and-reduce-costs"></a>Autres façons de gérer et réduire les coûts

<!-- Note to Azure service writer: This section is optional. Other than using the Cost Management methods above, your service probably has other specific ways to minimize costs. For example, you might have best practice advice or specific ways to reduce costs that are specific to your service. If so, try to add that guidance here or at least summarize key points. Try to be as prescriptive as possible. If you have more comprehensive content, add links to your other published articles or sections here.

Add a statement that discusses any recommended settings for your service that might help keep the charges minimal if a service isn't being actively used by the customer. For example: Will turning off a VM help to get no charges for the specific VM resource?

Otherwise, if no other cost-saving recommendations or best practices exist to reduce costs, cut this section.
-->

Pour vous aider à réduire les coûts sur vos applications logiques et les ressources associées, essayez les options suivantes :

* Dans la mesure du possible, utilisez des [déclencheurs et actions intégrés](../connectors/apis-list.md#built-in), dont le coût par exécution est inférieur à celui des [déclencheurs et actions de connecteur managé](../connectors/apis-list.md#managed-connectors).

  Vous pourriez, par exemple, réduire les coûts lors de l’accès à d’autres ressources à l’aide de [l’action HTTP](../connectors/connectors-native-http.md) ou en appelant une fonction que vous avez créée à l’aide du [service Azure Functions](../azure-functions/functions-overview.md) et à l’aide de [l’action Azure Functions intégrée](../logic-apps/logic-apps-azure-functions.md). Toutefois, l’utilisation d’Azure Functions entraîne également des coûts, donc veillez à comparer vos options.

* [Spécifiez des conditions de déclencheur précises](logic-apps-workflow-actions-triggers.md#trigger-conditions) pour l’exécution d’un flux de travail.

  Par exemple, vous pouvez spécifier qu’un déclencheur est activé uniquement quand le site web cible retourne une erreur interne du serveur. Dans la définition JSON du déclencheur, utilisez la propriété `conditions` pour spécifier une condition qui fait référence au code d’état du déclencheur.

* Si un déclencheur a une version d’interrogation et une version webhook, essayez la version webhook, qui attend que l’événement spécifié se produise avant de se déclencher, plutôt que de vérifier régulièrement la présence de l’événement.

* Appelez votre application logique via un autre service afin que le déclencheur s’active uniquement lorsque le flux de travail doit s’exécuter.

  Par exemple, vous pouvez appeler votre application logique à partir d’une fonction que vous créez et exécutez à l’aide du service Azure Functions. Pour obtenir un exemple, consultez [Appeler ou déclencher des applications logiques à l’aide d’Azure Functions et d’Azure Service Bus](logic-apps-scenario-function-sb-trigger.md).

* [Désactivez les applications logiques](manage-logic-apps-with-azure-portal.md#disable-or-enable-logic-apps) qui n’ont pas à s’exécuter en permanence, ou [supprimez les applications logiques](manage-logic-apps-with-azure-portal.md#delete-logic-apps) dont vous n’avez plus besoin. Dans la mesure du possible, désactivez toutes les autres ressources dont vous n’avez pas besoin en permanence.

## <a name="next-steps"></a>Étapes suivantes

* [Optimiser votre investissement dans le cloud avec Azure Cost Management](https://docs.microsoft.com/azure/cost-management-billing/costs/cost-mgt-best-practices?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
* [Gérer les coûts à l’aide de l’analyse des coûts](https://docs.microsoft.com/azure/cost-management-billing/costs/quick-acm-cost-analysis?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
* [Éliminer les coûts imprévus](https://docs.microsoft.com/azure/cost-management-billing/understand/analyze-unexpected-charges?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
* Suivre le cours d’apprentissage guidé [Cost Management](https://docs.microsoft.com/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)


