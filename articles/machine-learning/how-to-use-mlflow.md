---
title: MLflow Tracking pour les expériences ML
titleSuffix: Azure Machine Learning
description: Configurez MLflow avec Azure Machine Learning pour enregistrer les métriques et les artefacts de modèles de ML, et déployez vos modèles ML en tant que service web.
services: machine-learning
author: shivp950
ms.author: shipatel
ms.service: machine-learning
ms.subservice: core
ms.reviewer: nibaccam
ms.date: 09/08/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 116faae1bc0a93ce2007fcf809a8c96475289036
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90897205"
---
# <a name="track-model-metrics-and-deploy-ml-models-with-mlflow-and-azure-machine-learning-preview"></a>Suivre des métriques de modèle et déployer des modèles de ML avec MLflow et Azure Machine Learning (préversion)

Cet article montre comment activer l’URI de suivi de MLflow et l’API de journalisation, collectivement appelés [MLflow Tracking](https://mlflow.org/docs/latest/quickstart.html#using-the-tracking-api), pour connecter vos expériences de MLflow et Azure Machine Learning. 

Grâce à la prise en charge native d’Azure Machine Learning pour MLflow, vous pouvez :

+ Suivre et journaliser les métriques et les artefacts des expériences dans votre [espace de travail Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/concept-azure-machine-learning-architecture#workspaces). Si vous utilisez déjà MLflow Tracking pour vos expériences, l’espace de travail fournit un emplacement centralisé, sécurisé et évolutif pour stocker les métriques et les modèles d’entraînement.

+ Soumettre des tâches de formation avec MLflow Projects en utilisant la prise en charge du service principal Azure Machine Learning (version préliminaire). Vous pouvez envoyer des tâches en local en utilisant le suivi Azure Machine Learning ou migrer vos exécutions vers le cloud, par exemple, via une [capacité de calcul Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/how-to-create-attach-compute-sdk#amlcompute)de calcul.

+ Suivre et gérer des modèles dans MLflow et un registre de modèles Azure Machine Learning.

+ Déployez vos expériences MLflow en tant que service web Azure Machine Learning. En déployant un service web, vous pouvez appliquer les fonctionnalités de surveillance et de supervision des dérives de données Azure Machine Learning à vos modèles de production. 

[MLflow](https://www.mlflow.org) est une bibliothèque open source permettant de gérer le cycle de vie de vos expériences de Machine Learning. MLFlow Tracking est un composant de MLflow qui journalise et suit vos artefacts de modèle et métriques d’exécution de formation, quel que soit l’environnement de votre expérience : localement sur votre ordinateur, sur une cible de calcul distante, sur une machine virtuelle ou sur un cluster Azure Databricks. 

>[!NOTE]
> En tant que bibliothèque open source, MLflow change fréquemment. Ainsi, les fonctionnalités mises à disposition via l’intégration d’Azure Machine Learning et de MLflow doivent être considérées comme une préversion et non entièrement prises en charge par Microsoft.

Le diagramme suivant montre qu’avec MLflow Tracking, vous suivez les métriques d’exécution d’une expérience et vous stockez les artefacts du modèle dans votre espace de travail Azure Machine Learning.

![diagramme de mlflow avec azure machine learning](./media/how-to-use-mlflow/mlflow-diagram-track.png)

> [!TIP]
> Les informations contenues dans ce document sont principalement destinées aux scientifiques des données et aux développeurs qui veulent superviser le processus d’entraînement du modèle. Si vous êtes un administrateur intéressé par la supervision de l’utilisation de ressources et d’événements d’Azure Machine Learning, comme des quotas, des cycles de formation accomplis ou des déploiements de modèles effectués, voir [Supervision d’Azure Machine Learning](monitor-azure-machine-learning.md).

## <a name="compare-mlflow-and-azure-machine-learning-clients"></a>Comparer les clients MLflow et Azure Machine Learning

 Le tableau ci-dessous récapitule les différents clients pouvant utiliser Azure Machine Learning et leurs fonctionnalités respectives.

 MLflow Tracking offre des fonctionnalités de stockage de journalisation de métriques et d’artefact qui ne sont autrement disponibles que via le [Kit de développement logiciel (SDK) Python Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true).

| Fonctionnalité | Suivi et déploiement MLflow | SDK Python Azure Machine Learning |  Interface CLI Azure Machine Learning | Azure Machine Learning Studio|
|---|---|---|---|---|
| Gérer l'espace de travail |   | ✓ | ✓ | ✓ |
| Utiliser des magasins de données  |   | ✓ | ✓ | |
| Journaliser les métriques      | ✓ | ✓ |   | |
| Charger les artefacts | ✓ | ✓ |   | |
| Afficher les mesures     | ✓ | ✓ | ✓ | ✓ |
| Gérer le calcul   |   | ✓ | ✓ | ✓ |
| Déployer des modèles    | ✓ | ✓ | ✓ | ✓ |
|Surveiller les performances d'un modèle||✓|  |   |
| Détecter une dérive de données |   | ✓ |   | ✓ |

## <a name="prerequisites"></a>Prérequis

* [Installez MLflow.](https://mlflow.org/docs/latest/quickstart.html)
* [Installez le kit SDK Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true) sur votre ordinateur local. Le kit SDK assure la connexion permettant à MLflow d’accéder à votre espace de travail.
* [Créez un espace de travail Azure Machine Learning](how-to-manage-workspace.md).

## <a name="track-local-runs"></a>Suivre les exécutions locales

MLflow Tracking avec Azure Machine Learning vous permet de stocker les métriques et artefacts consignés à partir de vos exécutions locales dans votre espace de travail Azure Machine Learning.

Installez le package `azureml-mlflow` pour utiliser MLflow Tracking avec Azure Machine Learning sur vos expériences exécutées localement dans un Jupyter Notebook ou un éditeur de code.

```shell
pip install azureml-mlflow
```

Importez les classes `mlflow` et [`Workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py&preserve-view=true) pour accéder à l’URI de suivi de MLflow et configurer votre espace de travail.

Dans le code suivant, la méthode `get_mlflow_tracking_uri()` attribue un adresse URI de suivi unique à l’espace de travail, `ws`, et `set_tracking_uri()` pointe le L’URI de suivi de MLflow sur cette adresse.

```Python
import mlflow
from azureml.core import Workspace

ws = Workspace.from_config()

mlflow.set_tracking_uri(ws.get_mlflow_tracking_uri())
```

>[!NOTE]
>L’URI de suivi est valide pendant au maximum une heure. Si vous redémarrez votre script après un certain temps d’inactivité, utilisez l’API get_mlflow_tracking_uri pour obtenir un nouvel URI.

Définissez le nom de l’expérience MLflow avec `set_experiment()` et commencez votre cycle d’apprentissage avec `start_run()`. Utilisez ensuite `log_metric()` à activer l’API de journalisation de MLflow et commencer la journalisation des métriques de votre cycle d’apprentissage.

```Python
experiment_name = 'experiment_with_mlflow'
mlflow.set_experiment(experiment_name)

with mlflow.start_run():
    mlflow.log_metric('alpha', 0.03)
```

## <a name="track-remote-runs"></a>Suivre les exécutions à distance

MLflow Tracking avec Azure Machine Learning vous permet de stocker les métriques et artefacts consignés à partir de vos exécutions distantes dans votre espace de travail Azure Machine Learning.

Les exécutions à distance vous permettent d’effectuer l’apprentissage de vos modèles sur des calculs plus puissants, tels que des machines virtuelles activées par GPU ou des clusters de Capacité de calcul Machine Learning. Pour en savoir plus sur les différentes options de calcul, consultez [Utiliser des cibles de calcul pour l’entraînement des modèles](how-to-set-up-training-targets.md).

Configurez votre environnement de calcul et d’apprentissage avec la classe [`Environment`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py&preserve-view=true). Incluez les packages pip `mlflow` et `azureml-mlflow` dans la section [`CondaDependencies`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py&preserve-view=true) de l’environnement. Construisez ensuite [`ScriptRunConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig?view=azure-ml-py&preserve-view=true) avec votre calcul à distance en tant que cible de calcul.

```Python
from azureml.core.environment import Environment
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core import ScriptRunConfig

exp = Experiment(workspace = 'my_workspace',
                 name='my_experiment')

mlflow_env = Environment(name='mlflow-env')

cd = CondaDependencies.create(pip_packages=['mlflow', 'azureml-mlflow'])

mlflow_env.python.conda_dependencies = cd

src = ScriptRunConfig(source_directory='./my_script_location', script='my_training_script.py')

src.run_config.target = 'my-remote-compute-compute'
src.run_config.environment = mlflow_env
```

Dans votre script d’apprentissage, importez `mlflow` pour utiliser les API de journalisation MLflow et commencez à journaliser vos métriques d’exécution.

```Python
import mlflow

with mlflow.start_run():
    mlflow.log_metric('example', 1.23)
```

Avec cette configuration du cycle de calcul et d’apprentissage, utilisez la méthode `Experiment.submit()` pour soumettre un cycle. Cette méthode définit automatiquement l’URI du suivi MLflow et dirige la journalisation de MLflow vers votre espace de travail.

```Python
run = exp.submit(src)
```

## <a name="train-with-mlflow-projects"></a>Formation avec MLflow Projects

Grâce à [MLflow Projects](https://mlflow.org/docs/latest/projects.html), vous pouvez organiser et décrire votre code pour que d’autres scientifiques de données (ou des outils automatisés) puissent l’exécuter. MLflow Projects avec Azure Machine Learning vous permet de suivre et de gérer vos exécutions de formation dans votre espace de travail. 

Cet exemple montre comment soumettre des projets MLflow en local en utilisant le suivi Azure Machine Learning.

Installez le package `azureml-mlflow` pour utiliser MLflow Tracking avec Azure Machine Learning sur vos expériences exécutées en local. Vos expériences peuvent s’exécuter à l’aide d’un bloc-notes Jupyter ou d’un éditeur de code.

```shell
pip install azureml-mlflow
```

Importez les classes `mlflow` et [`Workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py) pour accéder à l’URI de suivi de MLflow et configurer votre espace de travail.

```Python
import mlflow
from azureml.core import Workspace

ws = Workspace.from_config()

mlflow.set_tracking_uri(ws.get_mlflow_tracking_uri())
```

Définissez le nom de l’expérience MLflow avec `set_experiment()` et commencez votre cycle d’apprentissage avec `start_run()`. Utilisez ensuite `log_metric()` pour activer l’API de journalisation de MLflow et commencer la journalisation des métriques de votre cycle de formation.

```Python
experiment_name = 'experiment-with-mlflow-projects'
mlflow.set_experiment(experiment_name)
```

Créez l’objet de configuration principal pour stocker les informations nécessaires à l’intégration, telles que la cible de calcul et le type d’environnement géré à utiliser.

```python
backend_config = {"USE_CONDA": False}
```
Ajoutez le package `azureml-mlflow` en tant que dépendance PIP à votre fichier de configuration d’environnement afin d’effectuer le suivi des métriques et des artefacts principaux dans votre espace de travail. 

``` shell
name: mlflow-example
channels:
  - defaults
  - anaconda
  - conda-forge
dependencies:
  - python=3.6
  - scikit-learn=0.19.1
  - pip
  - pip:
    - mlflow
    - azureml-mlflow
```
Soumettez l’exécution locale et veillez à définir le paramètre `backend = "azureml" `. Ce paramètre vous permet de soumettre des exécutions en local et bénéficier de la prise en charge supplémentaire du suivi automatique de la sortie, des fichiers journaux, des instantanés et des erreurs imprimées dans votre espace de travail. 

Affichez vos exécutions et métriques dans [Azure Machine Learning Studio](overview-what-is-machine-learning-studio.md). 


```python
local_env_run = mlflow.projects.run(uri=".", 
                                    parameters={"alpha":0.3},
                                    backend = "azureml",
                                    use_conda=False,
                                    backend_config = backend_config, 
                                    )

```

## <a name="track-azure-databricks-runs"></a>Suivre les exécutions Azure Databricks

MLflow Tracking avec Azure Machine Learning vous permet de stocker simultanément les métriques et artefacts consignés à partir de vos exécutions Azure Databricks dans les trois zones suivantes : 

* Espace de travail Azure Machine Learning
* Espace de travail Azure Databricks.
* MLflow

Pour exécuter vos expériences Mlflow avec Azure Databricks, vous devez d'abord créer un [espace de travail Azure Databricks et un cluster](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal). Dans votre cluster, installez la bibliothèque *azureml-mlflow* de PyPi pour vous assurer que votre cluster peut accéder aux fonctions et classes nécessaires.

> [!NOTE]
> Le package `azureml.core` comprend `azureml-mlflow`. Si vous avez déjà installé `azureml.core`, vous pouvez ignorer l’étape d’installation de `azureml-mlflow`. 

À partir de là, importez votre bloc-notes d’expériences, attachez-le à votre cluster Azure Databricks et exécutez votre expérience. 

### <a name="install-libraries"></a>Installation des bibliothèques

Pour installer des bibliothèques sur votre cluster, accédez à l'onglet **Bibliothèques**, puis cliquez sur **Installer**

 ![mlflow avec azure databricks](./media/how-to-use-mlflow/azure-databricks-cluster-libraries.png)

Dans le champ **Package**, tapez azureml-mlflow, puis cliquez sur Installer. Répétez cette étape si nécessaire afin d’installer d'autres packages sur votre cluster pour votre expérience.

 ![Installation de bibliothèque mlflow Azure DB](./media/how-to-use-mlflow/install-libraries.png)

### <a name="set-up-your-notebook-and-workspace"></a>Configurer votre notebook et votre espace de travail

Une fois votre cluster configuré, importez votre notebook d'expériences, ouvrez-le, puis et attachez-y votre cluster.

Le code suivant devrait figurer dans votre notebook d'expériences. Ce code obtient ainsi les détails de votre abonnement Azure pour instancier votre espace de travail. Ce code suppose que vous disposez d’un groupe de ressources existant et d’un espace de travail Azure Machine Learning ; si ce n’est pas le cas, vous pouvez [les créer](how-to-manage-workspace.md). 

```python
import mlflow
import mlflow.azureml
import azureml.mlflow
import azureml.core

from azureml.core import Workspace
from azureml.mlflow import get_portal_url

subscription_id = 'subscription_id'

# Azure Machine Learning resource group NOT the managed resource group
resource_group = 'resource_group_name' 

#Azure Machine Learning workspace name, NOT Azure Databricks workspace
workspace_name = 'workspace_name'  

# Instantiate Azure Machine Learning workspace
ws = Workspace.get(name=workspace_name,
                   subscription_id=subscription_id,
                   resource_group=resource_group)
```

### <a name="connect-your-azure-databricks-and-azure-machine-learning-workspaces"></a>Lier vos espaces de travail Azure Databricks et Azure Machine Learning

Dans le [portail Azure](https://ms.portal.azure.com), vous pouvez lier votre espace de travail Azure Databricks (ADB) à un espace de travail Azure Machine Learning (nouveau ou existant). Pour ce faire, accédez à votre espace de travail ADB et sélectionnez le bouton **Link Azure Machine Learning workspace** (Lier l’espace de travail Azure Machine Learning ) en bas à droite. Le fait de lier vos espaces de travail vous permet de suivre vos données d’expérience dans l’espace de travail Azure Machine Learning. 

### <a name="mlflow-tracking-in-your-workspaces"></a>MLflow Tracking dans vos espaces de travail

Une fois que vous avez instancié votre espace de travail, MLflow Tracking est automatiquement configuré pour être suivi dans tous ces emplacements :

* L’espace de travail Azure Machine Learning lié.
* Votre espace de travail ADB d’origine. 
* MLflow. 

Toutes vos expériences seront consignées dans le service de suivi géré d’Azure Machine Learning.

#### <a name="set-mlflow-tracking-to-only-track-in-your-azure-machine-learning-workspace"></a>Configurer MLflow Tracking pour qu’il effectue le suivi uniquement dans votre espace de travail Azure Machine Learning

Si vous préférez gérer vos expériences suivies dans un emplacement centralisé, vous pouvez définir MLflow Tracking pour effectuer le suivi **uniquement** dans votre espace de travail Azure Machine Learning. 


```python
uri = ws.get_mlflow_tracking_uri()
mlflow.set_tracking_uri(uri)
```

Dans votre script d’apprentissage, importez `mlflow` pour utiliser les API de journalisation MLflow et commencez à journaliser vos métriques d’exécution. L'exemple suivant journalise la métrique epoch loss. 

```python
import mlflow 
mlflow.log_metric('epoch_loss', loss.item()) 
```

## <a name="view-metrics-and-artifacts-in-your-workspace"></a>Afficher les métriques et les artefacts dans votre espace de travail

Les métriques et artefacts provenant de la journalisation de MLflow sont conservés dans votre espace de travail. Pour les voir à tout moment, accédez à votre espace de travail, puis recherchez l’expérience par son nom dans votre espace de travail dans [Azure Machine Learning Studio](https://ml.azure.com).  Ou exécutez le code ci-dessous. 

```python
run.get_metrics()
ws.get_details()
```

## <a name="manage-models"></a>Gérer les modèles 

Enregistrez et suivez vos modèles avec le [registre de modèles Azure Machine Learning](concept-model-management-and-deployment.md#register-package-and-deploy-models-from-anywhere) qui prend en charge le registre de modèles MLflow. Les modèles Azure Machine Learning sont alignés avec le schéma de modèle MLflow, ce qui facilite l’exportation et l’importation de ces modèles dans différents flux de travail. Les métadonnées associées à MLflow, telles que l’ID d’exécution, sont également marquées avec le modèle inscrit pour la traçabilité. Les utilisateurs peuvent soumettre des exécutions de formation, enregistrer, mais aussi déployer des modèles produits à partir de MLflow. 

Si vous souhaitez déployer et inscrire votre modèle prêt pour la production en une seule étape, consultez [déployer et inscrire des modèles MLflow](#deploy-and-register-mlflow-models).

Pour inscrire et afficher un modèle à partir d’une exécution, procédez comme suit :

1. Une fois l’exécution terminée, appelez la méthode `register_model()`.

    ```python
    # the model folder produced from the run is registered. This includes the MLmodel file, model.pkl and the conda.yaml.
    run.register_model(model_name = 'my-model', model_path = 'model')
    ```

1. Affichez le modèle inscrit dans votre espace de travail avec [Azure Machine Learning Studio](overview-what-is-machine-learning-studio.md).

    Dans l’exemple suivant, le modèle inscrit, `my-model` a des métadonnées de MLflow Tracking marquées. 

    ![registered-mlflow-model](./media/how-to-use-mlflow/registered-mlflow-model.png)

1. Sélectionnez l’onglet **Artefacts** pour afficher tous les fichiers de modèle qui s’alignent sur le schéma de modèle MLflow (conda.yaml, MLmodel, model.pkl).

    ![model-schema](./media/how-to-use-mlflow/mlflow-model-schema.png)

1. Sélectionnez MLmodel pour afficher le fichier MLmodel généré par l’exécution.

    ![MLmodel-schema](./media/how-to-use-mlflow/mlmodel-view.png)



## <a name="deploy-and-register-mlflow-models"></a>Déployer et inscrire des modèles MLflow 

Le déploiement de vos expériences MLflow en tant que service web Azure Machine Learning vous permet d’exploiter les capacités de gestion de modèles et de détection des dérives de données Azure Machine Learning et de les appliquer à vos modèles de production.

Pour ce faire, vous devez

1. enregistrer votre modèle
1. Déterminez la configuration de déploiement que vous souhaitez utiliser pour votre scénario.

    1. [Azure Container instance (ACI)](#deploy-to-aci) est un choix approprié pour un déploiement de test rapide.
    1. [Azure Kubernetes Service (AKS)](#deploy-to-aks) est adapté aux déploiements dans des environnements de production évolutifs.

Le diagramme suivant montre qu’avec l’API de déploiement MLflow, vous pouvez déployer vos modèles MLflow existants en tant que service web Azure Machine Learning, en dépit de leurs frameworks (PyTorch, TensorFlow, scikit-learn, ONNX, etc.) et gérer vos modèles de production dans votre espace de travail.

![ déployer des modèles mlflow avec azure machine learning](./media/how-to-use-mlflow/mlflow-diagram-deploy.png)


### <a name="deploy-to-aci"></a>Déployer sur ACI

Configurez votre configuration de déploiement avec la méthode [deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py#&preserve-view=truedeploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-). Vous pouvez également ajouter des balises et des descriptions pour faciliter le suivi de votre service web.

```python
from azureml.core.webservice import AciWebservice, Webservice

# Set the model path to the model folder created by your run
model_path = "model"

# Configure 
aci_config = AciWebservice.deploy_configuration(cpu_cores=1, 
                                                memory_gb=1, 
                                                tags={'method' : 'sklearn'}, 
                                                description='Diabetes model',
                                                location='eastus2')
```

Ensuite, inscrivez et déployez le modèle rapidement, grâce à la méthode [deploy](/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#&preserve-view=truedeploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) du Kit de développement logiciel (SDK) Azure Machine Learning. 

```python
(webservice,model) = mlflow.azureml.deploy( model_uri='runs:/{}/{}'.format(run.id, model_path),
                      workspace=ws,
                      model_name='sklearn-model', 
                      service_name='diabetes-model-1', 
                      deployment_config=aci_config, 
                      tags=None, mlflow_home=None, synchronous=True)

webservice.wait_for_deployment(show_output=True)
```

### <a name="deploy-to-aks"></a>Déployer sur AKS

Pour déployer sur AKS, commencez par créer un cluster AKS. Créez un cluster AKS avec la méthode [ComputeTarget.create()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.computetarget?view=azure-ml-py#&preserve-view=truecreate-workspace--name--provisioning-configuration-). La création d’un cluster peut prendre de 20 à 25 minutes.

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Use the default configuration (can also provide parameters to customize)
prov_config = AksCompute.provisioning_configuration()

aks_name = 'aks-mlflow'

# Create the cluster
aks_target = ComputeTarget.create(workspace=ws, 
                                  name=aks_name, 
                                  provisioning_configuration=prov_config)

aks_target.wait_for_completion(show_output = True)

print(aks_target.provisioning_state)
print(aks_target.provisioning_errors)
```
Configurez votre configuration de déploiement avec la méthode [deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py#&preserve-view=truedeploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-). Vous pouvez également ajouter des balises et des descriptions pour faciliter le suivi de votre service web.

```python
from azureml.core.webservice import Webservice, AksWebservice

# Set the web service configuration (using default here with app insights)
aks_config = AksWebservice.deploy_configuration(enable_app_insights=True, compute_target_name='aks-mlflow')

```

Ensuite, inscrivez et déployez le modèle en une seule étape avec le Kit de développement logiciel (SDK) Azure Machine Learning [deploy()]. Ensuite, inscrivez et déployez le modèle à l’aide de la méthode [deploy](/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#&preserve-view=truedeploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) du Kit de développement logiciel (SDK) Azure Machine Learning. 

```python

# Webservice creation using single command
from azureml.core.webservice import AksWebservice, Webservice

# set the model path 
model_path = "model"

(webservice, model) = mlflow.azureml.deploy( model_uri='runs:/{}/{}'.format(run.id, model_path),
                      workspace=ws,
                      model_name='sklearn-model', 
                      service_name='my-aks', 
                      deployment_config=aks_config, 
                      tags=None, mlflow_home=None, synchronous=True)


webservice.wait_for_deployment()
```

Le déploiement du service peut prendre plusieurs minutes.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous ne prévoyez pas d’utiliser les métriques et artefacts enregistrés dans votre espace de travail, la possibilité de les supprimer individuellement est actuellement indisponible. Au lieu de cela, supprimez le groupe de ressources contenant le compte de stockage et l’espace de travail afin d’éviter des frais supplémentaires :

1. Dans le portail Azure, sélectionnez **Groupes de ressources** tout à gauche.

   ![Supprimer dans le portail Azure](./media/how-to-use-mlflow/delete-resources.png)

1. À partir de la liste, sélectionnez le groupe de ressources créé.

1. Sélectionnez **Supprimer le groupe de ressources**.

1. Entrez le nom du groupe de ressources. Puis sélectionnez **Supprimer**.

## <a name="example-notebooks"></a>Exemples de notebooks

Les [notebooks MLflow avec Azure Machine Learning](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/track-and-monitor-experiments/using-mlflow) illustrent et développent les concepts abordés dans cet article.

## <a name="next-steps"></a>Étapes suivantes

* [Gérez vos modèles](concept-model-management-and-deployment.md).
* Surveillez la [dérive des données](how-to-monitor-data-drift.md) de vos modèles de production.
