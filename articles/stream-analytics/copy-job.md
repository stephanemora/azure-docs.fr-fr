---
title: Copier ou sauvegarder des travaux Azure Stream Analytics
description: Cet article explique comment copier ou sauvegarder un travail Azure Stream Analytics.
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 09/11/2019
ms.openlocfilehash: ba63358347cf9722d2cafa35598b9b3b37f49dc3
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93129454"
---
# <a name="copy-or-back-up-azure-stream-analytics-jobs"></a>Copier ou sauvegarder des travaux Azure Stream Analytics

Vous pouvez copier ou sauvegarder vos travaux Azure Stream Analytics déployés à l’aide de Visual Studio Code ou de Visual Studio. La copie d’un travail vers une autre région n’a pas pour effet de copie la dernière heure de sortie. Par conséquent, vous ne pouvez pas utiliser l’option [**Lors du dernier arrêt**](./start-job.md#start-options) lors du démarrage du travail copié.

## <a name="before-you-begin"></a>Avant de commencer
* Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/).

* Connectez-vous au [portail Azure](https://portal.azure.com/).

* Installez [l’extension Azure Stream Analytics pour Visual Studio Code](quick-create-visual-studio-code.md#install-the-azure-stream-analytics-tools-extension) ou [les outils Azure Stream Analytics pour Visual Studio](quick-create-visual-studio-code.md#install-the-azure-stream-analytics-tools-extension).  

## <a name="visual-studio-code"></a>Visual Studio Code

1. Cliquez sur l’icône **Azure** située sur la barre d’activités Visual Studio Code, puis développez le nœud **Stream Analytics**. Vos travaux doivent apparaître sous vos abonnements.

   ![Ouvrir l’Explorateur Stream Analytics](./media/vscode-explore-jobs/open-explorer.png)

2. Pour exporter un travail vers un projet local, recherchez le travail que vous souhaitez exporter dans l’ **Explorateur Stream Analytics** de Visual Studio Code. Puis, sélectionnez un dossier pour votre projet.

    ![Localiser le travail ASA dans Visual Studio Code](./media/vscode-explore-jobs/export-job.png)

    Le projet est exporté vers le dossier que vous sélectionnez et est ajouté à votre espace de travail actuel.

    ![Exporter le travail ASA dans Visual Studio Code](./media/stream-analytics-manage-job/copy-backup-stream-analytics-jobs.png)

3. Pour publier le travail dans une autre région ou dans une sauvegarde à l’aide d’un autre nom, sélectionnez **Select from your subscriptions to publish** (sélectionner parmi vos abonnements pour publier) dans l’éditeur de requête (\*.asaql) et suivez les instructions.

    ![Publier sur Azure dans Visual Studio Code](./media/quick-create-visual-studio-code/submit-job.png)

## <a name="visual-studio"></a>Visual Studio

1. Suivez les instructions [Exporter une tâche Azure Stream Analytics déployée vers un projet](./stream-analytics-vs-tools.md#export-jobs-to-a-project).

2. Ouvrez le fichier \*.asaql dans l’éditeur de requête, sélectionnez **Envoyer sur Azure** dans l’éditeur de script et suivez les instructions pour publier le travail dans une autre région ou une sauvegarde en utilisant un nouveau nom.

## <a name="next-steps"></a>Étapes suivantes

* [Démarrage rapide : créer un travail Stream Analytics à l’aide de Visual Studio Code](quick-create-visual-studio-code.md)
* [Démarrage rapide : créer un travail Stream Analytics à l’aide de Visual Studio](stream-analytics-quick-create-vs.md)
* [Déployer un travail Azure Stream Analytics avec CI/CD à l’aide de Azure Pipelines](stream-analytics-tools-visual-studio-cicd-vsts.md)