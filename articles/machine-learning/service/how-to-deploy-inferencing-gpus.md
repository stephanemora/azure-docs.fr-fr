---
title: Déployer un modèle pour l’inférence de GPU
titleSuffix: Azure Machine Learning service
description: Découvrez comment déployer un modèle d’apprentissage profond comme un service web qui utilise un GPU pour l’inférence. Dans cet article, un modèle Tensorflow est déployé sur un cluster Azure Kubernetes Service. Le cluster utilise une machine virtuelle de compatibles GPU pour héberger le service web et les demandes de l’inférence de score.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: vaidyas
author: csteegz
ms.reviewer: larryfr
ms.date: 05/02/2019
ms.openlocfilehash: ec71165553a1d65ff133d605bf94255100f74e6e
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66388917"
---
# <a name="deploy-a-deep-learning-model-for-inference-with-gpu"></a>Déployer un modèle d’apprentissage profond pour l’inférence de GPU

Découvrez comment utiliser l’inférence de GPU pour un modèle déployé comme un service web machine learning. Inférence, ou un modèle de score, est la phase où le modèle déployé est utilisé pour la prédiction, généralement sur les données de production.

Cet article vous apprend à utiliser le service Azure Machine Learning pour déployer un exemple de modèle à un cluster Azure Kubernetes Service (AKS) sur un ordinateur prenant en charge GPU de virtuel (VM) de formation approfondie de Tensorflow. Lorsque les demandes sont envoyées au service, le modèle utilise le GPU pour exécuter les charges de travail de l’inférence.

GPU offrent des avantages de performances sur les unités centrales de calcul hautement parallélisable. Excellente pour les machines virtuelles compatibles GPU inclut l’apprentissage profond formation et l’inférence, en particulier pour les grands lots de requêtes de modèle.

Cet exemple montre comment déployer un TensorFlow enregistré le modèle Azure Machine Learning. Procédez comme suit :

* Créer un cluster ACS compatibles GPU
* Déployer un modèle Tensorflow GPU

## <a name="prerequisites"></a>Conditions préalables

* Un espace de travail Azure Machine Learning services
* Une distribution de Python
* Un Tensorflow inscrit enregistré le modèle. Pour savoir comment inscrire des modèles, consultez [déployer les modèles](../service/how-to-deploy-and-where.md#registermodel).

Cet article est basé sur le notebook Jupyter, [déploiement de modèles Tensorflow sur AKS](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/production-deploy-to-aks-gpu/production-deploy-to-aks-gpu.ipynb). Le notebook Jupyter utilise TensorFlow enregistré des modèles et les déploie sur un cluster AKS. Vous pouvez également appliquer le bloc-notes vers n’importe quel framework qui prend en charge de GPU à l’apport de modifications mineures pour le fichier de score et le fichier d’environnement d’apprentissage.  

## <a name="provision-an-aks-cluster-with-gpus"></a>Approvisionner un cluster AKS avec des GPU

Azure dispose de nombreuses options GPU. Vous pouvez utiliser une d'entre elles pour l’inférence. Consultez [la liste des machines virtuelles série N](https://azure.microsoft.com/pricing/details/virtual-machines/linux/#n-series) pour une description complète des fonctionnalités et les coûts.

Pour plus d’informations sur l’utilisation d’ACS avec service Azure Machine Learning, consultez [comment déployer et où](../service/how-to-deploy-and-where.md#deploy-aks).

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
> Azure vous facture tant que le cluster AKS est approvisionné. Veillez à supprimer votre cluster AKS lorsque vous avez terminé avec lui.

## <a name="write-the-entry-script"></a>Écrire le script d’entrée

Enregistrez le code suivant dans votre répertoire de travail en tant que `score.py`. Ce fichier scores des images comme ils sont envoyés à votre service. Il charge le modèle TensorFlow enregistré, transmet l’image d’entrée à la session de TensorFlow sur chaque requête POST et renvoie les scores qui en résulte. Autres infrastructures d’inférence nécessitent différents fichiers de notation.

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

## <a name="define-the-conda-environment"></a>Définir l’environnement conda

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

## <a name="define-the-gpu-inferenceconfig-class"></a>Définir la classe InferenceConfig de GPU

Créer un `InferenceConfig` objet qui permet les GPU et garantit que CUDA est installé avec une image Docker.

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

Pour plus d'informations, consultez les pages suivantes :

- [Classe de InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py)
- [Classe de AksServiceDeploymentConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aks.aksservicedeploymentconfiguration?view=azure-ml-py)

## <a name="deploy-the-model"></a>Déployer le modèle

Déployer le modèle sur votre cluster AKS et attendez qu’elle se créer votre service.

```python
aks_service = Model.deploy(ws,
                           models=[model],
                           inference_config=inference_config, 
                           deployment_config=gpu_aks_config,
                           deployment_target=aks_target,
                           name=aks_service_name)

aks_service.wait_for_deployment(show_output = True)
print(aks_service.state)
```

> [!NOTE]
> Service Azure Machine Learning ne sont pas déployer un modèle avec une `InferenceConfig` objet censé GPU doit être activé pour un cluster qui n’a pas un GPU.

Pour plus d’informations, consultez [classe de modèle](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py).

## <a name="issue-a-sample-query-to-your-deployed-model"></a>Émettre un exemple de requête à votre modèle déployé

Envoyer une requête de test au modèle déployé. Lorsque vous envoyez une image jpeg pour le modèle, il évalue l’image.

```python
scoring_url = aks_service.scoring_uri
api_key = aks_service.get_key()(0)
IMAGEURL = "https://upload.wikimedia.org/wikipedia/commons/thumb/6/68/Lynx_lynx_poing.jpg/220px-Lynx_lynx_poing.jpg"

headers = {'Authorization':('Bearer '+ api_key)}
img_data = read_image_from(IMAGEURL).read()
r = requests.post(scoring_url, data = img_data, headers=headers)
```

> [!IMPORTANT]
> Pour réduire la latence et d’optimiser le débit, assurez-vous que votre client se trouve dans la même région Azure que le point de terminaison. Dans cet exemple, les API sont créées dans la région Azure est des États-Unis.

## <a name="clean-up-the-resources"></a>Nettoyer les ressources

Supprimer vos ressources une fois que vous avez terminé avec cet exemple.

> [!IMPORTANT]
> Factures Azure repose sur la durée pendant laquelle le cluster AKS est déployé. Veillez à nettoyer une fois que vous avez terminé avec lui.

```python
aks_service.delete()
aks_target.delete()
```

## <a name="next-steps"></a>Étapes suivantes

* [Déployer le modèle sur le FPGA](../service/how-to-deploy-fpga-web-service.md)
* [Déployer le modèle avec ONNX](../service/concept-onnx.md#deploy-onnx-models-in-azure)
* [Former des modèles de réseau de neurones profond Tensorflow](../service/how-to-train-tensorflow.md)
