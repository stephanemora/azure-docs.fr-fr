---
title: 'Tutoriel : Configurer l’extension Azure Machine Learning de Visual Studio Code'
titleSuffix: Azure Machine Learning
description: Découvrez comment configurer l’extension Azure Machine Learning de Visual Studio Code.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: luisquintanilla
ms.author: luquinta
ms.date: 01/16/2019
ms.openlocfilehash: 4000fcc80d507d3b1e871d7f3288fc3b77693c76
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/16/2020
ms.locfileid: "76157460"
---
# <a name="set-up-azure-machine-learning-visual-studio-code-extension"></a>Configurer l’extension Azure Machine Learning de Visual Studio Code

Découvrez comment installer et exécuter des scripts à l’aide de l’extension Azure Machine Learning de Visual Studio Code.

Dans ce tutoriel, vous allez apprendre à effectuer les tâches suivantes :

> [!div class="checklist"]
> * Installer l’extension Azure Machine Learning de Visual Studio Code
> * Vous connecter à votre compte Azure à partir de Visual Studio Code
> * Utiliser l’extension Azure Machine Learning pour exécuter un exemple de script

## <a name="prerequisites"></a>Prérequis

- Abonnement Azure. Si vous n’en avez pas, inscrivez-vous ou essayez la [version gratuite ou payante d’Azure Machine Learning](https://aka.ms/AMLFree)
- Installez [Visual Studio Code](https://code.visualstudio.com/docs/setup/setup-overview), éditeur de code léger et multiplateforme 

## <a name="install-the-extension"></a>Installer l’extension

1. Ouvrez Visual Studio Code.
1. Sélectionnez l’icône **Extensions** dans la **Barre d’activité** pour ouvrir la vue Extensions.
1. Dans la vue Extensions, recherchez « Azure Machine Learning ».
1. Sélectionnez **Installer**.

> [!NOTE]
> Vous pouvez également installer l’extension Azure Machine Learning par le biais de Visual Studio Marketplace en [téléchargeant directement le programme d’installation](https://aka.ms/vscodetoolsforai). 

Les autres étapes de ce tutoriel ont été testées avec la **version 0.6.8** de l’extension.

## <a name="sign-in-to-your-azure-account"></a>Vous connecter à votre compte Azure

Pour provisionner des ressources et exécuter des charges de travail sur Azure, vous devez vous connecter avec vos informations d’identification de compte Azure. Pour faciliter la gestion des comptes, Azure Machine Learning installe automatiquement l’extension Azure Account. Visitez le site suivant pour [en savoir plus sur l’extension Azure Account](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account).

1. Ouvrez la palette de commandes en sélectionnant **Affichage > Palette de commandes** dans la barre de menus. 
1. Entrez la commande « Azure: Sign In » dans la zone de texte pour démarrer le processus de connexion.

## <a name="run-a-script-in-azure"></a>Exécuter un script dans Azure

Maintenant que vous êtes connecté à Azure avec vos informations d’identification de compte, effectuez les étapes de cette section afin d’apprendre à utiliser l’extension pour entraîner un modèle Machine Learning.

1. Téléchargez et décompressez le [dépôt VS Code Tools for AI](https://github.com/microsoft/vscode-tools-for-ai/archive/master.zip) n’importe où sur votre ordinateur.
1. Ouvrez le répertoire `mnist-vscode-docs-sample` dans Visual Studio Code.
1. Sélectionnez l’icône **Azure** dans la barre d’activité.
1. Sélectionnez l’icône **Exécuter l’expérience** en haut de la vue Azure Machine Learning.

    > [!div class="mx-imgBorder"]
    > ![Exécuter une expérience](./media/tutorial-setup-vscode-extension/run-experiment.PNG)

1. Une fois la palette de commandes développée, suivez les invites.

    1. Sélectionnez votre abonnement Azure.
    1. Sélectionnez **Créer un espace de travail Azure ML**.
    1. Sélectionnez le type de travail **Entraînement à nœud unique TensorFlow**.
    1. Entrez `train.py` comme script d’entraînement. Il s’agit du fichier qui contient le code d’un modèle Machine Learning qui classifie des images de chiffres manuscrits.
    1. Spécifiez les packages suivants comme prérequis à exécuter.

        ```text
        pip: azureml-defaults; conda: python=3.6.2, tensorflow=1.15.0
        ```

1. À ce stade, un fichier de configuration semblable à celui ci-dessous apparaît dans l’éditeur de texte. La configuration contient les informations requises pour exécuter le travail d’entraînement, comme le fichier qui contient le code pour entraîner le modèle et les dépendances Python spécifiées à l’étape précédente.

    ```json
    {
        "workspace": "WS12191742",
        "resourceGroup": "WS12191742-rg2",
        "location": "South Central US",
        "experiment": "WS12191742-exp2",
        "compute": {
            "name": "WS12191742-com2",
            "vmSize": "Standard_D1_v2, Cores: 1; RAM: 3.5GB;"
        },
        "runConfiguration": {
            "filename": "WS12191742-com2-rc1",
            "condaDependencies": [
                "python=3.6.2",
                "tensorflow=1.15.0"
            ],
            "pipDependencies": [
                "azureml-defaults"
            ]
        }
    }
    ```

1. Sélectionnez **Soumettre l’expérience** pour exécuter votre expérience dans Azure. Le fichier `train.py` et le fichier de configuration sont alors envoyés vers votre espace de travail Azure Machine Learning. Le travail d’entraînement est ensuite démarré sur une ressource de calcul dans Azure.
1. Au bout de quelques minutes, un répertoire nommé `output` et contenant un modèle TensorFlow entraîné est créé localement.

## <a name="next-steps"></a>Étapes suivantes

* [Tutoriel : Entraîner et déployer un modèle de classification d’images TensorFlow à l’aide de l’extension Azure Machine Learning de Visual Studio Code](tutorial-train-deploy-image-classification-model-vscode.md).
