---
title: 'Démarrage rapide : Créer un projet de détection d’objets avec le SDK pour C# - Vision personnalisée'
titleSuffix: Azure Cognitive Services
description: Créez un projet, ajoutez des balises, chargez des images, effectuez l’apprentissage de votre projet et détectez des objets avec le kit de développement logiciel (SDK) .NET avec C#.
services: cognitive-services
author: areddish
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 12/05/2019
ms.author: areddish
ms.openlocfilehash: c6aaf69ba3ed682a00a203079b024a47121334e3
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/17/2020
ms.locfileid: "76170066"
---
# <a name="quickstart-create-an-object-detection-project-with-the-custom-vision-net-sdk"></a>Démarrage rapide : créer un projet de détection d’objet avec le kit de développement logiciel (SDK) du service Vision personnalisée pour .NET

Cet article vous montre comment bien démarrer avec le SDK Vision par ordinateur et C# pour générer un modèle de détection d’objet. Une fois le projet créé, vous pouvez ajouter des régions balisées, charger des images, effectuer l’apprentissage du projet, obtenir l’URL du point de terminaison de prédiction par défaut du projet et utiliser le point de terminaison pour tester une image par programmation. Utilisez cet exemple comme modèle pour générer votre propre application .NET. 

## <a name="prerequisites"></a>Conditions préalables requises

- N’importe quelle édition de [Visual Studio 2015 ou 2017](https://www.visualstudio.com/downloads/)
- [!INCLUDE [create-resources](includes/create-resources.md)]

## <a name="get-the-custom-vision-sdk-and-sample-code"></a>Obtenir le Kit de développement logiciel (SDK) Custom Vision et un exemple de code

Pour écrire une application .NET qui utilise le service Vision personnalisée, vous avez besoin des packages NuGet Vision personnalisée. Ces packages sont inclus dans l’exemple de projet que vous allez télécharger, mais vous pouvez y accéder individuellement ici.

- [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training/)
- [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction/)

Clonez ou téléchargez le projet [Exemples Cognitive Services .NET](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples). Accédez au dossier **CustomVision/ObjectDetection** et ouvrez _ObjectDetection.csproj_ dans Visual Studio.

Ce projet Visual Studio crée un projet Vision personnalisée nommé __My New Project__ (Nouveau projet), auquel vous pouvez accéder depuis le [site web Custom Vision](https://customvision.ai/). Elle charge ensuite les images pour entraîner et tester un modèle de détection d’objet. Dans ce projet, le modèle est entraîné pour détecter des fourches et des ciseaux dans des images.

[!INCLUDE [get-keys](includes/get-keys.md)]

## <a name="understand-the-code"></a>Comprendre le code

Ouvrez le fichier _Program.cs_ et inspectez le code. [Créez des variables d’environnement](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) pour vos clés de formation et de prédiction nommées `CUSTOM_VISION_TRAINING_KEY` et `CUSTOM_VISION_PREDICTION_KEY`, respectivement. Le script recherchera ces variables.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_keys)]

Récupérez l’URL de votre point de terminaison à partir de la page Paramètres du site web Custom Vision. Enregistrez-la dans une variable d’environnement appelée `CUSTOM_VISION_ENDPOINT`. Le script enregistre une référence à celle-ci à la racine de votre classe.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_endpoint)]

### <a name="create-a-new-custom-vision-service-project"></a>Créer un projet Custom Vision

L’extrait de code suivant crée un projet de détection d’objet. Le projet créé apparaît sur le [site web Custom Vision](https://customvision.ai/) sur lequel vous êtes allé plus tôt. Consultez la méthode [CreateProject](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.training.customvisiontrainingclientextensions.createproject?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_CustomVisionTrainingClientExtensions_CreateProject_Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_ICustomVisionTrainingClient_System_String_System_String_System_Nullable_System_Guid__System_String_System_Collections_Generic_IList_System_String__) pour spécifier d’autres options quand vous créez votre projet (procédure expliquée dans le guide du portail web [Build a detector](get-started-build-detector.md) [Créer un détecteur]).  

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_create)]


### <a name="add-tags-to-the-project"></a>Ajouter des balises au projet

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_tags)]

### <a name="upload-and-tag-images"></a>Charger et étiqueter des images

Lorsque vous appliquez des balises à des images dans des projets de détection d’objet, vous devez préciser la région de chaque objet balisé avec des coordonnées normalisées. Le code suivant associe chaque exemple d’image à sa région balisée.

> [!NOTE]
> Si vous ne disposez pas d’un utilitaire permettant d’effectuer des opérations de glisser-déposer pour marquer les coordonnées des régions, vous pouvez utiliser l’IU web sur [Customvision.ai](https://www.customvision.ai/). Dans cet exemple, les coordonnées sont déjà fournies.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_upload_regions)]

Cette carte d’associations est alors utilisée pour charger chaque exemple d’image avec ses coordonnées de région. Vous pouvez charger jusqu’à 64 images dans un même lot.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_upload)]

À ce stade, vous avez chargé tous les exemples d’images et vous avez étiqueté chacune d’elles (**fourchette** ou **ciseaux**) avec un rectangle de pixels associé.

### <a name="train-the-project"></a>Entraîner le projet

Ce code crée la première itération dans le projet.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_train)]

### <a name="publish-the-current-iteration"></a>Publier l’itération actuelle

Le nom donné à l’itération publiée peut être utilisé pour envoyer des requêtes de prédiction. L’itération n’est pas disponible sur le point de terminaison de prédiction tant qu’elle n’est pas publiée.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_publish)]

### <a name="create-a-prediction-endpoint"></a>Créer un point de terminaison de prédiction

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_prediction_endpoint)]

### <a name="use-the-prediction-endpoint"></a>Utiliser le point de terminaison de prédiction

Cette partie du script charge l’image test, interroge le point de terminaison du modèle, et renvoie des données de prédiction à la console.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_prediction)]

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

Vous savez maintenant comment effectuer chaque étape du processus de détection d’objet dans le code. Cet exemple exécute une seule itération d’entraînement, mais vous aurez souvent besoin d’entraîner et de tester votre modèle à plusieurs reprises pour le rendre plus précis. Le guide suivant traite de la classification d’images, mais ses principes sont identiques à la détection d’objet.

> [!div class="nextstepaction"]
> [Tester et réentraîner un modèle](test-your-model.md)
