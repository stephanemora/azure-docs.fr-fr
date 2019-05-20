---
title: Configuration d’un environnement de développement Python
titleSuffix: Azure Machine Learning service
description: Découvrez comment configurer un environnement de développement quand vous travaillez avec Azure Machine Learning service. Dans cet article, vous allez apprendre à utiliser des environnements Conda, de créer des fichiers de configuration et de configurer votre propre serveur de bloc-notes en nuage, les blocs-notes Jupyter, Azure Databricks, Azure Notebooks, IDE, éditeurs de code et la Machine virtuelle de science des données.
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.topic: conceptual
ms.date: 05/14/2019
ms.custom: seodec18
ms.openlocfilehash: 7be6c9eda6d0a70d929efe4c00f661eb67105820
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/14/2019
ms.locfileid: "65606411"
---
# <a name="configure-a-development-environment-for-azure-machine-learning"></a>Configurer un environnement de développement pour Azure Machine Learning

Dans cet article, vous allez découvrir comment configurer un environnement de développement pour qu’il fonctionne avec Azure Machine Learning service. Le Azure Machine Learning service est indépendant de toute plateforme.

Les seules conditions requises pour votre environnement de développement sont Python 3, Anaconda (pour les environnements isolés) et un fichier de configuration qui contient les informations de votre espace de travail Azure Machine Learning.

Cet article concerne les environnements et les outils suivants :

* Vos propres [portables cloud machine virtuelle](#notebookvm): Utilisez une ressource de calcul dans votre station de travail pour exécuter des blocs-notes Jupyter. Il s’agit du moyen le plus simple pour démarrer, car le Kit de développement logiciel (SDK) Azure Machine Learning est déjà installé.

* [Data Science Virtual Machine (DSVM)](#dsvm) : un environnement de développement/expérimentation préconfiguré dans le cloud Azure, qui est conçu pour le travail de science des données et peut être déployé à l’UC uniquement des instances de machines virtuelles ou aux instances basées sur le GPU. Python 3, Conda, Jupyter Notebook et SDK Azure Machine Learning sont déjà installés. La machine virtuelle est fournie avec les infrastructures, outils et éditeurs Deep Learning et Machine Learning les plus appréciés pour le développement de solutions Machine Learning. C’est probablement l’environnement de développement le plus complet pour le Machine Learning sur la plateforme Azure.

* [Jupyter Notebook](#jupyter) : si vous utilisez déjà le Jupyter Notebook, le Kit de développement logiciel (SDK) contient des fonctionnalités supplémentaires que vous devez installer.

* [Visual Studio Code](#vscode) : si vous utilisez Visual Studio Code, sachez qu’il est doté de certaines extensions utiles que vous pouvez installer.

* [Azure Databricks](#aml-databricks) : une plateforme Analytique données appréciée, basée sur Apache Spark. Découvrez comment obtenir le kit de développement logiciel (SDK) Azure Machine Learning sur votre cluster afin de déployer des modèles.

* [Azure Notebooks](#aznotebooks) : service Jupyter Notebook hébergé dans le cloud Azure. Également un moyen simple pour commencer, car le Kit de développement logiciel Azure Machine Learning est déjà installé.  

Si vous disposez déjà d’un environnement Python 3, ou que vous voulez uniquement obtenir les étapes de base pour installer le SDK, consultez la section [Ordinateur local](#local).

## <a name="prerequisites"></a>Conditions préalables

Un espace de travail de service Microsoft Azure Machine Learning. Pour créer l’espace de travail, consultez [créer un espace de travail du service Azure Machine Learning](setup-create-workspace.md). Un espace de travail est nécessaire pour la prise en main votre propre [serveur notebook nuage](#notebookvm), un [DSVM](#dsvm), [Azure Databricks](#aml-databricks), ou [Azure Notebooks](#aznotebooks).

Pour installer l’environnement du Kit de développement logiciel pour votre [ordinateur local](#local), [serveur Jupyter Notebook](#jupyter) ou [Visual Studio Code](#vscode) vous devez également :

- Soit le [Anaconda](https://www.anaconda.com/download/) ou [Miniconda](https://conda.io/miniconda.html) Gestionnaire de package.

- Sur Linux ou macOS, vous avez besoin de l’interpréteur de commandes Bash.

    > [!TIP]
    > Si vous avez un environnement Linux ou macOS et utilisez un interpréteur de commandes autre que Bash (par exemple, zsh), des erreurs peuvent s’afficher lorsque vous exécutez certaines commandes. Pour contourner ce problème, utilisez la commande `bash` pour démarrer un nouveau shell Bash et y exécuter les commandes.

- Dans Windows, vous avez besoin de l’invite de commandes Windows ou Anaconda (installée par Anaconda et Miniconda).

## <a id="notebookvm"></a>Votre propre cloud-portables de machine virtuelle

La machine virtuelle de bloc-notes (version préliminaire) est une cloud sécurisée, station de travail Azure qui fournit aux chercheurs de données avec un serveur Jupyter notebook, JupyterLab et un environnement ML fin prêt. 

Le bloc-notes est de la machine virtuelle : 

+ **Sécurisé**. Dans la mesure où l’accès VM et notebook est sécurisé avec HTTPS et Azure Active Directory par défaut, les professionnels de l’informatique peuvent appliquer facilement l’authentification unique et autres fonctionnalités de sécurité telles que l’authentification multifacteur.

+ **Préconfigurée**. Cet environnement Python ML entièrement préparé dessine ses généalogique à partir de populaires IaaS Data Science Virtual Machine, qui inclut :
  + Azure ML kit SDK Python (dernière version)
  + Configuration automatique pour travailler avec votre espace de travail
  + Un serveur Jupyter notebook
  + JupyterLab bloc-notes IDE
  + Pilotes GPU préconfigurées 
  + Une sélection des infrastructures d’apprentissage profond
 

  Si vous êtes dans le code, la machine virtuelle inclut des didacticiels et exemples pour vous aider à Explorer et découvrez comment utiliser le service Azure Machine Learning. Les exemples de notebooks sont stockés dans le compte de stockage d’objets Blob Azure de votre espace de travail rendant partageable entre machines virtuelles. Lors de l’exécution, ils ont accès aux magasins de données et ressources de votre espace de travail de calcul. 

+ **Le programme d’installation simple**: Créer une à tout moment à partir de votre espace de travail Azure Machine Learning. Fournir un nom et spécifiez un type de machine virtuelle Azure. Essayez-le dès maintenant avec ce [Guide de démarrage rapide : Utiliser un serveur de bloc-notes basé sur le cloud pour la prise en main Azure Machine Learning](quickstart-run-cloud-notebook.md).

+ **Personnalisable**. Lors de la VM gérée et sécurisée offre, vous conservez un accès complet aux fonctionnalités de matériel et le personnaliser vers le désir de votre choix. Par exemple, créer rapidement la dernière version NVidia V100 alimenté par machine virtuelle pour effectuer un débogage pas à pas de la nouvelle architecture de réseau neuronal.

Pour arrêter la comptabilisation des frais de machine virtuelle de bloc-notes, [arrêter la machine virtuelle de bloc-notes](quickstart-run-cloud-notebook.md#stop-the-notebook-vm). 

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

Lorsque vous utilisez un ordinateur local (qui peut également être une machine virtuelle à distance), créez un environnement Anaconda et installez le SDK en procédant comme suit :

1. Téléchargez et installez [Anaconda](https://www.anaconda.com/distribution/#download-section) (version 3.7 de Python) si vous ne l’avez déjà.

1. Ouvrez une invite de Anaconda et créer un environnement avec les commandes suivantes :

    Exécutez la commande suivante pour créer l’environnement.

    ```shell
    conda create -n myenv python=3.6.5
    ```

    Puis activez l’environnement.

    ```shell
    conda activate myenv
    ```

    Cet exemple crée un environnement à l’aide de python 3.6.5, mais n’importe quel subversions spécifiques peuvent être choisies. Compatibilité du Kit de développement logiciel ne peut pas être garantie avec certaines versions majeures (3.5 + recommandé), et il est recommandé d’essayer une autre version/subversion dans votre environnement Anaconda si vous rencontrez des erreurs. La création de l’environnement prend plusieurs minutes, le temps que les composants et les packages soient téléchargés.

1. Exécutez les commandes suivantes dans votre nouvel environnement pour activer les noyaux spécifiques à l’environnement ipython. Cela garantit le package et le noyau attendu comportement d’importation lorsque vous travaillez avec les blocs-notes Jupyter dans des environnements Anaconda :

    ```shell
    conda install notebook ipykernel
    ```

    Puis exécutez la commande suivante pour créer le noyau :

    ```shell
    ipython kernel install --user
    ```

1. Utilisez les commandes suivantes pour installer des packages :

    Cette commande installe le kit SDK de base Azure Machine Learning avec le bloc-notes et automl extras. Le `automl` supplémentaire est une installation de grande taille et peut être supprimé de crochets si vous ne prévoyez pas d’exécuter des expériences d’apprentissage automatique automatique. Le `automl` supplémentaire inclut également le SDK Azure Machine Learning Data Prep par défaut en tant que dépendance.

     ```shell
    pip install azureml-sdk[notebooks,automl]
    ```

    Utilisez cette commande pour installer le SDK Azure Machine Learning Data Prep sur son propre :

    ```shell
    pip install azureml-dataprep
    ```

   > [!NOTE]
   > Si vous recevez un message indiquant que PyYAML ne peut pas être désinstallé, utilisez plutôt la commande suivante :
   >
   > `pip install --upgrade azureml-sdk[notebooks,automl] azureml-dataprep --ignore-installed PyYAML`

   Il nécessitera plusieurs minutes pour installer le Kit de développement. Consultez le [guide d’installation](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) pour plus d’informations sur les options d’installation.

1. Installez les autres packages pour votre machine learning-expérimentation.

    Utilisez une des commandes suivantes et remplacez  *\<nouveau package >* avec le package que vous souhaitez installer. Installer des packages via `conda install` nécessite que le package fait partie des canaux en cours (les nouveaux canaux peuvent être ajoutés dans le Cloud d’Anaconda).

    ```shell
    conda install <new package>
    ```

    Vous pouvez également installer des packages via `pip`.

    ```shell
    pip install <new package>
    ```

### <a id="jupyter"></a>Jupyter Notebook

Jupyter Notebook fait partie de [Project Jupyter](https://jupyter.org/). Il fournit une expérience de codage interactive dans laquelle vous créez des documents qui associent du code en direct, des graphismes et du texte descriptif. Jupyter Notebook est aussi un excellent moyen de partager vos résultats avec d’autres utilisateurs, car il vous permet d’enregistrer la sortie de vos sections de code dans le document. Vous pouvez installer Jupyter Notebooks sur diverses plateformes.

La procédure décrite dans la [ordinateur Local](#local) section installe les composants nécessaires pour l’exécution des blocs-notes Jupyter dans un environnement Anaconda. Pour activer ces composants dans votre environnement Jupyter Notebook, procédez comme suit :

1. Ouvrez une invite de Anaconda et activer votre environnement.

    ```shell
    conda activate myenv
    ```

1. Lancer le serveur de bloc-notes Jupyter avec la commande suivante :

    ```shell
    jupyter notebook
    ```

1. Pour vérifier que le bloc-notes Jupyter pouvez utiliser le Kit de développement, créez un **New** bloc-notes, sélectionnez **Python 3** comme noyau, puis exécutez la commande suivante dans une cellule du bloc-notes :

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

1. Si vous rencontrez des problèmes d’importation de modules et que vous recevez un `ModuleNotFoundError`, vérifiez que votre noyau Jupyter est connecté pour le chemin d’accès correct pour votre environnement en exécutant le code suivant dans une cellule du bloc-notes.

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
Azure Databricks est un environnement basé sur Apache Spark dans le cloud Azure. Il fournit un environnement de bloc-notes en collaboration avec le cluster de calcul basé sur des UC ou GPU.

Comment Azure Databricks fonctionne avec le service Azure Machine Learning :
+ Vous pouvez former un modèle à l’aide de Spark MLlib et déployer le modèle sur ACI/AKS à partir de dans Azure Databricks. 
+ Vous pouvez également utiliser [automatisée apprentissage](concept-automated-ml.md) fonctionnalités dans un kit spécial de ML avec Azure Databricks.
+ Vous pouvez utiliser Azure Databricks comme cible de calcul à partir d’un [pipeline d’Azure Machine Learning](concept-ml-pipelines.md). 

### <a name="set-up-your-databricks-cluster"></a>Configurer votre cluster Databricks

Créer un [cluster Databricks](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal). Certains paramètres s’appliquent uniquement si vous installez le Kit de développement logiciel pour l’apprentissage automatisé sur Databricks.
**Il prendra quelques minutes pour créer le cluster.**

Utilisez ces paramètres :

| Paramètre |S'applique à| Valeur |
|----|---|---|
| Nom du cluster |toujours| Nom de votre cluster |
| Runtime Databricks |toujours| N’importe quel runtime non-ML (runtime 4.x, 5.x non-ML) |
| Version de Python |toujours| 3 |
| Rôles de travail |toujours| 2 ou plus |
| Types de machines virtuelles pour les nœuds worker <br>(détermine le nombre maximal d’itérations simultanées) |ML automatisé<br>Uniquement| Machine virtuelle à mémoire optimisé, de préférence |
| Activer la mise à l’échelle automatique |ML automatisé<br>Uniquement| Décochez la case |

Attendez que le cluster s’exécute avant de continuer.

### <a name="install-the-correct-sdk-into-a-databricks-library"></a>Installer le Kit de développement logiciel correct dans une bibliothèque de Databricks
Une fois que le cluster est en cours d’exécution, [créer une bibliothèque](https://docs.databricks.com/user-guide/libraries.html#create-a-library) pour attacher le package du Kit de développement logiciel Azure Machine Learning approprié à votre cluster. 

1. Choisissez **qu’une seule** option (aucune autre installation du Kit de développement logiciel ne sont pris en charge)

   |Kit de développement logiciel&nbsp;package&nbsp;extras|Source|PyPi&nbsp;Name&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|
   |----|---|---|
   |Pour Databricks| Charger Python Egg ou PyPI | azureml-sdk[databricks]|
   |Pour Databricks - with-<br> fonctions ML automatisée| Charger Python Egg ou PyPI | azureml-sdk[automl_databricks]|

   > [!Warning]
   > Aucune autres extras SDK ne peuvent être installés. Choisissez uniquement une des options précédentes [databricks] ou [automl_databricks].

   * Ne sélectionnez pas **joindre automatiquement à tous les clusters**.
   * Sélectionnez **attacher** en regard du nom de votre cluster.

1. Surveiller les erreurs jusqu'à ce que l’état **Attached**, ce qui peut prendre plusieurs minutes.  Si cette étape échoue, vérifiez les points suivants : 

   Essayez de redémarrer votre cluster en :
   1. Dans le volet gauche, sélectionnez **Clusters**.
   1. Sélectionnez le nom de votre cluster dans le tableau.
   1. Sous l’onglet **Bibliothèques**, sélectionnez **Redémarrer**.
      
   Tenez également compte :
   + Dans la configuration de Automl, lorsque vous utilisez Azure Databricks ajoutez les paramètres suivants :
       1. ```max_concurrent_iterations``` repose sur le nombre de nœuds de travail dans votre cluster. 
        2. ```spark_context=sc``` repose sur le contexte spark par défaut. 
   + Ou, si vous avez une ancienne version du Kit de développement logiciel, désactivez l’option de bibliothèques installées du cluster et déplacer vers la Corbeille. Installez la nouvelle version du SDK et redémarrez le cluster. En cas de problème après cette opération, détachez et rattachez votre cluster.

Si l’installation a réussi, la bibliothèque importée doit ressembler à un d’eux :
   
Kit de développement logiciel pour Databricks **_sans_** automatisée apprentissage ![Machine Learning Kit de développement pour Databricks](./media/how-to-configure-environment/amlsdk-withoutautoml.jpg)

Kit de développement logiciel pour Databricks **WITH** automatisée apprentissage ![SDK avec automatisée apprentissage installé sur Databricks](./media/how-to-configure-environment/automlonadb.jpg)

### <a name="start-exploring"></a>Commencez à Explorer

Faites un essai :
+ Téléchargez le [fichier d’archive bloc-notes](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks/Databricks_AMLSDK_1-4_6.dbc) pour le Kit de développement logiciel Azure Databricks/Azure Machine Learning et [importer le fichier d’archive](https://docs.azuredatabricks.net/user-guide/notebooks/notebook-manage.html#import-an-archive) dans votre cluster Databricks.  
  Bien que de nombreux exemples de bloc-notes sont disponibles, **uniquement [ces exemples de notebooks](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks) fonctionnent avec Azure Databricks.**
  
+ Découvrez comment [créer un pipeline avec Databricks en tant que le calcul de la formation](how-to-create-your-first-pipeline.md).

## <a id="aznotebooks"></a>Azure Notebooks

[Azure Notebooks](https://notebooks.azure.com) (préversion) est un environnement de blocs-notes pour le cloud Azure. Il est un moyen simple de bien démarrer avec le développement d’Azure Machine Learning.

* Le Kit de développement logiciel (SDK) Azure Machine Learning est déjà installé.
* Après avoir créé un espace de travail Azure Machine Learning service dans le portail Azure, vous pouvez cliquer sur un bouton pour configurer automatiquement votre environnement Azure Notebooks en vue de l’utiliser avec l’espace de travail.

Utilisez le [portail Azure](https://portal.azure.com) pour bien démarrer avec Azure Notebooks.  Ouvrez votre espace de travail et à partir de la **vue d’ensemble** section, sélectionnez **prise en main de Azure Notebooks**.

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

Ce fichier JSON doit se trouver dans la structure de répertoire qui contient vos scripts Python ou vos blocs-notes Jupyter Notebook. Il peut être dans le même répertoire, un sous-répertoire nommé *.azureml*, ou dans un répertoire parent.

Pour utiliser ce fichier à partir de votre code, utilisez `ws=Workspace.from_config()`. Ce code charge les informations à partir du fichier et se connecte à votre espace de travail.

Il existe trois façons de créer le fichier de configuration :

* **Suivez les étapes de [créer un espace de travail du service Azure Machine Learning](setup-create-workspace.md#sdk)**: Un fichier *config.json* est créé dans votre bibliothèque Azure Notebooks. Ce fichier contient les informations de configuration pour votre espace de travail. Vous pouvez télécharger ou copier le fichier *config.json* dans d’autres environnements de développement.

* **Téléchargez le fichier**: Dans le [portail Azure](https://ms.portal.azure.com), sélectionnez **Télécharger config.json** à partir de la section **Vue d’ensemble** de votre espace de travail.

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

    Ce code écrit le fichier de configuration pour le *.azureml/config.json* fichier.


## <a name="next-steps"></a>Étapes suivantes

- [Entraîner un modèle](tutorial-train-models-with-aml.md) sur Azure Machine Learning avec le jeu de données MNIST
- Voir les informations de référence sur le [Kit SDK Azure Machine Learning pour Python](https://aka.ms/aml-sdk)
- En savoir plus sur le [package de préparation des données pour Azure Machine Learning](https://aka.ms/data-prep-sdk)
