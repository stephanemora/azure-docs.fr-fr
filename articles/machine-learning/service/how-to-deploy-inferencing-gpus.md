---
title: Comment déployer un modèle d’apprentissage profond pour l’inférence avec GPU
titleSuffix: Azure Machine Learning service
description: Découvrez comment déployer un modèle d’apprentissage profond comme un service web qui utilise un GPU pour l’inférence. Dans cet article, un modèle Tensorflow est déployé sur un cluster Azure Kubernetes Service. Le cluster utilise une machine virtuelle de compatibles GPU pour héberger le service web et demandes de l’inférence de score.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: vaidyas
author: csteegz
ms.reviewer: larryfr
ms.date: 05/02/2019
ms.openlocfilehash: 5cc0fe0526937245d3ca913afc477f0259e2afd4
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/09/2019
ms.locfileid: "65515173"
---
# <a name="how-to-do-gpu-inferencing"></a>Procédure à suivre l’inférence GPU

Découvrez comment utiliser l’inférence GPU pour un modèle déployé comme un service web machine learning. Dans cet article, vous allez apprendre à utiliser le service Azure Machine Learning pour déployer un exemple de modèle d’apprentissage profond de Tensorflow. Le modèle est déployé sur un cluster Azure Kubernetes Service (AKS) qui utilise une machine virtuelle prenant en charge GPU pour héberger le service. Lorsque les demandes sont envoyées au service, le modèle utilise le GPU pour effectuer l’inférence.

GPU offrent des avantages de performances sur les unités centrales de calcul hautement parallélisable. Formation et inférence des modèles (en particulier pour les lots volumineux de demandes) deep learning sont des cas d’utilisation excellent pour GPU.  

Cet exemple affiche comment déployer un modèle TensorFlow enregistré dans Azure Machine Learning. 

## <a name="goals-and-prerequisites"></a>Objectifs et les conditions préalables

Suivez les instructions pour :
* Créer un GPU activé cluster AKS
* Déployer un modèle avec Tensorflow GPU

Configuration requise :
* Espace de travail Azure Machine Learning services
* Python
* Tensorflow SavedModel inscrit. Pour savoir comment inscrire les modèles consultez [déployer des modèles](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-and-where#registermodel)

Cet article est basé sur [déploiement de modèles Tensorflow sur AKS](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/production-deploy-to-aks-gpu/production-deploy-to-aks-gpu.ipynb), qui utilise TensorFlow enregistré modélise et déploie sur un cluster AKS. Toutefois, avec petites modifications au fichier de notation et au fichier d’environnement, il est applicable à toute infrastructure d’apprentissage automatique qui prennent en charge les GPU.  

## <a name="provision-aks-cluster-with-gpus"></a>Cluster AKS de disposition avec des GPU
Azure propose différentes options GPU, qui peut être utilisé pour l’inférence. Consultez [la liste de série N](https://azure.microsoft.com/pricing/details/virtual-machines/linux/#n-series) pour une description complète des fonctionnalités et les coûts. 

Pour plus d’informations sur l’utilisation d’ACS avec service Azure Machine Learning, consultez le [comment déployer et où l’article.](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-and-where#create-a-new-cluster)

```python
# Provision AKS cluster with GPU machine
prov_config = AksCompute.provisioning_configuration(vm_size="Standard_NC6")

# Create the cluster
aks_target = ComputeTarget.create(
    workspace=ws, name=aks_name, provisioning_configuration=prov_config
)

aks_target.wait_for_deployment()
```

> [!IMPORTANT]
> Azure vous facture tant que le cluster AKS est approvisionné. Veillez à supprimer votre cluster AKS, une fois que vous avez terminé l’utiliser.


## <a name="write-entry-script"></a>Écrire le script d’entrée

Enregistrez ce qui suit à votre répertoire de travail en tant que `score.py`. Ce fichier sera utilisé pour noter les images qu’ils sont envoyés à votre service. Ce fichier est chargé la TensorFlow enregistré le modèle, puis sur chaque requête POST demande passe de l’image d’entrée à la session TensorFlow et retourne les scores qui en résulte.
Autres infrastructures d’inférence nécessite différents fichiers de notation.

```python
import tensorflow as tf
import numpy as np
import ujson
from azureml.core.model import Model
from azureml.contrib.services.aml_request import AMLRequest, rawhttp
from azureml.contrib.services.aml_response import AMLResponse

def init():
    global session
    global input_name
    global output_name
    
    session = tf.Session()

    model_path = Model.get_model_path('resnet50')
    model = tf.saved_model.loader.load(session, ['serve'], model_path)
    if len(model.signature_def['serving_default'].inputs) > 1:
        raise ValueError("This score.py only supports one input")
    input_name = [tensor.name for tensor in model.signature_def['serving_default'].inputs.values()][0]
    output_name = [tensor.name for tensor in model.signature_def['serving_default'].outputs.values()]
    

@rawhttp
def run(request):
    if request.method == 'POST':
        reqBody = request.get_data(False)
        resp = score(reqBody)
        return AMLResponse(resp, 200)
    if request.method == 'GET':
        respBody = str.encode("GET is not supported")
        return AMLResponse(respBody, 405)
    return AMLResponse("bad request", 500)

def score(data):
    result = session.run(output_name, {input_name: [data]})
    return ujson.dumps(result[1])

if __name__ == "__main__":
    init()
    with open("lynx.jpg", 'rb') as f: #load file for testing locally
        content = f.read()
        print(score(content))

```

## <a name="define-conda-environment"></a>Définir l’environnement Conda
Créez un fichier d’environnement conda nommé `myenv.yml` pour spécifier les dépendances de votre service. Il est important de spécifier que vous utilisez `tensorflow-gpu` pour atteindre des performances accélérées.
```yaml
name: aml-accel-perf
channels:
  - defaults
dependencies:
  - tensorflow-gpu = 1.12
  - numpy
  - ujson
  - pip:
    - azureml-core
    - azureml-contrib-services
```

## <a name="define-gpu-inferenceconfig"></a>Définir les GPU InferenceConfig

Créer un [ `InferenceConfig` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) qui spécifie l’activation de GPU. Cela garantit que CUDA est installée avec votre Image.

```python
from azureml.core.model import Model
from azureml.core.model import InferenceConfig

aks_service_name ='gpu-rn'
gpu_aks_config = AksWebservice.deploy_configuration(autoscale_enabled = False, 
                                                    num_replicas = 3, 
                                                    cpu_cores=2, 
                                                    memory_gb=4)
model = Model(ws,"resnet50")

inference_config = InferenceConfig(runtime= "python", 
                                   entry_script="score.py",
                                   conda_file="myenv.yml", 
                                   gpu_enabled=True)
```

Pour plus d’informations, consultez [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) et [AksServiceDeploymentConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aks.aksservicedeploymentconfiguration?view=azure-ml-py).
## <a name="deploy-the-model"></a>Déployer le modèle

Déployer le modèle sur votre cluster AKS et attendez qu’elle se créer votre service.

```python
aks_service = Model.deploy(ws,
                           models=[model],
                           inference_config=inference_config, 
                           deployment_config=aks_config,
                           deployment_target=aks_target,
                           name=aks_service_name)

aks_service.wait_for_deployment(show_output = True)
print(aks_service.state)
```

> [!NOTE]
> Service Azure Machine Learning ne sera pas déployer un modèle avec un `InferenceConfig` qui attend le GPU vers un cluster sans GPU.

Pour plus d’informations, consultez [modèle](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py).

## <a name="issue-sample-query-to-deployed-model"></a>Exemple de requête de problème pour déployer le modèle

Émettre un exemple de requête à votre modèle déployé. Ce modèle sera score n’importe quelle image jpeg que vous envoyer à ce dernier sous la forme d’une demande post. 

```python
scoring_url = aks_service.scoring_uri
api_key = aks_service.get_key()(0)
IMAGEURL = "https://upload.wikimedia.org/wikipedia/commons/thumb/6/68/Lynx_lynx_poing.jpg/220px-Lynx_lynx_poing.jpg"

headers = {'Authorization':('Bearer '+ api_key)}
img_data = read_image_from(IMAGEURL).read()
r = requests.post(scoring_url, data = img_data, headers=headers)
```

> [!IMPORTANT]
> Pour optimiser la latence et le débit, votre client doit se trouver dans la même région Azure que le point de terminaison.  Les API sont actuellement créées dans la région Azure USA Est.

## <a name="cleaning-up-the-resources"></a>Nettoyage des ressources

Supprimer vos ressources une fois que vous avez terminé avec la démonstration.

> [!IMPORTANT]
> Azure vous facturera selon la durée pendant laquelle le cluster AKS est déployé. Veillez à nettoyer une fois que vous avez terminé avec lui.

```python
aks_service.delete()
aks_target.delete()
```

## <a name="next-steps"></a>Étapes suivantes

* [Déployer le modèle sur le FPGA](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-fpga-web-service)
* [Déployer le modèle avec ONNX](https://docs.microsoft.com/azure/machine-learning/service/how-to-build-deploy-onnx#deploy)
* [Former des modèles de réseau de neurones profond Tensorflow](https://docs.microsoft.com/azure/machine-learning/service/how-to-train-tensorflow)
