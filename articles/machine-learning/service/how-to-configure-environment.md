---
title: Configuration d’un environnement de développement Python
titleSuffix: Azure Machine Learning service
description: Découvrez comment configurer un environnement de développement quand vous travaillez avec Azure Machine Learning service. Dans cet article, vous allez découvrir comment utiliser des environnements Conda, créer des fichiers de configuration et configurer votre propre serveur de notebooks basé sur le cloud, Jupyter Notebooks, Azure Databricks, Azure Notebooks, des IDE, des éditeurs de code et une Data Science Virtual Machine.
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.topic: conceptual
ms.date: 05/14/2019
ms.custom: seodec18
ms.openlocfilehash: 3738ffe8b3faedc328bde01173400289403652f4
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/17/2019
ms.locfileid: "68297930"
---
# <a name="configure-a-development-environment-for-azure-machine-learning"></a>Configurer un environnement de développement pour Azure Machine Learning

Dans cet article, vous allez découvrir comment configurer un environnement de développement pour qu’il fonctionne avec Azure Machine Learning service. Le Azure Machine Learning service est indépendant de toute plateforme.

Les seuls prérequis pour votre environnement de développement sont Python 3, Anaconda (pour les environnements isolés) et un fichier config contenant les informations de votre espace de travail Azure Machine Learning.

Cet article concerne les environnements et les outils suivants :

* Votre propre [machine virtuelle de notebooks basée sur le cloud](#notebookvm) : utilisez une ressource de calcul dans votre station de travail pour exécuter des notebooks Jupyter. Il s’agit du moyen le plus simple pour démarrer, car le Kit de développement logiciel (SDK) Azure Machine Learning est déjà installé.

* [Data Science Virtual Machine (DSVM)](#dsvm) : un environnement de développement/expérimentation préconfiguré dans le cloud Azure, qui est conçu pour le travail de science des données et peut être déployé à l’UC uniquement des instances de machines virtuelles ou aux instances basées sur le GPU. Python 3, Conda, Jupyter Notebook et SDK Azure Machine Learning sont déjà installés. La machine virtuelle est fournie avec les infrastructures, outils et éditeurs Deep Learning et Machine Learning les plus appréciés pour le développement de solutions Machine Learning. C’est probablement l’environnement de développement le plus complet pour le Machine Learning sur la plateforme Azure.

* [Jupyter Notebooks](#jupyter) : si vous utilisez déjà le Jupyter Notebook, le Kit de développement logiciel (SDK) contient des fonctionnalités supplémentaires que vous devez installer.

* [Visual Studio Code](#vscode) : si vous utilisez Visual Studio Code, sachez qu’il est doté de certaines extensions utiles que vous pouvez installer.

* [Azure Databricks](#aml-databricks) : une plateforme Analytique données appréciée, basée sur Apache Spark. Découvrez comment obtenir le kit de développement logiciel (SDK) Azure Machine Learning sur votre cluster afin de déployer des modèles.

* [Azure Notebooks](#aznotebooks) : service Jupyter Notebook hébergé dans le cloud Azure. Il s’agit également d’un moyen simple pour démarrer, car le Kit de développement logiciel (SDK) Azure Machine Learning est déjà installé.  

Si vous disposez déjà d’un environnement Python 3, ou que vous voulez uniquement obtenir les étapes de base pour installer le SDK, consultez la section [Ordinateur local](#local).

## <a name="prerequisites"></a>Prérequis

Un espace de travail de service Microsoft Azure Machine Learning. Pour créer l’espace de travail, consultez [Create an Azure Machine Learning service workspace](setup-create-workspace.md) (Créer un espace de travail Azure Machine Learning service). Un espace de travail est nécessaire pour la prise en main de votre propre [serveur de notebooks basé sur le cloud](#notebookvm), d’une [DSVM](#dsvm), [d’Azure Databricks](#aml-databricks) ou [d’Azure Notebooks](#aznotebooks).

Pour installer l’environnement du Kit de développement logiciel (SDK) pour votre [ordinateur local](#local), [serveur Jupyter Notebook](#jupyter) ou [Visual Studio Code](#vscode) vous avez également besoin des éléments suivants :

- gestionnaire de package [Anaconda](https://www.anaconda.com/download/) ou [Miniconda](https://conda.io/miniconda.html).

- Sur Linux ou macOS, vous avez besoin de l’interpréteur de commandes Bash.

    > [!TIP]
    > Si vous avez un environnement Linux ou macOS et utilisez un interpréteur de commandes autre que Bash (par exemple, zsh), des erreurs peuvent s’afficher lorsque vous exécutez certaines commandes. Pour contourner ce problème, utilisez la commande `bash` pour démarrer un nouveau shell Bash et y exécuter les commandes.

- Dans Windows, vous avez besoin de l’invite de commandes Windows ou Anaconda (installée par Anaconda et Miniconda).

## <a id="notebookvm"></a>Votre propre machine virtuelle de notebooks basée sur le cloud

La machine virtuelle de notebooks (préversion) est une station de travail Azure basée sur le cloud et sécurisée qui fournit aux scientifiques des données un serveur de notebooks Jupyter, JupyterLab et un environnement ML prêt à être utilisé. 

La machine virtuelle de notebooks est : 

+ **Sécurisé**. Dans la mesure où l’accès au notebook et à la machine virtuelle est sécurisé avec HTTPS et Azure Active Directory par défaut, les professionnels de l’informatique peuvent appliquer facilement l’authentification unique et d’autres fonctionnalités de sécurité telles que l’authentification multifacteur.

+ **Préconfigurée**. Cet environnement ML Python entièrement préparé est de la même famille que la populaire Data Science VM IaaS et inclut :
  + le Kit de développement logiciel (SDK) Python Azure ML (dernière version) ;
  + la configuration automatique pour utiliser votre espace de travail ;
  + un serveur de notebooks Jupyter ;
  + un IDE de notebook JupyterLab ;
  + des pilotes GPU préconfigurés ; 
  + une sélection d’infrastructures de Deep Learning.
 

  Si vous êtes dans le code, la machine virtuelle inclut des didacticiels et exemples pour vous aider à découvrir et apprendre à utiliser Azure Machine Learning service. Les exemples de notebooks sont stockés dans le compte Stockage Blob Azure de votre espace de travail, afin de pouvoir les partager entre les machines virtuelles. Quand ils sont exécutés, ils ont également accès aux magasins de données et ressources de calcul de votre espace de travail. 

+ **Simple à configurer** : créez à tout moment depuis votre espace de travail Azure Machine Learning. Indiquez juste un nom et spécifiez un type de machine virtuelle Azure. Essayez dès maintenant avec ce [Démarrage rapide : Utiliser un serveur Notebook cloud pour prendre en main Azure Machine Learning](quickstart-run-cloud-notebook.md).

+ **Personnalisable**. Avec une offre de machine virtuelle sécurisée et gérée, vous conservez un accès total aux fonctionnalités matérielles et les personnalisez à votre gré. Par exemple, créez rapidement la dernière version de machine virtuelle reposant sur NVidia V100 pour effectuer un débogage pas à pas de la nouvelle architecture de réseau neuronal.

Pour mettre fin à la facturation relative à la machine virtuelle de notebooks, [arrêtez la machine virtuelle de notebooks](quickstart-run-cloud-notebook.md#stop-the-notebook-vm). 

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

Lorsque vous utilisez un ordinateur local (qui peut également être une machine virtuelle distante), procédez ainsi pour créer un environnement Anaconda et installer le Kit de développement logiciel (SDK) :

1. Si vous ne l’avez pas déjà, téléchargez et installez [Anaconda](https://www.anaconda.com/distribution/#download-section) (Python version 3.7).

1. Ouvrez une invite de commandes Anaconda et créez un environnement avec les commandes suivantes :

    Exécutez la commande suivante pour créer l’environnement.

    ```shell
    conda create -n myenv python=3.6.5
    ```

    Activez ensuite l’environnement.

    ```shell
    conda activate myenv
    ```

    Cet exemple crée un environnement à l’aide de python 3.6.5, mais n’importe quelle sous-version spécifique peut être choisie. La compatibilité du Kit de développement logiciel (SDK) ne peut pas être garantie avec certaines versions principales (versions 3.5 et supérieures recommandées), et il est recommandé d’essayer une autre version/sous-version dans votre environnement Anaconda si vous rencontrez des erreurs. La création de l’environnement prend plusieurs minutes, le temps que les composants et les packages soient téléchargés.

1. Exécutez les commandes suivantes dans votre nouvel environnement pour activer les noyaux ipython spécifiques à l’environnement. Cela permet de s’assurer du comportement d’importation du package et du noyau attendu lors de l’utilisation de Jupyter Notebook dans des environnements Anaconda :

    ```shell
    conda install notebook ipykernel
    ```

    Exécutez ensuite la commande ci-dessous pour créer le noyau :

    ```shell
    ipython kernel install --user
    ```

1. Utilisez les commandes suivantes pour installer les packages :

    Cette commande installe le Kit de développement logiciel (SDK) Azure Machine Learning de base avec le notebook et des suppléments automl. Le supplément `automl` est une installation volumineuse, qui peut être retirée si vous ne prévoyez pas d’exécuter des expériences d’apprentissage automatique automatisées. Le supplément `automl` inclut également le Kit de développement logiciel (SDK) de préparation de données Azure Machine Learning par défaut en tant que dépendance.

     ```shell
    pip install azureml-sdk[notebooks,automl]
    ```

   > [!NOTE]
   > Si vous recevez un message indiquant que PyYAML ne peut pas être désinstallé, utilisez plutôt la commande suivante :
   >
   > `pip install --upgrade azureml-sdk[notebooks,automl] --ignore-installed PyYAML`

   L’installation du Kit de développement logiciel (SDK) peut prendre plusieurs minutes. Consultez le [guide d’installation](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) pour plus d’informations sur les options d’installation.

1. Installez d’autres packages pour votre expérimentation d’apprentissage automatique.

    Utilisez l’une des commandes suivantes et remplacez *\<new package>* par le package que vous souhaitez installer. Installer des packages via `conda install` nécessite que le package fasse partie des chaînes actuelles (les nouvelles chaînes peuvent être ajoutées dans Anaconda Cloud).

    ```shell
    conda install <new package>
    ```

    Vous pouvez également installer des packages via `pip`.

    ```shell
    pip install <new package>
    ```

### <a id="jupyter"></a>Jupyter Notebook

Jupyter Notebook fait partie de [Project Jupyter](https://jupyter.org/). Il fournit une expérience de codage interactive dans laquelle vous créez des documents qui associent du code en direct, des graphismes et du texte descriptif. Jupyter Notebook est aussi un excellent moyen de partager vos résultats avec d’autres utilisateurs, car il vous permet d’enregistrer la sortie de vos sections de code dans le document. Vous pouvez installer Jupyter Notebooks sur diverses plateformes.

La procédure décrite dans la section [Ordinateur Local](#local) installe les composants nécessaires pour l’exécution de Jupyter Notebooks dans un environnement Anaconda. Pour activer ces composants dans votre environnement Jupyter Notebook, procédez comme suit :

1. Ouvrez une invite de commandes Anaconda et activez votre environnement.

    ```shell
    conda activate myenv
    ```
    
1. Clonez le [dépôt GitHub](https://aka.ms/aml-notebooks) pour un ensemble d’exemples de notebooks.

    ```CLI
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Lancez le serveur Jupyter Notebook avec la commande suivante :

    ```shell
    jupyter notebook
    ```

1. Pour vérifier que Jupyter Notebook peut utiliser le Kit de développement logiciel (SDK), ouvrez un **nouveau** notebook, sélectionnez **Python 3** comme noyau, puis exécutez la commande suivante dans une cellule du notebook :

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

1. Si vous rencontrez des problèmes d’importation de modules et que vous recevez une erreur `ModuleNotFoundError`, vérifiez que votre noyau Jupyter est connecté au chemin d’accès correct pour votre environnement en exécutant le code suivant dans une cellule du notebook.

    ```python
    import sys
    sys.path
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
Azure Databricks est un environnement basé sur Apache Spark dans le cloud Azure. Il fournit un environnement basé sur Notebook collaboratif avec un cluster de calcul basé sur l’UC ou le GPU.

Comment Azure Databricks fonctionne avec Azure Machine Learning service :
+ Vous pouvez effectuer l’apprentissage d’un modèle à l’aide de Spark MLlib et déployer le modèle sur ACI/AKS à partir d’Azure Databricks. 
+ Vous pouvez également utiliser les fonctionnalités [d’apprentissage automatique automatisées](concept-automated-ml.md) dans un Kit de développement logiciel (SDK) Azure ML spécial avec Azure Databricks.
+ Vous pouvez utiliser Azure Databricks comme cible de calcul à partir d’un [pipeline Azure Machine Learning](concept-ml-pipelines.md). 

### <a name="set-up-your-databricks-cluster"></a>Configuration de votre cluster Databricks

Créez un [cluster Databricks](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal). Certains paramètres s’appliquent uniquement si vous installez le Kit de développement logiciel (SDK) pour l’apprentissage automatique automatisé sur Databricks.
**La création du cluster prend quelques minutes.**

Utilisez les paramètres suivants :

| Paramètre |S’applique à| Valeur |
|----|---|---|
| Nom du cluster |toujours| Nom de votre cluster |
| Runtime Databricks |toujours| N’importe quel runtime non-ML (runtime 4.x, 5.x non-ML) |
| Version Python |toujours| 3 |
| Workers |toujours| 2 ou plus |
| Types de machines virtuelles pour les nœuds worker <br>(détermine le nombre maximal d’itérations concurrentes) |ML automatisé<br>uniquement| Machine virtuelle à mémoire optimisé, de préférence |
| Activer la mise à l’échelle automatique |ML automatisé<br>uniquement| Décochez la case |

Attendez que le cluster s’exécute avant de continuer.

### <a name="install-the-correct-sdk-into-a-databricks-library"></a>Installation du Kit de développement logiciel (SDK) correct dans une bibliothèque Databricks
Une fois que le cluster est en cours d’exécution, [créez une bibliothèque](https://docs.databricks.com/user-guide/libraries.html#create-a-library) pour associer le package du Kit de développement logiciel (SDK) Azure Machine Learning approprié à votre cluster. 

1. Ne choisissez **qu’une seule** option (aucune autre installation de Kit de développement logiciel (SDK) n’est prise en charge)

   |Suppléments de &nbsp;packages&nbsp; de Kit de développement logiciel (SDK)|Source|Nom&nbsp;PyPi&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|
   |----|---|---|
   |Pour Databricks| Charger Python Egg ou PyPI | azureml-sdk[databricks]|
   |Pour Databricks - avec-<br> des fonctionnalités ML automatisées| Charger Python Egg ou PyPI | azureml-sdk[automl_databricks]|

   > [!Warning]
   > Aucun autre supplément de Kit de développement logiciel (SDK) ne peut être installé. Choisissez uniquement l’une des options précédentes [databricks] ou [automl_databricks].

   * Ne sélectionnez pas **Attacher automatiquement à tous les clusters**.
   * Sélectionnez **Attacher** en regard du nom de votre cluster.

1. Surveillez les erreurs jusqu’à ce que l’état soit défini sur **Attaché**, ce qui peut prendre plusieurs minutes.  Si cette étape échoue, vérifiez les points suivants : 

   Essayez de redémarrer votre cluster en procédant comme suit :
   1. Dans le volet gauche, sélectionnez **Clusters**.
   1. Sélectionnez le nom de votre cluster dans le tableau.
   1. Sous l’onglet **Bibliothèques**, sélectionnez **Redémarrer**.
      
   Tenez également compte des aspects ci-dessous :
   + Dans la configuration d’Automl, lorsque vous utilisez Azure Databricks, ajoutez les paramètres suivants :
       1. ```max_concurrent_iterations``` est basé sur le nombre de nœuds Worker dans votre cluster. 
        2. ```spark_context=sc``` est basé sur le contexte spark par défaut. 
   + Ou, si vous avez une ancienne version du Kit de développement logiciel (SDK), désélectionnez-la des bibliothèques installées du cluster et déplacez-la vers la corbeille. Installez la nouvelle version du SDK et redémarrez le cluster. En cas de problème après cette opération, détachez et rattachez votre cluster.

Si l’installation a réussi, la bibliothèque importée doit ressembler à l’une de celles présentées ci-dessous :
   
Kit de développement logiciel (SDK) pour Databricks **_sans_** apprentissage automatique automatisé ![Kit de développement logiciel (SDK) Azure Machine Learning pour Databricks](./media/how-to-configure-environment/amlsdk-withoutautoml.jpg)

Kit de développement logiciel (SDK) pour Databricks **AVEC** apprentissage automatique automatisé ![Kit de développement logiciel (SDK) avec apprentissage automatique automatisé installé sur Databricks](./media/how-to-configure-environment/automlonadb.jpg)

### <a name="start-exploring"></a>Commencez à explorer

Lancez-vous :
+ Téléchargez le [fichier d’archive de notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks/Databricks_AMLSDK_1-4_6.dbc) pour le Kit de développement logiciel (SDK) Azure Machine Learning/Azure Databricks et [importez le fichier d’archive](https://docs.azuredatabricks.net/user-guide/notebooks/notebook-manage.html#import-an-archive) dans votre cluster Databricks.  
  Bien que de nombreux exemples de notebooks soient disponibles, **seuls[ ces exemples ](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks) fonctionnent avec Azure Databricks.**
  
+ Découvrez comment [créer un pipeline avec Databricks en tant que cible de calcul de formation](how-to-create-your-first-pipeline.md).

## <a id="aznotebooks"></a>Azure Notebooks

[Azure Notebooks](https://notebooks.azure.com) (préversion) est un environnement de blocs-notes pour le cloud Azure. Il s’agit d’un moyen simple pour démarrer avec le développement Azure Machine Learning.

* Le Kit de développement logiciel (SDK) Azure Machine Learning est déjà installé.
* Après avoir créé un espace de travail Azure Machine Learning service dans le portail Azure, vous pouvez cliquer sur un bouton pour configurer automatiquement votre environnement Azure Notebooks en vue de l’utiliser avec l’espace de travail.

Utilisez le [portail Azure](https://portal.azure.com) pour bien démarrer avec Azure Notebooks.  Ouvrez votre espace de travail et, dans la section **Vue d’ensemble**, sélectionnez **Bien démarrer dans Azure Notebooks**.

Par défaut, Azure Notebooks utilise un niveau de service gratuit limité à 4 Go de mémoire et de 1 Go de données. Cela étant, vous pouvez lever ces limites en attachant une instance Data Science Virtual Machine au projet Azure Notebooks. Pour plus d’informations, consultez [Gérer et configurer des projets Azure Notebooks - Niveau Calcul](/azure/notebooks/configure-manage-azure-notebooks-projects#compute-tier).

## <a id="workspace"></a>Créer un fichier de configuration d’espace de travail

Le fichier de configuration d’espace de travail est un fichier JSON qui indique au SDK comment communiquer avec votre espace de travail Azure Machine Learning service. Le fichier est nommé *config.json*, et il a le format suivant :

```json
{
    "subscription_id": "<subscription-id>",
    "resource_group": "<resource-group>",
    "workspace_name": "<workspace-name>"
}
```

Ce fichier JSON doit se trouver dans la structure de répertoire qui contient vos scripts Python ou vos blocs-notes Jupyter Notebook. Il peut se trouver dans le même répertoire, dans un sous-répertoire nommé *.azureml* ou dans un répertoire parent.

Pour utiliser ce fichier à partir de votre code, utilisez `ws=Workspace.from_config()`. Ce code charge les informations à partir du fichier et se connecte à votre espace de travail.

Il existe trois façons de créer le fichier de configuration :

* **Suivez la procédure indiquée dans [Create an Azure Machine Learning service workspace (Créer un espace de travail Azure Machine Learning service)](setup-create-workspace.md#sdk)** : Un fichier *config.json* est créé dans votre bibliothèque Azure Notebooks. Ce fichier contient les informations de configuration pour votre espace de travail. Vous pouvez télécharger ou copier le fichier *config.json* dans d’autres environnements de développement.

* **Téléchargez le fichier** : Dans le [portail Azure](https://ms.portal.azure.com), sélectionnez **Télécharger config.json** à partir de la section **Vue d’ensemble** de votre espace de travail.

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

    Ce code écrit le fichier config dans le fichier *.azureml/config.json*.


## <a name="next-steps"></a>Étapes suivantes

- [Entraîner un modèle](tutorial-train-models-with-aml.md) sur Azure Machine Learning avec le jeu de données MNIST
- Voir les informations de référence sur le [Kit SDK Azure Machine Learning pour Python](https://aka.ms/aml-sdk)
- En savoir plus sur le [package de préparation des données pour Azure Machine Learning](https://aka.ms/data-prep-sdk)
