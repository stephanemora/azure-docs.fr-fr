---
title: Générez et déployez un modèle de détection d’objet à l’aide du package Azure Machine Learning de la vision par ordinateur.
description: Découvrez comment créer, former, tester et déployer un modèle de détection d’objet de la vision par ordinateur à l’aide du package Azure Machine Learning de la vision par ordinateur.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: netahw
author: nhaiby
ms.date: 06/01/2018
ROBOTS: NOINDEX
ms.openlocfilehash: 1451cdc5efcb62cff5c5d3725d5b15eb44be1755
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/13/2018
ms.locfileid: "51613662"
---
# <a name="build-and-deploy-object-detection-models-with-azure-machine-learning"></a>Générer et déployer des modèles de détection d’objet avec Azure Machine Learning

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)]


Dans cet article, découvrez comment utiliser le **package Azure Machine Learning de la vision par ordinateur** (Azure Machine Learning Package for Computer Vision), pour former, tester et déployer un modèle de détection d’objet [R-CNN plus rapide](https://arxiv.org/abs/1506.01497). 

Un grand nombre de problèmes survenant dans le domaine de la vision par ordinateur peuvent être résolus au moyen de la détection d’objet. Ces problèmes incluent la création de modèles pour rechercher un nombre variable d’objets sur une image. 

Lors de la génération et du déploiement de ce modèle avec ce package, vous parcourez les étapes suivantes :
1.  Création d’un jeu de données
2.  Définition des modèles DNN (Deep Neural Network)
3.  Apprentissage du modèle
4.  Évaluation et visualisation
5.  Déploiement de services web
6.  Test de chargement de services Web

Dans cet exemple, TensorFlow est utilisé en tant que framework d’apprentissage profond, la formation est assurée localement sur une machine optimisée par GPU, comme la [machine virtuelle Deep learning Data Science](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.dsvm-deep-learning?tab=Overview), et le déploiement utilise Azure ML Operationalization CLI.

Consultez la [documentation de référence du package](https://aka.ms/aml-packages/vision) pour obtenir des informations détaillées sur chaque module et chaque classe.

## <a name="prerequisites"></a>Prérequis

1. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

1. Les comptes et l’application suivants doivent être configurés et installés :
   - un compte Azure Machine Learning Expérimentation ; 
   - Un compte Azure Machine Learning - Gestion des modèles
   - Azure Machine Learning Workbench installé.

   Si ces trois éléments ne sont pas déjà créés ou installés, suivez le [Guide de démarrage rapide d’Azure Machine Learning et l’installation de Workbench](../desktop-workbench/quickstart-installation.md). 

1. Le package Azure Machine Learning de la vision par ordinateur doit être installé. Découvrez comment [installer ce package ici](https://aka.ms/aml-packages/vision).

## <a name="sample-data-and-notebook"></a>Exemple de données et bloc-notes

### <a name="get-the-jupyter-notebook"></a>Se procurer le bloc-notes Jupyter

Téléchargez le bloc-notes pour exécuter vous-même l’exemple décrit ici.

> [!div class="nextstepaction"]
> [Se procurer le bloc-notes Jupyter](https://aka.ms/aml-packages/vision/notebooks/object_detection)

### <a name="load-the-sample-data"></a>Charger l’exemple de données

Pour cette démonstration, un jeu de données de produits alimentaires à l’intérieur de réfrigérateurs est fourni, comprenant 30 images et 8 classes (boîte à œufs, yoghurt, ketchup, champignon, moutarde, orange, sirop et eau). Pour chaque image jpg, il existe un fichier xml d’annotation portant un nom similaire. 

L’illustration suivante montre la structure des dossiers recommandée. 

![structure des dossiers](media/how-to-build-deploy-object-detection-models/data_directory.JPG)

## <a name="image-annotation"></a>Annotation image

Des emplacements d’objet annotés sont requis pour former et évaluer un détecteur d’objet. [LabelImg](https://tzutalin.github.io/labelImg) est un outil d’annotation open source qui peut être utilisé pour annoter des images. LabelImg écrit un fichier xml par image au format Pascal-VOC, qui peut être lu par ce package. 


```python
import warnings
warnings.filterwarnings("ignore")
import os, time
from cvtk.core import Context, ObjectDetectionDataset, TFFasterRCNN
from cvtk.evaluation import DetectionEvaluation
from cvtk.evaluation.evaluation_utils import graph_error_counts
from cvtk.utils import detection_utils

# Disable printing of logging messages
from azuremltkbase.logging import ToolkitLogger
ToolkitLogger.getInstance().setEnabled(False)

from matplotlib import pyplot as plt
# Display the images
%matplotlib inline
```

## <a name="create-a-dataset"></a>Créer un jeu de données

Créez un jeu de données CVTK qui se compose d’un ensemble d’images, avec leurs annotations de rectangle englobant respectives. Dans cet exemple, les images de réfrigérateur qui sont fournies dans le dossier «../sample_data/foods/training » sont utilisées. Seules les images JPEG sont prises en charge.


```python
image_folder = "detection/sample_data/foods/train"
data_train = ObjectDetectionDataset.create_from_dir(dataset_name='training_dataset', data_dir=image_folder,
                                                    annotations_dir="Annotations", image_subdirectory='JPEGImages')

# Show some statistics of the training image, and also give one example of the ground truth rectangle annotations
data_train.print_info()
_ = data_train.images[2].visualize_bounding_boxes(image_size = (10,10))
```

    F1 2018-05-25 23:12:21,727 INFO azureml.vision:machine info {"is_dsvm": true, "os_type": "Windows"} 
    F1 2018-05-25 23:12:21,733 INFO azureml.vision:dataset creating dataset for scenario=detection 
    Dataset name: training_dataset
    Total classes: 8, total images: 25
    Label-wise object counts:
        Label eggBox: 20 objects
        Label joghurt: 20 objects
        Label ketchup: 20 objects
        Label mushroom: 20 objects
        Label mustard: 20 objects
        Label orange: 20 objects
        Label squash: 40 objects
        Label water: 20 objects
    Bounding box width and height distribution:
        Bounding box widths  0/5/25/50/75/95/100-percentile: 54/61/79/117/133/165/311 pixels
        Bounding box heights 0/5/25/50/75/95/100-percentile: 48/58/75/124/142/170/212 pixels
    


![png](media/how-to-build-deploy-object-detection-models/output_6_1.JPG)


## <a name="define-a-model"></a>Définir un modèle

Dans cet exemple, le modèle R-CNN plus rapide est utilisé. Différents paramètres peuvent être fournis lors de la définition de ce modèle. La signification de ces paramètres, ainsi que les paramètres utilisés pour l’apprentissage (voir la section suivante) sont accessibles dans la documentation de l’API CVTK ou sur le [site web de détection d’objet Tensorflow](https://github.com/tensorflow/models/tree/master/research/object_detection). Vous trouverez plus d’informations sur le modèle R-CNN plus rapide avec [ce lien](https://docs.microsoft.com/cognitive-toolkit/Object-Detection-using-Faster-R-CNN#technical-details). Ce modèle est basé sur R-CNN rapide et plus d’informations sont accessibles [ici](https://docs.microsoft.com/cognitive-toolkit/Object-Detection-using-Fast-R-CNN#algorithm-details).


```python
score_threshold = 0.0       # Threshold on the detection score, use to discard lower-confidence detections.
max_total_detections = 300  # Maximum number of detections. A high value slows down training but might increase accuracy.
my_detector = TFFasterRCNN(labels=data_train.labels, 
                           score_threshold=score_threshold, 
                           max_total_detections=max_total_detections)
```

## <a name="train-the-model"></a>Formation du modèle

Le modèle R-CNN plus rapide formé avec COCO avec ResNet50 est utilisé comme point de départ pour l’apprentissage. 

Pour former le détecteur, le nombre d’étapes d’apprentissage dans le code est défini sur 350, afin que l’apprentissage s’exécute plus rapidement (~5 minutes avec une GPU). Dans la pratique, affectez-lui une valeur égale à au moins 10 fois le nombre d’images dans le jeu d’apprentissage.

Dans cet exemple, le nombre d’étapes d’apprentissage du détecteur est défini sur 350 pour un apprentissage rapide. Toutefois, dans la pratique, une règle empirique consiste à définir les étapes à une valeur égale à au moins 10 fois le nombre d’images dans le jeu d’apprentissage.

Deux paramètres clés pour l’apprentissage sont :
- Nombre d’étapes pour former le modèle, représenté par l’argument num_seps. Chaque étape effectue l’apprentissage du modèle avec un mini-lot d'un lot taille un.
- Taux d’apprentissage, qui peut être défini avec initial_learning_rate

```python
print("tensorboard --logdir={}".format(my_detector.train_dir))

# to get good results, use a larger value for num_steps, e.g., 5000.
num_steps = 350
learning_rate = 0.001 # learning rate

start_train = time.time()
my_detector.train(dataset=data_train, num_steps=num_steps, 
                  initial_learning_rate=learning_rate)
end_train = time.time()
print(end_train-start_train)
```

    tensorboard --logdir=C:\Users\lixun\Desktop\AutoDL\CVTK\Src\API\cvtk_output\temp_faster_rcnn_resnet50\models\train
    F1 2018-05-25 23:12:22,764 INFO azureml.vision:Fit starting in experiment  1125722225 
    F1 2018-05-25 23:12:22,767 INFO azureml.vision:model starting trainging for scenario=detection 
    Using existing checkpoint file that's saved at 'C:\Users\lixun\Desktop\AutoDL\CVTK\Src\API\cvtk_output\models\detection\faster_rcnn_resnet50_coco_2018_01_28\model.ckpt.index'.
    TFRecords creation started.
    F1 2018-05-25 23:12:22,773 INFO On image 0 of 25
    TFRecords creation completed.
    Training started.
    Training progressing: step 0 ...
    Training progressing: step 100 ...
    Training progressing: step 200 ...
    Training progressing: step 300 ...
    F1 2018-05-25 23:18:02,730 INFO Graph Rewriter optimizations enabled
    Converted 275 variables to const ops.
    F1 2018-05-25 23:18:10,722 INFO 2953 ops in the final graph.
    F1 2018-05-25 23:18:24,244 INFO azureml.vision:Fit finished in experiment  1125722225 
    Training completed.
    361.604615688324
    

TensorBoard peut être utilisé pour visualiser la progression de la formation. Les événements TensorBoard sont situés dans le dossier spécifié par l’attribut train_dir de l’objet de modèle. Pour afficher TensorBoard, procédez comme suit :
1. Copiez l’impression qui commence par « tensorboard--logdir » dans une ligne de commande et exécutez-la. 
2. Copiez l’URL retournée à partir de la ligne de commande dans un navigateur web pour afficher TensorBoard. 

TensorBoard doit ressembler à la capture d’écran suivante. Le remplissage du dossier d’apprentissage prend un certain temps. Par conséquent, si TensorBoard ne s’affiche pas correctement au premier essai, essayez d répéter les étapes 1 et 2.  

![tensorboard](media/how-to-build-deploy-object-detection-models/tensorboard.JPG)

## <a name="evaluate-the-model"></a>Évaluer le modèle

La méthode « evaluate » est utilisée pour évaluer le modèle. Cette fonction nécessite un objet ObjectDetectionDataset en tant qu’entrée. Le jeu de données d’évaluation peut être créé à l’aide de la même fonction que cellle utilisée pour le jeu de données d’apprentissage. La métrique prise en charge est Average Precision (Précision moyenne) telle que définie pour [PASCAL COV Challenge](http://host.robots.ox.ac.uk/pascal/VOC/pubs/everingham10.pdf).  


```python
image_folder = "detection/sample_data/foods/test"
data_val = ObjectDetectionDataset.create_from_dir(dataset_name='val_dataset', data_dir=image_folder)
eval_result = my_detector.evaluate(dataset=data_val)
```

    F1 2018-05-25 23:18:24,253 INFO azureml.vision:dataset creating dataset for scenario=detection 
    F1 2018-05-25 23:18:24,286 INFO On image 0 of 5
    F1 2018-05-25 23:18:29,300 INFO Starting evaluation at 2018-05-26-03:18:29
    F1 2018-05-25 23:18:32,403 INFO Creating detection visualizations.
    F1 2018-05-25 23:18:33,158 INFO Detection visualizations written to summary with tag image-0.
    F1 2018-05-25 23:18:33,518 INFO Creating detection visualizations.
    F1 2018-05-25 23:18:34,342 INFO Detection visualizations written to summary with tag image-1.
    F1 2018-05-25 23:18:34,714 INFO Creating detection visualizations.
    F1 2018-05-25 23:18:35,470 INFO Detection visualizations written to summary with tag image-2.
    F1 2018-05-25 23:18:35,835 INFO Creating detection visualizations.
    F1 2018-05-25 23:18:36,654 INFO Detection visualizations written to summary with tag image-3.
    F1 2018-05-25 23:18:37,010 INFO Creating detection visualizations.
    F1 2018-05-25 23:18:37,798 INFO Detection visualizations written to summary with tag image-4.
    F1 2018-05-25 23:18:37,804 INFO Running eval batches done.
    F1 2018-05-25 23:18:37,805 INFO # success: 5
    F1 2018-05-25 23:18:37,806 INFO # skipped: 0
    F1 2018-05-25 23:18:38,119 INFO Writing metrics to tf summary.
    F1 2018-05-25 23:18:38,121 INFO PASCAL/PerformanceByCategory/AP@0.5IOU/eggBox: 1.000000
    F1 2018-05-25 23:18:38,205 INFO PASCAL/PerformanceByCategory/AP@0.5IOU/joghurt: 0.942857
    F1 2018-05-25 23:18:38,206 INFO PASCAL/PerformanceByCategory/AP@0.5IOU/ketchup: 1.000000
    F1 2018-05-25 23:18:38,207 INFO PASCAL/PerformanceByCategory/AP@0.5IOU/mushroom: 1.000000
    F1 2018-05-25 23:18:38,208 INFO PASCAL/PerformanceByCategory/AP@0.5IOU/mustard: 1.000000
    F1 2018-05-25 23:18:38,209 INFO PASCAL/PerformanceByCategory/AP@0.5IOU/orange: 1.000000
    F1 2018-05-25 23:18:38,210 INFO PASCAL/PerformanceByCategory/AP@0.5IOU/squash: 1.000000
    F1 2018-05-25 23:18:38,211 INFO PASCAL/PerformanceByCategory/AP@0.5IOU/water: 1.000000
    F1 2018-05-25 23:18:38,211 INFO PASCAL/Precision/mAP@0.5IOU: 0.992857
    F1 2018-05-25 23:18:38,253 INFO Metrics written to tf summary.
    F1 2018-05-25 23:18:38,254 INFO Finished evaluation!
    

Les résultats de l’évaluation peuvent être imprimés dans un format propre.


```python
# print out the performance metric values
for label_obj in data_train.labels:
    label = label_obj.name
    key = 'PASCAL/PerformanceByCategory/AP@0.5IOU/' + label
    print('{0: <15}: {1: <3}'.format(label, round(eval_result[key], 2)))
print('{0: <15}: {1: <3}'.format("overall:", round(eval_result['PASCAL/Precision/mAP@0.5IOU'], 2))) 
```

    joghurt        : 0.94
    squash         : 1.0
    mushroom       : 1.0
    eggBox         : 1.0
    ketchup        : 1.0
    mustard        : 1.0
    water          : 1.0
    orange         : 1.0
    overall:       : 0.99
    

De même, vous pouvez calculer la précision du modèle sur le jeu d’apprentissage. Cela permet de s’assurer que la formation a convergé vers une bonne solution. La précision sur le jeu d’apprentissage après une formation réussie est souvent proche de 100 %.

Les résultats de l’évaluation peuvent également être affichés à partir de TensorBoard, notamment les valeurs mAP et les images avec des rectangles englobants prédits. Copiez l’impression à partir du code suivant dans une fenêtre de ligne de commande pour démarrer le client TensorBoard. Ici, une valeur de port 8008 est utilisée pour éviter tout conflit avec la valeur par défaut 6006, utilisée pour afficher l’état de l’apprentissage.


```python
print("tensorboard --logdir={} --port=8008".format(my_detector.eval_dir))
```

    tensorboard --logdir=C:\Users\lixun\Desktop\AutoDL\CVTK\Src\API\cvtk_output\temp_faster_rcnn_resnet50\models\eval --port=8008
    

## <a name="score-an-image"></a>Score d’une image

Une fois que vous êtes satisfait des performances du modèle formé, fonction « score » de l’objet de modèle peut être utilisée pour noter de nouvelles images. Les scores retournés peuvent être visualisés avec la fonction « visualize ». 


```python
image_path = data_val.images[1].storage_path
detections_dict = my_detector.score(image_path)
path_save = "./scored_images/scored_image_preloaded.jpg"
ax = detection_utils.visualize(image_path, detections_dict, image_size=(8, 12))
path_save_dir = os.path.dirname(os.path.abspath(path_save))
os.makedirs(path_save_dir, exist_ok=True)
ax.get_figure().savefig(path_save)
```

![png](media/how-to-build-deploy-object-detection-models/output_20_0.JPG)

##  <a name="save-the-model"></a>Enregistrer le modèle

Le modèle formé peut enregistré sur disque et rechargé en mémoire, comme indiqué dans les exemples de code suivants.


```python
save_model_path = "./frozen_model/faster_rcnn.model"
my_detector.save(save_model_path)
```

    F1 2018-05-25 23:18:55,166 INFO Graph Rewriter optimizations enabled
    Converted 275 variables to const ops.
    F1 2018-05-25 23:19:03,867 INFO 2953 ops in the final graph.
    

## <a name="load-the-saved-model-for-scoring"></a>Charger le modèle enregistré pour notation

Pour utiliser le modèle enregistré, chargez le modèle en mémoire avec la fonction « load ». Vous ne devez charger qu’une seule fois. 

```python
my_detector_loaded = TFFasterRCNN.load(save_model_path)
```

Une fois le modèle chargé, il peut être utilisé pour noter une image ou une liste d’images. Pour une seule image, un dictionnaire est retourné avec des clés telles que « detection_boxes », « detection_scores » et « num_detections ». Si l’entrée est une liste d’images, une liste de dictionnaires est retournée, avec un dictionnaire correspondant à une image. 


```python
detections_dict = my_detector_loaded.score(image_path)
```

Les objets détectés avec des scores au-dessus de 0,5, notamment les étiquettes, les scores et les coordonnées, peuvent être imprimés.


```python
look_up = dict((v,k) for k,v in my_detector.class_map.items())
n_obj = 0
for i in range(detections_dict['num_detections']):
    if detections_dict['detection_scores'][i] > 0.5:
        n_obj += 1
        print("Object {}: label={:11}, score={:.2f}, location=(top: {:.2f}, left: {:.2f}, bottom: {:.2f}, right: {:.2f})".format(
            i, look_up[detections_dict['detection_classes'][i]], 
            detections_dict['detection_scores'][i], 
            detections_dict['detection_boxes'][i][0],
            detections_dict['detection_boxes'][i][1], 
            detections_dict['detection_boxes'][i][2],
            detections_dict['detection_boxes'][i][3]))    
        
print("\nFound {} objects in image {}.".format(n_obj, image_path))           
```

    Object 0: label=squash     , score=0.99, location=(top: 0.74, left: 0.30, bottom: 0.84, right: 0.42)
    Object 1: label=squash     , score=0.98, location=(top: 0.27, left: 0.21, bottom: 0.37, right: 0.33)
    Object 2: label=orange     , score=0.98, location=(top: 0.31, left: 0.39, bottom: 0.37, right: 0.48)
    Object 3: label=joghurt    , score=0.98, location=(top: 0.57, left: 0.29, bottom: 0.67, right: 0.39)
    Object 4: label=eggBox     , score=0.97, location=(top: 0.41, left: 0.53, bottom: 0.49, right: 0.69)
    Object 5: label=water      , score=0.95, location=(top: 0.23, left: 0.51, bottom: 0.37, right: 0.57)
    Object 6: label=mustard    , score=0.88, location=(top: 0.61, left: 0.47, bottom: 0.66, right: 0.57)
    Object 7: label=ketchup    , score=0.80, location=(top: 0.62, left: 0.62, bottom: 0.68, right: 0.72)
    
    Found 8 objects in image ../sample_data/foods/test\JPEGImages\10.jpg.
    

Vous pouvez visualiser les scores comme auparavant.


```python
path_save = "./scored_images/scored_image_frozen_graph.jpg"
ax = detection_utils.visualize(image_path, detections_dict, path_save=path_save, image_size=(8, 12))
# ax.get_figure() # use this code extract the returned image
```

![png](media/how-to-build-deploy-object-detection-models/output_30_0.JPG)

## <a name="operationalization-deploy-and-consume"></a>Opérationnalisation : déployer et consommer

L’Opérationnalisation est le processus de publication de modèles et de code en tant que services web, et la consommation de ces services en vue de produire des résultats commerciaux. 

Dès que votre modèle est formé, vous pouvez le déployer comme service web pour la consommation au moyen d’[Azure Machine Learning CLI](https://docs.microsoft.com/azure/machine-learning/desktop-workbench/cli-for-azure-machine-learning). Vos modèles peuvent être déployés sur votre machine locale ou sur un cluster ACS (Azure Container Service). Avec ACS, vous pouvez faire évoluer votre service web manuellement, ou utiliser la fonctionnalité de mise à l’échelle automatique.

**Se connecter avec Azure CLI**

À l’aide d’un compte [Azure](https://azure.microsoft.com/) et d’un abonnement valide, connectez-vous au moyen de la commande CLI suivante :
<br>`az login`

+ Pour basculer vers un autre abonnement Azure, utilisez la commande :
<br>`az account set --subscription [your subscription name]`

+ Pour afficher le compte de gestion des modèles actif, utilisez la commande :
  <br>`az ml account modelmanagement show`

**Création et configuration de votre environnement de déploiement de cluster**

Vous n’avez besoin de configurer votre environnement de déploiement qu’une seule fois. Si vous n’en possédez pas déjà un, configurez votre environnement de déploiement maintenant en suivant [ces instructions](https://docs.microsoft.com/azure/machine-learning/desktop-workbench/deployment-setup-configuration#environment-setup). 

Pour voir votre environnement de déploiement actif, utilisez la commande CLI suivante :
<br>`az ml env show`
   
Exemple de commande Azure CLI pour créer et configurer l’environnement de déploiement :

```CLI
az provider register -n Microsoft.MachineLearningCompute
az provider register -n Microsoft.ContainerRegistry
az provider register -n Microsoft.ContainerService
az ml env setup --cluster -n [your environment name] -l [Azure region e.g. westcentralus] [-g [resource group]]
az ml env set -n [environment name] -g [resource group]
az ml env cluster
```
    
### <a name="manage-web-services-and-deployments"></a>Gérer des services web et des déploiements

Les API suivantes peuvent être utilisées pour déployer des modèles en tant que services web, gérer ces services web et gérer des déploiements.

|Tâche|API|
|----|----|
|Créer un objet de déploiement|`deploy_obj = AMLDeployment(deployment_name=deployment_name, associated_DNNModel=dnn_model, aml_env="cluster")`
|Déployer un service web|`deploy_obj.deploy()`|
|Attribuer un score à une image|`deploy_obj.score_image(local_image_path_or_image_url)`|
|Supprimer un service web|`deploy_obj.delete()`|
|Générer des images docker sans service web|`deploy_obj.build_docker_image()`|
|Lister le déploiement existant|`AMLDeployment.list_deployment()`|
|Supprimer si le service existe avec le nom du déploiement|`AMLDeployment.delete_if_service_exist(deployment_name)`|

**Documentation API :** consultez la[ documentation de référence du package](https://aka.ms/aml-packages/vision) pour obtenir des informations détaillées sur chaque module et chaque classe.

**Référence CLI :** pour les opérations plus poussées liées au déploiement, reportez-vous aux [informations de référence sur l’interface CLI de gestion des modèles](https://docs.microsoft.com/azure/machine-learning/desktop-workbench/model-management-cli-reference).

**Gestion du déploiement dans le portail Azure** : vous pouvez suivre et gérer vos déploiements dans le [portail Azure](https://ms.portal.azure.com/). À partir du portail Azure, trouvez la page de votre compte de gestion des modèles Machine Learning à l’aide du nom de ce compte. Ensuite, accédez à la page du compte de gestion des modèles > Gestion des modèles > Services.

```python
# ##### OPTIONAL - Interactive CLI setup helper ###### 
# # Interactive CLI setup helper, including model management account and deployment environment.
# # If you haven't setup you CLI before or if you want to change you CLI settings, you can use this block to help you interactively.
# # UNCOMMENT THE FOLLOWING LINES IF YOU HAVE NOT CREATED OR SET THE MODEL MANAGEMENT ACCOUNT AND DEPLOYMENT ENVIRONMENT

# from azuremltkbase.deployment import CliSetup
# CliSetup().run()
```


```python
from cvtk.operationalization import AMLDeployment

# set deployment name
deployment_name = "wsdeployment"

# Create deployment object
# It will use the current deployment environment (you can check it with CLI command "az ml env show").
deploy_obj = AMLDeployment(deployment_name=deployment_name, aml_env="cluster", associated_DNNModel=my_detector, replicas=1)

# Alternatively, you can provide azure machine learning deployment cluster name (environment name) and resource group name
# to deploy your model. It will use the provided cluster to deploy. To do that, please uncomment the following lines to create 
# the deployment object.

# azureml_rscgroup = "<resource group>"
# cluster_name = "<cluster name>"
# deploy_obj = AMLDeployment(deployment_name=deployment_name, associated_DNNModel=my_detector,
#                            aml_env="cluster", cluster_name=cluster_name, resource_group=azureml_rscgroup, replicas=1)

# Check if the deployment name exists, if yes remove it first.
if deploy_obj.is_existing_service():
    AMLDeployment.delete_if_service_exist(deployment_name)
    
# create the webservice
print("Deploying to Azure cluster...")
deploy_obj.deploy()
print("Deployment DONE")
```

### <a name="consume-the-web-service"></a>Consommer le services web

Une fois que vous avez créé le service web, vous pouvez noter des images avec le service web déployé. Vous disposez de plusieurs options :

   - Vous pouvez noter directement le service web avec l’objet de déploiement avec : deploy_obj.score_image(image_path_or_url) 
   - Vous pouvez également utiliser la clé de service et l’URL de point de terminaison du service (aucune valeur pour le déploiement local) avec : AMLDeployment.score_existing_service_with_image (image_path_or_url, service_endpoint_url, service_key=None)
   - Formez vos requêtes http directement pour attribuer un score au point de terminaison du service web (pour les utilisateurs avancés).

### <a name="score-with-existing-deployment-object"></a>Attribuer un score avec l’objet de déploiement existant
```
deploy_obj.score_image(image_path_or_url)
```


```python
# Score with existing deployment object

# Score local image with file path
print("Score local image with file path")
image_path_or_url = data_train.images[0].storage_path
print("Image source:",image_path_or_url)
serialized_result_in_json = deploy_obj.score_image(image_path_or_url, image_resize_dims=[224,224])
print("serialized_result_in_json:", serialized_result_in_json[:50])

# Score image url and remove image resizing
print("Score image url")
image_path_or_url = "https://cvtkdata.blob.core.windows.net/publicimages/microsoft_logo.jpg"
print("Image source:",image_path_or_url)
serialized_result_in_json = deploy_obj.score_image(image_path_or_url)
print("serialized_result_in_json:", serialized_result_in_json[:50])

```


```python
# Time image scoring
import timeit

num_images = 3
for img_index, img_obj in enumerate(data_train.images[:num_images]):
    print("Calling API for image {} of {}: {}...".format(img_index, num_images, img_obj.name))
    tic = timeit.default_timer()
    return_json = deploy_obj.score_image(img_obj.storage_path, image_resize_dims=[224,224])
    print("   Time for API call: {:.2f} seconds".format(timeit.default_timer() - tic))
```

### <a name="score-with-service-endpoint-url-and-service-key"></a>Attribuer un score avec l’url du point de terminaison du service et la clé du service
```
    AMLDeployment.score_existing_service_with_image(image_path_or_url, service_endpoint_url, service_key=None)
```


```python
# Import related classes and functions
from cvtk.operationalization import AMLDeployment

service_endpoint_url = "http://xxx" # please replace with your own service url
service_key = "xxx" # please replace with your own service key

# score image url
image_path_or_url = "https://cvtkdata.blob.core.windows.net/publicimages/microsoft_logo.jpg"
print("Image source:",image_path_or_url)
serialized_result_in_json = AMLDeployment.score_existing_service_with_image(image_path_or_url,service_endpoint_url, service_key = service_key, image_resize_dims=[224,224])
print("serialized_result_in_json:", serialized_result_in_json[:50])
```

### <a name="score-endpoint-with-http-request-directly"></a>Attribuer directement un score au point de terminaison avec une requête http
L’exemple de code suivant forme la requête http directement dans Python. Vous pouvez aussi utiliser d’autres langages de programmation pour accomplir cela.


```python
def score_image_with_http(image, service_endpoint_url, service_key=None, parameters={}):
    """Score local image with http request

    Args:
        image (str): Image file path
        service_endpoint_url(str): web service endpoint url
        service_key(str): Service key. None for local deployment.
        parameters (dict): Additional request parameters in dictionary. Default is {}.


    Returns:
        str: serialized result 
    """
    import requests
    from io import BytesIO
    import base64
    import json

    if service_key is None:
        headers = {'Content-Type': 'application/json'}
    else:
        headers = {'Content-Type': 'application/json',
                   "Authorization": ('Bearer ' + service_key)}
    payload = []
    encoded = None
    
    # Read image
    with open(image,'rb') as f:
        image_buffer = BytesIO(f.read()) ## Getting an image file represented as a BytesIO object
        
    # Convert your image to base64 string
    # image_in_base64 : "b'{base64}'"
    encoded = base64.b64encode(image_buffer.getvalue())
    image_request = {"image_in_base64": "{0}".format(encoded), "parameters": parameters}
    payload.append(image_request)
    body = json.dumps(payload)
    r = requests.post(service_endpoint_url, data=body, headers=headers)
    try:
        result = json.loads(r.text)
        json.loads(result[0])
    except:
        raise ValueError("Incorrect output format. Result cant not be parsed: " + r.text)
    return result[0]

```

### <a name="parse-serialized-result-from-webservice"></a>Analyser le résultat sérialisé à partir du service web
Le résultat à partir du service web est une chaîne json qui peut être analysée.


```python
image_path_or_url = image_path
print("Image source:",image_path_or_url)
serialized_result_in_json = deploy_obj.score_image(image_path_or_url)
print("serialized_result_in_json:", serialized_result_in_json[:50])
```


```python
# Parse result from json string
import numpy as np
parsed_result = TFFasterRCNN.parse_serialized_result(serialized_result_in_json)
print("Parsed result:", parsed_result)
```


```python
ax = detection_utils.visualize(image_path, parsed_result)
path_save = "./scored_images/scored_image_web.jpg"
path_save_dir = os.path.dirname(os.path.abspath(path_save))
os.makedirs(path_save_dir, exist_ok=True)
ax.get_figure().savefig(path_save)
```

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur le package Azure Machine Learning de la vision par ordinateur dans les articles suivants :

+ Lire la [vue d’ensemble du package](https://aka.ms/aml-packages/vision).

+ Explorer la [documentation de référence](https://docs.microsoft.com/python/api/overview/azure-machine-learning/computer-vision) de ce package.

+ En savoir plus sur les [autres packages Python pour Azure Machine Learning](reference-python-package-overview.md).
