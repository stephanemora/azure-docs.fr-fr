---
title: 'Démarrage rapide : Créer un projet de classification d’images à l’aide du SDK Custom Vision pour C#'
titlesuffix: Azure Cognitive Services
description: Créez un projet, ajoutez des balises, chargez des images, effectuez l’apprentissage votre projet ainsi qu’une prédiction en utilisant le kit des .NET avec C#.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 07/03/2019
ms.author: anroth
ms.openlocfilehash: fd5e46f47169705b9d19397a6b91770beab229dd
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67604071"
---
# <a name="quickstart-create-an-image-classification-project-with-the-custom-vision-net-sdk"></a>Démarrage rapide : Créer un projet de classification d’images à l’aide du SDK Custom Vision .NET

Cet article fournit des informations et un exemple de code pour vous aider à prendre en main le kit de développement logiciel (SDK) de Vision personnalisée avec C#, afin de générer un modèle de classification d’images. Après la création du projet, vous pouvez ajouter des mots clés, charger des images, entraîner le projet, obtenir l’URL du point de terminaison de prédiction par défaut du projet et utiliser ce point de terminaison pour tester par programmation une image. Utilisez cet exemple comme modèle pour générer votre propre application .NET. Si vous voulez générer et utiliser un modèle de classification _sans_ code, consultez le [guide basé sur navigateur](getting-started-build-a-classifier.md).

## <a name="prerequisites"></a>Prérequis

- N’importe quelle édition de [Visual Studio 2015 ou 2017](https://www.visualstudio.com/downloads/)

## <a name="get-the-custom-vision-sdk-and-sample-code"></a>Obtenir le Kit de développement logiciel (SDK) Vision personnalisée et des exemples de code

Pour écrire une application .NET qui utilise le service Vision personnalisée, vous avez besoin des packages NuGet Vision personnalisée. Ces packages sont inclus dans l’exemple de projet que vous allez télécharger, mais vous pouvez y accéder individuellement ici.

- [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training/)
- [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction/)

Clonez ou téléchargez le projet [Exemples Cognitive Services .NET](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples). Accédez au dossier **CustomVision/ImageClassification** et ouvrez le fichier _ImageClassification.csproj_ dans Visual Studio.

Ce projet Visual Studio crée un projet Vision personnalisée nommé __My New Project__ (Nouveau projet), auquel vous pouvez accéder depuis le [site web Custom Vision](https://customvision.ai/). Elle charge ensuite les images pour entraîner et tester un classifieur. Dans ce projet, le classifieur a pour but de déterminer si l’arbre est une __cigüe__ ou un __cerisier du Japon__.

[!INCLUDE [get-keys](includes/get-keys.md)]

## <a name="understand-the-code"></a>Comprendre le code

Ouvrez le fichier _Program.cs_ et inspectez le code. Insérez vos clés d’abonnement dans les définitions appropriées dans la méthode **Main**.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?range=21-30)]

Le paramètre du point de terminaison doit pointer vers la région dans laquelle le groupe de ressources Azure contenant les ressources Custom Vision a été créé. Dans cet exemple, nous supposons que la région USA Centre Sud est utilisée :

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?range=14-14)]

Les lignes de code suivantes exécutent la fonctionnalité principale du projet.

### <a name="create-a-new-custom-vision-service-project"></a>Créer un projet Service Vision personnalisée

Le projet créé apparaît sur le [site web Custom Vision](https://customvision.ai/) sur lequel vous êtes allé plus tôt. 

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?range=32-34)]

### <a name="create-tags-in-the-project"></a>Créer des balises dans un projet

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?range=36-38)]

### <a name="upload-and-tag-images"></a>Charger et étiqueter des images

Les images de ce projet sont incluses. Elles sont référencées dans la méthode **LoadImagesFromDisk** dans _Program.cs_.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?range=40-55)]

### <a name="train-the-classifier-and-publish"></a>Entraîner le classifieur et publier

Ce code crée la première itération dans le projet, puis la publie sur le point de terminaison de prédiction. Le nom donné à l’itération publiée peut être utilisé pour envoyer des requêtes de prédiction. Les itérations ne sont pas disponibles sur le point de terminaison de prédiction tant qu’elles n’ont pas été publiées.

```csharp
// The returned iteration will be in progress, and can be queried periodically to see when it has completed
while (iteration.Status == "Training")
{
        Thread.Sleep(1000);

        // Re-query the iteration to get it's updated status
        iteration = trainingApi.GetIteration(project.Id, iteration.Id);
}

// The iteration is now trained. Publish it to the prediction end point.
var publishedModelName = "treeClassModel";
var predictionResourceId = "<target prediction resource ID>";
trainingApi.PublishIteration(project.Id, iteration.Id, publishedModelName, predictionResourceId);
Console.WriteLine("Done!\n");
```

### <a name="set-the-prediction-endpoint"></a>Définir le point de terminaison de prédiction

Il s’agit de la référence que vous pouvez utiliser pour soumettre une image au modèle actuelle et obtenir une prédiction de classification.

```csharp
// Create a prediction endpoint, passing in obtained prediction key
CustomVisionPredictionClient endpoint = new CustomVisionPredictionClient()
{
        ApiKey = predictionKey,
        Endpoint = SouthCentralUsEndpoint
};
```

### <a name="submit-an-image-to-the-default-prediction-endpoint"></a>Soumettre une image au point de terminaison de prédiction par défaut

Dans ce script, l’image test est chargée dans la méthode **LoadImagesFromDisk** et la sortie du modèle de prédiction doit être affichée dans la console.

```csharp
// Make a prediction against the new project
Console.WriteLine("Making a prediction:");
var result = endpoint.ClassifyImage(project.Id, publishedModelName, testImage);

// Loop over each prediction and write out the results
foreach (var c in result.Predictions)
{
        Console.WriteLine($"\t{c.TagName}: {c.Probability:P1}");
}
```

## <a name="run-the-application"></a>Exécution de l'application

Lors de l’exécution de l’application, une fenêtre de console s’ouvre et la sortie suivante est écrite :

```console
Creating new project:
        Uploading images
        Training
Done!

Making a prediction:
        Hemlock: 95.0%
        Japanese Cherry: 0.0%
```

Vous pouvez ensuite vérifier que l’image test (trouvée dans **Images/Test/** ) est balisée de façon appropriée. Pour quitter l’application, appuyez sur une touche. Vous pouvez aussi revenir sur le [site web Custom Vision](https://customvision.ai) et consulter l’état actuel de votre nouveau projet.

[!INCLUDE [clean-ic-project](includes/clean-ic-project.md)]

## <a name="next-steps"></a>Étapes suivantes

Vous savez maintenant comment effectuer la classification d’images dans le code. Cet exemple exécute une itération d’apprentissage unique, mais vous aurez souvent besoin d’entraîner et de tester votre modèle à plusieurs reprises pour plus de précision.

> [!div class="nextstepaction"]
> [Tester et réentraîner un modèle](test-your-model.md)
