---
title: 'Tutoriel : Créer un projet de classification des images - Service Vision personnalisée, Java'
titlesuffix: Azure Cognitive Services
description: Créez un projet, ajoutez des étiquettes, chargez des images, entraînez votre projet et faites une prédiction en utilisant le point de terminaison par défaut.
services: cognitive-services
author: areddish
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: tutorial
ms.date: 08/28/2018
ms.author: areddish
ms.openlocfilehash: 9a7f50e0eb33016d6a2d8f28be047b327135c51f
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/19/2018
ms.locfileid: "46367353"
---
# <a name="tutorial-build-an-image-classification-project-with-java"></a>Tutoriel : Créer un projet de classification des images avec Java

Découvrez comment créer un projet de classification d’images à l’aide du service Vision personnalisée avec Java. Après la création du projet, vous pouvez ajouter des étiquettes, charger des images, entraîner le projet, obtenir l’URL du point de terminaison de prédiction par défaut du projet et utiliser ce point de terminaison pour tester une image par programmation. Utilisez cet exemple open source comme modèle de création pour votre propre application au moyen de l’API Vision personnalisée.

## <a name="prerequisites"></a>Prérequis

- JDK 7 ou 8 est installé.
- Maven est installé.

## <a name="get-the-training-and-prediction-keys"></a>Obtenir les clés d’entraînement et de prédiction

Pour obtenir les clés utilisées dans cet exemple, visitez la [page web Custom Vision](https://customvision.ai) et sélectionnez l’__icône d’engrenage__ dans le coin supérieur droit. Dans la section __Accounts__ (Comptes), copiez les valeurs des champs __Training Key__ (Clé d’entraînement) et __Prediction Key__ (Clé de prédiction).

![Image de l’interface utilisateur des clés](./media/python-tutorial/training-prediction-keys.png)

## <a name="install-the-custom-vision-service-sdk"></a>Installer le kit SDK Service Vision personnalisée

Vous pouvez installer le SDK Vision personnalisée à partir du référentiel central Maven :
* [SDK d’entraînement](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customvision-training)
* [SDK de prédiction](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customvision-prediction)

## <a name="understand-the-code"></a>Comprendre le code

Le projet complet, y compris les images, est disponible dans le [dépôt d’exemples Vision personnalisée Azure pour Java](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master). 

Utilisez l’IDE Java de votre choix pour ouvrir le projet `Vision/CustomVision`. 

Cette application utilise la clé d’entraînement que vous avez récupérée pour créer un projet nommé __Sample Java Project__. Elle charge ensuite les images pour entraîner et tester un classifieur. Le classifieur identifie si un arbre est un __Hemlock__ (Pin tsuga) ou un __Japanese Cherry__ (Cerisier japonais).

Les extraits de code suivants implémentent les principales fonctionnalités de cet exemple :

## <a name="create-a-custom-vision-service-project"></a>Créer un projet Service Vision personnalisée

> [!IMPORTANT]
> Affectez à `trainingApiKey` la valeur de la clé d’entraînement que vous avez récupérée précédemment.

```java
final String trainingApiKey = "insert your training key here";
TrainingApi trainClient = CustomVisionTrainingManager.authenticate(trainingApiKey);

Trainings trainer = trainClient.trainings();

System.out.println("Creating project...");
Project project = trainer.createProject()
            .withName("Sample Java Project")
            .execute();
```

## <a name="add-tags-to-your-project"></a>Ajouter des étiquettes à votre projet

```java
// create hemlock tag
Tag hemlockTag = trainer.createTag()
    .withProjectId(project.id())
    .withName("Hemlock")
    .execute();

// create cherry tag
Tag cherryTag = trainer.createTag()
    .withProjectId(project.id())
    .withName("Japanese Cherry")
    .execute();
```

## <a name="upload-images-to-the-project"></a>Charger des images dans le projet

L’exemple est configuré pour inclure les images dans le package final. Les images sont lues à partir de la section des ressources du fichier jar et sont chargées dans le service.

```java
System.out.println("Adding images...");
for (int i = 1; i <= 10; i++) {
    String fileName = "hemlock_" + i + ".jpg";
    byte[] contents = GetImage("/Hemlock", fileName);
    AddImageToProject(trainer, project, fileName, contents, hemlockTag.id(), null);
}

for (int i = 1; i <= 10; i++) {
    String fileName = "japanese_cherry_" + i + ".jpg";
    byte[] contents = GetImage("/Japanese Cherry", fileName);
    AddImageToProject(trainer, project, fileName, contents, cherryTag.id(), null);
}
```

L’extrait de code précédent utilise deux fonctions d’assistance qui récupèrent les images comme flux de ressources et les charge dans le service.

```java
private static void AddImageToProject(Trainings trainer, Project project, String fileName, byte[] contents, UUID tag)
{
    System.out.println("Adding image: " + fileName);

    ImageFileCreateEntry file = new ImageFileCreateEntry()
        .withName(fileName)
        .withContents(contents);

    ImageFileCreateBatch batch = new ImageFileCreateBatch()
        .withImages(Collections.singletonList(file));

    batch = batch.withTagIds(Collections.singletonList(tag));

    trainer.createImagesFromFiles(project.id(), batch);
}

private static byte[] GetImage(String folder, String fileName)
{
    try {
        return ByteStreams.toByteArray(CustomVisionSamples.class.getResourceAsStream(folder + "/" + fileName));
    } catch (Exception e) {
        System.out.println(e.getMessage());
        e.printStackTrace();
    }

    return null;
}
```

## <a name="train-the-project"></a>Entraîner le projet

Cette opération crée la première itération du projet et la marque comme l’itération par défaut. 

```java
System.out.println("Training...");
Iteration iteration = trainer.trainProject(project.id());

while (iteration.status().equals("Training"))
{
    System.out.println("Training Status: "+ iteration.status());
    Thread.sleep(1000);
    iteration = trainer.getIteration(project.id(), iteration.id());
}

System.out.println("Training Status: "+ iteration.status());
trainer.updateIteration(project.id(), iteration.id(), iteration.withIsDefault(true));
```

## <a name="get-and-use-the-default-prediction-endpoint"></a>Obtenir et utiliser le point de terminaison de prédiction par défaut

> [!IMPORTANT]
> Affectez à `predictionApiKey` la valeur de la clé de prédiction que vous avez récupérée précédemment.

```java
final String predictionApiKey = "insert your prediction key here";
PredictionEndpoint predictClient = CustomVisionPredictionManager.authenticate(predictionApiKey);

// Use below for predictions from a url
// String url = "some url";
// ImagePrediction results = predictor.predictions().predictImage()
//                         .withProjectId(project.id())
//                         .withUrl(url)
//                         .execute();

// load test image
byte[] testImage = GetImage("/Test", "test_image.jpg");

// predict
ImagePrediction results = predictor.predictions().predictImage()
    .withProjectId(project.id())
    .withImageData(testImage)
    .execute();

for (Prediction prediction: results.predictions())
{
    System.out.println(String.format("\t%s: %.2f%%", prediction.tagName(), prediction.probability() * 100.0f));
}
```

## <a name="run-the-example"></a>Exécuter l’exemple

Pour compiler et exécuter la solution à l’aide de Maven :

```
mvn compile exec:java
```

Le résultat de l’application ressemble au texte suivant :

```
Creating project...
Adding images...
Adding image: hemlock_1.jpg
Adding image: hemlock_2.jpg
Adding image: hemlock_3.jpg
Adding image: hemlock_4.jpg
Adding image: hemlock_5.jpg
Adding image: hemlock_6.jpg
Adding image: hemlock_7.jpg
Adding image: hemlock_8.jpg
Adding image: hemlock_9.jpg
Adding image: hemlock_10.jpg
Adding image: japanese_cherry_1.jpg
Adding image: japanese_cherry_2.jpg
Adding image: japanese_cherry_3.jpg
Adding image: japanese_cherry_4.jpg
Adding image: japanese_cherry_5.jpg
Adding image: japanese_cherry_6.jpg
Adding image: japanese_cherry_7.jpg
Adding image: japanese_cherry_8.jpg
Adding image: japanese_cherry_9.jpg
Adding image: japanese_cherry_10.jpg
Training...
Training status: Training
Training status: Training
Training status: Completed
Done!
        Hemlock: 93.53%
        Japanese Cherry: 0.01%
```