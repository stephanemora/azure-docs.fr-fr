---
title: 'Tutoriel : Créer un projet de classification d’images - Service Vision personnalisée, Python'
titlesuffix: Azure Cognitive Services
description: Créez un projet, ajoutez des balises, chargez des images, effectuez l’apprentissage votre projet ainsi qu’une prédiction en utilisant le point de terminaison par défaut.
services: cognitive-services
author: areddish
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: tutorial
ms.date: 08/28/2018
ms.author: areddish
ms.openlocfilehash: 14b805a60637a889698132e169d5a41670a8bce0
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/19/2018
ms.locfileid: "46363375"
---
# <a name="tutorial-create-an-image-classification-project-using-the-custom-vision-service-with-python"></a>Tutoriel : Créer un projet de classification d’images à l’aide du service Vision personnalisée avec Python

Découvrez comment créer un projet de classification d’images avec le service Vision personnalisé et un script Python de base. Après la création du projet, vous pouvez ajouter des mots-clés, charger des images, entraîner le projet, obtenir l’URL du point de terminaison de prédiction par défaut du projet et utiliser ce point de terminaison pour tester par programmation une image. Utilisez cet exemple open source comme modèle de création pour votre propre application au moyen de l’API Vision personnalisée.



## <a name="prerequisites"></a>Prérequis

- Python 2.7+ ou Python 3.5.x.
- L’outil pip.

## <a name="get-the-training-and-prediction-keys"></a>Obtenir les clés d’entraînement et de prédiction

Pour obtenir les clés utilisées dans cet exemple, visitez la [page web Custom Vision](https://customvision.ai) et sélectionnez l’__icône d’engrenage__ dans le coin supérieur droit. Dans la section __Accounts__ (Comptes), copiez les valeurs des champs __Training Key__ (Clé de formation) et __Prediction Key__ (Clé de prédiction).

![Image de l’interface utilisateur des clés](./media/python-tutorial/training-prediction-keys.png)

## <a name="install-the-custom-vision-service-sdk"></a>Installer le kit SDK Service Vision personnalisée

Pour installer le SDK Service Vision personnalisée, utilisez la commande suivante :

```
pip install azure-cognitiveservices-vision-customvision
```

## <a name="get-example-images"></a>Obtenir des exemples d’images

Cet exemple utilise les images du répertoire `Samples/Images` du projet [https://github.com/Microsoft/Cognitive-CustomVision-Windows](https://github.com/Microsoft/Cognitive-CustomVision-Windows/tree/master/Samples/Images). Clonez ou téléchargez et extrayez le projet sur votre environnement de développement.

## <a name="create-a-custom-vision-service-project"></a>Créer un projet Service Vision personnalisée

Pour créer un projet Service Vision personnalisée, créez un fichier nommé `sample.py`. Utilisez le code suivant comme contenu du fichier :

> [!IMPORTANT]
> Affectez à `training_key` la valeur de la clé d’entraînement que vous avez récupérée précédemment.
>
> Affectez à `prediction_key` la valeur de la clé de prédiction que vous avez récupérée précédemment.

```python
from azure.cognitiveservices.vision.customvision.training import training_api
from azure.cognitiveservices.vision.customvision.training.models import ImageUrlCreateEntry

# Replace with a valid key
training_key = "<your training key>"
prediction_key = "<your prediction key>"

trainer = training_api.TrainingApi(training_key)

# Create a new project
print ("Creating project...")
project = trainer.create_project("My Project")
```

## <a name="add-tags-to-your-project"></a>Ajouter des mots clés à votre projet

Pour ajouter des mots clés à votre projet, ajoutez le code suivant à la fin du fichier `sample.py` :

```python
# Make two tags in the new project
hemlock_tag = trainer.create_tag(project.id, "Hemlock")
cherry_tag = trainer.create_tag(project.id, "Japanese Cherry")
```

## <a name="upload-images-to-the-project"></a>Charger des images dans le projet

Pour ajouter les exemples d’images au projet, insérez le code suivant après la création de mots clés. Ce code charge l’image avec le mot clé correspondant :

> [!IMPORTANT]
>
> Modifiez le chemin aux images en fonction de l’emplacement où vous avez précédemment téléchargé le projet Cognitive-CustomVision-Windows.

```python
base_image_url = "https://raw.githubusercontent.com/Microsoft/Cognitive-CustomVision-Windows/master/Samples/"

print ("Adding images...")
for image_num in range(1,10):
    image_url = base_image_url + "Images/Hemlock/hemlock_{}.jpg".format(image_num)
    trainer.create_images_from_urls(project.id, [ ImageUrlCreateEntry(url=image_url, tag_ids=[ hemlock_tag.id ] ) ])

for image_num in range(1,10):
    image_url = base_image_url + "Images/Japanese Cherry/japanese_cherry_{}.jpg".format(image_num)
    trainer.create_images_from_urls(project.id, [ ImageUrlCreateEntry(url=image_url, tag_ids=[ cherry_tag.id ] ) ])


# Alternatively, if the images were on disk in a folder called Images alongside the sample.py, then
# they can be added by using the following:
#
#import os
#hemlock_dir = "Images\\Hemlock"
#for image in os.listdir(os.fsencode("Images\\Hemlock")):
#    with open(hemlock_dir + "\\" + os.fsdecode(image), mode="rb") as img_data: 
#        trainer.create_images_from_data(project.id, img_data, [ hemlock_tag.id ])
#
#cherry_dir = "Images\\Japanese Cherry"
#for image in os.listdir(os.fsencode("Images\\Japanese Cherry")):
#    with open(cherry_dir + "\\" + os.fsdecode(image), mode="rb") as img_data: 
#        trainer.create_images_from_data(project.id, img_data, [ cherry_tag.id ])
```

## <a name="train-the-project"></a>Entraîner le projet

Pour entraîner le classifieur, ajoutez le code suivant à la fin du fichier `sample.py` :

```python
import time

print ("Training...")
iteration = trainer.train_project(project.id)
while (iteration.status != "Completed"):
    iteration = trainer.get_iteration(project.id, iteration.id)
    print ("Training status: " + iteration.status)
    time.sleep(1)

# The iteration is now trained. Make it the default project endpoint
trainer.update_iteration(project.id, iteration.id, is_default=True)
print ("Done!")
```

## <a name="get-and-use-the-default-prediction-endpoint"></a>Obtenir et utiliser le point de terminaison de prédiction par défaut

Pour envoyer une image au point de terminaison de prédiction et récupérer la prédiction, ajoutez le code suivant à la fin du fichier `sample.py` :

```python
from azure.cognitiveservices.vision.customvision.prediction import prediction_endpoint
from azure.cognitiveservices.vision.customvision.prediction.prediction_endpoint import models

# Now there is a trained endpoint that can be used to make a prediction

predictor = prediction_endpoint.PredictionEndpoint(prediction_key)

test_img_url = base_image_url + "Images/Test/test_image.jpg"
results = predictor.predict_image_url(project.id, iteration.id, url=test_img_url)

# Alternatively, if the images were on disk in a folder called Images alongside the sample.py, then
# they can be added by using the following.
#
# Open the sample image and get back the prediction results.
# with open("Images\\test\\test_image.jpg", mode="rb") as test_data:
#     results = predictor.predict_image(project.id, test_data, iteration.id)

# Display the results.
for prediction in results.predictions:
    print ("\t" + prediction.tag_name + ": {0:.2f}%".format(prediction.probability * 100))
```

## <a name="run-the-example"></a>Exécuter l’exemple

Exécutez la solution. Les résultats de prédiction s’affichent sur la console.

```
python sample.py
```

Le résultat de l’application ressemble au texte suivant :

```
Creating project...
Adding images...
Training...
Training status: Training
Training status: Completed
Done!
        Hemlock: 93.53%
        Japanese Cherry: 0.01%
```