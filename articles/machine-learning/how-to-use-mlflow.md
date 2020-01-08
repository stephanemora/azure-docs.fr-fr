---
title: MLflow Tracking pour les expériences ML
titleSuffix: Azure Machine Learning
description: Configurez MLflow avec Azure Machine Learning pour consigner les métriques et les artefacts, et déployer des modèles à partir de Databricks, de votre environnement local ou de l’environnement de machines virtuelles.
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: nibaccam
ms.topic: conceptual
ms.date: 09/23/2019
ms.custom: seodec18
ms.openlocfilehash: 3af8f0768183c07bd656bbb50893985057e9e861
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/28/2019
ms.locfileid: "75534057"
---
# <a name="track-metrics-and-deploy-models-with-mlflow-and-azure-machine-learning-preview"></a>Suivre des métriques et déployer des modèles avec MLflow et Azure Machine Learning (préversion)
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Cet article montre comment activer l'URI de suivi MLflow et l'API de journalisation, collectivement appelés [MLflow Tracking](https://mlflow.org/docs/latest/quickstart.html#using-the-tracking-api), avec Azure Machine Learning. Cela vous permet d’effectuer les opérations suivantes :

+ Suivez et journalisez les métriques et les artefacts des expériences dans votre [espace de travail Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/concept-azure-machine-learning-architecture#workspaces). Si vous utilisez déjà MLflow Tracking pour vos expériences, l’espace de travail fournit un emplacement centralisé, sécurisé et scalable pour stocker les métriques et les modèles d’entraînement.

+ Déployez vos expériences MLflow en tant que service web Azure Machine Learning. En déployant un service web, vous pouvez appliquer les fonctionnalités de surveillance et de supervision des dérives de données Azure Machine Learning à vos modèles de production. 

[MLflow](https://www.mlflow.org) est une bibliothèque open source permettant de gérer le cycle de vie de vos expériences de Machine Learning. MLFlow Tracking est un composant de MLflow qui journalise et fait le suivi vos métriques et de vos artefacts de modèle des exécutions d’entraînement, quel que soit l’environnement de votre expérience : sur une cible de calcul distante, sur une machine virtuelle, localement sur votre ordinateur ou sur un cluster Azure Databricks.

Le diagramme suivant montre qu’avec MLflow Tracking, vous suivez les métriques d’exécution d’une expérience et vous stockez les artefacts du modèle dans votre espace de travail Azure Machine Learning.

![diagramme de mlflow avec azure machine learning](./media/how-to-use-mlflow/mlflow-diagram-track.png)

> [!TIP]
> Les informations contenues dans ce document sont principalement destinées aux scientifiques des données et aux développeurs qui veulent superviser le processus d’entraînement du modèle. Si vous êtes un administrateur intéressé par la supervision de l’utilisation des ressources et des événements d’Azure Machine Learning, comme les quotas, les exécutions d’entraînement réalisées ou les déploiements de modèles terminés, consultez [Supervision d’Azure Machine Learning](monitor-azure-machine-learning.md).

## <a name="compare-mlflow-and-azure-machine-learning-clients"></a>Comparer les clients MLflow et Azure Machine Learning

 Le tableau ci-dessous récapitule les différents clients pouvant utiliser Azure Machine Learning et leurs fonctionnalités respectives.

 MLflow Tracking offre des fonctionnalités de stockage de journalisation de métriques et d’artefact qui ne sont autrement disponibles que via le [Kit de développement logiciel (SDK) Python Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).


| | Suivi et déploiement MLflow | SDK Python Azure Machine Learning |  Interface CLI Azure Machine Learning | Azure Machine Learning Studio|
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

## <a name="prerequisites"></a>Conditions préalables requises

* [Installez MLflow.](https://mlflow.org/docs/latest/quickstart.html)
* [Installez le kit SDK Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) sur votre ordinateur local. Le kit SDK assure la connexion permettant à MLflow d’accéder à votre espace de travail.
* [Créez un espace de travail Azure Machine Learning](how-to-manage-workspace.md).

## <a name="track-local-runs"></a>Suivre les exécutions locales

MLflow Tracking avec Azure Machine Learning vous permet de stocker les métriques et artefacts consignés à partir de vos exécutions locales dans votre espace de travail Azure Machine Learning.

Installez le package `azureml-contrib-run` pour utiliser MLflow Tracking avec Azure Machine Learning sur vos expériences exécutées localement dans un Jupyter Notebook ou un éditeur de code.

```shell
pip install azureml-mlflow
```

>[!NOTE]
>L’espace de noms azureml.contrib change fréquemment car nous travaillons à améliorer le service. Par conséquent, tout ce qui est compris dans cet espace de noms doit être considéré comme une préversion et n’est pas entièrement pris en charge par Microsoft.

Importez les classes `mlflow` et [`Workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py) pour accéder à l’URI de suivi de MLflow et configurer votre espace de travail.

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

Les exécutions à distance vous permettent d’effectuer l’apprentissage de vos modèles sur des calculs plus puissants, tels que des machines virtuelles activées par GPU ou des clusters de Capacité de calcul Machine Learning. Pour en savoir plus sur les différentes options de calcul, voir [Configurer des cibles de calcul pour l’entraînement des modèles](how-to-set-up-training-targets.md).

Configurez votre environnement de calcul et d’apprentissage avec la classe [`Environment`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py). Incluez les packages pip `mlflow` et `azure-contrib-run` dans la section [`CondaDependencies`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py) de l’environnement. Construisez ensuite [`ScriptRunConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig?view=azure-ml-py) avec votre calcul à distance en tant que cible de calcul.

```Python
from azureml.core.environment import Environment
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core import ScriptRunConfig

exp = Experiment(workspace = 'my_workspace',
                 name='my_experiment')

mlflow_env = Environment(name='mlflow-env')

cd = CondaDependencies.create(pip_packages=['mlflow', 'azureml-contrib-run'])

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

Avec cette configuration du cycle de calcul et d’apprentissage, utilisez la méthode `Experiment.submit('train.py')` pour soumettre un cycle. Cette méthode définit automatiquement l’URI du suivi MLflow et dirige la journalisation de MLflow vers votre espace de travail.

```Python
run = exp.submit(src)
```

## <a name="track-azure-databricks-runs"></a>Suivre les exécutions Azure Databricks

MLflow Tracking avec Azure Machine Learning vous permet de stocker les métriques et artefacts consignés à partir de vos exécutions Databricks dans votre espace de travail Azure Machine Learning.

Pour exécuter vos expériences MLflow avec Azure Databricks, vous devez d’abord créer un [espace de travail et un cluster Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal).

Dans votre cluster, veillez à installer la bibliothèque *azureml-mlflow* de PyPi pour vous assurer que votre cluster peut accéder aux fonctions et classes nécessaires.
Maintenant, importez votre notebook d'expérience, attachez-le à votre cluster et exécutez votre expérience. 

### <a name="install-libraries"></a>Installation des bibliothèques

Pour installer des bibliothèques sur votre cluster, accédez à l'onglet **Bibliothèques**, puis cliquez sur **Installer**

 ![diagramme de mlflow avec azure machine learning](./media/how-to-use-mlflow/azure-databricks-cluster-libraries.png)

Dans le champ **Package**, tapez azureml-mlflow, puis cliquez sur Installer. Répétez cette étape si nécessaire afin d’installer d'autres packages sur votre cluster pour votre expérience.

 ![diagramme de mlflow avec azure machine learning](./media/how-to-use-mlflow/install-libraries.png)

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

#### <a name="connect-your-azure-databricks-and-azure-machine-learning-workspaces"></a>Lier vos espaces de travail Azure Databricks et Azure Machine Learning

Dans le [portail Azure](https://ms.portal.azure.com), vous pouvez lier votre espace de travail Azure Databricks (ADB) à un espace de travail Azure Machine Learning (nouveau ou existant). Pour ce faire, accédez à votre espace de travail ADB et sélectionnez le bouton **Link Azure Machine Learning workspace** (Lier l’espace de travail Azure Machine Learning ) en bas à droite. Le fait de lier vos espaces de travail vous permet de suivre vos données d’expérience dans l’espace de travail Azure Machine Learning. 

### <a name="link-mlflow-tracking-to-your-workspace"></a>Lier le suivi MLflow à votre espace de travail

Après avoir instancié votre espace de travail, définissez l'URI de MLflow Tracking. Vous reliez ainsi MLflow Tracking à l'espace de travail Azure Machine Learning. Après la liaison, toutes vos expériences seront consignées dans le service de suivi managé d’Azure Machine Learning.

#### <a name="directly-set-mlflow-tracking-in-your-notebook"></a>Configurer directement le suivi MLflow dans votre notebook

```python
uri = ws.get_mlflow_tracking_uri()
mlflow.set_tracking_uri(uri)
```

Dans votre script d’apprentissage, importez mlflow pour utiliser les API de journalisation MLflow et commencez à journaliser vos métriques d’exécution. L'exemple suivant journalise la métrique epoch loss. 

```python
import mlflow 
mlflow.log_metric('epoch_loss', loss.item()) 
```

#### <a name="automate-setting-mlflow-tracking"></a>Automatiser la configuration du suivi MLflow

Au lieu de définir manuellement l’URI de suivi dans chaque session suivante du notebook d’expérience sur vos clusters, faites-le automatiquement à l’aide de ce [script d’initialisation du cluster de suivi Azure Machine Learning](https://github.com/Azure/MachineLearningNotebooks/blob/3ce779063b000e0670bdd1acc6bc3a4ee707ec13/how-to-use-azureml/azure-databricks/linking/README.md).

Une fois cette configuration effectuée, vous pouvez voir vos données de suivi MLflow dans l’API REST d’Azure Machine Learning et sur tous les clients, ainsi que dans Azure Databricks via l’interface utilisateur MLflow ou en utilisant le client MLflow.

## <a name="view-metrics-and-artifacts-in-your-workspace"></a>Afficher les métriques et les artefacts dans votre espace de travail

Les métriques et artefacts provenant de la journalisation de MLflow sont conservés dans votre espace de travail. Pour les voir à tout moment, accédez à votre espace de travail, puis recherchez l’expérience par son nom dans votre espace de travail dans [Azure Machine Learning Studio](https://ml.azure.com).  Ou exécutez le code ci-dessous. 

```python
run.get_metrics()
ws.get_details()
```

## <a name="deploy-mlflow-models-as-a-web-service"></a>Déployer des modèles MLflow en tant que service web

Le déploiement de vos expériences MLflow en tant que service web Azure Machine Learning vous permet d'exploiter les capacités de gestion de modèles et de détection des dérives de données Azure Machine Learning et de les appliquer à vos modèles de production.

Le diagramme suivant montre qu’avec l’API de déploiement MLflow, vous pouvez déployer vos modèles MLflow existants en tant que service web Azure Machine Learning, en dépit de leurs frameworks (PyTorch, TensorFlow, scikit-learn, ONNX, etc.) et gérer vos modèles de production dans votre espace de travail.

![diagramme de mlflow avec azure machine learning](./media/how-to-use-mlflow/mlflow-diagram-deploy.png)

### <a name="log-your-model"></a>Enregistrer votre modèle

Avant de pouvoir déployer votre modèle, vérifiez qu’il est enregistré afin de pouvoir le référencer et définir son chemin en vue du déploiement. Votre script d’apprentissage devrait contenir un code similaire à la méthode suivante [mlflow.sklearn.log_model()](https://www.mlflow.org/docs/latest/python_api/mlflow.sklearn.html), qui enregistre votre modèle dans le répertoire de sortie spécifié. 

```python
# change sklearn to pytorch, tensorflow, etc. based on your experiment's framework 
import mlflow.sklearn

# Save the model to the outputs directory for capture
mlflow.sklearn.log_model(regression_model, model_save_path)
```
>[!NOTE]
> Incluez le paramètre `conda_env` pour passer une représentation de dictionnaire des dépendances et de l'environnement dans lequel ce modèle doit être exécuté.

### <a name="retrieve-model-from-previous-run"></a>Récupérer le modèle d’une exécution précédente

Pour récupérer l’exécution, vous avez besoin de l’ID d’exécution et du chemin dans l’historique des exécutions où le modèle a été enregistré. 

```python
# gets the list of runs for your experiment as an array
experiment_name = 'experiment-with-mlflow'
exp = ws.experiments[experiment_name]
runs = list(exp.get_runs())

# get the run ID and the path in run history
runid = runs[0].id
model_save_path = 'model'
```

### <a name="create-docker-image"></a>Créer une image Docker

La fonction `mlflow.azureml.build_image()` crée une image Docker à partir du modèle enregistré afin qu'elle soit compatible avec l’infrastructure. Elle crée automatiquement le code wrapper d'inférence spécifique à l’infrastructure et spécifie pour vous les dépendances de package. Spécifiez le chemin du modèle, votre espace de travail, l'ID d'exécution et d'autres paramètres.

Le code suivant génère une image Docker en utilisant *runs:/<run.id>/model* comme chemin de l’URI du modèle (model_uri) pour une expérience Scikit-learn.

```python
import mlflow.azureml

azure_image, azure_model = mlflow.azureml.build_image(model_uri='runs:/{}/{}'.format(runid, model_save_path),
                                                      workspace=ws,
                                                      model_name='sklearn-model',
                                                      image_name='sklearn-image',
                                                      synchronous=True)
```
La création de l'image Docker peut prendre plusieurs minutes. 

### <a name="deploy-the-docker-image"></a>Déployer l’image Docker 

Une fois l'image créée, utilisez le SDK Azure Machine Learning pour déployer l'image en tant que service web.

Spécifiez tout d’abord la configuration du déploiement. Azure Container Instance (ACI) est un choix approprié pour un déploiement rapide dans un environnement de développement/test, tandis qu’Azure Kubernetes Service (AKS) est adapté aux déploiements de production évolutifs.

#### <a name="deploy-to-aci"></a>Déployer sur ACI

Configurez votre configuration de déploiement avec la méthode [deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-). Vous pouvez également ajouter des balises et des descriptions pour faciliter le suivi de votre service web.

```python
from azureml.core.webservice import AciWebservice, Webservice

# Configure 
aci_config = AciWebservice.deploy_configuration(cpu_cores=1, 
                                                memory_gb=1, 
                                                tags={'method' : 'sklearn'}, 
                                                description='Diabetes model',
                                                location='eastus2')
```

Déployez ensuite l’image en utilisant la méthode [deploy_from_image()](/python/api/azureml-core/azureml.core.webservice.webservice(class)?view=azure-ml-py#deploy-from-image-workspace--name--image--deployment-config-none--deployment-target-none--overwrite-false-) du SDK Azure Machine Learning. 

```python
webservice = Webservice.deploy_from_image( image=azure_image, 
                                           workspace=ws, 
                                           name='diabetes-model-1', 
                                           deployment_config=aci_config)

webservice.wait_for_deployment(show_output=True)
```
#### <a name="deploy-to-aks"></a>Déployer sur AKS

Pour déployer sur AKS, créez d’abord un cluster AKS et ajoutez-y l’image Docker que vous voulez déployer. Pour cet exemple, ajoutez l’image précédemment créée à partir du déploiement ACI.

Pour obtenir l’image du déploiement ACI précédent, utilisez la classe [Image](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.image.image?view=azure-ml-py). 

```python
from azureml.core.image import Image

# Get the image by name, you can change this based on the image you want to deploy
myimage = Image(workspace=ws, name='sklearn-image') 
```

Créez un cluster AKS avec la méthode [ComputeTarget.create()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.computetarget?view=azure-ml-py#create-workspace--name--provisioning-configuration-). La création d’un cluster peut prendre de 20 à 25 minutes.

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
Configurez votre configuration de déploiement avec la méthode [deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-). Vous pouvez également ajouter des balises et des descriptions pour faciliter le suivi de votre service web.

```python
from azureml.core.webservice import Webservice, AksWebservice
from azureml.core.image import ContainerImage

# Set the web service configuration (using default here with app insights)
aks_config = AksWebservice.deploy_configuration(enable_app_insights=True)

# Unique service name
service_name ='aks-service'
```

Déployez ensuite l’image en utilisant la méthode [deploy_from_image()](/python/api/azureml-core/azureml.core.webservice.webservice(class)?view=azure-ml-py#deploy-from-image-workspace--name--image--deployment-config-none--deployment-target-none--overwrite-false-) du SDK Azure Machine Learning. 

```python
# Webservice creation using single command
aks_service = Webservice.deploy_from_image( workspace=ws, 
                                            name=service_name,
                                            deployment_config = aks_config
                                            image = myimage,
                                            deployment_target = aks_target)

aks_service.wait_for_deployment(show_output=True)
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

Les [notebooks MLflow avec Azure Machine Learning](https://aka.ms/azureml-mlflow-examples) illustrent et développent les concepts abordés dans cet article.

## <a name="next-steps"></a>Étapes suivantes
* [Gérez vos modèles](concept-model-management-and-deployment.md).
* Surveillez la [dérive des données](how-to-monitor-data-drift.md) de vos modèles de production.
