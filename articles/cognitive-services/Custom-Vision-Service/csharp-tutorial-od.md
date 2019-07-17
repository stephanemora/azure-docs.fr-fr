---
title: 'Démarrage rapide : créer un projet de détection d’objet avec le kit SDK du service Vision personnalisée pour C#'
titlesuffix: Azure Cognitive Services
description: Créez un projet, ajoutez des balises, chargez des images, effectuez l’apprentissage de votre projet et détectez des objets avec le kit de développement logiciel (SDK) .NET avec C#.
services: cognitive-services
author: areddish
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 07/03/2019
ms.author: areddish
ms.openlocfilehash: 63f5853199e3ee266df298b9599c2933b7da0826
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67606934"
---
# <a name="quickstart-create-an-object-detection-project-with-the-custom-vision-net-sdk"></a>Démarrage rapide : créer un projet de détection d’objet avec le kit de développement logiciel (SDK) du service Vision personnalisée pour .NET

Cet article fournit des informations et un exemple de code pour vous aider à prendre en main le kit de développement logiciel (SDK) de Vision personnalisée avec C#, afin de générer un modèle de détection d’objet. Une fois le projet créé, vous pouvez ajouter des régions balisées, charger des images, effectuer l’apprentissage du projet, obtenir l’URL du point de terminaison de prédiction par défaut du projet et utiliser le point de terminaison pour tester une image par programmation. Utilisez cet exemple comme modèle pour générer votre propre application .NET. 

## <a name="prerequisites"></a>Prérequis

- N’importe quelle édition de [Visual Studio 2015 ou 2017](https://www.visualstudio.com/downloads/)

## <a name="get-the-custom-vision-sdk-and-sample-code"></a>Obtenir le Kit de développement logiciel (SDK) Vision personnalisée et des exemples de code

Pour écrire une application .NET qui utilise le service Vision personnalisée, vous avez besoin des packages NuGet Vision personnalisée. Ces packages sont inclus dans l’exemple de projet que vous allez télécharger, mais vous pouvez y accéder individuellement ici.

- [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training/)
- [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction/)

Clonez ou téléchargez le projet [Exemples Cognitive Services .NET](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples). Accédez au dossier **CustomVision/ObjectDetection** et ouvrez _ObjectDetection.csproj_ dans Visual Studio.

Ce projet Visual Studio crée un projet Vision personnalisée nommé __My New Project__ (Nouveau projet), auquel vous pouvez accéder depuis le [site web Custom Vision](https://customvision.ai/). Elle charge ensuite les images pour entraîner et tester un modèle de détection d’objet. Dans ce projet, le modèle est entraîné pour détecter des fourches et des ciseaux dans des images.

[!INCLUDE [get-keys](includes/get-keys.md)]

## <a name="understand-the-code"></a>Comprendre le code

Ouvrez le fichier _Program.cs_ et inspectez le code. Insérez vos clés d’abonnement dans les définitions appropriées dans la méthode **Main**.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?range=18-27)]

Le paramètre du point de terminaison doit pointer vers la région dans laquelle le groupe de ressources Azure contenant les ressources Custom Vision a été créé. Dans cet exemple, nous supposons que la région USA Centre Sud est utilisée :

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?range=14-14)]

### <a name="create-a-new-custom-vision-service-project"></a>Créer un projet Custom Vision

L’extrait de code suivant crée un projet de détection d’objet. Le projet créé apparaît sur le [site web Custom Vision](https://customvision.ai/) sur lequel vous êtes allé plus tôt. 

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?range=29-35)]

### <a name="add-tags-to-the-project"></a>Ajouter des balises au projet

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?range=37-39)]

### <a name="upload-and-tag-images"></a>Charger et étiqueter des images

Lorsque vous appliquez des balises à des images dans des projets de détection d’objet, vous devez préciser la région de chaque objet balisé avec des coordonnées normalisées. Le code suivant associe chaque exemple d’image à sa région balisée.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?range=41-84)]

Cette carte d’associations est alors utilisée pour charger chaque exemple d’image avec ses coordonnées de région.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?range=86-104)]

À ce stade, tous les exemples d’images ont été chargés, et chacun dispose d’une balise (**fourche** ou **ciseaux**) et d’un rectangle associé à la balise.

### <a name="train-the-project"></a>Entraîner le projet

Ce code crée la première itération dans le projet.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?range=106-117)]

### <a name="publish-the-current-iteration"></a>Publier l’itération actuelle

Le nom donné à l’itération publiée peut être utilisé pour envoyer des requêtes de prédiction. Les itérations ne sont pas disponibles sur le point de terminaison de prédiction tant qu’elles n’ont pas été publiées.

```csharp
// The iteration is now trained. Publish it to the prediction end point.
var publishedModelName = "treeClassModel";
var predictionResourceId = "<target prediction resource ID>";
trainingApi.PublishIteration(project.Id, iteration.Id, publishedModelName, predictionResourceId);
Console.WriteLine("Done!\n");
```

### <a name="create-a-prediction-endpoint"></a>Créer un point de terminaison de prédiction

```csharp
// Create a prediction endpoint, passing in the obtained prediction key
CustomVisionPredictionClient endpoint = new CustomVisionPredictionClient()
{
        ApiKey = predictionKey,
        Endpoint = SouthCentralUsEndpoint
};
```

### <a name="use-the-prediction-endpoint"></a>Utiliser le point de terminaison de prédiction

Cette partie du script charge l’image test, interroge le point de terminaison du modèle, et renvoie des données de prédiction à la console.

```csharp
// Make a prediction against the new project
Console.WriteLine("Making a prediction:");
var imageFile = Path.Combine("Images", "test", "test_image.jpg");
using (var stream = File.OpenRead(imageFile))
{
        var result = endpoint.DetectImage(project.Id, publishedModelName, stream);

        // Loop over each prediction and write out the results
        foreach (var c in result.Predictions)
        {
                Console.WriteLine($"\t{c.TagName}: {c.Probability:P1} [ {c.BoundingBox.Left}, {c.BoundingBox.Top}, {c.BoundingBox.Width}, {c.BoundingBox.Height} ]");
        }
}
```

## <a name="run-the-application"></a>Exécution de l'application

Lors de l’exécution de l’application, une fenêtre de console s’ouvre et la sortie suivante est écrite :

```console
Creating new project:
        Training
Done!

Making a prediction:
        fork: 98.2% [ 0.111609578, 0.184719115, 0.6607002, 0.6637112 ]
        scissors: 1.2% [ 0.112389535, 0.119195729, 0.658031344, 0.7023591 ]
```

Vous pouvez alors vérifier que l’image test (trouvée dans **Images/Test/** ) est balisée de façon appropriée et que la région de détection est correcte. À ce stade, vous pouvez appuyer sur n’importe quelle touche pour quitter l’application.

[!INCLUDE [clean-od-project](includes/clean-od-project.md)]

## <a name="next-steps"></a>Étapes suivantes

Vous savez maintenant comment effectuer la détection d’objet dans le code. Cet exemple exécute une itération d’entraînement unique, mais vous aurez souvent besoin d’entraîner et de tester votre modèle à plusieurs reprises pour plus de précision. Le guide suivant traite de la classification d’images, mais ses principes sont identiques à la détection d’objet.

> [!div class="nextstepaction"]
> [Tester et réentraîner un modèle](test-your-model.md)
