---
title: Explorer des travaux Azure Stream Analytics dans Visual Studio Code
description: Cet article vous montre comment exporter un travail Azure Stream Analytics vers un projet local, répertorier des travaux et afficher des entités de travail.
ms.service: stream-analytics
author: su-jie
ms.author: sujie
ms.date: 07/21/2021
ms.topic: how-to
ms.openlocfilehash: 85b6dece608f47818f2115d0462dd4a56752581f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122532783"
---
# <a name="explore-azure-stream-analytics-with-visual-studio-code-preview"></a>Explorer Azure Stream Analytics avec Visual Studio Code (préversion)

L’extension Azure Stream Analytics pour Visual Studio Code permet aux développeurs de gérer facilement leurs travaux Stream Analytics. Elle peut être utilisée sur Windows, Mac et Linux. Cette extension Azure Stream Analytics permet de réaliser les actions suivantes :

- [Créer](quick-create-visual-studio-code.md), lancer et arrêter des travaux
- Exporter des travaux existants vers un projet local
- Répertorier les travaux et afficher les entités de travail
- Afficher le diagramme de travail et déboguer dans Moniteur de travaux

## <a name="export-a-job-to-a-local-project"></a>Exporter un travail vers un projet local

Pour exporter un travail vers un projet local, recherchez le travail que vous souhaitez exporter dans l’**Explorateur Stream Analytics** de Visual Studio Code. Puis, sélectionnez un dossier pour votre projet. Le projet est exporté vers le dossier sélectionné et vous pouvez continuer à gérer le travail à partir de Visual Studio Code. Pour plus d’informations sur l’utilisation de Visual Studio Code pour gérer les travaux Stream Analytics, consultez le [démarrage rapide](quick-create-visual-studio-code.md) de Visual Studio Code.

![Exporter le travail ASA dans Visual Studio Code](./media/vscode-explore-jobs/export-job.png)

## <a name="list-job-and-view-job-entities"></a>Répertorier les travaux et afficher les entités de travail

Vous pouvez utiliser la vue des tâches pour interagir avec les tâches Azure Stream Analytics à partir de Visual Studio.


1. Cliquez sur l’icône **Azure** situé sur la barre d’activités Visual Studio Code, puis développez le **nœud Stream Analytics**. Vos travaux doivent apparaître sous vos abonnements.

   ![Ouvrir l’Explorateur Stream Analytics](./media/vscode-explore-jobs/open-explorer.png)

2. Développez le nœud de votre travail. Vous pouvez ouvrir et afficher la requête, la configuration, les entrées, les sorties et les fonctions du travail. 

3. Cliquez avec le bouton droit sur le nœud de votre travail, puis choisissez le nœud **Ouvrir la vue des travaux dans le portail** pour ouvrir la vue des travaux dans le portail Azure.

   ![Ouvrir la vue des travaux dans le portail](./media/vscode-explore-jobs/open-job-view.png)

## <a name="view-job-diagram-and-debug-in-job-monitor"></a>Afficher le diagramme de travail et déboguer dans Moniteur de travaux

Vous pouvez utiliser Moniteur de travaux dans Visual Studio Code pour afficher et dépanner vos travaux Azure Stream Analytics.

### <a name="view-job-diagram-and-job-summary"></a>Afficher le diagramme de travail et le résumé du travail
1. Sélectionnez **Moniteur de travaux**. Votre moniteur de travaux doit s’afficher, et le diagramme de travail doit être chargé automatiquement.
   
   ![Ouvrir Moniteur de travaux](./media/vscode-explore-jobs/open-job-monitor.png)

2.  Vous pouvez afficher le diagramme de votre travail et cliquer sur **Résumé du travail** pour afficher les propriétés et les informations de votre travail. 

      ![Afficher le résumé du travail](./media/vscode-explore-jobs/view-jobs-summary.png)

3.  Vous pouvez cliquer sur le bouton **Tester la connexion** pour tester la connexion à vos entrées et sorties.

      ![Tester la connexion](./media/vscode-explore-jobs/test-connection.png)

4.  Vous pouvez également cliquer sur le bouton **Localiser le script** pour afficher votre requête.
   
      ![Afficher la requête](./media/vscode-explore-jobs/view-query.png)

### <a name="monitor-and-debug-with-metrics"></a>Superviser et déboguer à l’aide de Métriques

1.  Cliquez sur le bouton fléché pour ouvrir le panneau Métriques.

      ![Ouvrir le panneau Métriques](./media/vscode-explore-jobs/open-metrics-panel.png)

2.  Vous pouvez interagir avec ce panneau et analyser votre travail à l’aide des principales métriques affichées dans le graphique. Vous pouvez choisir d’afficher les métriques au niveau du travail ou les métriques au niveau des nœuds. Vous pouvez également décider quelles métriques vous souhaitez voir apparaître dans le graphique.

      ![Afficher les métriques](./media/vscode-explore-jobs/view-metrics.png)

### <a name="debug-with-diagnostic-logs-and-activity-logs"></a>Déboguer avec les journaux de diagnostic et les journaux d’activité

Vous pouvez afficher les journaux de diagnostic et les journaux d’activité de votre travail pour la résolution des problèmes.

1. Sélectionnez l’onglet **Journaux de diagnostic**.

   ![Afficher les journaux de diagnostic](./media/vscode-explore-jobs/view-diagnostic-log.png)

2. Sélectionnez l’onglet **Journaux d’activité**. 

   ![Afficher les journaux d’activité](./media/vscode-explore-jobs/view-activity-logs.png)

## <a name="next-steps"></a>Étapes suivantes

* [Créer un travail cloud Azure Stream Analytics dans Visual Studio Code (préversion)](quick-create-visual-studio-code.md)
