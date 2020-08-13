---
title: Configuration d’un environnement de développement Python
titleSuffix: Azure Machine Learning
description: Apprenez à configurer votre environnement de développement pour Azure Machine Learning. Utilisez des environnements Conda, créez des fichiers de configuration et configurez votre propre serveur de notebooks basé sur le cloud, des notebooks Jupyter, Azure Databricks, des IDE, des éditeurs de code et la machine Data Science Virtual Machine.
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.date: 12/27/2019
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 09d82c4834e50e0dff74edb5ccd14287736b937e
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/06/2020
ms.locfileid: "87844471"
---
# <a name="configure-a-development-environment-for-azure-machine-learning"></a>Configurer un environnement de développement pour Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Dans cet article, vous allez découvrir comment configurer un environnement de développement pour qu’il fonctionne avec Azure Machine Learning. Azure Machine Learning est indépendant de toute plateforme. La seule exigence matérielle pour votre environnement de développement est Python 3. Un environnement isolé comme Anaconda ou Virtualenv est également recommandé.

Le tableau suivant présente chaque environnement de développement évoqué dans cet article, ainsi que ses avantages et inconvénients.

| Environnement | Avantages | Inconvénients |
| --- | --- | --- |
| [Instance de calcul Azure Machine Learning basée sur le cloud (préversion)](#compute-instance) | Méthode la plus simple pour la mise en route. Le Kit de développement logiciel (SDK) entier est déjà installé sur votre machine virtuelle d’espace de travail, et les didacticiels du Notebook sont pré-clonés et prêts pour exécution. | Manque de contrôle de votre environnement de développement et des dépendances. Coût supplémentaire pour la machine virtuelle Linux (la machine virtuelle peut être arrêtée lorsqu’elle n’est pas utilisée pour éviter des frais). Consultez les [détails de la tarification](https://azure.microsoft.com/pricing/details/virtual-machines/linux/). |
| [Environnement local](#local) | Contrôle total de votre environnement de développement et des dépendances. Exécutez avec n’importe quel outil de build, environnement ou IDE de votre choix. | La mise en route prend plus de temps. Les packages de Kit de développement logiciel (SDK) nécessaires doivent être installés, ainsi qu’un environnement si vous n’en avez pas encore. |
| [Azure Databricks](#aml-databricks) | Idéal pour l'exécution de flux de travail de Machine Learning intensifs à grande échelle sur la plateforme Apache Spark évolutive. | Excessif pour du Machine Learning expérimental ou des expériences et des flux de travail à plus petite échelle. Coût supplémentaire pour Azure Databricks. Consultez les [détails de la tarification](https://azure.microsoft.com/pricing/details/databricks/). |
| [Data Science Virtual Machine (DSVM)](#dsvm) | Similaire à l’instance de calcul basée sur le cloud (Python et le SDK sont préinstallés), mais avec d’autres outils connus de science des données et de machine learning préinstallés. Facile à mettre à l’échelle et à combiner avec d’autres outils et flux de travail personnalisés. | Expérience de démarrage plus lente que celle de l’instance de calcul basée sur le cloud. |

Cet article fournit également des conseils d’utilisation supplémentaires pour les outils suivants :

* [Jupyter Notebooks](#jupyter) : si vous utilisez déjà le Jupyter Notebook, le Kit de développement logiciel (SDK) contient des fonctionnalités supplémentaires que vous devez installer.

* [Visual Studio Code](#vscode) : si vous utilisez Visual Studio Code, l’[extension Azure Machine Learning](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai) inclut une prise en charge complète du langage Python ainsi que diverses caractéristiques permettant de travailler avec Azure Machine Learning beaucoup plus facilement et rapidement.

## <a name="prerequisites"></a>Prérequis

Un espace de travail Azure Machine Learning. Pour créer l’espace de travail, voir [Créer un espace de travail Azure Machine Learning](how-to-manage-workspace.md). Un espace de travail est tout ce dont vous avez besoin pour commencer à utiliser votre propre [serveur de notebooks basé sur le cloud](#compute-instance), une [DSVM](#dsvm) ou [Azure Databricks](#aml-databricks).

Pour installer l’environnement du Kit de développement logiciel (SDK) pour votre [ordinateur local](#local), [serveur Jupyter Notebook](#jupyter) ou [Visual Studio Code](#vscode) vous avez également besoin des éléments suivants :

- gestionnaire de package [Anaconda](https://www.anaconda.com/download/) ou [Miniconda](https://conda.io/miniconda.html).

- Sur Linux ou macOS, vous avez besoin de l’interpréteur de commandes Bash.

    > [!TIP]
    > Si vous avez un environnement Linux ou macOS et utilisez un interpréteur de commandes autre que Bash (par exemple, zsh), des erreurs peuvent s’afficher lorsque vous exécutez certaines commandes. Pour contourner ce problème, utilisez la commande `bash` pour démarrer un nouveau shell Bash et y exécuter les commandes.

- Dans Windows, vous avez besoin de l’invite de commandes Windows ou Anaconda (installée par Anaconda et Miniconda).

## <a name="your-own-cloud-based-compute-instance"></a><a id="compute-instance"></a>Votre propre instance de calcul basée sur le cloud

L’[instance de calcul Azure Machine Learning (préversion)](concept-compute-instance.md) est une station de travail Azure sécurisée et basée sur le cloud, qui fournit aux scientifiques des données un serveur de notebooks Jupyter, JupyterLab et un environnement ML prêt à l’emploi.

Vous n’avez rien à installer ou à configurer pour une instance de calcul.  créez à tout moment depuis votre espace de travail Azure Machine Learning. Indiquez juste un nom et spécifiez un type de machine virtuelle Azure. Essayez dès maintenant avec ce [Didacticiel : Configurer l'environnement et l'espace de travail](tutorial-1st-experiment-sdk-setup.md).

Pour en savoir plus sur les instances de calcul, notamment comment installer des paquets, consultez [instances de calcul](concept-compute-instance.md).

Pour mettre fin à la facturation des frais de calcul, [arrêtez l’instance de calcul](tutorial-1st-experiment-sdk-train.md#clean-up-resources).

## <a name="data-science-virtual-machine"></a><a id="dsvm"></a>Data Science Virtual Machine

Une Data Science Virtual Machine (DSVM) ou « Machine virtuelle pour la science des données » est une image de machine virtuelle (VM) personnalisée. Elle est conçue pour les travaux de science des données préconfigurés avec :

  - Des packages tels que TensorFlow, PyTorch, Scikit-learn, XGBoost et le Kit de développement logiciel (SDK) Azure Machine Learning.
  - Des outils de science des données appréciés tels que Spark Standalone et Drill.
  - Des outils Azure tels que Azure CLI, AzCopy et Explorateur Stockage.
  - Des environnements de développement intégrés (IDE) tels que Visual Studio Code et PyCharm.
  - Serveur Jupyter Notebook

Le Kit de développement logiciel (SDK) Azure Machine Learning fonctionne aussi bien sur la version Windows que sur la version Ubuntu de la DSVM. Cela étant, si vous envisagez d’utiliser également DSVM comme cible de calcul, seule la version Ubuntu est prise en charge.

Pour utiliser DSVM comme environnement de développement, suivez ces étapes :

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

            ```azurecli-interactive
            # create a Ubuntu DSVM in your resource group
            # note you need to be at least a contributor to the resource group in order to execute this command successfully
            # If you need to create a new resource group use: "az group create --name YOUR-RESOURCE-GROUP-NAME --location YOUR-REGION (For example: westus2)"
            az vm create --resource-group YOUR-RESOURCE-GROUP-NAME --name YOUR-VM-NAME --image microsoft-dsvm:linux-data-science-vm-ubuntu:linuxdsvmubuntu:latest --admin-username YOUR-USERNAME --admin-password YOUR-PASSWORD --generate-ssh-keys --authentication-type password
            ```

        * Pour créer une instance de Data Science Virtual Machine pour Windows, utilisez la commande suivante :

            ```azurecli-interactive
            # create a Windows Server 2016 DSVM in your resource group
            # note you need to be at least a contributor to the resource group in order to execute this command successfully
            az vm create --resource-group YOUR-RESOURCE-GROUP-NAME --name YOUR-VM-NAME --image microsoft-dsvm:dsvm-windows:server-2016:latest --admin-username YOUR-USERNAME --admin-password YOUR-PASSWORD --authentication-type password
            ```

2. Le Kit de développement logiciel (SDK) Azure Machine Learning est déjà installé sur la DSVM. Pour utiliser l’environnement Conda qui contient le SDK, utilisez l’une des commandes suivantes :

    * Pour une DSVM Ubuntu :

        ```bash
        conda activate py36
        ```

    * Pour une DSVM Windows :

        ```bash
        conda activate AzureML
        ```

1. Pour vérifier si vous pouvez accéder au SDK afin d’en connaître la version, utilisez le code Python suivant :

    ```python
    import azureml.core
    print(azureml.core.VERSION)
    ```

1. Pour configurer la DSVM pour utiliser votre espace de travail Azure Machine Learning, voir la section [Créer un fichier de configuration d’espace de travail](#workspace).

Pour plus d’informations, consultez la page [Machines virtuelles Science des données](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/).

## <a name="local-computer"></a><a id="local"></a>Ordinateur local

Lorsque vous utilisez un ordinateur local (qui peut également être une machine virtuelle distante), créez un environnement Anaconda et installez le SDK. Voici un exemple :

1. Si vous ne l’avez pas déjà, téléchargez et installez [Anaconda](https://www.anaconda.com/distribution/#download-section) (Python version 3.7).

1. Ouvrez une invite de commandes Anaconda et créez un environnement avec les commandes suivantes :

    Exécutez la commande suivante pour créer l’environnement.

    ```bash
    conda create -n myenv python=3.7.7
    ```

    Activez ensuite l’environnement.

    ```bash
    conda activate myenv
    ```

    Cet exemple crée un environnement à l’aide de Python 3.7.7, mais n’importe quelle sous-version spécifique peut être choisie. La compatibilité du Kit de développement logiciel (SDK) ne peut pas être garantie avec certaines versions principales (versions 3.5 et supérieures recommandées), et il est recommandé d’essayer une autre version/sous-version dans votre environnement Anaconda si vous rencontrez des erreurs. La création de l’environnement prend plusieurs minutes, le temps que les composants et les packages soient téléchargés.

1. Exécutez les commandes suivantes dans votre nouvel environnement pour activer les noyaux IPython spécifiques à l’environnement. Cela permet de s’assurer du comportement d’importation du package et du noyau attendu lors de l’utilisation de Jupyter Notebook dans des environnements Anaconda :

    ```bash
    conda install notebook ipykernel
    ```

    Exécutez ensuite la commande ci-dessous pour créer le noyau :

    ```bash
    ipython kernel install --user --name myenv --display-name "Python (myenv)"
    ```

1. Utilisez les commandes suivantes pour installer les packages :

    Cette commande installe le SDK Azure Machine Learning de base avec le notebook et des suppléments `automl`. Le supplément `automl` est une installation volumineuse, qui peut être retirée si vous ne prévoyez pas d’exécuter des expériences d’apprentissage automatique automatisées. Le supplément `automl` inclut également le Kit de développement logiciel (SDK) de préparation de données Azure Machine Learning par défaut en tant que dépendance.

    ```bash
    pip install azureml-sdk[notebooks,automl]
    ```

   > [!NOTE]
   > * Si vous recevez un message indiquant que PyYAML ne peut pas être désinstallé, utilisez plutôt la commande suivante :
   >
   >   `pip install --upgrade azureml-sdk[notebooks,automl] --ignore-installed PyYAML`
   >
   > * À partir de macOS Catalina, zsh (interpréteur de commandes Z) est l'interpréteur de commandes de connexion par défaut et l'interpréteur de commandes interactif. Dans zsh, utilisez la commande suivante qui échappe les crochets à l'aide de « \\ » (barre oblique inverse) :
   >
   >   `pip install --upgrade azureml-sdk\[notebooks,automl\]`

   L’installation du Kit de développement logiciel (SDK) peut prendre plusieurs minutes. Pour plus d'informations sur les options d'installation, consultez le [guide d'installation](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

1. Installez d’autres packages pour votre expérimentation d’apprentissage automatique.

    Utilisez l’une des commandes suivantes et remplacez *\<new package>* par le package que vous souhaitez installer. Installer des packages via `conda install` nécessite que le package fasse partie des chaînes actuelles (les nouvelles chaînes peuvent être ajoutées dans Anaconda Cloud).

    ```bash
    conda install <new package>
    ```

    Vous pouvez également installer des packages via `pip`.

    ```bash
    pip install <new package>
    ```

### <a name="jupyter-notebooks"></a><a id="jupyter"></a>Jupyter Notebook

Jupyter Notebook fait partie de [Project Jupyter](https://jupyter.org/). Il fournit une expérience de codage interactive dans laquelle vous créez des documents qui associent du code en direct, des graphismes et du texte descriptif. Jupyter Notebook est aussi un excellent moyen de partager vos résultats avec d’autres utilisateurs, car il vous permet d’enregistrer la sortie de vos sections de code dans le document. Vous pouvez installer Jupyter Notebooks sur diverses plateformes.

La procédure décrite dans la section [Ordinateur Local](#local) installe les composants nécessaires pour l’exécution de Jupyter Notebooks dans un environnement Anaconda.

Pour activer ces composants dans votre environnement Jupyter Notebook :

1. Ouvrez une invite de commandes Anaconda et activez votre environnement.

    ```bash
    conda activate myenv
    ```

1. Clonez le [dépôt GitHub](https://aka.ms/aml-notebooks) pour un ensemble d’exemples de notebooks.

    ```bash
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Lancez le serveur Jupyter Notebook avec la commande suivante :

    ```bash
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

1. Pour configurer le Jupyter Notebook pour utiliser votre espace de travail Azure Machine Learning, voir la section [Créer un fichier de configuration d’espace de travail](#workspace).

### <a name="visual-studio-code"></a><a id="vscode"></a>Visual Studio Code

Visual Studio Code est un éditeur de code multiplateforme très populaire qui prend en charge un large éventail d’outils et de langages de programmation par le biais des extensions disponibles dans [Visual Studio Marketplace](https://marketplace.visualstudio.com/vscode). L’[extension Azure Machine Learning](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai) installe l’[extension Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python) qui vous permet de programmer dans tous les types d’environnements Python (virtuel, Anaconda, etc.). De plus, elle comporte des fonctionnalités utiles qui vous permettent d’utiliser des ressources Azure Machine Learning et d’exécuter des expériences Azure Machine Learning sans avoir à quitter Visual Studio Code.

Pour utiliser Visual Studio Code dans le cadre du développement :

1. Installez l’extension Azure Machine Learning pour Visual Studio Code, comme cela est décrit dans [Azure Machine Learning](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai).

    Pour plus d’informations, consultez la page [Premiers pas avec Azure Machine Learning pour Visual Studio Code](tutorial-setup-vscode-extension.md).

1. Apprenez à utiliser Visual Studio Code pour le développement Python de tout type, en lisant [Getting Started with Python in VS Code](https://code.visualstudio.com/docs/python/python-tutorial).

    - Pour sélectionner l’environnement SDK Python contenant le SDK, ouvrez VS Code, puis utilisez Ctrl+Maj+P (Linux et Windows) ou Commande+Maj+P (Mac).
        - La __palette de commandes__ s’ouvre.

    - Entrez __Python: Select Interpreter__, puis sélectionnez l’environnement approprié.

1. Pour vérifier que vous pouvez utiliser le SDK, créez un fichier Python (.py) qui contient le code suivant :

    ```python
    #%%
    import azureml.core
    azureml.core.VERSION
    ```
    Exécutez ce code en cliquant sur l’option CodeLens « Exécuter la cellule » ou en appuyant simplement sur Maj+Entrée.
<a name="aml-databricks"></a>

## <a name="azure-databricks"></a>Azure Databricks
Azure Databricks est un environnement basé sur Apache Spark dans le cloud Azure. Il fournit un environnement basé sur Notebook collaboratif avec un cluster de calcul basé sur l'UC ou le GPU.

Comment Azure Databricks fonctionne avec Azure Machine Learning :
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
| Runtime Databricks |toujours|Runtime non-ML 6.5 (Scala 2.11, Spark 2.4.3) |
| Version Python |toujours| 3 |
| Workers |toujours| 2 ou plus |
| Types de machines virtuelles pour les nœuds worker <br>(détermine le nombre maximal d’itérations concurrentes) |ML automatisé<br>uniquement| Machine virtuelle à mémoire optimisé, de préférence |
| Activer la mise à l’échelle automatique |ML automatisé<br>uniquement| Décochez la case |

Attendez que le cluster s’exécute avant de continuer.

### <a name="install-the-correct-sdk-into-a-databricks-library"></a>Installation du Kit de développement logiciel (SDK) correct dans une bibliothèque Databricks
Une fois que le cluster est en cours d’exécution, [créez une bibliothèque](https://docs.databricks.com/user-guide/libraries.html#create-a-library) pour associer le package du Kit de développement logiciel (SDK) Azure Machine Learning approprié à votre cluster.

1. Cliquez avec le bouton de droite sur le dossier de l’espace de travail actuel dans lequel vous souhaitez stocker la bibliothèque. Sélectionnez **Créer** > **Bibliothèque**.

1. Ne choisissez **qu’une seule** option (aucune autre installation de Kit de développement logiciel (SDK) n’est prise en charge)

   |Suppléments de &nbsp;packages&nbsp; de Kit de développement logiciel (SDK)|Source|Nom&nbsp;PyPi&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|
   |----|---|---|
   |Pour Databricks| Charger Python Egg ou PyPI | azureml-sdk[databricks]|
   |Pour Databricks - avec-<br> des fonctionnalités ML automatisées| Charger Python Egg ou PyPI | azureml-sdk[automl]|

   > [!Warning]
   > Aucun autre supplément de Kit de développement logiciel (SDK) ne peut être installé. Choisissez uniquement une des options précédentes [databricks] ou [automl].

   * Ne sélectionnez pas **Attacher automatiquement à tous les clusters**.
   * Sélectionnez **Attacher** en regard du nom de votre cluster.

1. Surveillez les erreurs jusqu’à ce que l’état soit défini sur **Attaché**, ce qui peut prendre plusieurs minutes.  Si cette étape échoue :

   Essayez de redémarrer votre cluster en procédant comme suit :
   1. Dans le volet gauche, sélectionnez **Clusters**.
   1. Sélectionnez le nom de votre cluster dans le tableau.
   1. Sous l’onglet **Bibliothèques**, sélectionnez **Redémarrer**.

   Tenez également compte des aspects ci-dessous :
   + Dans la configuration d'AutoML, lorsque vous utilisez Azure Databricks, ajoutez les paramètres suivants :
       1. ```max_concurrent_iterations``` est basé sur le nombre de nœuds Worker dans votre cluster.
        2. ```spark_context=sc``` est basé sur le contexte spark par défaut.
   + Ou, si vous avez une ancienne version du Kit de développement logiciel (SDK), désélectionnez-la des bibliothèques installées du cluster et déplacez-la vers la corbeille. Installez la nouvelle version du SDK et redémarrez le cluster. En cas de problème après le redémarrage, détachez et rattachez votre cluster.

Si l’installation a réussi, la bibliothèque importée doit ressembler à l’une de celles présentées ci-dessous :

Kit de développement logiciel (SDK) pour Databricks **_sans_** apprentissage automatique automatisé ![Kit de développement logiciel (SDK) Azure Machine Learning pour Databricks](./media/how-to-configure-environment/amlsdk-withoutautoml.jpg)

Kit de développement logiciel (SDK) pour Databricks **AVEC** apprentissage automatique automatisé ![Kit de développement logiciel (SDK) avec apprentissage automatique automatisé installé sur Databricks](./media/how-to-configure-environment/automlonadb.png)

### <a name="start-exploring"></a>Commencez à explorer

Lancez-vous :
+ Bien que de nombreux exemples de notebooks soient disponibles, **seuls[ ces exemples ](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks) fonctionnent avec Azure Databricks.**

+ Importez ces exemples directement à partir de votre espace de travail. Voir ci-dessous : ![Sélectionner Importer](./media/how-to-configure-environment/azure-db-screenshot.png)
![Panneau Importer](./media/how-to-configure-environment/azure-db-import.png)

+ Découvrez comment [créer un pipeline avec Databricks en tant que cible de calcul de formation](how-to-create-your-first-pipeline.md).

## <a name="create-a-workspace-configuration-file"></a><a id="workspace"></a>Créer un fichier de configuration d’espace de travail

Le fichier de configuration d’espace de travail est un fichier JSON qui indique au SDK comment communiquer avec votre espace de travail Azure Machine Learning. Le fichier est nommé *config.json*, et il a le format suivant :

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

* **Utilisez [WS. write_config](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)** : pour écrire un fichier *config.json*. Ce fichier contient les informations de configuration pour votre espace de travail. Vous pouvez télécharger ou copier le fichier *config.json* dans d’autres environnements de développement.

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
- Voir les informations de référence sur le [Kit SDK Azure Machine Learning pour Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
