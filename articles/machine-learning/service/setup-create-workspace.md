---
title: Créer un espace de travail
titleSuffix: Azure Machine Learning service
description: Utilisez le portail Azure, le Kit de développement, un modèle ou l’interface CLI pour créer votre espace de travail du service Azure Machine Learning. Cet espace de travail fournit un emplacement centralisé pour travailler avec tous les artefacts que vous créez lorsque vous utilisez le service Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: sgilley
ms.author: sgilley
author: sdgilley
ms.date: 04/19/2019
ms.openlocfilehash: cc6c93420e939e90e12b989def491199fd2c6b15
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2019
ms.locfileid: "60006996"
---
# <a name="create-an-azure-machine-learning-service-workspace"></a>Créer un espace de travail du service Azure Machine Learning

Pour utiliser le service Azure Machine Learning, vous devez un [ **espace de travail de service Azure Machine Learning**](concept-azure-machine-learning-architecture.md#workspace).  Cet espace de travail est la ressource de niveau supérieur pour le service et vous fournit un emplacement centralisé pour travailler avec tous les artefacts que vous créez. 

Dans cet article, vous allez apprendre à créer un espace de travail en utilisant l’une des méthodes suivantes : 
* Le [Azure portal](#portal) interface
* Le [Azure Machine Learning SDK pour Python](#sdk)
* Un modèle Azure Resource Manager
* Le [Azure Machine Learning CLI](#cli)

L’espace de travail que vous créez à l’aide de la procédure décrite ici dans peut être utilisé comme condition préalable à d’autres didacticiels et les articles de savoir-faire.

Si vous souhaitez utiliser un script pour le programme d’installation automatisée de machine learning dans un environnement Python local, consultez le [Azure/MachineLearningNotebooks GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning) pour obtenir des instructions.  

Lorsque vous créez un espace de travail les ressources Azure suivantes sont ajoutées automatiquement (si elles sont disponibles au niveau régional) :
 
- [Azure Container Registry](https://azure.microsoft.com/services/container-registry/)
- [Stockage Azure](https://azure.microsoft.com/services/storage/)
- [Azure Application Insights](https://azure.microsoft.com/services/application-insights/) 
- [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)

>[!Note]
>À l’instar des autres services Azure, certains quotas et limites sont associés au Machine Learning. [En savoir plus sur les quotas et comment demander à les relever.](how-to-manage-quotas.md)


## <a name="prerequisites"></a>Conditions préalables
Pour créer un espace de travail, vous avez besoin d’un abonnement Azure. Si vous n’avez pas d’abonnement Azure, créez un compte gratuit avant de commencer. Essayez la [version gratuite ou payante d’Azure Machine Learning service](https://aka.ms/AMLFree) dès aujourd’hui.

## <a name="portal"></a> Portail Azure

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

Quelle que soit la façon dont il a été créé, vous pouvez afficher votre espace de travail dans le [Azure portal](https://portal.azure.com/).  Consultez [afficher un espace de travail](how-to-manage-workspace.md#view) pour plus d’informations.

## <a name="sdk"></a> SDK Python

Créer votre espace de travail à l’aide du Kit de développement Python. Vous devez d’abord installer le Kit de développement.

> [!IMPORTANT]
> Ignorer l’installation du SDK si vous utilisez une Machine virtuelle de science des données Azure ou le Azure Databricks.
> * Le kit SDK Python est préinstallé sur les machines Azure DSVM créées après le 27 septembre 2018. Ignorer l’installation et commencer par [créer un espace de travail avec le SDK](#sdk-create).
> * Dans l’environnement Azure Databricks, suivez à la place les [étapes d’installation pour Databricks](how-to-configure-environment.md#azure-databricks).

>[!NOTE]
> Utilisez ces instructions pour installer et utiliser le SDK à partir de votre ordinateur local. Pour utiliser Jupyter sur une machine virtuelle à distance, configurez un référentiel distant ou bureau X session Terminal Server.

Avant d’installer le Kit de développement logiciel (SDK), nous vous recommandons de créer un environnement Python isolé. Bien que cet article utilise [Miniconda](https://docs.conda.io/en/latest/miniconda.html), vous pouvez également utiliser [Anaconda](https://www.anaconda.com/) complet installé ou un [environnement virtuel Python](https://virtualenv.pypa.io/en/stable/).

Les instructions fournies dans cet article va installer tous les packages que vous devez exécuter les blocs-notes de démarrage rapide et le didacticiel.  D’autres exemples de notebooks peuvent nécessiter l’installation de composants supplémentaires.  Pour plus d’informations sur ces composants, consultez [Installer le kit SDK Azure Machine Learning pour Python](https://docs.microsoft.com/python/api/overview/azure/ml/install).

### <a name="install-miniconda"></a>Installation de Miniconda

[Téléchargez et installez Miniconda](https://docs.conda.io/en/latest/miniconda.html). Sélectionnez la version de Python à installer (3.7). Ne sélectionnez pas la version 2.x de Python.  

### <a name="create-an-isolated-python-environment"></a>Création d’un environnement isolé de Python

1. Ouvrez une fenêtre de ligne de commande, puis créez un environnement conda nommé *myenv* et installez Python 3.6.5. Le kit SDK Azure Machine Learning fonctionne avec Python 3.5.2 ou ultérieur, mais les composants du machine learning automatisé ne sont pas entièrement fonctionnels sur Python 3.7.  La création de l’environnement prend plusieurs minutes, le temps que les composants et les packages soient téléchargés.

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

### <a name='sdk-create'></a> Créer un espace de travail avec le Kit de développement

Créez votre espace de travail dans un notebook Jupyter à l’aide du kit SDK Python.

1. Créez un répertoire et/ou accédez (cd) à celui que vous souhaitez utiliser pour le guide de démarrage rapide et les tutoriels.

1. Pour lancer Jupyter Notebook, entrez la commande suivante :

    ```shell
    jupyter notebook
    ```

1. Dans la fenêtre du navigateur, créez un nouveau bloc-notes avec le noyau `Python 3` par défaut. 

1. Pour afficher la version du Kit de développement logiciel (SDK), entrez puis exécutez le code Python suivant dans une cellule de bloc-notes :

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=import)]

1. Recherchez une valeur pour le paramètre `<azure-subscription-id>` dans la [liste des abonnements dans le Portail Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Utilisez n’importe quel abonnement dans lequel votre rôle est propriétaire ou contributeur. Pour plus d’informations sur les rôles, consultez [gérer l’accès à un espace de travail Azure Machine Learning](how-to-assign-roles.md) article.

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

Enregistrez les détails de votre espace de travail dans un fichier de configuration se trouvant dans le répertoire courant. Ce fichier est appelé *.azureml/config.json*.  

Ce fichier de configuration de l’espace de travail facilite son chargement ultérieur. Vous pouvez le charger avec d’autres ordinateurs portables et les scripts dans le même répertoire ou un sous-répertoire en utilisant le code `ws=Workspace.from_config()` . 

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=writeConfig)]

L’appel d’API `write_config()` crée le fichier de configuration dans le répertoire actif. Le fichier *config.json* contient le code suivant :

```json
{
    "subscription_id": "<azure-subscription-id>",
    "resource_group": "myresourcegroup",
    "workspace_name": "myworkspace"
}
```

> [!TIP]
> Pour utiliser votre espace de travail dans les scripts Python ou les blocs-notes Jupyter situés dans d’autres répertoires, copiez ce fichier à ce répertoire. Le fichier peut être dans le même répertoire, un sous-répertoire nommé *.azureml*, ou dans un répertoire parent.

## <a name="resource-manager-template"></a>Modèle resource manager

Pour créer un espace de travail avec un modèle, consultez [créer un espace de travail du service Azure Machine Learning à l’aide d’un modèle](how-to-create-workspace-template.md)

<a name="cli"></a>
## <a name="command-line-interface"></a>Interface de ligne de commande

Pour créer un espace de travail avec l’interface CLI, consultez [utiliser l’extension CLI pour le service Azure Machine Learning](reference-azure-machine-learning-cli.md).

## <a name="clean-up-resources"></a>Supprimer des ressources 

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Étapes suivantes

* Quelle que soit la façon dont il a été créé, vous pouvez afficher votre espace de travail dans le [Azure portal](https://portal.azure.com/).  Consultez [afficher un espace de travail](how-to-manage-workspace.md#view) pour plus d’informations.

* Tester votre espace de travail avec ces Démarrages rapides et des didacticiels.

    * Démarrage rapide : [Exécuter le bloc-notes Jupyter dans le cloud](quickstart-run-cloud-notebook.md).
    * Démarrage rapide : [Exécutez le bloc-notes Jupyter sur votre propre serveur](quickstart-run-local-notebook.md).
    * Didacticiel de deux parties : [Train](tutorial-train-models-with-aml.md) et [déployer](tutorial-deploy-models-with-aml.md) un mode de classification d’image.
    * Didacticiel de deux parties : [Préparer des données](tutorial-data-prep.md) et [utiliser l’apprentissage automatique](tutorial-auto-train-models.md) pour générer un modèle de régression.

* En savoir plus sur comment [configurer un environnement de développement](how-to-configure-environment.md).

* En savoir plus sur la [Azure Machine Learning SDK pour Python](https://aka.ms/aml-sdk).
