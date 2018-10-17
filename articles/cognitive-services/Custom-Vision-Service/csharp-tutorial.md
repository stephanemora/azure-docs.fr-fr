---
title: 'Tutoriel : Créer une application Windows pour le service Vision personnalisée avec C#'
titlesuffix: Azure Cognitive Services
description: Créez un projet, ajoutez des mots clés, chargez des images, entraînez votre projet et effectuez une prédiction en utilisant le point de terminaison par défaut.
services: cognitive-services
author: anrothMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: tutorial
ms.date: 05/03/2018
ms.author: anroth
ms.openlocfilehash: 9e5ed71d4620f7ffeac8acb15f90d67964a86870
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/19/2018
ms.locfileid: "46366639"
---
# <a name="tutorial-use-the-custom-vision-service-from-a-c-application"></a>Tutoriel : Utiliser le service Vision personnalisée à partir d’une application C#

Découvrez comment utiliser le service Vision personnalisée à partir d’une application C#. Après la création du projet, vous pouvez ajouter des mots clés, charger des images, entraîner le projet, obtenir l’URL du point de terminaison de prédiction par défaut du projet et utiliser ce point de terminaison pour tester par programmation une image. Utilisez cet exemple open source comme modèle de création de votre propre application pour Windows au moyen de l’API Service Vision personnalisée.

## <a name="prerequisites"></a>Prérequis

* Toute édition de Visual Studio 2017 pour Windows.

## <a name="get-the-custom-vision-sdk-and-samples"></a>Obtenir le Kit de développement logiciel (SDK) pour Custom Vision et des exemples
Pour créer cet exemple, vous devez disposer des Packages NuGet du Kit de développement logiciel (SDK) Custom Vision :

* [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training/)
* [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction/)

Vous pouvez télécharger les images avec les [exemples en C#](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/CustomVision).

## <a name="get-the-training-and-prediction-keys"></a>Obtenir les clés d’apprentissage et de prédiction

Pour obtenir les clés utilisées dans cet exemple, visitez la [page web Custom Vision](https://customvision.ai) et sélectionnez l’__icône d’engrenage__ dans le coin supérieur droit. Dans la section __Accounts__ (Comptes), copiez les valeurs des champs __Training Key__ (Clé de formation) et __Prediction Key__ (Clé de prédiction).

![Image de l’interface utilisateur des clés](./media/csharp-tutorial/training-prediction-keys.png)

## <a name="understand-the-code"></a>Comprendre le code

Dans Visual Studio, ouvrez le projet situé dans le répertoire `Samples/CustomVision.Sample/` du projet du kit SDK.

Cette application utilise la clé d’entraînement que vous avez récupérée avant pour créer un nouveau projet nommé __My New Project__. Elle charge ensuite les images pour entraîner et tester un classifieur. Le classifieur identifie si un arbre est un __Hemlock__ (Pin tsuga) ou un __Japanese Cherry__ (Cerisier japonais).

Les extraits de code suivants implémentent les principales fonctionnalités de cet exemple :

* __Créer un projet Service Vision personnalisée__ :

    ```csharp
     // Create a new project
    Console.WriteLine("Creating new project:");
    var project = trainingApi.CreateProject("My New Project");
    ```

* __Créer des mots clés dans un projet__ :

    ```csharp
    // Make two tags in the new project
    var hemlockTag = trainingApi.CreateTag(project.Id, "Hemlock");
    var japaneseCherryTag = trainingApi.CreateTag(project.Id, "Japanese Cherry");
    ```

* __Charger des images et leur attribuer des mots clés__ :

    ```csharp
    // Add some images to the tags
    Console.WriteLine("\tUploading images");
    LoadImagesFromDisk();

    // Images can be uploaded one at a time
    foreach (var image in hemlockImages)
    {
        using (var stream = new MemoryStream(File.ReadAllBytes(image)))
        {
            trainingApi.CreateImagesFromData(project.Id, stream, new List<string>() { hemlockTag.Id.ToString() });
        }
    }

    // Or uploaded in a single batch 
    var imageFiles = japaneseCherryImages.Select(img => new ImageFileCreateEntry(Path.GetFileName(img), File.ReadAllBytes(img))).ToList();
    trainingApi.CreateImagesFromFiles(project.Id, new ImageFileCreateBatch(imageFiles, new List<Guid>() { japaneseCherryTag.Id }));
    ```

* __Entraîner le classifieur__ :

    ```csharp
    // Now there are images with tags start training the project
    Console.WriteLine("\tTraining");
    var iteration = trainingApi.TrainProject(project.Id);

    // The returned iteration will be in progress, and can be queried periodically to see when it has completed
    while (iteration.Status == "Completed")
    {
        Thread.Sleep(1000);

        // Re-query the iteration to get it's updated status
        iteration = trainingApi.GetIteration(project.Id, iteration.Id);
    }
    ```

* __Définir une itération par défaut pour le point de terminaison de prédiction__ :

    ```csharp
    // The iteration is now trained. Make it the default project endpoint
    iteration.IsDefault = true;
    trainingApi.UpdateIteration(project.Id, iteration.Id, iteration);
    Console.WriteLine("Done!\n");
    ```

* __Créer un point de terminaison de prédiction__ :
 
    ```csharp
    // Create a prediction endpoint, passing in obtained prediction key
    PredictionEndpoint endpoint = new PredictionEndpoint() { ApiKey = predictionKey };
    ```
 
* __Envoyer une image au point de terminaison de prédiction__ :

    ```csharp
    // Make a prediction against the new project
    Console.WriteLine("Making a prediction:");
    var result = endpoint.PredictImage(project.Id, testImage);

    // Loop over each prediction and write out the results
    foreach (var c in result.Predictions)
    {
        Console.WriteLine($"\t{c.TagName}: {c.Probability:P1}");
    }
    ```

## <a name="run-the-application"></a>Exécution de l'application

1. Apportez les modifications suivantes pour ajouter les clés d’entraînement et de prédiction à l’application :

    * Ajoutez votre __clé d’entraînement__ à la ligne suivante :

        ```csharp
        string trainingKey = "<your key here>";
        ```

    * Ajoutez votre __clé de prédiction__ à la ligne suivante :

        ```csharp
        string predictionKey = "<your key here>";
        ```

2. Exécutez l'application. À mesure que l’application s’exécute, la sortie suivante est écrite dans la console :

    ```
    Creating new project:
            Uploading images
            Training
    Done!

    Making a prediction:
            Hemlock: 95.0%
            Japanese Cherry: 0.0%
    ```

3. Pour quitter l’application, appuyez sur une touche.
