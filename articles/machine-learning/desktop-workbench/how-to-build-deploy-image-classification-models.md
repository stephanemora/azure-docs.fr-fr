---
title: Générez et déployez un modèle de classification d’images à l’aide du package Azure Machine Learning de la vision par ordinateur.
description: Découvrez comment créer, former, tester et déployer un modèle de classification d’images de la vision par ordinateur à l’aide du package Azure Machine Learning de la vision par ordinateur.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: netahw
author: nhaiby
ms.date: 04/23/2018
ROBOTS: NOINDEX
ms.openlocfilehash: f5917cd7a5e4fcc2733765f642ad0958092372c1
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/13/2018
ms.locfileid: "51616212"
---
# <a name="build-and-deploy-image-classification-models-with-azure-machine-learning"></a>Générer et déployer des modèles de classification d’images avec Azure Machine Learning

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)]

Dans cet article, découvrez comment utiliser le package Azure Machine Learning de la vision par ordinateur (AMLPCV, Azure Machine Learning Package for Computer Vision), pour entraîner, tester et déployer un modèle de classification d’images. Pour une vue d’ensemble de ce package et de sa documentation de référence détaillée, [voir ici](https://aka.ms/aml-packages/vision).

Un grand nombre de problèmes survenant dans le domaine de la vision par ordinateur peuvent être résolus au moyen de la classification d’images. Ces problèmes incluent la création de modèles qui répondent aux questions, telles que :
+ _Y a-t-il un OBJET dans l’image ? Par exemple, un « chien », une « voiture », un « bateau », etc._
+ _Quel est le degré de gravité de la maladie oculaire mise en évidence par le scanner de la rétine de ce patient ?_

Lors de la génération et du déploiement de ce modèle avec AMLPCV, vous parcourez les étapes suivantes :
1. Création d’un jeu de données
2. Visualisation et annotation d’images
3. Augmentation d’images
4. Définition des modèles DNN (Deep Neural Network)
5. Formation du classifieur
6. Évaluation et visualisation
7. Déploiement de services web
8. Test de chargement de services Web

[CNTK](https://www.microsoft.com/cognitive-toolkit/) est utilisé en tant que framework d’apprentissage profond, la formation est assurée localement sur une machine optimisée par GPU, comme l’instance ([Deep learning Data Science VM](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.dsvm-deep-learning?tab=Overview)) et le déploiement utilise Azure ML Operationalization CLI.

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
> [Se procurer le bloc-notes Jupyter](https://aka.ms/aml-packages/vision/notebooks/image_classification)

### <a name="load-the-sample-data"></a>Charger l’exemple de données

L’exemple suivant utilise un jeu de données composé de 63 images de pièces de vaisselle. Chaque image est étiquetée comme appartenant à une des quatre classes distinctes : bowl (jatte), cup (gobelet), cutlery (couverts), plate (plat). Le nombre d’images de cet exemple est restreint pour que son exécution soit rapide. Dans la pratique, au moins 100 images par classe doivent être fournies. Toutes les images sont situées à l’emplacement suivant *".. /sample_data/imgs_recycling/"*, dans les sous-répertoires nommés "bowl", "cup", "cutlery" et "plate".

![Jeu de données Azure Machine Learning](media/how-to-build-deploy-image-classification-models/recycling_examples.jpg)


```python
import warnings
warnings.filterwarnings("ignore")
import json, numpy as np, os, timeit 
from azureml.logging import get_azureml_logger
from imgaug import augmenters
from IPython.display import display
from sklearn import svm
from cvtk import ClassificationDataset, CNTKTLModel, Context, Splitter, StorageContext
from cvtk.augmentation import augment_dataset
from cvtk.core.classifier import ScikitClassifier
from cvtk.evaluation import ClassificationEvaluation, graph_roc_curve, graph_pr_curve, graph_confusion_matrix
import matplotlib.pyplot as plt

from classification.notebook.ui_utils.ui_annotation import AnnotationUI
from classification.notebook.ui_utils.ui_results_viewer import ResultsUI
from classification.notebook.ui_utils.ui_precision_recall import PrecisionRecallUI

%matplotlib inline

# Disable printing of logging messages
from azuremltkbase.logging import ToolkitLogger
ToolkitLogger.getInstance().setEnabled(False)
```



## <a name="create-a-dataset"></a>Créer un jeu de données

Après avoir importé les dépendances et configuré le contexte de stockage, vous pouvez créer l’objet dataset.

Pour créer cet objet avec le package Azure Machine Learning de la vision par ordinateur, fournissez le répertoire racine des images sur le disque local. Ce répertoire doit suivre la même structure générale que le jeu de données de vaisselle, qui est, de contenir des sous-répertoires avec les images réelles :
- root
    - label 1
    - label 2
    - ...
    - label n
  
Former un modèle de classification d’images pour un autre jeu de données est aussi simple que de modifier le chemin racine `dataset_location` dans le code suivant pour pointer vers d’autres images.


```python
# Root image directory
dataset_location = os.path.abspath("classification/sample_data/imgs_recycling")

dataset_name = 'recycling'
dataset = ClassificationDataset.create_from_dir(dataset_name, dataset_location)
print("Dataset consists of {} images with {} labels.".format(len(dataset.images), len(dataset.labels)))
print("Select information for image 2: name={}, label={}, unique id={}.".format(
    dataset.images[2].name, dataset.images[2]._labels[0].name, dataset.images[2]._storage_id))
```

    F1 2018-04-23 17:12:57,593 INFO azureml.vision:machine info {"is_dsvm": true, "os_type": "Windows"} 
    F1 2018-04-23 17:12:57,599 INFO azureml.vision:dataset creating dataset for scenario=classification 
    Dataset consists of 63 images with 4 labels.
    Select information for image 2: name=msft-plastic-bowl20170725152154282.jpg, label=bowl, unique id=3.

L’objet dataset fournit la possibilité de télécharger des images à l’aide de l’[API Recherche d’images Bing](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/). 

Deux types de requêtes de recherche sont prises en charge : 
+ Requêtes de texte standard
+ Requêtes d’URL d’image

Ces requêtes, accompagnés de l’étiquette de classe, doivent être fournies à l’intérieur d’un fichier texte codé au format JSON. Par exemple : 

```json
{
    "bowl": [
                    "plastic bowl",
                    "../imgs_recycling/bowl"
    ],
    "cup": [
                    "plastic cup",
                    "../imgs_recycling/cup",
                    "http://cdnimg2.webstaurantstore.com/images/products/main/123662/268841/dart-solo-ultra-clear-conex-tp12-12-oz-pet-plastic-cold-cup-1000-case.jpg"
    ],
    "cutlery": [
                    "plastic cutlery",
                    "../imgs_recycling/cutlery",
                    "http://img4.foodservicewarehouse.com/Prd/1900SQ/Fineline_2514-BO.jpg"
    ],
    "plate": [
                    "plastic plate",
                    "../imgs_recycling/plate"
    ]
}
```

Par ailleurs, vous devez explicitement créer un objet Context pour contenir la clé de l’API Recherche d’images Bing. Pour cela, un abonnement à l’API Recherche d’images Bing est nécessaire.

## <a name="visualize-and-annotate-images"></a>Visualiser et annoter les images

Vous pouvez visualiser les images et corriger les étiquettes dans l’objet dataset à l’aide du widget donné ci-après. 

Si l’erreur « Widget Javascript not detected » (Widget Javascript non détecté) s’affiche, exécutez cette commande pour y remédier :
<br>`jupyter nbextension enable --py --sys-prefix widgetsnbextension`


```python
annotation_ui = AnnotationUI(dataset, Context.get_global_context())
display(annotation_ui.ui)
```

![Jeu de données Azure Machine Learning](media/how-to-build-deploy-image-classification-models/image_annotation.png)

## <a name="augment-images"></a>Augmenter les images

Le [module `augmentation`](https://docs.microsoft.com/python/api/cvtk.augmentation) fournit la possibilité d’augmenter un objet dataset en utilisant toutes les transformations décrites dans la bibliothèque [imgaug](https://github.com/aleju/imgaug). Les transformations d’image peuvent être regroupées dans un pipeline unique, dans ce cas toutes les transformations contenues dans le pipeline sont appliquées simultanément à chaque image. 

Si vous souhaitez appliquer séparément différentes étapes d’augmentation, ou les appliquer d’une autre manière, vous pouvez configurer plusieurs pipelines et les transmettre à la fonction *augment_dataset*. Pour obtenir plus d’informations et d’autres exemples d’augmentation d’image, consultez la [documentation imgaug](https://github.com/aleju/imgaug).

L’ajout d’images augmentées au jeu de formation est particulièrement utile pour les petits jeux de données. Étant donné que le processus de formation du réseau neuronal profond est plus lent compte tenu du nombre élevé d’images de formation, nous vous recommandons de commencer l’expérimentation sans augmentation.


```python
# Split the dataset into train and test  
train_set_orig, test_set = dataset.split(train_size = 0.66, stratify = "label")
print("Number of training images = {}, test images = {}.".format(train_set_orig.size(), test_set.size()))
```

    F1 2018-04-23 17:13:01,780 INFO azureml.vision:splitter splitting a dataset 
    F1 2018-04-23 17:13:01,805 INFO azureml.vision:dataset creating dataset for scenario=classification 
    F1 2018-04-23 17:13:01,809 INFO azureml.vision:dataset creating dataset for scenario=classification 
    Number of training images = 41, test images = 22.
    


```python
augment_train_set = False

if augment_train_set:
    aug_sequence = augmenters.Sequential([
            augmenters.Fliplr(0.5),             # horizontally flip 50% of all images
            augmenters.Crop(percent=(0, 0.1)),  # crop images by 0-10% of their height/width
        ])
    train_set = augment_dataset(train_set_orig, [aug_sequence])
    print("Number of original training images = {}, with augmented images included = {}.".format(train_set_orig.size(), train_set.size()))
else:
    train_set = train_set_orig  
```

## <a name="define-dnn-models"></a>Définir des modèles DNN

Les modèles du réseau neuronal profond (DNN) préformé suivants sont pris en charge avec ce package : 
+ Resnet-18
+ Resnet-34
+ Resnet-50
+ Resnet-101
+ Resnet-152

Ces réseaux neuronaux profonds peuvent être utilisés comme classifieur ou comme caractériseur. 

Des informations supplémentaires sur les réseaux sont à votre disposition [ici](https://github.com/Microsoft/CNTK/blob/master/PretrainedModels/Image.md), et une introduction de base à l’apprentissage par transfert (Transfer Learning) est consultable [ici](https://blog.slavv.com/a-gentle-intro-to-transfer-learning-2c0b674375a0).

Les paramètres par défaut de la classification d’images pour ce package sont : une résolution de 224 x 224 pixels et un DNN Resnet-18. Ces paramètres ont été sélectionnés pour leur bon fonctionnement sur un large éventail de tâches. La précision peut souvent être améliorée, par exemple en augmentant la résolution de l’image à 500 x 500 pixels et/ou en sélectionnant un modèle plus profond (Resnet-50). En revanche, la modification des paramètres peut entraîner une augmentation significative de la durée de formation. Consultez l’article sur le [Guide pratique pour améliorer la précision](https://docs.microsoft.com/azure/machine-learning/service/how-to-improve-accuracy-for-computer-vision-models).


```python
# Default parameters (224 x 224 pixels resolution, Resnet-18)
lr_per_mb = [0.05]*7 + [0.005]*7 +  [0.0005]
mb_size = 32
input_resoluton = 224
base_model_name = "ResNet18_ImageNet_CNTK"

# Suggested parameters for 500 x 500 pixels resolution, Resnet-50
# (see in the Appendix "How to improve accuracy", last row in table)
# lr_per_mb   = [0.01] * 7 + [0.001] * 7 + [0.0001]
# mb_size    = 8
# input_resoluton = 500
# base_model_name = "ResNet50_ImageNet_CNTK"

# Initialize model
dnn_model = CNTKTLModel(train_set.labels,
                       base_model_name=base_model_name,
                       image_dims = (3, input_resoluton, input_resoluton))
```

    Successfully downloaded ResNet18_ImageNet_CNTK
    

## <a name="train-the-classifier"></a>Former le classifieur

Vous pouvez choisir une des méthodes décrites ici pour le réseau neuronal profond préformé.

  - **Affinement du réseau neuronal profond**, qui forme le réseau neuronal profond à l’établissement direct de la classification. Même si la formation du réseau neuronal profond est lente, elle débouche généralement sur les meilleurs résultats possibles, car toutes les pondérations du réseau peuvent être améliorées au cours de la formation pour offrir une précision optimale.

  - **Caractérisation du réseau neuronal profond**, qui exécute le réseau neuronal profond tel quel, afin d’obtenir la représentation d’une image à des dimensions inférieures (512, 2048 ou 4096 floats). Cette représentation est ensuite utilisée comme entrée pour la formation d’un classifieur distinct. Étant donné que le réseau neuronal profond demeure inchangé, cette approche est beaucoup plus rapide comparativement à l’affinement du réseau neuronal profond, mais sa précision n’est pas aussi bonne. Cela dit, la formation d’un classifieur externe, tel qu’une machine à vecteurs de support (SVM) linéaire (comme indiqué dans le code suivant), peut fournir une base solide et aider à mieux comprendre la faisabilité d’un problème.
  
TensorBoard peut être utilisé pour visualiser la progression de la formation. Pour activer TensorBoard :
1. Ajoutez le paramètre `tensorboard_logdir=PATH` comme indiqué dans le code suivant.
1. Démarrez le client TensorBoard au moyen de la commande `tensorboard --logdir=PATH` dans une nouvelle console.
1. Ouvrez un navigateur web, comme indiqué par TensorBoard, qui par défaut est localhost:6006. 


```python
# Train either the DNN or a SVM as classifier 
classifier_name = "dnn"

if classifier_name.lower() == "dnn":  
    dnn_model.train(train_set, lr_per_mb = lr_per_mb, mb_size = mb_size, eval_dataset=test_set) #, tensorboard_logdir=r"tensorboard"
    classifier = dnn_model
elif classifier_name.lower() == "svm":
    learner = svm.LinearSVC(C=1.0, class_weight='balanced', verbose=0)
    classifier = ScikitClassifier(dnn_model, learner = learner)
    classifier.train(train_set)
else:
    raise Exception("Classifier unknown: " + classifier)   
```

    F1 2018-04-23 17:13:28,238 INFO azureml.vision:Fit starting in experiment  1541466320 
    F1 2018-04-23 17:13:28,239 INFO azureml.vision:model starting training for scenario=classification 
    <class 'int'>
    1 worker
    Training transfer learning model for 15 epochs (epoch_size = 41).
    non-distributed mode
    Training 15741700 parameters in 53 parameter tensors.
    Training 15741700 parameters in 53 parameter tensors.
    Learning rate per minibatch: 0.05
    Momentum per minibatch: 0.9
    PROGRESS: 0.00%
    Finished Epoch[1 of 15]: [Training] loss = 2.820586 * 41, metric = 68.29% * 41 5.738s (  7.1 samples/s);
    Evaluation Set Error :: 29.27%
    Finished Epoch[2 of 15]: [Training] loss = 0.286728 * 41, metric = 9.76% * 41 0.752s ( 54.5 samples/s);
    Evaluation Set Error :: 34.15%
    Finished Epoch[3 of 15]: [Training] loss = 0.206938 * 41, metric = 4.88% * 41 0.688s ( 59.6 samples/s);
    Evaluation Set Error :: 41.46%
    Finished Epoch[4 of 15]: [Training] loss = 0.098931 * 41, metric = 2.44% * 41 0.785s ( 52.2 samples/s);
    Evaluation Set Error :: 48.78%
    Finished Epoch[5 of 15]: [Training] loss = 0.046547 * 41, metric = 0.00% * 41 0.724s ( 56.6 samples/s);
    Evaluation Set Error :: 43.90%
    Finished Epoch[6 of 15]: [Training] loss = 0.059709 * 41, metric = 4.88% * 41 0.636s ( 64.5 samples/s);
    Evaluation Set Error :: 34.15%
    Finished Epoch[7 of 15]: [Training] loss = 0.005817 * 41, metric = 0.00% * 41 0.710s ( 57.7 samples/s);
    Evaluation Set Error :: 14.63%
    Learning rate per minibatch: 0.005
    Finished Epoch[8 of 15]: [Training] loss = 0.014917 * 41, metric = 0.00% * 41 0.649s ( 63.2 samples/s);
    Evaluation Set Error :: 9.76%
    Finished Epoch[9 of 15]: [Training] loss = 0.040539 * 41, metric = 2.44% * 41 0.777s ( 52.8 samples/s);
    Evaluation Set Error :: 9.76%
    Finished Epoch[10 of 15]: [Training] loss = 0.024606 * 41, metric = 0.00% * 41 0.626s ( 65.5 samples/s);
    Evaluation Set Error :: 7.32%
    PROGRESS: 0.00%
    Finished Epoch[11 of 15]: [Training] loss = 0.004225 * 41, metric = 0.00% * 41 0.656s ( 62.5 samples/s);
    Evaluation Set Error :: 4.88%
    Finished Epoch[12 of 15]: [Training] loss = 0.004364 * 41, metric = 0.00% * 41 0.702s ( 58.4 samples/s);
    Evaluation Set Error :: 4.88%
    Finished Epoch[13 of 15]: [Training] loss = 0.007974 * 41, metric = 0.00% * 41 0.721s ( 56.9 samples/s);
    Evaluation Set Error :: 4.88%
    Finished Epoch[14 of 15]: [Training] loss = 0.000655 * 41, metric = 0.00% * 41 0.711s ( 57.7 samples/s);
    Evaluation Set Error :: 4.88%
    Learning rate per minibatch: 0.0005
    Finished Epoch[15 of 15]: [Training] loss = 0.024865 * 41, metric = 0.00% * 41 0.688s ( 59.6 samples/s);
    Evaluation Set Error :: 4.88%
    Stored trained model at ../../../cvtk_output\model_trained\ImageClassification.model
    F1 2018-04-23 17:13:45,097 INFO azureml.vision:Fit finished in experiment  1541466320 
    


```python
# Plot how the training and test accuracy increases during gradient descent. 
if classifier_name == "dnn":
    [train_accs, test_accs, epoch_numbers] = classifier.train_eval_accs
    plt.xlabel("Number of training epochs") 
    plt.ylabel("Classification accuracy") 
    train_plot = plt.plot(epoch_numbers, train_accs, 'r-', label = "Training accuracy")
    test_plot = plt.plot(epoch_numbers, test_accs, 'b-.', label = "Test accuracy")
    plt.legend()
```

![png](media/how-to-build-deploy-image-classification-models/output_17_0.png)


## <a name="evaluate-and-visualize-model-performance"></a>Évaluer et visualiser les performances du modèle

Vous pouvez évaluer les performances du modèle formé sur un jeu de données de test indépendant à l’aide du module d’évaluation. Voici quelques métriques d’évaluation parmi ceux qu’il calcule :
 
+ Exactitude (moyenne par classe, par défaut)
+ Courbe PR (Précision-Rappel)
+ Courbe ROC
+ Aire sous la courbe (AUC)
+ Matrice de confusion


```python
# Run the classifier on all test set images
ce = ClassificationEvaluation(classifier, test_set, minibatch_size = mb_size)

# Compute Accuracy and the confusion matrix
acc = ce.compute_accuracy()
print("Accuracy = {:2.2f}%".format(100*acc))
cm  = ce.compute_confusion_matrix()
print("Confusion matrix = \n{}".format(cm))

# Show PR curve, ROC curve, and confusion matrix
fig, ((ax1, ax2, ax3)) = plt.subplots(1,3)
fig.set_size_inches(18, 4)
graph_roc_curve(ce, ax=ax1)
graph_pr_curve(ce, ax=ax2)
graph_confusion_matrix(ce, ax=ax3)
plt.show()
```

    F1 2018-04-23 17:14:37,449 INFO azureml.vision:evaluation doing evaluation for scenario=classification 
    F1 2018-04-23 17:14:37,450 INFO azureml.vision:model scoring dataset for scenario=classification 
    Accuracy = 95.45%
    Confusion matrix = 
    [[ 0  1  0  1]
     [ 0  7  0  0]
     [ 0  0  2  0]
     [ 0  0  0 11]]
    


![png](media/how-to-build-deploy-image-classification-models/output_20_1.png)



```python
# Results viewer UI
labels = [l.name for l in dataset.labels] 
pred_scores = ce.scores #classification scores for all images and all classes
pred_labels = [labels[i] for i in np.argmax(pred_scores, axis=1)]

results_ui = ResultsUI(test_set, Context.get_global_context(), pred_scores, pred_labels)
display(results_ui.ui)
```

![Jeu de données Azure Machine Learning](media/how-to-build-deploy-image-classification-models/Image_Classification_Results.png)


```python
# Precision / recall curve UI
precisions, recalls, thresholds = ce.compute_precision_recall_curve() 
thresholds = list(thresholds)
thresholds.append(thresholds[-1])
pr_ui = PrecisionRecallUI(100*precisions[::-1], 100*recalls[::-1], thresholds[::-1])
display(pr_ui.ui) 
```

![Jeu de données Azure Machine Learning](media/how-to-build-deploy-image-classification-models/image_precision_curve.png)

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
# ##### OPTIONAL###### 
# Interactive CLI setup helper, including model management account and deployment environment.
# If you haven't setup you CLI before or if you want to change you CLI settings, you can use this block to help you interactively.
#
# UNCOMMENT THE FOLLOWING LINES IF YOU HAVE NOT CREATED OR SET THE MODEL MANAGEMENT ACCOUNT AND DEPLOYMENT ENVIRONMENT
#
# from azuremltkbase.deployment import CliSetup
# CliSetup().run()
```


```python
# # Optional. Persist your model on disk and reuse it later for deployment. 
# from cvtk import TFFasterRCNN, Context
# import os
# save_model_path = os.path.join(Context.get_global_context().storage.persistent_path, "saved_classifier.model")
# # Save model to disk
# dnn_model.serialize(save_model_path)
# # Load model from disk
# dnn_model = CNTKTLModel.deserialize(save_model_path)
```


```python
from cvtk.operationalization import AMLDeployment

# set deployment name
deployment_name = "wsdeployment"

# Optional Azure Machine Learning deployment cluster name (environment name) and resource group name
# If you don't provide here. It will use the current deployment environment (you can check with CLI command "az ml env show").
azureml_rscgroup = "<resource group>"
cluster_name = "<cluster name>"

# If you provide the cluster information, it will use the provided cluster to deploy. 
# Example: deploy_obj = AMLDeployment(deployment_name=deployment_name, associated_DNNModel=dnn_model,
#                            aml_env="cluster", cluster_name=cluster_name, resource_group=azureml_rscgroup, replicas=1)

# Create deployment object
deploy_obj = AMLDeployment(deployment_name=deployment_name, aml_env="cluster", associated_DNNModel=dnn_model, replicas=1)

# Check if the deployment name exists, if yes remove it first.
if deploy_obj.is_existing_service():
    AMLDeployment.delete_if_service_exist(deployment_name)
    
# Create the web service
print("Deploying to Azure cluster...")
deploy_obj.deploy()
print("Deployment DONE")
```

### <a name="consume-the-web-service"></a>Consommer le services web 

Une fois le modèle déployé en tant que service web, vous pouvez attribuer des scores aux images en utilisant le service web et une des méthodes suivantes :

- Attribuer directement un score au service web avec l’objet de déploiement en utilisant `deploy_obj.score_image(image_path_or_url)`

- Utiliser l’URL du point de terminaison du service et la Clé du service (aucune pour le déploiement local) avec : `AMLDeployment.score_existing_service_with_image(image_path_or_url, service_endpoint_url, service_key=None)`

- Former vos requêtes HTTP directement pour attribuer un score au point de terminaison du service web. Cette option est destinée aux utilisateurs expérimentés.

### <a name="score-with-existing-deployment-object"></a>Attribuer un score avec l’objet de déploiement existant

```
deploy_obj.score_image(image_path_or_url)
```


```python
# Score with existing deployment object

# Score local image with file path
print("Score local image with file path")
image_path_or_url = test_set.images[0].storage_path
print("Image source:",image_path_or_url)
serialized_result_in_json = deploy_obj.score_image(image_path_or_url, image_resize_dims=[224,224])
print("serialized_result_in_json:", serialized_result_in_json)

# Score image url and remove image resizing
print("Score image url")
image_path_or_url = "https://cvtkdata.blob.core.windows.net/publicimages/microsoft_logo.jpg"
print("Image source:",image_path_or_url)
serialized_result_in_json = deploy_obj.score_image(image_path_or_url)
print("serialized_result_in_json:", serialized_result_in_json)

# Score image url with added parameters. Add softmax to score.
print("Score image url with added parameters. Add softmax to score")
from cvtk.utils.constants import ClassificationRESTApiParameters
image_path_or_url = "https://cvtkdata.blob.core.windows.net/publicimages/microsoft_logo.jpg"
print("Image source:",image_path_or_url)
serialized_result_in_json = deploy_obj.score_image(image_path_or_url, image_resize_dims=[224,224], parameters={ClassificationRESTApiParameters.ADD_SOFTMAX:True})
print("serialized_result_in_json:", serialized_result_in_json)
```


```python
# Time image scoring
import timeit

for img_index, img_obj in enumerate(test_set.images[:10]):
    print("Calling API for image {} of {}: {}...".format(img_index, len(test_set.images), img_obj.name))
    tic = timeit.default_timer()
    return_json = deploy_obj.score_image(img_obj.storage_path, image_resize_dims=[224,224])
    print("   Time for API call: {:.2f} seconds".format(timeit.default_timer() - tic))
    print(return_json)
```

### <a name="score-with-service-endpoint-url-and-service-key"></a>Attribuer un score avec l’url du point de terminaison du service et la clé du service

`AMLDeployment.score_existing_service_with_image(image_path_or_url, service_endpoint_url, service_key=None)`

```python
# Import related classes and functions
from cvtk.operationalization import AMLDeployment

service_endpoint_url = "" # please replace with your own service url
service_key = "" # please replace with your own service key
# score local image with file path
image_path_or_url = test_set.images[0].storage_path
print("Image source:",image_path_or_url)
serialized_result_in_json = AMLDeployment.score_existing_service_with_image(image_path_or_url,service_endpoint_url, service_key = service_key)
print("serialized_result_in_json:", serialized_result_in_json)

# score image url
image_path_or_url = "https://cvtkdata.blob.core.windows.net/publicimages/microsoft_logo.jpg"
print("Image source:",image_path_or_url)
serialized_result_in_json = AMLDeployment.score_existing_service_with_image(image_path_or_url,service_endpoint_url, service_key = service_key, image_resize_dims=[224,224])
print("serialized_result_in_json:", serialized_result_in_json)
```

### <a name="score-endpoint-with-http-request-directly"></a>Attribuer directement un score au point de terminaison avec une requête http

L’exemple de code suivant forme la requête HTTP directement dans Python. Vous pouvez aussi utiliser d’autres langages de programmation pour accomplir cela.


```python
def score_image_list_with_http(images, service_endpoint_url, service_key=None, parameters={}):
    """Score image list with http request

    Args:
        images(list): list of (input image file path, base64 image string, url or buffer)
        service_endpoint_url(str): endpoint url
        service_key(str): service key, None for local deployment.
        parameters(dict): service additional parameters in dictionary


    Returns:
        result (list): list of serialized result 
    """
    import requests
    from io import BytesIO
    import base64
    routing_id = ""

    if service_key is None:
        headers = {'Content-Type': 'application/json',
                   'X-Marathon-App-Id': routing_id}
    else:
        headers = {'Content-Type': 'application/json',
                   "Authorization": ('Bearer ' + service_key), 'X-Marathon-App-Id': routing_id}
    payload = []
    for image in images:
        encoded = None
        # read image
        with open(image,'rb') as f:
            image_buffer = BytesIO(f.read()) ## Getting an image file represented as a BytesIO object
        # convert your image to base64 string
        encoded = base64.b64encode(image_buffer.getvalue())
        image_request = {"image_in_base64": "{0}".format(encoded), "parameters": parameters}
        payload.append(image_request)
    body = json.dumps(payload)
    r = requests.post(service_endpoint_url, data=body, headers=headers)
    try:
        result = json.loads(r.text)
    except:
        raise ValueError("Incorrect output format. Result cant not be parsed: " + r.text)
    return result

# Test with images
images = [test_set.images[0].storage_path, test_set.images[1].storage_path] # A list of local image files
score_image_list_with_http(images, service_endpoint_url, service_key)
```

### <a name="parse-serialized-result-from-web-service"></a>Analyser le résultat sérialisé à partir du service web

La sortie à partir du service web est une chaîne JSON. Vous pouvez analyser cette chaîne JSON avec différentes classes de modèle DNN.


```python
image_path_or_url = test_set.images[0].storage_path
print("Image source:",image_path_or_url)
serialized_result_in_json = deploy_obj.score_image(image_path_or_url, image_resize_dims=[224,224])
print("serialized_result_in_json:", serialized_result_in_json)
```


```python
# Parse result from json string
import numpy as np
parsed_result = CNTKTLModel.parse_serialized_result(serialized_result_in_json)
print("Parsed result:", parsed_result)
# Map result to image class
class_index = np.argmax(np.array(parsed_result))
print("Class index:", class_index)
dnn_model.class_map
print("Class label:", dnn_model.class_map[class_index])
```


## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur le package Azure Machine Learning de la vision par ordinateur dans les articles suivants :

+ Découvrir comment [améliorer la précision de ce modèle](how-to-improve-accuracy-for-computer-vision-models.md).

+ Lire la [vue d’ensemble du package](https://aka.ms/aml-packages/vision).

+ Explorer la [documentation de référence](https://docs.microsoft.com/python/api/overview/azure-machine-learning/computer-vision) de ce package.

+ En savoir plus sur les [autres packages Python pour Azure Machine Learning](reference-python-package-overview.md).
