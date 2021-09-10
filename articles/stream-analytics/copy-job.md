---
title: Copier, sauvegarder et déplacer vos travaux Azure Stream Analytics entre les régions
description: Cet article explique comment déplacer, copier et sauvegarder un travail Azure Stream Analytics.
author: su-jie
ms.author: sujie
ms.service: stream-analytics
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 09/11/2019
ms.openlocfilehash: 7b3d203d3d34612b3495fa3eceaf13e357eaa2be
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122829405"
---
# <a name="copy-back-up-and-move-your-azure-stream-analytics-jobs-between-regions"></a>Copier, sauvegarder et déplacer vos travaux Azure Stream Analytics entre les régions

Vous pouvez copier ou sauvegarder vos travaux Azure Stream Analytics déployés à l’aide de Visual Studio Code ou de Visual Studio. La copie d’un travail vers une autre région n’a pas pour effet de copie la dernière heure de sortie. Par conséquent, vous ne pouvez pas utiliser l’option [**Lors du dernier arrêt**](./start-job.md#start-options) lors du démarrage du travail copié.

## <a name="before-you-begin"></a>Avant de commencer
* Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/).

* Connectez-vous au [portail Azure](https://portal.azure.com/).

* Installez [l’extension Azure Stream Analytics pour Visual Studio Code](quick-create-visual-studio-code.md#install-the-azure-stream-analytics-tools-extension) ou [les outils Azure Stream Analytics pour Visual Studio](quick-create-visual-studio-code.md#install-the-azure-stream-analytics-tools-extension).  

## <a name="visual-studio-code"></a>Visual Studio Code

1. Cliquez sur l’icône **Azure** située sur la barre d’activités Visual Studio Code, puis développez le nœud **Stream Analytics**. Vos travaux doivent apparaître sous vos abonnements.

   ![Ouvrir l’Explorateur Stream Analytics](./media/vscode-explore-jobs/open-explorer.png)

2. Pour exporter un travail vers un projet local, recherchez le travail que vous souhaitez exporter dans l’**Explorateur Stream Analytics** de Visual Studio Code. Puis, sélectionnez un dossier pour votre projet.

    ![Localiser le travail ASA dans Visual Studio Code](./media/vscode-explore-jobs/export-job.png)

    Le projet est exporté vers le dossier que vous sélectionnez et est ajouté à votre espace de travail actuel.

3. Pour publier le travail dans une autre région ou dans une sauvegarde à l’aide d’un autre nom, sélectionnez **Select from your subscriptions to publish** (sélectionner parmi vos abonnements pour publier) dans l’éditeur de requête (\*.asaql) et suivez les instructions.

    ![Publier sur Azure dans Visual Studio Code](./media/quick-create-visual-studio-code/submit-job.png)

## <a name="visual-studio"></a>Visual Studio

1. Suivez les instructions [Exporter une tâche Azure Stream Analytics déployée vers un projet](./stream-analytics-vs-tools.md#export-jobs-to-a-project).

2. Ouvrez le fichier \*.asaql dans l’éditeur de requête, sélectionnez **Envoyer sur Azure** dans l’éditeur de script et suivez les instructions pour publier le travail dans une autre région ou une sauvegarde en utilisant un nouveau nom.

## <a name="next-steps"></a>Étapes suivantes

* [Démarrage rapide : créer un travail Stream Analytics à l’aide de Visual Studio Code](quick-create-visual-studio-code.md)
* [Démarrage rapide : créer un travail Stream Analytics à l’aide de Visual Studio](stream-analytics-quick-create-vs.md)