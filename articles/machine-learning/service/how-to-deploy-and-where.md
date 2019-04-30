---
title: Comment et où déployer les modèles
titleSuffix: Azure Machine Learning service
description: 'Découvrez comment et où déployer vos modèles de service Azure Machine Learning, notamment : Azure Container Instances, Azure Kubernetes Service, Azure IoT Edge et FPGA (Field-Programmable Gate Arrays).'
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 04/02/2019
ms.custom: seoapril2019
ms.openlocfilehash: a6ef53d56fa293791658b37b16cbaff94aee6ef3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60819847"
---
# <a name="deploy-models-with-the-azure-machine-learning-service"></a>Déployer des modèles avec le service Azure Machine Learning

Dans ce document, découvrez comment déployer votre modèle en tant que service web dans le cloud Azure ou sur des appareils IoT Edge. 

## <a name="compute-targets-for-deployment"></a>Cibles de calcul pour le déploiement

Utilisez le Kit de développement logiciel Azure Machine Learning pour déployer votre modèle formé aux emplacements suivants :

| Cible de calcul | Type de déploiement | Description |
| ----- | ----- | ----- |
| [Azure Kubernetes Service (AKS)](#aks) | Inférence en temps réel | Convient aux déploiements de production à grande échelle. Fournit la mise à l’échelle automatique et des temps de réponse rapides. |
| [Azure Machine Learning Compute (amlcompute)](#azuremlcompute) | Inférence de lot | Exécutez prédiction par lot sur le calcul sans serveur. Prend en charge des machines virtuelles normale et basse priorité. |
| [Azure Container Instances (ACI)](#aci) | Test | Convient pour le développement ou le test. **Ne convient pas les charges de production.** |
| [Azure IoT Edge](#iotedge) | (Version préliminaire) Module IoT | Déploiement de modèles sur des appareils IoT. Une inférence se produit sur l’appareil. |
| [FPGA (Field-Programmable Gate Array)](#fpga) | (Version préliminaire) Service Web | Latence ultra-faible pour l'inférence en temps réel. |

## <a name="deployment-workflow"></a>Workflow du déploiement

Le processus de déploiement d'un modèle est similaire pour toutes les cibles de calcul :

1. Entraînez et inscrivez un modèle.
1. Configurez et inscrivez une image qui utilise le modèle.
1. Déployez l'image sur une cible de calcul.
1. test du déploiement

La vidéo suivante montre le déploiement sur Azure Container Instances :

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2Kwk3]


Pour plus d’informations sur les concepts impliqués dans le workflow de déploiement, consultez [Déployer, gérer et surveiller des modèles avec le service Azure Machine Learning](concept-model-management-and-deployment.md).

## <a name="prerequisites-for-deployment"></a>Conditions préalables pour le déploiement

[!INCLUDE [aml-prereq](../../../includes/aml-prereq.md)]

- Un modèle entraîné. Si vous n'avez pas de modèle formé, suivez la procédure du tutoriel [​​Former des modèles](tutorial-train-models-with-aml.md) pour en former et en inscrire un auprès du service Azure Machine Learning.

    > [!NOTE]
    > Bien que le service Azure Machine Learning peut fonctionner avec n’importe quel modèle générique qui peut être chargé dans Python 3, les exemples de ce document montrent à l’aide d’un modèle stocké au format pickle de Python.
    >
    > Pour plus d'informations sur l'utilisation des modèles ONNX, consultez le document [ONNX et Azure Machine Learning](how-to-build-deploy-onnx.md).

## <a id="registermodel"></a> Inscrire un modèle entraîné

Le registre de modèle est un moyen de stocker et d’organiser vos modèles entraînés dans le cloud Azure. Les modèles sont inscrits dans votre espace de travail de service Azure Machine Learning. Le modèle peut être formé à l'aide d'Azure Machine Learning ou d'un autre service. Le code suivant montre comment inscrire un modèle à partir du fichier, définissez un nom, balises et une description :

```python
from azureml.core.model import Model

model = Model.register(model_path = "outputs/sklearn_mnist_model.pkl",
                       model_name = "sklearn_mnist",
                       tags = {"key": "0.1"},
                       description = "test",
                       workspace = ws)
```

**Durée estimée** : Environ 10 secondes.

Pour obtenir un exemple d’inscription d’un modèle, consultez [former un classifieur d’images](tutorial-train-models-with-aml.md).

Pour plus d'informations, consultez la documentation de référence de la classe [Model](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py).

## <a id="configureimage"></a> Créer et inscrire une image

Les modèles déployés sont empaquetés sous forme d'image. L'image contient les dépendances nécessaires à l'exécution du modèle.

Pour les déploiements **Azure Container Instances**, **Azure Kubernetes Service** et **Azure IoT Edge**, la classe [azureml.core.image.ContainerImage](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.containerimage?view=azure-ml-py) permet de créer une configuration d’image. La configuration d'image est ensuite utilisée pour créer une image Docker.

Lorsque vous créez la configuration de l’image, vous pouvez utiliser un __image par défaut__ fournies par le service Azure Machine Learning ou un __image personnalisée__ que vous fournissez.

Le code suivant montre comment créer une configuration d'image :

```python
from azureml.core.image import ContainerImage

# Image configuration
image_config = ContainerImage.image_configuration(execution_script = "score.py",
                                                 runtime = "python",
                                                 conda_file = "myenv.yml"}
                                                 )
```

**Durée estimée** : Environ 10 secondes.

Les paramètres importants de cet exemple sont décrits dans le tableau suivant :

| Paramètre | Description |
| ----- | ----- |
| `execution_script` | Spécifie un script Python qui est utilisé pour recevoir les demandes soumises au service. Dans cet exemple, le script se trouve dans le fichier `score.py`. Pour plus d’informations, consultez la section [Script d’exécution](#script). |
| `runtime` | Indique que l’image utilise Python. L’autre option est `spark-py`, qui utilise Python avec Apache Spark. |
| `conda_file` | Permet de fournir un fichier d’environnement conda. Ce fichier définit l'environnement conda du modèle déployé. Pour plus d'informations sur la création de ce fichier, consultez [Créer un fichier d'environnement (myenv.yml)](tutorial-deploy-models-with-aml.md#create-environment-file). |

Pour obtenir un exemple de création d’une configuration d’une image, consultez [déployer un classifieur d’images](tutorial-deploy-models-with-aml.md).

Pour plus d'informations, consultez la documentation de référence de la classe [ContainerImage](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.containerimage?view=azure-ml-py).

### <a id="customimage"></a> Utiliser une image personnalisée

Lorsque vous utilisez une image personnalisée, l’image doit remplir les conditions suivantes :

* Ubuntu 16.04 ou version ultérieure.
* Conda 4.5. # ou supérieur.
* Python 3.5. # ou 3.6. #.

Pour utiliser une image personnalisée, définissez la `base_image` propriété de la configuration de l’image à l’adresse de l’image. L’exemple suivant montre comment utiliser une image à partir d’un Registre de conteneurs Azure public et privé :

```python
# use an image available in public Container Registry without authentication
image_config.base_image = "mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda"

# or, use an image available in a private Container Registry
image_config.base_image = "myregistry.azurecr.io/mycustomimage:1.0"
image_config.base_image_registry.address = "myregistry.azurecr.io"
image_config.base_image_registry.username = "username"
image_config.base_image_registry.password = "password"
```

Pour plus d’informations sur le téléchargement d’images dans Azure Container Registry, consultez [transmettre votre première image vers un Registre de conteneur Docker privé](https://docs.microsoft.com/azure/container-registry/container-registry-get-started-docker-cli).

Si votre modèle est formé sur Azure Machine Learning Compute, à l’aide __version 1.0.22 ou supérieur__ du SDK Azure Machine Learning, une image est créée au cours de formation. L’exemple suivant montre comment utiliser cette image :

```python
# Use an image built during training with SDK 1.0.22 or greater
image_config.base_image = run.properties["AzureML.DerivedImageName"]
```

### <a id="script"></a> Script d’exécution

Le script d’exécution reçoit des données soumises à une image déployée et les transmet au modèle. Ensuite, il prend la réponse retournée par le modèle et la retourne au client. **Le script est spécifique à votre modèle**; il doit comprendre les données qui le modèle attend et retourne. Pour un exemple de script qui fonctionne avec un modèle de classification d’images, consultez [déployer un classifieur d’images](tutorial-deploy-models-with-aml.md).

Le script contient deux fonctions qui chargent et exécuter le modèle :

* `init()`: en général, cette fonction charge le modèle dans un objet global. Cette fonction est exécutée une seule fois lors du démarrage du conteneur Docker.

* `run(input_data)`: cette fonction utilise le modèle pour prédire une valeur basée sur les données d'entrée. Les entrées et les sorties de l’exécution utilisent en général JSON pour la sérialisation et la désérialisation. Vous pouvez également utiliser des données binaires brutes. Vous pouvez transformer les données avant de les envoyer au modèle ou avant de les retourner au client.

#### <a name="working-with-json-data"></a>Utilisation de données JSON

L’exemple de script suivant accepte et retourne des données JSON. La fonction `run` transforme les données à partir du format JSON en un format que le modèle attend, puis transforme la réponse au format JSON avant de les retourner :

```python
%%writefile score.py
import json
import numpy as np
import os
import pickle
from sklearn.externals import joblib
from sklearn.linear_model import LogisticRegression
from azureml.core.model import Model

# load the model
def init():
    global model
    # retrieve the path to the model file using the model name
    model_path = Model.get_model_path('sklearn_mnist')
    model = joblib.load(model_path)

# Passes data to the model and returns the prediction
def run(raw_data):
    data = np.array(json.loads(raw_data)['data'])
    # make prediction
    y_hat = model.predict(data)
    return json.dumps(y_hat.tolist())
```

#### <a name="working-with-binary-data"></a>Utilisation de données binaires

Si votre modèle accepte les __données binaires__, utilisez `AMLRequest`, `AMLResponse` et `rawhttp`. L’exemple de script suivant accepte des données binaires et retourne les octets inversés pour les requêtes POST. Pour les requêtes GET, il retourne l’URL complète dans le corps de réponse :

```python
from azureml.contrib.services.aml_request  import AMLRequest, rawhttp
from azureml.contrib.services.aml_response import AMLResponse

def init():
    print("This is init()")

# Accept and return binary data
@rawhttp
def run(request):
    print("This is run()")
    print("Request: [{0}]".format(request))
    # handle GET requests
    if request.method == 'GET':
        respBody = str.encode(request.full_path)
        return AMLResponse(respBody, 200)
    # handle POST requests
    elif request.method == 'POST':
        reqBody = request.get_data(False)
        respBody = bytearray(reqBody)
        respBody.reverse()
        respBody = bytes(respBody)
        return AMLResponse(respBody, 200)
    else:
        return AMLResponse("bad request", 500)
```

> [!IMPORTANT]
> L’espace de noms `azureml.contrib` change fréquemment car nous travaillons à améliorer le service. Par conséquent, tout ce qui est compris dans cet espace de noms doit être considéré comme une préversion et n’est pas entièrement pris en charge par Microsoft.
>
> Si vous avez besoin d’effectuer ce test sur votre environnement de développement local, vous pouvez installer les composants dans l’espace de noms `contrib` à l’aide de la commande suivante :
> ```shell
> pip install azureml-contrib-services
> ```

### <a id="createimage"></a> Inscrire l’image

Une fois la configuration d’image créée, vous pouvez l’utiliser pour inscrire une image. Cette image est stockée dans le registre de conteneurs de votre espace de travail. Une fois l'image créée, vous pouvez la déployer sur plusieurs services.

```python
# Register the image from the image configuration
image = ContainerImage.create(name = "myimage",
                              models = [model], #this is the model object
                              image_config = image_config,
                              workspace = ws
                              )
```

**Durée estimée** : environ trois minutes.

Des versions sont automatiquement attribuées aux images lorsque vous en inscrivez plusieurs avec le même nom. Par exemple, la première image inscrite sous le nom `myimage` reçoit l'ID `myimage:1`. La prochaine fois que vous inscrirez une image sous le nom `myimage`, l'ID de la nouvelle image sera `myimage:2`.

Pour plus d'informations, consultez la documentation de référence de la classe [ContainerImage](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.containerimage?view=azure-ml-py).

## <a id="deploy"></a> Déployer comme un service web

Le processus de déploiement varie légèrement en fonction de la cible de calcul. Utilisez les informations des sections suivantes pour apprendre à effectuer un déploiement sur :

| Cible de calcul | Type de déploiement | Description |
| ----- | ----- | ----- |
| [Azure Kubernetes Service (AKS)](#aks) | Service Web (inférence en temps réel)| Convient aux déploiements de production à grande échelle. Fournit la mise à l’échelle automatique et des temps de réponse rapides. |
| [Azure ML Compute](#azuremlcompute) | Service Web (l’inférence de lot)| Exécutez prédiction par lot sur le calcul sans serveur. Prend en charge des machines virtuelles normale et basse priorité. |
| [Azure Container Instances (ACI)](#aci) | Service Web (développement/test)| Convient pour le développement ou le test. **Ne convient pas les charges de production.** |
| [Azure IoT Edge](#iotedge) | (Version préliminaire) Module IoT | Déploiement de modèles sur des appareils IoT. Une inférence se produit sur l’appareil. |
| [FPGA (Field-Programmable Gate Array)](#fpga) | (Version préliminaire) Service Web | Latence ultra-faible pour l'inférence en temps réel. |

> [!IMPORTANT]
> Le partage des ressources cross-origin (CORS) n’est pas pris en charge pendant le déploiement d’un modèle en tant que service web.

Les exemples de cette section utilisent [deploy_from_image](https://docs.microsoft.com/en-us/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#deploy-from-model-workspace--name--models--image-config--deployment-config-none--deployment-target-none-), ce qui nécessite que vous enregistriez le modèle et l’image avant de procéder à un déploiement. Pour plus d’informations sur les autres méthodes de déploiement, consultez [déployer](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#deploy-workspace--name--model-paths--image-config--deployment-config-none--deployment-target-none-) et [deploy_from_model](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#deploy-from-model-workspace--name--models--image-config--deployment-config-none--deployment-target-none-).

### <a id="aci"></a> Déployer sur Azure Container Instances (DEVTEST)

Utilisez Azure Container Instances pour déployer vos modèles en tant que service web si une ou plusieurs des conditions suivantes sont remplies :

- Vous avez besoin de déployer et de valider rapidement votre modèle. Le déploiement ACI se termine en moins de 5 minutes.
- Vous testez un modèle en cours de développement. Pour plus d'informations sur les quotas et la disponibilité d'ACI en fonction des régions, consultez le document [Disponibilité des régions et quotas pour Azure Container Instances](https://docs.microsoft.com/azure/container-instances/container-instances-quotas).

Pour effectuer un déploiement sur Azure Container Instances, procédez comme suit :

1. Définissez la configuration du déploiement. Cette configuration dépend de la configuration requise de votre modèle. L'exemple suivant définit une configuration qui utilise un seul cœur de processeur et 1 Go de mémoire :

    [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-deploy-to-aci/how-to-deploy-to-aci.py?name=configAci)]

2. Pour déployer l'image créée dans la section [Créer l'image](#createimage) de ce document, utilisez le code suivant :

    [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-deploy-to-aci/how-to-deploy-to-aci.py?name=option3Deploy)]

    **Durée estimée** : Environ 5 minutes.

Pour plus d'informations, consultez la documentation de référence des classes [AciWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py) et [Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice?view=azure-ml-py).

### <a id="aks"></a> Déployer sur Azure Kubernetes Service (PRODUCTION)

Pour déployer votre modèle en tant que service web de production à grande échelle, utilisez Azure Kubernetes Service (AKS). Vous pouvez utiliser un cluster AKS existant ou en créer un en utilisant le kit SDK Azure Machine Learning, l’interface CLI ou le portail Azure.

La création d’un cluster AKS est un processus qui n’est à effectuer qu’une seule fois pour votre espace de travail. Vous pouvez le réutiliser pour vos autres déploiements.

> [!IMPORTANT]
> Si vous supprimez le cluster, vous devrez créer un cluster lors du prochain déploiement.

Azure Kubernetes Service offre les fonctionnalités suivantes :

* Mise à l’échelle automatique
* Journalisation
* Collection de données de modèle
* Temps de réponse rapides pour vos services web
* Arrêt TLS
* Authentification

#### <a name="autoscaling"></a>Mise à l’échelle automatique

Mise à l’échelle peut être contrôlé en définissant `autoscale_target_utilization`, `autoscale_min_replicas`, et `autoscale_max_replicas` pour l’ACS service web. L’exemple suivant montre comment activer la mise à l’échelle :

```python
aks_config = AksWebservice.deploy_configuration(autoscale_enabled=True,
                                                autoscale_target_utilization=30,
                                                autoscale_min_replicas=1,
                                                autoscale_max_replicas=4)
```

Décisions relatives à l’échelle/repose sur l’utilisation des réplicas de conteneur actuel. Le nombre de réplicas qui sont occupés à (traiter une demande) divisé par le total nombre de réplicas en cours est l’utilisation actuelle. Si ce nombre est supérieur à l’utilisation de la cible, plusieurs réplicas sont créés. S’il est inférieur, les réplicas sont réduits. Par défaut, l’utilisation de la cible est de 70 %.

Décisions pour ajouter des réplicas sont effectuées et mises en œuvre rapidement (environ 1 seconde). Décisions pour supprimer les réplicas prennent plus de temps (environ 1 minute). Ce comportement conserve des réplicas inactifs autour d’une minute en cas de nouvelles demandes arrivent qu’elles peuvent traiter.

Vous pouvez calculer les réplicas requis en utilisant le code suivant :

```python
from math import ceil
# target requests per second
targetRps = 20
# time to process the request (in seconds)
reqTime = 10
# Maximum requests per container
maxReqPerContainer = 1
# target_utilization. 70% in this example
targetUtilization = .7

concurrentRequests = targetRps * reqTime / targetUtilization

# Number of container replicas
replicas = ceil(concurrentRequests / maxReqPerContainer)
```

Pour plus d’informations sur la configuration `autoscale_target_utilization`, `autoscale_max_replicas`, et `autoscale_min_replicas`, consultez le [AksWebservice.deploy_configuration](https://docs.microsoft.com/en-us/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py#deploy-configuration-autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--primary-key-none--secondary-key-none--tags-none--properties-none--description-none-) référence.

#### <a name="create-a-new-cluster"></a>Créer un cluster

Pour créer un cluster Azure Kubernetes Service, utilisez le code suivant :

> [!IMPORTANT]
> La création du cluster AKS est un processus qui n’est à effectuer qu’une seule fois pour votre espace de travail. Une fois créé, vous pouvez le réutiliser pour vos autres déploiements. Si vous supprimez le cluster ou le groupe de ressources dans lequel il se trouve, vous devrez créer un cluster lors du prochain déploiement.
> Pour [`provisioning_configuration()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py), si vous choisissez des valeurs personnalisées pour agent_count et vm_size, vous devez vous assurer que agent_count multiplié par vm_size est supérieur ou égal à 12 processeurs virtuels. Par exemple, si vous définissez vm_size sur « Standard_D3_v2 », qui comporte 4 processeurs virtuels, vous devez définir agent_count sur un nombre supérieur ou égal à 3.

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Use the default configuration (you can also provide parameters to customize this)
prov_config = AksCompute.provisioning_configuration()

aks_name = 'aml-aks-1'
# Create the cluster
aks_target = ComputeTarget.create(workspace = ws,
                                    name = aks_name,
                                    provisioning_configuration = prov_config)

# Wait for the create process to complete
aks_target.wait_for_completion(show_output = True)
print(aks_target.provisioning_state)
print(aks_target.provisioning_errors)
```

**Durée estimée** : environ 20 minutes.

#### <a name="use-an-existing-cluster"></a>Utiliser un cluster existant

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

# Wait for the operation to complete
aks_target.wait_for_completion(True)
```

**Durée estimée** : environ trois minutes.

Pour plus d’informations sur la création d’un cluster AKS en dehors du Kit de développement logiciel Azure Machine Learning, consultez les articles suivants :

* [Créer un cluster AKS](https://docs.microsoft.com/cli/azure/aks?toc=%2Fen-us%2Fazure%2Faks%2FTOC.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json&view=azure-cli-latest#az-aks-create)
* [Créer un cluster AKS (portail)](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough-portal?view=azure-cli-latest)

#### <a name="deploy-the-image"></a>Déployer l'image

Pour déployer sur le cluster Azure Kubernetes Service l’image créée dans la section [Créer l’image](#createimage) de ce document, utilisez le code suivant :

```python
from azureml.core.webservice import Webservice, AksWebservice

# Set configuration and service name
aks_config = AksWebservice.deploy_configuration()
aks_service_name ='aks-service-1'
# Deploy from image
service = Webservice.deploy_from_image(workspace = ws,
                                            name = aks_service_name,
                                            image = image,
                                            deployment_config = aks_config,
                                            deployment_target = aks_target)
# Wait for the deployment to complete
service.wait_for_deployment(show_output = True)
print(service.state)
```

**Durée estimée** : environ trois minutes.

Pour plus d'informations, consultez la documentation de référence des classes [AksWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py) et [Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice.webservice?view=azure-ml-py).

### <a id="azuremlcompute"></a> Inférence avec Azure ML Compute

Cibles de calcul Azure ML sont créés et gérés par le service Azure Machine Learning. Ils peuvent être utilisés pour la prédiction par lot à partir d’Azure ML Pipelines.

Pour obtenir une description de l’inférence de traitement par lots avec Azure ML Compute, lisez le [comment exécuter des prédictions par lot](how-to-run-batch-predictions.md) document.


### <a id="fpga"></a> Procéder à un déploiement sur FPGA (Field-Programmable Gate Arrays)

Project Brainwave permet d'atteindre une très faible latence pour les requêtes d'inférence en temps réel. Project Brainwave accélère les réseaux neuronaux profonds déployés sur des tableaux FPGA dans le cloud Azure. Les réseaux neuronaux profonds fréquemment utilisés sont disponibles en tant que caractériseurs pour l’apprentissage par transfert ou personnalisables avec des pondérations entraînées à partir de vos propres données.

Pour déployer un modèle à l'aide de Project Brainwave, consultez le document [Procéder à un déploiement sur FPGA (Field-Programmable Gate Arrays)](how-to-deploy-fpga-web-service.md).

## <a name="define-schema"></a>Définir un schéma

Éléments décoratifs personnalisés peuvent être utilisées pour [OpenAPI](https://swagger.io/docs/specification/about/) entrées et génération de la spécification du type manipulation lors du déploiement du service web. Dans le `score.py` fichier, de vous fournir un exemple de l’entrée et/ou de sortie dans le constructeur pour un des objets de type défini et de l’exemple et type sont utilisés pour créer automatiquement le schéma. Les types suivants sont actuellement pris en charge :

* `pandas`
* `numpy`
* `pyspark`
* Python standard

Vérifiez d’abord les dépendances nécessaires pour le `inference-schema` package sont inclus dans votre `env.yml` fichier d’environnement conda. Cet exemple utilise le `numpy` de type de paramètre pour le schéma, par conséquent, le pip supplémentaire `[numpy-support]` est également installé.

```python
%%writefile myenv.yml
name: project_environment
dependencies:
  - python=3.6.2
  - pip:
    - azureml-defaults
    - scikit-learn
    - inference-schema[numpy-support]
```

Ensuite, modifiez le `score.py` fichier à importer le `inference-schema` packages. Définir l’entrée et de sortie des exemples de formats dans le `input_sample` et `output_sample` variables qui représentent les formats de demande et de réponse pour le service web. Utiliser ces exemples dans l’entrée et de sortie des éléments décoratifs de fonction sur le `run()` (fonction).

```python
%%writefile score.py
import json
import numpy as np
import os
import pickle
from sklearn.externals import joblib
from sklearn.linear_model import LogisticRegression
from azureml.core.model import Model

from inference_schema.schema_decorators import input_schema, output_schema
from inference_schema.parameter_types.numpy_parameter_type import NumpyParameterType


def init():
    global model
    model_path = Model.get_model_path('sklearn_mnist')
    model = joblib.load(model_path)


input_sample = np.array([[1.8]])
output_sample = np.array([43638.88])

@input_schema('data', NumpyParameterType(input_sample))
@output_schema(NumpyParameterType(output_sample))
def run(raw_data):
    data = np.array(json.loads(raw_data)['data'])
    y_hat = model.predict(data)
    return json.dumps(y_hat.tolist())
```

Après avoir appliqué l’image normale d’inscription et web service processus de déploiement avec la mise à jour `score.py` de fichier, de récupérer l’uri de Swagger à partir du service. Demande de cet uri retournera le `swagger.json` fichier.

```python
service.wait_for_deployment(show_output=True)
print(service.swagger_uri)
```



Lorsque vous créez une image, vous devez manuellement mettre à jour chacun des services pour lesquels vous souhaitez utiliser la nouvelle image. Pour effectuer une mise à jour du service web, utilisez la méthode `update`. Le code suivant montre comment effectuer une mise à jour du service web pour utiliser une nouvelle image :

```python
from azureml.core.webservice import Webservice
from azureml.core.image import Image

service_name = 'aci-mnist-3'
# Retrieve existing service
service = Webservice(name = service_name, workspace = ws)

# point to a different image
new_image = Image(workspace = ws, id="myimage2:1")

# Update the image used by the service
service.update(image = new_image)
print(service.state)
```

Pour plus d’informations, consultez la documentation de référence de la classe [Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py).

## <a name="test-web-service-deployments"></a>Tester les déploiements de service web

Pour tester un déploiement de service web, vous pouvez utiliser la méthode `run` de l'objet Webservice. Dans l'exemple suivant, un document JSON est défini sur un service web et le résultat est affiché. Les données envoyées doivent correspondre aux attentes du modèle. Dans cet exemple, le format de données correspond à l'entrée attendue par le modèle diabetes.

```python
import json

test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10],
    [10,9,8,7,6,5,4,3,2,1]
]})
test_sample = bytes(test_sample,encoding = 'utf8')

prediction = service.run(input_data = test_sample)
print(prediction)
```

Le service web étant une API REST, vous pouvez créer des applications clientes dans un large éventail de langages de programmation. Pour plus d’informations, consultez [Créer des applications clientes pour utiliser des services web](how-to-consume-web-service.md).

## <a id="update"></a> Mettre à jour le service web

Lorsque vous créez une image, vous devez manuellement mettre à jour chacun des services pour lesquels vous souhaitez utiliser la nouvelle image. Pour effectuer une mise à jour du service web, utilisez la méthode `update`. Le code suivant montre comment effectuer une mise à jour du service web pour utiliser une nouvelle image :

```python
from azureml.core.webservice import Webservice
from azureml.core.image import Image

service_name = 'aci-mnist-3'
# Retrieve existing service
service = Webservice(name = service_name, workspace = ws)

# point to a different image
new_image = Image(workspace = ws, id="myimage2:1")

# Update the image used by the service
service.update(image = new_image)
print(service.state)
```

Pour plus d’informations, consultez la documentation de référence de la classe [Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py).

## <a id="iotedge"></a> Procéder à un déploiement sur Azure IoT Edge

Un appareil Azure IoT Edge est un appareil Linux ou Windows qui exécute le runtime Azure IoT Edge. À l’aide d’Azure IoT Hub, vous pouvez déployer des modèles d’apprentissage automatique sur ces appareils en tant que modules IoT Edge. Le déploiement d’un modèle sur un appareil IoT Edge permet à celui-ci d’utiliser le modèle directement, au lieu d’avoir à envoyer des données dans le cloud en vue de leur traitement. Les temps de réponse sont alors plus rapides et les transferts de données moins nombreux.

Les modules Azure IoT Edge sont déployés sur votre appareil à partir d'un registre de conteneurs. Lorsque vous créez une image à partir de votre modèle, elle est stockée dans le registre de conteneurs de votre espace de travail.

> [!IMPORTANT]
> Les informations contenues dans cette section part du principe que vous êtes déjà familiarisé avec les modules Azure IoT Hub et Azure IoT Edge. Bien que certaines informations dans cette section est spécifique au service Azure Machine Learning, la majorité du processus de déploiement sur un appareil edge se produit dans le service Azure IoT.
>
> Si vous n’êtes pas familiarisé avec Azure IoT, consultez [notions de base Azure IoT](https://docs.microsoft.com/azure/iot-fundamentals/) et [Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/) pour des informations de base. Utilisez ensuite les autres liens de cette section pour en savoir plus sur les opérations spécifiques.

### <a name="set-up-your-environment"></a>Configurer votre environnement

* Un environnement de développement. Pour plus d’informations, consultez le document [Guide pratique pour configurer un environnement de développement](how-to-configure-environment.md).

* Un [hub Azure IoT](../../iot-hub/iot-hub-create-through-portal.md) dans votre abonnement Azure.

* Un modèle entraîné. Pour obtenir un exemple d’entraînement de modèle, consultez le document [Entraîner un modèle de classification d’images avec Azure Machine Learning](tutorial-train-models-with-aml.md). Un modèle préentraîné est disponible sur le [dépôt GitHub AI Toolkit pour Azure IoT Edge](https://github.com/Azure/ai-toolkit-iot-edge/tree/master/IoT%20Edge%20anomaly%20detection%20tutorial).

### <a id="getcontainer"></a> Obtenir des informations d’identification de Registre de conteneur

Pour déployer un module IoT Edge sur votre appareil, Azure IoT a besoin des informations d'identification du registre de conteneurs dans lequel le service Azure Machine Learning stocke les images Docker.

Vous pouvez obtenir les informations d’identification de deux manières :

+ **À partir du portail Azure** :

  1. Connectez-vous au [Portail Azure](https://portal.azure.com/signin/index).

  1. Accédez à votre espace de travail du service Azure Machine Learning, puis sélectionnez __Vue d’ensemble__. Pour accéder aux paramètres du registre de conteneurs, sélectionnez le lien __Registre__.

     ![Image de l’entrée du registre de conteneurs](./media/how-to-deploy-and-where/findregisteredcontainer.png)

  1. Une fois dans le registre de conteneurs, sélectionnez **Clés d’accès**, puis activez l’utilisateur administrateur.

     ![Image de l’écran Clés d’accès](./media/how-to-deploy-and-where/findaccesskey.png)

  1. Enregistrez les valeurs du **serveur de connexion**, du **nom d'utilisateur** et du **mot de passe**.

+ **Avec un script Python** :

  1. Utilisez le script Python suivant après le code que vous avez exécuté précédemment pour créer un conteneur :

     ```python
     # Getting your container details
     container_reg = ws.get_details()["containerRegistry"]
     reg_name=container_reg.split("/")[-1]
     container_url = "\"" + image.image_location + "\","
     subscription_id = ws.subscription_id
     from azure.mgmt.containerregistry import ContainerRegistryManagementClient
     from azure.mgmt import containerregistry
     client = ContainerRegistryManagementClient(ws._auth,subscription_id)
     result= client.registries.list_credentials(resource_group_name, reg_name, custom_headers=None, raw=False)
     username = result.username
     password = result.passwords[0].value
     print('ContainerURL{}'.format(image.image_location))
     print('Servername: {}'.format(reg_name))
     print('Username: {}'.format(username))
     print('Password: {}'.format(password))
     ```
  1. Enregistrez les valeurs de l'URL du conteneur, du nom du serveur, du nom d'utilisateur et du mot de passe.

     Ces informations d'identification sont nécessaires pour permettre à l'appareil IoT Edge d'accéder aux images de votre registre de conteneurs privé.

### <a name="prepare-the-iot-device"></a>Préparer l’appareil IoT

Inscrire votre appareil avec Azure IoT Hub et puis installer le runtime IoT Edge sur l’appareil. Si vous n’êtes pas familiarisé avec ce processus, consultez [Guide de démarrage rapide : Déployer votre premier module IoT Edge sur un appareil Linux x64](../../iot-edge/quickstart-linux.md).

Autres méthodes d’inscription d’un appareil sont :

* [Portail Azure](https://docs.microsoft.com/azure/iot-edge/how-to-register-device-portal)
* [Interface de ligne de commande Azure](https://docs.microsoft.com/azure/iot-edge/how-to-register-device-cli)
* [Visual Studio Code](https://docs.microsoft.com/azure/iot-edge/how-to-register-device-vscode)

### <a name="deploy-the-model-to-the-device"></a>Déployer le modèle sur l'appareil

Pour déployer le modèle sur l’appareil, utilisez les informations de Registre recueillies le [obtenir des informations d’identification du Registre de conteneur](#getcontainer) section avec le déploiement de module les étapes pour les modules IoT Edge. Par exemple, lorsque [modules de déploiement Azure IoT Edge à partir du portail Azure](../../iot-edge/how-to-deploy-modules-portal.md), vous devez configurer le __paramètres du Registre__ pour l’appareil. Utilisez le __serveur de connexion__, __nom d’utilisateur__, et __mot de passe__ pour votre Registre de conteneurs d’espace de travail.

Vous pouvez également déployer à l’aide de [Azure CLI](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-modules-cli) et [Visual Studio Code](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-modules-vscode).

## <a name="clean-up"></a>Nettoyer

Pour supprimer un service web déployé, utilisez `service.delete()`.

Pour supprimer une image, utilisez `image.delete()`.

Pour supprimer un modèle inscrit, utilisez `model.delete()`.

Pour plus d’informations, consultez la documentation de référence de [WebService.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#delete--), [Image.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.image(class)?view=azure-ml-py#delete--) et [Model.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#delete--).

## <a name="next-steps"></a>Étapes suivantes

* [Résolution des problèmes de déploiement](how-to-troubleshoot-deployment.md)
* [Sécuriser les services web Azure Machine Learning avec SSL](how-to-secure-web-service.md)
* [Utiliser un modèle ML déployé en tant que service web](how-to-consume-web-service.md)
* [Exécuter des prédictions par lots](how-to-run-batch-predictions.md)
* [Superviser vos modèles Azure Machine Learning avec Application Insights](how-to-enable-app-insights.md)
* [Collecter des données pour des modèles en production](how-to-enable-data-collection.md)
* [SDK du service Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
* [Utiliser Azure Machine Learning service avec des réseaux virtuels Azure](how-to-enable-virtual-network.md)
* [Bonnes pratiques pour la création de systèmes de recommandation](https://github.com/Microsoft/Recommenders)
* [Générer une API de recommandation en temps réel sur Azure](https://docs.microsoft.com/azure/architecture/reference-architectures/ai/real-time-recommendation)
