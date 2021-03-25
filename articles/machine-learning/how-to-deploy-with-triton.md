---
title: Modèle de hautes performances avec Triton (préversion)
titleSuffix: Azure Machine Learning
description: Apprenez à déployer votre modèle à l’aide du logiciel NVIDIA Triton Inference Server dans Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: gopalv
author: gvashishtha
ms.date: 02/16/2020
ms.topic: conceptual
ms.reviewer: larryfr
ms.custom: deploy
ms.openlocfilehash: 2966b685e1904102467bf16994ea781556544047
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102519195"
---
# <a name="high-performance-serving-with-triton-inference-server-preview"></a>Haute performance avec un serveur Triton Inference (préversion) 

Découvrez comment utiliser [le serveur Triton Inference NVIDIA](https://aka.ms/nvidia-triton-docs) pour améliorer les performances du service Web utilisé pour l’inférence de modèle.

Déployer un modèle pour une inférence peut se faire comme un service Web. Par exemple, un déploiement vers Azure Container Instances et Azure Kubernetes Service. Par défaut, Azure Machine Learning utilise une infrastructure Web à thread unique, *à usage général* pour les déploiements de service Web.

Triton est une infrastructure *optimisée pour l’inférence*. Elle offre une meilleure utilisation des GPU et une inférence plus économique. Côté serveur, elle traite par lots les requêtes entrantes et envoie ces lots pour l’inférence. Le traitement par lot mieux fait appel aux ressources GPU et constitue un élément clé des performances de Triton.

> [!IMPORTANT]
> L’utilisation de Triton pour le déploiement à partir de Azure Machine Learning est actuellement en __préversion__. Les fonctions en préversion peuvent ne pas être couvertes par le service client. Pour plus d’informations, consulter [Conditions d’utilisation supplémentaires des Préversions Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

> [!TIP]
> Les extraits de code de ce document sont fournis à titre d’illustration et n’affichent pas une solution complète. Pour obtenir un exemple de code fonctionnel, consultez les [exemples de bout en bout de Triton dans Azure Machine Learning](https://aka.ms/triton-aml-sample).

## <a name="prerequisites"></a>Prérequis

* Un **abonnement Azure**. Si vous n’en avez pas, essayez la [version gratuite ou payante d’Azure Machine Learning](https://aka.ms/AMLFree).
* Vous savez [comment et où déployer un modèle](how-to-deploy-and-where.md) avec Azure Machine Learning.
* Le [Kit de développement logiciel (SDK) Azure Machine Learning pour Python](/python/api/overview/azure/ml/) **ou** [Azure CLI](/cli/azure/) et [l’extension de Machine Learning](reference-azure-machine-learning-cli.md).
* Une installation fonctionnelle de Docker pour le test local. Pour plus d’informations sur l’installation et la validation de Docker, consultez [Orientation et installation](https://docs.docker.com/get-started/) dans la documentation.

## <a name="architectural-overview"></a>Vue d’ensemble de l’architecture

Avant de tenter d’utiliser Triton pour votre propre modèle, il est important de comprendre comment il fonctionne avec Azure Machine Learning et comment il est comparé à un déploiement par défaut.

**Déploiement par défaut sans Triton**

* Plusieurs rôles de travail [Gunicorn](https://gunicorn.org/) démarrent pour gérer simultanément les requêtes entrantes.
* Ces rôles de travail gèrent le prétraitement, l’appel du modèle et le post-traitement. 
* Les clients utilisent l’__URI de scoring Azure ML__. Par exemple : `https://myservice.azureml.net/score`.

:::image type="content" source="./media/how-to-deploy-with-triton/normal-deploy.png" alt-text="Diagramme d’architecture de déploiement normal, non Triton":::

**Déploiement direct avec Triton**

* Les demandes sont dirigées directement vers le serveur Triton.
* Triton traite les requêtes par lots pour optimiser l’utilisation du GPU.
* Le client utilise l’__URI Triton__ pour effectuer des requêtes. Par exemple : `https://myservice.azureml.net/v2/models/${MODEL_NAME}/versions/${MODEL_VERSION}/infer`.

:::image type="content" source="./media/how-to-deploy-with-triton/triton-deploy.png" alt-text="Déploiement de Inferenceconfig avec Triton uniquement, sans aucun intergiciel Python":::

**Déploiement de configuration de l’inférence avec Triton**

* Plusieurs rôles de travail [Gunicorn](https://gunicorn.org/) démarrent pour gérer simultanément les requêtes entrantes.
* Les requêtes sont transmises au **serveur Triton**. 
* Triton traite les requêtes par lots pour optimiser l’utilisation du GPU.
* Le client utilise l’__URI de scoring Azure Machine Learning__ pour effectuer des requêtes. Par exemple : `https://myservice.azureml.net/score`.

:::image type="content" source="./media/how-to-deploy-with-triton/inference-config-deploy.png" alt-text="Déploiement avec Triton et un intergiciel Python":::

Le flux de travail pour l’utilisation de Triton pour votre modèle de déploiement est le suivant :

1. Servez votre modèle avec Triton directement.
1. Vérifiez que vous pouvez envoyer des requêtes à votre modèle déployé par Triton.
1. (Facultatif) Créer une couche d’intergiciel Python pour le pré-traitement et le post-traitement côté serveur

## <a name="deploying-triton-without-python-pre--and-post-processing"></a>Déploiement de Triton sans le pré-traitement ni le post-traitement de Python

Tout d’abord, suivez les étapes ci-dessous pour vérifier que le serveur d’inférence Triton peut servir votre modèle.

### <a name="optional-define-a-model-config-file"></a>(Facultati) Définir un fichier config de modèle

Le fichier config de modèle indique à Triton le nombre d’entrées attendues et les dimensions de ces entrées. Pour plus d’informations sur la création du fichier config, consultez [Configuration de modèle](https://aka.ms/nvidia-triton-docs) dans la documentation NVIDIA.

> [!TIP]
> Nous utilisons l’option `--strict-model-config=false` lors du démarrage du serveur Triton Inference, ce qui signifie que vous n’avez pas besoin de fournir un fichier `config.pbtxt` pour les modèles ONNX ou TensorFlow.
> 
> Pour plus d’informations sur la création du fichier config, consultez [Configuration de modèle](https://aka.ms/nvidia-triton-docs) dans la documentation NVIDIA.

### <a name="use-the-correct-directory-structure"></a>Utilisez la structure de répertoire appropriée

Lors de l’inscription d’un modèle avec Azure Machine Learning, vous pouvez inscrire des fichiers individuels ou une structure de répertoires. Pour utiliser Triton, l’inscription du modèle doit être pour une structure de répertoires qui contient un répertoire nommé `triton`. La structure générale de ce répertoire est la suivante :

```bash
models
    - triton
        - model_1
            - model_version
                - model_file
                - config_file
        - model_2
            ...
```

> [!IMPORTANT]
> Cette structure de répertoire est un référentiel de modèles Triton et est nécessaire pour que votre ou vos modèles fonctionnent avec Triton. Pour plus d’informations, consultez [Triton Model Repositories](https://aka.ms/nvidia-triton-docs) dans la documentation NVIDIA.

### <a name="register-your-triton-model"></a>Inscrire votre modèle Triton

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

```azurecli-interactive
az ml model register -n my_triton_model -p models --model-framework=Multi
```

Pour plus d’informations sur `az ml model register`, consultez la [documentation de référence](/cli/azure/ext/azure-cli-ml/ml/model).

# <a name="python"></a>[Python](#tab/python)


```python

from azureml.core.model import Model

model_path = "models"

model = Model.register(
    model_path=model_path,
    model_name="bidaf-9-tutorial",
    tags={"area": "Natural language processing", "type": "Question-answering"},
    description="Question answering from ONNX model zoo",
    workspace=ws,
    model_framework=Model.Framework.MULTI,  # This line tells us you are registering a Triton model
)

```
Pour plus d’informations, consultez la documentation sur la [classe Model](/python/api/azureml-core/azureml.core.model.model).

---

### <a name="deploy-your-model"></a>Déployer votre modèle

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

Si vous disposez d’un cluster Azure Kubernetes Service avec GPU, appelé « aks-gpu » et créé avec Azure Machine Learning, vous pouvez utiliser la commande suivante pour déployer votre modèle.

```azurecli
az ml model deploy -n triton-webservice -m triton_model:1 --dc deploymentconfig.json --compute-target aks-gpu
```

# <a name="python"></a>[Python](#tab/python)

```python
from azureml.core.webservice import AksWebservice
from azureml.core.model import InferenceConfig
from random import randint

service_name = "triton-webservice"

config = AksWebservice.deploy_configuration(
    compute_target_name="aks-gpu",
    gpu_cores=1,
    cpu_cores=1,
    memory_gb=4,
    auth_enabled=True,
)

service = Model.deploy(
    workspace=ws,
    name=service_name,
    models=[model],
    deployment_config=config,
    overwrite=True,
)
```
---

[Pour plus d’informations sur le déploiement de modèles, consultez cette documentation](how-to-deploy-and-where.md).

### <a name="call-into-your-deployed-model"></a>Appeler votre modèle déployé

Tout d’abord, récupérez l’URI de scoring et les jetons du porteur.

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)


```azurecli
az ml service show --name=triton-webservice
```
# <a name="python"></a>[Python](#tab/python)

```python
import requests

print(service.scoring_uri)
print(service.get_keys())

```

---

Ensuite, vérifiez que votre service est en cours d’exécution avec : 

```{bash}
!curl -v $scoring_uri/v2/health/ready -H 'Authorization: Bearer '"$service_key"''
```

Cette commande renvoie des informations similaires aux suivantes. Notez le `200 OK`. Cet état indique que le serveur Web est en cours d’exécution.

```{bash}
*   Trying 127.0.0.1:8000...
* Connected to localhost (127.0.0.1) port 8000 (#0)
> GET /v2/health/ready HTTP/1.1
> Host: localhost:8000
> User-Agent: curl/7.71.1
> Accept: */*
>
* Mark bundle as not supporting multiuse
< HTTP/1.1 200 OK
HTTP/1.1 200 OK
```

Après avoir effectué un contrôle d’intégrité, vous pouvez créer un client et envoyer des données à Triton pour l’inférence. Pour plus d’informations sur la création d’un client, consultez les [exemples de clients](https://aka.ms/nvidia-client-examples) dans la documentation NVIDIA. Il existe également des exemples [Python dans le GitHub Triton](https://aka.ms/nvidia-triton-docs).

À ce stade, si vous ne souhaitez pas ajouter le pré-traitement et le post-traitement Python à votre service web déployé, vous avez peut-être terminé là votre travail. Si vous voulez ajouter cette logique de pré- et de post-traitement, continuez votre lecture.

## <a name="optional-re-deploy-with-a-python-entry-script-for-pre--and-post-processing"></a>(Facultatif) Redéploiement à l’aide d’un script d’entrée Python pour le pré- et le post-traitement

Après avoir vérifié que le Triton est capable de servir votre modèle, vous pouvez ajouter du code de pré-traitement et de post-traitement en définissant un _script d’entrée_. Ce fichier est nommé `score.py`. Pour plus d’informations sur le script d’entrée, consultez [Définir le code d’entrée](how-to-deploy-and-where.md#define-an-entry-script).

Les deux étapes principales sont l’initialisation d’un client HTTP Triton dans votre méthode `init()` et l’appel de ce client dans votre fonction `run()`.

### <a name="initialize-the-triton-client"></a>Initialiser le client Triton

Incluez un code comme l’exemple suivant dans votre fichier `score.py`. Triton dans Azure Machine Learning s’attend à être traité sur localhost, port 8000. Dans ce cas, localhost se trouve à l’intérieur de l’image de Docker pour ce déploiement, et non un port sur votre ordinateur local :

> [!TIP]
> Le package PIP `tritonhttpclient` est inclus dans l’environnement `AzureML-Triton` organisé. Il n’est donc pas nécessaire de le spécifier en tant que dépendance PIP.

```python
import tritonhttpclient

def init():
    global triton_client
    triton_client = tritonhttpclient.InferenceServerClient(url="localhost:8000")
```

### <a name="modify-your-scoring-script-to-call-into-triton"></a>Modifiez votre script de scoring pour appeler Triton

L’exemple suivant montre comment demander dynamiquement les métadonnées du modèle :

> [!TIP]
> Vous pouvez demander dynamiquement les métadonnées des modèles qui ont été chargés avec Triton à l’aide de la méthode `.get_model_metadata` du client Triton. Pour obtenir un exemple d’utilisation, consultez les [exemples de notebooks](https://aka.ms/triton-aml-sample).

```python
input = tritonhttpclient.InferInput(input_name, data.shape, datatype)
input.set_data_from_numpy(data, binary_data=binary_data)

output = tritonhttpclient.InferRequestedOutput(
         output_name, binary_data=binary_data, class_count=class_count)

# Run inference
res = triton_client.infer(model_name,
                          [input]
                          request_id='0',
                          outputs=[output])

```

<a id="redeploy"></a>

### <a name="redeploy-with-an-inference-configuration"></a>Redéployer avec une configuration d’inférence

Une configuration d’inférence vous permet d’utiliser un script d’entrée, ainsi que le processus de déploiement Azure Machine Learning à l’aide du kit de développement logiciel (SDK) Python ou de Azure CLI.

> [!IMPORTANT]
> Vous devez spécifier le `AzureML-Triton` [d’environnement curé](./resource-curated-environments.md).
>
> L’exemple de code Python clone `AzureML-Triton` dans un autre environnement appelé `My-Triton`. Le code Azure CLI utilise également cet environnement. Pour plus d’informations sur le clonage d’un environnement, consultez la référence [Environnement.Clone()](/python/api/azureml-core/azureml.core.environment.environment#clone-new-name-).

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

> [!TIP]
> Pour plus d’informations sur la création d’une configuration d'inférence, consultez [Schéma de configuration de l’inférence](./reference-azure-machine-learning-cli.md#inference-configuration-schema).

```azurecli
az ml model deploy -n triton-densenet-onnx \
-m densenet_onnx:1 \
--ic inference-config.json \
-e My-Triton --dc deploymentconfig.json \
--overwrite --compute-target=aks-gpu
```

# <a name="python"></a>[Python](#tab/python)

```python
from azureml.core.webservice import LocalWebservice
from azureml.core import Environment
from azureml.core.model import InferenceConfig


local_service_name = "triton-bidaf-onnx"
env = Environment.get(ws, "AzureML-Triton").clone("My-Triton")

for pip_package in ["nltk"]:
    env.python.conda_dependencies.add_pip_package(pip_package)

inference_config = InferenceConfig(
    entry_script="score_bidaf.py",  # This entry script is where we dispatch a call to the Triton server
    source_directory=os.path.join("..", "scripts"),
    environment=env
)

local_config = LocalWebservice.deploy_configuration(
    port=6789
)

local_service = Model.deploy(
    workspace=ws,
    name=local_service_name,
    models=[model],
    inference_config=inference_config,
    deployment_config=local_config,
    overwrite=True)

local_service.wait_for_deployment(show_output = True)
print(local_service.state)
# Print the URI you can use to call the local deployment
print(local_service.scoring_uri)
```

---

Après le déploiement, l’URI de scoring s’affiche. Pour ce déploiement local, ce sera `http://localhost:6789/score`. Si vous déployez sur le Cloud, vous pouvez utiliser la commande CLI [az ml service show](/cli/azure/ext/azure-cli-ml/ml/service#ext_azure_cli_ml_az_ml_service_show) pour récupérer l’URI de scoring.

Pour plus d’informations sur la création d’un client qui envoie des demandes d’inférence à l’URI de score, consultez [Consommer un modèle déployé en tant que service Web](how-to-consume-web-service.md).

### <a name="setting-the-number-of-workers"></a>Définition du nombre de workers

Pour définir le nombre de workers dans votre déploiement, définissez la variable d’environnement `WORKER_COUNT`. Étant donné que vous avez un objet [Environnement](/python/api/azureml-core/azureml.core.environment.environment) nommé `env`, vous pouvez procéder comme suit :

```{py}
env.environment_variables["WORKER_COUNT"] = "1"
```

Cela indique à Azure ML d’activer le nombre de workers que vous spécifiez.


## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous prévoyez de continuer à utiliser l’espace de travail Azure Machine Learning, mais que vous souhaitez supprimer le service déployé, utilisez les options suivantes :


# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

```azurecli
az ml service delete -n triton-densenet-onnx
```
# <a name="python"></a>[Python](#tab/python)

```python
local_service.delete()
```


---

## <a name="next-steps"></a>Étapes suivantes

* [Consulter des exemples de bout-en-bout de Triton dans Azure Machine Learning](https://aka.ms/aml-triton-sample)
* Consulter [les exemples client Triton](https://aka.ms/nvidia-client-examples)
* Lire la [documentation du serveur Triton Inference](https://aka.ms/nvidia-triton-docs)
* [Résoudre des problèmes d’échec de déploiement](how-to-troubleshoot-deployment.md)
* [Déployer dans Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md)
* [Mettre à jour un service web](how-to-deploy-update-web-service.md)
* [Collecter des données pour des modèles en production](how-to-enable-data-collection.md)