---
title: 'Démarrage rapide : Créer un projet de classification d’images à l’aide du kit SDK Custom Vision pour Python'
titleSuffix: Azure Cognitive Services
description: Créez un projet, ajoutez des balises, chargez des images, effectuez l’apprentissage de votre projet ainsi qu’une prédiction avec le kit de développement logiciel (SDK) Python.
services: cognitive-services
author: areddish
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 04/14/2020
ms.author: areddish
ms.openlocfilehash: 2654b61ac4489d8257020923a77800745027f303
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81403484"
---
# <a name="quickstart-create-an-image-classification-project-with-the-custom-vision-python-sdk"></a>Démarrage rapide : Créer un projet de classification d’images à l’aide du kit SDK Custom Vision Python

Cet article vous montre comment bien démarrer avec le SDK Vision par ordinateur et Python pour générer un modèle de classification d’images. Après la création du projet, vous pouvez ajouter des étiquettes, charger des images, entraîner le projet, obtenir l’URL du point de terminaison de prédiction publié du projet et utiliser ce point de terminaison pour tester programmatiquement une image. Utilisez cet exemple comme modèle pour générer votre propre application Python. Si vous voulez générer et utiliser un modèle de classification _sans_ code, consultez le [guide basé sur navigateur](getting-started-build-a-classifier.md).

## <a name="prerequisites"></a>Prérequis

- [Python 2.7+ ou 3.5+](https://www.python.org/downloads/)
- Outil [pip](https://pip.pypa.io/en/stable/installing/)
- [!INCLUDE [create-resources](includes/create-resources.md)]

## <a name="install-the-custom-vision-sdk"></a>Installer le kit de développement logiciel (SDK) Vision personnalisée

Pour installer le SDK Service Vision personnalisée pour Python, exécutez la commande suivante dans PowerShell :

```powershell
pip install azure-cognitiveservices-vision-customvision
```

[!INCLUDE [get-keys](includes/get-keys.md)]

[!INCLUDE [python-get-images](includes/python-get-images.md)]

## <a name="add-the-code"></a>Ajouter le code

Créez un nouveau fichier nommé *sample.py* dans le répertoire de projet de votre choix.

### <a name="create-the-custom-vision-service-project"></a>Créer le projet Service Vision personnalisée

Pour créer un nouveau projet Service Vision personnalisée, ajoutez le code suivant à votre script. Insérez vos clés d’abonnement dans les définitions appropriées. Récupérez l’URL de votre point de terminaison à partir de la page Paramètres du site web Custom Vision.

Consultez la méthode [create_project](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.training.custom_vision_training_client.customvisiontrainingclient?view=azure-python#create-project-name--description-none--domain-id-none--classification-type-none--target-export-platforms-none--custom-headers-none--raw-false----operation-config- ) pour spécifier d’autres options quand vous créez votre projet (procédure expliquée dans le guide du portail web [Créer un classifieur](getting-started-build-a-classifier.md)).  

```Python
from azure.cognitiveservices.vision.customvision.training import CustomVisionTrainingClient
from azure.cognitiveservices.vision.customvision.training.models import ImageFileCreateEntry

ENDPOINT = "<your API endpoint>"

# Replace with a valid key
training_key = "<your training key>"
prediction_key = "<your prediction key>"
prediction_resource_id = "<your prediction resource id>"

publish_iteration_name = "classifyModel"

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

Pour ajouter les exemples d’images au projet, insérez le code suivant après la création de mots clés. Ce code charge chaque image avec la balise correspondante. Vous pouvez charger jusqu’à 64 images dans un même lot.

> [!NOTE]
> Vous devrez changer le chemin des images en fonction de l’emplacement à partir duquel vous avez téléchargé le dépôt d’exemples du kit SDK Python pour Cognitive Services.

```Python
base_image_url = "<path to repo directory>/cognitive-services-python-sdk-samples/samples/vision/"

print("Adding images...")

image_list = []

for image_num in range(1, 11):
    file_name = "hemlock_{}.jpg".format(image_num)
    with open(base_image_url + "images/Hemlock/" + file_name, "rb") as image_contents:
        image_list.append(ImageFileCreateEntry(name=file_name, contents=image_contents.read(), tag_ids=[hemlock_tag.id]))

for image_num in range(1, 11):
    file_name = "japanese_cherry_{}.jpg".format(image_num)
    with open(base_image_url + "images/Japanese Cherry/" + file_name, "rb") as image_contents:
        image_list.append(ImageFileCreateEntry(name=file_name, contents=image_contents.read(), tag_ids=[cherry_tag.id]))

upload_result = trainer.create_images_from_files(project.id, images=image_list)
if not upload_result.is_batch_successful:
    print("Image batch upload failed.")
    for image in upload_result.images:
        print("Image status: ", image.status)
    exit(-1)
```

### <a name="train-the-classifier-and-publish"></a>Entraîner le classifieur et publier

Ce code crée la première itération du modèle de prédiction, puis publie cette itération sur le point de terminaison de prédiction. Le nom donné à l’itération publiée peut être utilisé pour envoyer des requêtes de prédiction. Les itérations ne sont pas disponibles sur le point de terminaison de prédiction tant qu’elles n’ont pas été publiées.

```Python
import time

print ("Training...")
iteration = trainer.train_project(project.id)
while (iteration.status != "Completed"):
    iteration = trainer.get_iteration(project.id, iteration.id)
    print ("Training status: " + iteration.status)
    time.sleep(1)

# The iteration is now trained. Publish it to the project endpoint
trainer.publish_iteration(project.id, iteration.id, publish_iteration_name, prediction_resource_id)
print ("Done!")
```

### <a name="get-and-use-the-published-iteration-on-the-prediction-endpoint"></a>Obtenir et utiliser l’itération publiée sur le point de terminaison de prédiction

Pour envoyer une image au point de terminaison de prédiction et récupérer la prédiction, ajoutez le code suivant à la fin du fichier :

```python
from azure.cognitiveservices.vision.customvision.prediction import CustomVisionPredictionClient

# Now there is a trained endpoint that can be used to make a prediction
predictor = CustomVisionPredictionClient(prediction_key, endpoint=ENDPOINT)

with open(base_image_url + "images/Test/test_image.jpg", "rb") as image_contents:
    results = predictor.classify_image(
        project.id, publish_iteration_name, image_contents.read())

    # Display the results.
    for prediction in results.predictions:
        print("\t" + prediction.tag_name +
              ": {0:.2f}%".format(prediction.probability * 100))
```

## <a name="run-the-application"></a>Exécution de l'application

Exécutez *sample.py*.

```powershell
python sample.py
```

Le résultat de l’application ressemble au texte suivant :

```console
Creating project...
Adding images...
Training...
Training status: Training
Training status: Completed
Done!
        Hemlock: 93.53%
        Japanese Cherry: 0.01%
```

Vous pouvez ensuite vérifier que l’image de test (trouvée dans **<url_image_base>/images/Test/** ) est correctement étiquetée. Vous pouvez aussi revenir sur le [site web Custom Vision](https://customvision.ai) et consulter l’état actuel de votre nouveau projet.

[!INCLUDE [clean-ic-project](includes/clean-ic-project.md)]

## <a name="next-steps"></a>Étapes suivantes

Vous savez maintenant comment effectuer la détection d’objet dans le code. Cet exemple exécute une seule itération d’entraînement, mais vous aurez souvent besoin d’entraîner et de tester votre modèle à plusieurs reprises pour le rendre plus précis.

> [!div class="nextstepaction"]
> [Tester et réentraîner un modèle](test-your-model.md)
