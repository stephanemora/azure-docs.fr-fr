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
ms.date: 11/6/2018
ms.openlocfilehash: fa70e0dfa1f131e38e43faa3d80497d50a52e135
ms.sourcegitcommit: 8d88a025090e5087b9d0ab390b1207977ef4ff7c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/21/2018
ms.locfileid: "52275212"
---
# <a name="configure-a-development-environment-for-azure-machine-learning"></a>Configurer un environnement de développement pour Azure Machine Learning

Dans ce document, vous allez découvrir comment configurer un environnement de développement pour qu’il fonctionne avec le service Azure Machine Learning. Le service Azure Machine Learning est indépendant de toute plateforme. Les seuls prérequis pour votre environnement de développement sont __Python 3__, __Conda__ (pour les environnements isolés) et un fichier de configuration contenant les informations de votre espace de travail Azure Machine Learning.

Ce document concerne les environnements et outils suivants :

* [Azure Notebooks](#aznotebooks) : service Jupyter Notebook hébergé dans le cloud Azure. Il s’agit du __moyen le plus simple__ pour démarrer, puisque le SDK Azure Machine Learning est déjà installé.
* [Data Science Virtual Machine](#dsvm) : machine virtuelle du cloud Azure qui est __conçue pour le travail de science des données__. Python 3, Conda, Jupyter Notebook et SDK Azure Machine Learning sont déjà installés. La machine virtuelle est fournie avec des frameworks, des outils et des éditeurs Machine Learning populaires adaptés au développement de solutions Machine Learning. Il s’agit probablement de l’environnement de développement __le plus complet__ pour Machine Learning sur la plateforme Azure.
* [Jupyter Notebook](#jupyter) : si vous utilisez déjà Jupyter Notebook, le SDK contient des fonctionnalités supplémentaires que vous devez installer.
* [Visual Studio Code](#vscode) : si vous utilisez Visual Studio Code, il existe certaines extensions utiles que vous pouvez installer.

Si vous disposez déjà d’un environnement Python 3, ou que vous voulez uniquement obtenir les étapes de base pour installer le SDK, consultez la section [Ordinateur local](#local).

## <a name="prerequisites"></a>Prérequis

- Un espace de travail de service Microsoft Azure Machine Learning. Pour en créer un, suivez les étapes décrites dans [Bien démarrer avec le service Azure Machine Learning](quickstart-get-started.md).

- Le gestionnaire de package [Continuum Anaconda](https://www.anaconda.com/download/) ou [Miniconda](https://conda.io/miniconda.html)

    > [!IMPORTANT]
    > Anaconda et Miniconda ne sont pas nécessaires lorsque vous utilisez Azure Notebooks.

- Sur Linux ou Mac OS, vous avez besoin du shell Bash.

    > [!TIP]
    > Si vous avez un environnement Linux ou Mac OS et utilisez un shell autre que Bash (par exemple, zsh), des erreurs peuvent s’afficher lorsque vous exécutez certaines commandes. Pour contourner ce problème, utilisez la commande `bash` pour démarrer un nouveau shell Bash et y exécuter les commandes.

- Dans Windows, vous avez besoin de l’invite de commandes Windows ou Anaconda (installée par Anaconda et Miniconda).

## <a id="anotebooks"></a>Azure Notebooks

[Azure Notebooks](https://notebooks.azure.com) (préversion) est un environnement de blocs-notes pour le cloud Azure. Il s’agit du __moyen le plus simple__ de démarrer avec le développement Azure Machine Learning.

* Le SDK Azure Machine Learning est __déjà installé__.
* Après avoir créé un espace de travail Azure Machine Learning dans le portail Azure, vous pouvez cliquer sur un bouton pour configurer automatiquement votre environnement Azure Notebooks en vue de l’utiliser avec l’espace de travail.

Pour bien démarrer avec le développement Azure Notebooks, suivez les instructions du document [Bien démarrer avec le service Azure Machine Learning](quickstart-get-started.md).

## <a id="dsvm"></a>Data Science Virtual Machine

Data Science Virtual Machine (DSVM) est une image de machine virtuelle personnalisée qui est **conçue pour le travail de science des données**.  Il inclut :

  - Outils de science des données prisés
  - Environnements de développement intégré (IDE) tels que PyCharm et RStudio
  - Packages tels que Jupyter Notebooks et Tensorflow

Le SDK Azure Machine Learning fonctionne aussi bien sur la version Windows que sur la version Ubuntu de DSVM. Pour utiliser DSVM comme environnement de développement, suivez ces étapes :

1. Pour créer une instance Data Science Virtual Machine, suivez les étapes de l’un des documents suivants :

    * [Créer une instance Ubuntu de Data Science Virtual Machine](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro)
    * [Créer une instance Windows de Data Science Virtual Machine](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/provision-vm)

1. Le SDK Azure Machine Learning est **déjà installé** sur l’instance DSVM. Pour utiliser l’environnement Conda qui contient le SDK, utilisez l’une des commandes suivantes :

    * Sur la version __Ubuntu__ de DSVM, utilisez la commande suivante :

        ```shell
        conda activate py36
        ```

    * Sur la version __Windows__ de DSVM, utilisez la commande suivante :

        ```shell
        conda activate AzureML
        ```

1. Pour vérifier si vous pouvez accéder au SDK afin d’en connaître la version, utilisez le code Python suivant :

    ```python
    import azureml.core
    print(azureml.core.VERSION)
    ```

1. Pour configurer l’instance DSVM dans le but qu’elle utilise votre espace de travail Azure Machine Learning, consultez la section [Configurer l’espace de travail](#workspace).

Pour plus d’informations sur Data Science Virtual Machines, consultez [Data Science Virtual Machines](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/).

## <a id="local"></a>Ordinateur local

Lorsque vous utilisez un ordinateur local (qui peut également être une machine virtuelle distante), effectuez les étapes suivantes pour créer un environnement conda et installer le SDK :

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

1. Pour vérifier que le SDK est installé, utilisez le code Python suivant :

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

### <a id="jupyter"></a>Jupyter Notebook

Jupyter Notebook fait partie de [Project Jupyter](https://jupyter.org/). Il fournit une expérience de codage interactive dans laquelle vous créez des documents qui associent du code en direct, des graphismes et du texte descriptif. Jupyter Notebook est aussi un excellent moyen de partager vos résultats avec d’autres utilisateurs, puisqu’il vous permet d’enregistrer la sortie de vos sections de code dans le document. Vous pouvez installer Jupyter Notebooks sur diverses plateformes.

Les étapes décrites dans la section [Ordinateur local](#local) permettent d’installer les composants facultatifs de Jupyter Notebook. Pour activer ces composants dans votre environnement Jupyter Notebook, utilisez les étapes suivantes :

1. Ouvrez une invite de commandes ou un interpréteur de commandes.

1. Pour installer un serveur Jupyter Notebook prenant en charge conda et activer des widgets d’expérimentation, utilisez les commandes suivantes :

    ```shell
    # install Jupyter
    conda install nb_conda

    # install experiment widget
    jupyter nbextension install --py --user azureml.widgets

    # enable experiment widget
    jupyter nbextension enable --py --user azureml.widgets
    ```

1. Ouvrez Jupyter Notebook à l’aide de la commande suivante :

    ```shell
    jupyter notebook
    ```

1. Pour vérifier que Jupyter Notebook peut utiliser le SDK, ouvrez un nouveau bloc-notes, puis sélectionnez « myenv » comme noyau. Ensuite, exécutez la commande suivante dans une cellule du bloc-notes :

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

1. Pour configurer Jupyter Notebook dans le but qu’il utilise votre espace de travail Azure Machine Learning, consultez la section [Configurer l’espace de travail](#workspace).

### <a id="vscode"></a>Visual Studio Code

Visual Studio Code est un éditeur de code multiplateforme. Il s’appuie sur une installation locale de Python 3 et Conda pour la prise en charge de Python, mais il fournit des outils supplémentaires pour l’utilisation de l’intelligence artificielle. Il prend également en charge la sélection de l’environnement Conda à partir de l’éditeur de code.

Pour utiliser Visual Studio Code à des fins de développement, effectuez les étapes suivantes :

1. Pour savoir comment utiliser Visual Studio Code pour le développement Python, consultez le document [Get started with Python in VSCode](https://code.visualstudio.com/docs/python/python-tutorial).

1. Pour sélectionner l’environnement Conda, ouvrez VS Code, puis utilisez __Ctrl-Maj-P__ (Linux et Windows) ou __Commande-Maj-P__ (Mac) pour obtenir la __palette de commandes__. Entrez __Python: Select Interpreter__, puis sélectionnez l’environnement conda.

1. Pour vérifier que vous pouvez utiliser le SDK, créez un fichier Python (.py) qui contient le code suivant. Ensuite, exécutez le fichier :

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

1. Pour installer l’extension Visual Studio Code Tools for AI, consultez [Tools for AI](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai).

    Pour plus d’informations, consultez le document [Utiliser Visual Studio Code Tools for AI avec Azure Machine Learning](how-to-vscode-tools.md).

## <a id="workspace"></a>Créer un fichier de configuration d’espace de travail

Le fichier de configuration d’espace de travail est un document JSON qui indique au SDK comment communiquer avec votre espace de travail Azure Machine Learning. Le fichier se nomme `config.json` et son format est le suivant :

```json
{
    "subscription_id": "<subscription-id>",
    "resource_group": "<resource-group>",
    "workspace_name": "<workspace-name>"
}
```

Ce fichier doit se trouver dans la structure de répertoire qui contient vos scripts Python ou vos blocs-notes Jupyter Notebook. Il peut se trouver dans le même répertoire, dans un sous-répertoire nommé `aml_config` ou dans un répertoire parent.

Pour utiliser ce fichier à partir de votre code, utilisez `ws=Workspace.from_config()`. Ce code charge les informations à partir du fichier et se connecte à votre espace de travail.

Il existe trois façons de créer le fichier de configuration :

* Si vous suivez le [Guide de démarrage rapide Azure Machine Learning](quickstart-get-started.md), un fichier `config.json` est créé dans votre bibliothèque Azure Notebooks. Ce fichier contient les informations de configuration pour votre espace de travail. Vous pouvez télécharger ou copier ce `config.json` dans d’autres environnements de développement.

* Vous pouvez **créer manuellement le fichier** à l’aide d’un éditeur de texte. Vous trouverez les valeurs de ce fichier de configuration en accédant à votre espace de travail dans le [portail Azure](https://portal.azure.com). Copiez les valeurs situées sous __Nom de l’espace de travail__, __Groupe de ressources__, et __ID d’abonnement__, puis utilisez-les dans le fichier de configuration.
        ![Portail Azure](./media/how-to-configure-environment/configure.png)

* Vous pouvez **créer le fichier par programmation**. L’extrait de code suivant montre comment se connecter à un espace de travail en fournissant l’ID d’abonnement, le groupe de ressources et le nom de l’espace de travail. Ensuite, il enregistre la configuration de l’espace de travail dans un fichier :

    ```python
    from azureml.core import Workspace

    subscription_id = '<subscription-id>'
    resource_group  = '<resource-group>'
    workspace_name  = '<workspace-name>'

    try:
        ws = Workspace(subscription_id = subscription_id, resource_group = resource_group, workspace_name = workspace_name)
        ws.write_config()
        print('Library configuration succeeded')
    except:
        print('Workspace not found')
    ```

    Ce code écrit le fichier config dans le fichier `aml_config/config.json`.

## <a name="next-steps"></a>Étapes suivantes

- [Entraîner un modèle sur Azure Machine Learning avec le jeu de données MNIST](tutorial-train-models-with-aml.md)
- [SDK Azure Machine Learning pour Python](https://aka.ms/aml-sdk)
- [Kit de développement logiciel (SDK) de préparation de données Azure Machine Learning](https://aka.ms/data-prep-sdk)