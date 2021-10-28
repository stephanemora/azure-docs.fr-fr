---
title: Configurer AutoML pour la vision par ordinateur
titleSuffix: Azure Machine Learning
description: Configurez le ML automatisé Azure Machine Learning pour entraîner des modèles de vision par ordinateur avec le SDK Python Azure Machine Learning (préversion).
services: machine-learning
author: swatig007
ms.author: swatig
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.custom: automl
ms.date: 10/06/2021
ms.openlocfilehash: 312eea28371de6726bca5d1a89e305024e552dad
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130254362"
---
# <a name="set-up-automl-to-train-computer-vision-models-with-python-preview"></a>Configurer AutoML pour entraîner des modèles de vision par ordinateur avec Python (préversion)

> [!IMPORTANT]
> Cette fonctionnalité est actuellement disponible en préversion publique. Cette préversion est fournie sans contrat de niveau de service. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Dans cet article, vous allez apprendre à entraîner des modèles de vision par ordinateur sur des données d’image avec le ML automatisé du [kit SDK Python Azure Machine Learning](/python/api/overview/azure/ml/).

Le ML automatisé prend en charge l’entraînement de modèles pour les tâches de vision par ordinateur telles que la classification d’images, la détection d’objets et la segmentation d’instances. La création de modèles AutoML pour les tâches de vision par ordinateur est actuellement prise en charge via le kit SDK Python Azure Machine Learning. Les exécutions, modèles et sorties des expérimentations sont accessibles dans l’interface utilisateur du studio Azure Machine Learning. [En savoir plus sur le ML automatisé pour les tâches de vision par ordinateur sur les données d’image](concept-automated-ml.md).

> [!NOTE]
> Le ML automatisé pour les tâches de vision par ordinateur est disponible uniquement via le kit SDK Python Azure Machine Learning. 

## <a name="prerequisites"></a>Prérequis

* Un espace de travail Azure Machine Learning. Pour créer l’espace de travail, voir [Créer un espace de travail Azure Machine Learning](how-to-manage-workspace.md).

* Le kit SDK Python d’Azure Machine Learning installé.
    Pour installer le kit de développement logiciel (SDK), vous pouvez : 
    * Créer une instance de calcul, qui installe automatiquement le kit SDK et est préconfigurée pour les workflows ML. Pour plus d’informations, consultez [Créer et gérer une instance de calcul Azure Machine Learning](how-to-create-manage-compute-instance.md).

    * [Installez vous-même le package `automl`](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/README.md#setup-using-a-local-conda-environment), qui comprend l’[installation par défaut](/python/api/overview/azure/ml/install#default-install) du Kit de développement logiciel (SDK).
    
    > [!NOTE]
    > Seuls Python 3.6 et 3.7 sont compatibles avec la prise en charge du ML automatisé pour les tâches de vision par ordinateur. 

## <a name="select-your-task-type"></a>Sélectionner votre type de tâche
Le ML automatisé pour les images prend en charge les types de tâches suivants :


Type de tâche | Syntaxe de la configuration d’AutoMLImage
---|---
 classification d’image | `ImageTask.IMAGE_CLASSIFICATION`
classification d’images multi-étiquette | `ImageTask.IMAGE_CLASSIFICATION_MULTILABEL`
détection d’objets image | `ImageTask.IMAGE_OBJECT_DETECTION`
segmentation d’instances d’image| `ImageTask.IMAGE_INSTANCE_SEGMENTATION`

Ce type de tâche est un paramètre obligatoire et est transmis à l’aide du paramètre `task` d’`AutoMLImageConfig`. Par exemple :

```python
from azureml.train.automl import AutoMLImageConfig
from azureml.automl.core.shared.constants import ImageTask
automl_image_config = AutoMLImageConfig(task=ImageTask.IMAGE_OBJECT_DETECTION)
```

## <a name="training-and-validation-data"></a>Données de formation et de validation

Pour générer des modèles de vision par ordinateur, vous devez apporter les données d’image étiquetées comme entrées pour l’entraînement de modèles sous la forme d’un [TabularDataset](/python/api/azureml-core/azureml.data.tabulardataset) Azure Machine Learning. Vous pouvez utiliser soit un `TabularDataset` que vous avez [exporté à partir d’un projet d’étiquetage de données](./how-to-create-image-labeling-projects.md#export-the-labels), soit créer un nouveau `TabularDataset` avec vos données d’entraînement étiquetées. 

Si vos données d’entraînement sont dans un autre format (par exemple, pascal VOC ou COCO), vous pouvez appliquer les scripts d’assistance inclus avec les exemples de notebook pour convertir les données en JSONL. En savoir plus sur la [préparation des données pour les tâches de vision par ordinateur avec le ML automatisé](how-to-prepare-datasets-for-automl-images.md). 

> [!Warning]
> La création de TabularDatasets est prise en charge uniquement avec le kit SDK pour créer des jeux de données à partir de données au format JSONL pour cette fonctionnalité. La création du jeu de données via l’interface utilisateur n’est pas prise en charge pour l’instant.


### <a name="jsonl-schema-samples"></a>Exemples de schémas JSONL

La structure du TabularDataset dépend de la tâche en cours. Pour les types de tâche de vision par ordinateur, elle comprend les champs suivants :

Champ| Description
---|---
`image_url`| Contient le chemin du fichier comme objet StreamInfo
`image_details`|Les informations de métadonnées d’image se composent de la hauteur, de la largeur et du format. Ce champ est facultatif et peut donc exister ou pas.
`label`| Représentation JSON de l’étiquette de l’image, en fonction du type de tâche.

Voici un exemple de fichier JSONL pour la classification d’images :

```python
{
      "image_url": "AmlDatastore://image_data/Image_01.png",
      "image_details":
      {
          "format": "png",
          "width": "2230px",
          "height": "4356px"
      },
      "label": "cat"
  }
  {
      "image_url": "AmlDatastore://image_data/Image_02.jpeg",
      "image_details":
      {
          "format": "jpeg",
          "width": "3456px",
          "height": "3467px"
      },
      "label": "dog"
  }
  ```

  Le code suivant est un exemple de fichier JSONL pour la détection d’objets :

  ```python
  {
      "image_url": "AmlDatastore://image_data/Image_01.png",
      "image_details":
      {
          "format": "png",
          "width": "2230px",
          "height": "4356px"
      },
      "label":
      {
          "label": "cat",
          "topX": "1",
          "topY": "0",
          "bottomX": "0",
          "bottomY": "1",
          "isCrowd": "true",
      }
  }
  {
      "image_url": "AmlDatastore://image_data/Image_02.png",
      "image_details":
      {
          "format": "jpeg",
          "width": "1230px",
          "height": "2356px"
      },
      "label":
      {
          "label": "dog",
          "topX": "0",
          "topY": "1",
          "bottomX": "0",
          "bottomY": "1",
          "isCrowd": "false",
      }
  }
  ```


### <a name="consume-data"></a>Consommer les données

Une fois que vos données sont au format JSONL, vous pouvez créer un TabularDataset avec le code suivant :

```python
from azureml.core import Dataset

training_dataset = Dataset.Tabular.from_json_lines_files(
        path=ds.path('odFridgeObjects/odFridgeObjects.jsonl'),
        set_column_types={'image_url': DataType.to_stream(ds.workspace)})
training_dataset = training_dataset.register(workspace=ws, name=training_dataset_name)
```

Le ML automatisé n’impose aucune contrainte sur la taille des données d’entraînement ou de validation pour les tâches de vision par ordinateur. La taille maximale d’un jeu de données est seulement limitée par la couche de stockage derrière le jeu de données (par exemple, le magasin d’objets blob). Il n’y a aucun nombre minimal d’images ou d’étiquettes. Toutefois, nous vous recommandons de commencer avec un minimum de 10-15 échantillons par étiquette pour vous assurer que le modèle de sortie est suffisamment entraîné. Plus le nombre total d’étiquettes/classes est élevé, plus vous avez besoin d’échantillons par étiquette.



Les données d’entraînement sont obligatoires et passées à l’aide du paramètre `training_data`. Vous pouvez éventuellement spécifier un autre TabularDataset comme jeu de données de validation à utiliser pour votre modèle avec le paramètre `validation_data` d’AutoMLImageConfig. Si aucun jeu de données de validation n’est spécifié, 20 % de vos données d’entraînement sont utilisées pour la validation par défaut, sauf si vous passez l’argument `split_ratio` avec une valeur différente.

Par exemple :

```python
from azureml.train.automl import AutoMLImageConfig
automl_image_config = AutoMLImageConfig(training_data=training_dataset)
```

## <a name="compute-to-run-experiment"></a>Capacité de calcul pour exécuter l’expérience

Fournissez une [cible de calcul](concept-azure-machine-learning-architecture.md#compute-targets) pour le ML automatisé afin de procéder à l’entraînement des modèles. Les modèles du ML automatisé pour les tâches de vision par ordinateur requièrent des références SKU GPU et prennent en charge les familles NC et ND. Nous recommandons d’utiliser la série NCsv3 (avec v100 GPU) pour accélérer l’entraînement. Une cible de calcul avec une référence SKU de machine virtuelle multi-GPU utilise plusieurs GPU pour également accélérer l’entraînement. De plus, lorsque vous configurez une cible de calcul avec plusieurs nœuds, vous pouvez procéder à un entraînement plus rapide des modèles via le parallélisme lors du réglage des hyperparamètres de votre modèle.

La cible de calcul est un paramètre obligatoire et est passée à l’aide du paramètre `compute_target` d’`AutoMLImageConfig`. Par exemple :

```python
from azureml.train.automl import AutoMLImageConfig
automl_image_config = AutoMLImageConfig(compute_target=compute_target)
```

## <a name="configure-model-algorithms-and-hyperparameters"></a>Configurer les algorithmes et les hyperparamètres du modèle

Avec la prise en charge des tâches de vision par ordinateur, vous pouvez contrôler l’algorithme du modèle et balayer les hyperparamètres. Ces algorithmes et hyperparamètres du modèle sont passés comme espace de paramètres pour le balayage.

L’algorithme du modèle est obligatoire et est passé via le paramètre `model_name`. Vous pouvez spécifier un seul `model_name` ou choisir entre plusieurs. En plus du contrôle de l’algorithme du modèle, vous pouvez également régler les hyperparamètres utilisés pour l’entraînement du modèle. Même si un grand nombre des hyperparamètres exposés sont indépendants du modèle, il existe des cas où les hyperparamètres sont spécifiques à une tâche ou à un modèle.

### <a name="supported-model-algorithms"></a>Algorithmes de modèle pris en charge

Le tableau suivant récapitule les modèles pris en charge pour chaque tâche de vision par ordinateur. 

Tâche |  Algorithmes de modèle | Syntaxe des littéraux de chaîne<br> ***`default_model`\**** marqué d’un \*
---|----------|----------
Classification d’images<br> (multi-classe et multi-étiquette)| **MobileNet** : modèles légers pour applications mobiles <br> **ResNet** : réseaux résiduels<br> **ResNeSt** : réseaux d’attention partagée<br> **SE-ResNeXt50** : réseaux de compression et excitation (Squeeze-and-Excitation)<br> **ViT** : réseaux de transformateurs de vision| `mobilenetv2`   <br>`resnet18` <br>`resnet34` <br> `resnet50`  <br> `resnet101` <br> `resnet152`    <br> `resnest50` <br> `resnest101`  <br> `seresnext`  <br> `vits16r224` (petit) <br> **_`vitb16r224`\_** * (base) <br>`vitl16r224` (grand)|
Détection d’objets | **YOLOv5** : modèle de détection d’objets monophase   <br>  **Faster RCNN ResNet FPN** : modèles de détection d’objets double-phase  <br> **RetinaNet ResNet FPN** : résolution du déséquilibre de classes avec la perte focale <br> <br>*Remarque : Reportez-vous à l’[hyperparamètre `model_size`](#model-specific-hyperparameters) pour les tailles de modèle YOLOv5.*| ***`yolov5`\**** <br> `fasterrcnn_resnet18_fpn` <br> `fasterrcnn_resnet34_fpn` <br> `fasterrcnn_resnet50_fpn` <br> `fasterrcnn_resnet101_fpn` <br> `fasterrcnn_resnet152_fpn` <br> `retinanet_resnet50_fpn` 
Segmentation d’instances | **MaskRCNN ResNet FPN**| `maskrcnn_resnet18_fpn` <br> `maskrcnn_resnet34_fpn` <br> **_`maskrcnn_resnet50_fpn`\_***  <br> `maskrcnn_resnet101_fpn` <br> `maskrcnn_resnet152_fpn` <br>`maskrcnn_resnet50_fpn`


### <a name="model-agnostic-hyperparameters"></a>Hyperparamètres indépendants des modèles

Le tableau suivant décrit les hyperparamètres qui sont indépendants des modèles.

| Nom du paramètre | Description | Default|
| ------------ | ------------- | ------------ |
| `number_of_epochs` | Nombre d’époques d’entraînement. <br>Cette valeur doit être un entier positif. |  15 <br> (sauf `yolov5` : 30) |
| `training_batch_size` | Taille du lot d’entraînement.<br> Cette valeur doit être un entier positif.  | Multi-classe/multi-étiquette : 78 <br>(sauf *vit-variants* : <br> `vits16r224` : 128 <br>`vitb16r224` : 48 <br>`vitl16r224` :10)<br><br>Détection d’objets : 2 <br>(sauf `yolov5` : 16) <br><br> Segmentation d’instances : 2  <br> <br> *Remarque : Les valeurs par défaut correspondent à la plus grande taille de lot qui peut être utilisée sur une mémoire GPU de 12 Gio*.|
| `validation_batch_size` | Taille du lot de validation.<br> Cette valeur doit être un entier positif. | Multi-classe/multi-étiquette : 78 <br>(sauf *vit-variants* : <br> `vits16r224` : 128 <br>`vitb16r224` : 48 <br>`vitl16r224` :10)<br><br>Détection d’objets :1 <br>(sauf `yolov5` : 16) <br><br> Segmentation d’instances : 1  <br> <br> *Remarque : Les valeurs par défaut correspondent à la plus grande taille de lot qui peut être utilisée sur une mémoire GPU de 12 Gio*.|
| `grad_accumulation_step` | L’accumulation de gradients consiste à exécuter un nombre configuré de `grad_accumulation_step` sans mettre à jour les pondérations du modèle tout en accumulant les gradients de ces étapes, puis en utilisant les gradients accumulés pour calculer les mises à jour des pondérations. <br> Cette valeur doit être un entier positif. | 1 |
| `early_stopping` | Permet d’avoir une logique d’arrêt anticipé au cours de l’entraînement. <br> Doit être 0 ou 1.| 1 |
| `early_stopping_patience` | Nombre minimal d’époques ou d’évaluations de validation avec<br>aucune amélioration de métrique principale avant l’arrêt de l’exécution.<br> Cette valeur doit être un entier positif. | 5 |
| `early_stopping_delay` | Nombre minimal d’époques ou d’évaluations de validation à attendre<br>avant que l’amélioration de la métrique principale ne soit suivie pour un arrêt anticipé.<br> Cette valeur doit être un entier positif. | 5 |
| `learning_rate` | Taux d’apprentissage initial. <br>Doit être un nombre à virgule flottante dans la plage [0, 1]. | Multi-classe : 0.01 <br>(sauf *vit-variants* : <br> `vits16r224` : 0.0125<br>`vitb16r224` : 0.0125<br>`vitl16r224` : 0.001) <br><br> Multi-étiquette : 0.035 <br>(sauf *vit-variants* :<br>`vits16r224` : 0.025<br>`vitb16r224` : 0.025 <br>`vitl16r224` : 0.002) <br><br> Détection d’objets : 0.005 <br>(sauf `yolov5` : 0.01) <br><br> Segmentation d’instances : 0.005  |
| `lr_scheduler` | Type de planificateur du taux d’apprentissage. <br> Doit être `warmup_cosine` ou `step`. | `warmup_cosine` |
| `step_lr_gamma` | Valeur de gamma quand le planificateur du taux d’apprentissage est `step`.<br> Doit être un nombre à virgule flottante dans la plage [0, 1]. | 0.5 |
| `step_lr_step_size` | Valeur de la taille de l’étape (« step ») quand le planificateur du taux d’apprentissage est `step`.<br> Cette valeur doit être un entier positif. | 5 |
| `warmup_cosine_lr_cycles` | Valeur du cycle de cosinus quand le planificateur du taux d’apprentissage est `warmup_cosine`. <br> Doit être un nombre à virgule flottante dans la plage [0, 1]. | 0,45 |
| `warmup_cosine_lr_warmup_epochs` | Valeur des époques de préchauffage (warmup) quand le planificateur du taux d’apprentissage est `warmup_cosine`. <br> Cette valeur doit être un entier positif. | 2 |
| `optimizer` | Type d’optimiseur. <br> Doit être `sgd`, `adam`, `adamw`.  | `sgd` |
| `momentum` | Valeur du momentum quand l’optimiseur est `sgd`. <br> Doit être un nombre à virgule flottante dans la plage [0, 1]. | 0.9 |
| `weight_decay` | Valeur de la perte de poids lorsque l’optimiseur est `sgd`, `adam` ou `adamw`. <br> Doit être un nombre à virgule flottante dans la plage [0, 1]. | 1e-4 |
|`nesterov`| Activez `nesterov` quand l’optimiseur est `sgd`. <br> Doit être 0 ou 1.| 1 |
|`beta1` | Valeur de `beta1` quand l’optimiseur est `adam` ou `adamw`. <br> Doit être un nombre à virgule flottante dans la plage [0, 1]. | 0.9 |
|`beta2` | Valeur de `beta2` quand l’optimiseur est `adam` ou `adamw`.<br> Doit être un nombre à virgule flottante dans la plage [0, 1]. | 0.999 |
|`amsgrad` | Activez `amsgrad` quand l’optimiseur est `adam` ou `adamw`.<br> Doit être 0 ou 1. | 0 |
|`evaluation_frequency`| Fréquence d’évaluation du jeu de données de validation pour obtenir les scores de métrique. <br> Cette valeur doit être un entier positif. | 1 |
|`split_ratio`| Si les données de validation ne sont pas définies, spécifie le taux de division pour diviser les données d’entraînement en sous-ensembles d’entraînement et de validation aléatoires. <br> Doit être un nombre à virgule flottante dans la plage [0, 1].| 0.2 |
|`checkpoint_frequency`| Fréquence de stockage des points de contrôle du modèle. <br> Cette valeur doit être un entier positif. | Point de contrôle au niveau de l’époque avec la meilleure métrique principale lors de la validation.|
|`layers_to_freeze`| Nombre de couches à geler pour votre modèle. Par exemple, le fait de passer 2 comme valeur pour `seresnext` signifie figer couche0 et couche1 qui font référence aux informations de couche du modèle prises en charge ci-dessous. <br> Cette valeur doit être un entier positif. <br><br>`'resnet': [('conv1.', 'bn1.'), 'layer1.', 'layer2.', 'layer3.', 'layer4.'],`<br>`'mobilenetv2': ['features.0.', 'features.1.', 'features.2.', 'features.3.', 'features.4.', 'features.5.', 'features.6.', 'features.7.', 'features.8.', 'features.9.', 'features.10.', 'features.11.', 'features.12.', 'features.13.', 'features.14.', 'features.15.', 'features.16.', 'features.17.', 'features.18.'],`<br>`'seresnext': ['layer0.', 'layer1.', 'layer2.', 'layer3.', 'layer4.'],`<br>`'vit': ['patch_embed', 'blocks.0.', 'blocks.1.', 'blocks.2.', 'blocks.3.', 'blocks.4.', 'blocks.5.', 'blocks.6.','blocks.7.', 'blocks.8.', 'blocks.9.', 'blocks.10.', 'blocks.11.'],`<br>`'yolov5_backbone': ['model.0.', 'model.1.', 'model.2.', 'model.3.', 'model.4.','model.5.', 'model.6.', 'model.7.', 'model.8.', 'model.9.'],`<br>`'resnet_backbone': ['backbone.body.conv1.', 'backbone.body.layer1.', 'backbone.body.layer2.','backbone.body.layer3.', 'backbone.body.layer4.']` | pas de valeur par défaut  |


### <a name="task-specific-hyperparameters"></a>Hyperparamètres spécifiques aux tâches

Le tableau suivant récapitule les hyperparamètres pour les tâches de classification d’images (multi-classe et multi-étiquette).


| Nom du paramètre       | Description           | Default  |
| ------------- |-------------|-----|
| `weighted_loss` | 0 pour aucune perte de poids.<br>1 pour une perte de poids avec sqrt.(class_weights) <br> 2 pour une perte de poids avec class_weights. <br> Doit être 0 ou 1 ou 2. | 0 |
| `valid_resize_size` | Taille d’image à laquelle redimensionner avant le rognage du jeu de données de validation. <br> Cette valeur doit être un entier positif. <br> <br> *Remarques : <li> `seresnext` ne prend aucune taille arbitraire. <li> L’exécution de l’entraînement peut aboutir à un manque de mémoire de CUDA si la taille est trop grande*.  | 256  |
| `valid_crop_size` | Taille de rognage d’image qui est entrée dans votre réseau neuronal pour le jeu de données de validation.  <br> Cette valeur doit être un entier positif. <br> <br> *Remarques : <li>`seresnext` ne prend aucune taille arbitraire. <li> *ViT-variants* doit avoir les mêmes `valid_crop_size` et `train_crop_size`. <li> L’exécution de l’entraînement peut aboutir à un manque de mémoire de CUDA si la taille est trop grande*. | 224 |
| `train_crop_size` | Taille de rognage d’image qui est entrée dans votre réseau neuronal pour le jeu de données d’entraînement.  <br> Cette valeur doit être un entier positif. <br> <br> *Remarques : <li>`seresnext` ne prend aucune taille arbitraire. <li> *ViT-variants* doit avoir les mêmes `valid_crop_size` et `train_crop_size`. <li> L’exécution de l’entraînement peut aboutir à un manque de mémoire de CUDA si la taille est trop grande*. | 224 |


Les hyperparamètres suivants concernent les tâches de détection d’objets et de segmentation d’instances.

> [!Warning]
> Ces paramètres ne sont pas pris en charge avec l’algorithme `yolov5`.

| Nom du paramètre       | Description           | Default  |
| ------------- |-------------|-----|
| `validation_metric_type` | Méthode de calcul de métrique à utiliser pour les métriques de validation.  <br> Doit être `none`, `coco`, `voc` ou `coco_voc`. | `voc` |
| `min_size` | Taille minimale de l’image à remettre à l’échelle avant de l’alimenter à l’épine dorsale (backbone). <br> Cette valeur doit être un entier positif. <br> <br> *Remarque : L’exécution de l’entraînement peut aboutir à un manque de mémoire de CUDA si la taille est trop grande*.| 600 |
| `max_size` | Taille maximale de l’image à remettre à l’échelle avant de l’alimenter à l’épine dorsale (backbone). <br> Cette valeur doit être un entier positif.<br> <br> *Remarque : L’exécution de l’entraînement peut aboutir à un manque de mémoire de CUDA si la taille est trop grande*. | 1333 |
| `box_score_thresh` | Pendant l’inférence, retourne uniquement les propositions dont le score de classification est supérieur à `box_score_thresh`. <br> Doit être un nombre à virgule flottante dans la plage [0, 1].| 0.3 |
| `box_nms_thresh` | Seuil de suppression non maximale (NMS) pour la tête de prédiction. Utilisé pendant l’inférence.  <br>Doit être un nombre à virgule flottante dans la plage [0, 1]. | 0.5 |
| `box_detections_per_img` | Nombre maximal de détections par image, pour toutes les classes. <br> Cette valeur doit être un entier positif.| 100 |
| `tile_grid_size` | Taille de la grille à utiliser pour la mise en mosaïque de chaque image. <br>*Remarque : tile_grid_size ne doit pas être None pour permettre [une logique de](how-to-use-automl-small-object-detect.md)détection de petits objets*<br> Tuple de deux entiers passés sous forme de chaîne. Exemple : --tile_grid_size "(3, 2)" | Pas de valeur par défaut |
| `tile_overlap_ratio` | Ratio de chevauchement entre les mosaïques adjacentes dans chaque dimension. <br> Doit être un nombre à virgule flottante dans la plage de [0, 1]. | 0,25 |
| `tile_predictions_nms_thresh` | Seuil IOU à utiliser pour effectuer une suppression non maximale tout en fusionnant les prédictions des mosaïques et de l’image. Utilisé dans la validation/l’inférence. <br> Doit être un nombre à virgule flottante dans la plage de [0, 1]. | 0,25 |

### <a name="model-specific-hyperparameters"></a>Hyperparamètres spécifiques aux modèles

Ce tableau récapitule les hyperparamètres spécifiques à l’algorithme `yolov5`.

| Nom du paramètre       | Description           | Default  |
| ------------- |-------------|----|
| `validation_metric_type` | Méthode de calcul de métrique à utiliser pour les métriques de validation.  <br> Doit être `none`, `coco`, `voc` ou `coco_voc`. | `voc` |
| `img_size` | Taille d’image pour l’entraînement et la validation. <br> Cette valeur doit être un entier positif. <br> <br> *Remarque : L’exécution de l’entraînement peut aboutir à un manque de mémoire de CUDA si la taille est trop grande*. | 640 |
| `model_size` | Taille du modèle. <br> Doit être `small`, `medium`, `large` ou `xlarge`. <br><br> *Remarque : L’exécution de l’entraînement peut aboutir à un manque de mémoire de CUDA si la taille du modèle est trop grande*.  | `medium` |
| `multi_scale` | Permet d’avoir une image multi-échelle en variant la taille de l’image de +/-50 % <br> Doit être 0 ou 1. <br> <br> *Remarque : L’exécution de l’entraînement peut aboutir à un manque de mémoire de CUDA si la mémoire du GPU est insuffisante*. | 0 |
| `box_score_thresh` | Pendant l’inférence, retourne uniquement les propositions dont le score est supérieur à `box_score_thresh`. Le score correspond à la multiplication du score d’objet et de la probabilité de classification. <br> Doit être un nombre à virgule flottante dans la plage [0, 1]. | 0.1 |
| `box_iou_thresh` | Seuil IoU utilisé pendant l’inférence dans le cadre du post-traitement de la suppression non maximale. <br> Doit être un nombre à virgule flottante dans la plage [0, 1]. | 0.5 |


### <a name="data-augmentation"></a>Augmentation des données 

En général, les performances des modèles Deep Learning peuvent souvent s’améliorer avec davantage de données. L’augmentation des données est une technique pratique pour amplifier la taille des données et la variabilité d’un jeu de données, ce qui permet d’empêcher le surajustement et d’améliorer la capacité de généralisation du modèle sur les données non vues. Le ML automatisé applique différentes techniques d’augmentation des données en fonction de la tâche de vision par ordinateur, avant d’alimenter les images d’entrée dans le modèle. Actuellement, il n’y a pas d’hyperparamètre exposé pour contrôler les augmentations de données. 

|Tâche | Jeu de données impacté | Technique(s) d’augmentation des données appliquée(s) |
|-------|----------|---------|
|Classification d’images (multi-classe et multi-étiquette) | Entrainement <br><br><br> Validation et test| Redimensionnement et rognage aléatoires, retournement horizontal, variation des couleurs (luminosité, contraste, saturation et teinte), normalisation avec la moyenne et l’écart type d’ImageNet au niveau canal <br><br><br>Redimensionnement, rognage central, normalisation |
|Détection d’objets, segmentation d’instances| Entrainement <br><br> Validation et test |Rognage aléatoire autour des cadres englobants, développement, retournement horizontal, normalisation, redimensionnement <br><br><br>Normalisation, redimensionnement
|Détection d’objets avec yolov5| Entrainement <br><br> Validation et test  |Mise en mosaïque, affinité aléatoire (rotation, translation, mise à l’échelle, inclinaison), retournement horizontal <br><br><br> Redimensionnement des cadres|

## <a name="configure-your-experiment-settings"></a>Configurer les paramètres de votre expérience

Avant de faire un grand balayage pour rechercher les modèles et hyperparamètres optimaux, nous vous recommandons de tester les valeurs par défaut pour obtenir une première base de référence. Ensuite, vous pouvez explorer plusieurs hyperparamètres pour le même modèle avant de faire un balayage sur plusieurs modèles et leurs paramètres. De cette façon, vous pouvez utiliser une approche plus itérative parce qu’avec plusieurs modèles et plusieurs hyperparamètres pour chacun, l’espace de recherche augmente de manière exponentielle et vous avez besoin de plus d’itérations pour trouver des configurations optimales.

Si vous souhaitez utiliser les valeurs par défaut des hyperparamètres pour un algorithme donné (par exemple, yolov5), vous pouvez spécifier que la configuration de votre image AutoML s’exécute comme suit :

```python
from azureml.train.automl import AutoMLImageConfig
from azureml.train.hyperdrive import GridParameterSampling, choice
from azureml.automl.core.shared.constants import ImageTask

automl_image_config_yolov5 = AutoMLImageConfig(task=ImageTask.IMAGE_OBJECT_DETECTION,
                                               compute_target=compute_target,
                                               training_data=training_dataset,
                                               validation_data=validation_dataset,
                                               hyperparameter_sampling=GridParameterSampling({'model_name': choice('yolov5')}),
                                               iterations=1)
```

Une fois que vous avez créé un modèle de référence, vous voudrez peut-être optimiser les performances du modèle dans le but de faire un balayage sur l’espace de l’algorithme et des hyperparamètres du modèle. Vous pouvez utiliser l’exemple de configuration suivant pour balayer les hyperparamètres de chaque algorithme, en choisissant à partir d’une plage de valeurs pour learning_rate, optimizer, lr_scheduler, etc., afin de générer un modèle avec la métrique principale optimale. Si les valeurs d’hyperparamètres ne sont pas spécifiées, les valeurs par défaut sont utilisées pour l’algorithme spécifié.

### <a name="primary-metric"></a>Métrique principale

La métrique principale utilisée pour l’optimisation du modèle et le réglage des hyperparamètres dépend du type de tâche. L’utilisation d’autres valeurs de métriques principales n’est pas prise en charge actuellement. 

* `accuracy` pour IMAGE_CLASSIFICATION
* `iou` pour IMAGE_CLASSIFICATION_MULTILABEL
* `mean_average_precision` pour IMAGE_OBJECT_DETECTION
* `mean_average_precision` pour IMAGE_INSTANCE_SEGMENTATION
    
### <a name="experiment-budget"></a>Budget pour les expériences

Vous pouvez éventuellement spécifier le budget temps maximal pour votre expérience de vision AutoML avec `experiment_timeout_hours`, durée en heures avant l’arrêt de l’expérience. Si aucune durée n’est spécifiée, le délai d’expiration par défaut de l’expérience est de sept jours (maximum 60 jours).

## <a name="sweeping-hyperparameters-for-your-model"></a>Balayage des hyperparamètres pour votre modèle

Lors de l’entraînement des modèles de vision par ordinateur, les performances des modèles dépendent beaucoup des valeurs d’hyperparamètre sélectionnées. Souvent, vous voudrez probablement régler les hyperparamètres pour obtenir des performances optimales.
Avec la prise en charge des tâches de vision par ordinateur dans le ML automatisé, vous pouvez balayer les hyperparamètres pour trouver les paramètres optimaux pour votre modèle. Cette fonctionnalité applique les fonctions de réglage des hyperparamètres dans Azure Machine Learning. [Apprenez à régler les hyperparamètres](how-to-tune-hyperparameters.md).

### <a name="define-the-parameter-search-space"></a>Définir l’espace de recherche de paramètres

Vous pouvez définir les algorithmes et les hyperparamètres des modèles pour balayer l’espace de paramètres. Consultez [Configurer les algorithmes et les hyperparamètres des modèles](#configure-model-algorithms-and-hyperparameters) pour obtenir la liste des algorithmes et des hyperparamètres de modèle pris en charge pour chaque type de tâche. Consultez les [Détails sur les distributions prises en charge pour les hyperparamètres discrets et continus](how-to-tune-hyperparameters.md#define-the-search-space).


### <a name="sampling-methods-for-the-sweep"></a>Méthodes d’échantillonnage pour le balayage

Lors du balayage des hyperparamètres, vous devez spécifier la méthode d’échantillonnage à utiliser pour balayer l’espace de paramètres défini. Actuellement, les méthodes d’échantillonnage suivantes sont prises en charge avec le paramètre `hyperparameter_sampling` :

* [Échantillonnage aléatoire](how-to-tune-hyperparameters.md#random-sampling)
* [Échantillonnage par grille](how-to-tune-hyperparameters.md#grid-sampling) 
* [Échantillonnage bayésien](how-to-tune-hyperparameters.md#bayesian-sampling) 
    
Il convient de noter que seul l’échantillonnage aléatoire prend actuellement en charge les espaces conditionnels d’hyperparamètres.


### <a name="early-termination-policies"></a>Stratégies d’arrêt anticipé

Vous pouvez arrêter automatiquement les exécutions peu performantes avec une stratégie d’arrêt anticipé. Un arrêt anticipé améliore l’efficacité du calcul, en sauvant des ressources de calcul qui auraient autrement été consacrées à des configurations moins prometteuses. Le ML automatisé pour les images prend en charge les stratégies d’arrêt anticipé suivantes avec le paramètre `early_termination_policy`. Si aucune stratégie d’arrêt n’est spécifiée, toutes les configurations sont exécutées jusqu’à la fin.

* [Stratégie Bandit](how-to-tune-hyperparameters.md#bandit-policy)
* [Stratégie d’arrêt médiane](how-to-tune-hyperparameters.md#median-stopping-policy)
* [Stratégie de sélection de troncation](how-to-tune-hyperparameters.md#truncation-selection-policy)

Découvrez plus en détail comment [configurer la stratégie d’arrêt anticipé pour votre balayage d’hyperparamètres](how-to-tune-hyperparameters.md#early-termination).

### <a name="resources-for-the-sweep"></a>Ressources pour le balayage

Vous pouvez contrôler les ressources consacrées à votre balayage d’hyperparamètres en spécifiant `iterations` et `max_concurrent_iterations` pour le balayage.

Paramètre | Détail
-----|----
`iterations` |  Paramètre obligatoire pour le nombre maximal de configurations à balayer. Doit être un entier compris entre 1 et 1000. Lorsque vous explorez juste les hyperparamètres par défaut d’un algorithme de modèle donné, affectez la valeur 1 à ce paramètre.
`max_concurrent_iterations`| Nombre maximal d’exécutions pouvant se dérouler simultanément. En l’absence de spécification, toutes les exécutions s’exécutent en parallèle. En cas de spécification, doit être un entier compris entre 1 et 100.  <br><br> **REMARQUE :** Le nombre d’exécutions simultanées est limité par les ressources disponibles dans la cible de calcul spécifiée. Vérifiez que la cible de calcul dispose des ressources nécessaires à l’accès concurrentiel souhaité.


> [!NOTE]
> Pour obtenir un exemple complet de configuration de balayage, reportez-vous à ce [tutoriel](tutorial-auto-train-image-models.md#hyperparameter-sweeping-for-image-tasks).

### <a name="arguments"></a>Arguments

Vous pouvez passer en tant qu’arguments des paramètres fixes qui ne changent pas pendant le balayage de l’espace de paramètres. Les arguments sont passés dans les paires nom-valeur et le nom doit être préfixé avec un double tiret. 

```python
from azureml.train.automl import AutoMLImageConfig
arguments = ["--early_stopping", 1, "--evaluation_frequency", 2]
automl_image_config = AutoMLImageConfig(arguments=arguments)
```

## <a name="submit-the-run"></a>Soumettre l’exécution

Une fois votre objet `AutoMLImageConfig` prêt, vous pouvez soumettre l’expérience.

```python
ws = Workspace.from_config()
experiment = Experiment(ws, "Tutorial-automl-image-object-detection")
automl_image_run = experiment.submit(automl_image_config)
```
## <a name="outputs-and-evaluation-metrics"></a>Sorties et métriques d’évaluation

Les exécutions d’entraînement automl génèrent des fichiers de modèle de sortie, des métriques d’évaluation, des journaux et des artefacts de déploiement, comme le fichier de scoring et le fichier d’environnement, que vous pouvez consulter sous l’onglet des sorties, journaux et métriques des exécutions enfants.

> [!TIP]
> Regardez comment accéder aux résultats des exécutions à partir de la section [Consulter les résultats des exécutions](how-to-understand-automated-ml.md#view-run-results).

Pour obtenir des définitions et des exemples des graphiques et métriques de performances fournis pour chaque exécution, consultez [Évaluer les résultats de l’expérience de Machine Learning automatisé](how-to-understand-automated-ml.md#metrics-for-image-models-preview).

## <a name="register-and-deploy-model"></a>Inscrire et déployer un modèle

Une fois l’exécution terminée, vous pouvez enregistrer le modèle qui a été créé à partir de la meilleure exécution (configuration qui a généré la meilleure métrique principale).

```Python
best_child_run = automl_image_run.get_best_child()
model_name = best_child_run.properties['model_name']
model = best_child_run.register_model(model_name = model_name, model_path='outputs/model.pt')
```

Après avoir enregistré le modèle que vous voulez utiliser, vous pouvez le déployer en tant que service web sur [ACI (Azure Container Instances)](how-to-deploy-azure-container-instance.md) ou [AKS (Azure Kubernetes Service)](how-to-deploy-azure-kubernetes-service.md). ACI est l’option idéale pour tester les déploiements, tandis qu’AKS est mieux adapté à une utilisation en production à grande échelle.

Cet exemple déploie le modèle en tant que service web dans AKS. Pour déployer dans AKS, commencez par créer un cluster de calcul AKS ou utilisez un cluster AKS existant. Vous pouvez utiliser l’une des références SKU de machine virtuelle GPU ou CPU pour votre cluster de déploiement. 

```python

from azureml.core.compute import ComputeTarget, AksCompute
from azureml.exceptions import ComputeTargetException

# Choose a name for your cluster
aks_name = "cluster-aks-gpu"

# Check to see if the cluster already exists
try:
    aks_target = ComputeTarget(workspace=ws, name=aks_name)
    print('Found existing compute target')
except ComputeTargetException:
    print('Creating a new compute target...')
    # Provision AKS cluster with GPU machine
    prov_config = AksCompute.provisioning_configuration(vm_size="STANDARD_NC6", 
                                                        location="eastus2")
    # Create the cluster
    aks_target = ComputeTarget.create(workspace=ws, 
                                      name=aks_name, 
                                      provisioning_configuration=prov_config)
    aks_target.wait_for_completion(show_output=True)
```

Ensuite, vous pouvez définir la configuration d’inférence qui décrit comment configurer le service web contenant votre modèle. Vous pouvez utiliser le script de scoring et l’environnement de l’exécution d’entraînement dans votre configuration d’inférence.

```python
from azureml.core.model import InferenceConfig

best_child_run.download_file('outputs/scoring_file_v_1_0_0.py', output_file_path='score.py')
environment = best_child_run.get_environment()
inference_config = InferenceConfig(entry_script='score.py', environment=environment)
```

Vous pouvez ensuite déployer le modèle en tant que service web AKS.

```python
# Deploy the model from the best run as an AKS web service
from azureml.core.webservice import AksWebservice
from azureml.core.webservice import Webservice
from azureml.core.model import Model
from azureml.core.environment import Environment

aks_config = AksWebservice.deploy_configuration(autoscale_enabled=True,                                                    
                                                cpu_cores=1,
                                                memory_gb=50,
                                                enable_app_insights=True)

aks_service = Model.deploy(ws,
                           models=[model],
                           inference_config=inference_config,
                           deployment_config=aks_config,
                           deployment_target=aks_target,
                           name='automl-image-test',
                           overwrite=True)
aks_service.wait_for_deployment(show_output=True)
print(aks_service.state)
```

Vous pouvez aussi déployer le modèle à partir de l’[interface utilisateur du studio Azure Machine Learning](https://ml.azure.com/). Accédez au modèle que vous souhaitez déployer sous l’onglet **Modèles** de l’exécution du ML automatisé et sélectionnez **Déployer**.  

![Sélectionner un modèle dans les exécutions automl dans l’interface utilisateur du studio  ](./media/how-to-auto-train-image-models/select-model.png)

Vous pouvez configurer le nom du point de terminaison du déploiement du modèle et le cluster d’inférence à utiliser pour le déploiement de votre modèle dans le volet **Déployer un modèle**.

![Déployer la configuration](./media/how-to-auto-train-image-models/deploy-image-model.png)

### <a name="update-inference-configuration"></a>Mettre à jour la configuration de l’inférence

À l’étape précédente, nous avons téléchargé le fichier de scoring `outputs/scoring_file_v_1_0_0.py` du meilleur modèle dans un fichier `score.py` local et nous l’avons utilisé pour créer un objet `InferenceConfig`. Ce script peut être modifié pour changer les paramètres d’inférence spécifiques au modèle si nécessaire, une fois qu’il a été téléchargé et avant de créer `InferenceConfig`. Par exemple, c’est la section de code qui initialise le modèle dans le fichier de scoring :
    
```
...
def init():
    ...
    try:
        logger.info("Loading model from path: {}.".format(model_path))
        model_settings = {...}
        model = load_model(TASK_TYPE, model_path, **model_settings)
        logger.info("Loading successful.")
    except Exception as e:
        logging_utilities.log_traceback(e, logger)
        raise
...
```

Chacune des tâches (et certains modèles) a un ensemble de paramètres dans le dictionnaire `model_settings`. Par défaut, nous utilisons les mêmes valeurs pour les paramètres qui ont été utilisés pendant l’entraînement et la validation. En fonction du comportement dont nous avons besoin lors de l’utilisation du modèle pour l’inférence, nous pouvons changer ces paramètres. Vous trouverez ci-dessous une liste de paramètres pour chaque type de tâche et modèle.  

| Tâche | Nom du paramètre | Default  |
|--------- |------------- | --------- |
|Classification d’images (multi-classe et multi-étiquette) | `valid_resize_size`<br>`valid_crop_size` | 256<br>224 |
|Détection d’objets, segmentation d’instances| `min_size`<br>`max_size`<br>`box_score_thresh`<br>`box_nms_thresh`<br>`box_detections_per_img` | 600<br>1333<br>0.3<br>0.5<br>100 |
|Détection d’objets avec `yolov5`| `img_size`<br>`model_size`<br>`box_score_thresh`<br>`box_iou_thresh` | 640<br>moyenne<br>0.1<br>0.5 |

Pour une description détaillée de ces paramètres, reportez-vous à la section ci-dessus sur les [hyperparamètres spécifiques aux tâches](#task-specific-hyperparameters).
    
Si vous souhaitez utiliser la mise en mosaïque et contrôler son comportement, les paramètres suivants sont disponibles : `tile_grid_size`, `tile_overlap_ratio` et `tile_predictions_nms_thresh`. Pour plus d’informations sur ces paramètres, consultez [Entraîner un modèle de détection de petits objets avec AutoML](how-to-use-automl-small-object-detect.md).

## <a name="example-notebooks"></a>Exemples de notebooks
Passez en revue les exemples de code détaillé et les cas d’usage disponibles dans le [dépôt GitHub d’exemples de blocs-notes pour le Machine Learning automatisé](https://github.com/Azure/azureml-examples/tree/main/python-sdk/tutorials/automl-with-azureml). Vérifiez les dossiers avec le préfixe « image- » pour obtenir des exemples propres à la création de modèles de vision par ordinateur.


## <a name="next-steps"></a>Étapes suivantes

* [Tutoriel : Entraîner un modèle de détection d’objet (préversion) avec AutoML et Python](tutorial-auto-train-image-models.md).
* [Résoudre les problèmes d’expériences de ML automatisé](how-to-troubleshoot-auto-ml.md).