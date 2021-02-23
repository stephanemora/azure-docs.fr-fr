---
title: Visualisations pour Analyse des changements d’application - Azure Monitor
description: Découvrez comment utiliser des visualisations dans Analyse des changements d’application au sein d’Azure Monitor.
ms.topic: conceptual
author: cawams
ms.author: cawa
ms.date: 02/11/2021
ms.openlocfilehash: 643645eb3b361cd68def1177ba7e8af89f1963bc
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100520754"
---
# <a name="visualizations-for-application-change-analysis-preview"></a>Visualisations pour Analyse des changements d’application (préversion)

## <a name="standalone-ui"></a>Interface utilisateur autonome

Il existe dans Azure Monitor un volet autonome pour l’Analyse des changements qui permet d’afficher toutes les modifications avec des insights dans les ressources et les dépendances de l’application.

Recherchez Analyse des changements dans la barre de recherche du portail Azure pour lancer l’expérience.

![Capture d’écran de la recherche de l’Analyse des changements sur le Portail Azure](./media/change-analysis/search-change-analysis.png)

Toutes les ressources sous un abonnement sélectionné s’affichent avec les modifications effectuées au cours des dernières 24 heures. Pour optimiser les performances de chargement de la page, le service affiche 10 ressources à la fois. Sélectionnez la page suivante pour afficher plus de ressources. Nous travaillons actuellement à la suppression de cette limitation.

![Capture d’écran du panneau Analyse des changements sur le Portail Azure](./media/change-analysis/change-analysis-standalone-blade.png)

Cliquez sur une ressource pour afficher toutes ses modifications. Au besoin, explorez une modification pour afficher les détails et les insights de cette modification au format json.

![Capture d’écran des détails de la modification](./media/change-analysis/change-details.png)

Pour tout commentaire, utilisez le bouton d’envoi de commentaires ou envoyez un e-mail à l’adresse changeanalysisteam@microsoft.com.

![Capture d’écran du bouton d’envoi de commentaires sous l’onglet Analyse des changements](./media/change-analysis/change-analysis-feedback.png)

### <a name="multiple-subscription-support"></a>Prise en charge d'abonnements multiples

L’interface utilisateur prend en charge la sélection de plusieurs abonnements pour afficher les changements de ressources. Utiliser le filtre d’abonnement :

![Capture d’écran de filtre d’abonnement prenant en charge la sélection de plusieurs abonnements](./media/change-analysis/multiple-subscriptions-support.png)

### <a name="web-app-diagnose-and-solve-problems"></a>Diagnostiquer et résoudre les problèmes d’application web

Dans Azure Monitor, l’Analyse des changements est également intégrée à l’expérience en libre-service **Diagnostiquer et résoudre les problèmes**. Accédez à cette expérience à partir de la page **Vue d’ensemble** de votre application App Service.

![Capture d’écran du bouton « Vue d’ensemble » et du bouton « Diagnostiquer et résoudre les problèmes »](./media/change-analysis/change-analysis.png)

## <a name="application-change-analysis-in-the-diagnose-and-solve-problems-tool"></a>Analyse des changements d’application dans l’outil Diagnostiquer et résoudre les problèmes

L’analyse des changements d’application est un détecteur autonome dans les outils de diagnostic et de résolution des problèmes d’application web. Il est également agrégé dans **Blocages de l’application** et les **détecteurs d’application web arrêtée**. À mesure que vous entrez dans l’outil Diagnostiquer et résoudre les problèmes, le fournisseur de ressources **Microsoft.ChangeAnalysis** est automatiquement enregistré. Suivez ces instructions pour activer le suivi des modifications d’une application web dans l’invité.

1. Sélectionnez **Disponibilité et performances**.

    ![Capture d’écran des options de résolution des problèmes « Disponibilité et performances »](./media/change-analysis/availability-and-performance.png)

2. Cliquez sur **Changements d’application**. Cette fonctionnalité est également disponible dans **Blocages de l’application**.

   ![Capture d’écran du bouton « Blocage de l’application »](./media/change-analysis/application-changes.png)

3. Le lien dirige vers l’interface utilisateur Analyse des changements d’application à l’échelle de l’application web. Si le suivi des modifications dans l’invité de l’application web n’est pas activé, suivez la bannière pour accéder aux modifications apportées aux paramètres de fichier et d’application.

   ![Capture d’écran des options de « Blocage de l’application »](./media/change-analysis/enable-changeanalysis.png)

4. Activez **Analyse des changements** et sélectionnez **Enregistrer**. L’outil affiche toutes les applications web dans le cadre d’un plan App Service. Vous pouvez utiliser le commutateur de niveau de plan afin d’activer l’analyse des modifications pour toutes les applications web dans le cadre d’un plan.

    ![Capture d’écran de l’interface utilisateur « Activer l’analyse des changements »](./media/change-analysis/change-analysis-on.png)

5. Les données modifiées sont également disponibles dans les détecteurs d’**application web arrêtée** et **d’incident de l’application**. Vous verrez un graphique qui résume les types de modifications au fil du temps ainsi que des détails sur ces modifications. Par défaut, les modifications effectuées au cours des dernières 24 heures s’affichent pour vous aider à résoudre des problèmes immédiats.

     ![Capture d’écran de la vue de comparaison des modifications](./media/change-analysis/change-view.png)

## <a name="virtual-machine-diagnose-and-solve-problems"></a>Diagnostiquer et résoudre les problèmes d’une machine virtuelle

Accédez à l’outil Diagnostiquer et résoudre les problèmes d’une machine virtuelle.  Accédez à **Outils de résolution des problèmes**, parcourez la page et sélectionnez **Analyser les changements récents** pour afficher les changements sur la machine virtuelle.

![Capture d’écran de Diagnostiquer et résoudre les problèmes d’une machine virtuelle](./media/change-analysis/vm-dnsp-troubleshootingtools.png)

![Analyseur des changements dans les outils de dépannage](./media/change-analysis/analyze-recent-changes.png)

## <a name="activity-log-change-history"></a>Journal d’activité - Historique des modifications

La fonctionnalité [Affichage de l’historique des modifications](../platform/activity-log.md#view-change-history) dans le Journal d’activité demande au serveur principal du service Analyse des changements d’application d’obtenir les modifications associées à une opération. L’**historique des modifications** appelait directement [Azure Resource Graph](../../governance/resource-graph/overview.md), mais demandait au serveur principal d’appeler Analyse des changements d’application de sorte que les modifications retournées incluent les modifications au niveau des ressources d’[Azure Resource Graph](../../governance/resource-graph/overview.md), les propriétés de ressource d’[Azure Resource Manager](../../azure-resource-manager/management/overview.md) et les modifications dans l’invité de services PaaS tels que l’application web App Services. Pour que le service Analyse des changements d’application puisse analyser les modifications apportées aux abonnements des utilisateurs, un fournisseur de ressources doit être inscrit. La première fois que vous accédez à l’onglet **Historique des modifications**, l’outil commence automatiquement à inscrire le fournisseur de ressources **Microsoft.ChangeAnalysis**. Une fois inscrit, les modifications d’**Azure Resource Graph** sont immédiatement disponibles et couvrent les 14 derniers jours. Les modifications d’autres sources sont disponibles 4 heures environ après l’intégration de l’abonnement.

![Journal d’activité : Intégration de l’historique des modifications](./media/change-analysis/activity-log-change-history.png)

## <a name="vm-insights-integration"></a>Intégration de VM Insights

Les utilisateurs disposant de [VM Insights](../insights/vminsights-overview.md) activé peuvent voir ce qui a changé dans leurs machines virtuelles au point d’avoir entraîné des pics dans un graphique de métriques, comme le processeur ou la mémoire. Les données modifiées sont intégrées à la barre de navigation côté VM Insights. L’utilisateur peut voir si des modifications sont apportées à la machine virtuelle, puis sélectionner **Examiner les modifications** pour afficher les détails des modifications dans l’interface utilisateur autonome d’Analyse des changements d’application.

[![Intégration de VM Insights](./media/change-analysis/vm-insights.png)](./media/change-analysis/vm-insights.png#lightbox)

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [résoudre les problèmes liés à l’Analyse des changements](change-analysis-troubleshoot.md).
