---
title: 'Démarrage rapide : Utilisation du Kit de développement logiciel (SDK) Python afin de créer un espace de travail pour le service de Machine Learning - Azure Machine Learning'
description: Prise en main d’Azure Machine Learning. Installez le Kit de développement logiciel Python et utilisez-le pour créer un espace de travail. Dans le cloud, cet espace de travail est le socle que vous utilisez pour expérimenter, entraîner et déployer des modèles Machine Learning avec Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: quickstart
ms.reviewer: sgilley
author: hning86
ms.author: haining
ms.date: 12/04/2018
ms.openlocfilehash: f16d39238b46a811f34fdfe291d6c09bee662b9a
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2018
ms.locfileid: "52870064"
---
# <a name="quickstart-use-python-to-get-started-with-azure-machine-learning"></a>Démarrage rapide : Utilisation de Python pour démarrer avec Azure Machine Learning

Dans ce guide de démarrage rapide, vous utilisez le Kit de développement logiciel (SDK) Python d’Azure Machine Learning pour créer et utiliser un [espace de travail](concept-azure-machine-learning-architecture.md) destiné au service Machine Learning. Dans le cloud, cet espace de travail est le socle que vous utilisez pour expérimenter, entraîner et déployer des modèles Machine Learning avec Machine Learning. Dans ce démarrage rapide, vous commencez par configurer votre propre environnement Python et le serveur Jupyter Notebook. Pour exécuter sans installation, consultez [Démarrage rapide : Utiliser le Portail Azure pour bien démarrer avec Azure Machine Learning](quickstart-get-started.md).

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2G9N6]

Dans ce didacticiel, vous allez installer le Kit de développement logiciel (SDK) Python et :

* Créer un espace de travail dans votre abonnement Azure.
* Créer un fichier de configuration pour cet espace de travail qui servira plus tard dans les autres blocs-notes et scripts.
* Écrire un code qui enregistre les valeurs dans l’espace de travail.
* Afficher les valeurs journalisées dans votre espace de travail.

Dans ce guide de démarrage rapide, vous créez un espace de travail et un fichier de configuration. Vous pouvez les utiliser comme prérequis pour d’autres didacticiels sur le Machine Learning et articles de procédures. À l’instar des autres services Azure, des limites et des quotas sont associés au Machine Learning. [En savoir plus sur les quotas et comment demander à les relever.](how-to-manage-quotas.md)

Les ressources Azure suivantes sont automatiquement ajoutées à votre espace de travail lorsqu’elles sont disponibles au niveau régional :
 
- [Azure Container Registry](https://azure.microsoft.com/services/container-registry/)
- [Stockage Azure](https://azure.microsoft.com/services/storage/)
- [Azure Application Insights](https://azure.microsoft.com/services/application-insights/) 
- [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)

>[!NOTE]
> Le code présenté dans cet article a été testé avec le kit SDK Azure Machine Learning version 1.0.2 


Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://aka.ms/AMLfree) avant de commencer.


## <a name="install-the-sdk"></a>Installer le Kit de développement logiciel (SDK)

*Ignorez cette section si vous utilisez une machine virtuelle de science des données créée après le 27 septembre 2018.* Ces machines virtuelles de science des données sont fournies avec le Kit de développement logiciel (SDK) Python préinstallé.

Avant d’installer le Kit de développement logiciel (SDK), nous vous recommandons de créer un environnement Python isolé. Bien que ce démarrage rapide utilise [Miniconda](https://conda.io/docs/user-guide/install/index.html), vous pouvez également utiliser [Anaconda](https://www.anaconda.com/) complet installé ou un [environnement virtuel Python](https://virtualenv.pypa.io/en/stable/).

### <a name="install-miniconda"></a>Installation de Miniconda


[Téléchargez](https://conda.io/miniconda.html) et installez Miniconda. Sélectionnez la version 3.7 de Python ou une version ultérieure. Ne sélectionnez pas la version 2.x de Python.

### <a name="create-an-isolated-python-environment"></a>Création d’un environnement isolé de Python 

Ouvrez une fenêtre de ligne de commande. Créez ensuite un nouvel environnement conda nommé `myenv` avec Python 3.6.

```shell
conda create -n myenv -y Python=3.6
```

Active l’environnement.

  ```shell
  conda activate myenv
  ```

### <a name="install-the-sdk"></a>Installer le Kit de développement logiciel (SDK)

Dans l’environnement conda activé, installez le Kit de développement logiciel (SDK). Ce code installe les composants de base du Kit de développement logiciel (SDK) pour le Machine Learning. Il installe également un serveur Jupyter Notebook dans l’environnement conda. L’installation prend quelques minutes, en fonction de la configuration de votre ordinateur.

```sh
# install Jupyter
conda install nb_conda

# install the base SDK and Jupyter Notebook
pip install azureml-sdk[notebooks]

```

Vous pouvez également utiliser d’autres mots clés pour installer des composants supplémentaires du kit SDK.

```sh
# install the base SDK and auto ml components
pip install azureml-sdk[automl]

# install the base SDK and model explainability component
pip install azureml-sdk[explain]

# install the base SDK and experimental components
pip install azureml-sdk[contrib]
```

Utilisez plutôt cette installation dans un environnement Databricks.

```
# install the base SDK and automl components in Azure Databricks environment
# read more at: https://github.com/Azure/MachineLearningNotebooks/tree/master/databricks
pip install azureml-sdk[databricks]
```


## <a name="create-a-workspace"></a>Créer un espace de travail

Pour lancer Jupyter Notebook, entrez la commande suivante.
```shell
jupyter notebook
```

Dans la fenêtre du navigateur, créez un nouveau bloc-notes avec le noyau `Python 3` par défaut. 

Pour afficher la version du Kit de développement logiciel (SDK), entrez le code Python suivant dans une cellule de bloc-notes, puis exécutez-le.

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=import)]

Créez un nouveau groupe de ressources Azure et un nouvel espace de travail.

Recherchez une valeur pour `<azure-subscription-id>` dans la [liste des abonnements dans le Portail Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Utilisez n’importe quel abonnement dans lequel votre rôle est propriétaire ou contributeur.

```python
from azureml.core import Workspace
ws = Workspace.create(name='myworkspace',
                      subscription_id='<azure-subscription-id>',    
                      resource_group='myresourcegroup',
                      create_resource_group=True,
                      location='eastus2' # or other supported Azure region  
                     )
```

Il se peut qu’une nouvelle fenêtre de navigateur s’ouvre à l’exécution du code précédent pour que vous puissiez vous connecter à votre compte Azure. Une fois la connexion effectuée, le jeton d’authentification est mis en cache localement.

Pour afficher les détails de l’espace de travail, notamment le stockage associé, le registre des conteneurs et le coffre de clés, entrez le code suivant.

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=getDetails)]


## <a name="write-a-configuration-file"></a>Création d’un fichier de configuration

Enregistrez les détails de votre espace de travail dans un fichier de configuration se trouvant dans le répertoire courant. Ce fichier s’appelle « aml_config\config.json ».  

Ce fichier de configuration de l’espace de travail facilite son chargement ultérieur. Vous pouvez le charger avec d’autres blocs-notes et scripts dans le même répertoire ou dans un sous répertoire. 

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=writeConfig)]


L’appel d’API `write_config()` crée le fichier de configuration dans le répertoire courant. Le fichier `config.json` contient le script suivant.

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
Une fois l’exécution terminée, vous pouvez afficher l’essai sur le Portail Azure. Utilisez le code suivant pour imprimer l’URL des résultats de la dernière exécution.

```python
print(run.get_portal_url())
```

Utilisez le lien pour afficher les valeurs enregistrées dans le Portail Azure sur votre navigateur.

![Valeurs enregistrées dans le portail](./media/quickstart-create-workspace-with-python/logged-values.png)

## <a name="clean-up-resources"></a>Supprimer des ressources 
>[!IMPORTANT]
>Les ressources que vous avez créées peuvent être utilisées comme conditions préalables pour d’autres didacticiels de Machine Learning et des articles de procédure.

Si vous n’avez pas l’intention d’utiliser les ressources créées ici, supprimez-les pour éviter des frais.

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=delete)]


## <a name="next-steps"></a>Étapes suivantes

Vous avez créé les ressources dont vous avez besoin pour expérimenter des modèles et les déployer. Vous avez également exécuté du code dans un bloc-notes. Enfin, vous avez exploré l’historique des exécutions de ce code dans votre espace de travail dans le cloud.

Vous avez besoin de quelques packages supplémentaires dans votre environnement pour vous en servir avec les didacticiels de Machine Learning.

1. Dans votre navigateur, fermez votre bloc-notes.
1. Dans la fenêtre de ligne de commande, utilisez `Ctrl`+`C` pour arrêter le serveur de bloc-notes.
1. Installez les packages supplémentaires.

    ```shell
    conda install -y cython matplotlib scikit-learn pandas numpy
    pip install azureml-sdk[automl]
    ```


Après avoir installé ces packages, suivez les didacticiels pour entraîner et déployer un modèle. 

> [!div class="nextstepaction"]
> [Tutoriel : entraîner un modèle de classification d’images](tutorial-train-models-with-aml.md)

Vous pouvez aussi consulter des [exemples plus avancés sur GitHub](https://aka.ms/aml-notebooks).
