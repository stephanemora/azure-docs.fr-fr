---
title: Créer un espace de travail
titleSuffix: Azure Machine Learning service
description: Utilisez le portail Azure, le kit de développement logiciel (SDK), un modèle ou l'interface CLI pour créer votre espace de travail Azure Machine Learning service. Cet espace de travail fournit un emplacement centralisé dans lequel utiliser tous les artefacts que vous créez lorsque vous utilisez Azure Machine Learning service.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: sgilley
ms.author: sgilley
author: sdgilley
ms.date: 05/21/2019
ms.openlocfilehash: 36f3d421ee0b41a0ff71b549a4d4b5646188c3fa
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66417355"
---
# <a name="create-an-azure-machine-learning-service-workspace"></a>Créer un espace de travail Azure Machine Learning service

Pour utiliser Azure Machine Learning service, vous devez disposer d’un [**espace de travail Azure Machine Learning service**](concept-workspace.md).  Cet espace de travail correspond à la ressource de niveau supérieur du service, et vous offre un emplacement centralisé où utiliser tous les artefacts que vous créez. 

Dans cet article, vous allez apprendre à créer un espace de travail en utilisant l’une des méthodes suivantes : 
* L'interface du [portail Azure](#portal)
* Le [kit de développement logiciel (SDK) Azure Machine Learning pour Python](#sdk)
* Un modèle Azure Resource Manager
* L'[interface CLI Azure Machine Learning](#cli)

L’espace de travail que vous créez à l’aide de la procédure décrite ici peut faire office de prérequis à d'autres tutoriels et guides pratiques.

Si vous souhaitez utiliser un script pour configurer Machine Learning automatisé dans un environnement Python local, consultez le [GitHub Azure/MachineLearningNoteboo](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning) afin d'obtenir des instructions.  

Lorsque vous créez un espace de travail, les ressources Azure suivantes sont automatiquement ajoutées (lorsqu’elles sont disponibles au niveau régional) :
 
- [Azure Container Registry](https://azure.microsoft.com/services/container-registry/) : Pour réduire les coûts, ACR est **chargé en différé** jusqu’à la création des images de déploiement.
- [Stockage Azure](https://azure.microsoft.com/services/storage/)
- [Azure Application Insights](https://azure.microsoft.com/services/application-insights/) 
- [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)

>[!Note]
>À l’instar des autres services Azure, certains quotas et limites sont associés au Machine Learning. [En savoir plus sur les quotas et comment demander à les relever.](how-to-manage-quotas.md)


## <a name="prerequisites"></a>Prérequis
Pour créer un espace de travail, vous avez besoin d’un abonnement Azure. Si vous n’avez pas d’abonnement Azure, créez un compte gratuit avant de commencer. Essayez dès aujourd'hui la [version gratuite ou payante d’Azure Machine Learning service](https://aka.ms/AMLFree).

## <a name="portal"></a> Portail Azure

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

Quelle que soit la manière dont il a été créé, vous pouvez afficher votre espace de travail dans le [portail Azure](https://portal.azure.com/).  Pour plus d'informations, consulter [Afficher un espace de travail](how-to-manage-workspace.md#view).

## <a name="sdk"></a> Kit de développement logiciel (SDK) Python

Créez votre espace de travail à l’aide du kit de développement logiciel (SDK) Python. Vous devez d’abord installer le kit de développement logiciel (SDK).

> [!IMPORTANT]
> Ignorez l'installation du kit de développement logiciel (SDK) si vous utilisez une machine virtuelle Azure DSVM (Data Science Virtual Machine) ou Azure Databricks.
> * Le kit SDK Python est préinstallé sur les machines Azure DSVM créées après le 27 septembre 2018. Ignorez l’installation et consultez [Créer un espace de travail avec le kit de développement logiciel (SDK)](#sdk-create).
> * Dans l’environnement Azure Databricks, suivez à la place les [étapes d’installation pour Databricks](how-to-configure-environment.md#azure-databricks).

>[!NOTE]
> Utilisez ces instructions pour installer et utiliser le kit de développement logiciel (SDK) à partir de votre ordinateur local. Pour utiliser Jupyter sur une machine virtuelle distante, configurez une session Bureau à distance ou de terminal X.

Avant d’installer le Kit de développement logiciel (SDK), nous vous recommandons de créer un environnement Python isolé. Bien que cet article utilise [Miniconda](https://docs.conda.io/en/latest/miniconda.html), vous pouvez également utiliser [Anaconda](https://www.anaconda.com/) complet installé ou un [environnement virtuel Python](https://virtualenv.pypa.io/en/stable/).

Les instructions contenues dans cet article permettent d’installer tous les packages nécessaires à l’exécution des notebooks de démarrage rapide et de tutoriel.  D’autres exemples de notebooks peuvent nécessiter l’installation de composants supplémentaires.  Pour plus d’informations sur ces composants, consultez [Installer le kit SDK Azure Machine Learning pour Python](https://docs.microsoft.com/python/api/overview/azure/ml/install).

### <a name="install-miniconda"></a>Installation de Miniconda

[Téléchargez et installez Miniconda](https://docs.conda.io/en/latest/miniconda.html). Sélectionnez la version de Python à installer (3.7). Ne sélectionnez pas la version 2.x de Python.  

### <a name="create-an-isolated-python-environment"></a>Création d’un environnement isolé de Python

1. Ouvrez une invite Anaconda, puis créez un environnement conda nommé *myenv* et installez Python 3.6.5. Le kit SDK Azure Machine Learning fonctionne avec Python 3.5.2 ou ultérieur, mais les composants du machine learning automatisé ne sont pas entièrement fonctionnels sur Python 3.7.  La création de l’environnement prend plusieurs minutes, le temps que les composants et les packages soient téléchargés. 

    ```shell
    conda create -n myenv python=3.6.5
    ```

1. Active l’environnement.

    ```shell
    conda activate myenv
    ```

1. Activez les noyaux ipython spécifiques à l’environnement :

    ```shell
    conda install notebook ipykernel
    ```

    Créez ensuite le noyau :

    ```shell
    ipython kernel install --user
    ```

### <a name="install-the-sdk"></a>Installer le Kit de développement logiciel (SDK)

1. Dans l’environnement conda activé, installez les composants de base du kit SDK Machine Learning avec des fonctionnalités de notebook Jupyter. L’installation prend quelques minutes, selon la configuration de votre ordinateur.

    ```shell
    pip install --upgrade azureml-sdk[notebooks]
    ```

1. Pour utiliser cet environnement pour les tutoriels Azure Machine Learning, installez ces packages.

    ```shell
    conda install -y cython matplotlib pandas
    ```

1. Pour utiliser cet environnement pour les tutoriels Azure Machine Learning, installez les composants du machine learning automatisé.

    ```shell
    pip install --upgrade azureml-sdk[automl]
    ```

> [!IMPORTANT]
> Dans certains outils en ligne de commande, vous serez peut-être amené à ajouter des guillemets comme ceci :
> *  'azureml-sdk[notebooks]'
> * 'azureml-sdk[automl]'
>

### <a name='sdk-create'></a> Créer un espace de travail avec le kit de développement logiciel (SDK)

Créez votre espace de travail dans un notebook Jupyter à l’aide du kit SDK Python.

1. Créez un répertoire et/ou accédez (cd) à celui que vous souhaitez utiliser pour le guide de démarrage rapide et les tutoriels.

1. Pour lancer Jupyter Notebook, entrez la commande suivante :

    ```shell
    jupyter notebook
    ```

1. Dans la fenêtre du navigateur, créez un nouveau bloc-notes avec le noyau `Python 3` par défaut. 

1. Pour afficher la version du Kit de développement logiciel (SDK), entrez puis exécutez le code Python suivant dans une cellule de bloc-notes :

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=import)]

1. Recherchez une valeur pour le paramètre `<azure-subscription-id>` dans la [liste des abonnements dans le Portail Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Utilisez n’importe quel abonnement dans lequel votre rôle est propriétaire ou contributeur. Pour plus d'informations sur les rôles, consultez l'article [Gérer l'accès à un espace de travail Azure Machine Learning](how-to-assign-roles.md).

   ```python
   from azureml.core import Workspace
   ws = Workspace.create(name='myworkspace',
                         subscription_id='<azure-subscription-id>', 
                         resource_group='myresourcegroup',
                         create_resource_group=True,
                         location='eastus2' 
                        )
   ```

   Lorsque vous exécutez le code, vous pouvez être invité à vous connecter à votre compte Azure. Une fois la connexion effectuée, le jeton d’authentification est mis en cache localement.

1. Pour afficher les détails de l’espace de travail, notamment le stockage associé, le registre des conteneurs et le coffre de clés, entrez le code suivant :

    [!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=getDetails)]


### <a name="write-a-configuration-file"></a>Création d’un fichier de configuration

Enregistrez les détails de votre espace de travail dans un fichier de configuration se trouvant dans le répertoire courant. Ce fichier s’appelle *.azureml/config.json*.  

Ce fichier de configuration de l’espace de travail facilite son chargement ultérieur. Vous pouvez le charger avec d’autres blocs-notes et scripts dans le même répertoire ou dans un sous répertoire à l'aide du code `ws=Workspace.from_config()`. 

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=writeConfig)]

L’appel d’API `write_config()` crée le fichier de configuration dans le répertoire actif. Le fichier *.azureml/config.json* contient le code suivant :

```json
{
    "subscription_id": "<azure-subscription-id>",
    "resource_group": "myresourcegroup",
    "workspace_name": "myworkspace"
}
```

> [!TIP]
> Pour utiliser votre espace de travail dans des scripts Python ou Jupyter Notebooks situés dans d’autres répertoires, copiez ce fichier dans le répertoire en question. Le fichier peut se trouver dans le même répertoire, dans un sous-répertoire nommé *.azureml* ou dans un répertoire parent.

## <a name="resource-manager-template"></a>Modèle Azure Resource Manager

Pour créer un espace de travail à l'aide d'un modèle, consultez [Créer un espace de travail Azure Machine Learning service à l’aide d’un modèle](how-to-create-workspace-template.md)

<a name="cli"></a>
## <a name="command-line-interface"></a>Interface de ligne de commande

Pour créer un espace de travail avec l’interface CLI, consultez [Utiliser l’extension CLI pour Azure Machine Learning service](reference-azure-machine-learning-cli.md).

## <a name="clean-up-resources"></a>Supprimer des ressources 

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Étapes suivantes

* Quelle que soit la manière dont il a été créé, vous pouvez afficher votre espace de travail dans le [portail Azure](https://portal.azure.com/).  Pour plus d'informations, consulter [Afficher un espace de travail](how-to-manage-workspace.md#view).

* Essayez votre espace de travail avec ces démarrages rapides et tutoriels.

    * Démarrage rapide : [Exécutez Jupyter Notebook dans le cloud](quickstart-run-cloud-notebook.md).
    * Démarrage rapide : [Exécutez Jupyter Notebook sur votre propre serveur](quickstart-run-local-notebook.md).
    * Tutoriel en deux parties : [Effectuez l'apprentissage](tutorial-train-models-with-aml.md) et [déployez](tutorial-deploy-models-with-aml.md) un modèle de classification d'images.
    * Tutoriel en deux parties : [Préparez les données](tutorial-data-prep.md) et [utilisez le Machine Learning automatisé](tutorial-auto-train-models.md) pour générer automatiquement un modèle de régression.

* En savoir plus sur la [configuration d'un environnement de développement](how-to-configure-environment.md).

* En savoir plus sur le [Kit de développement logiciel (SDK) Azure Machine Learning pour Python](https://aka.ms/aml-sdk).
