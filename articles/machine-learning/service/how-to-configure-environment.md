---
title: Configuration d’un environnement de développement Python
titleSuffix: Azure Machine Learning service
description: Découvrez comment configurer un environnement de développement quand vous travaillez avec Azure Machine Learning service. Dans cet article, vous allez découvrir comment utiliser des environnements Conda, créer des fichiers de configuration et configurer Jupyter Notebooks, Azure Notebooks, , Azure Databricks, des IDE, des éditeurs de code et une Data Science Virtual Machine.
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.component: core
ms.reviewer: larryfr
manager: cgronlun
ms.topic: conceptual
ms.date: 01/18/2019
ms.custom: seodec18
ms.openlocfilehash: 0f53b3cec843ca8016c61a360025b5e731b96f55
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/23/2019
ms.locfileid: "54815869"
---
# <a name="configure-a-development-environment-for-azure-machine-learning"></a>Configurer un environnement de développement pour Azure Machine Learning

Dans cet article, vous allez découvrir comment configurer un environnement de développement pour qu’il fonctionne avec Azure Machine Learning service. Le Azure Machine Learning service est indépendant de toute plateforme. 

Les seuls prérequis pour votre environnement de développement sont Python 3, Conda (pour les environnements isolés) et un fichier de configuration contenant les informations de votre espace de travail Azure Machine Learning.

Cet article concerne les environnements et les outils suivants :

* [Azure Notebooks](#aznotebooks) : service Jupyter Notebook hébergé dans le cloud Azure. Il s’agit du moyen le plus simple pour démarrer, car le Kit de développement logiciel (SDK) Azure Machine Learning est déjà installé.

* [Data Science Virtual Machine (DSVM)](#dsvm) : un environnement de développement/expérimentation préconfiguré dans le cloud Azure, qui est conçu pour le travail de science des données et peut être déployé à l’UC uniquement des instances de machines virtuelles ou aux instances basées sur le GPU. Python 3, Conda, Jupyter Notebook et SDK Azure Machine Learning sont déjà installés. La machine virtuelle est fournie avec les infrastructures, outils et éditeurs Deep Learning et Machine Learning les plus appréciés pour le développement de solutions Machine Learning. C’est probablement l’environnement de développement le plus complet pour le Machine Learning sur la plateforme Azure.

* [Jupyter Notebook](#jupyter) : si vous utilisez déjà le Jupyter Notebook, le Kit de développement logiciel (SDK) contient des fonctionnalités supplémentaires que vous devez installer.

* [Visual Studio Code](#vscode) : si vous utilisez Visual Studio Code, sachez qu’il est doté de certaines extensions utiles que vous pouvez installer.

* [Azure Databricks](#aml-databricks) : une plateforme Analytique données appréciée, basée sur Apache Spark. Découvrez comment obtenir le kit de développement logiciel (SDK) Azure Machine Learning sur votre cluster afin de déployer des modèles.

Si vous disposez déjà d’un environnement Python 3, ou que vous voulez uniquement obtenir les étapes de base pour installer le SDK, consultez la section [Ordinateur local](#local).

## <a name="prerequisites"></a>Prérequis

- Un espace de travail de service Microsoft Azure Machine Learning. Pour créer l’espace de travail, consultez [les articles sur le démarrage rapide d’Azure Machine Learning service](quickstart-get-started.md).

- Le gestionnaire de package [Continuum Anaconda](https://www.anaconda.com/download/) ou [Miniconda](https://conda.io/miniconda.html)

    > [!IMPORTANT]
    > Anaconda et Miniconda ne sont pas nécessaires lorsque vous utilisez Azure Notebooks.

- Sur Linux ou macOS, vous avez besoin de l’interpréteur de commandes Bash.

    > [!TIP]
    > Si vous avez un environnement Linux ou macOS et utilisez un interpréteur de commandes autre que Bash (par exemple, zsh), des erreurs peuvent s’afficher lorsque vous exécutez certaines commandes. Pour contourner ce problème, utilisez la commande `bash` pour démarrer un nouveau shell Bash et y exécuter les commandes.

- Dans Windows, vous avez besoin de l’invite de commandes Windows ou Anaconda (installée par Anaconda et Miniconda).

## <a id="aznotebooks"></a>Azure Notebooks

[Azure Notebooks](https://notebooks.azure.com) (préversion) est un environnement de blocs-notes pour le cloud Azure. Il s’agit du moyen le plus simple de démarrer avec le développement Azure Machine Learning.

* Le Kit de développement logiciel (SDK) Azure Machine Learning est déjà installé.
* Après avoir créé un espace de travail Azure Machine Learning service dans le portail Azure, vous pouvez cliquer sur un bouton pour configurer automatiquement votre environnement Azure Notebooks en vue de l’utiliser avec l’espace de travail.

Pour bien démarrer avec le développement Azure Notebooks, suivez les instructions consultez [les articles sur le démarrage rapide d’Azure Machine Learning service](quickstart-get-started.md).

Par défaut, Azure Notebooks utilise un niveau de service gratuit limité à 4 Go de mémoire et de 1 Go de données. Cela étant, vous pouvez lever ces limites en attachant une instance Data Science Virtual Machine au projet Azure Notebooks. Pour plus d’informations, consultez [Gérer et configurer des projets Azure Notebooks - Niveau Calcul](/azure/notebooks/configure-manage-azure-notebooks-projects.md#compute-tier).

## <a id="dsvm"></a>Data Science Virtual Machine

Une Data Science Virtual Machine (DSVM) ou « Machine virtuelle pour la science des données » est une image de machine virtuelle (VM) personnalisée. Elle est conçue pour les travaux de science des données préconfigurés avec :

  - Des packages tels que TensorFlow, PyTorch, Scikit-learn, XGBoost et le Kit de développement logiciel (SDK) Azure Machine Learning.
  - Des outils de science des données appréciés tels que Spark Standalone et Drill.
  - Des outils Azure tels que Azure CLI, AzCopy et Explorateur Stockage.
  - Des environnements de développement intégrés (IDE) tels que Visual Studio Code et PyCharm.
  - Serveur Jupyter Notebook

Le Kit de développement logiciel (SDK) Azure Machine Learning fonctionne aussi bien sur la version Windows que sur la version Ubuntu de la DSVM. Cela étant, si vous envisagez d’utiliser également DSVM comme cible de calcul, seule la version Ubuntu est prise en charge.

Pour utiliser une DSVM en tant qu’environnement de développement, procédez comme suit :

1. Créer une DSVM dans l’un des environnements suivants :

    * Le portail Azure :

        * [Créer une instance de Data Science Virtual Machine pour Ubuntu](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro)

        * [Créer une instance Windows de Data Science Virtual Machine](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/provision-vm)

    * L’interface de ligne de commande Azure CLI :

        > [!IMPORTANT]
        > * Lorsque vous utilisez Azure CLI, vous devez, dans un premier temps, vous connecter à votre abonnement Azure à l’aide de la commande `az login`.
        >
        > * Lorsque vous utilisez les commandes dans cette étape, vous devez fournir un nom de groupe de ressources, un nom pour la machine virtuelle, un nom d’utilisateur et un mot de passe.

        * Pour créer une instance de Data Science Virtual Machine pour Ubuntu, utilisez la commande suivante :

            ```azurecli
            # create a Ubuntu DSVM in your resource group
            # note you need to be at least a contributor to the resource group in order to execute this command successfully
            # If you need to create a new resource group use: "az group create --name YOUR-RESOURCE-GROUP-NAME --location YOUR-REGION (For example: westus2)"
            az vm create --resource-group YOUR-RESOURCE-GROUP-NAME --name YOUR-VM-NAME --image microsoft-dsvm:linux-data-science-vm-ubuntu:linuxdsvmubuntu:latest --admin-username YOUR-USERNAME --admin-password YOUR-PASSWORD --generate-ssh-keys --authentication-type password
            ```

        * Pour créer une instance de Data Science Virtual Machine pour Windows, utilisez la commande suivante :

            ```azurecli
            # create a Windows Server 2016 DSVM in your resource group
            # note you need to be at least a contributor to the resource group in order to execute this command successfully
            az vm create --resource-group YOUR-RESOURCE-GROUP-NAME --name YOUR-VM-NAME --image microsoft-dsvm:dsvm-windows:server-2016:latest --admin-username YOUR-USERNAME --admin-password YOUR-PASSWORD --authentication-type password
            ```    

2. Le Kit de développement logiciel (SDK) Azure Machine Learning est déjà installé sur la DSVM. Pour utiliser l’environnement Conda qui contient le SDK, utilisez l’une des commandes suivantes :

    * Pour une DSVM Ubuntu :

        ```shell
        conda activate py36
        ```

    * Pour une DSVM Windows :

        ```shell
        conda activate AzureML
        ```

1. Pour vérifier si vous pouvez accéder au SDK afin d’en connaître la version, utilisez le code Python suivant :

    ```python
    import azureml.core
    print(azureml.core.VERSION)
    ```

1. Pour configurer la DSVM pour utiliser votre espace de travail Azure Machine Learning service, veuillez consulter la section [Créer un fichier de configuration d’espace de travail](#workspace).

Pour plus d’informations, consultez la page [Machines virtuelles Science des données](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/).

## <a id="local"></a>Ordinateur local

Lorsque vous utilisez un ordinateur local (qui peut également être une machine virtuelle distante), procédez ainsi pour créer un environnement Conda et installer le Kit de développement logiciel (SDK) :

1. Ouvrez une invite de commandes ou un interpréteur de commandes.

1. Créez un environnement Conda en utilisant les commandes suivantes :

    ```shell
    # create a new Conda environment with Python 3.6, NumPy, and Cython
    conda create -n myenv Python=3.6 cython numpy

    # activate the Conda environment
    conda activate myenv

    # On macOS run
    source activate myenv
    ```

    La création de l’environnement peut prendre plusieurs minutes si Python 3.6 et d’autres composants doivent être téléchargés.

1. Installez le Kit de développement logiciel (SDK) Azure Machine Learning avec des fonctionnalités supplémentaires de blocs-notes et le Kit de développement logiciel (SDK) de préparation des données à l’aide de la commande suivante :

     ```shell
    pip install --upgrade azureml-sdk[notebooks,automl] azureml-dataprep
    ```

   > [!NOTE]
   > Si vous recevez un message indiquant que PyYAML ne peut pas être désinstallé, utilisez plutôt la commande suivante :
   >
   > `pip install --upgrade azureml-sdk[notebooks,automl] azureml-dataprep --ignore-installed PyYAML`

   L’installation du Kit de développement logiciel (SDK) peut prendre plusieurs minutes.

1. Installez des packages pour votre expérimentation Machine Learning. Utilisez la commande suivante et remplacez *\<new package>* par le package que vous souhaitez installer :

    ```shell
    conda install <new package>
    ```

1. Pour vérifier que le Kit de développement logiciel (SDK) est installé, utilisez le code Python suivant :

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

### <a id="jupyter"></a>Jupyter Notebook

Jupyter Notebook fait partie de [Project Jupyter](https://jupyter.org/). Il fournit une expérience de codage interactive dans laquelle vous créez des documents qui associent du code en direct, des graphismes et du texte descriptif. Jupyter Notebook est aussi un excellent moyen de partager vos résultats avec d’autres utilisateurs, car il vous permet d’enregistrer la sortie de vos sections de code dans le document. Vous pouvez installer Jupyter Notebooks sur diverses plateformes.

La procédure décrite dans la section [Ordinateur local](#local) installe les composants facultatifs pour les blocs-notes Jupyter. Pour activer ces composants dans votre environnement Jupyter Notebook, procédez comme suit :

1. Ouvrez une invite de commandes ou un interpréteur de commandes.

1. Pour installer un serveur Jupyter Notebook prenant en charge Conda, utilisez la commande suivante :

    ```shell
    # install Jupyter
    conda install nb_conda
    ```

1. Ouvrez Jupyter Notebook à l’aide de la commande suivante :

    ```shell
    jupyter notebook
    ```

1. Pour vérifier que Jupyter Notebook peut utiliser le Kit de développement logiciel (SDK), ouvrez un nouveau bloc-notes, sélectionnez **myenv** comme noyau, puis exécutez la commande suivante dans une cellule du bloc-notes :

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

1. Pour configurer le Jupyter Notebook pour utiliser votre espace de travail Azure Machine Learning service, veuillez consulter la section [Créer un fichier de configuration d’espace de travail](#workspace).

### <a id="vscode"></a>Visual Studio Code

Visual Studio Code est un éditeur de code multiplateforme. Il s’appuie sur une installation locale de Python 3 et Conda pour la prise en charge de Python, mais il fournit des outils supplémentaires pour l’utilisation de l’intelligence artificielle. Il prend également en charge la sélection de l’environnement Conda à partir de l’éditeur de code.

Pour utiliser Visual Studio Code pour le développement, procédez comme suit :

1. Pour savoir comment utiliser Visual Studio Code pour le développement Python, consultez la page [Getting Started with Python in VS Code](https://code.visualstudio.com/docs/python/python-tutorial) (contenu en anglais).

1. Pour sélectionner l’environnement Conda, ouvrez VS Code, puis utilisez Ctrl+Maj+P (Linux et Windows) ou Commande+Maj+P (Mac).  
    Le __palette de commandes__ s’ouvre. 

1. Entrez __Python: Select Interpreter__, puis sélectionnez l’environnement Conda.

1. Pour vérifier que vous pouvez utiliser le Kit de développement logiciel (SDK), créez puis exécutez un fichier Python (.py) qui contient le code suivant :

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

1. Pour installer l’extension Azure Machine Learning pour Visual Studio Code, consultez la page [Azure Machine Learning for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai) (contenu en anglais).

    Pour plus d’informations, consultez la page [Premiers pas avec Azure Machine Learning pour Visual Studio Code](how-to-vscode-tools.md).

<a name="aml-databricks"></a>

## <a name="azure-databricks"></a>Azure Databricks

Vous pouvez utiliser une version personnalisée du SDK Azure Machine Learning pour Azure Databricks à des fins Machine Learning personnalisé de bout en bout. Vous pouvez également entraîner votre modèle dans Databricks et utiliser [Visual Studio Code](how-to-vscode-train-deploy.md#deploy-your-service-from-vs-code) pour le déployer.

Pour préparer votre cluster Databricks et obtenir des exemples de notebooks :

1. Créez un [cluster Databricks](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal) avec les paramètres suivants :

    | Paramètre | Valeur |
    |----|---|
    | Nom du cluster | Nom de votre cluster |
    | Runtime Databricks | N’importe quel runtime non-ML (runtime 4.x, 5.x non-ML) |
    | Version Python | 3 |
    | Workers | 2 ou plus |

    Utilisez ces paramètres uniquement si vous êtes susceptible de recourir au machine learning automatisé sur Databricks :
    
    |   Paramètre | Valeur |
    |----|---|
    | Types de machines virtuelles pour les nœuds worker | Machine virtuelle à mémoire optimisé, de préférence |
    | Activer la mise à l’échelle automatique | Décochez la case |
    
    Le nombre de nœuds worker dans votre cluster Databricks détermine le nombre maximal d’itérations simultanées dans les paramètres de ML automatisé.  

    La création du cluster prend quelques minutes. Attendez que le cluster s’exécute avant de continuer.

1. Installez le package du SDK Azure Machine Learning et attachez-le à votre cluster.  

    * [Créez une bibliothèque](https://docs.databricks.com/user-guide/libraries.html#create-a-library) avec l’un de ces paramètres (_choisissez une seule de ces options_) :
    
        * Pour installer le SDK Azure Machine Learning _sans_ la fonctionnalité de machine learning automatisé :
            | Paramètre | Valeur |
            |----|---|
            |Source | Charger Python Egg ou PyPI
            |Nom PyPi | azureml-sdk[databricks]
    
        * Pour installer le SDK Azure Machine Learning _avec_ machine learning automatisé :
            | Paramètre | Valeur |
            |----|---|
            |Source | Charger Python Egg ou PyPI
            |Nom PyPi | azureml-sdk[automl_databricks]
    
    * Ne sélectionnez pas **Attacher automatiquement à tous les clusters**

    * Sélectionnez **Attacher** en regard du nom de votre cluster

    * Vérifiez qu’il n’y a aucune erreur jusqu’à ce que l’état devienne **Attaché**. Cela peut prendre quelques minutes.

    Si vous avez une ancienne version du SDK, désélectionnez-la des bibliothèques installées du cluster et déplacez-la vers la corbeille. Installez la nouvelle version du SDK et redémarrez le cluster. En cas de problème après cette opération, détachez et rattachez votre cluster.

    Quand vous avez terminé, la bibliothèque est attachée, comme illustré dans l’image suivante. Soyez conscient de ces [problèmes Databricks courants](resource-known-issues.md#databricks).

    * Si vous avez installé le SDK Azure Machine Learning _sans_ machine learning automatisé![SDK sans machine learning automatisé installé sur Databricks ](./media/how-to-configure-environment/amlsdk-withoutautoml.jpg)

    * Si vous avez installé le SDK Azure Machine Learning _avec_ machine learning automatisé![SDK avec machine learning automatisé installé sur Databricks ](./media/how-to-configure-environment/automlonadb.jpg)

   Si cette étape échoue, redémarrez votre cluster en procédant comme suit :

   a. Dans le volet gauche, sélectionnez **Clusters**. 
   
   b. Sélectionnez le nom de votre cluster dans le tableau. 

   c. Sous l’onglet **Bibliothèques**, sélectionnez **Redémarrer**.

1. Téléchargez le [fichier d’archive de notebook du Kit de développement logiciel (SDK) Azure Databricks / Azure Machine Learning](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks/Databricks_AMLSDK_1-4_6.dbc).

   >[!Warning]
   > De nombreux exemples de notebooks sont disponibles à des fins d'utilisation avec le service Azure Machine Learning. Seuls [ces exemples de notebooks](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks) fonctionnent avec Azure Databricks.

1.  [Importez le fichier d’archive](https://docs.azuredatabricks.net/user-guide/notebooks/notebook-manage.html#import-an-archive) dans votre cluster Databricks et commencez à l’explorer comme décrit dans le dépôt GitHub [Machine Learning Notebooks](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks).


## <a id="workspace"></a>Créer un fichier de configuration d’espace de travail

Le fichier de configuration d’espace de travail est un fichier JSON qui indique au SDK comment communiquer avec votre espace de travail Azure Machine Learning service. Le fichier est nommé *config.json*, et il a le format suivant :

```json
{
    "subscription_id": "<subscription-id>",
    "resource_group": "<resource-group>",
    "workspace_name": "<workspace-name>"
}
```

Ce fichier JSON doit se trouver dans la structure de répertoire qui contient vos scripts Python ou vos blocs-notes Jupyter Notebook. Il peut se trouver dans le même répertoire, dans un sous-répertoire nommé *aml_config* ou dans un répertoire parent.

Pour utiliser ce fichier à partir de votre code, utilisez `ws=Workspace.from_config()`. Ce code charge les informations à partir du fichier et se connecte à votre espace de travail.

Il existe trois façons de créer le fichier de configuration :

* **Suivez le [Guide de démarrage rapide Azure Machine Learning](quickstart-get-started.md)**: Un fichier *config.json* est créé dans votre bibliothèque Azure Notebooks. Ce fichier contient les informations de configuration pour votre espace de travail. Vous pouvez télécharger ou copier le fichier *config.json* dans d’autres environnements de développement.

* **Créez manuellement le fichier** : Cette méthode consiste à utiliser un éditeur de texte. Vous trouverez les valeurs de ce fichier de configuration en accédant à votre espace de travail dans le [portail Azure](https://portal.azure.com). Copiez les valeurs situées sous Nom de l’espace de travail, Groupe de ressources, et ID d’abonnement, puis utilisez-les dans le fichier de configuration.

     ![Portail Azure](./media/how-to-configure-environment/configure.png)

* **Créez le fichier par programmation** : Dans l’extrait de code suivant, vous vous connectez à un espace de travail en fournissant l’ID d’abonnement, le groupe de ressources et le nom de l’espace de travail. Ensuite, il enregistre la configuration de l’espace de travail dans le fichier :

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

    Ce code écrit le fichier de configuration dans le fichier *aml_config/config.json*.

## <a name="next-steps"></a>Étapes suivantes

- [Entraîner un modèle](tutorial-train-models-with-aml.md) sur Azure Machine Learning avec le jeu de données MNIST
- Voir les informations de référence sur le [Kit SDK Azure Machine Learning pour Python](https://aka.ms/aml-sdk)
- Découvrir le [kit SDK Azure Machine Learning Data Prep](https://aka.ms/data-prep-sdk)
