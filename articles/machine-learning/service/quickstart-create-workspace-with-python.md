---
title: 'Démarrage rapide : Bien démarrer dans Python'
titleSuffix: Azure Machine Learning service
description: Bien démarrer avec le service Azure Machine Learning dans Python. Utilisez le kit SDK Python pour créer un espace de travail, qui est le socle dans le cloud que vous utilisez pour expérimenter, effectuer l’apprentissage et déployer des modèles Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: quickstart
ms.reviewer: sgilley
author: hning86
ms.author: haining
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: da84d6361d80db8aea797827ed3d7bc612e2eda3
ms.sourcegitcommit: da69285e86d23c471838b5242d4bdca512e73853
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/03/2019
ms.locfileid: "53999048"
---
# <a name="quickstart-use-the-python-sdk-to-get-started-with-azure-machine-learning"></a>Démarrage rapide : Utilisation du kit SDK Python pour démarrer avec Azure Machine Learning

Dans cet article, vous allez utiliser le Kit de développement logiciel (SDK) Python d’Azure Machine Learning pour créer et utiliser un [espace de travail](concept-azure-machine-learning-architecture.md) destiné à Azure Machine Learning service. Dans le cloud, l’espace de travail est le socle que vous utilisez pour expérimenter, effectuer l’apprentissage et déployer des modèles Machine Learning avec Machine Learning. 

Commencez par configurer votre propre environnement Python et votre serveur Jupyter Notebook. Pour l’exécuter sans installation, consultez le [guide de démarrage rapide : Utiliser le Portail Azure pour prendre en main Azure Machine Learning](quickstart-get-started.md).

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2G9N6]

Dans cet article, vous découvrirez comment :
* Installer le Kit de développement logiciel (SDK) Python.
* Créer un espace de travail dans votre abonnement Azure.
* Créer un fichier de configuration pour cet espace de travail qui servira plus tard dans les autres blocs-notes et scripts.
* Écrire un code qui enregistre les valeurs dans l’espace de travail.
* Afficher les valeurs journalisées dans votre espace de travail.

Vous devez créer un espace de travail et un fichier de configuration qui serviront de prérequis à d’autres didacticiels et guides pratiques relatifs à Machine Learning. À l’instar des autres services Azure, certains quotas et limites sont associés au Machine Learning. [En savoir plus sur les quotas et comment demander à les relever.](how-to-manage-quotas.md)

Les ressources Azure suivantes sont automatiquement ajoutées à votre espace de travail lorsqu’elles sont disponibles au niveau régional :
 
- [Azure Container Registry](https://azure.microsoft.com/services/container-registry/)
- [Stockage Azure](https://azure.microsoft.com/services/storage/)
- [Azure Application Insights](https://azure.microsoft.com/services/application-insights/) 
- [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)

Si vous n’avez pas d’abonnement Azure, créez un compte gratuit avant de commencer. Essayez la [version gratuite ou payante de Azure Machine Learning service](http://aka.ms/AMLFree) dès aujourd’hui.

## <a name="install-the-sdk"></a>Installer le Kit de développement logiciel (SDK)

> [!IMPORTANT]
> Ignorez cette section si vous utilisez une machine virtuelle de science des données créée après le 27 septembre 2018.
> Après cette date, les machines virtuelles de science des données sont fournies avec le Kit de développement logiciel (SDK) Python préinstallé.

Le code présenté dans cet article nécessite le kit SDK Azure Machine Learning version 1.0.2 ou ultérieure.

Avant d’installer le Kit de développement logiciel (SDK), nous vous recommandons de créer un environnement Python isolé. Bien que cet article utilise [Miniconda](https://conda.io/docs/user-guide/install/index.html), vous pouvez également utiliser [Anaconda](https://www.anaconda.com/) complet installé ou un [environnement virtuel Python](https://virtualenv.pypa.io/en/stable/).

### <a name="install-miniconda"></a>Installation de Miniconda

[Téléchargez et installez Miniconda](https://conda.io/miniconda.html). Sélectionnez Python 3.7 ou une version ultérieure. Ne choisissez pas la version 2.x de Python.

### <a name="create-an-isolated-python-environment"></a>Création d’un environnement isolé de Python 

1. Ouvrez une fenêtre en ligne de commande et créez un nouvel environnement conda nommé *myenv* avec Python 3.6.

    ```shell
    conda create -n myenv -y Python=3.6
    ```

1. Active l’environnement.

    ```shell
    conda activate myenv
    ```

### <a name="install-the-sdk"></a>Installer le Kit de développement logiciel (SDK)

Dans l’environnement conda activé, installez le Kit de développement logiciel (SDK). Ce code installe les composants de base du Kit de développement logiciel (SDK) pour le Machine Learning. Il installe également un serveur Jupyter Notebook dans l’environnement conda. L’installation prend quelques minutes, selon la configuration de votre ordinateur.

```sh
# Install Jupyter
conda install nb_conda

# Install the base SDK and Jupyter Notebook
pip install azureml-sdk[notebooks]
```

Vous pouvez utiliser des mots clés supplémentaires pour installer d’autres composants du SDK :

```sh
# Install the base SDK and auto ml components
pip install azureml-sdk[automl]

# Install the base SDK and the model explainability component
pip install azureml-sdk[explain]

# Install the base SDK and experimental components
pip install azureml-sdk[contrib]
```

Dans l’environnement Azure Databricks, utilisez plutôt la commande d’installation suivante :

```
# Install the base SDK and automl components in the Azure Databricks environment.
# For more information, see https://github.com/Azure/MachineLearningNotebooks/tree/master/databricks.
pip install azureml-sdk[databricks]
```


## <a name="create-a-workspace"></a>Créer un espace de travail

1. Pour lancer Jupyter Notebook, entrez la commande suivante :

    ```shell
    jupyter notebook
    ```

1. Dans la fenêtre du navigateur, créez un nouveau bloc-notes avec le noyau `Python 3` par défaut. 

1. Pour afficher la version du Kit de développement logiciel (SDK), entrez puis exécutez le code Python suivant dans une cellule de bloc-notes :

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=import)]

1. Recherchez une valeur pour le paramètre `<azure-subscription-id>` dans la [liste des abonnements dans le Portail Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Utilisez n’importe quel abonnement dans lequel votre rôle est propriétaire ou contributeur.

   ```python
   from azureml.core import Workspace
   ws = Workspace.create(name='myworkspace',
                         subscription_id='<azure-subscription-id>', 
                         resource_group='myresourcegroup',
                         create_resource_group=True,
                         location='eastus2' # Or other supported Azure region   
                        )
   ```

   Lorsque vous exécutez le code, vous pouvez être invité à vous connecter à votre compte Azure. Une fois la connexion effectuée, le jeton d’authentification est mis en cache localement.

1. Pour afficher les détails de l’espace de travail, notamment le stockage associé, le registre des conteneurs et le coffre de clés, entrez le code suivant :

    [!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=getDetails)]


## <a name="write-a-configuration-file"></a>Création d’un fichier de configuration

Enregistrez les détails de votre espace de travail dans un fichier de configuration se trouvant dans le répertoire courant. Ce fichier s’appelle *aml_config\config.json*.  

Ce fichier de configuration de l’espace de travail facilite son chargement ultérieur. Vous pouvez le charger avec d’autres blocs-notes et scripts dans le même répertoire ou dans un sous-répertoire. 

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=writeConfig)]

L’appel d’API `write_config()` crée le fichier de configuration dans le répertoire courant. Le fichier *config.json* contient le script suivant :

```json
{
    "subscription_id": "<azure-subscription-id>",
    "resource_group": "myresourcegroup",
    "workspace_name": "myworkspace"
}
```

## <a name="use-the-workspace"></a>Utiliser l'espace de travail

Créez un code qui se sert des API de base du Kit de développement logiciel (SDK) pour suivre les exécutions des essais.

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=useWs)]

## <a name="view-logged-results"></a>Afficher les résultats enregistrés
Une fois l’exécution terminée, vous pouvez afficher l’essai sur le Portail Azure. Pour imprimer une URL permettant d’accéder aux résultats de la dernière exécution, utilisez le code suivant :

```python
print(run.get_portal_url())
```

Utilisez le lien pour afficher les valeurs enregistrées dans le Portail Azure sur votre navigateur.

![Valeurs enregistrées dans le Portail Azure](./media/quickstart-create-workspace-with-python/logged-values.png)

## <a name="clean-up-resources"></a>Supprimer des ressources 
>[!IMPORTANT]
>Vous pouvez utiliser les ressources que vous avez créées ici comme conditions préalables à l’utilisation d’autres didacticiels Machine Learning et articles de procédure.

Si vous ne souhaitez pas utiliser les ressources que vous avez créées dans cet article, supprimez-les pour éviter des frais inutiles.

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=delete)]

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez créé les ressources dont vous avez besoin pour expérimenter des modèles et les déployer. Vous avez exécuté du code dans un bloc-notes et exploré l’historique des exécutions de ce code dans votre espace de travail dans le cloud.

Pour utiliser le code avec les didacticiels dédiés au Machine Learning, vous avez besoin de quelques packages supplémentaires dans votre environnement.

1. Dans votre navigateur, fermez votre bloc-notes.
1. Dans la fenêtre de ligne de commande, sélectionnez Ctrl+C pour arrêter le serveur Jupyter Notebook.
1. Installez les packages supplémentaires.

    ```shell
    conda install -y cython matplotlib scikit-learn pandas numpy
    pip install azureml-sdk[automl]
    ```

Après avoir installé ces packages, suivez les didacticiels pour entraîner et déployer un modèle. 

> [!div class="nextstepaction"]
> [Tutoriel : Entraîner un modèle de classification d’images](tutorial-train-models-with-aml.md)

Vous pouvez aussi consulter des [exemples plus avancés sur GitHub](https://aka.ms/aml-notebooks).
