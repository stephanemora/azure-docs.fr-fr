---
title: Comment et où déployer des modèles ?
titleSuffix: Azure Machine Learning service
description: 'Découvrez comment et où déployer vos modèles de service Azure Machine Learning, notamment : Azure Container Instances, Azure Kubernetes Service, Azure IoT Edge et FPGA (Field-Programmable Gate Arrays).'
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 07/08/2019
ms.custom: seoapril2019
ms.openlocfilehash: 6b9ebb2f7ef46fd2900d036f178201863ecbc8d4
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68358822"
---
# <a name="deploy-models-with-the-azure-machine-learning-service"></a>Déployer des modèles avec le service Azure Machine Learning

Découvrez comment déployer votre modèle Machine Learning en tant que service web dans le cloud Azure ou sur des appareils IoT Edge. 

Le workflow est similaire quel que soit l’endroit [où vous déployez](#target) votre modèle :

1. Inscrire le modèle.
1. Préparer le déploiement (spécifier les ressources, l’utilisation et la cible de calcul).
1. Déployer le modèle sur la cible de calcul.
1. Tester le modèle déployé, également appelé service web.

Pour plus d’informations sur les concepts impliqués dans le workflow de déploiement, consultez [Déployer, gérer et surveiller des modèles avec le service Azure Machine Learning](concept-model-management-and-deployment.md).

## <a name="prerequisites"></a>Prérequis

- Un modèle Si vous n’avez pas de modèle entraîné, vous pouvez utiliser les fichiers de modèle et de dépendance fournis dans [ce tutoriel](https://aka.ms/azml-deploy-cloud).

- L’[extension Azure CLI pour Machine Learning service](reference-azure-machine-learning-cli.md), le [SDK Azure Machine Learning pour Python](https://aka.ms/aml-sdk) ou l’[extension Azure Machine Learning pour Visual Studio Code](how-to-vscode-tools.md).

## <a id="registermodel"></a> Inscrire votre modèle

Un modèle inscrit est un conteneur logique pour un ou plusieurs fichiers qui composent votre modèle. Par exemple, si vous disposez d'un modèle stocké dans plusieurs fichiers, vous pouvez enregistrer ces derniers en tant que modèle unique dans l'espace de travail. Après l’inscription, vous pouvez télécharger ou déployer le modèle inscrit et recevoir tous les fichiers qui ont été inscrits.

Les modèles Machine Learning sont inscrits dans votre espace de travail Azure Machine Learning. Le modèle peut provenir d’Azure Machine Learning ou d’un autre emplacement. Les exemples suivants montrent comment inscrire un modèle à partir d’un fichier :

### <a name="register-a-model-from-an-experiment-run"></a>Inscrire un modèle à partir d’une exécution d’expérience

+ **Exemple Scikit-learn avec le SDK**
  ```python
  model = run.register_model(model_name='sklearn_mnist', model_path='outputs/sklearn_mnist_model.pkl')
  print(model.name, model.id, model.version, sep='\t')
  ```

  > [!TIP]
  > Pour inclure plusieurs fichiers dans l’inscription du modèle, définissez `model_path` dans le répertoire contenant les fichiers.

+ **Avec l’interface CLI**

  ```azurecli-interactive
  az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment
  ```

  > [!TIP]
  > Pour inclure plusieurs fichiers dans l’inscription du modèle, définissez `--asset-path` dans le répertoire contenant les fichiers.

+ **Avec VS Code**

  Utilisez l’extension [VS Code](how-to-vscode-tools.md#deploy-and-manage-models) pour inscrire des modèles à l’aide de fichiers ou dossiers de modèles.

### <a name="register-an-externally-created-model"></a>Inscrire un modèle créé en externe

[!INCLUDE [trusted models](../../../includes/machine-learning-service-trusted-model.md)]

Vous pouvez inscrire un modèle créé en externe en indiquant le **chemin local** du modèle. Vous pouvez fournir un dossier ou un seul fichier.

+ **Exemple ONNX avec le SDK Python :**
  ```python
  onnx_model_url = "https://www.cntk.ai/OnnxModels/mnist/opset_7/mnist.tar.gz"
  urllib.request.urlretrieve(onnx_model_url, filename="mnist.tar.gz")
  !tar xvzf mnist.tar.gz
  
  model = Model.register(workspace = ws,
                         model_path ="mnist/model.onnx",
                         model_name = "onnx_mnist",
                         tags = {"onnx": "demo"},
                         description = "MNIST image classification CNN from ONNX Model Zoo",)
  ```

  > [!TIP]
  > Pour inclure plusieurs fichiers dans l’inscription du modèle, définissez `model_path` dans le répertoire contenant les fichiers.

+ **Avec l’interface CLI**
  ```azurecli-interactive
  az ml model register -n onnx_mnist -p mnist/model.onnx
  ```

  > [!TIP]
  > Pour inclure plusieurs fichiers dans l’inscription du modèle, définissez `-p` dans le répertoire contenant les fichiers.

**Durée estimée** : Environ 10 secondes.

Pour plus d'informations, consultez la documentation de référence de la classe [Model](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py).

Pour plus d’informations sur l’utilisation de modèles formés en dehors du service Azure Machine Learning, consultez [Déployer un modèle existant](how-to-deploy-existing-model.md).

<a name="target"></a>

## <a name="choose-a-compute-target"></a>Choisir une cible de calcul

Les cibles de calcul (ou ressources de calcul) suivantes peuvent héberger votre déploiement de service web. 

[!INCLUDE [aml-compute-target-deploy](../../../includes/aml-compute-target-deploy.md)]

## <a name="prepare-to-deploy"></a>Préparer au déploiement

Pour déployer un modèle en tant que service web, vous devez créer une configuration d’inférence (`InferenceConfig`) et une configuration de déploiement. L’inférence, ou scoring du modèle, est la phase où le modèle déployé est utilisé pour la prédiction, généralement sur des données de production. Dans la configuration de l’inférence, vous spécifiez les scripts et les dépendances nécessaires à l’utilisation de votre modèle. Dans la configuration du déploiement, vous spécifiez de quelle manière le modèle doit être utilisé sur la cible de calcul.

> [!IMPORTANT]
> Le Kit de développement logiciel (SDK) Azure Machine Learning n’offre aucun moyen pour les déploiements de service Web ou de IoT Edge d’accéder à votre magasin de données ou à vos jeux de données. Si vous avez besoin du modèle déployé pour accéder aux données stockées en dehors du déploiement, comme dans un compte de stockage Azure, vous devez développer une solution de code personnalisée à l’aide du Kit de développement logiciel (SDK) approprié. Exemple : [Kit de développement logiciel (SDK) Stockage Azure pour Python](https://github.com/Azure/azure-storage-python).
>
> Une autre solution possible pour votre scénario consiste à utiliser les [prédictions par lots](how-to-run-batch-predictions.md), qui donnent accès aux magasins de travail lors du scoring.

### <a id="script"></a> 1. Définir votre script d’entrée et les dépendances

Le script d’entrée reçoit les données envoyées à un service web déployé, puis les passe au modèle. Ensuite, il prend la réponse retournée par le modèle et la retourne au client. **Le script est propre à votre modèle**. Il doit comprendre les données que le modèle attend et retourne.

Le script contient deux fonctions qui chargent et exécutent le modèle :

* `init()`: en général, cette fonction charge le modèle dans un objet global. Cette fonction est exécutée une seule fois lors du démarrage du conteneur Docker de votre service web.

* `run(input_data)`: cette fonction utilise le modèle pour prédire une valeur basée sur les données d'entrée. Les entrées et les sorties de l’exécution utilisent en général JSON pour la sérialisation et la désérialisation. Vous pouvez également utiliser des données binaires brutes. Vous pouvez transformer les données avant de les envoyer au modèle ou avant de les retourner au client.

#### <a name="what-is-get_model_path"></a>Qu’est-ce que l’API get_model_path ?

Quand vous inscrivez un modèle, vous fournissez un nom de modèle qui sera utilisé pour la gestion du modèle dans le Registre. Vous utilisez ce nom avec [Model.get_model_path()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#get-model-path-model-name--version-none---workspace-none-) pour récupérer le chemin de chaque fichier de modèle présent sur le système de fichiers local. Si vous inscrivez un dossier ou une collection de fichiers, cette API retourne le chemin du répertoire qui contient ces fichiers.

Lorsque vous inscrivez un modèle, vous lui donnez un nom qui reflète l’emplacement du modèle, localement ou durant le déploiement du service.

L’exemple ci-dessous retourne le chemin d’un seul fichier appelé `sklearn_mnist_model.pkl` (mais qui a été inscrit sous le nom `sklearn_mnist`) :

```python
model_path = Model.get_model_path('sklearn_mnist')
```

#### <a name="optional-automatic-swagger-schema-generation"></a>(Facultatif) Génération automatique d’un schéma Swagger

Si vous voulez générer automatiquement un schéma pour votre service web, spécifiez un exemple d’entrée et/ou de sortie dans le constructeur pour l’un des objets de type définis. Le type et l’exemple fournis sont alors utilisés automatiquement pour créer le schéma. Azure Machine Learning service crée ensuite une spécification (Swagger) [OpenAPI](https://swagger.io/docs/specification/about/) pour le service web pendant le déploiement.

Les types suivants sont pris en charge :

* `pandas`
* `numpy`
* `pyspark`
* Objet Python standard

Pour utiliser la génération de schéma, incluez le package `inference-schema` dans votre fichier d’environnement Conda. L’exemple suivant contient `[numpy-support]`, car le script d’entrée utilise un type de paramètre numpy : 

#### <a name="example-dependencies-file"></a>Exemple de fichier de dépendances
L’extrait YAML suivant est un exemple de fichier de dépendances Conda pour l’inférence.

```YAML
name: project_environment
dependencies:
  - python=3.6.2
  - pip:
    - azureml-defaults
    - scikit-learn==0.20.0
    - inference-schema[numpy-support]
```

Si vous souhaitez utiliser la génération de schéma automatique, votre script d’entrée **doit** importer les packages `inference-schema`. 

Définissez les exemples de formats d’entrée et de sortie dans les variables `input_sample` et `output_sample`, qui représentent les formats de requête et de réponse pour le service web. Utilisez ces exemples dans les éléments décoratifs des fonctions d’entrée et de sortie sur la fonction `run()`. L’exemple scikit-learn ci-dessous utilise la génération de schéma.

> [!TIP]
> Après avoir déployé le service, utilisez la propriété `swagger_uri` pour récupérer le document JSON du schéma.

#### <a name="example-entry-script"></a>Exemple de script d’entrée

L’exemple suivant montre comment accepter et retourner des données JSON :

```python
#example: scikit-learn and Swagger
import json
import numpy as np
from sklearn.externals import joblib
from sklearn.linear_model import Ridge
from azureml.core.model import Model

from inference_schema.schema_decorators import input_schema, output_schema
from inference_schema.parameter_types.numpy_parameter_type import NumpyParameterType


def init():
    global model
    # note here "sklearn_regression_model.pkl" is the name of the model registered under
    # this is a different behavior than before when the code is run locally, even though the code is the same.
    model_path = Model.get_model_path('sklearn_regression_model.pkl')
    # deserialize the model file back into a sklearn model
    model = joblib.load(model_path)


input_sample = np.array([[10, 9, 8, 7, 6, 5, 4, 3, 2, 1]])
output_sample = np.array([3726.995])


@input_schema('data', NumpyParameterType(input_sample))
@output_schema(NumpyParameterType(output_sample))
def run(data):
    try:
        result = model.predict(data)
        # you can return any datatype as long as it is JSON-serializable
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```

#### <a name="example-script-with-dictionary-input-support-consumption-from-power-bi"></a>Exemple de script avec une entrée de dictionnaire (prise en charge de la consommation à partir de Power BI)

L’exemple suivant montre comment définir les données d’entrée en tant que dictionnaire <clé:valeur> à l’aide d’un Dataframe. Cette méthode est prise en charge pour la consommation du service web déployé à partir de Power BI ([en savoir plus sur la consommation du service web à partir de Power BI](https://docs.microsoft.com/power-bi/service-machine-learning-integration)) :

```python
import json
import pickle
import numpy as np
import pandas as pd
import azureml.train.automl
from sklearn.externals import joblib
from azureml.core.model import Model

from inference_schema.schema_decorators import input_schema, output_schema
from inference_schema.parameter_types.numpy_parameter_type import NumpyParameterType
from inference_schema.parameter_types.pandas_parameter_type import PandasParameterType


def init():
    global model
    # replace model_name with your actual model name, if needed
    model_path = Model.get_model_path('model_name')
    # deserialize the model file back into a sklearn model
    model = joblib.load(model_path)


input_sample = pd.DataFrame(data=[{
    # This is a decimal type sample. Use the data type that reflects this column in your data
    "input_name_1": 5.1,
    # This is a string type sample. Use the data type that reflects this column in your data
    "input_name_2": "value2",
    # This is a integer type sample. Use the data type that reflects this column in your data
    "input_name_3": 3
}])

# This is a integer type sample. Use the data type that reflects the expected result
output_sample = np.array([0])


@input_schema('data', PandasParameterType(input_sample))
@output_schema(NumpyParameterType(output_sample))
def run(data):
    try:
        result = model.predict(data)
        # you can return any datatype as long as it is JSON-serializable
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```
Pour obtenir d’autres exemples de scripts, consultez ces exemples :

* Pytorch : [https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-pytorch](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-pytorch)
* TensorFlow : [https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow)
* Keras : [https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras)
* ONNX : [https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx/](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx/)
* Scoring par rapport à des données binaires : [Comment consommer un service web](how-to-consume-web-service.md)

### <a name="2-define-your-inferenceconfig"></a>2. Définir votre configuration d’inférence

La configuration de l’inférence décrit comment configurer le modèle pour les prédictions. L’exemple suivant montre comment créer une configuration d’inférence. Cette configuration spécifie le runtime, le script d’entrée et (facultatif) le fichier d’environnement conda :

```python
inference_config = InferenceConfig(runtime="python",
                                   entry_script="x/y/score.py",
                                   conda_file="env/myenv.yml")
```

Pour plus d’informations, consultez les informations de référence de classe sur [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py).

Pour plus d’informations sur l’utilisation d’une image Docker personnalisée avec configuration de l’inférence, consultez [Guide pratique pour déployer un modèle à l’aide d’une image Docker personnalisée](how-to-deploy-custom-docker-image.md).

### <a name="cli-example-of-inferenceconfig"></a>Exemple CLI InferenceConfig

[!INCLUDE [inferenceconfig](../../../includes/machine-learning-service-inference-config.md)]

La commande suivante montre comment déployer un modèle à l’aide de l’interface CLI :

```azurecli-interactive
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json
```

Dans cet exemple, la configuration contient les éléments suivants :

* L’information indiquant l’utilisation de Python
* Le [script d’entrée](#script) qui est utilisé pour gérer les requêtes web envoyées au service déployé
* Le fichier Conda qui décrit les packages Python nécessaires à l’inférence

Pour plus d’informations sur l’utilisation d’une image Docker personnalisée avec configuration de l’inférence, consultez [Guide pratique pour déployer un modèle à l’aide d’une image Docker personnalisée](how-to-deploy-custom-docker-image.md).

### <a name="3-define-your-deployment-configuration"></a>3. Définir votre configuration de déploiement

Avant de commencer le déploiement, vous devez définir la configuration de déploiement. La configuration de déploiement est propre à la cible de calcul qui va héberger le service web. Par exemple, dans un déploiement local, vous devez spécifier le port sur lequel le service accepte les requêtes.

Vous pouvez aussi avoir besoin de créer la ressource de calcul. C’est le cas, par exemple, si vous n’avez pas encore associé Azure Kubernetes Service à votre espace de travail.

Le tableau suivant donne un exemple de configuration de déploiement créée pour chaque cible de calcul :

| Cible de calcul | Exemple de configuration de déploiement |
| ----- | ----- |
| Local | `deployment_config = LocalWebservice.deploy_configuration(port=8890)` |
| Azure Container Instance | `deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |
| Azure Kubernetes Service | `deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |

Les sections suivantes expliquent comment créer la configuration de déploiement et l’utiliser ensuite pour déployer le service web.

### <a name="optional-profile-your-model"></a>Facultatif : Profiler votre modèle

Avant de déployer votre modèle en tant que service, vous pouvez le profiler afin de déterminer les exigences optimales en processeur et en mémoire avec le SDK ou la CLI.  Les résultats du profilage du modèle sont fournis sous la forme d’un objet `Run`. Les détails complets du [schéma du profil de modèle se trouvent dans la documentation de l’API](https://docs.microsoft.com/python/api/azureml-core/azureml.core.profile.modelprofile?view=azure-ml-py)

En savoir plus sur [la façon de profiler votre modèle à l’aide du SDK](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#profile-workspace--profile-name--models--inference-config--input-data-).

Pour profiler votre modèle à l’aide de la CLI, utilisez [az ml model profile](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-profile).

## <a name="deploy-to-target"></a>Déployer sur la cible

### <a id="local"></a> Déploiement local

Pour un déploiement local, vous devez avoir **Docker installé** sur votre machine locale.

+ **Avec le kit SDK**

  ```python
  deployment_config = LocalWebservice.deploy_configuration(port=8890)
  service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
  service.wait_for_deployment(show_output = True)
  print(service.state)
  ```

+ **Avec l’interface CLI**

    Pour déployer à l’aide de la CLI, utilisez la commande suivante. Remplacez `mymodel:1` par le nom et la version du modèle inscrit :

  ```azurecli-interactive
  az ml model deploy -m mymodel:1 -ic inferenceconfig.json -dc deploymentconfig.json
  ```

    [!INCLUDE [deploymentconfig](../../../includes/machine-learning-service-local-deploy-config.md)]

### <a id="aci"></a> Azure Container Instances (DEVTEST)

Utilisez Azure Container Instances pour déployer vos modèles en tant que service web si une ou plusieurs des conditions suivantes sont remplies :
- Vous avez besoin de déployer et de valider rapidement votre modèle.
- Vous testez un modèle en cours de développement. 

Pour plus d’informations sur les quotas et la disponibilité d’ACI en fonction des régions, consultez l’article [Quotas et disponibilité dans les régions pour Azure Container Instances](https://docs.microsoft.com/azure/container-instances/container-instances-quotas).

+ **Avec le kit SDK**

  ```python
  deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)
  service = Model.deploy(ws, "aciservice", [model], inference_config, deployment_config)
  service.wait_for_deployment(show_output = True)
  print(service.state)
  ```

+ **Avec l’interface CLI**

    Pour déployer à l’aide de la CLI, utilisez la commande suivante. Remplacez `mymodel:1` par le nom et la version du modèle inscrit. Remplacez `myservice` par le nom à attribuer à ce service :

    ```azurecli-interactive
    az ml model deploy -m mymodel:1 -n myservice -ic inferenceconfig.json -dc deploymentconfig.json
    ```

    [!INCLUDE [deploymentconfig](../../../includes/machine-learning-service-aci-deploy-config.md)]

+ **Avec VS Code**

  Pour [déployer vos modèles avec VS Code](how-to-vscode-tools.md#deploy-and-manage-models), vous n’avez pas besoin de créer un conteneur ACI en vue de le tester au préalable, car les conteneurs ACI sont créés instantanément.

Pour plus d'informations, consultez la documentation de référence des classes [AciWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py) et [Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice?view=azure-ml-py).

### <a id="aks"></a>Azure Kubernetes Service (DEVTEST & PRODUCTION)

Vous pouvez utiliser un cluster AKS existant ou en créer un en utilisant le kit SDK Azure Machine Learning, l’interface CLI ou le portail Azure.

<a id="deploy-aks"></a>

Si vous avez déjà un cluster AKS attaché, vous pouvez le déployer. Si vous n’avez pas créé ou attaché de cluster AKS, suivez le processus pour <a href="#create-attach-aks">créer un cluster AKS</a>.

+ **Avec le kit SDK**

  ```python
  aks_target = AksCompute(ws,"myaks")
  # If deploying to a cluster configured for dev/test, ensure that it was created with enough
  # cores and memory to handle this deployment configuration. Note that memory is also used by
  # things such as dependencies and AML components.
  deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)
  service = Model.deploy(ws, "aksservice", [model], inference_config, deployment_config, aks_target)
  service.wait_for_deployment(show_output = True)
  print(service.state)
  print(service.get_logs())
  ```

+ **Avec l’interface CLI**

    Pour déployer à l’aide de la CLI, utilisez la commande suivante. Remplacez `myaks` par le nom de la cible de calcul AKS. Remplacez `mymodel:1` par le nom et la version du modèle inscrit. Remplacez `myservice` par le nom à attribuer à ce service :

  ```azurecli-interactive
  az ml model deploy -ct myaks -m mymodel:1 -n myservice -ic inferenceconfig.json -dc deploymentconfig.json
  ```

    [!INCLUDE [deploymentconfig](../../../includes/machine-learning-service-aks-deploy-config.md)]

+ **Avec VS Code**

  Vous pouvez également choisir de [déployer sur AKS à l’aide de l’extension VS Code](how-to-vscode-tools.md#deploy-and-manage-models). Dans ce cas, vous devez préalablement configurer les clusters AKS.

Pour en savoir plus sur le déploiement d’AKS et sa mise à l’échelle automatique, consultez la documentation de référence sur [AksWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice).

#### Créer un cluster AKS<a id="create-attach-aks"></a>
**Durée estimée** : environ 20 minutes.

La création ou l’attachement d’un cluster AKS est un processus à effectuer une seule fois pour votre espace de travail. Vous pouvez le réutiliser pour vos autres déploiements. Si vous supprimez le cluster ou le groupe de ressources dans lequel il se trouve, vous devrez recréer un cluster lors du prochain déploiement. Vous pouvez avoir plusieurs clusters AKS attachés à votre espace de travail.

Si vous souhaitez créer un cluster AKS pour le développement, la validation et le test, définissez `cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST` quand vous utilisez [`provisioning_configuration()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py). Un cluster créé avec ce paramètre a un seul nœud.

> [!IMPORTANT]
> Le paramètre `cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST` crée un cluster AKS qui ne convient pas à la gestion du trafic de production. Les temps d’inférence peuvent être plus longs que sur un cluster créé spécialement pour la production. Par ailleurs, la tolérance de panne n’est pas garantie pour les clusters de développement/test.
>
> Les clusters créés pour les besoins de développement/test doivent si possible avoir deux processeurs virtuels.

L’exemple suivant montre comment créer un cluster Azure Kubernetes Service :

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Use the default configuration (you can also provide parameters to customize this).
# For example, to create a dev/test cluster, use:
# prov_config = AksCompute.provisioning_configuration(cluster_purpose = AksComputee.ClusterPurpose.DEV_TEST)
prov_config = AksCompute.provisioning_configuration()

aks_name = 'myaks'
# Create the cluster
aks_target = ComputeTarget.create(workspace=ws,
                                  name=aks_name,
                                  provisioning_configuration=prov_config)

# Wait for the create process to complete
aks_target.wait_for_completion(show_output=True)
```

Pour plus d’informations sur la création d’un cluster AKS en dehors du SDK Azure Machine Learning, consultez les articles suivants :
* [Créer un cluster AKS](https://docs.microsoft.com/cli/azure/aks?toc=%2Fazure%2Faks%2FTOC.json&bc=%2Fazure%2Fbread%2Ftoc.json&view=azure-cli-latest#az-aks-create)
* [Créer un cluster AKS (portail)](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough-portal?view=azure-cli-latest)

Pour plus d’informations sur le paramètre `cluster_purpose`, consultez la documentation de référence sur [AksCompute.ClusterPurpose](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose?view=azure-ml-py).

> [!IMPORTANT]
> Pour [`provisioning_configuration()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py), si vous choisissez des valeurs personnalisées pour agent_count et vm_size, vous devez vous assurer que agent_count multiplié par vm_size est supérieur ou égal à 12 processeurs virtuels. Par exemple, si vous définissez vm_size sur « Standard_D3_v2 », qui comporte 4 processeurs virtuels, vous devez définir agent_count sur un nombre supérieur ou égal à 3.
>
> Le kit de développement logiciel (SDK) Azure Machine Learning ne prend pas en charge la mise à l’échelle d'un cluster AKS. Pour mettre à l'échelle les nœuds du cluster, utilisez l’interface utilisateur de votre cluster AKS dans le portail Azure. Vous pouvez modifier le nombre de nœuds, mais pas la taille de machine virtuelle du cluster.

#### <a name="attach-an-existing-aks-cluster"></a>Attacher un cluster AKS existant
**Durée estimée** : 5 minutes environ.

Si vous avez déjà un cluster AKS version 1.12.## dans votre abonnement Azure, vous pouvez l’utiliser pour déployer votre image.

> [!WARNING]
> Quand vous attachez un cluster AKS à un espace de travail, vous pouvez spécifier la façon dont vous allez l’utiliser en définissant le paramètre `cluster_purpose`.
>
> Si vous ne définissez pas le paramètre `cluster_purpose`, ou `cluster_purpose = AksCompute.ClusterPurpose.FAST_PROD`, le cluster doit avoir au moins 12 processeurs virtuels disponibles.
>
> Si vous définissez `cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST`, le cluster n’a pas besoin d’autant de processeurs virtuels. Toutefois, un cluster configuré pour le développement/test n’est pas approprié pour le trafic de production et peut augmenter les temps d’inférence.

Le code suivant montre comment attacher un cluster AKS 1.12.## à votre espace de travail :

```python
from azureml.core.compute import AksCompute, ComputeTarget
# Set the resource group that contains the AKS cluster and the cluster name
resource_group = 'myresourcegroup'
cluster_name = 'mycluster'

# Attach the cluster to your workgroup. If the cluster has less than 12 virtual CPUs, use the following instead:
# attach_config = AksCompute.attach_configuration(resource_group = resource_group,
#                                         cluster_name = cluster_name,
#                                         cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST)
attach_config = AksCompute.attach_configuration(resource_group=resource_group,
                                                cluster_name=cluster_name)
aks_target = ComputeTarget.attach(ws, 'mycompute', attach_config)
```

Pour plus d’informations sur `attack_configuration()`, consultez la documentation de référence sur [AksCompute.attach_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-).

Pour plus d’informations sur le paramètre `cluster_purpose`, consultez la documentation de référence sur [AksCompute.ClusterPurpose](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose?view=azure-ml-py).

## <a name="consume-web-services"></a>Utiliser des services web

Chaque service web déployé fournit une API REST, qui vous permet de créer des applications clientes dans divers langages de programmation. Si vous avez activé l’authentification pour votre service, vous devez fournir une clé de service comme jeton dans l’en-tête de requête.

### <a name="request-response-consumption"></a>Consommation de requête-réponse

Voici un exemple montrant comment appeler votre service dans Python :
```python
import requests
import json

headers = {'Content-Type': 'application/json'}

if service.auth_enabled:
    headers['Authorization'] = 'Bearer '+service.get_keys()[0]

print(headers)

test_sample = json.dumps({'data': [
    [1, 2, 3, 4, 5, 6, 7, 8, 9, 10],
    [10, 9, 8, 7, 6, 5, 4, 3, 2, 1]
]})

response = requests.post(
    service.scoring_uri, data=test_sample, headers=headers)
print(response.status_code)
print(response.elapsed)
print(response.json())
```

Pour plus d’informations, consultez [Créer des applications clientes pour utiliser des services web](how-to-consume-web-service.md).


### <a id="azuremlcompute"></a> Inférence par lots
Les cibles de calcul Azure Machine Learning sont créées et managées par Azure Machine Learning service. Elles peuvent être utilisées pour la prédiction par lots à partir d’Azure Machine Learning Pipelines.

Pour obtenir une présentation détaillée de l’inférence par lots avec la capacité de calcul Azure Machine Learning, lisez l’article [Exécuter des prédictions par lots](how-to-run-batch-predictions.md).

### <a id="iotedge"></a> Inférence IoT Edge
La prise en charge du déploiement en périphérie est en préversion. Pour plus d’informations, consultez l’article [Déployer Azure Machine Learning en tant que module IoT Edge](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-machine-learning).


## <a id="update"></a> Mettre à jour les services web

Lorsque vous créez un modèle, vous devez mettre à jour manuellement chacun des services pour lesquels vous souhaitez utiliser le nouveau modèle. Pour effectuer une mise à jour du service web, utilisez la méthode `update`. Le code suivant montre comment effectuer une mise à jour du service web pour utiliser un nouveau modèle :

```python
from azureml.core.webservice import Webservice
from azureml.core.model import Model

# register new model
new_model = Model.register(model_path="outputs/sklearn_mnist_model.pkl",
                           model_name="sklearn_mnist",
                           tags={"key": "0.1"},
                           description="test",
                           workspace=ws)

service_name = 'myservice'
# Retrieve existing service
service = Webservice(name=service_name, workspace=ws)

# Update to new model(s)
service.update(models=[new_model])
print(service.state)
print(service.get_logs())
```

## <a name="continuous-model-deployment"></a>Modèle de déploiement en continu 

Vous pouvez déployer des modèles en continu à l’aide de l’extension Machine Learning pour [Azure DevOps](https://azure.microsoft.com/services/devops/). L'extension Machine Learning pour Azure DevOps vous permet de déclencher un pipeline de déploiement lorsqu’un nouveau modèle Machine Learning est inscrit dans l’espace de travail du service Azure Machine Learning. 

1. Inscrivez-vous sur [Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/get-started/pipelines-sign-up?view=azure-devops), ce qui permet l'intégration et la livraison continues de votre application vers n’importe quelle plateforme/n'importe quel cloud. Azure Pipelines [diffère des pipelines ML](concept-ml-pipelines.md#compare). 

1. [Créez un projet Azure DevOps.](https://docs.microsoft.com/azure/devops/organizations/projects/create-project?view=azure-devops)

1. Installer l'[extension Machine Learning pour Azure Pipelines](https://marketplace.visualstudio.com/items?itemName=ms-air-aiagility.vss-services-azureml&targetId=6756afbe-7032-4a36-9cb6-2771710cadc2&utm_source=vstsproduct&utm_medium=ExtHubManageList) 

1. Utilisez __Connexions au service__ pour configurer une connexion de principal de service à votre espace de travail de service Azure Machine Learning afin d'accéder à tous vos artefacts. Accédez aux paramètres du projet, cliquez sur Connexions au service, puis sélectionnez Azure Resource Manager.

    ![view-service-connection](media/how-to-deploy-and-where/view-service-connection.png) 

1. Définissez AzureMLWorkspace en tant que __niveau d'étendue__ et renseignez les paramètres suivants.

    ![view-azure-resource-manager](media/how-to-deploy-and-where/resource-manager-connection.png)

1. Ensuite, pour déployer en continu votre modèle Machine Learning à l’aide d'Azure Pipelines, sous Pipelines, sélectionnez __Mise en production__. Ajoutez un nouvel artefact, sélectionnez l’artefact Modèle AzureML et la connexion au service créée à l’étape précédente. Sélectionnez le modèle et la version pour déclencher un déploiement. 

    ![select-AzureMLmodel-artifact](media/how-to-deploy-and-where/enable-modeltrigger-artifact.png)

1. Activez le déclencheur de modèle sur votre artefact de modèle. En activant le déclencheur, chaque fois que la version spécifiée (version la plus récente) de ce modèle est inscrite dans votre espace de travail, un pipeline de mise en production Azure DevOps est déclenché. 

    ![enable-model-trigger](media/how-to-deploy-and-where/set-modeltrigger.png)

Pour des exemples de projets, consultez [le référentiel MLOps](https://github.com/Microsoft/MLOps)

## <a name="clean-up-resources"></a>Supprimer des ressources
Pour supprimer un service web déployé, utilisez `service.delete()`.
Pour supprimer un modèle inscrit, utilisez `model.delete()`.

Pour plus d’informations, consultez la documentation de référence sur [WebService.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#delete--) et [Model.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#delete--).

## <a name="next-steps"></a>Étapes suivantes
* [Guide pratique pour déployer un modèle à l’aide d’une image Docker personnalisée](how-to-deploy-custom-docker-image.md)
* [Résolution des problèmes liés au déploiement](how-to-troubleshoot-deployment.md)
* [Sécuriser les services web Azure Machine Learning avec SSL](how-to-secure-web-service.md)
* [Utiliser un modèle ML déployé en tant que service web](how-to-consume-web-service.md)
* [Superviser vos modèles Azure Machine Learning avec Application Insights](how-to-enable-app-insights.md)
* [Collecter des données pour des modèles en production](how-to-enable-data-collection.md)

