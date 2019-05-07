---
title: Comment et où déployer les modèles
titleSuffix: Azure Machine Learning service
description: 'Découvrez comment et où déployer vos modèles de service Azure Machine Learning, notamment : Azure Container Instances, Azure Kubernetes Service, Azure IoT Edge et FPGA (Field-Programmable Gate Arrays).'
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 05/02/2019
ms.custom: seoapril2019
ms.openlocfilehash: 113c894122fe2b0673dfb47f8a9c0cbecf4c6290
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65205079"
---
# <a name="deploy-models-with-the-azure-machine-learning-service"></a>Déployer des modèles avec le service Azure Machine Learning

Découvrez comment déployer votre modèle d’apprentissage comme un service web dans le cloud Azure ou sur des appareils IoT Edge. Les informations contenues dans ce document vous apprend à déployer sur les cibles de calcul suivantes :

| Cible de calcul | Type de déploiement | Description |
| ----- | ----- | ----- |
| [Service web local](#local) | Test et de débogage | Convient pour les tests et la résolution des problèmes limitée.
| [Azure Kubernetes Service (AKS)](#aks) | Inférence en temps réel | Convient aux déploiements de production à grande échelle. Fournit la mise à l’échelle automatique et des temps de réponse rapides. |
| [Azure Container Instances (ACI)](#aci) | Test | Convient pour la petite échelle, les charges de travail basées sur le processeur. |
| [Capacité de calcul Azure Machine Learning](how-to-run-batch-predictions.md) | (Version préliminaire) Inférence de lot | Exécuter le calcul sans serveur de notation par lots. Prend en charge des machines virtuelles normale et basse priorité. |
| [Azure IoT Edge](#iotedge) | (Version préliminaire) Module IoT | Déployer et traiter les modèles ML sur les appareils IoT. |

## <a name="deployment-workflow"></a>Workflow du déploiement

Le processus de déploiement d'un modèle est similaire pour toutes les cibles de calcul :

1. Inscrire ou les modèles.
1. Déployer des modèles.
1. Test déployé ou les modèles.

Pour plus d’informations sur les concepts impliqués dans le workflow de déploiement, consultez [Déployer, gérer et surveiller des modèles avec le service Azure Machine Learning](concept-model-management-and-deployment.md).

## <a name="prerequisites-for-deployment"></a>Conditions préalables pour le déploiement

- Un modèle Si vous n’avez pas d’un modèle formé, vous pouvez utiliser le modèle de & fichiers de dépendance fournie dans [ce didacticiel](http://aka.ms/azml-deploy-cloud).

- Le [extension Azure CLI pour le service Machine Learning](reference-azure-machine-learning-cli.md), ou le [Azure Machine Learning Python SDK](https://aka.ms/aml-sdk).

## <a id="registermodel"></a> Inscrire un modèle d’apprentissage automatique

Le registre de modèle est un moyen de stocker et d’organiser vos modèles entraînés dans le cloud Azure. Les modèles sont inscrits dans votre espace de travail de service Azure Machine Learning. Le modèle peut être formé à l’aide d’Azure Machine Learning ou importées à partir d’un modèle formé ailleurs. Les exemples suivants montrent comment inscrire un modèle à partir du fichier :

### <a name="register-a-model-from-an-experiment-run"></a>Inscrire un modèle à partir d’une exécution de l’expérience

**Exemple de Scikit-Learn avec l’interface CLI**
```azurecli-interactive
az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment
```
**L’utilisation du SDK**
```python
model = run.register_model(model_name='sklearn_mnist', model_path='outputs/sklearn_mnist_model.pkl')
print(model.name, model.id, model.version, sep='\t')
```

### <a name="register-an-externally-created-model"></a>Inscrire un modèle créé en externe
Vous pouvez inscrire un modèle créé en externe en fournissant un **chemin d’accès local** au modèle. Vous pouvez fournir un dossier ou un seul fichier.

**Exemple ONNX avec le kit SDK Python :**
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

**À l’aide de l’interface CLI**
```azurecli-interactive
az ml model register -n onnx_mnist -p mnist/model.onnx
```

**Durée estimée** : Environ 10 secondes.

Pour plus d'informations, consultez la documentation de référence de la classe [Model](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py).

## <a name="how-to-deploy"></a>Comment déployer

Pour déployer comme un service web, vous devez créer une configuration de l’inférence (`InferenceConfig`) et une configuration de déploiement. Dans la configuration de l’inférence, vous spécifiez les scripts et les dépendances nécessaires pour traiter votre modèle. Dans la configuration de déploiement, vous spécifiez plus d’informations à traiter le modèle de la cible de calcul.


### <a id="script"></a> 1. Définir votre script d’entrée et les dépendances

Le script d’entrée reçoit des données soumises à un service web déployé et les transmet au modèle. Ensuite, il prend la réponse retournée par le modèle et la retourne au client. **Le script est spécifique à votre modèle**; il doit comprendre les données qui le modèle attend et retourne.

Le script contient deux fonctions qui chargent et exécuter le modèle :

* `init()`: en général, cette fonction charge le modèle dans un objet global. Cette fonction est exécutée une seule fois lorsque le conteneur Docker pour votre service web est démarré.

* `run(input_data)`: cette fonction utilise le modèle pour prédire une valeur basée sur les données d'entrée. Les entrées et les sorties de l’exécution utilisent en général JSON pour la sérialisation et la désérialisation. Vous pouvez également utiliser des données binaires brutes. Vous pouvez transformer les données avant de les envoyer au modèle ou avant de les retourner au client.

#### <a name="optional-automatic-swagger-schema-generation"></a>(Facultatif) Génération de schéma Swagger automatique

Pour générer un schéma pour votre service web automatiquement, fournir un exemple de l’entrée et/ou de sortie dans le constructeur pour l’une des objets de type défini et le type et l’exemple sont utilisés pour créer automatiquement le schéma. Service Azure Machine Learning crée ensuite un [OpenAPI](https://swagger.io/docs/specification/about/) spécification (Swagger) pour le service web pendant le déploiement.

Les types suivants sont actuellement pris en charge :

* `pandas`
* `numpy`
* `pyspark`
* objet Python standard

Pour utiliser la génération de schéma, incluez le `inference-schema` package dans votre fichier d’environnement conda. L’exemple suivant utilise `[numpy-support]` étant donné que le script d’entrée utilise un type de paramètre numpy : 

#### <a name="example-dependencies-file"></a>Exemple de fichier de dépendances
Voici un exemple d’un fichier de dépendances Conda pour l’inférence.
```python
name: project_environment
dependencies:
  - python=3.6.2
  - pip:
    - azureml-defaults
    - scikit-learn
    - inference-schema[numpy-support]
```

Si vous souhaitez utiliser la génération de schéma automatique, votre script d’entrée **doit** importer le `inference-schema` packages. 

Définir l’entrée et de sortie des exemples de formats dans le `input_sample` et `output_sample` variables qui représentent les formats de demande et de réponse pour le service web. Utiliser ces exemples dans l’entrée et de sortie des éléments décoratifs de fonction sur le `run()` (fonction). Le scikit-Découvrez l’exemple ci-dessous utilise la génération de schéma.

> [!TIP]
> Après avoir déployé le service, utilisez le `swagger_uri` propriété pour récupérer le document JSON de schéma.

#### <a name="example-entry-script"></a>Exemple de script entrée

L’exemple suivant montre comment accepter et retourner des données JSON :

**Exemple de Scikit-learn avec génération de Swagger :**
```python
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

input_sample = np.array([[10,9,8,7,6,5,4,3,2,1]])
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

Pour plus des exemples de scripts, consultez les exemples suivants :

* Pytorch : [https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-pytorch](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-pytorch)
* TensorFlow : [https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow)
* Keras : [https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras)
* ONNX : [https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx/](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx/)
* Calcul de score par rapport aux données binaires : [Comment utiliser un service web](how-to-consume-web-service.md)

### <a name="2-define-your-inferenceconfig"></a>2. Définir votre InferenceConfig

La configuration de l’inférence décrit comment configurer le modèle pour élaborer des prédictions. L’exemple suivant montre comment créer une configuration de l’inférence :

```python
inference_config = InferenceConfig(source_directory="C:/abc",
                                   runtime= "python",
                                   entry_script="x/y/score.py",
                                   conda_file="env/myenv.yml")
```

Dans cet exemple, la configuration contient les éléments suivants :

* Un répertoire qui contient les ressources nécessaires à l’inférence
* Que ce modèle nécessite Python
* Le [script d’entrée](#script), qui est utilisé pour gérer les requêtes web envoyées au service déployé
* Le fichier conda qui décrit les packages Python nécessaires pour exécuter l’inférence

Pour plus d’informations sur les fonctionnalités de InferenceConfig, consultez le [configuration avancée](#advanced-config) section.

### <a name="3-define-your-deployment-configuration"></a>3. Définir la configuration de votre déploiement

Avant de déployer, vous devez définir la configuration de déploiement. La configuration de déploiement est spécifique à la cible de calcul qui hébergera le service web. Par exemple, lorsque vous déployez localement, vous devez spécifier le port où le service accepte les requêtes.

Vous serez peut-être amené à créer la ressource de calcul. Par exemple, si vous ne le faites pas déjà avez un service Azure Kubernetes Service associé à votre espace de travail.

Le tableau suivant fournit un exemple de création d’une configuration de déploiement pour chaque cible de calcul :

| Cible de calcul | Exemple de configuration de déploiement |
| ----- | ----- |
| Local | `deployment_config = LocalWebservice.deploy_configuration(port=8890)` |
| Azure Container Instance | `deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |
| Azure Kubernetes Service | `deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |

Les sections suivantes expliquent comment créer la configuration de déploiement et puis l’utiliser pour déployer le service web.

## <a name="where-to-deploy"></a>Emplacement de déploiement

### <a id="local"></a> Déployer localement

Les exemples de cette section utilisent [deploy_from_image](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#deploy-from-model-workspace--name--models--image-config--deployment-config-none--deployment-target-none-), ce qui nécessite que vous enregistriez le modèle et l’image avant de procéder à un déploiement. Pour plus d’informations sur les autres méthodes de déploiement, consultez [déployer](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#deploy-workspace--name--model-paths--image-config--deployment-config-none--deployment-target-none-) et [deploy_from_model](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#deploy-from-model-workspace--name--models--image-config--deployment-config-none--deployment-target-none-).

**Pour déployer en local, vous devez avoir Docker installé sur votre ordinateur local.**

**L’utilisation du SDK**

```python
deployment_config = LocalWebservice.deploy_configuration(port=8890)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

**À l’aide de l’interface CLI**

```azurecli-interactive
az ml model deploy -m sklearn_mnist:1 -ic inferenceconfig.json -dc deploymentconfig.json
```

### <a id="aci"></a> Déployer sur Azure Container Instances (DEVTEST)

Utilisez Azure Container Instances pour déployer vos modèles en tant que service web si une ou plusieurs des conditions suivantes sont remplies :
- Vous avez besoin de déployer et de valider rapidement votre modèle.
- Vous testez un modèle en cours de développement. 

Pour obtenir le quota et zone de disponibilité pour ACI, consultez le [Quotas et disponibilité des régions pour Azure Container Instances](https://docs.microsoft.com/azure/container-instances/container-instances-quotas) article.

**L’utilisation du SDK**

```python
deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)
service = Model.deploy(ws, "aciservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

**À l’aide de l’interface CLI**

```azurecli-interactive
az ml model deploy -m sklearn_mnist:1 -n aciservice -ic inferenceconfig.json -dc deploymentconfig.json
```

Pour plus d'informations, consultez la documentation de référence des classes [AciWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py) et [Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice?view=azure-ml-py).

### <a id="aks"></a> Déployer sur Azure Kubernetes Service (PRODUCTION)

Vous pouvez utiliser un cluster AKS existant ou en créer un en utilisant le kit SDK Azure Machine Learning, l’interface CLI ou le portail Azure.


> [!IMPORTANT]
> La création d’un cluster AKS est un processus qui n’est à effectuer qu’une seule fois pour votre espace de travail. Vous pouvez le réutiliser pour vos autres déploiements.
> Si vous n’avez pas créé ou attaché une AKS cluster go <a href="#create-attach-aks">ici</a>.

#### Déployer sur AKS <a id="deploy-aks"></a>

Vous pouvez déployer dans ACS avec l’interface CLI Azure ML :
```azurecli-interactive
az ml model deploy -ct myaks -m mymodel:1 -n aksservice -ic inferenceconfig.json -dc deploymentconfig.json
```

Vous pouvez également utiliser le kit SDK Python :
```python
aks_target = AksCompute(ws,"myaks")
deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)
service = Model.deploy(ws, "aksservice", [model], inference_config, deployment_config, aks_target)
service.wait_for_deployment(show_output = True)
print(service.state)
print(service.get_logs())
```

Pour plus d’informations sur la configuration de votre déploiement AKS, y compris la mise à l’échelle, consultez le [AksWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice) référence.

**Durée estimée :** Environ 5 minutes.

#### Créer ou joindre un cluster AKS <a id="create-attach-aks"></a>
La création ou l’attachement d’un cluster ACS est un **un processus de temps** pour votre espace de travail. Une fois un cluster a été associé à votre espace de travail, vous pouvez l’utiliser pour plusieurs déploiements. 

Si vous supprimez le cluster ou le groupe de ressources qui le contient, vous devez créer un nouveau cluster la prochaine fois que vous devez déployer.

##### <a name="create-a-new-aks-cluster"></a>Créer un cluster AKS
Pour plus d’informations sur la configuration `autoscale_target_utilization`, `autoscale_max_replicas`, et `autoscale_min_replicas`, consultez le [AksWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py#deploy-configuration-autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--primary-key-none--secondary-key-none--tags-none--properties-none--description-none-) référence.
L’exemple suivant montre comment créer un cluster Azure Kubernetes Service :

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Use the default configuration (you can also provide parameters to customize this)
prov_config = AksCompute.provisioning_configuration()

aks_name = 'myaks'
# Create the cluster
aks_target = ComputeTarget.create(workspace = ws,
                                    name = aks_name,
                                    provisioning_configuration = prov_config)

# Wait for the create process to complete
aks_target.wait_for_completion(show_output = True)
```

Pour plus d’informations sur la création d’un cluster AKS en dehors du Kit de développement logiciel Azure Machine Learning, consultez les articles suivants :
* [Créer un cluster AKS](https://docs.microsoft.com/cli/azure/aks?toc=%2Fazure%2Faks%2FTOC.json&bc=%2Fazure%2Fbread%2Ftoc.json&view=azure-cli-latest#az-aks-create)
* [Créer un cluster AKS (portail)](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough-portal?view=azure-cli-latest)


> [!IMPORTANT]
> Pour [`provisioning_configuration()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py), si vous choisissez des valeurs personnalisées pour agent_count et vm_size, vous devez vous assurer que agent_count multiplié par vm_size est supérieur ou égal à 12 processeurs virtuels. Par exemple, si vous définissez vm_size sur « Standard_D3_v2 », qui comporte 4 processeurs virtuels, vous devez définir agent_count sur un nombre supérieur ou égal à 3.

**Durée estimée** : environ 20 minutes.

##### <a name="attach-an-existing-aks-cluster"></a>Joindre un cluster ACS existant

Si vous avez déjà cluster AKS dans votre abonnement Azure, et il s’agit version 1.12. ## et a au moins 12 processeurs virtuels, vous pouvez l’utiliser pour déployer votre image. Le code suivant montre comment attacher un 1.12 AKS existant. ## cluster à votre espace de travail :

```python
from azureml.core.compute import AksCompute, ComputeTarget
# Set the resource group that contains the AKS cluster and the cluster name
resource_group = 'myresourcegroup'
cluster_name = 'mycluster'

# Attach the cluster to your workgroup
attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                         cluster_name = cluster_name)
aks_target = ComputeTarget.attach(ws, 'mycompute', attach_config)
```

## <a name="consume-web-services"></a>Utiliser des services web
Chaque service web déployé fournit une API REST, donc vous pouvez créer des applications clientes dans un large éventail de langages de programmation. Si vous avez activé l’authentification pour votre service, vous devez fournir une clé de service en tant que jeton dans votre en-tête de demande.

Voici un exemple montrant comment appeler votre service dans Python :
```python
import requests
import json

headers = {'Content-Type':'application/json'}

if service.auth_enabled:
    headers['Authorization'] = 'Bearer '+service.get_keys()[0]

print(headers)
    
test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10], 
    [10,9,8,7,6,5,4,3,2,1]
]})

response = requests.post(service.scoring_uri, data=test_sample, headers=headers)
print(response.status_code)
print(response.elapsed)
print(response.json())
```

Pour plus d’informations, consultez [Créer des applications clientes pour utiliser des services web](how-to-consume-web-service.md).

## <a id="update"></a> Mettre à jour le service web

Lorsque vous créez un nouveau modèle, vous devez manuellement mettre à jour chaque service que vous souhaitez utiliser le nouveau modèle. Pour effectuer une mise à jour du service web, utilisez la méthode `update`. Le code suivant montre comment mettre à jour le service web pour utiliser un nouveau modèle :

```python
from azureml.core.webservice import Webservice
from azureml.core.model import Model

# register new model
new_model = Model.register(model_path = "outputs/sklearn_mnist_model.pkl",
                       model_name = "sklearn_mnist",
                       tags = {"key": "0.1"},
                       description = "test",
                       workspace = ws)

service_name = 'myservice'
# Retrieve existing service
service = Webservice(name = service_name, workspace = ws)

# Update to new model(s)
service.update(models = [new_model])
print(service.state)
print(service.get_logs())
```

## <a name="clean-up"></a>Nettoyer
Pour supprimer un service web déployé, utilisez `service.delete()`.
Pour supprimer un modèle inscrit, utilisez `model.delete()`.

Pour plus d’informations, consultez la documentation de référence [WebService.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#delete--), et [Model.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#delete--).

## Paramètres de configuration avancée <a id="advanced-config"></a>

### <a id="customimage"></a> Utiliser une image personnalisée de base

En interne, InferenceConfig crée une image Docker qui contient le modèle et autres ressources requises par le service. Si non spécifié, une image de base par défaut est utilisée.

Lorsque vous créez une image à utiliser avec votre configuration de l’inférence, l’image doit remplir les conditions suivantes :

* Ubuntu 16.04 ou version ultérieure.
* Conda 4.5. # ou supérieur.
* Python 3.5. # ou 3.6. #.

Pour utiliser une image personnalisée, définissez la `base_image` propriété de la configuration de l’inférence à l’adresse de l’image. L’exemple suivant montre comment utiliser une image à partir d’un Registre de conteneurs Azure public et privé :

```python
# use an image available in public Container Registry without authentication
inference_config.base_image = "mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda"

# or, use an image available in a private Container Registry
inference_config.base_image = "myregistry.azurecr.io/mycustomimage:1.0"
inference_config.base_image_registry.address = "myregistry.azurecr.io"
inference_config.base_image_registry.username = "username"
inference_config.base_image_registry.password = "password"
```

L’image suivante URI sont pour les images fournies par Microsoft et peut être utilisé sans fournir une valeur de mot de passe ou nom d’utilisateur :

* `mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda`
* `mcr.microsoft.com/azureml/onnxruntime:v0.4.0`
* `mcr.microsoft.com/azureml/onnxruntime:v0.4.0-cuda10.0-cudnn7`
* `mcr.microsoft.com/azureml/onnxruntime:v0.4.0-tensorrt19.03`

Pour utiliser ces images, définissez le `base_image` à l’URI à partir de la liste ci-dessus. Réglez `base_image_registry.address` sur `mcr.microsoft.com`.

> [!IMPORTANT]
> Les images de Microsoft qui utilisent CUDA ou TensorRT doivent être utilisés uniquement sur les Services Microsoft Azure.

Pour plus d’informations sur le chargement de vos propres images dans Azure Container Registry, consultez [transmettre votre première image vers un Registre de conteneur Docker privé](https://docs.microsoft.com/azure/container-registry/container-registry-get-started-docker-cli).

Si votre modèle est formé sur Azure Machine Learning Compute, à l’aide __version 1.0.22 ou supérieur__ du SDK Azure Machine Learning, une image est créée au cours de formation. L’exemple suivant montre comment utiliser cette image :

```python
# Use an image built during training with SDK 1.0.22 or greater
image_config.base_image = run.properties["AzureML.DerivedImageName"]
```

## <a name="other-inference-options"></a>Autres options d’inférence

### <a id="azuremlcompute"></a> Inférence de lot
Objectifs d’Azure Machine Learning Compute sont créés et gérés par le service Azure Machine Learning. Ils peuvent être utilisés pour la prédiction par lot à partir d’Azure Machine Learning Pipelines.

Pour obtenir une description de l’inférence de traitement par lots avec Azure Machine Learning Compute, lisez le [comment exécuter des prédictions par lot](how-to-run-batch-predictions.md) article.

## <a id="iotedge"></a> Inférence sur IoT Edge
Prise en charge pour le déploiement sur le bord est en version préliminaire. Pour plus d’informations, consultez le [déployer Azure Machine Learning en tant que IoT Edge module](https://docs.microsoft.com/en-us/azure/iot-edge/tutorial-deploy-machine-learning) article.

## <a name="next-steps"></a>Étapes suivantes
* [Résolution des problèmes de déploiement](how-to-troubleshoot-deployment.md)
* [Sécuriser les services web Azure Machine Learning avec SSL](how-to-secure-web-service.md)
* [Utiliser un modèle ML déployé en tant que service web](how-to-consume-web-service.md)
* [Superviser vos modèles Azure Machine Learning avec Application Insights](how-to-enable-app-insights.md)
* [Collecter des données pour des modèles en production](how-to-enable-data-collection.md)
