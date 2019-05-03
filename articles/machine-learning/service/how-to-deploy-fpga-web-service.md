---
title: Déployer des modèles sur des FPGA
titleSuffix: Azure Machine Learning service
description: Découvrez comment déployer un service web avec un modèle s’exécutant sur un FPGA avec Azure Machine Learning service pour l’inférence de très faible latence.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: tedway
author: tedway
ms.date: 05/02/2019
ms.custom: seodec18
ms.openlocfilehash: cfe21d2119b92665c5950d792dec6500257c6316
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65024178"
---
# <a name="deploy-a-model-as-a-web-service-on-an-fpga-with-azure-machine-learning-service"></a>Comment déployer un modèle en tant que service web sur un FPGA avec Azure Machine Learning service

Vous pouvez déployer un modèle comme un service web sur [champ FPGA programmables (des FPGA)](concept-accelerate-with-fpgas.md) avec des modèles d’accélérée de matériel Azure Machine Learning. L’utilisation de FGPA assure une inférence à très faible latence, même avec une taille de lot unique.

Ces modèles sont actuellement disponibles :
  - ResNet 50
  - ResNet 152
  - DenseNet-121
  - VGG-16
  - SSD-VGG

L’appel à des FPGA est disponibles dans ces régions Azure :
  - USA Est
  - USA Ouest 2
  - Europe Ouest
  - Asie Sud-Est

> [!IMPORTANT]
> Pour optimiser le débit et la latence, votre client qui envoie des données au modèle FPGA doit être dans une des régions ci-dessus (celui qui) que vous avez déployé le modèle).

## <a name="prerequisites"></a>Conditions préalables

- Si vous n’avez pas d’abonnement Azure, créez un compte gratuit avant de commencer. Essayez la [version gratuite ou payante d’Azure Machine Learning service](https://aka.ms/AMLFree) dès aujourd’hui.

- Un espace de travail du service Azure Machine Learning et le kit SDK Azure Machine Learning pour Python installé. Découvrez comment obtenir ces prérequis à l’aide du document [Guide pratique pour configurer un environnement de développement](how-to-configure-environment.md).
 
  - Installer le kit SDK Python pour les modèles avec accélération matérielle :

    ```shell
    pip install --upgrade azureml-accel-models
    ```

## <a name="sample-notebooks"></a>Exemples de bloc-notes

Pour votre commodité, [exemples de blocs-notes](https://aka.ms/aml-notebooks) sont disponibles pour l’exemple ci-dessous et en plus des autres exemples.  Regardez sous les dossiers how-to-utilisation-azureml et de déploiement pour les modèles accélérées.

## <a name="create-and-containerize-your-model"></a>Créer et mettre en conteneur de votre modèle

Ce document sera décrivent comment créer un graphique de TensorFlow pour l’image d’entrée de prétraitement, rendre un préapprentissage à l’aide de ResNet 50 sur un FPGA et ensuite exécuter les fonctionnalités via un classifieur formé sur le jeu de données ImageNet.

Suivez les instructions pour :

* Définir le modèle TensorFlow
* Déployer le modèle
* Utiliser le modèle déployé
* Supprimer les services déployés

### <a name="load-azure-ml-workspace"></a>Charger l’espace de travail Azure ML

Charger votre espace de travail Azure ML.

```python
import os
import tensorflow as tf
 
from azureml.core import Workspace
 
ws = Workspace.from_config()
print(ws.name, ws.resource_group, ws.location, ws.subscription_id, sep = '\n')
```

### <a name="preprocess-image"></a>Prétraiter l’image

L’entrée au service web est une image JPEG.  La première étape consiste à décoder l’image JPEG et prétraiter.  Les images JPEG sont traités comme chaînes et le résultat sont TENSEURS qui seront l’entrée au modèle ResNet 50.

```python
# Input images as a two-dimensional tensor containing an arbitrary number of images represented a strings
import azureml.accel.models.utils as utils
in_images = tf.placeholder(tf.string)
image_tensors = utils.preprocess_array(in_images)
print(image_tensors.shape)
```

### <a name="load-featurizer"></a>Préapprentissage de charge

Initialisez le modèle et téléchargez un point de contrôle TensorFlow de la version quantifiée de ResNet50 à utiliser comme caractériseur.  Vous pouvez remplacer « QuantizedResnet50 » dans l’extrait de code ci-dessous avec en important des autres réseaux neuronaux en profondeur :

- QuantizedResnet152
- QuantizedVgg16
- Densenet121

```python
from azureml.accel.models import QuantizedResnet50
save_path = os.path.expanduser('~/models')
model_graph = QuantizedResnet50(save_path, is_frozen = True)
feature_tensor = model_graph.import_graph_def(image_tensors)
print(model_graph.version)
print(feature_tensor.name)
print(feature_tensor.shape)
```

### <a name="add-classifier"></a>Ajouter le classifieur

Ce classifieur a été entraîné sur le jeu de données ImageNet.  Exemples de transfert d’apprentissage et de formation votre poids personnalisés sont disponibles dans l’ensemble de [exemples de blocs-notes](https://aka.ms/aml-notebooks).

```python
classifier_output = model_graph.get_default_classifier(feature_tensor)
print(classifier_output)
```

### <a name="save-the-model"></a>Enregistrer le modèle

Maintenant que le préprocesseur, ResNet 50 préapprentissage et le classifieur ont été chargés, vous devez enregistrer le graphique et les variables associées en tant que modèle.

```python
model_name = "resnet50"
model_def_path = os.path.join(save_path, model_name)
print("Saving model in {}".format(model_def_path))

with tf.Session() as sess:
    model_graph.restore_weights(sess)
    tf.saved_model.simple_save(sess, model_def_path,
                                   inputs={'images': in_images},
                                   outputs={'output_alias': classifier_output})
```

### <a name="register-model"></a>Inscrire le modèle

[Inscrire](./concept-model-management-and-deployment.md) le modèle que vous avez créé.  Ajout de balises et autres métadonnées relatives au modèle vous permet d’effectuer le suivi de vos modèles formés.

```python
from azureml.core.model import Model

registered_model = Model.register(workspace = ws
                                  model_path = model_def_path,
                                  model_name = model_name)

print("Successfully registered: ", registered_model.name, registered_model.description, registered_model.version, sep = '\t')
```

Si vous avez déjà enregistré un modèle et que vous souhaitez charger, vous pouvez l’extraire.

```python
from azureml.core.model import Model
model_name = "resnet50"
# By default, the latest version is retrieved. You can specify the version, i.e. version=1
registered_model = Model(ws, name="resnet50")
print(registered_model.name, registered_model.description, registered_model.version, sep = '\t')
```

### <a name="convert-model"></a>Convertir en modèle

Le graphique de TensorFlow doit être converti au format d’échange de réseau neuronal ouvert ([ONNX](https://onnx.ai/)).  Vous devez fournir les noms de l’entrée et de sortie TENSEURS ; ces noms seront utilisées par votre client lorsque vous consommez le service web.

```python
input_tensor = in_images.name
output_tensors = classifier_output.name

print(input_tensor)
print(output_tensors)


from azureml.accel.accel_onnx_converter import AccelOnnxConverter

convert_request = AccelOnnxConverter.convert_tf_model(ws, registered_model, input_tensor, output_tensors)
convert_request.wait_for_completion(show_output=True)

# If the above call succeeded, get the converted model
converted_model = convert_request.result
print(converted_model.name, converted_model.url, converted_model.version, converted_model.id,converted_model.created_time)
```

### <a name="create-docker-image"></a>Créer image Docker

Le modèle converti et toutes les dépendances sont ajoutés à une image Docker.  Cette image Docker peut être déployée et instanciée dans le cloud ou un périphérique de périmètre pris en charge comme [zone de données Azure Edge](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview).  Vous pouvez également ajouter des balises et descriptions pour votre image Docker inscrit.

```python
from azureml.core.image import Image
from azureml.accel.accel_container_image import AccelContainerImage

image_config = AccelContainerImage.image_configuration()
image_name = "{}-image".format(model_name)

image = Image.create(name = image_name,
                     models = [converted_model],
                     image_config = image_config, 
                     workspace = ws)


image.wait_for_creation(show_output = True)
```

Répertorier les images par balise et obtient les journaux détaillés pour tout le débogage.

```python
for i in Image.list(workspace = ws):
    print('{}(v.{} [{}]) stored at {} with build log {}'.format(i.name, i.version, i.creation_state, i.image_location, i.image_build_log_uri))
```

## <a name="model-deployment"></a>Déploiement de modèle

### <a name="deploy-to-the-cloud"></a>Déployer vers le cloud

Pour déployer votre modèle en tant que service web de production à grande échelle, utilisez Azure Kubernetes Service (AKS). Vous pouvez créer un à l’aide du Kit de développement logiciel Azure Machine Learning, CLI ou le portail Azure.

```python
# Use the default configuration (can also provide parameters to customize)
prov_config = AksCompute.provisioning_configuration()

aks_name = 'my-aks-9' 
# Create the cluster
aks_target = ComputeTarget.create(workspace = ws, 
                                  name = aks_name, 
                                  provisioning_configuration = prov_config)

%%time
aks_target.wait_for_completion(show_output = True)
print(aks_target.provisioning_state)
print(aks_target.provisioning_errors)

#Set the web service configuration (using default here)
aks_config = AksWebservice.deploy_configuration()

%%time
aks_service_name ='aks-service-1'

aks_service = Webservice.deploy_from_image(workspace = ws, 
                                           name = aks_service_name,
                                           image = image,
                                           deployment_config = aks_config,
                                           deployment_target = aks_target)
aks_service.wait_for_deployment(show_output = True)
print(aks_service.state)
print(aks_service.scoring_uri)
```

#### <a name="test-the-cloud-service"></a>Tester le service cloud
L’image Docker prend en charge gRPC et TensorFlow service « prédire « API.  Utilisez l’exemple de client pour appeler dans l’image Docker pour obtenir des prédictions à partir du modèle.  Exemple de code client est disponible :
- [Python](https://github.com/Azure/aml-real-time-ai/blob/master/pythonlib/amlrealtimeai/client.py)
- [C#](https://github.com/Azure/aml-real-time-ai/blob/master/sample-clients/csharp)

Si vous souhaitez utiliser à servant de TensorFlow, vous pouvez [télécharger un exemple de client](https://www.tensorflow.org/serving/setup).

```python
import requests
classes_entries = requests.get("https://raw.githubusercontent.com/Lasagne/Recipes/master/examples/resnet50/imagenet_classes.txt").text.splitlines()

# Score image using input and output tensor names
results = client.score_file(path="./snowleopardgaze.jpg", 
                             input_name=input_tensor, 
                             outputs=output_tensors)

# map results [class_id] => [confidence]
results = enumerate(results)
# sort results by confidence
sorted_results = sorted(results, key=lambda x: x[1], reverse=True)
# print top 5 results
for top in sorted_results[:5]:
    print(classes_entries[top[0]], 'confidence:', top[1])
```

### <a name="clean-up-the-service"></a>Le service de nettoyage
Supprimer votre service web, une image et un modèle (doit être effectuée dans cet ordre dans la mesure où il existe des dépendances).

```python
aks_service.delete()
image.delete()
registered_model.delete()
converted_model.delete()
```

## <a name="deploy-to-a-local-edge-server"></a>Déployer sur un serveur de périphérie locaux

Tous les [périphériques de périmètre de zone de données Azure](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview
) contiennent un FPGA pour le modèle en cours d’exécution.  Un seul modèle peut être en cours d’exécution sur le FPGA en même temps.  Pour exécuter un modèle différent, déployez simplement un nouveau conteneur. Vous trouverez les instructions et des exemples de code dans [cet exemple Azure](https://github.com/Azure-Samples/aml-hardware-accelerated-models).

## <a name="secure-fpga-web-services"></a>Sécuriser les services web FPGA

Pour plus d’informations sur la sécurisation des services web FPGA, consultez le document [Sécuriser des services web](how-to-secure-web-service.md).