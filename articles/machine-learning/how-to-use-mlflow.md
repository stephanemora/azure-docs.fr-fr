---
title: MLflow Tracking pour les expériences ML
titleSuffix: Azure Machine Learning
description: Configurez MLflow avec Azure Machine Learning pour consigner les métriques et artefacts de modèles Machine Learning créés dans des clusters Databricks, votre environnement local ou un environnement de machine virtuelle.
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: nibaccam
ms.topic: conceptual
ms.date: 02/03/2020
ms.custom: seodec18
ms.openlocfilehash: 95567a177635dc7d7ed03404487e62c76db8bdac
ms.sourcegitcommit: a9784a3fd208f19c8814fe22da9e70fcf1da9c93
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/22/2020
ms.locfileid: "83779123"
---
# <a name="track-models-metrics-with-mlflow-and-azure-machine-learning-preview"></a>Suivre des métriques de modèle avec MLflow et déployer Azure Machine Learning (préversion)

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Cet article montre comment activer l’URI de suivi de MLflow et l’API de journalisation, collectivement appelés [MLflow Tracking](https://mlflow.org/docs/latest/quickstart.html#using-the-tracking-api), pour connecter vos expériences de MLflow et Azure Machine Learning. Cela vous permet de suivre et de journaliser les métriques et artefacts d’expériences dans votre [espace de travail Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/concept-azure-machine-learning-architecture#workspaces). Si vous utilisez déjà MLflow Tracking pour vos expériences, l’espace de travail fournit un emplacement centralisé, sécurisé et scalable pour stocker les métriques et les modèles d’entraînement.

<!--
+ Deploy your MLflow experiments as an Azure Machine Learning web service. By deploying as a web service, you can apply the Azure Machine Learning monitoring and data drift detection functionalities to your production models. 
-->

[MLflow](https://www.mlflow.org) est une bibliothèque open source permettant de gérer le cycle de vie de vos expériences de Machine Learning. MLFlow Tracking est un composant de MLflow qui journalise et suit vos artefacts de modèle et métriques d’exécution de formation, quel que soit l’environnement de votre expérience : localement sur votre ordinateur, sur une cible de calcul distante, sur une machine virtuelle ou sur un cluster Azure Databricks. 

Le diagramme suivant montre qu’avec MLflow Tracking, vous suivez les métriques d’exécution d’une expérience et vous stockez les artefacts du modèle dans votre espace de travail Azure Machine Learning.

![diagramme de mlflow avec azure machine learning](./media/how-to-use-mlflow/mlflow-diagram-track.png)

> [!TIP]
> Les informations contenues dans ce document sont principalement destinées aux scientifiques des données et aux développeurs qui veulent superviser le processus d’entraînement du modèle. Si vous êtes un administrateur intéressé par la supervision de l’utilisation de ressources et d’événements d’Azure Machine Learning, comme des quotas, des cycles de formation accomplis ou des déploiements de modèles effectués, voir [Supervision d’Azure Machine Learning](monitor-azure-machine-learning.md).

## <a name="compare-mlflow-and-azure-machine-learning-clients"></a>Comparer les clients MLflow et Azure Machine Learning

 Le tableau ci-dessous récapitule les différents clients pouvant utiliser Azure Machine Learning et leurs fonctionnalités respectives.

 MLflow Tracking offre des fonctionnalités de stockage de journalisation de métriques et d’artefact qui ne sont autrement disponibles que via le [Kit de développement logiciel (SDK) Python Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).


| | MLflow&nbsp;Tracking <!--& Deployment--> | SDK Python Azure Machine Learning |  Interface CLI Azure Machine Learning | Azure Machine Learning Studio|
|---|---|---|---|---|
| Gérer l'espace de travail |   | ✓ | ✓ | ✓ |
| Utiliser des magasins de données  |   | ✓ | ✓ | |
| Journaliser les métriques      | ✓ | ✓ |   | |
| Charger les artefacts | ✓ | ✓ |   | |
| Afficher les mesures     | ✓ | ✓ | ✓ | ✓ |
| Gérer le calcul   |   | ✓ | ✓ | ✓ |

<!--| Deploy models    | ✓ | ✓ | ✓ | ✓ |
|Monitor model performance||✓|  |   |
| Detect data drift |   | ✓ |   | ✓ |
-->
## <a name="prerequisites"></a>Conditions préalables requises

* [Installez MLflow.](https://mlflow.org/docs/latest/quickstart.html)
* [Installez le kit SDK Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) sur votre ordinateur local. Le kit SDK assure la connexion permettant à MLflow d’accéder à votre espace de travail.
* [Créez un espace de travail Azure Machine Learning](how-to-manage-workspace.md).

## <a name="track-local-runs"></a>Suivre les exécutions locales

MLflow Tracking avec Azure Machine Learning vous permet de stocker les métriques et artefacts consignés à partir de vos exécutions locales dans votre espace de travail Azure Machine Learning.

Installez le package `azureml-mlflow` pour utiliser MLflow Tracking avec Azure Machine Learning sur vos expériences exécutées localement dans un Jupyter Notebook ou un éditeur de code.

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

Configurez votre environnement de calcul et d’apprentissage avec la classe [`Environment`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py). Incluez les packages pip `mlflow` et `azureml-mlflow` dans la section [`CondaDependencies`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py) de l’environnement. Construisez ensuite [`ScriptRunConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig?view=azure-ml-py) avec votre calcul à distance en tant que cible de calcul.

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

Avec cette configuration du cycle de calcul et d’apprentissage, utilisez la méthode `Experiment.submit('train.py')` pour soumettre un cycle. Cette méthode définit automatiquement l’URI du suivi MLflow et dirige la journalisation de MLflow vers votre espace de travail.

```Python
run = exp.submit(src)
```

## <a name="track-azure-databricks-runs"></a>Suivre les exécutions Azure Databricks

MLflow Tracking avec Azure Machine Learning vous permet de stocker les métriques et artefacts consignés à partir de vos exécutions Azure Databricks dans votre espace de travail Azure Machine Learning.

Pour exécuter vos expériences Mlflow avec Azure Databricks, vous devez d'abord créer un [espace de travail Azure Databricks et un cluster](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal). Dans votre cluster, veillez à installer la bibliothèque *azureml-mlflow* de PyPi pour vous assurer que votre cluster peut accéder aux fonctions et classes nécessaires.

À partir de là, importez votre bloc-notes d’expériences, attachez-le à votre cluster Azure Databricks et exécutez votre expérience. 

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

<!-- ## Deploy MLflow models as a web service

Deploying your MLflow experiments as an Azure Machine Learning web service allows you to leverage the Azure Machine Learning model management and data drift detection capabilities and apply them to your production models.

The following diagram demonstrates that with the MLflow deploy API you can deploy your existing MLflow models as an Azure Machine Learning web service, despite their frameworks--PyTorch, Tensorflow, scikit-learn, ONNX, etc., and manage your production models in your workspace.

![mlflow with azure machine learning diagram](./media/how-to-use-mlflow/mlflow-diagram-deploy.png)

### Log your model

Before you can deploy, be sure that your model is saved so you can reference it and its path location for deployment. In your training script, there should be code similar to the following [mlflow.sklearn.log_model()](https://www.mlflow.org/docs/latest/python_api/mlflow.sklearn.html) method, that saves your model to the specified outputs directory. 

```python
# change sklearn to pytorch, tensorflow, etc. based on your experiment's framework 
import mlflow.sklearn

# Save the model to the outputs directory for capture
mlflow.sklearn.log_model(regression_model, model_save_path)
```
>[!NOTE]
> Include the `conda_env` parameter to pass a dictionary representation of the dependencies and environment this model should be run in.

### Retrieve model from previous run

To retrieve the run, you need the run ID and the path in run history of where the model was saved. 

```python
# gets the list of runs for your experiment as an array
experiment_name = 'experiment-with-mlflow'
exp = ws.experiments[experiment_name]
runs = list(exp.get_runs())

# get the run ID and the path in run history
runid = runs[0].id
model_save_path = 'model'
```

### Deploy the model

Use the Azure Machine Learning SDK to deploy the model as a web service.

First, specify the deployment configuration. Azure Container Instance (ACI) is a suitable choice for a quick dev-test deployment, while Azure Kubernetes Service (AKS) is suitable for scalable production deployments.

#### Deploy to ACI

Set up your deployment configuration with the [deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-) method. You can also add tags and descriptions to help keep track of your web service.

```python
from azureml.core.webservice import AciWebservice, Webservice

# Configure 
aci_config = AciWebservice.deploy_configuration(cpu_cores=1, 
                                                memory_gb=1, 
                                                tags={'method' : 'sklearn'}, 
                                                description='Diabetes model',
                                                location='eastus2')
```

Then, register and deploy the model by using the Azure Machine Learning SDK [deploy](/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) method. 

```python
(webservice,model) = mlflow.azureml.deploy( model_uri='runs:/{}/{}'.format(run.id, model_path),
                      workspace=ws,
                      model_name='sklearn-model', 
                      service_name='diabetes-model-1', 
                      deployment_config=aci_config, 
                      tags=None, mlflow_home=None, synchronous=True)

webservice.wait_for_deployment(show_output=True)
```
#### Deploy to AKS

To deploy to AKS, first create an AKS cluster. Create an AKS cluster using the [ComputeTarget.create()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.computetarget?view=azure-ml-py#create-workspace--name--provisioning-configuration-) method. It may take 20-25 minutes to create a new cluster.

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
Set up your deployment configuration with the [deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-) method. You can also add tags and descriptions to help keep track of your web service.

```python
from azureml.core.webservice import Webservice, AksWebservice

# Set the web service configuration (using default here with app insights)
aks_config = AksWebservice.deploy_configuration(enable_app_insights=True, compute_target_name='aks-mlflow')


Then, deploy the image by using the Azure Machine Learning SDK [deploy()](Then, register and deploy the model by using the Azure Machine Learning SDK [deploy](/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) method. 

```python
# Webservice creation using single command
from azureml.core.webservice import AksWebservice, Webservice
(webservice, model) = mlflow.azureml.deploy( model_uri='runs:/{}/{}'.format(run.id, model_path),
                      workspace=ws,
                      model_name='sklearn-model', 
                      service_name='my-aks', 
                      deployment_config=aks_config, 
                      tags=None, mlflow_home=None, synchronous=True)


webservice.wait_for_deployment()
```

The service deployment can take several minutes.

## Clean up resources

If you don't plan to use the logged metrics and artifacts in your workspace, the ability to delete them individually is currently unavailable. Instead, delete the resource group that contains the storage account and workspace, so you don't incur any charges:

1. In the Azure portal, select **Resource groups** on the far left.

   ![Delete in the Azure portal](./media/how-to-use-mlflow/delete-resources.png)

1. From the list, select the resource group you created.

1. Select **Delete resource group**.

1. Enter the resource group name. Then select **Delete**.

 -->

 ## <a name="example-notebooks"></a>Exemples de notebooks

Les [notebooks MLflow avec Azure Machine Learning](https://aka.ms/azureml-mlflow-examples) illustrent et développent les concepts abordés dans cet article.

## <a name="next-steps"></a>Étapes suivantes
* [Gérez vos modèles](concept-model-management-and-deployment.md).
* Surveillez la [dérive des données](how-to-monitor-data-drift.md) de vos modèles de production.
