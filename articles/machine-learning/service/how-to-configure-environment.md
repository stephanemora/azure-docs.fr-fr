---
title: Configurer un environnement de développement pour Azure Machine Learning | Microsoft Docs
description: Découvrez comment configurer un environnement de développement quand vous travaillez avec le service Azure Machine Learning. Dans ce document, vous allez découvrir comment utiliser des environnements Conda, créer des fichiers de configuration et configurer des blocs-notes Jupyter, des blocs-notes Azure, des IDE, des éditeurs de code et Data Science Virtual Machine.
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.component: core
ms.reviewer: larryfr
manager: cgronlun
ms.topic: conceptual
ms.date: 8/6/2018
ms.openlocfilehash: 657a762874f7c2fb40553552ef6c17d9b5b6da0f
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/24/2018
ms.locfileid: "49958616"
---
# <a name="configure-a-development-environment-for-azure-machine-learning"></a>Configurer un environnement de développement pour Azure Machine Learning

Cet article vous explique comment configurer un environnement de développement pour fonctionner avec le service Azure Machine Learning, notamment :

- Comment créer un fichier de configuration qui associe votre environnement à un espace de travail du service Azure Machine Learning.
- Comment configurer les environnements de développement suivants :
  - Blocs-notes Jupyter Notebook sur votre ordinateur
  - Visual Studio Code
  - Éditeur de code personnalisé
- Comment configurer un [environnement virtuel conda](https://conda.io/docs/user-guide/tasks/manage-environments.html) et l’utiliser pour Azure Machine Learning. Nous vous recommandons d’utiliser Continuum Anaconda pour isoler votre environnement de travail afin d’éviter les conflits de dépendance entre les packages.

## <a name="prerequisites"></a>Prérequis

- Configurez un espace de travail de service Microsoft Azure Machine Learning. Suivez les étapes décrites dans [Démarrage rapide : bien démarrer avec le service Azure Machine Learning](quickstart-get-started.md).
- Installez le gestionnaire de package [Continuum Anaconda](https://www.anaconda.com/download/) ou [Miniconda](https://conda.io/miniconda.html).
- Si vous utilisez Visual Studio Code, récupérez [l’extension Python](https://code.visualstudio.com/docs/python/python-tutorial).

> [!NOTE]
> Vous pouvez tester les commandes de l’interpréteur de commandes présentées dans cet article à l’aide de Bash (dans Linux et Mac OS) ou de l’invite de commandes (dans Windows).

## <a name="create-a-workspace-configuration-file"></a>Créer un fichier de configuration d’espace de travail

Le Kit de développement logiciel (SDK) Azure Machine Learning utilise le fichier de configuration d’espace de travail pour communiquer avec votre espace de travail du service Azure Machine Learning.

- Pour créer le fichier de configuration, suivez le [Guide de démarrage rapide d’Azure Machine Learning](quickstart-get-started.md).
  - Le processus de démarrage rapide permet de créer un fichier `config.json` dans Azure Notebooks. Ce fichier contient les informations de configuration pour votre espace de travail.
  - Téléchargez ou copiez le fichier `config.json` dans le même répertoire que les scripts ou les blocs-notes qui le référencent.

- Vous pouvez également créer manuellement le fichier en procédant comme suit :

    1. Ouvrez votre espace de travail dans le [portail Azure](https://portal.azure.com). Copie le __Nom de l’espace de travail__, le __Groupe de ressources__ et l’__ID de l’abonnement__. Ces valeurs servent à créer le fichier de configuration.
        ![Portail Azure](./media/how-to-configure-environment/configure.png)

    1. Créez le fichier avec le code Python suivant et veillez à exécuter le code dans le même répertoire que les scripts ou les blocs-notes qui référencent l’espace de travail :

        ```python
        from azureml.core import Workspace

        subscription_id ='<subscription-id>'
        resource_group ='<resource-group>'
        workspace_name = '<workspace-name>'

        try:
           ws = Workspace(subscription_id = subscription_id, resource_group = resource_group, workspace_name = workspace_name)
           ws.write_config()
           print('Library configuration succeeded')
        except:
           print('Workspace not found')
        ```
        Le code écrit le fichier `aml_config/config.json` suivant :

        ```json
        {
        "subscription_id": "<subscription-id>",
        "resource_group": "<resource-group>",
        "workspace_name": "<workspace-name>"
        }
        ```
        Vous pouvez copier le répertoire `aml_config` ou seulement le fichier `config.json` dans n’importe quel autre répertoire référençant l’espace de travail.

       > [!NOTE]
       > Les autres scripts ou blocs-notes présents dans le même répertoire ou sous celui-ci chargent l’espace de travail avec `ws=Workspace.from_config()`.

## <a name="azure-notebooks-and-data-science-virtual-machines"></a>Azure Notebooks et Data Science Virtual Machines

Azure Notebooks et Azure Data Science Virtual Machines (DSVM) sont fournis configurés pour fonctionner avec le service Azure Machine Learning. Ces environnements incluent les composants requis, tels que le Kit de développement logiciel (SDK) Azure Machine Learning.

- Azure Notebooks est un service de blocs-notes Jupyter dans le cloud Azure.
- Data Science Virtual Machine est une image de machine virtuelle personnalisée qui est conçue pour le travail de science des données. Il inclut :
  - des outils courants ;
  - des environnements de développement intégrés ;
  - des packages tels que Jupyter Notebooks, PyCharm et Tensorflow.
- Vous avez toujours besoin d’un fichier de configuration d’espace de travail pour utiliser ces environnements.

Pour obtenir un exemple d’utilisation d’Azure Notebooks avec le service Azure Machine Learning, consultez le document [Bien démarrer avec le service Azure Machine Learning](quickstart-get-started.md).

Pour plus d’informations sur Data Science Virtual Machines, consultez [Data Science Virtual Machines](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/).

## <a name="configure-jupyter-notebooks-on-your-computer"></a>Configurer des blocs-notes Jupyter Notebook sur votre ordinateur

1. Ouvrez une invite de commandes ou un interpréteur de commandes.

1. Créez un environnement conda avec les commandes suivantes :

    ```shell
    # create a new conda environment with Python 3.6, numpy, and cython
    conda create -n myenv Python=3.6 cython numpy

    # activate the conda environment
    conda activate myenv

    # On Mac OS run
    source activate myenv
    ```

    La création de l’environnement peut prendre plusieurs minutes si Python 3.6 et d’autres composants doivent être téléchargés.

1. Installez le Kit de développement logiciel (SDK) Azure Machine Learning avec des fonctionnalités supplémentaires de blocs-notes et le Kit de développement logiciel (SDK) de préparation des données à l’aide de la commande suivante :

     ```shell
    pip install --upgrade azureml-sdk[notebooks,automl] azureml-dataprep
    ```

    > [!NOTE]
    > Si vous recevez un message indiquant que `PyYAML` ne peut pas être désinstallé, utilisez plutôt la commande suivante :
    >
    > `pip install --upgrade azureml-sdk[notebooks,automl] azureml-dataprep --ignore-installed PyYAML`

    L’installation du Kit de développement logiciel (SDK) peut prendre plusieurs minutes.

1. Installez des packages pour votre expérimentation Machine Learning. Utilisez la commande suivante et remplacez `<new package>` par le package que vous souhaitez installer :

    ```shell
    conda install <new package>
    ```

1. Installez un serveur Jupyter Notebook prenant en charge conda et activez des widgets d’expérimentation (afin de visualiser les informations sur les exécutions). Utilisez les commandes suivantes :

    ```shell
    # install Jupyter
    conda install nb_conda

    # install experiment widget
    jupyter nbextension install --py --user azureml.train.widgets

    # enable experiment widget
    jupyter nbextension enable --py --user azureml.train.widgets
    ```

1. Ouvrez Jupyter Notebook à l’aide de la commande suivante :

    ```shell
    jupyter notebook
    ```

1. Ouvrez un nouveau bloc-notes, sélectionnez « myenv » en tant que noyau, puis vérifiez que le Kit de développement logiciel (SDK) Azure Machine Learning est installé. Exécutez la commande suivante dans une cellule de bloc-notes :

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

## <a name="configure-visual-studio-code"></a>Configurer Visual Studio Code

1. Ouvrez une invite de commandes ou un interpréteur de commandes.

1. Créez un environnement conda avec les commandes suivantes :

    ```shell
    # create a new conda environment with Python 3.6, numpy and cython
    conda create -n myenv Python=3.6 cython numpy

    # activate the conda environment
    conda activate myenv

    # If you are running Mac OS you should run
    source activate myenv
    ```

1. Installez le Kit de développement logiciel (SDK) Azure Machine Learning et le Kit de développement logiciel (SDK) de préparation des données à l’aide de la commande suivante :

    ```shell
    pip install --upgrade azureml-sdk[automl] azureml-dataprep
    ```

1. Installez l’extension Visual Studio Code Tools for AI. Consultez la page [Visual Studio Code Tools for AI](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai).

1. Installez des packages pour votre expérimentation Machine Learning. Utilisez la commande suivante et remplacez `<new package>` par le package que vous souhaitez installer :

    ```shell
    conda install <new package>
    ```

1. Ouvrez Visual Studio Code, puis utilisez **Ctrl-Maj-P** (dans Windows) ou **Commande-Maj-P** (dans Mac OS) pour obtenir la **palette de commandes**. Entrez _Python: Select Interpreter_, puis sélectionnez l’environnement conda que vous avez créé.

   > [!NOTE]
   > Visual Studio Code prend automatiquement en charge les environnements conda sur votre ordinateur. Pour plus d’informations, consultez la [documentation de Visual Studio Code](https://code.visualstudio.com/docs/python/environments#_conda-environments).

1. Validez la configuration à l’aide de Visual Studio Code afin de créer un fichier de script Python avec le code suivant, puis exécutez-le :

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

## <a name="configure-a-custom-code-editor"></a>Configuration d’un éditeur de code personnalisé

Vous pouvez utiliser l’éditeur de code de votre choix avec le Kit de développement logiciel (SDK) Azure Machine Learning.

1. Créez votre environnement conda, comme décrit à l’étape 2 [Configurer Visual Studio Code](#configure-visual-studio-code) ci-dessus.
1. Suivez les instructions de chaque éditeur pour utiliser l’environnement conda. Par exemple, vous pouvez suivre les [instructions PyCharm](https://www.jetbrains.com/help/pycharm/2018.2/conda-support-creating-conda-virtual-environment.html).

## <a name="next-steps"></a>Étapes suivantes

- [Entraîner un modèle sur Azure Machine Learning avec le jeu de données MNIST](tutorial-train-models-with-aml.md)
