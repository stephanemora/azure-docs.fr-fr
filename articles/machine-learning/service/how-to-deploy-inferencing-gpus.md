---
title: Déployer un modèle pour une inférence avec GPU
titleSuffix: Azure Machine Learning service
description: Cet article explique comment utiliser Azure Machine Learning service pour déployer un modèle de deep learning TensorFlow compatible GPU en tant que service web et noter les demandes d’inférence.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: vaidyas
author: csteegz
ms.reviewer: larryfr
ms.date: 06/01/2019
ms.openlocfilehash: 8086d059913cc61bff0bca31681368bea6d76777
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/03/2019
ms.locfileid: "67543801"
---
# <a name="deploy-a-deep-learning-model-for-inference-with-gpu"></a>Déployer un modèle de deep learning pour une inférence avec GPU

Cet article explique comment utiliser Azure Machine Learning service pour déployer un modèle de deep learning TensorFlow compatible GPU en tant que service web.

Déployez votre modèle sur un cluster Azure Kubernetes Service (AKS) pour effectuer l’inférence compatible GPU. L’inférence, ou notation de modèle, est la phase au cours de laquelle le modèle déployé est utilisé à des fins de prédiction. L’utilisation de GPU au lieu d’UC offre des avantages en termes de performances pour le calcul hautement parallélisable.

Bien que cet exemple utilise un modèle TensorFlow, vous pouvez appliquer les étapes suivantes à toute infrastructure d’apprentissage automatique prenant en charge les GPU en apportant de petites modifications au fichier de notation et au fichier d’environnement. 

Dans cet article, vous procédez comme suit :

* Créer un cluster AKS compatible GPU
* Déployer un modèle GPU Tensorflow
* Envoyer un exemple de requête à votre modèle déployé

## <a name="prerequisites"></a>Conditions préalables

* Un espace de travail Azure Machine Learning service.
* Une distribution Python.
* Un modèle enregistré Tensorflow inscrit.
    * Pour savoir comment inscrire des modèles, voir [Déployer des modèles](../service/how-to-deploy-and-where.md#registermodel).

Vous pouvez suivre la première partie de cette série d’instructions, [ Comment effectuer l’apprentissage d’un modèle TensorFlow ](how-to-train-tensorflow.md) afin de remplir les conditions préalables.

## <a name="provision-an-aks-cluster-with-gpus"></a>Approvisionner un cluster AKS avec des GPU

Azure offre de nombreuses options de GPU. Vous pouvez utiliser celle de votre choix pour l’inférence. Pour obtenir la liste complète des fonctionnalités et des coûts, voir la [ liste des machines virtuelles série N](https://azure.microsoft.com/pricing/details/virtual-machines/linux/#n-series).

Pour plus d’informations sur l’utilisation d’AKS avec Azure Machine Learning service, voir [Vomment déployer et où](../service/how-to-deploy-and-where.md#deploy-aks).

```Python
# Choose a name for your cluster
aks_name = "aks-gpu"

# Check to see if the cluster already exists
try:
    compute_target = ComputeTarget(workspace=ws, name=aks_name)
    print('Found existing compute target')
except ComputeTargetException:
    print('Creating a new compute target...')
    # Provision AKS cluster with GPU machine
    prov_config = AksCompute.provisioning_configuration(vm_size="Standard_NC6")

    # Create the cluster
    aks_target = ComputeTarget.create(
        workspace=ws, name=aks_name, provisioning_configuration=prov_config
    )

    aks_target.wait_for_completion(show_output=True)
```

> [!IMPORTANT]
> Azure vous facture tant que le cluster AKS est approvisionné. Veillez à supprimer votre cluster AKS lorsque vous n’en avec plus besoin.

## <a name="write-the-entry-script"></a>Écrire le script d’entrée

Enregistrez le code suivant dans votre répertoire de travail sous le nom `score.py`. Ce fichier note les images à mesure qu’elles sont envoyées à votre service. Il charge le modèle enregistré TensorFlow, transmet l’image en entrée à la session TensorFlow sur chaque requête POST, puis renvoie les notations obtenues. D’autres infrastructures d’inférence nécessitent des fichiers de notation différents.

```python
import json
import numpy as np
import os
import tensorflow as tf

from azureml.core.model import Model

def init():
    global X, output, sess
    tf.reset_default_graph()
    model_root = Model.get_model_path('tf-dnn-mnist')
    saver = tf.train.import_meta_graph(os.path.join(model_root, 'mnist-tf.model.meta'))
    X = tf.get_default_graph().get_tensor_by_name("network/X:0")
    output = tf.get_default_graph().get_tensor_by_name("network/output/MatMul:0")
    
    sess = tf.Session()
    saver.restore(sess, os.path.join(model_root, 'mnist-tf.model'))

def run(raw_data):
    data = np.array(json.loads(raw_data)['data'])
    # make prediction
    out = output.eval(session=sess, feed_dict={X: data})
    y_hat = np.argmax(out, axis=1)
    return y_hat.tolist()

```
## <a name="define-the-conda-environment"></a>Définir l’environnement Conda

Créez un fichier d’environnement Conda nommé `myenv.yml` pour spécifier les dépendances de votre service. Il est important de spécifier que vous utilisez `tensorflow-gpu` pour accélérer les performances.

```yaml
name: project_environment
dependencies:
  # The python interpreter version.
  # Currently Azure ML only supports 3.5.2 and later.
- python=3.6.2

- pip:
  - azureml-defaults==1.0.43.*
- numpy
- tensorflow-gpu=1.12
channels:
- conda-forge
```

## <a name="define-the-gpu-inferenceconfig-class"></a>Définir la classe InferenceConfig de GPU

Créez un objet `InferenceConfig` qui active les GPU et s’assure que CUDA est installé avec votre image Docker.

```python
from azureml.core.model import Model
from azureml.core.model import InferenceConfig

aks_service_name ='aks-dnn-mnist'
gpu_aks_config = AksWebservice.deploy_configuration(autoscale_enabled = False, 
                                                    num_replicas = 3, 
                                                    cpu_cores=2, 
                                                    memory_gb=4)
model = Model(ws,"tf-dnn-mnist")

inference_config = InferenceConfig(runtime= "python", 
                                   entry_script="score.py",
                                   conda_file="myenv.yml", 
                                   enable_gpu=True)
```

Pour plus d'informations, consultez les pages suivantes :

- [Classe InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py)
- [Classe AksServiceDeploymentConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aks.aksservicedeploymentconfiguration?view=azure-ml-py)

## <a name="deploy-the-model"></a>Déployer le modèle

Déployez le modèle sur votre cluster AKS et attendez qu’il crée votre service.

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
> Azure Machine Learning service ne déploie pas un modèle avec un objet `InferenceConfig` qui s’attend à ce que le GPU soit activé pour un cluster ne disposant pas de GPU.

Pour plus d’informations, voir [Classe de modèle](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py).

## <a name="issue-a-sample-query-to-your-model"></a>Envoyer un exemple de requête à votre modèle

Envoyez une requête test au modèle déployé. Lorsque vous envoyez une image jpeg au modèle, celui-ci note l’image. L’exemple de code suivant utilise une fonction utilitaire externe pour charger les images. Vous pouvez trouver le code approprié dans l’[Exemple TensorFlow sur GitHub](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow/utils.py). 

```python
# Used to test your webservice
from utils import load_data 

# Load test data from model training
X_test = load_data('./data/mnist/test-images.gz', False) / 255.0
y_test = load_data('./data/mnist/test-labels.gz', True).reshape(-1)

# send a random row from the test set to score
random_index = np.random.randint(0, len(X_test)-1)
input_data = "{\"data\": [" + str(list(X_test[random_index])) + "]}"

api_key = aks_service.get_keys()[0]
headers = {'Content-Type':'application/json', 'Authorization':('Bearer '+ api_key)}
resp = requests.post(aks_service.scoring_uri, input_data, headers=headers)

print("POST to url", aks_service.scoring_uri)
#print("input data:", input_data)
print("label:", y_test[random_index])
print("prediction:", resp.text)
```

> [!IMPORTANT]
> Pour réduire la latence et optimiser le débit, assurez-vous que votre client se trouve dans la même région Azure que le point de terminaison. Dans cet exemple, les API sont créées dans la région Azure USA Est.

## <a name="clean-up-the-resources"></a>Nettoyer les ressources

Supprimez vos ressources une fois que vous en avez terminé avec cet exemple.

> [!IMPORTANT]
> Azure vous facture en fonction de la durée de déploiement du cluster AKS. Veillez à le nettoyer lorsque vous n’en avez plus besoin.

```python
aks_service.delete()
aks_target.delete()
```

## <a name="next-steps"></a>Étapes suivantes

* [Déployer le modèle sur FPGA](../service/how-to-deploy-fpga-web-service.md)
* [Déployer le modèle avec ONNX](../service/concept-onnx.md#deploy-onnx-models-in-azure)
* [Effectuer l’apprentissage de modèles DNN Tensorflow](../service/how-to-train-tensorflow.md)
