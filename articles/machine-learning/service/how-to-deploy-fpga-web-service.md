---
title: Déployer un modèle en tant que service web sur un FPGA avec Azure Machine Learning
description: Découvrez comment déployer un service web avec un modèle en cours d’exécution sur un FPGA avec Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: tedway
author: tedway
ms.date: 10/01/2018
ms.openlocfilehash: 925173f85301d6481ae3b9cf891041239b06bc8f
ms.sourcegitcommit: 4eddd89f8f2406f9605d1a46796caf188c458f64
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2018
ms.locfileid: "49113714"
---
# <a name="deploy-a-model-as-a-web-service-on-an-fpga-with-azure-machine-learning"></a>Déployer un modèle en tant que service web sur un FPGA avec Azure Machine Learning

Vous pouvez déployer un modèle en tant que service web sur des [FPGA (Field Programmable Gate Arrays)](concept-accelerate-with-fpgas.md).  L’utilisation de FGPA assure une inférence à très faible latence, même avec une taille de lot unique.   

## <a name="prerequisites"></a>Prérequis

- Un abonnement Azure. Si vous n’en avez pas, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

- Vous devez demander un quota FPGA et obtenir une approbation. Pour demander un accès, remplissez le formulaire de demande de quota : https://aka.ms/aml-real-time-ai

- Un espace de travail du service Azure Machine Learning et le kit SDK Azure Machine Learning pour Python installé. Découvrez comment obtenir ces prérequis à l’aide du document [Guide pratique pour configurer un environnement de développement](how-to-configure-environment.md).
 
  - Votre espace de travail doit se trouver dans la région *USA Est 2*.

  - Installez les extras :

    ```shell
    pip install --upgrade azureml-sdk[contrib]
    ```  

## <a name="create-and-deploy-your-model"></a>Créer et déployer votre modèle
Créez un pipeline pour prétraiter l’image en entrée, caractérisez-la à l’aide de ResNet 50 sur un FPGA, puis exécutez les caractéristiques par le biais d’un classifieur entraîné sur le jeu de données ImageNet.

Suivez les instructions pour :

* Définir le pipeline de modèle
* Déployer le modèle
* Utiliser le modèle déployé
* Supprimer les services déployés

> [!IMPORTANT]
> Pour optimiser la latence et le débit, votre client doit se trouver dans la même région Azure que le point de terminaison.  Les API sont actuellement créées dans la région Azure USA Est.



### <a name="preprocess-image"></a>Prétraiter l’image
La première étape du pipeline consiste à prétraiter les images.

```python
import os
import tensorflow as tf

# Input images as a two-dimensional tensor containing an arbitrary number of images represented a strings
import azureml.contrib.brainwave.models.utils as utils
in_images = tf.placeholder(tf.string)
image_tensors = utils.preprocess_array(in_images)
print(image_tensors.shape)
```

### <a name="add-featurizer"></a>Ajouter un caractériseur
Initialisez le modèle et téléchargez un point de contrôle TensorFlow de la version quantifiée de ResNet50 à utiliser comme caractériseur.

```python
from azureml.contrib.brainwave.models import QuantizedResnet50, Resnet50
model_path = os.path.expanduser('~/models')
model = QuantizedResnet50(model_path, is_frozen = True)
feature_tensor = model.import_graph_def(image_tensors)
print(model.version)
print(feature_tensor.name)
print(feature_tensor.shape)
```

### <a name="add-classifier"></a>Ajouter un classifieur
Ce classifieur a été entraîné sur le jeu de données ImageNet.

```python
classifier_input, classifier_output = Resnet50.get_default_classifier(feature_tensor, model_path)
```

### <a name="create-service-definition"></a>Créer une définition de service
Maintenant que vous avez défini le prétraitement d’image, le caractériseur et le classifieur qui s’exécute sur le service, vous pouvez créer une définition de service. La définition de service est un ensemble de fichiers générés à partir du modèle déployé sur le service FPGA. Elle est constituée d’un pipeline. Le pipeline est une série de phases qui sont exécutées dans l’ordre.  Les phases TensorFlow, Keras et BrainWave sont prises en charge.  Les phases sont exécutées dans l’ordre sur le service, la sortie de chaque phase étant fournie comme entrée à la phase suivante.

Pour créer une phase TensorFlow, spécifiez une session contenant le graphe (ici, le graphe par défaut est utilisé) et les tensors d’entrée et de sortie de cette phase.  Ces informations servent à enregistrer le graphe afin qu’il puisse être exécuté sur le service.

```python
from azureml.contrib.brainwave.pipeline import ModelDefinition, TensorflowStage, BrainWaveStage

save_path = os.path.expanduser('~/models/save')
model_def_path = os.path.join(save_path, 'service_def.zip')

model_def = ModelDefinition()
with tf.Session() as sess:
    model_def.pipeline.append(TensorflowStage(sess, in_images, image_tensors))
    model_def.pipeline.append(BrainWaveStage(sess, model))
    model_def.pipeline.append(TensorflowStage(sess, classifier_input, classifier_output))
    model_def.save(model_def_path)
    print(model_def_path)
```

### <a name="deploy-model"></a>Déployer le modèle
Créez un service à partir de la définition de service.  Votre espace de travail doit se trouver dans la région USA Est 2.

```python
from azureml.core import Workspace

ws = Workspace.from_config()
print(ws.name, ws.resource_group, ws.location, ws.subscription_id, sep = '\n')

from azureml.core.model import Model
model_name = "resnet-50-rtai"
registered_model = Model.register(ws, model_def_path, model_name)

from azureml.core.webservice import Webservice
from azureml.exceptions import WebserviceException
from azureml.contrib.brainwave import BrainwaveWebservice, BrainwaveImage
service_name = "imagenet-infer"
service = None
try:
    service = Webservice(ws, service_name)
except WebserviceException:
    image_config = BrainwaveImage.image_configuration()
    deployment_config = BrainwaveWebservice.deploy_configuration()
    service = Webservice.deploy_from_model(ws, service_name, [registered_model], image_config, deployment_config)
    service.wait_for_deployment(true)
```

### <a name="test-the-service"></a>Testez le service
Pour envoyer une image à l’API et tester la réponse, ajoutez un mappage à partir de l’ID de classe de sortie au nom de la classe ImageNet.

```python
import requests
classes_entries = requests.get("https://raw.githubusercontent.com/Lasagne/Recipes/master/examples/resnet50/imagenet_classes.txt").text.splitlines()
```

Appelez votre service et remplacez le nom de fichier « your-image.jpg » ci-dessous par une image de votre machine. 

```python
with open('your-image.jpg') as f:
    results = service.run(f)
# map results [class_id] => [confidence]
results = enumerate(results)
# sort results by confidence
sorted_results = sorted(results, key=lambda x: x[1], reverse=True)
# print top 5 results
for top in sorted_results[:5]:
    print(classes_entries[top[0]], 'confidence:', top[1])
``` 

### <a name="clean-up-service"></a>Nettoyer le service
Supprimez le service.

```python
service.delete()
    
registered_model.delete()
```

## <a name="secure-fpga-web-services"></a>Sécuriser les services web FPGA

Les modèles Azure Machine Learning exécutés sur des FPGA fournissent la prise en charge du protocole SSL et l’authentification basée sur la clé. Cela vous permet de restreindre l’accès à votre service et de sécuriser les données envoyées par vos clients. [Découvrez comment sécuriser le service web](how-to-secure-web-service.md).


## <a name="sample-notebook"></a>Exemple de notebook

Les concepts de cet article sont illustrés dans le notebook [projet-brainwave/projet-brainwave-quickstart.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/project-brainwave/project-brainwave-quickstart.ipynb).

Consultez ce bloc-notes :

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]
