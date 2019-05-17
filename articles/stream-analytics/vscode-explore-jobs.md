---
title: Explorez les travaux Azure Stream Analytique avec Visual Studio Code (version préliminaire)
description: Cet article vous montre comment exporter un travail Azure Stream Analytique pour un projet local, répertorier les travaux et afficher les entités de travail.
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.date: 05/15/2019
ms.topic: conceptual
ms.openlocfilehash: 8674d478646c8f9be6b32521c6624752ac6df052
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/17/2019
ms.locfileid: "65827799"
---
# <a name="explore-azure-stream-analytics-with-visual-studio-code-preview"></a>Explorez Analytique Stream Azure avec Visual Studio Code (version préliminaire)

L’Analytique de Stream Azure pour l’extension de Visual Studio Code offre aux développeurs une expérience lightweight pour gérer leur travail Analytique de Stream. Il peut être utilisé sur Windows, Mac et Linux. Avec l’extension Azure Stream Analytique, vous pouvez :

- [Créer](quick-create-vs-code.md), démarrer et arrêter des travaux
- Exporter des travaux existants dans un projet local
- Répertorier les travaux et afficher les entités de travail

## <a name="export-a-job-to-a-local-project"></a>Exporter un travail à un projet local

Pour exporter un travail dans un projet local, recherchez le travail que vous souhaitez exporter dans le **Stream Analytique Explorer** dans Visual Studio Code. Puis sélectionnez un dossier pour votre projet. Le projet est exporté vers le dossier que vous sélectionnez, et vous pouvez continuer à gérer le travail à partir de Visual Studio Code. Pour plus d’informations sur l’utilisation de Visual Studio Code pour gérer les travaux d’Analytique de Stream, consultez Visual Studio Code [quickstart](quick-create-vs-code.md).

![Exporter le travail ASA dans Visual Studio Code](./media/vscode-explore-jobs/export-job.png)

## <a name="list-job-and-view-job-entities"></a>Répertorier les travaux et afficher les entités de travail

Vous pouvez utiliser la vue des tâches pour interagir avec les tâches Azure Stream Analytics à partir de Visual Studio.


1. Cliquez sur le **Azure** icône sur la barre d’activités Visual Studio Code, puis développez **nœud de Stream Analytique**. Vos travaux doivent apparaître sous vos abonnements.

   ![Explorateur d’Analytique Open Stream](./media/vscode-explore-jobs/open-explorer.png)

2. Développez le nœud de votre projet, vous pouvez ouvrir et afficher la requête de travail, configuration, les entrées, sorties et fonctions. 

3. Cliquez avec le bouton droit sur le nœud de votre travail, puis choisissez le **ouvrir la vue de travail dans le portail** un nœud pour ouvrir la vue des travaux dans le portail Azure.

   ![Vue du travail en cours dans le portail](./media/vscode-explore-jobs/open-job-view.png)

## <a name="next-steps"></a>Étapes suivantes

* [Créez un travail de cloud d’Azure Stream Analytique dans Visual Studio Code (version préliminaire)](quick-create-vs-code.md)
