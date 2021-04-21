---
title: Se connecter à l’instance de calcul dans Visual Studio Code (préversion)
titleSuffix: Azure Machine Learning
description: Découvrez comment vous connecter à une instance de calcul Azure Machine Learning dans Visual Studio Code pour exécuter des charges de travail de développement à distance et Jupyter Notebook interactives.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: luquinta
author: luisquintanilla
ms.date: 04/08/2021
ms.openlocfilehash: 14f0d15d48193267c224f3497c24651ca3249b0b
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/07/2021
ms.locfileid: "107028583"
---
# <a name="connect-to-an-azure-machine-learning-compute-instance-in-visual-studio-code-preview"></a>Se connecter à une instance de calcul Azure Machine Learning dans Visual Studio Code (préversion)

Dans cet article, vous allez apprendre à vous connecter à une instance de calcul Azure Machine Learning à l’aide de Visual Studio Code.

Une [Instance de calcul Azure Machine Learning](concept-compute-instance.md) est une station de travail Cloud complètement managée pour les scientifiques des données qui fournit des fonctionnalités de gestion et de préparation de l’entreprise pour les administrateurs informatiques.

Il existe deux façons de se connecter à une instance de calcul à partir de Visual Studio Code :

* Instance de calcul distante. Cette option vous fournit un environnement de développement complet pour créer vos projets Machine Learning.
* Serveur Jupyter Notebook distant. Cette option vous permet de définir une instance de calcul en tant que serveur Jupyter Notebook distant.

## <a name="configure-a-remote-compute-instance"></a>Configurer une instance de calcul distante

Pour configurer une instance de calcul distante pour le développement, vous devez respecter quelques conditions préalables.

* Extension Azure Machine Learning de Visual Studio Code. Pour plus d’informations, consultez le [guide d’installation de l’extension Azure Machine Learning de Visual Studio Code](tutorial-setup-vscode-extension.md).
* Espace de travail Azure Machine Learning. [Utilisez l’extension Azure Machine Learning de Visual Studio Code pour créer un espace de travail](how-to-manage-resources-vscode.md#create-a-workspace) si vous n’en avez pas déjà un.
* Instance de calcul Azure Machine Learning. [Utilisez l’extension Azure Machine Learning Visual Studio Code pour créer une instance de calcul](how-to-manage-resources-vscode.md#create-compute-instance) si vous n’en avez pas.

Pour vous connecter à votre instance de calcul distante :

# <a name="vs-code"></a>[Code Visual Studio](#tab/extension)

### <a name="azure-machine-learning-extension"></a>Extension Azure Machine Learning

1. Dans VS Code, lancez l’extension Azure Machine Learning.
1. Développez le nœud **Instances de calcul** dans votre extension.
1. Cliquez avec le bouton droit sur l’instance de calcul à laquelle vous souhaitez vous connecter, puis sélectionnez **Se connecter à l’instance de calcul**.

:::image type="content" source="media/how-to-set-up-vs-code-remote/vs-code-compute-instance-launch.png" alt-text="Se connecter à l’instance de calcul dans l’extension Visual Studio Code Azure ML" lightbox="media/how-to-set-up-vs-code-remote/vs-code-compute-instance-launch.png":::

### <a name="command-palette"></a>Palette de commandes

1. Dans VS Code, ouvrez la palette de commandes en sélectionnant **Affichage > Palette de commandes**.
1. Entrez **Azure ML : Se connecter à l’instance de calcul** dans la zone de texte.
1. Sélectionnez votre abonnement.
1. Sélectionnez votre espace de travail.
1. Sélectionnez votre instance de calcul ou créez-en une nouvelle.

# <a name="studio"></a>[Studio](#tab/studio)

Accédez à [ml.azure.com](https://ml.azure.com).

> [!IMPORTANT]
> Pour vous connecter à votre instance de calcul distante à partir de Visual Studio Code, assurez-vous que le compte auquel vous êtes connecté dans Azure Machine Learning studio est le même que celui que vous utilisez dans Visual Studio Code.

### <a name="compute"></a>Calcul

1. Sélectionnez l’onglet **Calcul**.
1. Dans la colonne *URI de l’application*, sélectionnez **VS Code** pour l’instance de calcul à laquelle vous souhaitez vous connecter.

:::image type="content" source="media/how-to-set-up-vs-code-remote/studio-compute-instance-vs-code-launch.png" alt-text="Se connecter à l’instance de calcul dans VS Code Azure ML studio" lightbox="media/how-to-set-up-vs-code-remote/studio-compute-instance-vs-code-launch.png":::

### <a name="notebook"></a>Notebook

1. Sélectionnez l’onglet **Notebook**.
1. Dans l’onglet *Notebook*, sélectionnez le fichier que vous souhaitez modifier.
1. Sélectionnez **Éditeurs > Modifier dans VS Code (préversion)** .

:::image type="content" source="media/how-to-set-up-vs-code-remote/studio-notebook-compute-instance-vs-code-launch.png" alt-text="Se connecter à l’instance de calcul dans VS Code Azure ML Notebook" lightbox="media/how-to-set-up-vs-code-remote/studio-notebook-compute-instance-vs-code-launch.png":::

---

Une nouvelle fenêtre s’ouvre pour votre instance de calcul distante. Lorsque vous tentez d’établir une connexion à une instance de calcul distante, les tâches suivantes sont effectuées :

1. Autorisation. Certaines vérifications sont effectuées pour s’assurer que l’utilisateur qui tente d’établir une connexion est autorisé à utiliser l’instance de calcul.
1. VS Code Remote Server est installé sur l’instance de calcul.
1. Une connexion WebSocket est établie pour une interaction en temps réel.

Une fois la connexion établie, elle est persistante. Un jeton est émis au début de la session et est actualisé automatiquement pour maintenir la connexion avec votre instance de calcul.

Après vous être connecté à votre instance de calcul distante, utilisez l’éditeur pour :

* [Créer et gérer des fichiers sur votre instance de calcul distante ou sur le partage de fichiers](https://code.visualstudio.com/docs/editor/codebasics).
* Utiliser le [terminal intégré VS Code](https://code.visualstudio.com/docs/editor/integrated-terminal) afin d’[exécuter des commandes et des applications sur votre instance de calcul distante](how-to-access-terminal.md).
* [Déboguer vos scripts et vos applications](https://code.visualstudio.com/Docs/editor/debugging).
* [Utiliser VS Code pour gérer vos référentiels Git](concept-train-model-git-integration.md).

## <a name="configure-compute-instance-as-remote-notebook-server"></a>Configurer une instance de calcul en tant que serveur de notebook distant

Pour configurer une instance de calcul en tant que serveur Jupyter Notebook distant, vous avez besoin de respecter quelques conditions préalables :

* Extension Azure Machine Learning de Visual Studio Code. Pour plus d’informations, consultez le [guide d’installation de l’extension Azure Machine Learning de Visual Studio Code](tutorial-setup-vscode-extension.md).
* Espace de travail Azure Machine Learning. [Utilisez l’extension Azure Machine Learning de Visual Studio Code pour créer un espace de travail](how-to-manage-resources-vscode.md#create-a-workspace) si vous n’en avez pas déjà un.

Pour se connecter à une instance de calcul :

1. Ouvrez un notebook Jupyter dans Visual Studio Code.
1. Lors du chargement de l’expérience intégrée du notebook, sélectionnez **Serveur Jupyter**.

    > [!div class="mx-imgBorder"]
    > ![Menu déroulant Lancer le serveur Jupyter Notebook distant d'Azure Machine Learning](media/how-to-set-up-vs-code-remote/launch-server-selection-dropdown.png)

    Vous pouvez également utiliser la palette de commandes :

    1. Ouvrez la palette de commandes en sélectionnant **Affichage > Palette de commandes** dans la barre de menus.
    1. Entrez `Azure ML: Connect to Compute instance Jupyter server` dans la zone de texte.

1. Choisissez `Azure ML Compute Instances` dans la liste des options du serveur Jupyter.
1. Sélectionnez votre abonnement dans la liste des abonnements. Si vous avez déjà configuré votre espace de travail Azure Machine Learning par défaut, cette étape est ignorée.
1. Sélectionnez votre espace de travail.
1. Sélectionnez votre instance de calcul dans la liste. Si vous n’en avez pas, sélectionnez **Créer une instance Capacité de calcul ML** et suivez les instructions pour en créer une.
1. Pour que les modifications prennent effet, vous devez recharger Visual Studio Code.
1. Ouvrez un notebook Jupyter et exécutez une cellule.

> [!IMPORTANT]
> Vous **DEVEZ** exécuter une cellule afin d’établir la connexion.

À ce stade, vous pouvez continuer à exécuter des cellules dans votre instance de Jupyter Notebook.

> [!TIP]
> Vous pouvez également utiliser des fichiers de script Python (.py) contenant des cellules de code de type Jupyter. Pour plus d’informations, consultez la [documentation interactive de Visual Studio Code relative à Python](https://code.visualstudio.com/docs/python/jupyter-support-py).

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez configuré Visual Studio Code Remote, vous pouvez utiliser une instance de calcul comme calcul à distance à partir de Visual Studio Code pour [déboguer votre code de manière interactive](how-to-debug-visual-studio-code.md).

[Tutoriel : Effectuer l’apprentissage de votre premier modèle ML](tutorial-1st-experiment-sdk-train.md) montre comment utiliser une instance de calcul avec un notebook intégré.
