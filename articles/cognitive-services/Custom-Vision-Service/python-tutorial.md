---
title: 'Démarrage rapide : Créer un projet de classification d’images à l’aide du kit SDK Custom Vision pour Python'
titlesuffix: Azure Cognitive Services
description: Créez un projet, ajoutez des balises, chargez des images, effectuez l’apprentissage de votre projet ainsi qu’une prédiction avec le kit de développement logiciel (SDK) Python.
services: cognitive-services
author: areddish
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 11/2/2018
ms.author: areddish
ms.openlocfilehash: 66fc773dd354f80428d6fd65906610b901260a59
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2019
ms.locfileid: "57407032"
---
# <a name="quickstart-create-an-image-classification-project-with-the-custom-vision-python-sdk"></a>Démarrage rapide : Créer un projet de classification d’images à l’aide du kit SDK Custom Vision Python

Cet article fournit des informations et un exemple de code pour vous aider à prendre en main le kit de développement logiciel (SDK) de Vision personnalisée avec Python, afin de générer un modèle de classification d’images. Après la création du projet, vous pouvez ajouter des mots clés, charger des images, entraîner le projet, obtenir l’URL du point de terminaison de prédiction par défaut du projet et utiliser ce point de terminaison pour tester par programmation une image. Utilisez cet exemple comme modèle pour générer votre propre application Python. Si vous voulez générer et utiliser un modèle de classification _sans_ code, consultez le [guide basé sur navigateur](getting-started-build-a-classifier.md).

## <a name="prerequisites"></a>Prérequis

- [Python 2.7+ ou 3.5+](https://www.python.org/downloads/)
- Outil [pip](https://pip.pypa.io/en/stable/installing/)

## <a name="install-the-custom-vision-sdk"></a>Installer le kit de développement logiciel (SDK) Vision personnalisée

Pour installer le SDK Service Vision personnalisée pour Python, exécutez la commande suivante dans PowerShell :

```PowerShell
pip install azure-cognitiveservices-vision-customvision
```

[!INCLUDE [get-keys](includes/get-keys.md)]

[!INCLUDE [python-get-images](includes/python-get-images.md)]


## <a name="add-the-code"></a>Ajouter le code

Créez un nouveau fichier nommé *sample.py* dans le répertoire de projet de votre choix.

### <a name="create-the-custom-vision-service-project"></a>Créer le projet Service Vision personnalisée

Pour créer un nouveau projet Service Vision personnalisée, ajoutez le code suivant à votre script. Insérez vos clés d’abonnement dans les définitions appropriées.

```Python
from azure.cognitiveservices.vision.customvision.training import CustomVisionTrainingClient
from azure.cognitiveservices.vision.customvision.training.models import ImageFileCreateEntry

ENDPOINT = "https://southcentralus.api.cognitive.microsoft.com"

# Replace with a valid key
training_key = "<your training key>"
prediction_key = "<your prediction key>"

trainer = CustomVisionTrainingClient(training_key, endpoint=ENDPOINT)

# Create a new project
print ("Creating project...")
project = trainer.create_project("My New Project")
```

### <a name="create-tags-in-the-project"></a>Créer des balises dans un projet

Pour créer des balises de classification à votre projet, ajoutez le code suivant à la fin du fichier *sample.py* :

```Python
# Make two tags in the new project
hemlock_tag = trainer.create_tag(project.id, "Hemlock")
cherry_tag = trainer.create_tag(project.id, "Japanese Cherry")
```

### <a name="upload-and-tag-images"></a>Charger et étiqueter des images

Pour ajouter les exemples d’images au projet, insérez le code suivant après la création de mots clés. Ce code charge chaque image avec la balise correspondante. Vous devez entrer le chemin d’accès de l’URL de base de l’image, en fonction d’où vous avez téléchargé le projet d’exemple du kit de développement logiciel (SDK) Cognitive Services pour Python.

> [!NOTE]
> Vous devez changer le chemin d'accès aux images, en fonction d’où vous avez téléchargé le projet d’exemple du kit de développement logiciel (SDK) Cognitive Services pour Python.

```Python
base_image_url = "<path to project>"

print("Adding images...")

image_entry = lambda image_path, tag_id: ImageFileCreateEntry(
    name=image_path.split("/")[-1], contents=image_path, tag_ids=[tag_id]
)

image_list = [
    image_entry(
        base_image_url + "Images/Hemlock/hemlock_{}.jpg".format(image_num),
        hemlock_tag.id,
    )
    for image_num in range(1, 10)
] + [
    image_entry(
        base_image_url
        + "Images/Japanese Cherry/japanese_cherry_{}.jpg".format(image_num),
        cherry_tag.id,
    )
    for image_num in range(1, 10)
]

trainer.create_images_from_files(project.id, images=image_list)
```

### <a name="train-the-classifier"></a>Former le classifieur

Ce code crée la première itération du projet et la marque comme l’itération par défaut. L’itération par défaut correspond à la version du modèle qui répondra aux requêtes de prédiction. Vous devez la mettre à jour à chaque fois que vous réentraînez le modèle.

```Python
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

### <a name="get-and-use-the-default-prediction-endpoint"></a>Obtenir et utiliser le point de terminaison de prédiction par défaut

Pour envoyer une image au point de terminaison de prédiction et récupérer la prédiction, ajoutez le code suivant à la fin du fichier :

```python
from azure.cognitiveservices.vision.customvision.prediction import CustomVisionPredictionClient

# Now there is a trained endpoint that can be used to make a prediction

predictor = CustomVisionPredictionClient(prediction_key, endpoint=ENDPOINT)

test_img_url = base_image_url + "Images/Test/test_image.jpg"
results = predictor.predict_image_url(project.id, iteration.id, url=test_img_url)

# Display the results.
for prediction in results.predictions:
    print ("\t" + prediction.tag_name + ": {0:.2f}%".format(prediction.probability * 100))
```

## <a name="run-the-application"></a>Exécution de l'application

Exécutez *sample.py*.

```PowerShell
python sample.py
```

Le résultat de l’application ressemble au texte suivant :

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

Vous pouvez ensuite vérifier que l’image test (trouvée dans **<base_image_url>/Images/Test**) est balisée de façon appropriée. Vous pouvez aussi revenir sur le [site web Custom Vision](https://customvision.ai) et consulter l’état actuel de votre nouveau projet.

[!INCLUDE [clean-ic-project](includes/clean-ic-project.md)]

## <a name="next-steps"></a>Étapes suivantes

Vous savez maintenant comment effectuer la classification d’images dans le code. Cet exemple exécute une itération d’apprentissage unique, mais vous aurez souvent besoin d’entraîner et de tester votre modèle à plusieurs reprises pour plus de précision.

> [!div class="nextstepaction"]
> [Tester et réentraîner un modèle](test-your-model.md)
