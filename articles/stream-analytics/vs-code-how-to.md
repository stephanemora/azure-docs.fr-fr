---
title: Explorez Analytique Stream Azure avec Visual Studio Code (version préliminaire)
description: Cet article vous montre comment exporter un travail Azure Stream Analytique à un projet local, la liste des travaux et afficher les entités de travail et configurer un pipeline CI/CD pour votre travail Stream Analytique.
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.date: 05/06/2019
ms.topic: conceptual
ms.openlocfilehash: 640a81fcb94194e2e907e9339f016bd35e279fdd
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65079209"
---
# <a name="explore-azure-stream-analytics-with-visual-studio-code-preview"></a>Explorez Analytique Stream Azure avec Visual Studio Code (version préliminaire)

L’Analytique de Stream Azure pour l’extension de Visual Studio Code offre aux développeurs une expérience lightweight pour gérer leur travail Analytique de Stream. Avec l’extension Azure Stream Analytique, vous pouvez :

- [Créer](quick-create-vs-code.md), démarrer et arrêter des travaux
- Exporter des travaux existants dans un projet local
- Exécuter des requêtes localement
- Configurer un pipeline CI/CD

## <a name="export-a-job-to-a-local-project"></a>Exporter un travail à un projet local

Pour exporter un travail dans un projet local, recherchez le travail que vous souhaitez exporter dans le **Stream Analytique Explorer** dans Visual Studio code. Puis sélectionnez un dossier pour votre projet. Le projet est exporté vers le dossier que vous sélectionnez, et vous pouvez continuer à gérer le travail à partir de Visual Studio Code. Pour plus d’informations sur l’utilisation de Visual Studio Code pour gérer les travaux d’Analytique de Stream, consultez Visual Studio Code [quickstart](quick-create-vs-code.md).

![Exporter le travail ASA dans Visual Studio Code](./media/vs-code-how-to/export-job.png)

## <a name="run-queries-locally"></a>Exécuter des requêtes localement

Vous pouvez utiliser l’extension d’Azure Stream Analytique pour Visual Studio Code pour tester vos travaux Stream Analytique localement à l’aide d’exemples de données.

1. Une fois que vous avez créé votre travail Analytique de Stream, utilisez **Ctrl + Maj + P** pour ouvrir la palette de commandes. Puis tapez et sélectionnez **ASA : Ajouter une entrée**.

    ![Ajouter une entrée de ASA dans Visual Studio code](./media/vs-code-how-to/add-input.png)

2. Sélectionnez **entrée locale**.

    ![Ajouter une entrée locale ASA dans Visual Studio code](./media/vs-code-how-to/add-local-input.png)

3. Sélectionnez **+ nouvelle entrée Local**.

    ![Ajouter un nouveau ASA local d’entrée dans le code de Visual Studio](./media/vs-code-how-to/add-new-local-input.png)

4. Entrez le même alias d’entrée que vous avez utilisé dans votre requête.

    ![Ajouter un nouvel alias d’entrée local ASA](./media/vs-code-how-to/new-local-input-alias.png)

5. Dans le **LocalInput_DefaultLocalStream.json** de fichier, entrez le chemin d’accès du fichier où se trouve votre fichier de données local.

    ![Entrez le chemin d’accès du fichier local dans Visual Studio](./media/vs-code-how-to/local-file-path.png)

6. Revenez à votre éditeur de requête, puis cliquez **exécuter localement**.

    ![Sélectionnez exécuter localement dans l’éditeur de requête](./media/vs-code-how-to/run-locally.png)

## <a name="set-up-a-cicd-pipeline"></a>Configurer un pipeline CI/CD

Vous pouvez activer l’intégration continue et déploiement pour les travaux Azure Stream Analytique à l’aide de la **asa-cicd outils** package NPM. Le package NPM fournit les outils pour le déploiement automatique des projets de Stream Analytique Visual Studio Code. Il peut être utilisé sur Windows, macOS et Linux sans installer Visual Studio Code.

Une fois que vous avez [téléchargé le package](https://usqldownload.blob.core.windows.net/ext/asa/asa-cicd-0.0.1-preview-beta.tar), utilisez la commande suivante pour générer les modèles Azure Resource Manager. Si le **outputPath** n’est pas spécifié, les modèles seront placés dans le **déployer** dossier sous le projet **bin** dossier.

```powershell
asa-cicd build -scriptPath <scriptFullPath> -outputPath <outputPath>
```

## <a name="next-steps"></a>Étapes suivantes

* [Créez un travail de cloud d’Azure Stream Analytique dans Visual Studio Code (version préliminaire)](quick-create-vs-code.md)