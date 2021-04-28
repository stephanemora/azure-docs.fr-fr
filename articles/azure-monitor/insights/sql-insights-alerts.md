---
title: Créer des alertes avec SQL Insights (préversion)
description: Créer des alertes avec SQL Insights dans Azure Monitor
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2021
ms.openlocfilehash: bb42f74f6ac8487a93479bdf980c66ef87e8e742
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107726859"
---
# <a name="create-alerts-with-sql-insights-preview"></a>Créer des alertes avec SQL Insights (préversion)
SQL Insights comprend un ensemble de modèles de règles d’alerte que vous pouvez utiliser pour créer des [règles d’alerte dans Azure Monitor](../alert/../alerts/alerts-overview.md) pour les problèmes SQL courants. Les règles d’alerte dans SQL Insights sont des règles d’alerte de journal basées sur les données de performances stockées dans la table *InsightsMetrics* des journaux Azure Monitor.  

> [!NOTE]
> Pour créer une alerte pour SQL Insights à l’aide d’un modèle Resource Manager, consultez [Exemples de modèles Resource Manager pour SQL Insights](resource-manager-sql-insights.md#create-an-alert-rule-for-sql-insights).


> [!NOTE]
> Si vous avez des demandes sur d’autres modèles de règles d’alerte SQL Insights, envoyez vos commentaires en utilisant le lien au bas de cette page ou en utilisant le lien de commentaires sur SQL Insights dans le portail Azure.

## <a name="enable-alert-rules"></a>Activer des règles d’alerte 
Effectuez les étapes suivantes pour activer les alertes dans Azure Monitor à partir du portail Azure.Les règles d’alerte créées seront étendues à toutes les ressources SQL surveillées sous le profil de surveillance sélectionné.  Lorsqu’une règle d’alerte est déclenchée, elle se déclenche sur l’instance ou la base de données SQL spécifique.

> [!NOTE]
> Vous pouvez également créer des [règles d’alerte de journal](../alerts/alerts-log.md) personnalisées en exécutant des requêtes sur les jeux de données de la table *InsightsMetrics*, puis en enregistrant ces requêtes en tant que règle d’alerte. 

Sélectionnez **SQL (préversion)** dans la section **Insights** du menu Azure Monitor du portail Azure. Cliquez sur **Alertes**

:::image type="content" source="media/sql-insights-alerts/alerts-button.png" alt-text="Bouton Alertes":::

Le volet **Alertes** s’ouvre à droite de la page. Par défaut, il affiche les alertes déclenchées pour les ressources SQL dans le profil de surveillance sélectionné en fonction des règles d’alerte que vous avez déjà créées. Sélectionnez **Modèles d’alerte** pour afficher la liste des modèles disponibles que vous pouvez utiliser pour créer une règle d’alerte.

:::image type="content" source="media/sql-insights-alerts/alert-templates.png" alt-text="Modèles d’alerte":::

Sur la page **Créer une règle d’alerte**, passez en revue les paramètres par défaut de la règle et modifiez-les si nécessaire. Vous pouvez également sélectionner un [groupe d’actions](../alerts/action-groups.md) pour créer des notifications et des actions lorsque la règle d’alerte est déclenchée. Cliquez sur **Activer une règle d’alerte** pour créer la règle d’alerte une fois que vous avez vérifié toutes ses propriétés.


:::image type="content" source="media/sql-insights-alerts/alert-rule.png" alt-text="Page Règles d’alerte":::

Pour déployer la règle d’alerte immédiatement, cliquez sur **Déployer une règle d’alerte**. Cliquez sur **Afficher le modèle** si vous souhaitez afficher le modèle de règle avant de le déployer réellement.

:::image type="content" source="media/sql-insights-alerts/alert-rule-deploy.png" alt-text="Déployer une règle d’alerte":::

Si vous choisissez d’afficher les modèles, sélectionnez **Déployer** dans la page du modèle pour créer la règle d’alerte.

:::image type="content" source="media/sql-insights-alerts/view-template-deploy.png" alt-text="Déployer à partir d’un modèle de vue":::


## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les [alertes dans Azure Monitor](../alerts/alerts-overview.md).

