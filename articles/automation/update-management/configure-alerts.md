---
title: Guide pratique pour créer des alertes pour Azure Automation Update Management
description: Cet article explique comment configurer des alertes Azure vous informant de l’état des évaluations ou déploiements de mises à jour.
services: automation
ms.subservice: update-management
ms.date: 03/15/2021
ms.topic: conceptual
ms.openlocfilehash: 224a7b5457a099fd763ac657349fc5497824ab76
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104601412"
---
# <a name="how-to-create-alerts-for-update-management"></a>Guide pratique pour créer des alertes pour Update Management

Les alertes dans Azure vous informent de façon proactive des résultats des travaux de runbook, des problèmes d’intégrité de service ou d’autres scénarios liés à votre compte Automation. Azure Automation n’inclut pas de règles d’alerte préconfigurées, mais vous pouvez créer vos propres règles en fonction des données générées. Cet article fournit des conseils sur la création de règles d’alerte à l’aide des métriques incluses dans Update Management.

## <a name="available-metrics"></a>Métriques disponibles

Azure Automation crée deux métriques de plateforme distinctes relatives à Update Management, collectées et transférées à Azure Monitor. Ces métriques peuvent être utilisées à des fins d’analyse avec [Metrics Explorer](../../azure-monitor/essentials/metrics-charts.md) et pour les alertes avec une [règle d’alerte de métriques](../../azure-monitor/alerts/alerts-metric.md).

Les deux métriques émises sont les suivantes :

* Nombre total d’exécutions de déploiement de mises à jour de machines
* Nombre total d’exécutions de déploiement de mises à jour

Quand elles sont utilisées pour les alertes, les deux métriques prennent en charge les dimensions contenant des informations supplémentaires pour limiter l’alerte à un détail spécifique du déploiement des mises à jour. Le tableau suivant présente les détails relatifs aux métriques et aux dimensions disponibles dans le cadre de la configuration d’une alerte.

|Nom du signal|Dimensions|Description
|---|---|---|
|`Total Update Deployment Runs`|- Nom du déploiement de mises à jour<br>- Statut | Génère des alertes concernant le statut global d’un déploiement de mises à jour.|
|`Total Update Deployment Machine Runs`|- Nom du déploiement de mises à jour</br>- Statut</br>- Ordinateur cible</br>- ID d’exécution du déploiement des mises à jour    |Génère des alertes concernant le statut d’un déploiement de mises à jour ciblant des machines spécifiques.|

## <a name="create-alert"></a>Créer une alerte

Effectuez les étapes ci-dessous pour configurer des alertes afin de vous informer de l’état d’un déploiement de mises à jour. Si vous débutez avec les alertes Azure, consultez [Vue d’ensemble des alertes Azure](../../azure-monitor/alerts/alerts-overview.md).

1. Dans votre compte Automation, sélectionnez **Alertes** sous **Supervision**, puis sélectionnez **Nouvelle règle d’alerte**.

1. Dans la page **Créer une règle d’alerte**, votre compte Automation est déjà sélectionné comme ressource. Si vous souhaitez le changer, sélectionnez **Modifier la ressource**.

1. Dans la page Sélectionner une ressource, choisissez **Comptes Automation** dans la liste déroulante **Filtrer par type de ressource**.

1. Sélectionnez le compte Automation que vous souhaitez utiliser, puis sélectionnez **Terminé**.

1. Sélectionnez **Ajouter une condition** pour choisir le signal correspondant à vos exigences.

1. Pour une dimension, sélectionnez une valeur valide dans la liste. Si la valeur souhaitée ne figure pas dans la liste, sélectionnez **\+** à côté de la dimension et tapez le nom personnalisé. Sélectionnez ensuite la valeur à rechercher. Si vous voulez sélectionner toutes les valeurs d’une dimension, sélectionnez le bouton **Sélectionner \*** . Si vous ne choisissez aucune valeur de dimension, Update Management ignore cette dimension.

    ![Configurer la logique du signal](./media/manage-updates-for-vm/signal-logic.png)

1. Sous **Logique d’alerte**, entrez les valeurs dans les champs **Agrégation du temps** et **Seuil**, puis sélectionnez **Terminé**.

1. Dans la page suivante, entrez un nom et une description pour l’alerte.

1. Définissez le champ **Gravité** sur **Information (gravité 2)** pour une exécution réussie, ou sur **Information (gravité 1)** pour une exécution ayant échoué.

    ![Capture d’écran montrant la section Définir les détails de l’alerte avec les champs Nom de la règle d’alerte, Description et Gravité en surbrillance.](./media/manage-updates-for-vm/define-alert-details.png)

1. Sélectionnez **Oui** pour activer la règle d’alerte.

## <a name="configure-action-groups-for-your-alerts"></a>Configurer des groupes d’actions pour vos alertes

Une fois vos alertes configurées, vous pouvez configurer un groupe d’actions à exécuter quand plusieurs alertes sont déclenchées. Les actions peuvent inclure les notifications par e-mail, les runbooks, les webhooks et bien plus encore. Pour en savoir plus sur les groupes d’actions, consultez [Créer et gérer des groupes d’actions](../../azure-monitor/alerts/action-groups.md).

1. Sélectionnez une alerte, puis sélectionnez **Ajouter des groupes d’actions** sous **Actions**. Le volet **Sélectionner un groupe d’actions à associer à cette règle d’alerte** s’affiche.

   :::image type="content" source="./media/manage-updates-for-vm/select-an-action-group.png" alt-text="Utilisation et estimation des coûts.":::

1. Activez la case à cocher du groupe d’actions à associer, puis appuyez sur Sélectionner.

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur les [alertes dans Azure Monitor](../../azure-monitor/alerts/alerts-overview.md).

* Découvrez les [requêtes de journal](../../azure-monitor/logs/log-query-overview.md) pour récupérer et analyser les données d’un espace de travail Log Analytics.

* Gérer [l’utilisation et les coûts à l’aide des Journaux de Azure Monitor](../../azure-monitor/logs/manage-cost-storage.md) décrit comment contrôler vos coûts en modifiant la période de conservation des données, et comment analyser et alerter sur l’utilisation de vos données.