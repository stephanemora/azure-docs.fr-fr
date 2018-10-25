---
title: Déployer des services web sur Azure Container Instances (ACI) - Azure Machine Learning
description: Découvrez comment déployer un modèle entraîné en tant que service web sur Azure Container Instances (ACI) avec le service Azure Machine Learning. Cet article présente trois méthodes différentes pour déployer un modèle sur ACI. Elles diffèrent quant au nombre de lignes de code et au contrôle dont vous disposez dans le nommage des composants du déploiement.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: raymondl
author: raymondlaghaeian
ms.reviewer: sgilley
ms.date: 09/24/2018
ms.openlocfilehash: e796feaf8ef25eaa91b7db810a11a67da13e9df1
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/03/2018
ms.locfileid: "48237175"
---
# <a name="deploy-web-services-to-azure-container-instances"></a>Déployer des services web sur Azure Container Instances 

Vous pouvez déployer votre modèle entraîné en tant que service web sur [Azure Container Instances](https://azure.microsoft.com/services/container-instances/) (ACI), [Azure Kubernetes Service](https://azure.microsoft.com/services/kubernetes-service/) (AKS), un appareil IoT Edge, ou des [FPGA (Field Programmable Gate Arrays)](concept-accelerate-with-fpgas.md). 

ACI est généralement moins cher qu’AKS, et peut être configuré en quatre à six lignes de code. ACI est l’option idéale pour tester les déploiements. Plus tard, quand vous serez prêt à utiliser vos modèles et services web pour un usage de production à grande échelle, vous pourrez [les déployer sur AKS](how-to-deploy-to-aks.md).

Cet article présente trois méthodes différentes pour déployer un modèle sur ACI. Elles diffèrent quant au nombre de lignes de code et au contrôle dont vous disposez dans le nommage des composants du déploiement. De la méthode exigeant le moins de code et offrant le moins de contrôle à la méthode exigeant le plus de code et offrant le plus de contrôle, les options ACI sont les suivantes :

* Déployer à partir d’un fichier de modèle à l’aide de `Webservice.deploy()` 
* Déployer à partir d’un modèle inscrit à l’aide de `Webservice.deploy_from_model()`
* Déployer un modèle inscrit à partir d’une image à l’aide de `Webservice.deploy_from_image()`

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.


## <a name="prerequisites"></a>Prérequis

- Un espace de travail de service Azure Machine Learning et le SDK Azure Machine Learning pour Python installé. Découvrez comment obtenir ces prérequis dans le guide de démarrage rapide [Bien démarrer avec Azure Machine Learning](quickstart-get-started.md).

- L’objet d’espace de travail de service Microsoft Azure Machine Learning

    ```python
    from azureml.core import Workspace
    ws = Workspace.from_config()
    ```

- Un modèle à déployer. Les exemples de ce document utilisent le modèle créé quand vous suivez le tutoriel « [Entraîner un modèle](tutorial-train-models-with-aml.md) ». Si vous n’utilisez pas ce modèle, modifiez les étapes pour qu’elles référencent le nom de votre modèle.  Vous devez également écrire votre propre script de scoring pour exécuter votre modèle


## <a name="configure-an-image"></a>Configurer une image

Configurez l’image Docker qui est utilisée pour stocker tous les fichiers de modèle.
1. Créez un script de scoring (score.py) [à l’aide de ces instructions](tutorial-deploy-models-with-aml.md#create-scoring-script).

1. Créez un fichier d’environnement (myenv.yml) [à l’aide de ces instructions](tutorial-deploy-models-with-aml.md#create-environment-file). 

1. Utilisez ces deux fichiers pour configurer l’image Docker dans Python à l’aide du SDK comme suit :

    ```python
    from azureml.core.image import ContainerImage

    image_config = ContainerImage.image_configuration(execution_script = "score.py",
                                                      runtime = "python",
                                                      conda_file = "myenv.yml",
                                                      description = "Image with mnist model",
                                                      tags = {"data": "mnist", "type": "classification"}
                                                     )
    ```

## <a name="configure-the-aci-container"></a>Configurer le conteneur ACI

Configurez le conteneur ACI en spécifiant le nombre de processeurs et de gigaoctets de RAM nécessaires pour votre conteneur ACI. La valeur par défaut d’un seul cœur et de 1 gigaoctet de RAM suffit pour de nombreux modèles. Si plus tard vous pensez avoir besoin de davantage, recréez l’image et redéployez le service.  

```python
from azureml.core.webservice import AciWebservice

aciconfig = AciWebservice.deploy_configuration(cpu_cores = 1, 
                                               memory_gb = 1, 
                                               tags = {"data": "mnist", "type": "classification"},
                                               description = 'Handwriting recognition')
```

## <a name="register-a-model"></a>Inscrivez un modèle

> Ignorez ce prérequis si vous [déployez à partir d’un fichier de modèle](#deploy-from-model-file) (`Webservice.deploy()`).

Inscrivez un modèle pour utiliser [`Webservice.deploy_from_model`](#deploy-from-registered-model) ou [`Webservice.deploy_from_image`](#deploy-from-image). Si vous avez déjà un modèle inscrit, récupérez-le maintenant.

### <a name="retrieve-a-registered-model"></a>Récupérer un modèle inscrit
Si vous utilisez Azure Machine Learning pour entraîner votre modèle, il se peut qu’il soit déjà inscrit dans votre espace de travail.  Par exemple, la dernière étape du tutoriel [Entraîner un modèle](tutorial-train-models-with-aml.md) a inscrit le modèle.  Vous récupérez ensuite le modèle inscrit à déployer.

```python
from azureml.core.model import Model

model_name = "sklearn_mnist"
model=Model(ws, model_name)
```
  
### <a name="register-a-model-file"></a>Inscrire un fichier de modèle

Si votre modèle a été créé ailleurs, vous pouvez tout de même l’inscrire dans votre espace de travail.  Pour inscrire un modèle, il faut que le fichier de modèle (`sklearn_mnist_model.pkl` dans cet exemple) se trouve dans le répertoire de travail actif. Ensuite, inscrivez ce fichier en tant que modèle nommé `sklearn_mnist` dans l’espace de travail avec `Model.register()`.
    
```python
from azureml.core.model import Model

model_name = "sklearn_mnist"
model = Model.register(model_path = "sklearn_mnist_model.pkl",
                        model_name = model_name,
                        tags = {"data": "mnist", "type": "classification"},
                        description = "Mnist handwriting recognition",
                        workspace = ws)
```


## <a name="option-1-deploy-from-model-file"></a>Option 1 : Déployer à partir d’un fichier de modèle

L’option de déploiement à partir d’un fichier de modèle nécessite le moins de code, mais elle offre également un moindre contrôle sur le nommage des composants. Cette option commence avec un fichier de modèle, et l’inscrit dans l’espace de travail pour vous.  Toutefois, vous ne pouvez pas nommer le modèle, ni y associer des balises ou une description.  

Cette option utilise la méthode du SDK, Webservice.deploy().  

**Durée estimée** : le déploiement prend de six à sept minutes.

1. Vérifiez que le fichier de modèle se trouve dans votre répertoire de travail local.

1. Ouvrez le fichier de modèle prérequis, score.py, et remplacez la section `init()` par :

    ```python
    def init():
        global model
        # retreive the local path to the model using the model name
        model_path = Model.get_model_path('sklearn_mnist_model.pkl')
        model = joblib.load(model_path)
    ```

1. Déployez votre fichier de modèle.

    ```python
    from azureml.core.webservice import Webservice
    
    service_name = 'aci-mnist-1'
    service = Webservice.deploy(deployment_config = aciconfig,
                                    image_config = image_config,
                                    model_paths = ['sklearn_mnist_model.pkl'],
                                    name = service_name,
                                    workspace = ws)
    
    service.wait_for_deployment(show_output = True)
    print(service.state)
    ```

1. Vous pouvez à présent [tester le service web](#test-web-service).

## <a name="option-2-deploy-from-registered-model"></a>Option 2 : Déployer à partir d’un modèle inscrit

L’option de déploiement d’un fichier de modèle inscrit nécessite quelques lignes de code supplémentaires et offre un certain contrôle sur le nommage des sorties. Cette option constitue un moyen efficace de déployer un modèle inscrit dont vous disposez déjà.  Toutefois, vous ne pouvez pas nommer l’image Docker.  

Cette option utilise la méthode du SDK, Webservice.deploy_from_model().

**Durée estimée** : le déploiement avec cette option prend environ huit minutes.

1. Exécutez le code pour configurer le conteneur Docker et le conteneur ACI, et spécifier le modèle inscrit.

    ```python
    from azureml.core.webservice import Webservice

    service_name = 'aci-mnist-2'
    service = Webservice.deploy_from_model(deployment_config = aciconfig,
                                           image_config = image_config,
                                           models = [model], # this is the registered model object
                                           name = service_name,
                                           workspace = ws)
    service.wait_for_deployment(show_output = True)
    print(service.state)
    ```

1. Vous pouvez à présent [tester le service web](#test-web-service).

## <a name="option-3-deploy-from-image"></a>Option 3 : Déployer à partir d’une image

Déployez un modèle inscrit (`model`) à l’aide de `Webservice.deploy_from_image()`. Cette méthode vous permet de créer l’image Docker séparément, puis de déployer à partir de cette image.

1. Créez et inscrivez l’image Docker sous l’espace de travail à l’aide de `ContainerImage.create()`.

    Cette méthode offre davantage de contrôle sur l’image en la créant lors d’une étape distincte.  Le modèle inscrit (`model`) est inclus dans l’image.
    
    ```python
    from azureml.core.image import ContainerImage
    
    image = ContainerImage.create(name = "myimage1",
                                  models = [model], # this is the registered model object
                                  image_config = image_config,
                                  workspace = ws)
    
    image.wait_for_creation(show_output = True)
    ```
**Durée estimée** : environ trois minutes.

1. Déployez l’image Docker en tant que service à l’aide de `Webservice.deploy_from_image()`.

    Maintenant, déployez l’image sur ACI.  
    
    ```python
    from azureml.core.webservice import Webservice
    
    service_name = 'aci-mnist-3'
    service = Webservice.deploy_from_image(deployment_config = aciconfig,
                                                image = image,
                                                name = service_name,
                                                workspace = ws)
    service.wait_for_deployment(show_output = True)
    print(service.state)
    ```   
 
**Durée estimée** : environ trois minutes.

Cette méthode offre le plus de contrôle sur la création et le nommage des composants du déploiement.

Vous pouvez maintenant tester le service web.

## <a name="test-the-web-service"></a>Test du service web

Le service web est le même quelle que soit la méthode utilisée.  Pour obtenir des prédictions, utilisez la méthode `run` du service.  

```python
# Load Data
import os
import urllib

os.makedirs('./data', exist_ok = True)

urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz', filename = './data/test-images.gz')

from utils import load_data
X_test = load_data('./data/test-images.gz', False) / 255.0

from sklearn import datasets
import numpy as np
import json

# find 5 random samples from test set
n = 5
sample_indices = np.random.permutation(X_test.shape[0])[0:n]

test_samples = json.dumps({"data": X_test[sample_indices].tolist()})
test_samples = bytes(test_samples, encoding = 'utf8')

# predict using the deployed model
prediction = service.run(input_data = test_samples)
print(prediction)
```


## <a name="clean-up-resources"></a>Supprimer des ressources

Si vous ne prévoyez pas d’utiliser ce service web, supprimez-le afin d’éviter des frais.

```python
service.delete()
```

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment [déployer sur Azure Kubernetes Service](how-to-deploy-to-aks.md) pour un déploiement à plus grande échelle. 
